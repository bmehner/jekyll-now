---
layout: post
title: You're up and running!
---
I have tons of old letters, invoices and other stuff printed on good old paper. The stuff piles up and can be
found everywhere around our house. Of course some of this stuff is more or less obsolete. On the other hand
some of these papers are really, really important like insurance policies. A while ago I started thinking about which of
them are so important that they even have to survive the house burning down. As I tried to figure this out I realized:

1. I can not really decide which ones need to survive a fire.
2. Even if I could, it would be too many of them to fit into some sort of fireproof safe or suitcase which I could grap
  in the middle of the night when I flee from a burning house.
3. The above would all be useless in case of me being somewhere else when the catastrophe happens.
4. Even leaving the catastrophe case to the side: all this paper stuff is getting on my nerves. There is no fulltext
  search engine. You do not find things easily. A lot of time is spent to flip through masses of papers in order to find
  anything or even to find just the right place to file it. And believe me: With a family of four there is a lot of
  stuff to file.

## Digitalization to the rescue

Digitalizing all the stuff, all documents fitting on one hard drive - how cool would that be? I have a reasonable pass-through scanner here. And scanning the documents would have easily removed all the paper piles throughout the house. But it would not solve the problem of managing the information. Instead of flipping through pages, I would be flipping through files. The information would be just images and you can not index the content of images easily.

So I did some research on [OCR](https://en.wikipedia.org/wiki/Optical_character_recognition) in the Linux world and stumbled over [ocrmypdf](https://github.com/jbarlow83/OCRmyPDF). This is a software that can do OCR on pictures in a PDF. 

With `ocrmypdf -d -c source.pdf target.pdf` you tell it to read the file "source.pdf", do its OCR magic and write the result to a file called "target.pdf". It adds a layer with transparent text on top of the images. The result is a searchable PDF document. The images are still there, but now when you search in the document the PDF reader highlights the area of the image where ocrmypdf identified the corresponding text. The filesize is only slightly larger than before. I was skeptical first, but the results are suprisingly good. Printed text is recognized so well that almost all text is contained afterwards. Of course fancy graphical distorted text is not matched, the same with handwriting. But up to now all the relevant keywords in your typical printed letter were always recognized.

With [pdfgrep](https://pdfgrep.org/) you can search whole directory trees of this OCR-ed files within seconds. In fact this works so well that up to now I did not feel the need for a full blown document management system with fulltext search capabilities. `pdfgrep -r <regular_expression>` does the trick.

And of course the most important thing: Backing up these documents in the cloud is now a no-brainer. One can access them anytime from anywhere if necessary -  even in case your house burns down.

## Resources

* OCR: https://en.wikipedia.org/wiki/Optical_character_recognition
* ocrmypdf: https://github.com/jbarlow83/OCRmyPDF
* pdfgrep: https://pdfgrep.org/
