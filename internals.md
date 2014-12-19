Distributed anti-cheating Pokémon system
========================================
Nintendo doesn't like fan-made games, they'll shut down anything that they fear
is going to damage their power over their franchise.

This is usually the biggest issue in making a new game, from scratch, around
the world of Pokémon, after a year of hard work Nintendo might just contact you
and tell you to cease and desist.

Completely in their right, but there's a simple way to work around that, if
there's no server to shut down or data to remove, there's nothing to cease.

This is why the best way for a Pokémon MMO (that isn't some rehashed thing with
sprites taken from the third generation), is to make it completely
decentralized.

The problem is without a server, there's no way to ensure the player isn't
cheating, what's preventing them from changing a value in memory or disk and
suddenly have everything?

This text aims to design cheat-prevention in a distributed Pokémon MMO.

Entities
--------
An entity is anything that can be tampered with to get an advantage on other
players, a Pokémon and the player are good examples of entities.

An entity needs to be verifiable, you can't just take what another client tells
you for granted, this is achieved through tracking the history of each entity
and verifying it to be real*(istic)*.

History
-------
The history is a chain of events that happen to the entity or the entity makes
happen.

Examples are a Pokémon using an ability in a battle, or the player winning a
fight and earning currency.

Event
-----
Each event that isn't a creation event is composed of few elements:

1. `data` - the event metadata, so that the event can be verified to be realistic
2. `timestamp` - the seconds passed since the creation of the history
3. `hash` - `SHA256(bcrypt(previous.hash) + timestamp + data)`

The `timestamp` is stored in comparison to the creation time to reduce file
size and make things easier.

The `hash` is the important part, it works as checksum for the previous event,
and ensures the history hasn't been tampered with. **bcrypt** is used to create
the salt to make forging histories almost impossible on a meaningful time scale
while still allowing proper gameplay.