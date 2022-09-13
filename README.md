{importujemy niezbędne pakiety}
from skimage.measure import compare_ssim
import argparse
import imutils
import cv2
import numpy as np

ap = argparse.ArgumentParser()
ap.add_argument(„-f”, „–first”, required=True,
help=”first input image”)
ap.add_argument(„-s”, „–second”, required=True,
help=”second”)
args = vars(ap.parse_args())

{wczytujemy dwa obrazki, które będą porównywane ze sobą}
imageA = cv2.imread(args[„first”])
imageB = cv2.imread(args[„second”])

{operujemy na masce…}
mask = np.zeros(imageA.shape, dtype=np.uint8)
roi_corners = np.array([[{współrzędne wybranego prostokątnego obszaru, np. (x1,y1),(x2,y2),(x3,y3),(x4,y4)}]], dtype=np.int32)
channel_count = imageA.shape[2]
ignore_mask_color = (255,)*channel_count
cv2.fillPoly(mask, roi_corners, ignore_mask_color)
imageA = cv2.bitwise_and(imageA, mask)
{… tutaj kończymy operację na masce}

{kod tak samo jak wyżej, ale tym razem dla obrazka imageB}

{konwertujemy obrazy do skali szarości}
grayA = cv2.cvtColor(imageA, cv2.COLOR_BGR2GRAY)
grayB = cv2.cvtColor(imageB, cv2.COLOR_BGR2GRAY)

{obliczamy wartość SSIM}
(score, diff) = compare_ssim(grayA, grayB, full=True)
diff = (diff * 255).astype(„uint8”)

{otrzymujemy wynik, tutaj wyrażony jako dopełnienie do wartości 10000}
print( int( round(1e4*(1-score)) ) )
