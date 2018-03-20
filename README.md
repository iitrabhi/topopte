# Topology optimization #

![halfbeam](./img/topopt.gif)

This is an implementation of the [classic topology optimization code](http://www.topopt.dtu.dk/) described in [A 99 line topology optimization code written in Matlab](http://www.topopt.dtu.dk/files/matlab.pdf) by Ole Sigmund.
Start with [example.py](src/example.py).

## Prerequisites ##
Python 3 with NumPy, SciPy, matplotlib, OpenCV and prefereble scikit-umfpack with eider Intel mkl or OpenBlas support. Installing OpenCV can be dificult, see the official [instalation manual](http://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_setup/py_setup_in_windows/py_setup_in_windows.html) for more inforamation. The anaconda enviroment imported from Anaconda.yaml file upcomming.

## Generating a new load class ##
The folowing section will explain how to set up a simulation for a new geometry. One is expected to have some knowlege of FEM and topology optimization. No explanation on the simplation settings, such as the resolution, filter size or volume constrain will follow. For information on those topics I recomend reading "Topology Optimization" from M.P. Bends&#248;e and O. Sigmund.

Making a new load class is fairly simple, make the following steps:
 1. Open the [loads.py](topopt/src/loads.py) file
 2. Copy the [HalfBeam class](https://github.com/AJJLagerweij/topopt/blob/1ef7adf60cc21a4467f51391ff65db5c5831ac3a/src/loads.py#L82-L97) to the botom of the file and change the name of the class.
 3. Change the boundery conditions e.g. the load vector and fix certern degrees of freedom (fixdofs). The folowing seciton wil show how to do it for an example problem.

To allow the load vector and fixdofs to change with your mesh size it is important to formulate it as equations of the amount of elements in x and y direction. The nodes are numbered from the top left corner and go down to the botom before starting on the next column of nodes. Funciton [nodes](https://github.com/AJJLagerweij/topopt/blob/1ef7adf60cc21a4467f51391ff65db5c5831ac3a/src/loads.py#L27) can helps with finding the nodal coodinates of point as a function of the number of elements in x and y direction. Take the HalfBeam example which among others is fixed in the y direction at the botom end of the beam. Then using `n1, n2, n3, n4 = self.nodes(self.nelx-1, self.nely-1, self.nelx, self.nely)` returns the nodal coordinates of the element at position nelx-1, nely-1. (the -1 is used as python starts counting from 0) The position of `n1` is in the top left the others are defined in clockwise order. Thus the botom right node, which is the one we need, is `n3`. To convert the nodal position to the position of the x orientation of the node in the displacement or load vector simply multiply by the dimensions used (`self.dim`). For the location of the y orientation simply add `+1` to the x location.

Sometimes a wole side is fixed (due to a wall or symetry) than using the `range(start, stop, step)` can be usefull. Take for exapmle the HalfBeam again. At the left side all movement in x direction is constrained due to symetry. As it is known that ranging numbers from 0 (the top left node its x orientation) to the botom of the last element (`self.dim*(self.nely+1)`) with steps of `self.dim` (2 in the 2D case) contains all the locations in the displacement vector that need to be fixed. Thus the code `[x for x in range(0, self.dim*(self.nely+1), self.dim)]` retuns a list with all these locations. 

![HalfBeamFBD](topopt/img/FBDExample.png)

## To be implemented ##


## Special Thanks To ##
 1. Ole Sigmund and Martin Bends&#248;e from the Technical University of Denmark for there contributions to the field of topology optimization
 2. Li-Yi Wei from the University of Hong Kong for making a beautiful [python code](https://github.com/1iyiwei/topopt)
