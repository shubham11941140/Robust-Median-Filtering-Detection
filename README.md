# Robust-Median-Filtering-Detection
It involves the complete implementation of research paper on Robust Median Filtering based on Local Difference Descriptor with code

## **THEORY**

We will use a **Local Difference Descriptor (LDD)** which is a set of features that we will use to discriminate between median filtered and non-median filtered images. 

$LDD$ will consist of: 

1. **Joint Histogram of Rotation Invariant Uniform LBP**
2. **Corellation Coefficient of PDM**

### **1. Joint Histogram of Rotation Invariant Uniform LBP:**

#### **LBP (Local Binary Pattern):**

LBP is a simple yet very efficient texture operator which labels the pixels of an image by thresholding the neighborhood of each pixel and considers the result as a binary number. Due to its discriminative power and computational simplicity, LBP texture operator has become a popular approach in various applications. It can be used for texture classification, segmentation, face recognition, and other applications.

**Formulation:**

The LBP operator can be written as:

$$LBP_{P,R} = \sum_{p=0}^{P-1} s(g_p - g_c)2^p$$

where $g_c$ is the intensity of the center pixel and $g_p$ is the intensity of the neighboring pixels. $s(x)$ is a function that returns 0 if $x$ is negative and 1 otherwise. $P$ is the number of pixels in the neighborhood and $R$ is the radius of the neighborhood.

#### **Rotation Invariant LBP:**

In rotation invariant LBP, we cycle through all possible rotations of the binary pattern and use the smallest one as the result. For example, if the binary pattern is $10010000$, we would get $01001000$, $00100100$, $00010010$, $00001001$, $00000100$, $00000010$, $00000001$, and $10000010$. The smallest one is $00000001$, which is the result of rotation invariant LBP.

**Formulation:**

The rotation invariant LBP operator can be written as:

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/1721da1d-a23b-45d0-b84c-12425bd185c0)


where $ROR(x, i)$ is the result of rotating the binary pattern $x$ by $i$ bits to the right.

#### **Uniform LBP:**

A binary pattern is called uniform if it contains at most two bitwise transitions from 0 to 1 or vice versa when the bit pattern is traversed circularly. For example, $00000000$ and $01110000$ are uniform patterns, while $01010000$ and $11000000$ are not.

#### **Rotation Invariant Uniform LBP:**

In the paper, they have talked about rotation invariant uniform LBP because it is more discriminative than the original LBP.

**Formulation:**

The rotation invariant uniform LBP operator can be written as:

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/30770fb4-3121-4c06-a9f9-f6c6eb095288)

where $U(x)$ is the number of bitwise transitions in the binary pattern $x$.

#### **CLBP (Completed Local Binary Pattern):**

Because the conventional **LBP** descriptor just use the sign information of image local difference, and the magnitude information is discarded. Another paper (referenced by this paper) proposed a **completed LBP** descriptor by decomposing the image local difference into two complementary components, i.e., the $Sign$ $(s_p)$ and the $Magnitude$ $(m_p)$, respectively.

$$s_p = s(g_p - g_c)$$

$$m_p = |g_p - g_c|$$

where $g_p$, $g_c$ and $s(x)$ are defined above. 

Then they defined the $CLBP-Sign$ $(CLBPS)$ (i.e., $LBP$) and $CLBP-Magnitude$ $(CLBPM)$ as:

$$CLBPM_{P,R} = \sum_{p=0}^{P-1} s(m_p - c) 2^p$$

where $c$ denotes the mean value of $m_p$ in the whole image.

Hence, we get:

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/4803ece4-6b5f-464a-9a65-20269087d582)


**Note: The Outputs will be converted to Rotation Invariant Form.**

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/0d3ff3e7-4d3d-4c53-8426-1f477f1c6657)

### **2. Correlation Coefficients of PDM**

The local pixel differences can better describe how pixel values change and implicitly encode important micro structures. To show the behavior
of local pixel difference pair, we present the joint probability distribution of local pixel difference pair which is denoted as:

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/fcbf5b62-8933-4944-b066-288923bfe76d)


where $g^p_{i, j}$ and $g^q_{i, j}$ are the pixel values of the $p_{th}$ and $q_{th}$ neighbors of the center pixel $(i, j)$, respectively. $t_x$ and $t_y$ are the local pixel difference pair.

Essentially it is the Probability of $p_{th}$ neighbour differing by a difference of $t_x$ from the center pixel $g_{i,j}$ and $q_{th}$ neighbour differing by a difference of $t_y$ from the center pixel $g_{i,j}$.

In order to take advantage of the correlation between the local pixel difference pair, we compute the correlation coefficients of the PDM. 

#### **PDM (Pixel Difference Matrix):**

First, we obtain a PDV (Pixel Difference Vector) for each pixel in the image. The PDV is a vector of length $P$ which contains the difference between the center pixel and its $P$ neighbours. The PDV is denoted as:

$$d_i = [d_{i,1}, d_{i,2}, ..., d_{i,P}]^T$$

where $d_i$ is the PDV of $i^{th}$ pixel in the image. $d_{i,j}$ is the difference between the center pixel and its $j^{th}$ neighbour.

Now, we eliminate the PDVs whose elements are all $0$ values.

Then, we construct a PDM (Pixel Difference Matrix) for the remaining PDVs. The PDM is denoted as:

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/e2cff4ac-925a-46b6-95d3-b3a7e368c71e)


where $M$ is the PDM of the image. $N$ is the number of PDVs after eliminating the PDVs whose elements are all $0$ values.

The PDV measures the differences between the center point and neighboring pixels within a patch, thus it can better describe how pixel values change and can implicitly encode important visual patterns such as edges and lines in images.

