When trying to recreate reproducible code with randomness it's a good idea to introduce a seed. As long as this seed is kept the same, the same 'random' things (most often numbers) will be generated repeatedly.

In PyTorch, this can be done using the following code:
	
	seed = 123
	torch.manual_seed(seed)