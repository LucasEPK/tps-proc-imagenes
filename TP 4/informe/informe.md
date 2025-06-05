# Trabajo Practico 4
**Alumnos:**  
Nahuel Arrieta  
Lucas Moyano

## Introducción
El presente informe corresponde al cuarto trabajo de la materia "Procesamiento de Imágenes". En el mismo, se abordan ejercicios relacionadas a la Trasnformada Discreta de Fourier (DFT) aplicada a imágenes. Se busca analizar la frecuencia de las imágenes y su representación en el dominio de la frecuencia.
Para explicar las implementaciones, en cada consigna se encuentra parte del código utilizado para la resolución de los ejercicios. El código completo se encuentra en el notebook `TP 2/code/TP2.ipynb`.

## Sección 1: Transformada de Fourier
### 10. (*) Responder las siguientes preguntas. Tratar de utilizar dos im´agenes en aquellos ejercicios que no soliciten algo particular. Utilizar una imagen que muestre frecuencias bajas en su mayor´ıa y otras con mayor´ıa de frecuencias altas. Por cada pregunta se ha agregado una sugerencia de como realizar el ejercicio practico para acompa˜nar su respuesta.

### 11. Transformada de Fourier inversa: Realiza la transformada de Fourier inversa para recuperar la imagen original a partir de su versi´on filtrada en el dominio frecuencial. Compara la imagen original con la imagen recuperada.

```python
def fourier_transform(image, shift=True):
    ## Convert to grayscale
    if len(image.shape) == 3:
        # Convert to grayscale if the image is in color
        image = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)

    ## Apply Fourier Transform
    f_transform = np.fft.fft2(image)

    ## Shift the zero frequency component to the center
    if shift:
        f_transform = np.fft.fftshift(f_transform)

    ## Calculate the magnitude spectrum
    magnitude = np.abs(f_transform)

    ## Calculate the phase spectrum
    phase = np.angle(f_transform)
    
    return magnitude, phase
``` 

```python
def inverse_fourier_transform(magnitude, phase):
    # Reconstruct the complex spectrum
    f_reconstructed = magnitude * np.exp(1j * phase)

    # Shift the zero frequency component back to the original position
    f_reconstructed = np.fft.ifftshift(f_reconstructed)

    # Apply inverse Fourier Transform
    image_reconstructed_complex = np.fft.ifft2(f_reconstructed)

    # Get the real part (or absolute value)
    image_reconstructed = np.abs(image_reconstructed_complex)

    # Normalize the image to the range [0, 255]
    image_reconstructed = cv2.normalize(image_reconstructed, None, 0, 255, cv2.NORM_MINMAX)

    return np.uint8(image_reconstructed)
```

#### (a) ¿C´omo se visualiza la diferencia entre las frecuencias altas y bajas en una imagen? Ejercicio sugerido: Aplicar la Transformada de Fourier (DFT) y mostrar la magnitud del espectro centrado con fftshift.
Cuando aplicamos la DFT a una imagen, las frecuencias bajas corresponden a las componentes de baja variación en la imagen, como áreas homogéneas o suaves. Por otro lado, las frecuencias altas representan detalles finos y bordes. Al visualizar la magnitud del espectro centrado con `fftshift`, podemos observar que las frecuencias bajas se encuentran en el centro del espectro, mientras que las frecuencias altas están en los bordes. 

En el espectro podemos ver la magnitud de cada frecuencia, es decir, la cantidad de energía que tiene cada frecuencia en la imagen. Mientrás más blanco sea el pixel, más energía tiene esa frecuencia. 

Por ejemplo:

![Imagen original](images/Lenna.png)

![Espectro de Fourier (magnitud)](images/Lenna-mgn.png)

![Espectro de Fourier (fase)](images/Lenna-ph.png)

#### (b) ¿Qu´e ocurre si eliminamos las componentes de alta frecuencia de una imagen? ¿Y si eliminamos las de baja frecuencia? Ejercicio sugerido: Aplicar filtros pasa bajos y pasa altos en el dominio de la frecuencia y reconstruir la imagen con la transformada inversa.

Si eliminamos las componentes de alta frecuencia de una imagen, la imagen resultante se verá más suave y menos detallada, ya que estamos eliminando los bordes y detalles finos. Esto se traduce en una imagen más borrosa. Por otro lado, si eliminamos las componentes de baja frecuencia, la imagen resultante tendrá un aspecto más ruidoso y con más detalles, pero perderá la información de las áreas homogéneas.

En los siguientes ejemplos se aplicarion filtros ideales (circulos con radio cutoff) a la imagen de Lenna. En el caso del filtro pasa bajo, se eliminan las frecuencias altas (fuera del circulo), mientras que en el caso del filtro pasa alto, se eliminan las frecuencias bajas (dentro del circulo).


![Imagen original](images/Lenna.png)

![Espectro de Fourier (magnitud)](images/Lenna-mgn.png)

Filtros de paso bajo:

![Imagen filtrada (pasa bajo - cutoff=30)](images/lp30.png)

