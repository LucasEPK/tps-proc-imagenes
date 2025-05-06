# Trabajo Practico 2
**Alumnos:**  
Nahuel Arrieta  
Lucas Moyano

## Sección 1: Histogramas

### 7. (*) Transformar la distribución de intensidades de una imagen para que se parezca a la de otra. Implementar el ajuste de histograma usando OpenCV o skimage.exposure.match histograms(). Comparar los histogramas antes y después del ajuste.
Se utilizó `exposure.match_histograms` de `skimage` para ajustar el histograma de la imagen 1 al de la imagen 2. Se pude observar como los colores de la imagen 1 cambian para poder parecerse a los de la imagen 2.

```python
image1_transformed = exposure.match_histograms(image_1, image_2)
```

![alt text](imgs/Lenna.png)
![alt text](imgs/mate.png)
![alt text](imgs/Lenna-adjusted-mate.png)

A continuación se muestran los histogramas de las imágenes originales y la imagen transformada.
![alt text](imgs/Lenna-hist.png)
![alt text](imgs/mate-hist.png)
![alt text](imgs/adjusted-hist.png)

### 8. (*) Aplicar ecualización de histograma a una imagen en escala de grises. Comparar la imagen original con la ecualizada.
Se utilizó `cv2.equalizeHist` para ecualizar el histograma de la imagen. A continuación se muestran la imagen original y la ecualizada, y sus histogramas.

```python
equalized = cv2.equalizeHist(grayscale_image)
```

![alt text](imgs/Lenna-grayscale.png)
![alt text](imgs/Lenna-equalized.png)
![alt text](imgs/Lenna-hist-comparission.png)

### 9. (*) Implementar una umbralización manual eligiendo un valor de umbral. Usar el método de Otsu para calcular un umbral óptimo automáticamente.
Se implementó una función propia de umbralización manual y para el método de Otsu se utilizó `cv2.threshold` con el flag `cv2.THRESH_BINARY + cv2.THRESH_OTSU`.
A continuación se muestran los resultados de aplicar ambos métodos a la imagen Lenna.

```python
def manual_threshold(image, threshold_value):
    # Create a new image with the same dimensions as the original image
    thresholded_image = image.copy()

    # Iterate through each pixel and apply the threshold
    for i in range(image.shape[0]):  # Iterate over rows (height)
        for j in range(image.shape[1]):  # Iterate over columns (width)
            if image[i, j] > threshold_value:
                thresholded_image[i, j] = 255  # Set to white
            else:
                thresholded_image[i, j] = 0    # Set to black
    
    return thresholded_image
```

```python
def otsu_threshold(image):
    _, otsu_thresholded_image = cv2.threshold(image, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
    return otsu_thresholded_image
```

![alt text](imgs/Lenna-grayscale.png)
![alt text](imgs/Lenna-manual-threshold.png)
![alt text](imgs/Lenna-Otsu-threshold.png)

### 11. (*) Implementar la transformación gamma I’=I^y permitiendo ajustar el valor de y dinámicamente. Aplicar diferentes valores de y en distintas regiones de la imagen (por ejemplo, usando una máscara o adaptando y en función del brillo local). Visualizar el efecto de la corrección gamma en la imagen y en su histograma.
Se implementó una función para aplicar la transformación gamma a la imagen. A continuación se muestra el resultado de aplicar diferentes valores de gamma a distintas regiones de la imagen.

```python
def gamma_correction(image, gamma):
    # Normalize the image to [0, 1]
    image_normalized = np.float32(image) / 255.0
    # Apply gamma correction
    image_gamma_corrected = np.power(image_normalized, gamma)
    # Rescale to [0, 255]
    image_gamma_corrected = np.uint8(image_gamma_corrected * 255)
    return image_gamma_corrected
``` 

![alt text](imgs/Lenna-grayscale.png)

Con un `y = 5` se logra un oscurecimiento de la imagen:

![alt text](imgs/Lenna-gamma-5.png)

Con un `y = 0.2` se logra un aumento del brillo:

![alt text](imgs/Lenna-gamma-0,2.png)



## Sección 2: Combinación de imagenes

### 3. (*) Multiplicación y división de imágenes: Multiplicar y divide dos imágenes píxel a píxel utilizando cv2.multiply() y cv2.divide(), observando cómo afecta el brillo y contraste.

```python
multipliedImg = cv2.multiply(imageKurisu, imageWallpaper)
dividedImg = cv2.divide(imageKurisu, imageWallpaper)
```
Imagenes originales:  
![](imgs/kurisu1366.jpg)  
![](imgs/wallhaven-vqlwkp_1366x768.png)

Imagenes multiplicadas:  
![](imgs/multiply.png)

Imagenes divididas:  
![](imgs/divided.png)

La primera imagen como se multiplica, el brillo es aumentado notoriamente y presenta un ligero contraste por el cual se puede ver la primera imagen original.  
Mientras que en la segunda imagen el brillo es disminuido llegando casi a 0 y presenta un contraste todavía menor al anterior casi invisible.

### 5. (*) Combinación con operadores lógicos: Usa operadores booleanos (cv2.bitwise and, cv2.bitwise or, cv2.bitwise xor) para fusionar imágenes basándose en una máscara binaria. Describir que sucede en cada caso

```python
andImage = cv2.bitwise_and(imageKurisu, imageWallpaper, mask=binary_mask)

orImage = cv2.bitwise_or(imageKurisu, imageWallpaper, mask=binary_mask)

xorImage = cv2.bitwise_xor(imageKurisu, imageWallpaper, mask=binary_mask)
```

