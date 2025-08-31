# IPL_Analysis
Power_Bi Dashboard(Ipl analysis)

Project Title

IPL Analysis Dashboard (2008–2025) | Power BI



📌 Overview

This project is an interactive IPL (Indian Premier League) Analysis Dashboard created using Power BI.
It provides a detailed overview of IPL statistics from 2008 to 2025, including team performance, Orange Cap & Purple Cap stats, top players, and season-wise analysis.



📊 Key Features

Champion & Runner-up Teams visualization

Orange Cap & Purple Cap Holders with runs and wickets stats

Max Sixes & Max Fours player highlights

Points Table with Matches, Wins, Losses, NR, and Points

Season Highlights: Total 6s, 4s, Matches, Teams, Centuries, Half Centuries, Venues

Social media & reference links integrated for better accessibility



🛠 Tools & Technologies

Power BI → Dashboard creation, DAX formulas, Data Modeling

Excel / CSV → Data collection & preprocessing

Data Cleaning → Removed duplicates, formatted columns (Teams, Players, Matches)

Visualizations Used: Card visuals, Table, Images, Custom Icons, and KPI visuals



📂 Dataset

Historical IPL data (2008–2025) including match results, player statistics, and team standings.

Sources: Kaggle, ESPN CricInfo, and IPLT20.com



🚀 Process

Collected raw IPL datasets (matches, players, points table).

Cleaned and preprocessed data in Excel/Power Query.

Created data model in Power BI (relationships between players, teams, matches).

Built visuals & KPIs to track Orange Cap, Purple Cap, Sixes, Fours, and Team Performance.

Designed an interactive dashboard with branding (TATA IPL theme).



📷 Screenshot


<img width="1287" height="771" alt="Relationship Table" src="https://github.com/user-attachments/assets/fc681b9e-ca88-46da-b01a-abb6b014280d" />

<img width="1494" height="804" alt="Dashboard Snap" src="https://github.com/user-attachments/assets/3ed3c248-f190-4719-8ad2-a9511ba1bc63" />


📈 Insights Generated

RCB won IPL 2025 with Punjab Kings as Runner-up.

B Sai Sudharsan (GT) – Orange Cap holder with 759 runs.

M Prasidh Krishna (GT) – Purple Cap holder with 25 wickets.

N Pooran (LSG) – Most Sixes (40).

B Sai Sudharsan (LSG) – Most Fours (88).


=============================================================================================


Centuries = 
VAR SlectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR SeasonData = FILTER(ball_by_ball_data,
                        RELATED(ipl_matches_data[season]) = SlectedSeason)
VAR BattetRuns =
    SUMMARIZE(
        SeasonData,
        ball_by_ball_data[match_id],
        ball_by_ball_data[batter],
        "TotalRuns", SUM(ball_by_ball_data[batter_runs])
    )

VAR CenturyCount = FILTER(BattetRuns, [TotalRuns] >= 100)

RETURN COUNTROWS(CenturyCount)

=============================================================================================
Half Centuries = 
VAR SlectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR SeasonData = FILTER(ball_by_ball_data,
                        RELATED(ipl_matches_data[season]) = SlectedSeason)
VAR BattetRuns =
    SUMMARIZE(
        SeasonData,
        ball_by_ball_data[match_id],
        ball_by_ball_data[batter],
        "TotalRuns", SUM(ball_by_ball_data[batter_runs])
    )

VAR CenturyCount = FILTER(BattetRuns, [TotalRuns] >= 50 && [TotalRuns] < 100)

RETURN COUNTROWS(CenturyCount)

=============================================================================================
Matches Lost = 
VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR Team1LostMatches =
    CALCULATE(COUNTROWS(ipl_matches_data),
        USERELATIONSHIP(ipl_matches_data[team1], teams_data[team_name]),
        ipl_matches_data[season] = SelectedSeason,
        ipl_matches_data[match_type] = "T20",
        NOT ISBLANK(ipl_matches_data[match_winner]),
        ipl_matches_data[match_winner] <> ipl_matches_data[team1]
    )
VAR Team2LostMatches =
    CALCULATE(COUNTROWS(ipl_matches_data),
        USERELATIONSHIP(ipl_matches_data[team2], teams_data[team_name]),
        ipl_matches_data[season] = SelectedSeason,
        ipl_matches_data[match_type] = "T20",
        NOT ISBLANK(ipl_matches_data[match_winner]),
        ipl_matches_data[match_winner] <> ipl_matches_data[team2]
    )
