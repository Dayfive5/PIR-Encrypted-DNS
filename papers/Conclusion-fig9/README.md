Here is my conclusion for the reproduction of the figure 9 page 12 of the article.<br/>

### Organization
- *domain-length.png* contains the histogram that I made for the domain-length histogram<br/>
- *4th-packet.png* contains the histogram that I made for the TLS record sizes of the 4th packet<br/>
<br/>

### Conclusion
I have found the same histograms that they had in the paper except that for the 4th-packet histogram, 
I don't have a peak at the length 88, which is kind of suprising as I used the same LOC1 dataset that they used in their github.
But for the rest of the histogram, we can see that this is the exact same shape that they had in the article.<br/>
This confirms that the 4th packet often contains the first-party DoH query, as the domain name length histogram is almost identical to the histogram of the 
4th TLS record in the dataset. 
(They explained in the paper that the difference of 51 bytes between the two  histograms were caused by the HTTPS header.
