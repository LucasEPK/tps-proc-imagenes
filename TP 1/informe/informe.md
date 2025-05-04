# Trabajo Practico 1
**Alumnos:**  
Nahuel Arrieta  
Lucas Moyano

## Sección 1:  Modos de color en imagenes

### 6. **(*) La conversión de una imagen de color a escala de grises se puede hacer de varias formas. El ejercicio consiste en convertir la imagen de Lenna color a escala de grises utilizando diferentes metodos.**

#### a. **Usando la libreria cv2 y el metodo cvtColor()**

![](imgs/lenna_cv2_cvtcolor.png)

#### b. **Usando la fórmula de luminancia**

![](imgs/lenna_luminancia.png)

#### c. **Usando scickit-image y el método rgb2gray**

![](imgs/lenna_luminancia.png)

#### d. **¿Qué pasa con los canales?**

Ahora las imágenes solo tienen un único canal que corresponde, a la intensidad del blanco.

#### e. **¿Qué profundidad de bits tiene la imagen?**

Tienen una profundidad de 8 bits.

#### f. **Evaluar con otra imagen de mayor profundidad**

![](imgs/16_bits.png)
![](imgs/16_bits_gray.png)

#### g. **¿Qué sucede con la imagen? ¿Ha cambiado algo?**

Al ojo humano, parece no haber diferencia entre una profundidad de bits de 8 y una de 16.

### 7. **(*) Convertir la imagen de Lenna a otros modos de color, como CMYK, HSV, HSL. Mostrar el resultado.**

CMYK:
![](imgs/lenna_cmyk.PNG)
HSV:
![](imgs/lenna_hsv.PNG)
HSL:
![](imgs/lenna_hsl.PNG)


### 8. **(*) Tomar la imagen convertida en escala de grises y volver a convertir al en modo RGB. ¿Qué ha sucedido?**

![](imgs/lenna_gray_to_color.png)  
Cuando se convirtió a escala de grises, se perdió información respectiva a la intensidad de cada color. Cuando volvemos a convertir a RGB, se aplica la misma información a todos los canales, perdiendo toda diferencia entre intensidad de colores.


## Sección 2: Compresión de Imagenes



### 2. **(*) Dar detalles de las siguientes métricas de calidad de compresión (PSNR, SSIM)**

#### *PNSR  (Peak Signal-to-Noise Ratio)*  
El PSNR es una métrica utilizada para evaluar la calidad de una imagen comprimida en comparación con la imagen original. Se basa en la relación entre la potencia máxima de la señal (la imagen original) y la potencia del ruido (la diferencia entre la imagen original y la imagen comprimida).  
Se  calcula:  
![](imgs/psnr.png)  
Donde:  
- MAXI es el valor máximo de un píxel (para imágenes en escala de grises con 8 bits, MAXI  =255).
- MSE es el Mean Squared Error, que mide la diferencia promedio entre los píxeles de la imagen original y la imagen comprimida.

#### *SSIM (Structural Similarity Index)*  
El SSIM es una métrica más avanzada y perceptual que evalúa la calidad de una imagen teniendo en cuenta la estructura visual que es importante para el ojo humano. Mide las similitudes en la luminancia, el contraste y la estructura de las imágenes comparadas. Se calcula:  
![](imgs/ssim.png)  



### 6. **(*) Implementar un modelo de compresión basado en codificación Run-Length Encoding (RLE). El algoritmo Run-Length Encoding (RLE) reduce el tamaño de una imagen representando secuencias consecutivas de píxeles idénticos como una sola entrada. Para ello convertir una imagen en escala de grises. luego, implementar el algoritmo RLE para comprimir la imagen. Posteriormente, implementar una funcion para descomprimir la imagen. Al finalizar, mostrar la imagen original y la imagen reconstruida. Probar con dos o tres imagenes que tengan diferentes características, modos de color. Utilizar alguna de las metricas nombradas anteriormente e evaluar el resultado de la misma.**

Imagenes originales:  
![](imgs/senkuOG.png)
![](imgs/lucasOG.png)  
Imagenes greyscale:  
![](imgs/senku_grayscale.png)
![](imgs/lucas_grayscale.png)  
Imagenes reconstruidas:  
![](imgs/senku_reconstructed.png)
![](imgs/lucas_reconstructed.png)

Utilizando PSNR notamos que nos da infinito, ya que las imagenes no pierden datos y son identicas.