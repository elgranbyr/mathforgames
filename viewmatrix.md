

# View Camera Space for Games
The goal of this document is describe the mininum math components to build a general camera model for rendering a 3D world.

## What is a camera in video games
Basacly in a nutshell a camera is an abstract object that is used to bounded what the game should display to player. Ok, everyone knows that but wait..... have you ever ask about how it works? 

Lets to start imagining a fantasy enviroment with some buildings, a main character , some props, etc all those things are set in some part of the space of our 3d enviroment.  Every object has been builded with 3 dimensions also they are laying in a 3D Space. But, once the player watch the TV the he sees only 2D images... WOOOOW how should this posible

Wait... from 3D to 2D ... What is just I read!. But I watch the depth in the image displayed from TV every time that I Move the character or camera... what is the magic?

The first resposability of a camera model is transforming the 3D data in world space into a 3D data into a view Space.   


## Camera Space

Every content in our enviroment has been created using a coordinate system for our case a euclidian space, and each object is placed in relative each other also if any of those do not have a parent object. For instance

Enviroment:

    - coordinate system
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
Continue with our player, he has his own orientations(Axis) and position (this is the same structure like coordinate system) relative to the player space, for instance the player has a local position in 10 units from x axis, but what is his parent space?.

Lets start to remember the Producuct Ax (Matrix x Vector) again., for this instance we assume that player has the same basis axis orientations that the coordinate system this mean $Ax = x = Ix$.  
For each point(x,y,z) in the geometry we need to be transformed by its local space but what hapen with the position of the object

$$
\vec{P_{Local}}+A_{Local}x= \begin{bmatrix}P_{Lx}\\P_{Ly}\\P_{Lz}\end{bmatrix}+\begin{bmatrix}a_{11} & a_{12} & a_{13} \\a_{21} & a_{22} & a_{23} \\a_{31} & a_{31} & a_{33} \end{bmatrix}_{L}\begin{bmatrix}  x\\y\\z\end{bmatrix} = \begin{bmatrix}P_{Lx}\\P_{Ly}\\P_{Lz}\end{bmatrix}+\begin{bmatrix}a_{11}x+a_{12}y+a_{13}z\\a_{21}x+a_{22}y+a_{23}z\\a_{31}x+a_{32}y+a_{33}z\end{bmatrix} = \vec{X}_{Local}
$$
  

  Know we can see that every point is transform by its local space but, it does not consider the World space parent  

$$
\vec{P_{World}}+A_{World}\vec{X}_{local}= \vec{P_{World}}+(A_{World})(\vec{P_{Local}}+A_{Local}x)
$$

Then for this instance we are replacing $A_{World}$ = $A_{local}$ = I

$$
\vec{P_{World}}+A_{World}\vec{X}_{local}= \begin{bmatrix}0\\0\\0\end{bmatrix}_W+\begin{bmatrix}1 & 0 & 0 \\0 &1 &0 \\0 & 0 & 1 \end{bmatrix}_W \begin{bmatrix}   \begin{bmatrix}10\\0\\0\end{bmatrix}_L + \begin{bmatrix}1 & 0 & 0 \\0 &1 &0 \\0 & 0 & 1 \end{bmatrix}_L \begin{bmatrix}  x\\y\\z\end{bmatrix} \end{bmatrix} = \begin{bmatrix}0\\0\\0\end{bmatrix}+\begin{bmatrix}10+x\\0+y\\0+z\end{bmatrix} 
$$

So for any vertex in the geometry will considere the local and World space.

## Transformations
Until know we are working with 2 math concepts, first is a linear transformation and second vector translations.   
This section do not pretend cover linear matrix transformation.

### Linear transformations
A linear transformation is a function, this mean that a function has domain and codomain. Noted that this function is working in a vectoreal spaces. So we have 2 vectoreal spaces $U$ and $W$ and a function $F$ that map from $U$ to $W$ $F:U\to W$, but there are some rules for this function. 

$$
F:R^3\to R^3  \text{ is a linear transformation is if and only if }
\newline
\   
\newline
1. F(\vec{u}+\vec{v}) = F(\vec{u})+F(\vec{v}) \ \forall \ \vec{u},\vec{v} \in U
\newline
2.F(k\vec{u})=kF(\vec{u}) \ \forall \ \vec{u} \in U, \forall \ k \in R
\newline
\   
\newline
\text{Then, if we rewrite this with a matrix } M:R^3\to R^3 \text{ and this map vector to vector by } 
\vec{W}=M\vec{U}
\newline
 \text{ The linearity can be described by } 
 \newline
 \  
 \newline
 \ M(k\vec{u}+\vec{v}) = kM\vec{u}+M\vec{v} \ | \ \vec{u},\vec{v} \in R^3