RETURN Team1LostMatches + Team2LostMatches
=============================================================================================
Matches Played = 
VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR Team1Matches =
    CALCULATE(COUNTROWS(ipl_matches_data),
        USERELATIONSHIP(ipl_matches_data[team1], teams_data[team_name]),
        ipl_matches_data[season] = SelectedSeason,
        ipl_matches_data[match_type] = "T20")

VAR Team2Matches =
    CALCULATE(COUNTROWS(ipl_matches_data),
        USERELATIONSHIP(ipl_matches_data[team2], teams_data[team_name]),
        ipl_matches_data[season] = SelectedSeason,
        ipl_matches_data[match_type] = "T20")

RETURN Team1Matches + Team2Matches
=============================================================================================
Matches Won = 

VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR CurrentTeam = SELECTEDVALUE(teams_data[team_name])

RETURN
CALCULATE(COUNTROWS(ipl_matches_data),
    ipl_matches_data[season] = SelectedSeason,
    ipl_matches_data[match_winner] = CurrentTeam,
    ipl_matches_data[match_type] = "T20")
=============================================================================================
No Result Matches = 
VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR Team1Matches =
    CALCULATE(COUNTROWS(ipl_matches_data),
        USERELATIONSHIP(ipl_matches_data[team1], teams_data[team_name]),
        ipl_matches_data[season] = SelectedSeason,
        ipl_matches_data[match_type] = "T20" , ipl_matches_data[result] = "no result")

VAR Team2Matches =
    CALCULATE(COUNTROWS(ipl_matches_data),
        USERELATIONSHIP(ipl_matches_data[team2], teams_data[team_name]),
        ipl_matches_data[season] = SelectedSeason,
        ipl_matches_data[match_type] = "T20" , ipl_matches_data[result] = "no result")

RETURN Team1Matches + Team2Matches
=============================================================================================
Orange Cap Holder = 

VAR SelectedSeason =
    SELECTEDVALUE ( ipl_matches_data[season] )

VAR SeasonDataOnly =
    FILTER ( ball_by_ball_data,
        RELATED ( ipl_matches_data[season] ) = SelectedSeason )

VAR RunSummary =
    SUMMARIZE ( SeasonDataOnly, ball_by_ball_data[batter],
        "Total Runs", SUM ( ball_by_ball_data[batter_runs] ) )

VAR MaxRuns =
    MAXX ( RunSummary, [Total Runs] )

VAR TopScorer =
    CALCULATETABLE ( VALUES ( ball_by_ball_data[batter] ),
        FILTER ( RunSummary, [Total Runs] = MaxRuns ) )

RETURN
    MAXX ( TopScorer, ball_by_ball_data[batter] )
=============================================================================================
Orange Cap Image = 

VAR SelectedSeason =
    SELECTEDVALUE ( ipl_matches_data[season] )

VAR SeasonDataOnly =
    FILTER ( ball_by_ball_data,
        RELATED ( ipl_matches_data[season] ) = SelectedSeason )

VAR RunSummary =
    SUMMARIZE ( SeasonDataOnly, ball_by_ball_data[batter],
        "Total Runs", SUM ( ball_by_ball_data[batter_runs] ) )

VAR MaxRuns =
    MAXX ( RunSummary, [Total Runs] )

VAR TopScorer =
    CALCULATETABLE ( VALUES ( ball_by_ball_data[batter] ),
        FILTER ( RunSummary, [Total Runs] = MaxRuns ) )

RETURN
   LOOKUPVALUE('players-data-updated'[player_image],'players-data-updated'[player_name],MAXX(TopScorer,ball_by_ball_data[batter]))

=============================================================================================
Orange Cap Runs = 
VAR SelectedSeason =
    SELECTEDVALUE ( ipl_matches_data[season] )
VAR SeasonDataOnly =
    FILTER ( ball_by_ball_data,
        RELATED ( ipl_matches_data[season] ) = SelectedSeason )
VAR RunSummary =
    SUMMARIZE ( SeasonDataOnly, ball_by_ball_data[batter],
        "Total Runs", SUM ( ball_by_ball_data[batter_runs] ) )
