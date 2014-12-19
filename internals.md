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

Player
------
Every player has an ECDSA key pair that's used when catching a pokémon (to
generate the creation event) or when doing a pokémon transfer.

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

Creation
========
To ensure the pokémon sex, ability and nature aren't forged, the tracker will
generate them randomly and sign the generation with its own key.

Clients will then have a list of keys they trust and they'll be able to verify
the creation wasn't forged.

To avoid pokémon becoming unusable in case a tracker gets taken down, clients
will be able to trust multiple keys and use multiple trackers at the same time.

In case a key gets compromised there will be an option to trust pokémon
generated before a certain date.

Forging
=======
This design doesn't *prevent* forging, but it makes it utterly *pointless*. The
history must be realistic, this means you can't just put an event where you got
a gorillion experience points and made it up to maximum level with maximum
stats.

You'd have to carefully forge the history of the Pokémon you're trying to forge
and changes to the history of the player in a way that makes it level up, it
would be thousands, or hundreds of thousand of entries to forge, bcrypt salts
included.

And even if you could forge the whole history in an acceptable time-frame it
would be unusable, because it would end up in the future since you cannot
change the player history to have caught a pokémon in the past without changing
the whole history for the player thus resulting in forgery when verified by
clients that already know you.