$$ 

### Vector Translation
Vector translation is defined by a fixed vector $\vec{v} \in R^3$ such that 

$$
\vec{Y} =\vec{u} + \vec{v} \ | \ \forall \ \vec{u} , \vec{v} \in R^3
$$

This function can not be expresed like a linear transformation in the form $\vec{W}=M\vec{U}$ for $R^3$ and this is the reason why our space until know has been  expreced as

$$
\vec{Y}=\vec{P_{World}}+A_{World}\vec{X}
$$

Remember P is our Origin of our space this is a fixed vector and the prodoct $Ax$ is other vector in $R^3$ so, our cordinate system was expressed as a linear transformation and vector translation.

### Homogeneous Transformation
In order to introduce the concept of Homogeneous transformation I will describe what is a projection, in general term projection is a mapping of a set wich is idempotent

$$
\newline
\text{A function f such that }  f:M \to M \text{ of a set M its called idempotent if }
\newline
f \circ f=f  \text{ such that }
\newline \forall x \in M, f(f(x)))=f(x) \implies f(x)=x , \forall x \in f(M)
$$

we define a vector $\vec{u}=(x,y,z) \in R^3$ into 4D space $R^4$ using a $w$ component $\vec{v}=(x,y,z,w) \in R^4$ such that

$$ 
    \vec{u}=(x,y,z) \to (x,y,z,1) \text{in this way we can project any point onto the hyperplane w=1 by} 
\newline
    \vec{v}=(x,y,z,w)\to(\frac{x}w,\frac{y}w,\frac{z}w,1)
$$
This vector $\vec{v}$ is called homogeneous coordinate, lets then to describe a homogeneous matrix

Right now we need a vectorial transalation to represent any local space for $R^3$, we want to descrbie $Ax$ producto such that $A$ is a basis space in $R^4$ and $x \in R^4 \text{ in a homogeneous coordinate}$ 

$$
H_A=\begin{bmatrix}M_{33} & \vec{P}_{31}\\\vec{S^T}_{13}&1\end{bmatrix}
\newline
 M_{33} \text{ is a matrix space } \in R^3, \vec{p} \text{ is the constant vector (Origin)} \in R^3, s^T=0 \text{ The vector } \vec{s} \text{ will cover in projection space }
$$
So in this way we will use $H_A$ how our homogeneous matrix $\vec{Y}=H_A\vec{x}$

$$
H_A=\begin{bmatrix}M & \vec{p}\\\vec{S^T}&1\end{bmatrix}\begin{bmatrix}\vec{x} \\ w\end{bmatrix} = 
\begin{bmatrix}M\vec{x}+w\vec{p} \\ \vec{S^T}\vec{x}+ w\end{bmatrix} \text{Applying coordinate homogeneous }

\begin{bmatrix} \frac{M\vec{x}+w\vec{p}}{\vec{S^T}\vec{x}+ w}  \\ 1 \end{bmatrix} 

$$

So right now we can describe any point translation in $R^3$ using our Homogeneous matrix, 




$$
\vec{Y} = R\vec{x}+\vec{P} \backsim  H_{A}\vec{x} = 
\begin{bmatrix}M & \vec{p}\\\vec{0^T}&1\end{bmatrix}\begin{bmatrix}\vec{x} \\ 1\end{bmatrix}=\begin{bmatrix}\vec{Y} \\ 1\end{bmatrix}
$$ 

Then homogeneous matrix four our coordinate system,  Origin $\vec{P}=\vec{0}$ and  $M=\begin{bmatrix}\vec{i}&\vec{j}&\vec{k}\end{bmatrix}$ is describe in $R^4$ 

$$
H_{R^3}=\begin{bmatrix}
        \begin{bmatrix}
              1 & 0 & 0
            \\0 & 1 & 0
            \\0 & 0 & 1
        \end{bmatrix}_M &\begin{bmatrix}0\\0\\0\end{bmatrix}_P 
        \\ \begin{bmatrix} 0 & 0 & 0\end{bmatrix}_S&1
    \end{bmatrix}
$$

and homogeneous transformation $\vec{Y} = H_{R^3}\vec{x}$

$$
\vec{Y} =\begin{bmatrix}
        
              1 & 0 & 0 & 0_{p_x}
            \\0 & 1 & 0 & 0_{p_y}
            \\0 & 0 & 1 & 0_{p_z}
            \\0 & 0 & 0 & 1
    \end{bmatrix}
    \begin{bmatrix}x\\y\\z\\1\end{bmatrix}_{\vec{x}} = 
    \begin{bmatrix}x+0_{p_x}\\y+0_{p_y}\\z+0_{p_z}\\1\end{bmatrix}