VAR MaxRuns = MAXX ( RunSummary, [Total Runs] )
RETURN MaxRuns
=============================================================================================
Orange Cap Team Name = 

VAR SelectedSeason =
    SELECTEDVALUE ( ipl_matches_data[season] )

VAR SeasonDataOnly =
    FILTER ( ball_by_ball_data,
        RELATED ( ipl_matches_data[season] ) = SelectedSeason )

VAR RunSummary =
    SUMMARIZE ( SeasonDataOnly, ball_by_ball_data[batter],
        "Total Runs", SUM ( ball_by_ball_data[batter_runs] ) )

VAR MaxRuns =
    MAXX ( RunSummary, [Total Runs] )

VAR TopScorer =
    CALCULATETABLE ( VALUES ( ball_by_ball_data[batter] ),
        FILTER ( RunSummary, [Total Runs] = MaxRuns ) )

VAR FullTeamName = CALCULATE(MAX(ball_by_ball_data[team_batting]),FILTER(SeasonDataOnly,ball_by_ball_data[batter] = MAXX(TopScorer,ball_by_ball_data[batter])))   RETURN FullTeamName
=============================================================================================
PurpleCapHolder = 
VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

-- Filter: wickets in selected season, exclude non-bowler dismissals
VAR SeasonWickets =
    FILTER(
        ball_by_ball_data,
        RELATED(ipl_matches_data[season]) = SelectedSeason &&
        ball_by_ball_data[is_wicket] = TRUE() &&
        NOT ball_by_ball_data[wicket_kind] IN { "run out", "retired hurt", "obstructing the field" }
    )

-- Summarize bowler and count wickets
VAR WicketSummary =
    SUMMARIZE(
        SeasonWickets,
        ball_by_ball_data[bowler],
        "WicketCount", COUNTROWS(
            FILTER(SeasonWickets, ball_by_ball_data[bowler] = EARLIER(ball_by_ball_data[bowler]))
        )
    )

-- Find highest wicket count
VAR MaxWickets = MAXX(WicketSummary, [WicketCount])

-- Get the bowler(s) with that wicket count
VAR TopBowler =
    CALCULATETABLE(
        VALUES(ball_by_ball_data[bowler]),
        FILTER(WicketSummary, [WicketCount] = MaxWickets)
    )

-- Return the name (if multiple, it picks one alphabetically)
RETURN
    MAXX(TopBowler, ball_by_ball_data[bowler])

=============================================================================================
PurpleCapImage = 
VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

-- Step 1: Filter valid bowler wickets for the selected season
VAR SeasonWickets =
    FILTER(
        ball_by_ball_data,
        RELATED(ipl_matches_data[season]) = SelectedSeason &&
        ball_by_ball_data[is_wicket] = TRUE() &&
        NOT ball_by_ball_data[wicket_kind] IN {
            "run out",
            "retired hurt",
            "retired out",
            "obstructing the field",
            "timed out"
        }
    )

-- Step 2: Summarize bowler-wise wicket count
VAR WicketSummary =
    SUMMARIZE(
        SeasonWickets,
        ball_by_ball_data[bowler],
        "WicketCount", COUNTROWS(
            FILTER(
                SeasonWickets,
                ball_by_ball_data[bowler] = EARLIER(ball_by_ball_data[bowler])
            )
        )
    )

-- Step 3: Get the max wicket count
VAR MaxWickets = MAXX(WicketSummary, [WicketCount])

-- Step 4: Get the bowler with max wickets
VAR TopBowler =
    CALCULATETABLE(
        VALUES(ball_by_ball_data[bowler]),
        FILTER(WicketSummary, [WicketCount] = MaxWickets)
    )

-- Step 5: Return image using LOOKUP from player table
RETURN
    LOOKUPVALUE(
        'players-data-updated'[player_image],
        'players-data-updated'[Player_name], MAXX(TopBowler, ball_by_ball_data[bowler])
    )

=============================================================================================
PurpleCapTeam = 
VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

-- Step 1: Filter valid wickets for the selected season
VAR SeasonWickets =
    FILTER(
        ball_by_ball_data,
        RELATED(ipl_matches_data[season]) = SelectedSeason &&
        ball_by_ball_data[is_wicket] = TRUE() &&
        NOT ball_by_ball_data[wicket_kind] IN {
            "run out",
            "retired hurt",
            "retired out",
            "obstructing the field",
            "timed out"
        }
    )

