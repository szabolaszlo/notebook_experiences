# Notebook Experiences #

## ASUS ZenBook 14 UX433FN ##

### Suspend Mode in Linux - Not wake from suspend - Freez after suspend ###
Try in POP OS! 18.04 and Ubuntu 18.04. The bug exist both.
Problem with the `s2idle` suspend mode:
https://bugs.launchpad.net/ubuntu/+source/linux/+bug/1808957?fbclid=IwAR3W10gQ9ahx8evGgbR8KtsPTgU5-wb9h-9SpQxqi3VrtHtR4nbdqel6Sbo

#### Resolve in Ubuntu 18.04 ####
I have confirmed this by suspending and then checking:
sudo journalctl | grep "PM: suspend" | tail -2. If the output is

PM: suspend entry (s2idle)
PM: suspend exit

cat /sys/power/mem_sleep showed

[s2idle] deep

As a temporary fix, I typed
echo deep > /sys/power/mem_sleep
as a root user (sudo -i).

Then the output of cat /sys/power/mem_sleep was
s2idle [deep]

After suspending now,
sudo journalctl | grep "PM: suspend" | tail -2 returns

PM: suspend entry (deep)
PM: suspend exit

I have made this permanent by editing
/etc/default/grub

and replacing
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
with
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash mem_sleep_default=deep"

then regenerating my grub configuration (sudo grub-mkconfig -o /boot/grub/grub.cfg).

This appears to be working with no ill effects.

#### Resolve in POP OS! 18.04 ####
After running cat /sys/power/mem_sleep my console showed [s2idle] deep.
Then I run sudo kernelstub -a mem_sleep_default=deep, rebooted and it changed previous command output to s2idle [deep] - and my problem with suspend is now gone.
