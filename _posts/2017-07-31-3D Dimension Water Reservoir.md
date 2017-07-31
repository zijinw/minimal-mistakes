---
layout: single
title:  "3-D Water Reservoir Problem"
date:   2017-07-31 22:00:00
categories: Algorithm
---

# 3-D Water Reservoir Problem. So tricky! Actually I don't think I will come across it because I will not have an interview with Google.

In this problem, we should think that for each coordinate (x,y,z) in this map, the water that it can retain is the highest *z'* around it (not necessarily adjacent) minus the *z* it itself.

Similar to the 2-D problem where we can start from the lower side to move the pointer (the greedy solution), first we find the lowest *z* on the border of this map. Then, from this point, we start to do BFS-II. The logic is as follows:

1.	Initial point: put all the border points to the min heap. The point which has the lowest height at the border will be polled. 

2.	Generate rule: expand one point and put all of its adjacent points into the heap.

3.	For each point, it has a **Height** and a **Level**. Height is its original height, which can be read from the map (2-D array). The level is its maximum volume to retain water. If this point has the highest height till now, then its level will be 0 (it cannot retain any water). Otherwise, its level is the highest height till now minus this point's height.

4.	Pay attention that, some points has level 0 because of high height, but their neighbors will thus be affected has ability to retain water if they are lower. So 

5. For point P and its neighbor Q,

    Level(Q) = max(Height(Q), Level(P))

The level here is the ability of retaining water if the height == 0. The actually volume of water to be retained is

    Height(Q) - Level(Q)

So we can find that if Height(Q) < Level(P), the level of P will be passed to Q. The neighbors of Q will continuously be compared with Level(P). If Height(Q) > Level(P), the volume of water it in fact can retain is 0, but its neighbors will be compared with Level(Q), a higher level.