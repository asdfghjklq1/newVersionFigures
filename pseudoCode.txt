Here is the pseudo code:
Algorithm4: Steps ③ of SPOON for column rescaling and row summation with the optimization for atomicAdd.
Line1-7 are the same as algorithm3; 
 8:  rowFactor=𝑓𝑎𝑐𝑡𝑜𝑟𝑅𝑜𝑤[𝑟𝑜𝑤]; 
 9:  for 𝑖=𝑠𝑡𝑎𝑟𝑡; 𝑖<𝑒𝑛𝑑; 𝑖++ do
 10:   𝑖𝑑𝑥=𝑖∗𝑤𝑎𝑟𝑝𝑆𝑖𝑧𝑒+𝑙𝑎𝑛𝑒𝐼𝑑;
 11:   𝑡𝑒𝑚𝑝=𝑣𝑎𝑙𝑢𝑒[𝑖𝑑𝑥];
 12:   𝑡𝑒𝑚𝑝∗=rowFactor;
 13:   𝑣𝑎𝑙𝑢𝑒[𝑖𝑑𝑥]=𝑡𝑒𝑚𝑝; 
 14:   𝑝𝑎𝑟𝑡𝑖𝑎𝑙ColSum=𝑡𝑒𝑚𝑝; 
 15:   tmpSum=0
 16:   for w = 1; w <= 16; w <<= 1 do
 17:     tmpSum =__shfl_up_sync(0xffffffff, 𝑡𝑒𝑚𝑝,w); 
 18:     if laneId >= w then 
 19:       𝑝𝑎𝑟𝑡𝑖𝑎𝑙ColSum+=tmpSum;
 20：    endif
 21:   end for
 22:   colSeglen=colSegLength[𝑖𝑑𝑥]-1;
 23:   scan_sum=__shfl_down_sync(0xffffffff, 𝑝𝑎𝑟𝑡𝑖𝑎𝑙ColSum,colSeglen);
 24:   𝑝𝑎𝑟𝑡𝑖𝑎𝑙ColSum=scan_sum-𝑡𝑒𝑚𝑝+𝑝𝑎𝑟𝑡𝑖𝑎𝑙ColSum;
 25:   if colSeglen >= 0 then
 26:      atomicAdd(&𝑓𝑎𝑐𝑡𝑜𝑟𝐶𝑜l[colIdx[idx]], 𝑝𝑎𝑟𝑡𝑖𝑎𝑙ColSum);
 27：  end if
 28:  end for