-- Step 2: Summarize bowler and team, count wickets per bowler
VAR WicketSummary =
    ADDCOLUMNS(
        SUMMARIZE(
            SeasonWickets,
            ball_by_ball_data[bowler],
            ball_by_ball_data[team_bowling]
        ),
        "WicketCount",
        COUNTROWS(
            FILTER(
                SeasonWickets,
                ball_by_ball_data[bowler] = EARLIER(ball_by_ball_data[bowler])
            )
        )
    )

-- Step 3: Max Wickets
VAR MaxWickets = MAXX(WicketSummary, [WicketCount])

-- Step 4: Return full team name of the top wicket-taker
RETURN
    MAXX(
        FILTER(WicketSummary, [WicketCount] = MaxWickets),
        ball_by_ball_data[team_bowling]
    )

=============================================================================================
PurpleCapWicketCount = 
VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

-- Step 1: Filter valid bowler wickets in selected season
VAR SeasonWickets =
    FILTER(
        ball_by_ball_data,
        RELATED(ipl_matches_data[season]) = SelectedSeason &&
        ball_by_ball_data[is_wicket] = TRUE() &&
        NOT ball_by_ball_data[wicket_kind] IN {
            "run out",
            "retired hurt",
            "retired out",
            "obstructing the field",
            "timed out"
        }
    )

-- Step 2: Summarize wickets per bowler
VAR WicketSummary =
    SUMMARIZE(
        SeasonWickets,
        ball_by_ball_data[bowler],
        "WicketCount", COUNTROWS(
            FILTER(
                SeasonWickets,
                ball_by_ball_data[bowler] = EARLIER(ball_by_ball_data[bowler])
            )
        )
    )

-- Step 3: Get the highest wicket count
VAR MaxWickets = MAXX(WicketSummary, [WicketCount])

RETURN
    MaxWickets

=============================================================================================
Runner UP = 
VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])     --2025

VAR FinalMatchDate = CALCULATE(MAX(ipl_matches_data[match_date]),
                               ipl_matches_data[season] = SelectedSeason)   --Max Date = 3 June

VAR FinalMatchWinner = CALCULATE(MAX(ipl_matches_data[match_winner]),   -- Winner Team
                                ipl_matches_data[season] = SelectedSeason,
                                ipl_matches_data[match_date] = FinalMatchDate)

VAR Team1 = CALCULATE(MAX(ipl_matches_data[team1]),
                                ipl_matches_data[season] = SelectedSeason,
                                ipl_matches_data[match_date] = FinalMatchDate)

VAR Team2 = CALCULATE(MAX(ipl_matches_data[team2]),
                                ipl_matches_data[season] = SelectedSeason,
                                ipl_matches_data[match_date] = FinalMatchDate)

RETURN  IF(FinalMatchWinner=Team1,Team2,Team1)
=============================================================================================
Runner UP Team Logo = 
VAR SelectedSeason =
    SELECTEDVALUE ( ipl_matches_data[season] ) --2025

VAR FinalMatchDate =
    CALCULATE (
        MAX ( ipl_matches_data[match_date] ),
        ipl_matches_data[season] = SelectedSeason
    ) --Max Date = 3 June

VAR FinalMatchWinner =
    CALCULATE (
        MAX ( ipl_matches_data[match_winner] ), -- Winner Team
        ipl_matches_data[season] = SelectedSeason,
        ipl_matches_data[match_date] = FinalMatchDate
    )

VAR Team1 =
    CALCULATE (
        MAX ( ipl_matches_data[team1] ),
        ipl_matches_data[season] = SelectedSeason,
        ipl_matches_data[match_date] = FinalMatchDate
    )

VAR Team2 =
    CALCULATE (
        MAX ( ipl_matches_data[team2] ),
        ipl_matches_data[season] = SelectedSeason,
        ipl_matches_data[match_date] = FinalMatchDate
    )

-- Determine Runner-Up (team that is not the winner)
VAR RunnerUp =
    IF ( Team1 = FinalMatchWinner, Team2, Team1 )

RETURN
    LOOKUPVALUE (
        teams_data[image_url],
        teams_data[team_name], RunnerUp
    )

=============================================================================================
Season Winner = 
VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])     --2025

