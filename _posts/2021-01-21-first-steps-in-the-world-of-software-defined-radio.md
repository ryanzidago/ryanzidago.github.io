I recently purchased a [RTL-SDR R820T2 TCXO Dongle with dipole antenna kit](https://shop.jetvision.de/epages/64807909.sf/sec05a62a858d/?ObjectPath=/Shops/64807909/Products/53196).

![RTL-SDR R820T2 TCXO Dongle with dipole antenna kit](https://shop.jetvision.de/WebRoot/Store26/Shops/64807909/5DF3/4ED6/8F49/F332/04A9/0A0C/6D12/8B31/53256.png)

Let's listen to a local radio station with it.

After you have plugged in the USB dongle into your computer, as well as the antenna to the RTL-SDR device, follow the instructions in this [blog post](https://www.scivision.dev/sdr-sharp-ubuntu/).

When you run SDR#, you might see an error message:
```
Error loading 'SDRSharp.SDRIQ.SdrIqIO,SDRSharp.SDRIQ' - Exception has been thrown by the target of an invocation.
```

Simply click the `ok` button. It is possible that a second error message pops up:

```
Error loading 'SDRSharp.HackRF.HackRFIO,SDRSharp.HackRF' - Exception has been thrown by the target of an invocation
```

Here also, simply click `ok`. Those errors are a bit cryptic. All I know is that they are about two SDR receivers:
- the SDR-IQ (that is discontinued according to [here](http://www.rfspace.com/RFSPACE/SDR-IQ.html))
- and the HackRF One (more information on this device [here](https://greatscottgadgets.com/hackrf/one/))

Since we use the RTL-SDR receiver, we should actually be fine.

Now you should see the SDRSharp window:
![SDR Sharp](/assets/images/Screenshot-from-2021-01-21 19-42-08.png)

Left to the `Play/Stop` button, you can configure the source input. Select `RTL-SDR / USB`.
Now look up for frequencies of a radio station in your area and input one of them to the field with the ten digits number separated by dots:
```
0.000.000.000
```

For example, if you were in London and wanted to listen to [BBC Radio 2](https://www.transmissionzero.co.uk/radio/london-fm-radio/) on the `88.4` frequency, you would set the frequency like so in SDRSharp:
```
0.088.400.000
```
Under the radio section, there are 8 [modulation modes](https://en.wikipedia.org/wiki/Category:Radio_modulation_modes). Select the `WFM` radio button and press the play button. According to the [RTL-SDR website](https://www.rtl-sdr.com/tag/wfm/), the WFM mode, (Stereo Wideband FM signal) "is used for typical broadcast radio [...]". Common frequencies are from 87.5 to 108.0 MHz, which is why for example, all of the [London FM radio stations](https://www.transmissionzero.co.uk/radio/london-fm-radio/) emit within this frequencies range.

Normally, the sound should also contain a lot of noise. To improve the signal-to-noise ratio, you need to configure the `RF Gain`. I have found a good explanation of what the `RF Gain` is [here](https://www.wearecb.com/what-is-rf-gain-cb-radio.html):

> RF is used as a synonym for "radio," in this case a CB radio. It describes the wireless communication of signals through the air rather than through wires like a plug-in home phone. Noise interferes with signals and it comes from the atmosphere, other nearby channels that overlap slightly, and environmental surroundings. To counteract noise, RF gain acts as a sensitivity filter. It reduces noise in the receiver without reducing reception power as a CB radio squelch does.

Click on `Configure`, the button left of where you selected `RTL-SDR / USB` as input source. The following pop us will be displayed on the screen:

![SDRSharp Configure Pop Up](/assets/images/Screenshot from-2021-01-21-19-57-13.png)

At the bottom of the pop up, you will see a `RF Gain` setting. By default, it is set to zero. Increase the value of this setting to improve the sound quality, and voilà! You should now be able to listen to local radio stations with your SDR dongle.

I do not really understand why yet but unplugging the antenna from the wire (without unplugging the wire from the receiver) does not prevent the USB dongle to receive signals. However, unplugging the wire from the dongle make the latter unable to receive any signals.
