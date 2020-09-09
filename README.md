# dimensionality-reduction-classic
Tutorials on classic dimensionality reduction techniques using tailored basis (SVD) and generic basis (Fourier and Wavelets).

## Dimensionality Reduction with Tailored Basis (Singular Value Decomposition)

In this [Jupyter Notebook](https://github.com/rsyamil/dimensionality-reduction-classic/blob/master/dimensionality_reduction_classic_tailored.ipynb), we will first perform Singular Value Decomposition (SVD) on a single [image](https://phys.org/news/2019-09-image-rio-negro-solimes-river.html) and generate the approximations using a few numbers of leading basis, *k*. As the number of leading basis that are included to generate the approximation increases, so will the quality of the image and the amount of memory needed. 

![Svd_single](/readme/svd_single_image.png)

We can observe in the image above that as *k* increases, more details are available in the approximation. When *k=100* (out of 480), even the smaller channels are reproduced well. This suggests that an image may contain redundant information (i.e. compressible) that can be compactly summarized into few key components and in this case when SVD is used, those components are the tailored basis given by the **U** which represents a low-dimensional pattern space that is obtained through a coordinate transformation from the high-dimensional space. 

![Svd_multi](/readme/svd_multi_basis.png)

The tailored basis can also be learnt from a collection of images with rich features. As an example, we will perform SVD on the (pre-processed) digit MNIST dataset to obtain learnt basis **U**. In the figure above, we visualize the first 100 columns within **U** where the reshaped columns represent feature spaces that are ranked according to the variance. The leading basis contain large features while the non-leading ones contain smaller features.

![Svd_leading](/readme/svd_multi_basis_leading.png)

The matrix **U** contains orthonormal basis that are ordered according to the amount of variance in the columns of the digit MNIST dataset (in this case, the dimension is (28x28)xN where each column represents a flattened 2D image). As such, when the high-dimensional images are projected onto the low-dimensional feature spaces, we can expect that the leading basis will provide distinguishing power between the images, especially when they contain very different features. In the figure above, the low-rank representations for N data points are visualized (and color-coded by class label) in a scatter plot of *z1* vs *z2* where different class of digits form different clusters. Naturally, plotting a scatter of representations in non-leading feature spaces (i.e higher *z*s) will not give you clear clustering between the images from different class labels. 

![Svd_recons](/readme/svd_multi_basis_recons.png)

Suppose we retain only the leading *k* singular values and vectors and use them for approximation, the figure above shows some samples of the rank-*k* approximation compared with the original image. In other words, once the matrix **U** is learnt, the coefficients (sometimes called projections or low-rank representations) can be manipulated for several applications. 

![Svd_approx](/readme/svd_multi_basis_approx.png)

To demonstrate this, we take the [image](https://phys.org/news/2019-09-image-rio-negro-solimes-river.html) we have used earlier and crop into a 28x28 image. Then the cropped image is projected onto the low-dimensional pattern space and reconstructed, using the learnt matrix **U** from the digit MNIST dataset. We can see that as *k* increases, the reconstruction quality improves as there are richer features that are present to reconstruct the features in the channel image. 

## Dimensionality Reduction with Generic Basis (Fourier and Wavelets)

In this [Jupyter Notebook](https://github.com/rsyamil/dimensionality-reduction-classic/blob/master/dimensionality_reduction_classic_generic_git.ipynb) we will perform dimensionality reduction of 2D image using Fast Fourier Transform (FFT) and multilevel Discrete Wavelet Transform (DWT). For FFT, we will use the built-in FFT routine in NumPy where *np.fft.fft2* will perform row-wise FFT on the image and subsequently a column-wise FFT to give a 2D discretized Fourier transform. 

![Fourier_single](/readme/fourier_single.png)

We can then experiment with different levels of thresholding (i.e. sort and retain only the top *k* percent) on the transform matrix. The thresholded transform matrix is then used as an input to the inverse FFT routine *np.fft.ifft2* to obtain the approximated image. In the figure above, we can observe the effect of *k* on the reconstructed (i.e compressed) image.

![Wavelet_single](/readme/wavelet_single.png)

For DWT, we will use the PyWavelets library where the function *pywt.wavedec2* will perform multi-level DWT on the image to give a list of coefficients by level (in this case, we use three levels). To visualize the multi-level decomposition, we normalize each level independently and use *pywt.coeffs_to_array* to tile the normalized coefficients into the original image dimension as we can see in the top right image in the figure above. The bottom right image represents the non-normalized coefficients. Since wavelet multi-level decomposition is hierarchical, the first level represents the low resolution component while the other levels will provide additional details. 

![Wavelet_recons](/readme/wavelet_recons.png)

In other words, all the cofficients can be used to reconstruct the full original image. For compression purposes, similar to FFT, we can experiment with different levels of thresholding (i.e. sort and retain only the top *k* percent) on the coefficients. The function *pywt.waverec2* is then used on the thresholded coefficients to reconstruct the image approximation. We can now compare the approximations from FFT and DWT!

