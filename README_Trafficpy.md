What worked well?
More convolutional layers (64) with higher kernel sizes (5,5).
Increasing hidden layers to 450.

What didn't work well?
Fewer convolutional layers (32). Raising the dropout rate by 10% had a very minor loss reduction effect, as well as lower accuracy.
Lower pool size (2,2).
Increasing the pool size to 500 over 450 lowered accuracy by 0.3% and increased loss by 0.7%


What did I notice?
The first thing I noticed was the filename which I'd earlier amended as I was learning how to save the model output. I've learned that .h5 or .keras are the two formats it accepts.
The first output using the same convolutional (32) and pooling (2,2) layers as the lecture gave a total accuracy of 96.2% with a loss of 15.7% with the first two epochs taking 5s and the latter ones 4s.
I then increased the number of convolutional filters to 64, and it took significantly longer to compile with all 10 epochs taking 9-10 seconds. It gave a total accuracy of 96.5% and a loss of 12%, better than the first round.
I then updated the pool size to (3,3). This time compiling took 11s per epoch. Interestingly, the results were accuracy 94.5% and loss 24.6%.
I kept the pool size of (3,3) and changed the kernel size layer to (5,5). Compiling took a non-linear range of time per epoch, starting at 7s, up to 10s and then down to 6s, back up to 11s then finally  10s for the 10th epoch. The results were 97.8% accuracy and 10.6% loss.
So far the learnings are that fewer convolutional layers with higher kernel sizes and a higher  pool size layer produced better results.
I then increased the number of hidden layers to 350. This time compilation took 9-10s per epoch. The results were accuracy of 98.3% and loss of 7.5%.
I was happy so far with the increased accuracy and lower loss. I then increased the dropout rate to 0.6. The results were that compilation time remained the same, and accuracy was 98.1% with loss at 7.1%.
So far the learnings are, along with the above, that increasing hidden layers improves accuracy and lowers loss, while increasing the dropout rate by 10% slightly reduces loss.
I then increased hidden layers to 450. Compilation took even longer with 11-12s  then increasing to 20-21s per epoch, and the results were accuracy 97.9% and loss 8.8%.
I then lowered the dropout rate back to 0.5, which had a similar compilation time. The results were accuracy 98.6% and loss 6.5%. This is the most accurate model so far.
I then lowered the pool size back to (2,2) and the hidden layers back to 350. It still had high compilation times and accuracy was lower at 97.9% and loss at 9.1%
I undid the last changes above.
Finally, I increased the hidden layers to 500, the epochs had the longest compilation time thus far of 27s. The results were accuracy 98.3% and loss 7.3%.
I undid the final changes above.
My amendments ultimately increased accuracy by 1.7% and lowered loss by nearly half, or 6.9%
