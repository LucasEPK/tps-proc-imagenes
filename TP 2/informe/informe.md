# Trabajo Practico 1
**Alumnos:**  
Nahuel Arrieta  
Lucas Moyano

## 1. Histogramas

## 2. Combinación de imagenes

### 3. (*) Multiplicación y división de imágenes: Multiplicar y divide dos imágenes píxel a píxel utilizando cv2.multiply() y cv2.divide(), observando cómo afecta el brillo y contraste.

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

## 3. Dominio espacial

