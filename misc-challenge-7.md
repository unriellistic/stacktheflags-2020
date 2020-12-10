### Preliminary Steps

We are handed a png file with no leads. To acquire said leads we should first bang the file through common tools such as binwalk and pngcheck.


![alt text](https://github.com/unriellistic/stacktheflags-2020/blob/main/misc-challenge-7-picures/1.png)

While binwalk shows an embedded zlib file to be present, it is characteristic of png files and should be ignored.


![alt text](https://github.com/unriellistic/stacktheflags-2020/blob/main/misc-challenge-7-picures/2.png)

Pngcheck, on the other hand, reveals the presence of a "zTXt" chunk. After some quick googling it appears to be a chunk type for compressed data, and with a tool like [png-chunks](https://www.dcode.fr/png-chunks), its contents can be easily extracted. 
Using [CyberChef](https://gchq.github.io/CyberChef/) we easily discern it to be a png file converted to base64.

This is the png file within the png file:
![alt text](https://github.com/unriellistic/stacktheflags-2020/blob/main/misc-challenge-7-picures/download%20(2).png)

### Getting the Flag


After the usual checks on the new picture reveal nothing, we move on to image stenography. [StegOnline](https://stegonline.georgeom.net/) is a wonderful tool for this purpose. Upon uploading the flag picture, stegonline writes in big red words that the image contains a custom colour palette.

![alt text](https://github.com/unriellistic/stacktheflags-2020/blob/main/misc-challenge-7-picures/3.png)

Clicking through the bitmaps reveals the flag.
![alt text](https://github.com/unriellistic/stacktheflags-2020/blob/main/misc-challenge-7-picures/4.png)
