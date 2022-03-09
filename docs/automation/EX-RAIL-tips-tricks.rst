************************
EX-RAIL Tips and Tricks
************************

EX-RAIL is Awesome
====================

By now you've no doubt come across the brand new EX-RAIL automation capability in all its wonderous glory and are no doubt wondering how you can effectively put it to use in your layout.

Well, wonder no more as we're here to help you understand how best to take advantage of this in ways you've never dreamt of!

On this page you'll find some basic concepts to get your brain "into the swing" of how EX-RAIL works as well as some really handy tips and tricks to get your head around the possibilities.

Tips
=====

Read the documentation
-----------------------

This one's pretty obvious, but it's read the documentation! Most importantly, read the :doc:`EX-RAIL-intro` to get familiar with the very basics and the examples provided, and pay attention to the syntax.

Pay attention to the author's note also to understand why DCC++ EX was created in the first place, and in particular his comment on "reversing the usual assumptions", which takes us to our first real tip...

Think from the Engineer's perspective
--------------------------------------

A common topic discussed in the DCC++ EX `Discord server <https://discord.gg/PuPnNMp8Qf>`_ is how to deal with some of the concepts that need to be understood in order to define successful automation sequences.

The one key concept arising out of these conversations is to think from the Engineer's perspective, ie. think from the view of the person driving the train, not from the overall layout control perspective.

This in my mind is akin to DC vs. DCC control to a certain extent. In DC, you're effectively driving or controlling the track, not the locomotives directly, whereas in DCC you are controlling the locomotives directly.

As an example of putting this concept into practise, consider the RESERVE() and FREE() functions that control when a locomotive can enter a virtual block.



Tricks
=======

Converting Linear DCC address to address/subaddress
----------------------------------------------------

In EX-RAIL, DCC controlled turnouts are defined using the address and subaddress rather than their simple, linear address.

The simplest explanation is that this addressing method allows for a DCC accessory decoder to have up to four outputs associated with it, with the address component effectively representing the board address, and the subaddress representing one of the four outputs.

The magical formulas required are:

Address = (Linear Address + 3) / 4, rounded down to a whole number.
Subaddress = (Linear Address + 3) - Address * 4.

A DCC linear address of 10 will yield:

Address = 3: (10 + 3) / 4 = 3.25, rounds down to 3.
Subaddress = 1: (10 + 3) - 3 * 4 = 1.

A DCC linear address of 1979 will yield:

Address = 495: (1979 + 3) / 4 = 495.5, rounds down to 495.
Subaddress = 2: (1979 + 3) - 495 * 4.

Define your own commands
-------------------------

In EX-RAIL, you're not limited to the defined EX-RAIL commands, and you can make your own commands by combining multiple EX-RAIL commands into a single macro.

These are represented in myAutomation.h as a line beginning with "#define", and the sky is the limit on what you can do with these.

As an example, here is a macro written to provide the brief pulse required by a single coil Rokuhan turnout when a CLOSE or THROW command is sent to the CommandStation, and as a bonus, this macro also defines the turnout in the CommandStation, and gives it an alias that can be referenced elsewhere by EX-RAIL. The comments in myAutomation.h have been included for reference.

.. code-block:: cpp

    /* Rokuhan turnout macro
    *  This macro defines Rokuhan turnouts as well as ensuring that these
    *  turnouts are driven only by a short pulse to prevent coil burn out.
    *  
    *  Call this macro with:
    *  ROKUHANTURNOUT(t, p1, p2, desc, ali)
    *  
    *  Where:
    *  t = turnout ID
    *  p1 = direction pin
    *  p2 = enable pin
    *  desc = description of the turnout
    *  ali = alias assigned to the turnout ID
    *  
    *  Adjust the PULSE time (in ms) below as required, 25 seems reliable.
    *  
    *  When a turnout command is sent to one of the defined turnouts, the macro
    *  will close or throw the turnout using the defined PULSE time.
    *  
    *  This also ensures all turnouts are defined as part of the compilation.
    */
    
    #define PULSE 25

    #define ROKUHANTURNOUT(t, p1, p2, desc, ali) \
        PIN_TURNOUT(t, 0, desc) \
        ALIAS(ali, t) \
        DONE \
        ONCLOSE(t) \
        SET(p1) \
        SET(p2)DELAY(PULSE)RESET(p2) \
        DONE \
        ONTHROW(t) \
        RESET(p1) \
        SET(p2)DELAY(PULSE)RESET(p2) \
        DONE

In essence, the macro (or command) above defines the turnout so it can be seen in Engine Driver or JMRI, gives it a human-friendly alias for automation activities, sets the correction direction for an L293D motor driver IC, and enables the output of that IC for a brief period of time to switch the turnout.

To implement this custom macro (or command if you prefer), you need to call it within myAutomation.h and pass the appropriate parameters as such:

.. code-block:: cpp
    ROKUHANTURNOUT(105, 168, 176, "Yard entrance", YD_E)

In this particular scenario:

- "t" is the chosen unique turnout ID (101) used to address the turnout, so in Engine Driver or JMRI this will show up as 101.
- "p1" is a VPIN (164) on an MCP23017 I/O expander connected to the first direction input on an L293D motor driver IC (the second input is via a hex inverter in case you're wondering).
- "p2" is another VPIN (176) on the same MCP23017 I/O expander connected to the enable pin on the same L293D motor driver IC.
- "desc" is the description of the turnout that will display in Engine Driver, note this must be surrounded by inverted commas or quotes.
- "ali" is an alias that can be referenced elsewhere in sequences or automations in myAutomation.h instead of having to remember the assigned turnout ID.

