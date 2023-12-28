# rpi-tspl-cups-driver
CUPS Driver for TSPL-based label printers on the Raspberry Pi 


Inspired by [this Hackaday post](https://hackaday.io/project/184984-cheap-poshmark-label-printer-iphone-airprint) to bring a CUPS driver for TSPL-based printers to armv7-based platforms. 

** NOTE ** - This driver is not provided by the printer manufacturer, and is not officially supported. I am not affiliated with any printer manufacturer. I am just a user who was annoyed that his printer wouldn't work on his Raspberry Pi.


## Supported Hardware

There are a number of printers which all seem to share the same underlying hardware/firmware, where drivers are expected to communicate via the "TSPL" printer language. I have only tested this with the one printer I have/use, but many more are expected to work. 


### Tested Printers

- iDPRT SP420


### Untested, but expected to work

** NOTE ** - Have you tested this with any of these printers? Report back if they work, and I'll move them to the "Tested" list

- iDPRT SP310
- iDPRT SP320
- iDPRT SP410
- HPRT SL42


### Also expected to work

Based on the notes in Proski's modifications, this likely also works with certain printers from the following brands:

- Beeprt
- Rollo
- Munbyn

If you confirm that this works with a printer from one of these brands, feel free to submit a PR (or issue!) and we'll get it added to the list (and add a ppd file, if available!).


## Installation

Installing one of these printers (and making it work) requires two steps -- Installing the PPD file into CUPS, and copying the filter (raster-tspl) to the right directory.

To install the PPD file into CUPS, walk through the "Add Printer" workflow. When you get to the last screen (where you are asked to select the model) select "Choose File" under "Or Provide a PPD File", select the appropriate PPD file for your printer, and choose "Add Printer".

PPD files are available in the `ppd/` folder of this repo, or can be made by modifying the "official" ppd files provided by the manufacturer (see: Converting PPD Files below).

To determine which filter to install, use `uname -a` to determine your system architecture.

To install the filter, select the correct filter from the `filters` directory of this repo, un-gzip it, and `sudo cp` it to the appropriate location containing your CUPS filters. For me, this means copying it to `/usr/lib/cups/filter/`


## Converting PPD Files

In order for the custom version of `raster-tspl` included to work, you will need to make some modifications to the ppd file. PPD files are plain text, and can be edited in your plain text editor of choice.

I have included a handful of PPD files in the `/ppd` directory of this repo -- these changes have already been made in those files. 

- Change `cupsModelNumber` to `20`
- Change `cupsFilter` to `"application/vnd.cups-raster 0 raster-tspl"`


## About the Filter

The `raster-tspl` provided in this package is **explicitly not** the same as the version released by iDPRT (or other manufacturers). The official version is closed source, and as such cannot be recompiled for Raspberry Pis (armv7 or aarch64).

This version instead is based on the official `rastertolabel` filter, integrating the the [modifications made by Proski](https://github.com/proski/cups) to create a suitable filter that functions for the printers listed above.

The armv7 filter is compiled against the version of CUPS that I have installed on my Raspberry Pi (which I believe is the default that comes with Buster). 

The aarch64 filter is compiled against the proski cups repository mentioned above. Specifically, this revision:

`commit bfdcaad258f58ab512da0bdc1457dc963a25cf8c`


### Code modifications required to make the filter work

If you are interested in seeing the specific code modifications that were required to make this work, take a look [here](https://github.com/OpenPrinting/cups/compare/v2.2.10...thorrak:cups:2.2.10_beeprt). 


## Troubleshooting

### Why are my prints inverted (black is white, white is black)?

The filter automatically converts grayscale images to black and white by applying a threshhold. The original modifications made by Proski use a different threshhold than I selected, and therefore I expect there may be printers out there for which the threshhold I selected may not work.

If you experience this, open an issue on GitHub and I will see about recompiling a new filter with the original threshholds. 


## Credit

100% of the credit for this goes to Proski. All I did was compile it for my specific version of CUPS and host a binary here with instructions. 