$$

In this way we can translate any point using a linear transformation changing the vector P in the matrix.


## Put it all together In World space

We saw in local space that it can be expresses like susetion of transformations

$$
\vec{Y} =\vec{P_{World}}+A_{World}\vec{X}_{local}= \vec{P_{World}}+(A_{World})(\vec{P_{Local}}+A_{Local}x)
$$

Then we need rewrite the formula as a sussesions of linear transformations

$$
\begin{bmatrix}\vec{Y} \\ 1\end{bmatrix}=H_{0}H_{1}H_{2}...H_{n}\vec{x}
$$

For our player example $H_n=G$ is our World Space(Coordinate system, the vector $\vec{o}$ is the origin), $H_{n-1}=L$ is the player space (Local space, the vector $\vec{p}$ is the player position) 

$$
\vec{Y} =\begin{bmatrix}
        
              1 & 0 & 0 & 10_{p_x}
            \\0 & 1 & 0 & 0_{p_y}
            \\0 & 0 & 1 & 0_{p_z}
            \\0 & 0 & 0 & 1
    \end{bmatrix}_L 
    \begin{bmatrix}
        
              1 & 0 & 0 & 0_{0_x}
            \\0 & 1 & 0 & 0_{0_y}
            \\0 & 0 & 1 & 0_{0_z}
            \\0 & 0 & 0 & 1
    \end{bmatrix}_W
    \begin{bmatrix}x\\y\\z\\1\end{bmatrix}_{\vec{x}} = 
    \begin{bmatrix}x+10_{p_x}\\y+0_{p_y}\\z+0_{p_z}\\1\end{bmatrix}
$$

Note that our World space is in the right side of maxtrix product. why we need to do that, because the local space does not to have transform the parent spaces, matrix product is not conmutative.

## Camera Space
As we see, every space has defined by a vector translation and a linear transformation
$\vec{Y} =\vec{P}+A\vec{x} \backsim \begin{bmatrix}\vec{Y} \\ 1\end{bmatrix}=H\vec{x}$, and camera model should  be describe like a homogeneous matrix too. The camera has a location in a World space $\vec{c}$, this position should be the origin of the camera space. The three axis that form a orthonormal matrix, represents the orientation of the camera, this mean one of this axis should be the direction view. $\vec{v}_{direction}$, and exits other two vectors mutually perpendicular eachother then one of them is the the Up vector $\vec{u}_{}$ and the last vector $\vec{r}$ is defined by cross product $\vec{r}=\vec{v}\times\vec{u}$ , then $M=\begin{bmatrix}\vec{r} & \vec{u} & \vec{v}\end{bmatrix}$, Then we can write Any point $\vec{x} =\vec{c}+M\vec{y}$ solving this, Remember that M is orthonormal and property $M^{-1}=M^T, then \ \vec{y}=M^T(\vec{x}-\vec{c})$

Then $H_{View \ Space}=\begin{bmatrix}M^T & -M^T\vec{c}\\\vec{0^T}&1\end{bmatrix}$
and Any Homogeneous transformation can be describe like

$$
\\\vec{Y} =H\vec{x}   \to \begin{bmatrix}M^T & -M^T\vec{c}\\\vec{0^T}&1\end{bmatrix}\begin{bmatrix}\vec{x} \\ 1\end{bmatrix}=\begin{bmatrix}\vec{Y} \\ 1\end{bmatrix}
$$

Lets to Explain $H_{View Space}$: Basicly we want to describe the World space in term of the camera space, this mean that the origin of this spaces is the camera position and the coordinate axis are the orientation vectors of the camera

$$
M^T=\begin{bmatrix} \vec{r}.x & \vec{r}.y & \vec{r}.z \\ \vec{u}.x & \vec{u}.y & \vec{u}.z \\ \vec{v}.x & \vec{v}.y & \vec{v}.z \end{bmatrix}
$$

Translation vector $\vec{T}$ as we can see $-(M^T\vec{c})$ is an $Ax$ product this Transposed matrix has in its rows each vector axis, this mean that the operation of this product can be described by dot product of each axis with its position:  

$$
\vec{T}=-M^T\vec{c}=
\begin{bmatrix}-(\vec{r} \cdot \vec{c}) \\ -(\vec{u} \cdot \vec{c}) \\ -(\vec{v} \cdot \vec{c}) \end{bmatrix}
$$

Then why the minus in the Translation vector?. This is because we want to bring the space to origin. so

