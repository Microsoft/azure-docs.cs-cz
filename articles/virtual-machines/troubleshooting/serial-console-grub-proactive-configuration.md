---
title: Proaktivní GRUB konfigurace služby Azure Serial Console | Microsoft Docs
description: Nakonfigurujte GRUB napříč různými distribucemi, které umožňují přístup k jednomu uživateli a režimu obnovení na virtuálních počítačích Azure.
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
ms.openlocfilehash: aba47500400004c1d6a7044a266bad6f20d5d9c9
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360544"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Proaktivní zajištění přístupu k GRUB a SysRq vám může ušetřit spoustu času.

Když máte přístup ke konzole sériového prostředí a GRUB, ve většině případů se prodlouží doba obnovení virtuálního počítače s IaaS Linux. GRUB nabízí možnosti obnovení, které by jinak mohly obnovit virtuální počítač déle. 


Důvody pro provedení obnovení virtuálního počítače jsou mnoho a můžou se jim přizpůsobovat tyto scénáře:

   - Poškozené systémy souborů/jádra/MBR (hlavní spouštěcí záznam)
   - Neúspěšné upgrady jádra
   - Nesprávné parametry jádra GRUB
   - Nesprávná konfigurace fstab
   - Konfigurace brány firewall
   - Ztracené heslo
   - Soubory s pozměněnými sshd konfiguracemi
   - Síťové konfigurace

 Mnoho dalších scénářů, jak je popsáno [zde](./serial-console-linux.md#common-scenarios-for-accessing-the-serial-console)

Ověřte, že máte přístup k GRUB a Sériová konzola na virtuálních počítačích nasazených v Azure. 

Pokud s konzolou sériového portu začínáte, přečtěte si [Tento odkaz](./serial-console-linux.md).

> [!TIP]
> Před provedením změn se ujistěte, že jste provedli zálohování souborů.

Podívejte se na následující video a zjistěte, jak můžete rychle obnovit virtuální počítač se systémem Linux, když máte přístup k GRUB.

[Video o obnovení virtuálního počítače se systémem Linux](https://youtu.be/KevOc3d_SG4)

K dispozici je několik metod, které vám pomůžou s obnovením virtuálních počítačů se systémem Linux. V cloudovém prostředí byl tento proces náročný.
Probíhá nepřetržité provádění nástrojů a funkcí, které zajistí rychlé obnovení služeb.

Pomocí konzole sériového prostředí Azure můžete s VIRTUÁLNÍm počítačem se systémem Linux pracovat, jako kdyby jste pracovali v konzole systému.

Můžete manipulovat s mnoha konfiguračními soubory, včetně způsobu, jakým se jádro spustí. 

Zkušení správci systému Linux/UNIX budou porušovat **jednotlivé uživatelské** a  **nouzové režimy** , které jsou přístupné prostřednictvím konzoly Azure Serial, což provádí swap disku a odstraňování virtuálních počítačů pro mnoho scénářů obnovení.

Metoda obnovení závisí na problému, ke kterému došlo, například ztracené nebo nesprávně umístěné heslo je možné resetovat prostřednictvím Azure Portal možností – >  **resetovat heslo**. Funkce **reset Password** se nazývá rozšíření a komunikuje s agentem hosta v systému Linux.

K dispozici jsou i další rozšíření, jako je vlastní skript, ale tyto možnosti vyžadují, aby se Linux **waagent** v dobrém stavu, který není vždycky v případě případu.

![Stav agenta](./media/virtual-machines-serial-console/agent-status.png)


Ujistěte se, že máte přístup ke konzole sériového rozhraní Azure a GRUB znamená, že se změna hesla nebo nesprávná konfigurace dá opravit v řádu minut, nikoli v hodinách. Můžete dokonce vynutit, aby se virtuální počítač spouštěl z alternativního jádra, pokud máte v situaci, kdy je vaše primární jádro poškozené, více jader na disku.

![více jader](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Navrhované pořadí metod obnovení:

- Sériová konzola Azure

- Výměna disku – můžete automatizovat pomocí těchto akcí:

   - [Skripty pro obnovení PowerShellu](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [Skripty pro obnovení bash](https://github.com/sribs/azure-support-scripts)

- Legacy – metoda

## <a name="disk-swap-video"></a>Video o prohození disku:

Pokud nemáte přístup k GRUB, podívejte se na [Toto](https://youtu.be/m5t0GZ5oGAc) video a podívejte se, jak můžete snadno automatizovat postup odkládacího disku pro obnovení virtuálního počítače.

## <a name="challenges"></a>Výzev

Ve výchozím nastavení nejsou všechny virtuální počítače Azure Linux nakonfigurované pro přístup GRUB a ani nejsou nakonfigurované tak, aby se přerušily pomocí příkazů SysRq. Některé starší distribuce, jako je SLES 11, nejsou nakonfigurované tak, aby zobrazovaly výzvu k přihlášení v konzole Azure Serial Console.

V tomto článku si probereme různé distribuce systému Linux a konfigurace dokumentů, jak zpřístupnit GRUB k dispozici.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Postup konfigurace virtuálního počítače se systémem Linux pro příjem klíčů SysRq
Klíč SysRq je ve výchozím nastavení povolený u některých novějších distribuce systému Linux, i když u jiných může být nakonfigurovaný pro přijímání hodnot jenom pro určité funkce SysRq.
Na starším distribuce může být zcela zakázán.

Funkce SysRq je užitečná pro restartování chybných nebo nereagujících virtuálních počítačů přímo z konzole Azure Serial Console, která je také užitečná při získávání přístupu do nabídky GRUB, nebo při restartování virtuálního počítače z jiného okna portálu nebo relace SSH může vyřadit aktuální připojení konzoly, čímž vyprší časový limit GRUB, který se použije k zobrazení nabídky GRUB.
Virtuální počítač musí být nakonfigurovaný tak, aby pro parametr jádra přijímal hodnotu 1, která umožňuje všechny funkce SysRq nebo 128, což umožňuje restart/stavu PowerOff.


[Povolit SysRq video](https://youtu.be/0doqFRrHz_Mc)


Pokud chcete virtuální počítač nakonfigurovat tak, aby přijímal restart přes SysRq příkazy na Azure Portal, budete muset nastavit hodnotu 1 pro parametr jádra kernel.sysRQ.

Aby tato konfigurace trvala restart, přidejte do souboru **sysctl. conf položku.**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Dynamické konfigurace parametru jádra

`sysctl -w kernel.sysrq=1`

Pokud nemáte **kořenový** přístup nebo je sudo, nebude možné konfigurovat SysRq z příkazového řádku prostředí.

V tomto scénáři můžete povolit SysRq pomocí Azure Portal. Tato metoda může být výhodná, pokud byl soubor  **sudoers. d/waagent** poškozen nebo byl odstraněn.

Pomocí funkce Azure Portal Operations-> spustit příkaz > RunShellScript vyžaduje, aby byl proces waagent v pořádku, abyste mohli povolit SysRq, můžete tento příkaz Vložit.

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Jak je znázorněno zde: ![ Povolení sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Po dokončení se můžete pokusit o přístup k **SysRq** a měli byste vidět, že je možné restartovat počítač.

![Povolit sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Vyberte příkaz **restartovat** a **Odeslat SysRq** .

![Povolit sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

Systém by měl protokolovat resetující zprávu, například

![Povolit sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Konfigurace Ubuntu GRUB

Ve výchozím nastavení byste měli mít přístup k GRUB tím, že při spuštění virtuálního počítače podržíte klávesu **ESC** , pokud se nabídka grub nezobrazuje, můžete vynutit a ponechat nabídku grub na obrazovce v konzole sériového rozhraní Azure pomocí jedné z těchto možností.

**Možnost 1** – vynutí zobrazení grub na obrazovce 

Aktualizujte soubor/etc/default/grub.d/50-cloudimg-Settings.cfg tak, aby se pro zadaný časový limit zachovala nabídka GRUB na obrazovce.
Není nutné, abyste provedli **klávesu ESC** , protože grub se zobrazí okamžitě.

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Možnost 2** – umožní stisknutí **klávesy ESC** před spuštěním.

K podobnému chování může dojít, když provedete změny v souboru/etc/default/grub a při stisknutí **klávesy ESC** sledovat 3 sekundy.


Odkomentujte tyto dva řádky:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
a přidejte tento řádek:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12 \. 04

Ubuntu 12,04 umožní přístup ke konzole sériového portu, ale nenabízí možnost pracovat. **Přihlášení:** výzva se nezobrazuje.


12,04 pro získání **přihlašovacích údajů:** výzva:
1. Vytvořte soubor s názvem/etc/init/ttyS0.conf, který obsahuje následující text:

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

2. Požádat o spuštění Getty     
    ```
    sudo start ttyS0
    ```
 
Nastavení potřebná ke konfiguraci sériové konzoly pro verze Ubuntu najdete [tady](https://help.ubuntu.com/community/SerialConsoleHowto) .

## <a name="ubuntu-recovery-mode"></a>Režim obnovení Ubuntu

Další možnosti obnovení a vyčištění jsou dostupné pro Ubuntu prostřednictvím GRUB, ale tato nastavení jsou dostupná jenom v případě, že se odpovídajícím způsobem konfigurují parametry jádra.
Při konfiguraci tohoto parametru spuštění jádra by se měla vynutit odeslání nabídky obnovení do Azure Diagnostics, a ne do konzole Azure Serial Console.
Přístup k nabídce obnovení Ubuntu můžete získat pomocí následujících kroků:

Přerušení SPOUŠTĚCÍho procesu a přístup k GRUB nabídce

Vyberte rozšířené možnosti pro Ubuntu a stiskněte ENTER.

![Snímek obrazovky ukazuje Sériová konzola s vybranými pokročilými možnostmi pro Ubuntu.](./media/virtual-machines-serial-console/ubunturec1.png)

Vyberte řádek se zobrazením *(režim obnovení)* nestiskněte klávesu ENTER, ale stiskněte klávesu "e".

![Snímek obrazovky ukazuje Sériová konzola se zvolenou verzí režimu obnovení.](./media/virtual-machines-serial-console/ubunturec2.png)

Vyhledejte řádek, který načte jádro, a nahraďte poslední parametr **nomodeset** jako cíl jako **Console = ttyS0** .

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![Snímek obrazovky zobrazuje Sériová konzola se změněnou hodnotou.](./media/virtual-machines-serial-console/ubunturec3.png)

Stisknutím **kombinace kláves CTRL + x** spusťte a načtěte jádro.
Pokud vše proběhne správně, uvidíte tyto další možnosti, které vám můžou pomáhat s dalšími možnostmi obnovení.

![Snímek obrazovky ukazuje Sériová konzola v nabídce obnovení, která nabízí další možnosti obnovení.](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Konfigurace Red Hat GRUB

## <a name="red-hat-74-grub-configuration"></a>GRUB konfigurace Red Hat 7 \. 4 \+
Výchozí konfigurace/etc/default/grub na těchto verzích je vhodně nakonfigurovaná.

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

Povolit klíč SysRq

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>GRUB konfigurace Red Hat 7 \. 2 a 7 \. 3
Soubor, který se má upravit, je/etc/default/grub – výchozí konfigurace vypadá jako v tomto příkladu:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Změnit následující řádky v/etc/default/grub

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

/etc/default/grub by teď měl vypadat podobně jako v tomto příkladu:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Dokončení a aktualizace konfigurace grub pomocí

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Nastavte parametr jádra SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Případně můžete konfigurovat GRUB a SysRq pomocí jednoho řádku buď v prostředí shell, nebo pomocí příkazu run. Před spuštěním tohoto příkazu zálohujte soubory:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Konfigurace Red Hat 6 \. x grub
Soubor, který se má upravit, je/boot/grub/grub.conf.. `timeout`Hodnota určuje, jak dlouho se má grub zobrazovat.

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


Poslední řádek  *terminál –-timeout = 5 sériové konzole* zvýší časový limit **grub** přidáním výzvy k zadání 5 sekund a **pokračováním stisknutí libovolné klávesy.**

![Snímek obrazovky ukazuje konzolu s výstupem.](./media/virtual-machines-serial-console/rh6-1.png)

Nabídka GRUB by se měla zobrazit na obrazovce pro nakonfigurovaný časový limit = 15, aniž by bylo nutné stisknout klávesu ESC. Nezapomeňte kliknout na konzolu v prohlížeči a aktivovat tak nabídku a vybrat požadované jádro.

![Snímek obrazovky zobrazuje konzolu se dvěma možnostmi systému Linux.](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 SP1
Buď použijte program pro spouštění YaST jako oficiální [dokumentaci](./serial-console-grub-single-user-mode.md#grub-access-in-suse-sles)

Nebo přidejte nebo změňte/etc/default/grub následující parametry:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Ověřte, že se v GRUB_CMDLINE_LINUX používá ttyS0 nebo GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Opětovné vytvoření grub. cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
Zobrazí se konzola sériového portu a zobrazí se spouštěcí zprávy, ale nezobrazí se **přihlašovací jméno:** výzva

Otevřete na virtuálním počítači relaci SSH a aktualizujte soubor **/etc/inittab** tak, že Odkomentujete tento řádek:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Dále spusťte příkaz 

`telinit q`

Chcete-li povolit GRUB, je třeba provést následující změny/boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Tato konfigurace umožní, aby se zpráva po dobu 5 sekund zobrazovala v konzole, aby se zobrazila **Jakákoli klávesa** . 

Pak se po stisknutí šipky dolů zobrazí nabídka GRUB (po dalších 5 sekundách) a vyberte jádro, které chcete spustit: buď přidejte klíčové slovo **Single** pro režim single user, který vyžaduje nastavení kořenového hesla.

Připojení příkazu **init =/bin/bash** načte jádro, ale zajistí, že je program init nahrazen prostředím bash shell.

Budete mít přístup k prostředí bez nutnosti zadávat heslo. Pak můžete pokračovat v aktualizaci hesla pro účty systému Linux nebo provést jiné změny konfigurace.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Vynutit jádro na příkazovém řádku bash
Přístup k GRUB vám umožňuje přerušit proces inicializace. Tato interakce je užitečná pro mnoho postupů obnovení.
Pokud nemáte kořenové heslo a jeden uživatel vyžaduje, abyste měli kořenové heslo, můžete spustit jádro, které nahradí inicializační program pomocí bash výzvy – toto přerušení je možné dosáhnout připojením init =/bin/bash ke spouštěcímu řádku jádra.

![Snímek obrazovky zobrazuje konzolu s aktualizovaným spouštěcím řádkem.](./media/virtual-machines-serial-console/bash1.png)

Opětovně připojte soubor/(root) systém souborů RW pomocí příkazu.

`mount -o remount,rw /`

![Snímek obrazovky ukazuje konzolu s akcí znovu připojit.](./media/virtual-machines-serial-console/bash2.png)


Nyní můžete provést změnu kořenového hesla nebo mnoho dalších změn konfigurace systému Linux.

![Snímek obrazovky ukazuje konzolu, kde můžete změnit kořenové heslo a další konfiguraci.](./media/virtual-machines-serial-console/bash3.png)

Restartujte virtuální počítač s 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Režim jednoho uživatele

Případně je možné, že budete potřebovat přístup k virtuálnímu počítači v režimu jednoho uživatele nebo v nouzovém režimu. Vyberte jádro, které chcete spustit, nebo přerušit pomocí kláves se šipkami.
Zadejte požadovaný režim připojením klíčového slova **Single** nebo **1** ke spouštěcímu řádku jádra. V systémech RHEL můžete také připojit **Rd. Break**.

Další informace o tom, jak přistupovat k jednomu uživatelskému režimu, najdete v [tomto dokumentu](./serial-console-grub-single-user-mode.md#general-single-user-mode-access) . 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Další kroky
Další informace o [službě Azure Serial Console]( ./serial-console-linux.md)