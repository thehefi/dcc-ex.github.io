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

