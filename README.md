# tas2783-compat

Stop-gap Debian package for TI TAS2783 SoundWire speaker amplifiers, made for
the HP OmniBook X Flip 14-kc0xxx (board 8EA1). Kernel 7.3 has everything else
this laptop needs. The package fills the gaps that remain until the kernel,
linux-firmware and alsa-ucm-conf catch up.

linux-firmware ships the 8EA1 amplifier firmware under four-field names such as
`ti/audio/tas2783/8EA1-0-0-0x9.bin`, but the tas2783-sdw driver asks for
`8EA1-0-0x9.bin`, so the speakers stay silent with "error playback without fw
download". The package links the names the driver asks for in
`/usr/lib/firmware/updates/`, where they can never clash with a firmware package
file. The links use the AAC tuning (`8EA1-0-0-*`); HP also ships an FG set
(`8EA1-1-0-*`) for the other speaker supplier.

alsa-ucm-conf gained `ucm2/sof-soundwire/tas2783.conf` after its 1.2.16.1
release, and without it PipeWire has no Speaker device for this card. The package
carries an unchanged copy and links it into place only when alsa-ucm-conf does
not have it, so an alsa-ucm-conf release that includes it simply replaces the link.

The amplifiers' I/V-sense capture (`SDW0-PIN4-CAPTURE-SmartAmp`) can't be opened:
TAS2783 uid C answers with "port 2 not supported for TX". WirePlumber's "Pro
Audio" profile probes every PCM, so each session start logged about 120 kernel
errors. A WirePlumber rule turns Pro Audio off for this card (matched on the
`-8EA1` card long name). UCM HiFi still provides every device.

Drop the UCM part once Debian's alsa-ucm-conf ships the file, the firmware
links once the driver or linux-firmware agree on a name, and the WirePlumber
rule once the kernel stops creating the broken capture link.
