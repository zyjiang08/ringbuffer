# ringbuffer
Lock free ringbuffer for real time synchronisation

Ringbuffer (circular FIFO)

Marc\_G 2016

We need two FIFOs: one for realtime audio to enter and one for outgoing.

## Real time audio input

JACK writes into FIFO (push) and is never held back by FIFO full.
Analysis reads from FIFO (pop) and blocks on FIFO empty.

This means that analysis should be fast enough to keep up with incoming
data and will on average be _just_ behind the incoming audio.


## Real time audio output

JACK reads from FIFO (pop) and is never held back by FIFO empty.
Synthesis writes into FIFO (push) and blocks on FIFO full.

This means that synthesis should be fast enough to keep up with outgoing
data and will on average be some time before the incoming audio. This time
must be as low as possible to prevent latency.

# DESIGN

FIFO design can be generic, with blocking push() and pop() and atomic
passing of tail and head. No need for a mutex if head and tail are to
be updated independently and atomically (one word each).

N.B.: atomic does not necessarily mean lock-free because the implementation
can be done using mutexes. Therefore we need to check fifo.isLockFree()

For efficiency, push() and pop() receive a pointer to a memory area and
perform a memcpy


# SEE ALSO
https://github.com/jackaudio/jack1/blob/master/libjack/ringbuffer.c
(JACK ringbuffer)

