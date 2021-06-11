# Gradients image

```python

import pylab as plt

import matplotlib
cmap1 = matplotlib.cm.get_cmap('hot')
import matplotlib.cm 


for roi_i in range(1, n_labels + 1):
  import pylab as plt
  m=0
  n=0



  roi_indexes = np.where(labeled_dm == roi_i)
  roi_indexes1 = np.logical_or(labeled_dm == roi_i, labeled_dm == roi_i)
  roi_indexes1 = nb.Nifti1Image(roi_indexes1.astype(np.uint64), flair_image.affine)
  roi_indexes1_array = roi_indexes1.get_fdata()
  roi_indexes1_array =  roi_indexes1_array.astype(np.uint64)


  x_min, x_max = roi_indexes[0].min(), roi_indexes[0].max()
  y_min, y_max = roi_indexes[1].min(), roi_indexes[1].max()
  z_min, z_max = roi_indexes[2].min(), roi_indexes[2].max()



  # crea el cubo con la roi y los valores fuera de la roi cero.
  mic = roi_indexes1_array[x_min:(x_max+1), y_min:(y_max+1), z_min:(z_max+1)] * flair_array[x_min:(x_max+1), y_min:(y_max+1), z_min:(z_max+1) ]
  
  #mic = np.rot90(mic)

  #imagen binaria lesion
  mic100 = roi_indexes1_array[x_min:(x_max+1), y_min:(y_max+1), z_min:(z_max+1)] 
  
  
  ####################################################
  # Creacion del borde binario
  
  mic2 = ndimage.binary_erosion(mic100).astype(mic100.dtype)
  mic3 =  mic100 - mic2

  # Borde con niveles de intensidad  

  mic300= mic3 * flair_array[x_min:(x_max+1), y_min:(y_max+1), z_min:(z_max+1) ]
  
  
  mic10 = mic[np.where(mic > 0)]

  if mic10.sum == 0:
    mic1=0
  else:
    mic1 = pd.Series(mic10.ravel())


  #########################################
  ##

  mic30 = mic300[np.where(mic300 > 0)]

  if mic30.sum == 0:
    mic4=0
  else:
    mic4 = pd.Series(mic30.ravel())

  lesion =  mic


  #############################################
  # Histogram Lesion 

  if mic1.empty:
    nobs1 =0
    min_value1 = 0
    max_value1 = 0
    mean1 = 0
    var1 = 0
    skew1 = 0
    kurt1 = 0
  else:
    nobs1, (min_value1, max_value1), mean1, var1, skew1, kurt1 = stats.describe(mic1, nan_policy='propagate')
    #print(f'min_lesion: {min_value1}')

  #####################################
# Cuantizacion 8 por lesion 

  w100 = (max_value1 - min_value1) / 31
  face100 = lesion - min_value1
  face100[face100  <= 0] = 0

  Lesion1 = (face100 / w100)
  Lesion1 = Lesion1.astype(np.uint8)

  Lesion1 = Lesion1 + 1
  Lesion1 = Lesion1 * roi_indexes1_array[x_min:(x_max+1), y_min:(y_max+1), z_min:(z_max+1)]
  Lesion1 = Lesion1.astype(np.uint8)

  # Tamaño de la lesion
  axis0 = np.size(Lesion1, 0)
  axis1 = np.size(Lesion1, 1)
  axis2 = np.size(Lesion1, 2)
  max = np.max(Lesion1)


  x, y, z = np.meshgrid(np.arange(0, axis1, 1), np.arange(0, axis0, 1), np.arange(0, axis2, 1))


  dx = Lesion1
  dy = Lesion1
  dz = Lesion1

  m=0
  n=0
  dx0 = np.zeros(shape=(axis0,axis1,axis2), dtype=int)
  for a in range(axis0): 
    for b in range(axis1): 
      for c in range(axis2): 
        if a>0 and b>0 and c>0 and a<axis0-1 and b<axis1-1 and c<axis2-1:
          m= dx[a+1, b, c]
          n= dx[a-1, b, c] 
          dx0[a, b, c] = int(m) - int(n)

  m=0
  n=0
  dx1 = np.zeros(shape=(axis0,axis1,axis2), dtype=int)
  for a in range(axis0): 
    for b in range(axis1): 
      for c in range(axis2): 
        if a>0 and b>0 and c>0 and a<axis0-1 and b<axis1-1 and c<axis2-1:
          m= dx[a][b+1][c]
          n= dx[a][b-1][c] 
          dx1[a][b][c] = int(m) - int(n)

  m=0
  n=0
  dx2 = np.zeros(shape=(axis0,axis1,axis2), dtype=int)
  for a in range(axis0): 
    for b in range(axis1): 
      for c in range(axis2): 
        if a>0 and b>0 and c>0 and a<axis0-1 and b<axis1-1 and c<axis2-1:
          m= dx[a][b][c+1]
          n= dx[a][b][c-1] 
          dx2[a][b][c] = int(m) - int(n)


  u = dx0
  v = dx1
  w = dx2

  print(x.shape)
  print(u.shape)
  print(Lesion1.shape)

  color_array =np.sqrt((v)**2 + (u)**2+ (w)**2)
  aw = np.array(color_array.ravel())
  fig = plt.figure(figsize=[10,10])
  plt = fig.gca(projection='3d')





    # Color by azimuthal angle
  c = np.sqrt((v)**2 + (u)**2+ (w)**2)
  # Flatten and normalize
  c = (c.ravel() - c.min()) / c.ptp()
  # Repeat for each body line and two head lines
  c = np.concatenate((c, np.repeat(c, 2)))
  # Colormap
  #c = plt.cm.hsv(c)





  q=plt.quiver(y+0.5, x+0.5, z+0.5, v, u, w, cmap=cmap1, pivot = 'middle', length=1, lw=2, arrow_length_ratio=0.3, normalize=True)
  #q=plt.quiver(x, y, z, u, v, w, pivot = 'middle', length=0.3, lw=1)
  q.set_array(aw)
  q1=plt.voxels(Lesion1, alpha=0.05, edgecolors = 'gray', facecolors='gray')
  #print(aw.ravel())
  #plt.scatter(y,x,z, color = 'grey', s=1)
  plt.axis('off') 
  plt.set_xlabel("axis 0")
  plt.set_ylabel("axis 1")
  plt.set_zlabel("axis 2")
  #plt.set_zlim(0,2)




```
