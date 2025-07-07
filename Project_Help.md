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
