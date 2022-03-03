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

Pay attention to the author's note also to understand why DCC++ EX was created in the first place, and particular his comment on "reversing the usual assumptions", which leads us to tip 2.

Think from the Engineer's perspective
--------------------------------------

A common topic discussed in the DCC++ EX `Discord server <https://discord.gg/PuPnNMp8Qf>`_ is how to deal with some of the concepts that need to be understood in order to define successful automation sequences.

The one key concept arising out of these conversations is to think from the Engineer's perspective, ie. think from the view of the person driving the train, not from the overall layout control perspective.

This in my mind is akin to DC vs. DCC control to a certain extent. In DC, you're effectively driving or controlling the track, not the locomotives directly, whereas in DCC you are controlling the locomotives directly.

As an example of putting this concept into practise, consider the RESERVE() and FREE() functions that control when a locomotive and enter a virtual block.

Let's take Example 7 from the :doc:`/automation/EX-RAIL-intro` page:

.. code-block:: cpp

    SEQUENCE(12) // From block 1 to block 2
      DELAYRANDOM(10000,20000) // random wait between 10 and 20 seconds
      RESERVE(2) // we wish to enter block 2… so wait for it
      CLOSE(1) // Now we “own” the block, set the turnout
      FWD(30) // and proceed forward
      AFTER(71) // Once we have reached AND passed sensor 71
      FREE(1) // we no longer occupy block 1
      AT(72) // When we get to sensor 72
      FOLLOW(23) // follow route from block 2 to block 3
   
   SEQUENCE(23) // Travel from block 2 to block 3
      RESERVE(3) // will STOP if block 3 occupied
      CLOSE(2) // Now we have the block, we can set turnouts
      FWD(20) // we may or may not have stopped at the RESERVE
      AT(42) // sensor 2 is at the far end of platform B
      STOP
      FREE(2)
      DELAY(15000)
      FOLLOW(34)
   
   SEQUENCE(34) // you get the idea
      RESERVE(4)
      THROW(2)
      REV(20)
      AFTER(73)
      FREE(3)
      AT(74)
      FOLLOW(41)
   
   SEQUENCE(41)
      RESERVE(1)
      THROW(1)
      REV(20)
      AT(41)
      STOP
      FREE(4)
      FOLLOW(12) // follows Route 12 again… forever

