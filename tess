#!/usr/bin/env -S bash -e

[ "$#" -eq 1 ] || {
  echo "Usage: $0 <image path>">&2
  exit 1
}

[ "$(tesseract -v 2>/dev/null|head -1|sed 's/^tesseract .*$/e/')" = "e" ] || {
  sudo add-apt-repository ppa:alex-p/tesseract-ocr -y
  sudo apt update -y
  sudo apt install tesseract-ocr libtesseract-dev libgtk2.0-dev -y
}

[ -n "$(tesseract --list-langs|grep Japanese)" ] || {
  sudo apt install tesseract-ocr-jpn  tesseract-ocr-jpn-vert
  sudo apt install tesseract-ocr-script-jpan tesseract-ocr-script-jpan-vert
}

[ -f "$1" ] || {
  echo "Error: '$1' is not found">&2
  exit 1
}
echo "#[test: command]"
tesseract "$1" stdout -l jpn

echo "#[test: python]"
python -m pip install -U pytesseract Pillow opencv-python -q
python <<A
# https://qiita.com/FukuharaYohei/items/e09049c8d312eaf166a5
import cv2
from PIL import Image
from pprint import pprint
import pytesseract

FILENAME = "$1"
im = cv2.imread(FILENAME)
d = pytesseract.image_to_data(im, output_type=pytesseract.Output.DICT)

for i in range(len(d['level'])):
    x, y = d['left'][i], d['top'][i]
    w, h = d['width'][i], d['height'][i]
    cv2.rectangle(im, (x, y),
                  (x + w, y + h), (0, 255, 0), 2)

cv2.imshow('img', im)
cv2.waitKey(0)
cv2.destroyAllWindows()
A
