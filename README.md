# Distributed Google Maps Scraper

A tool that downloads maps from Google maps for offline use on PC and smartphones, either using a direct connection (not recommanded) or using Tor proxy.

This tool will create HTML files that you can directly open on browsers (PC and smartphone) for offline use. Beware that using extensively this tool without Tor will certainly cause your IP to be graylisted by Google, hence, if the map you want to scrape is too big do not use direct connection but Tor instead (see below).

The amount of image files (tiles) can reach a very big value, this can lead to a very long time of copying (from PC to sdcard for example). If you do not need a map that covers a very large area, choose to put this script in different folders to split images files to the scrict necessary. Nonetheless, having a very large amount of tiles in the same directory may prove useful, and this script can dela with this kind of challenge.

## Dependencies

I tried to use only the defalut programs that can be found in nearly any Linux distibution nowadays, of course, installing [Tor program](https://www.torproject.org/download/download.html.en "Click to download Tor") is still needed for the ones who did not installed it yet. The programs needed are `tor`, `wget`, `ls`, `cp`, `cat`, `xmllint`, `sed`, `echo`, `printf`, `read`, `bc`, `touch`, `export`, `nc`, `awk`, `grep`, `sleep`, `rm`, `basename`, `sort`, `head`, `identify`, `mkdir`, `cut`, `jobs`. The shell version used here is the Bourne-Again Shell `/bash`.

## Getting Started

Nothing to do apart downloading the shell script and installing Tor if not already done. If the script is not executable make it so:
```bash
chmod +x dgms
```
## Examples

*All examples assume you've already installed [Tor](https://www.torproject.org/download/download.html.en "Click to download Tor")*

### Without using Tor (not recommanded!)

> Please do not run the following commands (i.e. those not using Tor) for a long time otherwise your IP will get graylisted a you'll need to change it, thses examples are only introduction for the **Using Tor** (next) section. This script was intended primarily to use Tor.

#### Use the builtin search engine

If you don't want to search for latitude/longitude coordinates by yourself this script offer the possibility to enter the address directly:

```bash
./dgms --address algiers --zoom 15
# OR using shortcuts
./dgms -ad algiers -z 15
```

This command will download a map (file `algiers.html`) centered at Algiers, zoomed at 15, the width and height of the map will be (approximatively[^approx]) the default ones (±0.01 for both longitude and latitude). You noticed that the script diplayed an interactive screen asking for the result to choose (in that case there were only on result), it also offers the possibility to search againe `[R]` or to abort `[A]`. If you are sure that the returned first result in the interactive screen is what you want (for example in the second of subsequent tentative) you can use the argument `--lucky`:

```bash
./dgms --address algiers --zoom 15 --lucky
# OR using shortcuts
./dgms -ad algiers -z 15 -lk
```

This will prevent the script to display an interactive screen and pass directly to the downloading process. However if you are not sure that the first result will be the right one, do not use `--lucky`, try `-ad paris` to see.

You can also choose to download a range of zoom, beware that the more the zoom is bigger the more there will be tiles, it can reach hundreds of thousands of tiles[^graylisted]:

```bash
./dgms --address bejaia --min-zoom 2 --max-zoom 16 --lucky
# OR using shortcuts
./dgms -ad bejaia -mz 2 -Mz 16 -lk
```

If you want to specify by yoursef the name of the creatd HTML file, use the argument `--name`:

```bash
./dgms --address bejaia --min-zoom 2 --max-zoom 16 --lucky --name bougie
# OR using shortcuts
./dgms -ad bejaia -mz 2 -Mz 16 -lk -n bougie
```

You can choose the language of the displayed addresses in the layers[^lang] (by default the script will use the default system language found in the `$LANG` variable), for example if we want russian language:

```bash
./dgms --address bejaia --min-zoom 2 --max-zoom 16 --lucky --name bougie --language ru
# OR using shortcuts
./dgms -ad bejaia -mz 2 -Mz 16 -lk -n bougie -l ru
```
You'll certainly need to scrape a wider region than the ±0.01x±0.01 default one, to do this use *deviations*:

```bash
./dgms --address "Oran, algeria" --min-zoom 2 --max-zoom 13 --longitude-deviation 0.05 --latitude-deviation 0.05 --lucky --name oran
# OR using shortcuts
./dgms -ad "Oran, algeria" -mz 2 -Mz 13 -lod 0.05 -lad 0.05 -lk -n oran
```

If you don't need the addresses layer, you can use `--only-sat` argument to tell the script to not scrape layers:

```bash
./dgms --address Temouchent --min-zoom 2 --max-zoom 16 --lucky --only-sat
# OR using shortcuts
./dgms -ad Temouchent -mz 2 -Mz 16 -lk -os
```
[^approx]: The map is made of tiles of fixed size, these tiles begin and end at discrete values of latitude/longitude coordinates, thus a give boundary value of latitude/longitude may be inside a tile (not on its corner), however this tile will be entirely downloaded and displayed on the screen, which will always make the width/height values greater or equal to ±0.01.
[^graylisted]: At this step if you see that your IP address have been greylisted please change it or pass to the *using Tor* examples.
[^lang]: provided that it is available for the selected area

#### Specify latitude/longitude manually

If you have the coordinates of a particular place that has not address, you can use the `--latitude` and `--longitude` arguments (stop this command immediatly after lauch):

```bash
./dgms --latitude 15.298569 --longitude 19.429733 --zoom 23 --only-sat --name super_zoom
# OR using shortcuts
./dgms -la 15.298569 -lo 19.429733 -z 23 -os -n super_zoom
```

You'll notice the very big number of tiles to be downloaded 

```
 > Total number of tiles that will be downloaded: 226495
```

This is of course not impossible with our script, but it will take time and needs to pass through Tor, let's reduce the deviations:

```bash
./dgms --latitude 15.298569 --longitude 19.429733 --zoom 22 --only-sat --name super_zoom --latitude-deviation 0.0001 --longitude-deviation 0.0001
# OR using shortcuts
./dgms -la 15.298569 -lo 19.429733 -z 22 -os -n super_zoom -lad 0.0001 -lod 0.0001
```

This will scrape one of the closest zooms available on Google maps (there are no available adresses at this zoom and mainly on this region, this is why `-os` have been used). If you don't specify the `--name` argument value, the script will use the coordinates and zoom to build the HTML file name, for example this command:

```bash
./dgms --latitude 15.298569 --longitude 19.429733 --zoom 22 --only-sat --latitude-deviation 0.0001 --longitude-deviation 0.0001
# OR using shortcuts
./dgms -la 15.298569 -lo 19.429733 -z 22 -os -lad 0.0001 -lod 0.0001
```
will create a HTML file names `15.298569,19.429733,22.html`.

### Using Tor

To tell the script to use Tor add the argument `--use-tor` together with the two arguments `-start-port` and `--end-port` which define the range of ports that will be used:

```bash
./dgms --latitude 21.422352 --longitude 39.826338 --min-zoom 2 --max-zoom 18 --name mecca --latitude-deviation 0.05 --longitude-deviation 0.05 --use-tor --start-port 7000 --end-port 7009
# OR using shortcuts
./dgms -la 21.422352 -lo 39.826338 -mz 2 -Mz 18 -n mecca -lad 0.05 -lod 0.05 -T -sp 7000 -ep 7009
```

This can take some times (8846 tiles to download) but it will **certainly** download them (if a Tor node is graylisted we change it), to speed things we can increase the nomber of simultaneous downloads (which is by default 4) and/or add Tor instances (incread the ports range):

```bash
./dgms --latitude 21.422352 --longitude 39.826338 --min-zoom 2 --max-zoom 18 --name mecca --latitude-deviation 0.05 --longitude-deviation 0.05 --use-tor --start-port 7000 --end-port 7019 --max-connections 15
# OR using shortcuts
./dgms -la 21.422352 -lo 39.826338 -mz 2 -Mz 18 -n mecca -lad 0.05 -lod 0.05 -T -sp 7000 -ep 7019 -mc 15
```
This will make the script do 15 simultaneous downloads at the same time (it may consume a lot of resources depending on your machine). When the ports range is somewhat big, the Tor initialisation can take several minutes to achieve, but when it's lanched, you will notice that the number of tiles will increase rapidly!

## Known bugs

* Very buggy on firefox for smartphones.
* Drag may become buggy if the cursor reaches the borders of the window.

## TODO

* Add the "Terrain" feature (very easy in fact).
* Make the jquery application more mobile friendly (use jquery mobile?).
* Make the bash script consume less resources.
* Skip the downloaded files efficiently (store downloaded ranges/zooms?).
* Add the "mark a position" feature.
* Do not use flat files, make it export tiles to a DB format like RMaps SQLite Database.

## Support us

If you're bikers, hikers or everything else and this script helped you, please consider helping us to support our work, thank you :-)

<form action="https://www.paypal.com/cgi-bin/webscr" method="post" target="_top">
<input type="hidden" name="cmd" value="_s-xclick">
<input type="hidden" name="hosted_button_id" value="L94ML4RUPYJW8">
<input type="image" src="https://raw.githubusercontent.com/ubugnu/jquery-ui/master/donate.png" border="0" name="submit" alt="PayPal - la solution de paiement en ligne la plus simple et la plus sécurisée !">
<img alt="" border="0" src="https://www.paypalobjects.com/fr_FR/i/scr/pixel.gif" width="1" height="1">
</form>
