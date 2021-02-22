# Baseball-Grade
A work-in-progress of a Baseball Stat Designed to assign a value to a player in an easy to interpret format


The idea of Grade is to **grade**** a player. 
I feel like the more stats that become available for Baseball, the harder it is concisely analyze a player based on statistics alone.
I'm also in the middle on the old-fashioned vs. Sabremetric scale
Do we need to analyze deeper than total stats and batting average? Definitely
Are these stats still valuable? I think so

The grade for a player is determined from the individual season, and based on the player's position

The following information has been tested with 2019 data, and is not concrete.
Pre 2003: Advanced defensive statistics are not available, more weight is placed on WAR, dWAR, and DEF to account for this

By adjusting the maximum and minimum values based on the player's position that year,
Grade essentially evaluates the value of that player compared to the best available at that position, that season
So a 2B and 1B with similar power numbers are likely to be graded differently, as 1B typically will have a higher maximum and minimum from
qualified players, so 25 HRs on the year will result in a different amount of HR points given to that player (breakdown and example below.

The Grade breakdown is as follows: 

**Catchers**
Offense and WAR: 80%
        Homeruns (HR) - 5%
        Hits (H)- 5%
        Total Bases (TB) - 5%
        Runs Batted In (RBI) - 5%
        Batting Average (BA) - 10%
        Slugging Percentage (SLG) - 5%
        On-Base Plus Slugging (OPS) - 10%
        weighted On Base Average (wOBA) - 15%
        weighted Runs Created Plus (wRC+) - 15%
        Walks per Strikeout (BB/K) - 5%
        bWAR - 10%
        fWAR - 10%
        
        Total of 100% Offense/Value -> 80% Total
        
Defense: 20%
        Defense (DEF) - 25%
        defensive WAR (dWAR) - 25%
        Defensive Runs Saved (DRS) - 25%
        Stolen Base Percentage (SBP) - 25%
        
        Total of 100% Defense -> 20% Total

**All other Hitters**
Offense and WAR: 80%
        Homeruns (HR) - 5%
        Hits (H)- 5%
        Total Bases (TB) - 5%
        Runs Batted In (RBI) - 5%
        Batting Average (BA) - 10%
        Slugging Percentage (SLG) - 5%
        On-Base Plus Slugging (OPS) - 10%
        weighted On Base Average (wOBA) - 15%
        weighted Runs Created Plus (wRC+) - 15%
        Walks per Strikeout (BB/K) - 5%
        bWAR - 10%
        fWAR - 10%
        
        Total of 100% Offense/Value -> 80% Total
        
Defense: 20%
        Defense (DEF) - 20%
        defensive WAR (dWAR) - 20%
        Defensive Runs Saved (DRS) - 30%
        Ultimate Zone Rating (UZR) - 30%
        
        Total of 100% Defense -> 20% Total

**Pitchers**
Strikeouts per Nine Innings (K/9) - 10%
Strikeouts per Walk (K/BB) - 5%
Walks Per Nine Innings (BB/9) - 10%
Earned Run Average - (ERA) - 10%
Fielding Independent Pitching (FIP) - 10% 
fWAR - 7.5%
bWAR - 7.5%
Adjusted Earned Run Average (ERA-) - 10%
Adjusted Fielding Independent Pitching (FIP-) - 15% 
Walks and Hits Per Inning (WHIP) - 15%

For a total of 100%
* Percentages are not concrete, I am very open to suggestions here or suggestions to different stats to use, the main reason I'm putting this on GitHub is for those suggestions
This goes for every type not just for pitchers

**Assigning Values for Each Stat**
I'm currently creating a script to find a Max/Min for each stat, at each position (SP/RP for pitchers)
I also believe that being average in a stat is more valuable than 50% of the possible points 

I created a function that looks like a sideways trinomial
If a player has greater than or equal to half of the (max - min) for a stat, 2/3 of their points for that stat will come from a logarithmic function
If a player has less than half of the (max - min) for a stat, 2/3 of their points for that stat will come from an exponential function
The remaining 1/3 of the grade will come from the function not used
This is done to create a scale that rewards players for being at least average for a certain statistic, and penalizes them for being below average in the statistic.

Whoever gest the maximum for a stat at each position will receive 100% of that stat's possible point
Whoever has the minimum value for that stat at each position, will receive 0 points

For an example:

In 2019, Pete Alonso had the max HRs for 1B: 53, Daniel Murphy the min: 13 (for qualified players)
Same year, Gleyber Torres led 2B with 38, Yolmer Sanchez had the minimum of 2 (for qualified players)

Ketel Marte hit 32 HRs, Jose Abreau hit 33

Here is the formula for the amount of HRPoints given to each player:

	if (HR < (HRMax1B/2) {
			HRPoints = (exp(.5 * ((HR - HRMin1B)/ (HRMax1B - HRMin1B))) - 1) * (1.541 * 3.34) + (log(((HR - HRMin1B)/ (HRMax1B - HRMin1B)) + 1) * 1.443 * 1.66);
			// The above is assuming that the percentage for HRs is 5
		}
		else {
			HRPoints = (exp(.5 * ((HR - HRMin1B) /(HRMax1B - HRMin1B))) - 1) * (1.541 * 1.66) + (log(((HR - HRMin1B) / (HRMax1B - HRMin1B)) + 1) * 1.443 * 3.34);
			// The above is assuming that the percentage for HRs is 5
		}
	if (HR < HRMax2B/2) {
			HRPoints = (exp(.5 * ((HR - HRMin2B) / (HRMax2B - HRMin2B))) - 1) * (1.541 * 3.34) + (log(((HR - HRMin2B) / (HRMax2B - HRMin2B)) + 1) * 1.443 * 1.66);
			// The above is assuming that the percentage for HRs is 5
		}
		else {
			HRPoints = (exp(.5 * ((HR - HRMin2B) /(HRMax2B - HRMin2B))) - 1) * (1.541 * 1.66) + (log(((HR - HRMin2B) / (HRMax2B - HRMin2B)) + 1) * 1.443 * 3.34);
			// The above is assuming that the percentage for HRs is 5
		}
HRMax1B / 2 = 26.5
HRMax2B / 2 = 19

Both players are over the half requirement to be graded more favorably, with the majority of the weight being on the logarithmic function instead of exponential

Jose Abreau - (e^(.5*(33-13/53-13)-1)(1.541(1.67)) + (log(33-13/53-13)+1)(1.443(1.66))
Ketel Marte - (e^(.5*(33-2/38-2)-1)(1.541(1.67)) + (log(32-2/38-2)+1)(1.443(1.66))

Jose Abreau - .73 + 1.95 = 2.68 out of 5 possible
Ketel Marte - 1.33 + 2.91 = 4.24 out of 5 possible

The two homerun totals are practically identical, but Ketel Marte's was much more valuable when using the players' positions and the expected output of that position that season

By doing this for every stat, the output of Grade represents the value of the player compared to the best possible at that position, that season

So the overall grade value reads as a percentage of possible production based on what actually happened that season. 
Someone with a grade of 87 for a season produced 87% of the maximum output at that position that season
The same logic is applied to the total Offense and Defense values

**Career Grade**
I hade a career Grade calculator, that used a 7 year prime along with career stats to grade the career
I only had career Maximum values when I made that, so the prime maximums were generated based on career total leaders
This led to possible grades greater than 100, which is counterintuitive

The prime stats accounted for 75%, and the career stats for 25%, added together makes 100% career grade

My new idea is to grade each individual season, and use the 7 highest grades the player has to create an average, that will constitute 75% of Career Grade
The remaining seasons' average would constitute the other 25% of Career Grade
I don't think this is perfect, and I will certainly tweak it as I get the data available, and would love suggestions on it

**Output**
I plan on putting the data into a grid with a GUI that lets the user sort based on overall, position, team, offense, or defense
Being able to filter based on team or year would be great too
I'm relatively inexperienced in this sort of programming compared to what would be expected to make this happen, so I'd love any suggestions on making this happen

**Suggestions/Questions**
Feel free to ask or suggest something about anything in here
At the very least this is enjoyable for me, and I plan on making something simmilar for Basketball
I can definitely handle criticism if this format doesn't seem to accomplish what I'm setting out to do feel free to let me know
I will also be uploading code soon as I test it more

I currently have a player rater that will work if you set the max/min values for each position by hard-coding. 
This is not ideal and I am working on storing all this data and referencing it to make things easier



