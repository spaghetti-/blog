---
title: Setting up an ecobee 3 for a heat only system without a native C wire
date: 2022-07-30 17:46:17
tags:
  - home-automation
  - homekit
---

I use an [Ecobee 3
Lite](https://www.ecobee.com/en-us/smart-thermostats/smart-wifi-thermostat/) as
my home thermostat. I have a heat only system (a furnace in the basement) and I
only had 3 wires run upto my house (R, W, G). Getting an electrician or HVAC
tech to run a wire up to the house would cost >$150 (at minimum) so I decided
against that.

Before the setup, some introductory material as to what the wires do:

| Wire  | Purpose                               |
|-------|---------------------------------------|
| Rh    | Hot (heating side transformer)        |
| Rc    | Hot (cooling side transformer)        |
| C     | Common tap (heating side transformer) |
| W(1?) | Stage 1 heating switch                |
| W2    | Stage 1 heating switch                |
| G     | Fan switch                            |
| Y     | AC                                    |

I call them switches because that's how we can turn on heat/the fan/AC. If you
short the Rh wire to W, you'll find that your furnace turns on, and similarly
for the fan control. They're simply relays at the furnace control board. For my
system, which is a single stage heat only system, I have R, G and W wires.

The Ecobee 3 and most other smart thermostats require the C wire so that they
can draw power by completing the R->C circuit. It turns out that they prefer to
complete the Rc->C circuit instead of Rh, which is lucky because you can get a
random 24VAC transformer off Amazon and provide power to the thermostat this
way.

Here's how you hook it up:

```

            ┌────────────┐
            │            │
            │            │
            │    24VAC   │
            │  External  │
            │   Power    │
            │            │
            └──┬─────┬───┘
               │     │
     ┌─────────┘     └───────────────────┐
     │                                   │
     │  ┌───────────────────────┐        │
     │  │                       │      ┌─┼──┐  R (to furnace)
     └──┤Rc                   Rh├──────┘ │  └────
        │                       │        │
        │                       │        │
     ───┤G     Ecobee3 Lite   C ├────────┘
        │      Baseplate        │
        │                       │              W (to furnace)
     ───┤Y1                   W1├─────────────────
        │                       │
        │                       │
     ───┤Y2                   W2├───
        │                       │
        │                       │
     ───┤O/B                 PEK├───
        └───────────────────────┘

```

I intentionally left out the G wire here because connecting it upto G do
anything yet. When the thermostat wants to turn on the fan, its internal relay
will close the circuit from Rc -> G -> C but the furnace requires the circuit Rh
-> G -> C to be closed. To get around this you can get any 24VAC relay from
Digi-key or mouser (coil voltage 24VAC, SPST, NO NC 24-240V or anything that you
can find) for $3. Then we can do this instead:

```
             ┌────────────┐
             │            │
             │            │
             │    24VAC   │
             │  External  │
             │   Power    │
             │            │
             └──┬─────┬───┘
                │     │
      ┌─────────┘     └───────────────────┐
      │                                   │
      │  ┌───────────────────────┐        │
      │  │                       │      ┌─┼──┐  R (to furnace)
      └──┤Rc                   Rh├──────┘ │  ├────
         │                       │        │  │
         │                       │        │  │
┌────────┤G     Ecobee3 Lite   C ├────────┤  └┐
│        │      Baseplate        │        └─┐ │
│        │                       │          │ │ W (to furnace)
│     ───┤Y1                   W1├──────────┼─┼────
│        │                       │          │ │
│        │                       │        ┌─┘ │
│     ───┤Y2                   W2├───     │   │
│        │                       │        │   │
│        │                       │        │   │
│     ───┤O/B                 PEK├───     │   │
│        └───────────────────────┘        │   │
│                                         │   │
│                                         │   │
│                                         │   │
│                                         │   │
│    (G)       ┌───────────────┐ (Rh)     │   │
└──────────────┤ Coil     NC   ├──────────┼───┘
               │     Relay     │          │
               │               │          │          G (to furnance)
               │ Coil     NO   ├──────────┼───────────────
               └───┬───────────┘          │
                   │                      │
                   └──────────────────────┘
                     (C from external power)
```

Basically, hookup G from the ecobee and the C side of the external power supply
to the coil of the relay. When the thermostats internal relay latches, power
will flow from Rc->G->Relay->C. Hookup the G wire from the furnace and the Rh
wire to NC and NO contacts on the relay. I recommend heat shrinking all contacts
after soldering for safety.

Now you can go into the thermostats settings and test each control one by one
and both your heat and fan should turn on independently.

Being able to control your thermostat through Homekit besides, the cost of
setting this up:

| Item         | Cost               |
|--------------|--------------------|
| Thermostat   | $146.63            |
| Power supply | $19.43             |
| Relay        | $9 (incl shipping) |
| PG&E Rebate  | -$120              |
| Total        | $46.06             |

and some wires so about 50 bucks in all. I could have cut down the cost by
choosing a cheaper power supply and by not paying Digikey $6 to ship a $3
component, but I wasn't about to wait 2-3 weeks to get stuff off Aliexpress and
the like.
