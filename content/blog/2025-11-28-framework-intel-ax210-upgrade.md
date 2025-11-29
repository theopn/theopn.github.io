+++
date = '2025-11-28T22:40:33-05:00'
draft = false
title = 'Upgrading My Framework 13 With Suspicious Intel AX210 Network Card'
tags = ["laptop", "framework", "Linux"]
+++

MediaTek RZ616 network cards that ship with Framework laptops with AMD Ryzen 7040 series or Ryzen "AI" 300 series CPU are [known](https://community.frame.work/t/solved-rz616-wifi-issues/42240) [to](https://www.reddit.com/r/framework/comments/1bavjic/friendly_psa_the_ax210_does_work_in_the_amd_7040/) [be](https://community.frame.work/t/intel-ax210-solved-my-wifi-issues/72880) [unreliable](https://community.frame.work/t/mediatek-rz616-very-slow).
I too, have been experiencing issues with it in my Framework 13.
The speed is acceptable, but establishing connection, especially with my school Wi-Fi that uses 802.1x authentication, would take minutes, and in many densely populated areas, it would disconnect frequently.
Since it was on Black Friday sale, I bought the Intel AX210 from the Amazon link in [this Framework forum thread](https://community.frame.work/t/intel-ax210-solved-my-wifi-issues/72880).
It seemed to be a popular upgrade, and as long as you get one *without* the Intel vPro, it is a simple and effective upgrade.

## Did I Get Scammed?

One thing I did not know: it turns out, Intel AX210 is name of the chipset, and listings for PCIe cards you see on Amazon are other manufacturers making network cards with the said chipset.
So imagine my surprise when I opened the box and it was from...

{{< figure src="/images/2025-11-28-framework-intel-ax-210-upgrade/reduced-stripped-intel-ax-210-packaging-00.JPG" position="center" caption="M.2 WiFi Card from... what?"  >}}

{{< figure src="/images/2025-11-28-framework-intel-ax-210-upgrade/reduced-stripped-intel-ax-210-packaging-01.JPG" position="center" caption="I did not blur the text, it is part of the manual"  >}}

{{< figure src="https://i.kym-cdn.com/entries/icons/original/000/053/460/reading_paper_meme_cover.jpg" position="center" caption="I -??"  >}}

Apparently, [I wasn't alone in questioning the naming choice](https://www.reddit.com/r/ZephyrusG14/comments/1bhu1uz/people_who_upgraded_to_an_intel_ax210_from_amazon/).
Please invest a little bit of time and money to proofread your company name when you are doing a business in English speaking countries.

## Installation

After verifying that I probably was not scammed, I proceeded with the installation.

{{< figure src="/images/2025-11-28-framework-intel-ax-210-upgrade/reduced-stripped-intel-ax-210-installation-00.JPG" position="center" caption="So easy to work with"  >}}

{{< figure src="/images/2025-11-28-framework-intel-ax-210-upgrade/reduced-stripped-intel-ax-210-installation-01.JPG" position="center" caption="Comparison with the MediaTek RZ616 -- ignore the mess, I dusted the internals out before closing"  >}}

{{< figure src="/images/2025-11-28-framework-intel-ax-210-upgrade/reduced-stripped-intel-ax-210-installation-02.JPG" position="center" caption="Seems to be a genuine Intel chip, phew"  >}}

{{< figure src="/images/2025-11-28-framework-intel-ax-210-upgrade/reduced-stripped-intel-ax-210-installation-03.JPG" position="center" caption="Antenna organized, ready to go!" >}}

I used `lspci -k` to verify that it was indeed the Intel AX 210.

{{< figure src="/images/2025-11-28-framework-intel-ax-210-upgrade/lspci-network-controller.png" position="center" caption="" >}}

## Massive Speed Improvement?

I used `speedtest-cli` in my apartment Wi-Fi (unknown spec) before and after the installation.

Here is the before:

{{< figure src="/images/2025-11-28-framework-intel-ax-210-upgrade/speedtest-apartment-mediatek.png" position="center" caption="MediaTek RZ616" >}}

And here is the result after the upgrade!

{{< figure src="/images/2025-11-28-framework-intel-ax-210-upgrade/speedtest-apartment-intel.png" position="center" caption="Intel AX210" >}}

And the crowd goes... mild *<insert that one GIF of ants starring at something from Bug's Life that you cannot find anywhere online because Disney>*

For the reference, this is the same test on my M1 MacBook Air:

{{< figure src="/images/2025-11-28-framework-intel-ax-210-upgrade/speedtest-apartment-m1.jpg" position="center" caption="M1" >}}

This is obviously not the most scientific testing, but a simple test in my school WiFi showed that it was marginally better than the MediaTek, but not as good as whatever the integrated network module that Apple uses.

## Conclusion

The real test is whether it can holds a reliable connection even in crowded areas, which was my biggest issue with the MediaTek.
I cannot test that until next week when the Thanksgiving break ends, so fingers crossed.

