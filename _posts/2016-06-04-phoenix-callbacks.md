---
layout: post
title: Phoenix callbacks, who needs ‘em?
---

I’m a Rails developer by trade, launching my way into the world of Elixir and Phoenix. While I aim to approach each problem through a fresh, non-Railsy pair of eyes, I typically find myself searching for ways to do Rails-like things in Phoenix, rather than stepping back to first principles and opening my eyes to the new paradigm.

I’m building an application for editing and viewing cave survey files. In the domain, a cave has many surveys, and surveys have many shots.

```
cave: {
 surveys: [
  { 
  name: “foo”,
  shots: [
  {from: “foo1”, to: “foo2”, distance: 11.0, inclination: -12.44},     {from: “foo2”, to: “foo3”, distance: 15.0, inclination: -9.01},
}]
```

A shot represents the journey travelled between two stations. To draw maps and vector diagrams I need to translate the shots into a collection of stations:

```
stations: [
  { name: “foo1”, depth: -9.0, point: [10,0]},
  { name: “foo2”, depth: -9.0, point: [20,4]}]
```

The above data isn’t correct, but you should get the idea. After loading a bunch of surveys and shots, I calculate the depth and point location (x,y) to display the cave as a vector diagram. These calculations all begin at the cave entry point. Surveys are tied into other stations, shot attributes can be edited. In short, the collection of stations need to be rebuilt whenever the cave, survey or shot data is edited. And I want to persist the stations in the database so I can use PostGis functions such as ST_Project to help with geo calculations.

So I went looking for an after_create / update callback that I could hang on to the cave, survey and station models. No dice, model callbacks are actually being removed from Ecto 2. Unfortunately, I can’t build stations in the changeset as the iterator is all about making comparisons with persisted records. More on that design later, I’m sure.

Anyways, it seems I’m not the only one asking what the hell am I meant to use instead? I thought about scattering update calls around the controllers or making one mahoosive transactional update. Neither very appealing.

And then the penny dropped. I could solve this with the design. Stations don’t need to be tied to CRUD actions. Stations only need to be built when someone wants to see the map. The responsibility can be pushed into the controller that’s rendering the map. Grit my Rails-way teeth and do a little more work in the controller. I can build stations on demand and in one place. And I never have to worry about stale data. I’ve also decoupled the map from the CRUD actions.
```
Instead of a route like this: /caves/my-big-cave/map

I now have a map-centric route: /maps/my-big-cave
```
