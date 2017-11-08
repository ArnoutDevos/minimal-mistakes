This version of the popular Viterbi algorithm assumes that all the input values are given in log-probabilities. Therefore summations are used instead of multiplications. The input sentence has `N` words, and we are trying to assign a label to each word, chosen from a set of `L` labels.
## Viterbi algorithm pseudocode

    viterbi(Emission, Trans, Start, End):
        
        # Set internal variables
        s <- 0.0
        Y <- []
        Trellis <- empty NxL matrix
        Backpointers <- empty (N-1)xL matrix
        
        # Set first row of Trellis
        Trellis[0, :] <- start + Emission[0,:]
        
        # Construct rest of Trellis table and keep Backpointers table
        for each word i in {1, ..., N-1}:
        	for each label j in {0, ..., L-1}:
        		Trellis[i,j] <- Emission[i,j] + max_k(Trans[k,j] + Trellis[i-1,k])
        		Backpointers[i-1,j] <- argmax_k(Trans[k,j] + Trellis[i-1,k])
        
        # Calculate total score s, last backpointer b_next, add b_next to the result Y
        s = max_k(End[k] + Trellis[-1,k])
        b_next = argmax_k(End[k] + Trellis[N-1,k])
        Y[N-1] <- b_next
        
        # Do backpropagation for remaining N-1 words
        for word i in {N-2, ..., 0}:
        	b_next <- Backpointers[i, b_next]
        	Y[i] <- b_next
        	
        return (s,Y)
        
**Explanation**

The algorithm takes 2 matrices and 2 vectors as input:

- *`Emission`* is an `NxL` matrix storing the probabily of observing word n, given a label l 

	`P(n|l) = Emission[n,l]`
- *`Trans`* is an `LxL` matrix storing the transition probability from the previous label (Yp) to the current label (Yc)
	
	`P(Yc|Yp) = Trans[Yp,Yc]`
- *`Start`* is a `Lx1` vector storing the transition scores of the beginning of a sentence `<s>` to the every label `l`

	`P(l|<s>) = Start[l]`
- *`End`* is a `Lx1` vector storing the end transition scores from the label `l` of the last word to the end of the sentence `</s>`

	`P(</s>|l) = End[l]`

Internally, the `NxL` matrix *`Trellis[i,l]`* stores the score of the *best* sequence from 1 ... i such that l<sub>i</sub> = l. The `(N-1)xL` *`Backpointers`* matrix tracks from which previous label the calculated optimal score for each cell came. Note that *`Backpointers`* has one row less than *`Trellis`* as the last backpointer can be stored in a single variable (`b_next`), before starting backpropagation.