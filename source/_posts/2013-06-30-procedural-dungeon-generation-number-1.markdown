title: "Procedural Dungeon Generation #1"
date: 2013-06-30 23:43
tags:
- procedural
---

This post briefly explains a technique for generating randomized dungeons<!-- more --> that is loosely based on the one explained [here](http://www.youtube.com/watch?v=GcM9Ynfzll0#t=05m20s).

## Grid Generation

Generate a grid:

{% img center /images/grid.png %}

## Grid Difficulty Coloring 

Color the grid with x red rooms (hard), y blue rooms (medium) and z green rooms (easy) such that x+y+z = n rooms in the grid (x, y, z can be changed to control difficulty):

**1.** First color it with x red rooms such that no red room (or most red rooms) has another same colored neighbor (this can be changed if dungeon difficulty should be higher);
**2.** Then for each red room color one neighbor blue and one neighbor green;
**3.** Color the rest of the rooms with the remaining number of rooms for each color.

{% img center /images/color.png %}

## Dungeon Path Creation

Choose two nodes that are far apart enough and then find a path between them while mostly avoiding red rooms. If you choose a proper x, since red rooms can't be neighbors to themselves and the pathfinding algorithm doesn't go for diagonals, it should create a not so direct path from one node to the other.

{% img center /images/path.png %}

For all nodes in the path, add their red[, green, blue] neighbors. This should add the possibility of side paths and overall complexity/difficulty in the dungeon.

{% img center /images/filled-path.png %}

## Room Creation and Connection

Join smaller grids into bigger ones according to predefined room sizes. Higher chance of smaller width/height and lower chance of bigger width/height.

{% img center /images/join.png %}

Generate all possible connections between rooms.

{% img center /images/connected.png %}

Randomly remove connections until a certain number of connections per room is met. If n = total rooms, then set n\*x rooms with >=4 connections, n\*y with 3, n\*z with 2 and n\*w with 1, such that x+y+z+w=1. Controlling x, y, z and w lets you control how mazy the dungeon gets. If z or w are considerably higher than x or y then there won't be many "hub" rooms that connect different paths, so it's gonna have lots of different thin paths with dead ends. If x or y are higher then the dungeon will be super connected and therefore easier.

{% img center /images/disconnected.png %}

Reconnect isolated "islands". Since step 7 is completely random, there's a big chance that rooms or groups of rooms will become unreachable. 

{% img center /images/disconnected-groups.png %}

To fix that:

**1.** Flood fill to figure out how many isolated groups exist;
**2.** Pick a group at random and go through its rooms. For each room check if it neighbors a room belonging to another group, if it does then connect them and end the search;
**3.** Repeat the previous steps until there's only one group left (everyone's connected).

{% img center /images/final.png %}

## END

And that's it, I guess. There's more stuff like adding special rooms, but that's specific to each game, so whatever. But basically I have a few parameters I can control when creating a new dungeon: dungeon width/height; percentage of hard, medium and easy rooms; what color neighbors get added to the original path and the percentage of rooms with >=4, 3, 2 or 1 connections. I think that's enough to have control of how easy/hard the dungeon will be...