Mascara binaria utilizada:  
![alt text](imgs/127_binary_mask.png)  

**Resultado de operación AND:**
![alt text](imgs/and.png)  
Los colores se suman, por ende la hue cambia dependiendo de los dos colores que se sumen.  

**Resultado de operación OR:**  
![alt text](imgs/or.png)  
El brillo es aumentado en toda la imagen menos en la mascara binaria.  

**Resultado de operación XOR:**  
![alt text](imgs/xor.png)  
Pareciera un efecto parecido al de invertir colores, las montañas que eran oscuras son ahora blancas, mientras que la nieve que era blanca es ahora oscura.

### 8. (*) Uso de operadores lógicos para reemplazar partes de una imagen: Reemplazar un área específica de una imagen con otra utilizando operadores lógicos y relacionales para definir la región de interés (ROI).

```python
effect_masked = cv2.bitwise_and(imageEffect, imageEffect, mask=inverted_mask)
merged_max = cv2.max(imageShigeo, effect_masked)
```

Imagenes utilizadas:  
![alt text](imgs/shigeo.png)
![alt text](imgs/psycho_vfx.png)

Remplazamos el pelo y el uniforme del chico con el efecto de la segunda imagen  

Aplicamos umbralización a la primera imagen:  
![alt text](imgs/shigeo_mask.png)  

La invertimos con el operador NOT:  
![alt text](imgs/shigeo_not.png)  

Agregamos el efecto con un AND con la mascara invertida:  
![alt text](imgs/mask_effect.png)

Usamos maximo para terminar uniendo la imagen original con la anterior:  
![alt text](imgs/shigeo_max.png)  

## Sección 3: Dominio espacial

### 8. Suavizado y Sobel (*): Aplicar un filtro gaussiano antes del operador de Sobel y analizar las diferencias en la detección de bordes.

```python
blurred_img = cv2.GaussianBlur(kurisu_img, (41, 41), 0)
image = cv2.cvtColor(blurred_img, cv2.COLOR_BGR2GRAY)

# Aplicar el filtro de Sobel en X y Y
sobelx = cv2.Sobel(image, cv2.CV_64F, 1, 0, ksize=3)  # Derivada en X
sobely = cv2.Sobel(image, cv2.CV_64F, 0, 1, ksize=3)  # Derivada en Y

# Magnitud del gradiente (opcional)
sobel_mag = np.sqrt(sobelx**2 + sobely**2)
sobel_mag = np.uint8(np.clip(sobel_mag, 0, 255))  # Convertir a imagen visible
```

Sobel:  
![alt text](imgs/sobel.png)

Sobel con gaussiano:  
![alt text](imgs/sobel_blurred.png)

Notamos que los bordes ya no están tan definidos como cuando aplicamos sobel a la imagen original, son más gruesos y menos detallados.

### 12. (*) Comparación de Métodos de Detección de Bordes : Comparar Sobel, Prewitt, Laplace y Canny trabajando diversas imágenes con características diferentes.

Imagen original:  
![alt text](imgs/kurisu_gray.png)  
Sobel:  
![alt text](imgs/kurisu_sobel.png)  
Prewitt:  
![alt text](imgs/kurisu_prewitt.png)  
Laplace:  
![alt text](imgs/kurisu_laplaciano.png)  
Canny:  
![alt text](imgs/kurisu_canny.png)  

Imagen original:  
![alt text](imgs/lucas_grey.png)  
Sobel:  
![alt text](imgs/lucas_sobel.png)  
Prewitt:  
![  ](imgs/lucas_prewitt.png)  
Laplace:  
![alt text](imgs/lucas_laplaciano.png)  
Canny:  
![alt text](imgs/lucas_canny.png)  

Imagen original:  
![alt text](imgs/bp_gray.png)  
Sobel:  
![alt text](imgs/bp_sobel.png)  
Prewitt:  
![alt text](imgs/bp_prewitt.png)  
Laplace:  
![alt text](imgs/bp_laplaciano.png)  
Canny:  
![  ](imgs/bp_canny.png)  

Observando las imagenes podemos observar como todos los filtros son más buenos cuando se trata de un dibujo, ya que en general los bordes están mucho mejor definidos.  

En una fotografía el que pudo detectar mejor los bordes fue Sobel, pero probablemente con diferentes parametros se puede mejorar el resultado con canny.

### 13. (*) Realce de Detalles: Aplicar un filtro de paso alto y sumarlo a la imagen original para mejorar los detalles.

```python
canny_image = cv2.Canny(image, 100, 200)
added_image = cv2.add(image, canny_image)
```

Imagen original:  
![alt text](imgs/kurisu_gray.png)  
Canny aplicado:  
![alt text](imgs/kurisu_canny.png)  
Sumados:  
![alt text](imgs/kurisu_borders_added.png)  

### 15. (*) Filtro de Diferencia Gaussiana (DoG): Aplicar la técnica de Diferencia de Gaussiana para resaltar bordes.

```python
def apply_dog(img):
  # Aplicar dos filtros gaussianos con distintos sigmas
  gauss1 = cv2.GaussianBlur(img, (81, 81), sigmaX=1)
  gauss2 = cv2.GaussianBlur(img, (81, 81), sigmaX=6)

  # Calcular la diferencia de Gaussiana
  DoG = cv2.subtract(gauss1, gauss2)
  return DoG
```

Diferencia gaussiana:  
![alt text](imgs/kurisu_dog.png)