## Quarterback Bot


### MoSCoW
#### Must Have:  
- communicate the play to receivers
- execute the play
- qbBot make decision to pass or run based on defensive player positions
- throw the ball

#### Should Have:  

#### Could Have:  
- running plays
- additional receivers (running back and tight end or 4th wide receiver)
- consideration for gaining first down, current iteration always just looks for open receiver

#### Won't Have:  
- starting ball somewhere other than midpoint between the sidelines
- account for additional defenders (safeties and linebackers)
- ability to track first downs gained, i.e. 
- team will not punt, will just play 4 downs to score irregardless of drive starting position.
- turnovers 

```
START Program 

<!-- create all objects necessary for program -->
INIT()

<!-- assign initial values to variables -->
SET down = 1
    ball = position[25, 20]
    lineOfScrimmage = position [25, 20]
    qbBot = hasBall(false)
            position([null])
            isRushed(false)
    receiver[X, Y, Z] = hasBall[false, false, false]
                        position{[null], [null], [null]}
    opponent[X, Y, Z] = position{[null], [null], [null]}
    rusher[A, B, C, D] = position{[null], [null], [null]}


WHILE (down <= 4 OR touchdown(false) ) {
  
  <!-- receive play call from head coach, this tells the receivers where to be at t=0, t=1, t=2 etc.   -->
    huddleAt(lineOfScrimmage)

  <!-- MAJOR HANDWAVING HERE. Opponents will matchup with their receiver, and try and follow their matchup with varying
  speed statistics. This is what will allow distance to vary between receivers to make passing judgements. -->
    IMPORT defensivePlayerAI.behavior

  <!-- move players into pre-snap position -->
    SET receiver[X, Y, Z].position(t_0)
        opponent[X, Y, Z].position(t_0)
        rusher[A, B, C, D].position(t_0)
        qbBot.position(t_0) = lineOfScrimmage.position

  <!-- start WHILE loop function to start passing play -->
    executePlay()

  <!-- executePlay() has two output scenarios that EXIT the loop so there are two corresponding code blocks below. -->
  <!-- 1) throwBallTo(openReceiver) EXIT -->
  <!-- if pass completed, execute run() which loops until touchdown is scored or player is tackled. TOUCHDOWN will cause 
   exit of overall loop. -->
    IF passCompleted(){
        run(receiver)
        }
  <!-- if pass not completed, increment the down and rehuddle at position of Line of Scrimmage. THIS CAN CAUSE down > 4 
  which would exit the overall loop. -->
    ELSE {
        down++
        huddleAt(lineOfScrimmage)
    }

  <!-- 2) isRushed(true) OR t>5 EXIT -->
  <!-- if rushers are closing in on the qbBot OR if more than 5 seconds in the pocket, qbBot will scramble. -->
    IF (qbBot.isRushed() OR t>=5){
        run(qbBot)
    }
}

<!-- both segments have terminating conditions (scoring TD or being tackled) that will exit the overall WHILE loop starting on line 42. -->

END SCRIPT
```

### Objects:
    headCoachBot: 
        database that generates pass routes (position for each receiver at t=0-5) to each receiver
    
    defensivePlayerAI:
        database that generates defensive player behavior
    
    ball:
        position()
    
    lineOfScrimmage: start position of the ball (assume drive always starting halfway across, 20yd line).
        position()
    
    qbBot: 
        hasBall()
        position()
        isRushed()
    
    receiver[X, Y, Z]: 
        hasBall() 
        position()

    opponent[X, Y, Z]: 
        hasBall()
        position()

    rusher[A, B, C, D]: 
        position()


### Variables:
    down: var to determine if, at the end of a play sequence, the program continues to run or if break out occurs to END PROGRAM

    time (t): incrementor for while loop during executePlay()
    
    field: array of positions H (0-50 yds) x V (0-100 yds)
    


### Functions:
```
<!-- create all objects necessary for program -->
    INIT()
        CREATE headCoachBot
        CREATE defensivePlayerAI
        CREATE ball
        CREATE lineOfScrimmage
        CREATE field
        CREATE down
        CREATE qbBot
        CREATE receiver[X, Y, Z]
        CREATE opponent[X, Y, Z]
        CREATE rusher[A, B, C, D]

<!-- position all players behind line of scrimmage for next play and import play from coach. -->
    huddleAt()
        setPositionOfTeam (25, LOS-3)
        IMPORT headCoachBot.passingRoutes()

<!-- primary loop which uses a decision tree with nested functions (see isOpen() below) to determine when a receiver is open, 
  and subsequently check if rushers are close and decide to scramble or not. End of Loop increments t. -->
    executePlay(){
        WHILE (t<5 || qbBot.isRushed = FALSE){
        qbBot.stepBack()
        IF(isOpen(receiver[X, Y, Z], opponent[X, Y, Z])){
            throwBallTo(sortedDist_0)
            BREAK
        }
        IMPORT rusher[A_t, B_t, C_t, D_t]
        isRushed([qbBot.position, rusher[A_t, B_t, C_t, D_t]])
            BREAK
        t++
        }   
    }

<!-- object retreats 1 yard -->
    stepBack(){
        self.position(V-1)
    }

<!-- sorts resulting array from dist() and decides if the most open receiver is open enough to throw to. -->
    isOpen(receiver[X, Y, Z], opponent[X, Y, Z]){
        dist(receiver[X, Y, Z], opponent[X, Y, Z]) -> [dX, dY, dZ]
        SORT[dX, dY, dZ] -> sortedDist[]
        IF(sortedDist_0[].value > 2) {
            sortedDist_0.isOpen = TRUE
        }
        ELSE {
            receiver[X, Y, Z].isOpen = FALSE
        }
    }
 
<!-- calculates distance between the receiver positions and their matched up opponent positions. -->
    dist(receiver[X, Y, Z], opponent[X, Y, Z])
        d(N) = sqrt((rH-dH)^2+(rV-dV)^2)
        output -> [dX, dY, dZ]
    }


<!-- changes possession of the ball from QB to openReceiver (possibly)         -->
    throwBallTo(openReceiver){
        qb hasBall(FALSE);
        
        64.4% chance * wr hasBall(TRUE);        //some hand-waving here
        ELSE{
            openReceiver.hasBall(FALSE);
            passComplete() = FALSE;    
        } 
    }


<!-- checks distance between array of rushers and qbBot -->
    isRushed({qbBot.position, rushers[A, B, C, D]}){
        dist(qbBot.position, rushers[A, B, C, D]) -> [dA, dB, dC, dD]
        sortedArray = SORT[dA, dB, dC, dD]
        IF sortedArray_0 < 2 qbBot.isRushed = TRUE
    }    

       
<!-- move forward, check for touchdown, if near opponent, juke, if opp tackled runner, INCREMENT down, 
  if 4th down or turnover go to bench END PROGRAM else go to huddle. -->
    run(player){
        while player.isTackled(false){
            moveForward(1)
            IF player.v.position = 100 {
                celebrate()
                END PROGRAM
            }
            ELSE IF(oppNear()){
                juke()
                isTackled(){
                    lineOfScrimmage = [25, lineOfScrimmage.plusNumberOfRunLoopsExecuted]
                    down++
                    IF(down > 4){
                        sitOnBench()
                        END PROGRAM
                    }
                }
            }
        }
    }

<!-- advances position of player X yard toward end zone -->
    moveForward(X)

<!-- checks to see if opponent is within range to attempt tackle -->
    oppNear()



```
