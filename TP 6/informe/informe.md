# Trabajo Practico 6
**Alumnos:**  
Nahuel Arrieta  
Lucas Moyano

## Introducción
El presente informe corresponde al sexto trabajo de la materia "Procesamiento de Imágenes". En el mismo, se abordan ejercicios relacionadas a la detección de patrones en imágenes, abarcando técnicas de representación y descripción de características, y el reconocimiento de patrones.

Para explicar las implementaciones, en cada consigna se encuentra parte del código utilizado para la resolución de los ejercicios. El código completo se encuentra en el notebook `TP 6/code/TP 6.ipynb`.

## Sección 1: Representaci´on y Descripci´on de Caracter´ısticas

### 2. Representaci´on por relleno de regiones. Identificar los objetos en una imagen binaria y colorear cada regi´on detectada. Sugerencia: scikit-image: measure.label, regionprops, label2rgb.

Para este ejercicio se utilizaron las herramientas `measure.label` y `label2rgb` de la librería `scikit-image`. La función `measure.label` se utiliza para etiquetar las regiones conectadas en una imagen binaria, mientras que `label2rgb` se usa para colorear estas regiones etiquetadas.

```python
label_image = measure.label(binary_image)
colored = label2rgb(label_image, image=binary_image, bg_label=0)
```

El resultado es el siguiente:

![alt text](images/regions.png)

### 4. (*) C´alculo de propiedades geom´etricas. Extraer ´area, per´ımetro, excentricidad y compacidad de cada regi´on segmentada. Sugerencia: regionprops de skimage.measure.

Se utilizó la función `regionprops` de `skimage.measure` para calcular las propiedades geométricas de las regiones segmentadas. Esta función devuelve una lista de objetos que contienen diversas propiedades de cada región, como el área, el perímetro, la excentricidad y la compacidad.

```python
label_image = measure.label(binary_image)
props = measure.regionprops(label_image)
for prop in props:
    area = prop.area
    perimeter = prop.perimeter
    eccentricity = prop.eccentricity
    compactness = (perimeter ** 2) / area if area > 0 else 0    
```

Se muestran los reusltado para la siguiente imágen:

![alt text](images/circles.png)

```bash
Región 1: Area=187418.0, Perimeter=9535.681092190353, Eccentricity=0.64, Compactness=485.17
Región 2: Area=40881.0, Perimeter=753.3523804664972, Eccentricity=0.03, Compactness=13.88
Región 3: Area=3067.0, Perimeter=203.8233764908628, Eccentricity=0.09, Compactness=13.55
Región 4: Area=20596.0, Perimeter=533.8721497261419, Eccentricity=0.04, Compactness=13.84
Región 5: Area=3066.0, Perimeter=203.8233764908628, Eccentricity=0.09, Compactness=13.55
Región 6: Area=29861.0, Perimeter=643.0264786586926, Eccentricity=0.05, Compactness=13.85
Región 7: Area=7182.0, Perimeter=314.14927829866735, Eccentricity=0.07, Compactness=13.74
Región 8: Area=7181.0, Perimeter=314.3919189857867, Eccentricity=0.05, Compactness=13.76
Región 9: Area=13007.0, Perimeter=422.47518010647184, Eccentricity=0.03, Compactness=13.72
Región 10: Area=3065.0, Perimeter=205.23759005323595, Eccentricity=0.07, Compactness=13.74
Región 11: Area=13017.0, Perimeter=424.71782079359116, Eccentricity=0.04, Compactness=13.86
Región 12: Area=3071.0, Perimeter=203.82337649086284, Eccentricity=0.09, Compactness=13.53
Región 13: Area=29873.0, Perimeter=643.6122650963194, Eccentricity=0.03, Compactness=13.87
Región 14: Area=20575.0, Perimeter=533.0437226013956, Eccentricity=0.05, Compactness=13.81
Región 15: Area=7181.0, Perimeter=314.97770542341357, Eccentricity=0.06, Compactness=13.82
```


### 6. (*) Descriptores de textura con GLCM. Calcular contraste, correlaci´on y homogeneidad de regiones usando matrices de co-ocurrencia. skimage.feature.greycomatrix, greycoprops.

Para este ejercicio se utilizó la función `graycomatrix` de `skimage.feature` para calcular la matriz de co-ocurrencia de una imagen en escala de grises. Luego, se aplicó `graycoprops` para extraer los descriptores de textura como contraste, correlación y homogeneidad.

```python
# Compute the Gray-Level Co-occurrence Matrix (GLCM)
glcm = graycomatrix(image_uint8, 
                    distances=[1], 
                    angles=[0], 
                    levels=256,
                    symmetric=True, 
                    normed=True)

# Extract texture properties
contrast = graycoprops(glcm, 'contrast')[0, 0]
correlation = graycoprops(glcm, 'correlation')[0, 0]
homogeneity = graycoprops(glcm, 'homogeneity')[0, 0]
```

A continuación se muestran algunos resultados obtenidos:

### Imágen 1

![alt text](images/wood.png)

```bash
Contrast:     17806.4886
Correlation:  0.4394
Homogeneity:  0.7262
```

### Imágen 2
![alt text](images/sky.png)
    
```bash 
Contrast:     695.0470
Correlation:  0.9786
Homogeneity:  0.9893
```

### Imágen 3
![alt text](images/colors.png)

```bash
Contrast:     9153.1955
Correlation:  0.7157
Homogeneity:  0.8592
```

### 8. (*) Relaci´on espacial entre regiones. Determinar si las regiones est´an adyacentes o si una est´a contenida en otra. skimage.measure.regionprops + an´alisis de coordenadas / bounding boxes.

Se implemetó un algoritmo que utiliza las propiedades de las regiones obtenidas con `regionprops` para determinar la relación espacial entre ellas. Se verifica si dos regiones son adyacentes o si una está contenida dentro de otra comparando sus coordenadas y bounding boxes.

```python
def check_containment(bbox1, bbox2):
    """Check if bbox2 is fully contained within bbox1."""
    return (bbox2[0] >= bbox1[0] and bbox2[1] >= bbox1[1] and
            bbox2[2] <= bbox1[2] and bbox2[3] <= bbox1[3])

def check_adjacency(mask1, mask2):
    """Check if two regions are adjacent using morphological dilation."""
    # Determine the maximum shape for padding
    max_rows = max(mask1.shape[0], mask2.shape[0])
    max_cols = max(mask1.shape[1], mask2.shape[1])

    # Pad both masks to the same shape
    padded_mask1 = np.zeros((max_rows, max_cols), dtype=bool)
    padded_mask2 = np.zeros((max_rows, max_cols), dtype=bool)

    padded_mask1[:mask1.shape[0], :mask1.shape[1]] = mask1
    padded_mask2[:mask2.shape[0], :mask2.shape[1]] = mask2

    # Dilate the first padded mask
    dilation_structure = np.ones((10, 10), dtype=bool)  # 3x3 square structuring element
    dilated1 = binary_dilation(padded_mask1, structure=dilation_structure)

    # Check for adjacency
    return np.any(dilated1 & padded_mask2)
```

Un ejemplo de los resultados obtenidos es el siguiente:

![alt text](images/triangles.png)

```bash
Region 1 is adjacent to Region 2
Region 1 contains Region 6
Region 1 is adjacent to Region 7
Region 2 contains Region 4
Region 2 is adjacent to Region 7
```