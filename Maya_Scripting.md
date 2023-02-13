# MEL
### Control rig using selected edges

```c++
string $selection[] = `ls -sl -fl`;
for ($i=0;$i<size($selection);$i++){
	select -cl;
	select -r $selection[$i];
	polyToCurve -form 2 -degree 1 -conformToSmoothMeshPreview 1;
}
string $name = "change_curve_name_"+(string)int(rand (0,8000));
group -em -name $name;
string $shape_list[] = `ls -s "polyToCurveShape*"`;
for($j=0;$j<size($shape_list);$j++)
{
	if($j==0) select -r $shape_list[0];	
	else select -add $shape_list[$j];	
}
select -add $name;
parent -r -s;
select -r "polyToCurve*";
select -d "polyToCurveShape*";
doDelete;
```

### Motion path automation

```c++
for( $i=1; $i<3; ++$i)
{
    $objs = "s"+(string)$i;
    $curve_paths = "path"+(string)$i;
    select -r $objs;
    select -add $curve_paths;
    pathAnimation -fractionMode true -follow true -followAxis x -upAxis y -worldUpType "vector" -worldUpVector 0 1 0 -inverseUp false -inverseFront false -bank false -startTimeU `playbackOptions -query -minTime` -endTimeU  `playbackOptions -query -maxTime`;
    print($objs+" is parented");
}
```

### Move object to other object's pivot

```c++
string $selection[] = `ls -sl -fl`;
$pivot_ref = $selection[0];
$move_geo = $selection[1];

float $pivot_ref_pos[] = `objectCenter -gl $pivot_ref`;

$x = $pivot_ref_pos[0];
$y = $pivot_ref_pos[1];
$z = $pivot_ref_pos[2];
move -absolute $x $y $z $move_geo;
```

# Python
### Add suffix for selection with incremental numbering

```python
import maya.cmds as cmds
obj_names = cmds.ls(selection=True)
suffix = input("Enter the suffix")
numbering = input("Want numbering?")


for i in range(len(obj_names)):
    if(numbering=="yes"):
        cmds.rename(obj_names[i],obj_names[i]+suffix+"_"+str(i))
    else:
        cmds.rename(obj_names[i],obj_names[i]+suffix)
```

### Lowest point in mesh

```python
import maya.cmds as cmds
vtx_count = cmds.polyEvaluate(v=True) 
obj_name = cmds.ls(selection=True)
print(obj_name[0])
print(vtx_count)
y_pos = []
for i in range(vtx_count):
    vtx_address = obj_name[0]+".vtx["+str(i)+"]"
    tmp = cmds.pointPosition(vtx_address)
    print(tmp[1]) 
    y_pos.append(tmp[1])


print("\n\n The least value of y is")
print(min(y_pos))
```

### Joint angle query

```python
import maya.cmds as cmds
obj_names = cmds.ls(selection=True)

for i in range(0,len(obj_names)):
    print("Joint: "+obj_names[i])
    tempOrientation_x = cmds.joint(obj_names[i],q=True,ax=True)
    tempOrientation_y = cmds.joint(obj_names[i],q=True,ay=True)
    tempOrientation_z = cmds.joint(obj_names[i],q=True,az=True)
    print(str(tempOrientation_x)+" "+str(tempOrientation_y)+" "+str(tempOrientation_z))
```

### Maya distance between two points

```python
import maya.cmds as cmds
import math
obj_names = cmds.ls(selection=True)
def diffsquare(a,b):
    return (b-a)**2

if(len(obj_names)==2):
    point1 = cmds.pointPosition(obj_names[1])
    point2 = cmds.pointPosition(obj_names[0])
    
    x1 = point1[0]
    x2 = point1[1]
    x3 = point1[2]
    
    y1 = point2[0]
    y2 = point2[1]
    y3 = point2[2]

    print("\n\nEdge length: "),
    print(math.sqrt(diffsquare(x1,y1)+diffsquare(x2,y2)+diffsquare(x3,y3)))
else:
    print("Select any two object and try again")
```

### Reset transformation shelf tool

```python
import maya.cmds as cmds
obj_names = cmds.ls(selection=True)

for object in obj_names:
    cmds.move(0,0,0,object,absolute=True)
    cmds.rotate(0,0,0,object,absolute=True)
    cmds.scale(1,1,1,object,absolute=True)
```