VAR FinalMatchDate = CALCULATE(MAX(ipl_matches_data[match_date]),
                               ipl_matches_data[season] = SelectedSeason)   --Max Date = 3 June

VAR FinalMatchWinner = CALCULATE(MAX(ipl_matches_data[match_winner]),   -- Winner Team
                                ipl_matches_data[season] = SelectedSeason,
                                ipl_matches_data[match_date] = FinalMatchDate)

RETURN FinalMatchWinner
=============================================================================================
Season winner Logo = 

VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])     --2025

VAR FinalMatchDate = CALCULATE(MAX(ipl_matches_data[match_date]),
                               ipl_matches_data[season] = SelectedSeason)   --Max Date = 3 June

VAR FinalMatchWinner = CALCULATE(MAX(ipl_matches_data[match_winner]),   -- Winner Team
                                ipl_matches_data[season] = SelectedSeason,
                                ipl_matches_data[match_date] = FinalMatchDate)

RETURN
LOOKUPVALUE(
    teams_data[image_url],
    teams_data[team_name], FinalMatchWinner
)

=============================================================================================
Tie Matches = 
VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR Team1Matches =
    CALCULATE(COUNTROWS(ipl_matches_data),
        USERELATIONSHIP(ipl_matches_data[team1], teams_data[team_name]),
        ipl_matches_data[season] = SelectedSeason,
        ipl_matches_data[match_type] = "T20", ipl_matches_data[result] = "tie")

VAR Team2Matches =
    CALCULATE(COUNTROWS(ipl_matches_data),
        USERELATIONSHIP(ipl_matches_data[team2], teams_data[team_name]),
        ipl_matches_data[season] = SelectedSeason,
        ipl_matches_data[match_type] = "T20", ipl_matches_data[result] = "tie")

RETURN Team1Matches + Team2Matches
=============================================================================================
Top Fours Count = 

VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR SeasonFours = FILTER(ball_by_ball_data, RELATED(ipl_matches_data[season]) = SelectedSeason &&
                            ball_by_ball_data[batter_runs] = 4 )

VAR FourSummary = SUMMARIZE(SeasonFours, ball_by_ball_data[batter], "FoursCount",
                            COUNTROWS(FILTER(SeasonFours, ball_by_ball_data[batter] = EARLIER(ball_by_ball_data[batter]))))

VAR MaxFours = MAXX(FourSummary, [FoursCount])
RETURN MaxFours
=============================================================================================
Top Fours Player Image = 

VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR SeasonFours = FILTER(ball_by_ball_data, RELATED(ipl_matches_data[season]) = SelectedSeason &&
                            ball_by_ball_data[batter_runs] = 4 )

VAR FourSummary = SUMMARIZE(SeasonFours, ball_by_ball_data[batter], "FoursCount",
                            COUNTROWS(FILTER(SeasonFours, ball_by_ball_data[batter] = EARLIER(ball_by_ball_data[batter]))))

VAR MaxFours = MAXX(FourSummary, [FoursCount])

VAR TopFoursPlayer = CALCULATETABLE(VALUES(ball_by_ball_data[batter]),
                                    FILTER(FourSummary, [FoursCount] = MaxFours))

RETURN
    LOOKUPVALUE(
        'players-data-updated'[player_image],
        'players-data-updated'[player_name], MAXX(TopFoursPlayer, ball_by_ball_data[batter])
    )
=============================================================================================
Top Fours Player Name = 

VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR SeasonFours = FILTER(ball_by_ball_data, RELATED(ipl_matches_data[season]) = SelectedSeason &&
                            ball_by_ball_data[batter_runs] = 4 )

VAR FourSummary = SUMMARIZE(SeasonFours, ball_by_ball_data[batter], "FoursCount",
                            COUNTROWS(FILTER(SeasonFours, ball_by_ball_data[batter] = EARLIER(ball_by_ball_data[batter]))))

VAR MaxFours = MAXX(FourSummary, [FoursCount])

VAR TopFoursPlayer = CALCULATETABLE(VALUES(ball_by_ball_data[batter]),
                                    FILTER(FourSummary, [FoursCount] = MaxFours))

RETURN MAXX(TopFoursPlayer, ball_by_ball_data[batter])

=============================================================================================
Top Six Count = 

VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR SeasonFours = FILTER(ball_by_ball_data, RELATED(ipl_matches_data[season]) = SelectedSeason &&
                            ball_by_ball_data[batter_runs] = 6 )

