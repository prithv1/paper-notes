
# Unitary evolution recurrent neural networks
  * http://arxiv.org/abs/1511.06464
  * Motivation: avoid exploding / vanishing gradient in RNNs
  * To avoid exploding and vanishing gradients, we need to use a matrix for recurrent corrections with eigen values 1 => we parametrize the matrix so that we ensure it is unitary
  * parametrize matrices with real entries is difficult and either results in a high computational complexity or too simple parametrization (use a matrix with non zero entries for the diagonal as a matrix for recurrent connections)
  * instead of working in real space, let's work in complex space => easier to find a definition for the matrix of the recurrent connections that is unitary in the complex space. This is computationally feasible and captures enough complexity for learning
  * memory & cost increase almost linearly with the size of the hidden layer
  * new activation function, relu for complex numbers
  * good results for Rnns example tasks (binary addition, etc), but not for a real life example

# Exponential linear units
  * http://arxiv.org/abs/1511.07289
  * Motivation: increase speed of learning and classification accuracy
  * Idea:
    * avoid vanishing gradient by using identity for positive values, but have negative values which allow to push the mean unit activation towards zero
    * zero mean activation brings the unit closer to the unit natural gradient
    * units that have a non-zero mean activation act as bias for the next layer
  * like batch normalization, they push the mean towards zero, but with less computational footprint
  * Elu formula:
    * x(f) = x for x >=0 and a(exp(x) - 1) for x < 0
    * a is a tunable hyperparameter
  * Idea behind natural gradient:
    * The parameter space can be complicated and not euclidean => the direction of the gradient is no longer appropriate => account for the curvature of the space
    * More [here](http://www.yaroslavvb.com/papers/amari-why.pdf)
  * Proof in paper:
    * Paper proves that zero mean activations speed up learning when the natural gradient is used
    * This applies to all techniques that strive to get zero mean activations (including batch normalization)
  * Results:
    * improvement in accuracy for network with more than 5 layers

# Exploiting Similarities among Language for Machine Translation
  * http://arxiv.org/abs/1309.4168
  * Motivation: Increase the size of language dictionaries from monolingual data
  * Idea:
    * It is easy to procure multilingual data, we can use that to learn embeddings in different languages
    * Learn a linear mapping from embeddings in the source language to embeddings in the target language from a small source language to target language dictionary
    * To expand the given source to target language dictionary:
      * take a word in the source language and compute its embedding
      * map the embedding to the target language using the learned mapping from the given dictionary
      * chose the word in the target language with the smallest cosine distance to the mapped embedding
    *
  * Results:
    * Good sample translations from English to Spanish
  * Potential issue: at test time one needs to compute the cosine distance against all the words in the target vocabulary which can be very big. However, this is can be parallelized and clustering method can also be used to improve speed.
  * Use cases:
    * find mistakes in dictionaries
    * expand dictionaries to new words

# A Simple Way to Initialize RNNs of Relu
  * http://arxiv.org/abs/1504.00941
  * Motivation: RNNs hard to train due to vanishing & exploding gradients
  * Idea: use the identity matrix (or a scaled version) to initialize the recurrent connections
    * activation function: Relu
    * Initialize biases to 0 and the recurrent connection to the identity matrix.
  * Advantage: simple architecture compared to LSTMs
  * Results: comparable with LSTM on a couple of toy examples + speech recognition

# Recurrent neural network regularization
  * http://arxiv.org/abs/1409.2329
  * Use dropout for Recurrent Neural Nets for the input connections, but not for recurrent connections
  * Results:
    * minimize overfitting for LSTM training
    * good results for language modelling and image caption

# Neural networks with few multiplications
  * http://arxiv.org/abs/1510.03009
  * Motivation: big computational cost of NN training
  * Idea: avoid multiplication by binarizing weights (forward pass) and convert multiplication into binary shift (backward pass)
  * mention of Completely Boolean networks: simplify test time computation with acceptable hit in accuracy, but no training time is saved.
  * Binary connect:
    * Traditional: y = h (Wx + b)
    * Idea: sample each weight to be 1 or -1
    * p(w_{ij}) = (w'_{ij} + 1) / 2, where w'_{ij} is the usual weight value, but constrained to be in between -1 and 1 (by being capped)
    * random number generation has to be fast and not do a lot of multiplication for this to be worth it
  * Ternary connect
    * In NNs a lot of weights are 0 or close to 0 => allow weights to be 0
    * if w'_{ij} > 0, use P(w_{ij} = 1) =  w'_{ij}, 0 otherwise
    * if w'_{ij} < 0, use P(w_{ij} = - 1) =  - w'_{ij}, 0 otherwise
  * Quantized backpropagation:
    * do bit shifts in order to avoid multiplication, not for the gradient but for the input.
  * they use batch normalization in their architecture
  * Results:
    * qualitative: a bit better than using simple NNs and CNNs
    * no measure of speed improvements


# Generative adversarial networks
  * http://arxiv.org/abs/1406.2661
  * Motivation: generate good samples
  * Used for: start of the art image generation with Laplacian pyramids of GANs
  * min-max game:
     * Generator (G) tries to generate good samples
     * Discriminator (D) tries to learn which samples come from the true distribution of the input and which samples come from G
     * at the end of training you hope that D cannot distinguish between real samples and samples from G
  * loss: = E_(x∼pdata(x))[log D(x)] + E_(z∼pz(z))[log(1 − D(G(z)))]
  * no need for MCMC and approximations
  * general framework, D&G not bound to be neural nets
  * training tricks:
      * update D k times for each update of G
      * min log(1-D(G(z))) does poorly at the begging of training because D has an advantage (easy to distinguish between samples from G and samples from the real distribution), so instead max log(D(G(z))), which has a stronger gradient
  * Theoretic result: the optimal distribution for D is: D*(G(x)) = p_data(x) / (p_data(x) + p_G(x))
