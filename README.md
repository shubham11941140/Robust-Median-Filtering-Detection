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

$$LBP_{P,R}^{ri} = min\{ROR(x, i)\}$$

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
