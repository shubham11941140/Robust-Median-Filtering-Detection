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

$$LBP_{P,R}^{ri} = min \{ROR(x, i) \}$$

where $ROR(x, i)$ is the result of rotating the binary pattern $x$ by $i$ bits to the right.

#### **Uniform LBP:**

A binary pattern is called uniform if it contains at most two bitwise transitions from 0 to 1 or vice versa when the bit pattern is traversed circularly. For example, $00000000$ and $01110000$ are uniform patterns, while $01010000$ and $11000000$ are not.

#### **Rotation Invariant Uniform LBP:**

In the paper, they have talked about rotation invariant uniform LBP because it is more discriminative than the original LBP.

**Formulation:**

The rotation invariant uniform LBP operator can be written as:

$$LBP_{P,R}^{riu2} = \begin{cases} \sum_{p=0}^{P-1} s(g_p - g_c)2^p & \text{if } U(LBP_{P, R}) \leq 2 \\ P+1 & otherwise \end{cases}$$

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

$$CLBPS_{P,R}^{riu2} = \begin{cases} \sum_{p=0}^{P-1} s(g_p - g_c)2^p & \text{if } U(LBP_{P, R}) \leq 2 \\ P+1 & otherwise \end{cases}$$

$$CLBPM_{P,R}^{riu2} = \begin{cases} \sum_{p=0}^{P-1} s(m_p - c)2^p & \text{if } U(LBP_{P, R}) \leq 2 \\ P+1 & otherwise \end{cases}$$

**Note: The Outputs will be converted to Rotation Invariant Form.**
The center gray level component of $CLBP$ is discard, because it describes the global information of an image. The histogram of the $CLBPC$ cannot distinguish the median filtered images from non-median filtered images. The mappings from $LBP_{P,R}$ to $CLBPS^{riu2}_{P, R}$ and $CLBPM^{riu2}_{P, R}$, which have $P + 2$ distinct output values, can be implemented with a lookup table of $2^P$ elements. 

Finally, we use the joint $2D$ histogram of the $CLBPS^{riu2}_{P, R}$ and $CLBPM^{riu2}_{P, R}$ codes as our first features set $(JHLBP)$ with $(P + 2)^2$ dimensions for median filtering detection.

### **2. Correlation Coefficients of PDM**

The local pixel differences can better describe how pixel values change and implicitly encode important micro structures. To show the behavior
of local pixel difference pair, we present the joint probability distribution of local pixel difference pair which is denoted as:

$$P^{p, q}_{i, j}(t_x, t_y) = Pr(g^p_{i, j} - g_{i,j} = t_x, g^q_{i, j} - g_{i,j} = t_y)$$

where $g^p_{i, j}$ and $g^q_{i, j}$ are the pixel values of the $p_{th}$ and $q_{th}$ neighbors of the center pixel $(i, j)$, respectively. $t_x$ and $t_y$ are the local pixel difference pair.

Essentially it is the Probability of $p_{th}$ neighbour differing by a difference of $t_x$ from the center pixel $g_{i,j}$ and $q_{th}$ neighbour differing by a difference of $t_y$ from the center pixel $g_{i,j}$.

In order to take advantage of the correlation between the local pixel difference pair, we compute the correlation coefficients of the PDM. 

#### **PDM (Pixel Difference Matrix):**

First, we obtain a PDV (Pixel Difference Vector) for each pixel in the image. The PDV is a vector of length $P$ which contains the difference between the center pixel and its $P$ neighbours. The PDV is denoted as:

$$d_i = [d_{i,1}, d_{i,2}, ..., d_{i,P}]^T$$

where $d_i$ is the PDV of $i^{th}$ pixel in the image. $d_{i,j}$ is the difference between the center pixel and its $j^{th}$ neighbour.

Now, we eliminate the PDVs whose elements are all $0$ values.

Then, we construct a PDM (Pixel Difference Matrix) for the remaining PDVs. The PDM is denoted as:

$$M = \begin{bmatrix} d_{1,1} & d_{2,1} & \cdots & d_{N,1} \\ d_{1,2} & d_{2,2} & \cdots & d_{N,2} \\ \vdots & \vdots & \ddots & \vdots \\ d_{1,P} & d_{2,P} & \cdots & d_{N,P} \end{bmatrix}$$

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
