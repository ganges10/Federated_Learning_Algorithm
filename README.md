# Federated_Learning_Algorithm

This algorithm was presented in the paper <b><i>Communication-Efficient Learning of Deep Networks from Decentralized Data </b></i> by <i>H. Brendan McMahan,Eider Moore,Daniel Ramage,Seth Hampson and Blaise Agüera y Arcas</i>. This paper focuses on<br>

- The identification of the problem of training on decentralized data from mobile devices as an important research direction
- The selection of a straightforward and practical algorithm that can be applied to this setting
- An extensive empirical evaluation of the proposed approach

### Advantages in Federated Learning
   ---
   - Training on real-world data from mobile devices provides a distinct advantage over training on proxy data that is generally available in the data center.
   - The large and sensitive data are secure as they need not be logged to the data center purely for the purpose of model training.
   - For supervised tasks, labels on the data can be inferred naturally from user interaction.
   
### Federated Optimization
   ---
   Federated optimization refers to the optimization problem implicit in federated learning.Federated optimization has several key properties that differentiate it from a typical distributed optimization problem:
   
   - **Non-IID**: The training data on a given client is typically based on the usage of the mobile device by a particular
user, and hence any particular user’s local dataset will not be representative of the population distribution.
   - **Unbalanced**: Similarly, depending upon the users the usage of the app or service varies, leading
to varying amounts of local training data.
   - **Massively distributed**: The number of clients participating in an optimization will be much larger.
   - **Limited communication**: Mobile devices are frequently offline or on slow or expensive connections.
   
  A **deployed federated optimization system** must also address practical issues such as
   - Changing nature of client datasets due to addition or deletion of data
   - Client availability that correlates with the local data distribution in complex ways
   - Clients that never respond or send corrupted updates.
   
 ### Model Overview
   ---
   This work focuses on the non-IID and unbalanced properties of the optimization, as well as the critical nature
of the communication constraints.The assumption here is that *a synchronous update scheme that proceeds in rounds of
communication. There is a fixed set of K clients, each with a fixed local dataset.* At the beginning of each round, a random fraction **C** of clients is selected, and the server sends the current global algorithm state to each of these
clients. *Only a fraction of clients are selected for efficiency, as experiments show diminishing returns for adding more clients beyond a certain point.* Each selected client then performs local computation based on the global state and its local dataset, and sends an update to the server. The server then applies these updates to its global state, and the process repeats.

### Computational and Communication costs
   ---
   In federated optimization communication costs dominate and the reason is as follows:<br>
   Bandwidth for an upload will be limited by 1 MB/s or less. Further, clients will typically only volunteer to participate in the optimization when they are charged, plugged-in, and on an unmetered wi-fi connection. Further,each client will only participate in a small number of update rounds per day. On the other hand, since any single on-device dataset is small compared to the total dataset size, and modern smartphones have relatively fast processors (including GPUs), computation becomes essentially free compared to communication costs for many model types.
  
### Adding more Computation
 ---
 This can be done in 2 ways:
 
 - **Increased parallelism**, where we can have more clients working independently between each communication round
 - **Increased computation on each client**, where rather than performing a simple computation like a gradient calculation, each client performs a more complex calculation between each communication round.
 
 The paper reveals that the speedups achieved are due to adding more computation on each client, once a minimum level of parallelism over clients is used.
 
 ### *FederatedAveraging* Algorithm
  ---
      We consider a simple one-shot averaging, where each client solves for the model that minimizes (possibly regularized) loss on their local data, and these models are averaged to produce the final global model.
      
  **Applying SGD**:
   
   - This approach is computationally efficient, but requires very large numbers of rounds of training to produce good models.
   - To use large-batch synchronous SGD in the federated setting, we select a **C-fraction of clients on each round**, and compute the gradient of the loss over all the data held by these clients. Thus, C controls the global batch size, with C = 1 corresponding to full-batch (non-stochastic) gradient descent(FederatedSGD).
