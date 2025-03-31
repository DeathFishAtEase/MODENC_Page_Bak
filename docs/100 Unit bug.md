100 Unit bug
---
- Version  16:16, 29 March 2025‎ 4SG

---
TS, FS, RA2 and YR are coded in a way that limits the number of total buildable VehicleTypes, InfantryTypes, and AircraftTypes as well, to 100 units each. If you have a buildable object on a place after 100, the AI will think that is the only unit buildable and create it over and over. It will continue building until there is no money left or you stop it. This bug is fixed in RockPatch and Ares for Yuri's Revenge. For Tiberian Sun, [TS-Patches](https://github.com/CnCNet/ts-patches) fixes it which is used in [TS Client](https://www.moddb.com/mods/tiberian-sun-client). For another solution that partly avoids the bug see below for details.

### Avoiding the 100 unit bug (by DCoder)

---
Look at your InfantryTypes and VehicleTypes. How many entries do they have? If there's more than 101 buildable units (those without TechLevel=-1) and they're not all in the first 101 lines of the list, the AI will go nuts - it'll build the last unit in the list non-stop, and won't attack. To avoid it, you'll need to rearrange these lists, put buildable units first and the civilian and dummy units in the very end. I personally sorted it like this:

```ini
[VehicleTypes]
1=AEGIS
2=AMCV
3=BFRT
4=CARRIER
5=CMIN
6=DEST
7=DLPH
8=FV
9=LCRF
10=MGTK
11=MTNK
12=ROBO
13=SHAD
14=SREF
15=TNKD

16=APOC
17=DISK
18=DRED
19=DRON
20=DTRUCK
21=HARV
22=HYD
23=HTK
24=HTNK
25=SAPC
26=SCHP
27=SMCV
28=SQD
29=SUB
30=TTNK
31=V3
32=ZEP

33=BSUB
34=CAOS
35=YHVR
36=YTNK
37=LTNK
38=MIND
39=PCV
40=SMIN
41=TELE

;notice the gap here
102=AMBU
103=BCAB
104=BUS
105=CAR
106=CBLC
107=CDEST
108=CIVP
109=CMON
110=CONA
111=COP
112=CRUISE
113=DDBX
114=DeathDummy
115=DOLY
116=EUROC
117=FTRK
118=HORV
119=HOWI
120=YCAB
121=YDUM
122=JEEP
123=LIMO
124=PICK
125=PROPA
126=PTRUCK
127=SCHD
128=STANG
129=SUVB
130=SUVW
131=TAXI
132=TRUCKA
133=TRUCKB
134=TUG
135=UTNK
136=VLAD
137=WINI
138=XCOMET
```

Note the allied stuff is first, next soviet and yuri, then there's a gap between 41 and 101 before civilian units are listed. The numbers don't really matter, only the order of the list does. Now I see that I have 41 buildable units, and can still make 60 extra ones without screwing up. If rules(md).ini has more than 100 units and new units are added in a map, the new units from map are appended to the rules list, in such cases this method fails to avoid the bug.

### Note about the list enumeration

---
Note, the numbers on the left can really be anything. The engine doesn't pay any attention to it, it discards everything to the left of the = sign, and uses its own enumeration, starting at 1 (or possibly zero, but the numeration doesn't make the slightest difference in this case). You can even use stuff like

```ini
ALLIEDMCV=AMCV
H4RV3S73R=HARV
7^3-4^2=CMIN
```

and similar if you want, it makes no difference. But, obviously, using a proper 1-based ordinal enumeration gives you ideas of how many "slots" you stil have available for buildable units.

### 100 Unit Bug and Online Play: The 100 Units Bug causes Reconnection Errors! (DoctorEvil)
After playtesting an early prototype of a heavily modded survival map of mine (DoctorEvil's Second Pacific Survival to be exact) I have noticed that having a map with the 100 unit bug causes reconnection errors to occur after a while (during an online match via cncnet). I'm not sure on what exactly causes the recon error, but fixing the 100 unit bug also prevents random errors from happening.

The way I fixed the 100 unit bug on my second pacific survival is by overwriting the existing civilian units instead of creating entirely new units. For example instead of...

[STEALTHFIGHTER] ROT=3 Ammo=2 Cost=1200 Dock=GAAIRC,AMRADR Name=Stealth Fighter Armor=light Image=BEAG Primary=Maverick2 (...)

Overwrite existing units like this:

[COP] ROT=3 Ammo=2 Cost=1200 Dock=GAAIRC,AMRADR Name=Stealth Fighter Armor=light Image=BEAG Primary=Maverick2 (...)

Just make sure to check the INI flags for each civilian unit. All of them have TechLevel=11 or -1, ThreatPosed=0, AmbientSound=xxx, and so on. Keep in mind that fixing it this way also causes some cameos to not show the thing you declared in Image=xxx, due to the engine reading certain units from Art.ini instead.

### Note

---
- Since the method I suggested was removed, because I didn't tell you how to do it all (AI doesn't have enough production cost, why don't you create a money source that players can't create (really stupid)).

And later I found a better method, but I didn't tell you for personal reasons, but today I'm going to reveal it
1. You add the tag
[RequiresStolenAlliedTech=yes](https://modenc.renegadeprojects.com/RequiresStolenAlliedTech)
[RequiresStolenSovietTech=yes](https://modenc.renegadeprojects.com/RequiresStolenSovietTech)
[RequiresStolenThirdTech=yes](https://modenc.renegadeprojects.com/RequiresStolenThirdTech)
to the unit you want to create alone, but don't want AI to create with it
2. Add the PrerequisiteOverride tag to that unit, and specify only buildings that human players can build (I won't tell you what tags are used to define this method, but you wouldn't be more stupid than I thought).
Even though the spy didn't enter the lab of all 3 factions, it definitely works
