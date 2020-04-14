---
title: Proaktivní konfigurace GRUB konzoly Azure Serial Console| Dokumenty společnosti Microsoft
description: Konfigurace GRUB napříč různými distribucemi, které umožňují přístup k režimu jednoho uživatele a režimu obnovení ve virtuálních počítačích Azure.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: 573bd0797e63fc512e59b0e0882c718e4569111c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262889"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Proaktivně zajištění přístupu k GRUB a sysrq vám může ušetřit spoustu prostojů

Přístup k sériové konzoli a GRUB zlepší dobu obnovení vašeho virtuálního počítače IaaS Linux ve většině případů. GRUB nabízí možnosti obnovení, které by jinak trvalo déle obnovit virtuální počítač. 


Důvody pro obnovení virtuálního mísy jsou mnohé a lze je připsat scénářům, jako jsou:

   - Poškozené souborové systémy/jádro/MBR (hlavní spouštěcí záznam)
   - Neúspěšné upgrady jádra
   - Nesprávné parametry jádra GRUB
   - Nesprávné konfigurace fstab
   - Konfigurace brány firewall
   - Ztracené heslo
   - Mangled sshd konfigurace soubory
   - Síťové konfigurace

 Mnoho dalších scénářů, jak [je podrobně popsáno zde](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)

Ověřte, že máte přístup ke grubu a konzolové konzoli Serial na virtuálních počítačích nasazených v Azure. 

