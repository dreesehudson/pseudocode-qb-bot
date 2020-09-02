<!-- START Program 

Objects:
    headCoachBot: provide play (routes) to each receiver
    ball
    lineOfScrimmage
    field: array of positions
    qbBot: hasBall(), position()
    receiverX: hasBall(), position(), distanceToDefender[dX, dY, dZ, dRB, dTE]
    receiverY: hasBall(), position(), distanceToDefender[dX, dY, dZ, dRB, dTE]
    receiverZ: hasBall(), position(), distanceToDefender[dX, dY, dZ, dRB, dTE]
    receiverRB: hasBall(), position(), distanceToDefender[dX, dY, dZ, dRB, dTE]
    receiverTE: hasBall(), position(), distanceToDefender[dX, dY, dZ, dRB, dTE]
    defenderX: hasBall(), position()
    defenderY: hasBall(), position()
    defenderZ: hasBall(), position()
    defenderRB: hasBall(), position()
    defenderTE: hasBall(), position()
    rusherA: position()
    rusherB: position()
    rusherC: position()
    rusherD: position()


Functions:
    distance()
    throwBall()
    readDefense()
    run()
    huddle()
    formation()
    getUp()

Rough Outline:

    1. Huddle
    2. Receive Play from headCoachBot
    3. Communicate Play to Team
    4. Get in Formation
    5. Analyze Defense (Inital distanceToDefender() calcs)
    6. Snap Ball t=0.
    7. Step Back, Read Defense, IF Throw, IF rush CHECK rb, else run t=1
    8. Step Back, Read Defense, IF Throw, IF rush CHECK rb, else run t=2
    9. Step Back, Read Defense, IF Throw, IF rush CHECK rb, else run t=3
    10. Step Back, Read Defense, IF Throw, IF rush CHECK rb, else run t=4

    11. IF t=5 run

    





-->