Now, Joint probability is suitable to elaborate the behavior of local difference pairs. Therefore, the $Normalized$ $Cross$ $Correlation$ $(NCC)$ coefficients can be used as features to capture the joint probability of local difference pairs.

#### **Normalized Cross Correlation (NCC):**

The $NCC$ coefficient of $i^{th}$ and $j^{th}$ rows in the PDM is denoted as:

$$ NCC(i, j) = \dfrac{cov([d_{1, i}, d_{2, i}, ..., d_{N, i}]^T, [d_{1, j}, d_{2, j}, ..., d_{N, j}]^T)}{\sqrt{D([d_{1, i}, d_{2, i}, ..., d_{N, i}]^T)D([d_{1, j}, d_{2, j}, ..., d_{N, j}]^T)}}$$

where $cov$ is the covariance of two vectors and $D$ is defined as:

$$D([d_{1, i}, d_{2, i}, ..., d_{N, i}]^T) = cov([d_{1, i}, d_{2, i}, ..., d_{N, i}]^T, [d_{1, i}, d_{2, i}, ..., d_{N, i}]^T)$$

The $NCC$ coefficient is a measure of similarity between two vectors. It is equal to $1$ when the two vectors are identical, and it is equal to $0$ when the two vectors are orthogonal.

Our second set of features can be summarized as follows:
1. Group the $PDVs$ to form the $PDM$ with $P$ rows;
2. Consider an arbitrary row of $PDM$ as a random variable and obtain the $NCC$ coefficients of any different variables;
3. Concatenate all the $NCC$ coefficients of $PDM$ $(CPDM)$ to yield a $^PC_2-Dimensional$ feature vector $C$.

### **3. Final Features Set**

Combining $JHLBP$ features and $CPDM$ features, we obtain the final $LDD$ features with $(P+2)^2 + \frac{P(P-1)}{2}$ elements for median filtering
detection.

**NOTE:** As instructed in the research paper, we will use $8$ neighbours i.e. $P = 8$ and $R = 1$ where $P$ is the number of neighbours and $R$ is the radius of the neighbourhood.

### The requirement for the project are:

- numpy==1.19.2
- cv2==4.5.1
- matplotlib==3.3.2
- threading==3.9.1
- scikit-learn==0.24.1
- pickle==4.0
- pandas==1.2.1

You can install these packages using pip as follows:

```bash
pip install -r requirements.txt
```

Ensure that the UCID Dataset is provided for generation of the mean/median filtered dataset and running of code.

## Running the code

We have submitted a single IPYNB file containing all the code for the project. The code is well commented and can be run in a sequential manner.

You just need to run the cells in the notebook in a sequential manner and the code will run.

The code can be run on Visual Studio Code or Jupyter Notebook or Google Colab or any other platform that supports IPYNB files as per your convenience.

## Implementation Steps:

### Construction of datasets to create median and average filtered images with 3 $\times$ 3 and 5 $\times$ 5 kernels

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/bf0b4804-41b5-4596-bdef-b58bdc14003c)
![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/3000bbf5-ac29-4986-8802-5b0000a88fee)

### Implementing LDD (Local Difference Descriptor) Features

### Correlation Coefficients of PDM

### Reconstruction of Uniform Rotational Invariant Histogram

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/2a5f037b-17ae-4afe-96fe-dad8c8ba3c04)

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/576933b9-69e4-44af-8689-d023d26778e1)

### Creation of the LDD Feature Set

### Reconstruction of 2D Projection of proposed features by using Linear Discriminant Analysis (LDA) corresponding to JHLBP, CPDM, LDD Features

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/a823f139-5f88-485d-abc0-28cf9a9d703a)

$Blue$ refers to Unaltered Images

$Red$ refers to Images with Median filtering applied with a kernel size 3x3

$Yellow$ refers to Images with Median filtering applied with a kernel size 5x5

$Green$ refers to Images with Average filtering applied with a kernel size 3x3

The Discriminative ability is shown via Clustering Effect.

We see the same pattern as shown in the research paper i.e. LDD provides better discriminative ability than the other two methods.

JHLBP provides better discriminative ability than CPDM which has very low discriminative ability.

**NOTE:** We see MF3 and MF5 are a bit merged, but the median filtering as a whole is separated than the original images and the average filtered images.

## Experiments and Results

The $C-SVM$ with $Gaussian$ $Kernel$ is used as the classifier in our experiments. Using the **five-fold cross-validation** in conjunction with a grid search, we obtain the best kernel parameters for the $SVM$. The grid search for the optimal parameters are performed on the multiplicative grid $(C, \gamma) \in \{ (2^i, 2^j) | i, j \in Z \}$.

We use those optimal parameters to get the classifier model on the entire training set, and the trained classifier model is used to perform a classification on the testing set.

Specifically, the images in the $UCID$ database are randomly divided into four folds of nearly equal size. The training set is composed of three folds, while the remaining fold is used for evaluation.

The performance of each detection method is summarized by the minimal average decision error of each technique under the assumption of equal priors and equal costs, 

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/4450ca92-22f6-418e-bb78-b71dc5255690)


where $P_{fp}$ and $P_{tp}$ denote the false positive and true positive rates, respectively.

### C-SVM with Gaussian Kernel

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/f0bae6dc-14c2-4685-afea-e74850453ebb)

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/cd944e24-2b2d-41eb-9ddd-667bc70945aa)

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/13f10d98-580e-40ab-bfc6-d20cc501ac86)

![image](https://github.com/shubham11941140/Robust-Median-Filtering-Detection/assets/63910248/3576e7a6-8486-4d3a-83cc-a26997cf5a3d)

