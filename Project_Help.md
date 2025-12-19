## June 21st (Suggestions for Experiment #2)

**Question:** Should I build a ReLU feedforward network with L = Q + 1 layers, where Q is the number of classes (so L = 4 for digits 0, 1, 2), and use gradient descent?  
**Answer:** Yes

---

**Question:** Is it correct that the input layer has dimension d0 = 784, and the hidden layers can have decreasing widths as long as each width is at least Q?  
**Answer:** Yes. I suggest starting with letting all layers have dimension 784, except for the last one, which should have d_L = 10.

---

**Question:** After training, should I compute the cumulative weight matrices W_l and bias vectors b_l using the recursive formulas: W_l = W_l * W_{l-1} * ..., and b_l = W_l * b_{l-1} + b_l?  
**Answer:** Yes

---

**Question:** Once I have W_l and b_l, do I apply the truncation map tau_{W, b}(x) = W_pseudoinverse (ReLU(Wx + b) - b) at each layer?  
**Answer:** After the network is trained, you don't need to apply the truncation maps, as the neural network is already implementing a truncation map with some cones. **

---

**Question:** To extract the cone geometry, should I compute the base point as p = -W_pseudoinverse * b and the edge vectors as v_i = W_pseudoinverse * e_i, where e_i are standard basis vectors?  
**Answer:** Yes

---

**Question:** Finally, I'm guessing I should use PCA to project the data and cone structures into 2D or 3D space, and then visualize how the data transforms layer-by-layer?  
**Answer:** Initially I was just thinking to have a look at what the cones look like, maybe superimposed with the initial data. It would be very cool to also have a graphic visualization of the cones transforming the data, but that can come later.

---

