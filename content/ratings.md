---
title: RacketLogger Rating Process
---
* TOC
{:toc}

## RacketLogger Rating Process

The RacketLogger league strives to be focused on fun and skill improvement, not pure competition. The most fun is when matches are played among players whose relative level to each other is somewhat similar. This provides the maximum enjoyment and it also arguably leads to the most amount of improvement in skills.

The players register a relative **rating system** as the season progresses. At the end of the season, this rating can determine the outcome of changes in the player play level. This in turn determines which league level that player can sign up for next.

Note that we reserve the right to tweak this algorithm to compensate for feedback related to overrating/underrating players.

## Rules for Rating

The following rules are introduced by RacketLogger for rating leagues:

* The rating is the average of the number of points a player obtains over the matches of a season: R = Sum(ratings)/matches
* Each match provides the player with a rating: positive for the winner and equal but negative for the other player.
* Each match rating is the sum of a **set rating** (positive points) and **game delta** (possibly negative): Match = Set rating + Game delta
* Set rating: if the match went to 2 sets, the winner gets +8 points (-8 for the other player). If it went to 3 sets, the winner gets +5 and the loser -5.
* Game delta: it's the total sum of the differences of games in each set.
* Default and unplayed matches do not count towards the rating

## Moving Levels

* If at the end of the season the rating of a player is +10 or greater, the player is promoted to the next level up in the skills (typically 0.5 levels, e.g. 3.5 to 4.0)
* Conversely, if the rating at the end of a season is -10 or lower, the player is relegated one level down.

## Example

A score of "6-4, 1-6, 6-0" means:

* Set points: the winner gets a rating of +5 and the loser -5.
* Game delta: 3 points (2-5+6) for the winner and -3 for the loser.
* The winner of this match gets a rating of 8 (5+3) and the loser -8, for this match

If the same winning player above plays a subsequent match and gets a +12 rating, the average for these two matches is (8+12)/2 = +10.

If the losing player in the example above now plays a match and gets a +16 rating the average would be (-8+16)/2 = +4 for those two maches.