![Espectro de magnitud (pasa bajo - cutoff=30)](images/lp30-mgn.png)

![Imagen filtrada (pasa bajo - cutoff=90)](images/lp90.png)

![Espectro de magnitud (pasa bajo - cutoff=90)](images/lp90-mgn.png)

Filtros de paso alto:

![Imagen filtrada (pasa alto - cutoff=30)](images/hp30.png)

![Espectro de magnitud (pasa alto - cutoff=30)](images/hp30-mgn.png)

![Imagen filtrada (pasa alto - cutoff=90)](images/hp90.png)

![Espectro de magnitud (pasa alto - cutoff=90)](images/hp90-mgn.png)

### (c) ¿Qu´e representa la fase de la transformada de Fourier de una imagen? ¿Qu´e ocurre si se conserva solo la fase o solo la magnitud? Ejercicio sugerido: Reconstruir una imagen usando solo la magnitud y fase de otra imagen, intercambiar fase y magnitud entre dos im´agenes distintas.

La fase de la transformada de Fourier de una imagen representa el desplazamiento de cada una de las frecuencias. 

Si se conserva solo la fase y se descarta la magnitud, la imagen reconstruida sigue mostrando la estructura y formas reconocibles, aunque con contraste distorsionado. 

En cambio, si se conserva solo la magnitud y se elimina la fase, la imagen reconstruida pierde su estructura y se vuelve irreconocible, pareciendo una niebla borrosa. 

En el siguiente ejemplo se intercambiaron la fase y la magnitud entre dos imágenes distintas. 

Imagen 1:

![Imagen 1](images/Lenna.png)

![Espectro de Fourier (magnitud)](images/img-1-mgn.png)

![Espectro de Fourier (fase)](images/img-1-ph.png)

Imagen 2:

![Imagen 2](images/cameraman.png)

![Espectro de Fourier (magnitud)](images/img-2-mgn.png)

![Espectro de Fourier (fase)](images/img-2-ph.png)

Imagenes reconstruidas:

![Imagen reconstruida 1](images/new-image-1.png)

![Imagen reconstruida 2](images/new-image-2.png)

### (d) ¿Por qu´e se centra la transformada de Fourier para su visualizaci´on? ¿Qu´e efecto tiene? Ejercicio sugerido: Mostrar el espectro de magnitud con y sin aplicar fftshift.
Se centra la transformada de Fourier usando fftshift para facilitar su visualización, ya que por defecto el componente de frecuencia cero (las bajas frecuencias) queda en la esquina superior izquierda, lo que resulta poco intuitivo. Al centrarla, las bajas frecuencias se ubican en el centro y las altas en los bordes, permitiendo una interpretación más clara del contenido espectral y facilitando el diseño de filtros. Esta operación no altera los datos originales, solo reorganiza su disposición para hacerla más comprensible.

![alt text](images/mg_no_shift.png)

![alt text](images/mg_shift.png)

### (e)  ¿C´omo se comporta la transformada de Fourier ante la traslaci´on o rotaci´on de una imagen? Ejercicio sugerido: Aplicar una traslaci´on o rotaci´on y comparar los espectros de magnitud y fase antes y despu´es.

Cuando se traslada una imagen, la teoría dice que solo debería cambiar la fase del espectro de Fourier, mientras que la magnitud debería mantenerse igual. Sin embargo, en los experimentos realizados, al trasladar la imagen con cv2.warpAffine, se generan bordes negros en las zonas que quedan vacías. Estos bordes introducen nuevos detalles en la imagen que afectan el resultado, por lo que la magnitud también cambia. En cambio, al aplicar una rotación, el espectro de Fourier se rota en el mismo ángulo que la imagen, conservando su forma. Esto se puede ver claramente en las imágenes comparadas.

Imágen original:

![alt text](images/Lenna.png)

![alt text](images/Lenna-mgn.png)

![alt text](images/Lenna-ph.png)

Imágen rotada 45°:

![alt text](images/rotated-45.png)

![alt text](images/rotated-45-mg.png)

![alt text](images/rotated-45-ph.png)

Imágen trasladada:

![alt text](images/traslated.png)

![alt text](images/traslated-mg.png)

![alt text](images/traslated-ph.png)


### (f) ¿C´omo se refleja una estructura peri´odica en el dominio frecuencial? Ejercicio sugerido: Usar im´agenes sint´eticas (rejillas, l´ıneas) y observar c´omo se representan sus frecuencias dominantes

Cuando analizamos la transformada de Fourier de una imagen con estructuras periódicas, como rejillas o líneas, observamos que estas estructuras generan picos con estructuras periódicas en el espectro de magnitud y de fase. 
Dependiendo de la estrucutra de la imágen sintética, los picos pueden aparecer en diferentes posiciones del espectro. Por ejemplo, una rejilla regular genera picos en posiciones equidistantes, mientras que circunferencias generan picos en posiciones radiales.

Lineas verticales:

![alt text](images/lines.png)

Lineas diagonales:

![alt text](images/lines-45.png)

Circunferencias:

![alt text](images/circles.png)