VAR FourSummary = SUMMARIZE(SeasonFours, ball_by_ball_data[batter], "FoursCount",
                            COUNTROWS(FILTER(SeasonFours, ball_by_ball_data[batter] = EARLIER(ball_by_ball_data[batter]))))

VAR MaxFours = MAXX(FourSummary, [FoursCount])
RETURN MaxFours
=============================================================================================
Top Six Player Image = 

VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR SeasonFours = FILTER(ball_by_ball_data, RELATED(ipl_matches_data[season]) = SelectedSeason &&
                            ball_by_ball_data[batter_runs] = 6 )

VAR FourSummary = SUMMARIZE(SeasonFours, ball_by_ball_data[batter], "FoursCount",
                            COUNTROWS(FILTER(SeasonFours, ball_by_ball_data[batter] = EARLIER(ball_by_ball_data[batter]))))

VAR MaxFours = MAXX(FourSummary, [FoursCount])

VAR TopFoursPlayer = CALCULATETABLE(VALUES(ball_by_ball_data[batter]),
                                    FILTER(FourSummary, [FoursCount] = MaxFours))

RETURN
    LOOKUPVALUE(
        'players-data-updated'[player_image],
        'players-data-updated'[player_name], MAXX(TopFoursPlayer, ball_by_ball_data[batter])
    )
=============================================================================================
Top Six Player Name = 

VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR SeasonFours = FILTER(ball_by_ball_data, RELATED(ipl_matches_data[season]) = SelectedSeason &&
                            ball_by_ball_data[batter_runs] = 6 )

VAR FourSummary = SUMMARIZE(SeasonFours, ball_by_ball_data[batter], "FoursCount",
                            COUNTROWS(FILTER(SeasonFours, ball_by_ball_data[batter] = EARLIER(ball_by_ball_data[batter]))))

VAR MaxFours = MAXX(FourSummary, [FoursCount])

VAR TopFoursPlayer = CALCULATETABLE(VALUES(ball_by_ball_data[batter]),
                                    FILTER(FourSummary, [FoursCount] = MaxFours))

RETURN MAXX(TopFoursPlayer, ball_by_ball_data[batter])

=============================================================================================
Top Six Player Team Name = 

VAR SelectedSeason = SELECTEDVALUE(ipl_matches_data[season])

VAR SeasonFours = FILTER(ball_by_ball_data, RELATED(ipl_matches_data[season]) = SelectedSeason &&
                            ball_by_ball_data[batter_runs] = 6 )

VAR FourSummary = SUMMARIZE(SeasonFours, ball_by_ball_data[batter], "FoursCount",
                            COUNTROWS(FILTER(SeasonFours, ball_by_ball_data[batter] = EARLIER(ball_by_ball_data[batter]))))

VAR MaxFours = MAXX(FourSummary, [FoursCount])

VAR TopFoursPlayer = CALCULATETABLE(VALUES(ball_by_ball_data[batter]),
                                    FILTER(FourSummary, [FoursCount] = MaxFours))

VAR BatterTeam =
    CALCULATE(MAX(ball_by_ball_data[team_batting]),
        FILTER(SeasonFours, ball_by_ball_data[batter] = MAXX(TopFoursPlayer, ball_by_ball_data[batter])))

RETURN BatterTeam
=============================================================================================
Total 4's = 

CALCULATE (
    COUNTROWS ( ball_by_ball_data ),
    ball_by_ball_data[batter_runs] = 4,
    KEEPFILTERS ( VALUES ( ipl_matches_data[season] ) )
)
=============================================================================================
Total 6's = 

CALCULATE (
    COUNTROWS ( ball_by_ball_data ),
    ball_by_ball_data[batter_runs] = 6,
    KEEPFILTERS ( VALUES ( ipl_matches_data[season] ) )
)
=============================================================================================
Total Matches = (CALCULATE(DISTINCTCOUNT(ipl_matches_data[match_id])))
=============================================================================================
Total Point = VAR Win = [Matches Won] VAR NR = [No Result Matches] RETURN (Win*2) + NR
=============================================================================================
Total Teams = (CALCULATE(DISTINCTCOUNT(ipl_matches_data[team1])))  
=============================================================================================

=============================================================================================

=============================================================================================

=============================================================================================

=============================================================================================