$$
H_{ViewSpace}=\begin{bmatrix} \vec{r}.x & \vec{r}.y & \vec{r}.z & -(\vec{r} \cdot \vec{c})\\ \vec{u}.x & \vec{u}.y & \vec{u}.z & -(\vec{u} \cdot \vec{c})\\ \vec{v}.x & \vec{v}.y & \vec{v}.z & -(\vec{v} \cdot \vec{c}) \\ 0 & 0 & 0& 1\end{bmatrix}
$$

Positive view axis is going to the screen (this is the case for OpenGL)
Example:

$$
\text{Camera position } \vec{c}= \begin{bmatrix} 5 \\ 5 \\ 5 \end{bmatrix}
\text{LookAt position } \vec{p}= \begin{bmatrix} 5 \\ 0 \\ 0 \end{bmatrix}
\newline
$$
Build the axes based on Camera position and lookat point. 

$$
\   
\text{Camera axis } 
\vec{r}= \begin{bmatrix} 1 \\ 0 \\ 0  \end{bmatrix}
,\vec{u}= \begin{bmatrix} 0 \\ \frac{1} {\sqrt{2}} \\ -\frac{1} {\sqrt{2}} \end{bmatrix}
\vec{v}= \begin{bmatrix} 0 \\ \frac{1} {\sqrt{2}} \\ \frac{1} {\sqrt{2}} \end{bmatrix}
$$

$$
H=\begin{bmatrix} 1 & 0 & 0          & -(5)
\\ 0 & \frac{1} {\sqrt{2}} & -\frac{1} {\sqrt{2}} & -(0)
\\ 0 & \frac{1} {\sqrt{2}} & \frac{1} {\sqrt{2}} & -(\frac{10} {\sqrt{2}})
 \\ 0 & 0 & 0& 1\end{bmatrix}
$$
Lets Test the Camera Target and camera position

$$
Target=\begin{bmatrix} 1 & 0 & 0          & -(5)
\\ 0 & \frac{1} {\sqrt{2}} & -\frac{1} {\sqrt{2}} & -(0)
\\ 0 & \frac{1} {\sqrt{2}} & \frac{1} {\sqrt{2}} & -(\frac{10} {\sqrt{2}})
 \\ 0 & 0 & 0& 1\end{bmatrix}
\begin{bmatrix}5  \\ 0 \\ 0 \\ 1\end{bmatrix}=
\begin{bmatrix}0  \\ 0 \\ -(\frac{10} {\sqrt{2}}) \\ 1\end{bmatrix}
$$

$$
Camera=\begin{bmatrix} 1 & 0 & 0          & -(5)
\\ 0 & \frac{1} {\sqrt{2}} & -\frac{1} {\sqrt{2}} & -(0)
\\ 0 & \frac{1} {\sqrt{2}} & \frac{1} {\sqrt{2}} & -(\frac{10} {\sqrt{2}})
 \\ 0 & 0 & 0& 1\end{bmatrix}
\begin{bmatrix}5  \\ 5 \\ 5 \\ 1\end{bmatrix}=
\begin{bmatrix}0  \\ 0 \\ 0 \\ 1\end{bmatrix}
$$

Know you can see the camera position is the origin of the space, the target is infront of the camera, back to screen.

So what happen with point if they lay back to the camera. lets to test the point $\text{Test point } \vec{x}= \begin{bmatrix} 5 \\ 10 \\ 10 \end{bmatrix}$

$$
Camera=\begin{bmatrix} 1 & 0 & 0          & -(5)
\\ 0 & \frac{1} {\sqrt{2}} & -\frac{1} {\sqrt{2}} & -(0)
\\ 0 & \frac{1} {\sqrt{2}} & \frac{1} {\sqrt{2}} & -(\frac{10} {\sqrt{2}})
 \\ 0 & 0 & 0& 1\end{bmatrix}
\begin{bmatrix}5  \\ 10 \\ 10 \\ 1\end{bmatrix}=
\begin{bmatrix}0  \\ 0 \\ (\frac{10} {\sqrt{2}}) \\ 1\end{bmatrix}
$$
 Know you can see the vertex is in the positive side of view axis. 

 And this is the reason why this matrix is called view space, because any vertex of world space is transformed into a new space by linear transformation.


The pipeline For transforming any point of any world space into a view space is required a matrix product

 $$
 \vec{y} =(H_{ViewSpace})*(H_{WorldSpace})\vec{x}
 $$

 $$
 \begin{bmatrix}\vec{y} \\ 1\end{bmatrix}=\begin{bmatrix}M^T & -M^T\vec{c}\\\vec{0^T}&1\end{bmatrix}
\begin{bmatrix}M & \vec{p}\\\vec{0^T}&1\end{bmatrix}
\begin{bmatrix}\vec{x} \\ 1\end{bmatrix}
 
 $$