Pokud se konzolou Serial Console tečujete, přečtěte si [tento odkaz](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> Před provedením změn se ujistěte, že zálohujete soubory

Podívejte se na toto video níže a zjistěte, jak můžete rychle obnovit virtuální počítač s Linuxem, jakmile budete mít přístup k GRUB

[Obnovení videa virtuálního počítače s Operačním systémem Linux](https://youtu.be/KevOc3d_SG4)

Existuje celá řada metod, které pomáhají obnovit virtuální počítače s Linuxem. V cloudovém prostředí byl tento proces náročný.
Neustále dochází k pokroku v oblasti nástrojů a funkcí, které zajišťují rychlé obnovení služeb.

S Konzolou Azure Serial Console můžete pracovat s virtuálním počítačem s Linuxem, jako byste byli na konzoli systému.

Můžete manipulovat s mnoha konfiguračními soubory, včetně toho, jak se jádro spustí. 

Zkušenější správci systému Linux/Unix ocení **režimy pro jednoho uživatele** a **nouzové režimy,** které jsou přístupné prostřednictvím konzoly Azure Serial Console, díky nimž je pro mnoho scénářů obnovení redundantní provýměnu a odstranění disku.

Metoda obnovení závisí na problému dochází, například ztracené nebo chybně umístěné heslo lze obnovit prostřednictvím možností portálu Azure -> **resetovat heslo**. Funkce **Resetovat heslo** se označuje jako rozšíření a komunikuje s agentem Host linuxu.

Další rozšíření, jako je vlastní skript jsou k dispozici však tyto možnosti vyžadují, aby **linuxový waagent** být nahoru a ve zdravém stavu, který není vždy případ.

![stav agenta](./media/virtual-machines-serial-console/agent-status.png)


Zajištění přístupu k službě Azure Serial Console a GRUB znamená, že změna hesla nebo nesprávná konfigurace lze opravit během několika minut namísto hodin. Můžete dokonce vynutit spuštění virtuálního počítače z alternativního jádra, pokud máte více jader na disku ve scénáři, kde dojde k poškození primárního jádra.

![více jádra](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Navrhované pořadí metod obnovení:

- Azure Serial Console

- Disk Swap – lze automatizovat pomocí:

   - [Skripty pro obnovení prostředí Power Shell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash Zotavení Skripty](https://github.com/sribs/azure-support-scripts)

- Starší metoda

## <a name="disk-swap-video"></a>Video pro výměnu disku:

Pokud nemáte přístup k GRUB podívejte se na [toto](https://youtu.be/m5t0GZ5oGAc) video a uvidíte, jak můžete snadno automatizovat postup odkládání disků obnovit virtuální počítač

## <a name="challenges"></a>Výzvy:

Ne všechny virtuální počítače Linux Azure jsou ve výchozím nastavení nakonfigurované pro přístup ke GRUB a ani všechny nejsou nakonfigurovány tak, aby byly přerušeny pomocí příkazů sysrq. Některé starší distribuce, například SLES 11, nejsou nakonfigurovány tak, aby zobrazovaly výzvu Přihlášení v konzoli Azure Serial Console

V tomto článku zkontrolujeme různé distribuce Linuxu a konfigurace dokumentů o tom, jak zpřístupnit GRUB.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Jak nakonfigurovat virtuální počítač s Linuxem pro přijímání klíčů SysRq
Klíč sysrq je povolen na některých novějších distribucích Linuxu ve výchozím nastavení, i když v jiných může být nakonfigurován pro příjem hodnot pouze pro určité funkce SysRq.
U starších distribucí může být zcela zakázán.

Funkce SysRq je užitečná pro restartování havarovaného nebo zavěšeného virtuálního počítače přímo z konzoly Azure Serial Console, která je také užitečná při získání přístupu k nabídce GRUB, případně restartování virtuálního počítače z jiného okna portálu nebo relace ssh může vynechat aktuální připojení konzoly, což vyprší vypršení časového limitu GRUB, na které se slouží k zobrazení nabídky GRUB.
Virtuální počítač musí být nakonfigurován tak, aby přijímal hodnotu 1 pro parametr jádra, který umožňuje všechny funkce sysrq nebo 128, což umožňuje restartování/vypnutí


[Povolit video sysrq](https://youtu.be/0doqFRrHz_Mc)


Chcete-li nakonfigurovat virtuální počítač tak, aby přijímal restartování pomocí příkazů SysRq na webu Azure Portal, budete muset nastavit hodnotu 1 pro parametr jádra kernel.sysrq

Aby tato konfigurace trvala při restartování počítače, přidejte položku do souboru **sysctl.conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Dynamické konfigurace parametru jádra

`sysctl -w kernel.sysrq=1`

Pokud nemáte **root** přístup nebo sudo je přerušeno, nebude možné konfigurovat sysrq z řádku prostředí.

Sysrq můžete povolit v tomto scénáři pomocí portálu Azure. Tato metoda může být užitečná, pokud došlo k porušení nebo odstranění **souboru sudoers.d/waagent.**

Použití funkce Operace portálu Azure -> Spustit příkaz -> RunShellScript vyžaduje, aby byl proces waagent v pořádku, můžete tento příkaz vložit a povolit sysrq.

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Jak je ![znázorněno zde: povolit sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Po dokončení, pak můžete zkusit přístup k **sysrq** a měli byste vidět, že restartování je možné.

![povolit sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Vybrat **příkaz Restartovat** a **odeslat příkaz SysRq**

![povolit sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

Systém by měl zaprotokolovat zprávu o obnovení, například tuto

![povolit sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Konfigurace Ubuntu GRUB

Ve výchozím nastavení byste měli mít přístup ke grubu podržením **klávesy Esc** během spuštění virtuálního počítače, pokud není zobrazena nabídka GRUB, můžete vynutit a ponechat nabídku GRUB na obrazovce v konzoli Azure Serial Console pomocí jedné z těchto možností.

**Možnost 1** - Vynutí zobrazení GRUB na obrazovce 

Aktualizujte soubor /etc/default/grub.d/50-cloudimg-settings.cfg, aby nabídka GRUB byla na obrazovce pro zadaný časový limit.
Nemusíte zasáhnout **Esc,** protože GRUB se zobrazí okamžitě

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Možnost 2** - Umožňuje **esc,** které mají být stisknuty před spuštěním

Podobné chování může být provedeno provedením změn v souboru /etc/default/grub a dodržujte časový limit 3 sekundy, aby se dosáhlo **hodnoty Esc**


Zakomentujte tyto dva řádky:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
a přidejte tento řádek:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 umožní přístup k sériové konzoli, ale nenabízí schopnost komunikovat. **Přihlášení:** výzva není vidět


Pro 12.04 získat **přihlášení:** výzva:
1. Vytvořte soubor s názvem /etc/init/ttyS0.conf obsahující následující text:

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. Zeptejte se povýšence začít getty     
    ```
    sudo start ttyS0
    ```
 
Nastavení potřebná ke konfiguraci sériové konzole pro verze Ubuntu naleznete [zde](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Režim obnovy Ubuntu

Další možnosti obnovy a vyčištění jsou k dispozici pro Ubuntu přes GRUB, ale tato nastavení jsou přístupná pouze tehdy, pokud odpovídajícím způsobem nakonfigurujete parametry jádra.
Pokud se nepodaří nakonfigurovat tento zaváděcí parametr jádra, vynutíte, aby se nabídka Obnovení odeslala do diagnostiky Azure, a nikoli do konzoly Azure Serial Console.
Přístup k nabídce obnovení Ubuntu můžete získat následujícím postupem:

Přerušení procesu BOOT a přístup k nabídce GRUB

Vyberte rozšířené možnosti pro Ubuntu a stiskněte klávesu ENTER

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Vyberte zobrazení čáry *(režim obnovení)* nestisknout enter, ale stiskněte tlačítko "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Vyhledejte řádek, který načte jádro, a nahraďte poslední parametr **nomodeset** cílem jako **console=ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Stisknutím **klávesctrl-x** spusťte a načtěte jádro.
Pokud vše půjde dobře, uvidíte tyto další možnosti, které mohou pomoci provést další možnosti obnovení

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Red Hat GRUB konfigurace

## <a name="red-hat-74-grub-configuration"></a>Red Hat\.\+ 7 4 GRUB konfigurace
Výchozí konfigurace /etc/default/grub v těchto verzích je odpovídajícím způsobem nakonfigurována

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Povolení klíče SysRq

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat\.7\.2 a 7 3 GRUB konfigurace
Soubor, který chcete upravit, je /etc/default/grub – výchozí konfigurace vypadá jako tento příklad:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Změna následujících řádků v /etc/default/grub

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

Přidejte také tento řádek:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub by nyní měl vypadat podobně jako v tomto příkladu:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Kompletní a aktualizovat grub konfiguraci pomocí

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Nastavte parametr jádra SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Můžete alternativně nakonfigurovat GRUB a SysRq pomocí jednoho řádku buď v prostředí nebo pomocí příkazu Spustit. Před spuštěním tohoto příkazu zálohujte soubory:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat\.6 x GRUB konfigurace
Soubor, který chcete upravit, je /boot/grub/grub.conf. Hodnota `timeout` určuje, jak dlouho je GRUB zobrazen.

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


Poslední řádek *terminálu --timeout =5 sériové konzole* bude dále zvyšovat **GRUB** timeout přidáním výzvu 5 sekund zobrazení **Stiskněte libovolnou klávesu pokračovat.**

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

GRUB menu by se mělo objevit na obrazovce pro nakonfigurovaný timeout = 15 bez nutnosti stisknout Esc. Ujistěte se, že klikněte v konzole v prohlížeči, aby se aktivní menu a vyberte požadované jádro

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>Suse

## <a name="sles-12-sp1"></a>SLES 12 š1
Buď použijte yast bootloader podle oficiálních [dokumentů](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles)

Nebo přidat/změnit /etc/default/grub následující parametry:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Ověřte, zda se v GRUB_CMDLINE_LINUX nebo GRUB_CMDLINE_LINUX_DEFAULT používá ttys0

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Znovu grub.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
Zobrazí se sériová konzola, zobrazí spouštěcí zprávy, ale nezobrazí **se přihlašovací číslo:** výzva

Otevřete relaci ssh do virtuálního virtuálního účtu a aktualizujte soubor **/etc/inittab** zrušením komentáře k tomuto řádku:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Další spuštění příkazu 

`telinit q`

Chcete-li povolit GRUB, je třeba provést následující změny na /boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Tato konfigurace umožní, aby se zpráva **Stisknutím libovolné klávesy** zobrazovala na konzoli po dobu 5 sekund 

Poté se zobrazí menu GRUB na dalších 5 sekund - stisknutím šipky dolů přerušíte počítadlo a vyberete jádro, které chcete spustit, a to buď připojit **klíčové slovo pro** režim jednoho uživatele, který vyžaduje nastavení kořenového hesla.

Připojení příkazu **init=/bin/bash** načte jádro, ale zajistí, že program init bude nahrazen bash shellem.

Získáte přístup k prostředí, aniž byste museli zadávat heslo. Potom můžete pokračovat v aktualizaci hesla pro účty Linux nebo provést jiné změny konfigurace.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Vynutit jádro na bash prompt
Mít přístup k GRUB umožňuje přerušit proces inicializace tato interakce je užitečná pro mnoho postupů obnovení.
Pokud nemáte root heslo a jeden uživatel vyžaduje, abyste měli root heslo, můžete zavést jádro nahrazující init program s bash prompt - toto přerušení lze dosáhnout připojením init = / bin / bash k zaváděcí lince jádra

![bash1](./media/virtual-machines-serial-console/bash1.png)

Znovu namontovat / (kořenový) souborový systém RW pomocí příkazu

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


Nyní můžete provést změnu hesla root nebo mnoho dalších změn konfigurace Linuxu

![bash3](./media/virtual-machines-serial-console/bash3.png)

Restartujte virtuální počítač pomocí 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Režim jednoho uživatele

Případně možná budete muset získat přístup k virtuálnímu virtuálnímu mněmu v režimu jednoho uživatele nebo nouzového režimu. Vyberte jádro, které chcete zavést nebo přerušit pomocí kláves se šipkami.
Zadejte požadovaný režim připojením klíčového slova **single** nebo **1** k zaváděcí lince jádra. V systémech RHEL můžete také připojit **rd.break**.

Další informace o přístupu k režimu jednoho uživatele naleznete v [tomto dokumentu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Další kroky
Další informace o [službě Azure Serial Console]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
