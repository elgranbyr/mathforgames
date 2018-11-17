# View Camera Space and Projection Space for Games
The goal of this document is describe the mininum math components to build a general camera model for rendering a 3D world.

## What is a camera in video games
Basacly in a nutshell a camera is an abstract object that is used to bounded what the game should display to player. Ok, everyone knows that but wait..... have you ever ask about how it works? 

Lets to start imagining a fantasy enviroment with some buildings, a main character , some props, etc all those things are set in some part of the space of our 3d enviroment.  Every object has been builded with 3 dimensions also they are laying in a 3D Space. But, once the player watch the TV the he sees only 2D images... WOOOOW how should this posible

Wait... from 3D to 2D ... What is just I read!. But I watch the depth in the image displayed from TV every time that I Move the character or camera... what is the magic?

The first resposability of a camera model is transforming the 3D data in world space into a 3D data into a view Space.   


## Camera Space

Every content in our enviroment has been created using a coordinate system for our case a euclidian space, and each object is placed in relative each other also if any of those do not have a parent object. For instance

Enviroment:

    - Ground (0,0,0)
        - field
        - Forest
            - Tree_A
                - leaf
                - trunk
            - Tree_B
            - Tree_C
    - Player (10,0,0)
     
In this world the objects Ground and  Player was placed without any relative object, but they exists in the space. what happen here!. The artist modeled those objects in cordinate space 

<img src="./img/zyzcoordinate.jpg" alt="Kitten"
	title="A cute kitten" width="300" height="300" />


What does it means?


1. The ground is in the origin of the coordinate system 
2. The Player is place in 10 units from the origin in the x-axes 

$$ 
Ground= \begin{bmatrix}0 \\ 0\\ 0 \end{bmatrix} 
Player=\begin{bmatrix}10 \\ 0\\ 0 \end{bmatrix} 
$$ 

3. World Space components

  
$$ 
\\
Origin=
\begin{bmatrix}0 \\ 0\\ 0 \end{bmatrix} 
\quad
i= \begin{bmatrix}1 \\ 0\\ 0 \end{bmatrix} 
j= \begin{bmatrix}0 \\ 1\\ 0 \end{bmatrix} 
k= \begin{bmatrix}0 \\ 0\\ 1 \end{bmatrix} 
\\
$$

## Cordinate system
A coordinate system has 4 components. An origin $\vec{P}$ and three axis vector $\vec{i} \ \vec{j} \ \vec{k}$
, eachone is a unit length vector and mutually 
perpendidular. We need rewrite the axis in a matrix form, this matrix should be Orthonormal*. 




$$
Standard \ euclidian \ coordinate \ system \\ \begin{bmatrix}\vec{i}&\vec{j}&\vec{k}\end{bmatrix} =  \begin{bmatrix}1 & 0 & 0 \\0 &1 &0 \\0 & 0 & 1 \end{bmatrix} = I 
\newline
\   
\newline
\text{
This Orthonormal matrix R have this properties 
}
R^{-1}= R^T, \ and \ the \det({R})=1 
\newline
\text{ If det(R) =1  this system is right handed., If det(R) = -1  this system is Left handed.}
$$


Know we need to describe any point given a coordinate system, Any Vector can be described like a linear combination so

$$
\vec{X} = \vec{P} + y_0\vec{i}+ y_1\vec{j}+ y_2\vec{k} = \vec{P}+R\vec{Y}
$$, 
Then $Ax=b$, This property for solve $\vec{Y}$

$$
 \vec{Y}= R^{-1}(\vec{X}-\vec{P})=R^T(\vec{X}-\vec{P})
$$



## Local space:  
Continue with our player, he has his own orientations(Axis) and position (this is the same structure like coordinate system) relative to the player space, for instance the player has a local position in 10 unix far from x axis, but what is his parent space?.

Lets start to remember the Producuct Ax (Matrix x Vector) again., for this instance we assume that player has the same basis axis orientations that the coordinate system this mean $Ax = x = Ix$.  
For each point(x,y,z) in the geometry we need to be transformed by its local space but what hapen with the position of the object

$$
\vec{P}+Ax= \begin{bmatrix}P_x\\P_y\\P_z\end{bmatrix}+\begin{bmatrix}1 & 0 & 0 \\0 &1 &0 \\0 & 0 & 1 \end{bmatrix}\begin{bmatrix}x\\y\\z\end{bmatrix} = \begin{bmatrix}P_x\\P_y\\P_z\end{bmatrix}+\begin{bmatrix}x\\y\\z\end{bmatrix}
$$
  
  Know we can see that every point transformed by matrix do not consider the local position because the local position is not cosidered in the Ax product, lets to introduce it, what is the result desired?

## Homogeneous transformation
