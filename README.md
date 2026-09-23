# netbad

Break your network on purpose, to test how your software copes.

A friendly wrapper around Linux `tc` + `netem`. Linux only.

## Why this exists

```bash
sudo tc qdisc add dev eth0 root netem loss 10% delay 100ms
```

I keep googling the same `tc` commands. I keep copy-pasting things I didn't want to understand again. I keep thinking: "there has to be a better way to say this."

So I wrote netbad.

This is the tool that worked for me. It may or may not work for you. That's fine.

## Install

```bash
git clone https://github.com/arjunshajitech/netbad.git
cd netbad
sudo install -m 755 netbad /usr/local/bin/netbad
```

Check it worked:

```bash
netbad help
```

Uninstall: `sudo rm /usr/local/bin/netbad`

## Use

```bash
netbad drop 10%          # lose 10% of packets
netbad delay 100ms       # add 100ms of delay
netbad shuffle 10%       # deliver 10% out of order
netbad duplicate 5%      # send 5% of packets twice
netbad corrupt 1%        # flip a bit in 1% of packets
netbad limit 1mbit       # cap the bandwidth

netbad status            # what's happening now
netbad list              # all interfaces and their state
netbad clear             # put it back to normal
```

No `sudo` needed — netbad asks for it when it needs it.

## Extras

Add these to any command:

```bash
netbad drop 10% on eth0     # pick an interface (default: your internet one)
netbad drop 10% for 30s     # undo it automatically after 30s
netbad delay 100ms --only   # replace what's applied instead of adding to it
netbad drop 10% --dry-run   # show the tc command, change nothing
```

Impairments stack — `netbad drop 10%` then `netbad delay 100ms` gives you both.

## Good to know

- Only **outgoing** traffic is affected. That's how netem works.
- It hits all traffic on the interface, not one app or port.
- Careful over SSH — use `for 30s` so it heals itself.
- Nothing survives a reboot. `netbad clear all` fixes anything you forgot.