**Note:**  
** After you get the cumulative weights and biases, you can assume that the neural network is applying every truncation map tau_{W^(l), bˆ(l)} on the data, one after the other. So the first layer does tau_{W^(1), bˆ(1)}, the second layer does tau_{W^(2), bˆ(2)} and so on, up until the last layer when everything gets mapped to the output (that's the content of equation (3.7) [here](https://arxiv.org/pdf/2405.07098)). 

The action of each of these truncation maps is given by the corresponding cone. So for the first cone, you would compute p = -W^(1)_pseudoinverse * b^(1) and v_i = W^(1)_pseudoinverse and similarly for the other cones.

------

## July 3rd (Feedback after Experiment #1 and Experiment #2 + Suggestions for Next Steps)

[Here](https://colab.research.google.com/drive/19IYlsoutYqcNrU2dE7FOujP-jCeNkfl-?usp=sharing) is the Google Colab notebook with the last modifications that I made, so you can see a test where everything ended up full-rank.

Here are some quick comments on your code, some of which we discussed today:  
- Derivation of cumulative parameters is correct in your implementation, but wrong in your handwritten notes :)  
- I noticed you used CrossEntropyLoss instead of MSELoss. I wonder if/how that might change the geometry of the trained network.  
- No need to test for injectivity of matrices: if dimensions of hidden layers go down, they can never be injective; if the dimensions stay the same, then W is injective [if and only if](https://en.wikipedia.org/wiki/Rank%E2%80%93nullity_theorem) it is surjective.

---

**And suggestions for next steps:**

**1. For your implementation of visualizing the data changing through the layers:**  
- No need to compute the a_i components, you can compute truncated data points directly from the definition of the truncation map, using the cumulative parameters.  
- For producing the plots, I suggest: Do PCA in original training data, then use those vectors (principal components) when doing the other projections. For Q=3 classes, you would end up with 1 plot for the original data, then 3 plots for the data truncated with τ_{W^(ell), b^(ell)} for ell = 1, 2, 3, for a total of 4 plots of data in R^784 projected down to 2D.  
- There could be better choices of how to project the data (for instance, use the principal components from truncated data, or project to class means, etc).  
- You can plot what the output looks like as well in R^Q, which for Q=3 would require no projection to a lower dimensional space.

**2. Synthetic data:**  
- I suggest generating classification data in R^3, training a ReLU neural network to classify it, and then extract the cumulative parameters and plot the cones (as you already did for Experiment #2) or the truncated data (as you will be implementing for MNIST above).  
- The easiest data geometry to start with might be Q=3 classes, clustered data. For generating, you could implement one of the following, or something else that is easy for you to do:  
  - Take 3 balls centered at (1,0,0), (0,1,0), and (0,0,1) and produce a uniform sample of points in each.  
  - Use a Gaussian mixture model, centering the Gaussians on the same points as above, with a small standard deviation.

Other data geometries I have in mind, vaguely defined:  
- 3 classes which are SLS but not clustered.  
- 2 classes which are not linearly separable, but not concentric (like Figure 1a in [Ewa25]).  
- 2 classes which are concentric (like Figure 1b in [Ewa25]).

**3.** Thomas sent you his notes on his intuitive explanation of the hyperplane separation in the toy model for MNIST. If that sparks any ideas you think are nice, feel free to experiment with that!

---

Also, note that both papers you've been referring to have new, improved versions on the arXiv now: CE, E. The results are the same, so nothing changes with your implementation, but I suggest downloading new PDFs and using those as references.



## August 1st (Suggestions about Fine-Tuning and Directionalizing Results)

1. **For MNIST and synthetic data:**
   - Fix implementation of truncation map. See eq. (7) in [CE24], or the changes I made to the copy I sent you.  
   - Implement training until some error threshold is met or flow gets stuck.  
   - If this is working well, produce plots of truncated data and output layer for different thresholds.  
   - Also, note: 0,1,6,4 are SLS (in this order). So perhaps using (0, 1, 6) instead of (0, 1, 8) might be better, since we know a zero cost global minimum exists.  
   - The idea here is to see if the data gets more clustered in the output layer (neural collapse) and/or how the cone geometry might change if the error gets smaller.  

2. **A new experiment for the synthetic data:**
   - For each data configuration you are generating, use the constructions from [CE24] and [Ewa25] to find zero cost global minima.  
   - It looks like you're using the classifying hyperplanes/cones to construct some of the data ((b) and (c)), so hopefully you should be able to use those same hyperplanes/cones to construct the parameters of the network.  
   - Perturb this parameter vector slightly (add some vector in parameter space with small norm), use this to initialize, and then see what SGD converges to.  
   - Do the same for smaller/larger norms.  
   - The idea here is to get a feel for what the loss landscape looks like around these constructive global minima.  

3. **For pixel hyperplanes:**
   - You can try to visualize the separating hyperplanes with colors, with different colors per hyperplane, and each point being colored (or not) depending on which side of that hyperplane it is on.

------

## September 13th (Clarifications on Perturbation Strategy and Zero-Loss Constructions)

**Email Summary (Mayank → Patrícia, Sept 11)**  

Dear Patrícia,  

Good afternoon! I have a couple of questions I would like to confirm for the **synthetic data experiment** we are currently implementing. Could you please verify that I understand the required steps correctly, or point out any mistakes in my reasoning?

**What I need to do:**  
1. For each synthetic dataset configuration, construct weights and biases following the approach in **[CE24]/[Ewa25]** so that the network classifies all points correctly with zero loss.  
2. Verify that this manually constructed network indeed achieves zero training loss.  
3. Generate random noise tensors (same shape as each \(W_l, b_l\)) to act as perturbations.  
4. Scale each noise tensor by a small constant and add it to the constructed parameters to form a *perturbed parameter vector*.  
5. Use this perturbed initialization for **SGD** and train normally.  
6. Repeat for multiple constants (small / medium / large) to observe when SGD converges back to zero loss and when it does not.  
7. Run many trials for each constant and plot **histograms of final losses** to analyze the local shape of the loss landscape near these zero-loss minima.

**Questions:**  
- How exactly should the “standard deviation weight matrix” be created for the perturbations?  
- Should the perturbation magnitude (λ) be a fixed scalar (e.g., 0.1) or defined relative to the parameter norms?  
- Could you briefly describe how to create the weights and biases directly from the hyperplanes/cones mentioned earlier?

---

**Reply (Patrícia → Mayank + Thomas, Sept 11)**  

Your summary looks excellent!  

**Perturbation Strategy:**  
- Generate a random tensor of the same shape as each weight or bias.  
- Normalize it and scale by **λ / ‖tensor‖**, varying λ (start around 0.1, then try larger/smaller).  
- For now, you don’t need to account for the norm of the original parameter tensor—though that could be tested later.

**Zero-Loss Construction Guidelines:**  
- Each synthetic geometry defines a **polyhedral cone** per class that collapses that class to its base point \(p\), while either  
  (i) leaving the other classes unaffected (types (a) and (b)), or  
  (ii) projecting them beneficially (types (c) and (d)).  
- Each cone defines a **cumulative weight matrix + bias** for one layer; the **final layer** performs linear regression.  

**For data type (c): Non-SLS, Non-Concentric (Fig. 1a [Ewa25])**  
- You already have \(p\) and two directions \(v_1, v_2\).  
- To make the first layer’s matrix surjective, introduce a third independent direction:  
  \[
  v_3 = v_1 + v_2 + (0,0,1)
  \]  
  Adjust the first two directions slightly:  
  \[
  v_1 = v_1 + (0,0,-1), \quad v_2 = v_2 + (0,0,-1)
  \]  
  Shift the base point toward the blue class:  
  \[
  p_1 = p + (0, -\tfrac{1}{2}, 0)
  \]  
- Use **Lemma 2.1 [Ewa25]** to build the first \(W^{(1)}, b^{(1)}\).  
- For the second cone: take \(p_2 = 0\) and \(v_i^{(2)} = -v_i^{(1)}\); again use these to construct \(W^{(2)}, b^{(2)}\).  
- The final cumulative layer should map \(p_1\) and \(p_2\) to their one-hot labels. The last bias can likely be set to 0.  
- Finally, derive the **non-cumulative parameters** from these and pass them to the model.

**Next Steps:**  
- Test this construction on the synthetic (type c) dataset.  
- Once it reproduces the expected zero-loss behavior, extend to other geometries (types (a), (b), (d)) using analogous cone definitions.

---

## October 28th (Suboptimal Minima, Speed of Truncation & Next Steps)

Hi Mayank,

### **1. Finish construction of zero-loss minimizer for the concentric dataset**
- Complete the cone-based construction analogous to the earlier synthetic settings.
- Verify that the cumulative parameters classify all points with zero loss.
- Once confirmed, this will serve as the “reference point” for the next experiments on suboptimal minima and truncation dynamics.

---

### **2. Study of suboptimal local minimizers**
In the clustered-data construction with \(Q\) classes, each truncation map collapses exactly one class while leaving the others unchanged.  
By shifting the base point (i.e., modifying the bias), each truncation map can be toggled between:
- **“Full collapse”** of that class, or  
- **“No collapse”** (identity on all classes)

This yields  
\[
2^Q - 1
\]
distinct suboptimal local minima.

The goal is to understand:
- What the loss landscape looks like around these suboptimal minima  
- How stable each one is under perturbations

#### **For \(Q = 3\):**
- Construct several of these suboptimal minima  
  (due to symmetry, not all need to be constructed explicitly)
- For each minimizer:
  - **Perturb → train (SGD) → measure final loss**  
    (same method as in the zero-loss perturbation experiments)
  - **Perturb → evaluate loss directly** without training  
    (gives a sense of the curvature and basin shape)

---

### **3. Speed of truncation**
Thomas has a paper analyzing how truncation evolves during training.  
He shows that the amount of truncation grows **exponentially** with the number of already-truncated data points.

You can watch his recent talk — he starts discussing this around **46:20**.

The goal is to obtain **empirical evidence**, even qualitatively, that mirrors this phenomenon.

#### **Suggested setup (again with \(Q=3\) clustered data):**
1. Construct networks that **partially truncate** certain clusters  
   (easy once the suboptimal minima are implemented — you can interpolate biases between states)
2. Train these networks with SGD
3. Generate a few **video clips** visualizing:
   - Data movement through layers during training  
   - Degree of truncation over time  
   - How fast clusters collapse toward their base points

This does **not** need to be precise or quantitative yet — qualitative evidence and visual intuition are sufficient for a first pass.

---
