---
title: Synchronizace času pro virtuální počítače s Linuxem v Azure
description: Synchronizace času pro virtuální počítače s Linuxem.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 7c93c1f525713a90abd71c30a21401b9d1cfcb9f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460898"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Synchronizace času pro virtuální počítače s Linuxem v Azure

Synchronizace času je důležitá pro zabezpečení a korelaci událostí. Někdy se používá pro implementaci distribuovaných transakcí. Časové přesnosti mezi více počítačovými systémy je dosaženo prostřednictvím synchronizace. Synchronizace může být ovlivněna více věcmi, včetně restartování a síťového provozu mezi zdrojem času a počítačem, který načítá čas. 

Azure je podpořený infrastrukturou se systémem Windows Server 2016. Windows Server 2016 vylepšil algoritmy používané ke spravení času a stavu místních hodin pro synchronizaci s utc.  Funkce Přesný čas systému Windows Server 2016 výrazně zlepšila způsob, jakým služba VMICTimeSync, která řídí virtuální moduly s hostitelem pro přesný čas. Vylepšení zahrnují přesnější počáteční čas na spuštění virtuálního počítači nebo obnovení virtuálního počítači a opravu latence přerušení. 

>[!NOTE]
>Rychlý přehled služby Systémový čas naleznete v tomto [video přehledu na vysoké úrovni](https://aka.ms/WS2016TimeVideo).
>
> Další informace naleznete [v tématu Přesný čas pro systém Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Přehled

Přesnost počítačových hodin je měřena podle toho, jak blízko jsou hodiny počítače k časovému standardu Koordinovaný světový čas (UTC). UTC je definována nadnárodním vzorkem přesných atomových hodin, které mohou být vypnuty pouze o jednu sekundu za 300 let. Čtení UTC však přímo vyžaduje specializovaný hardware. Místo toho časové servery jsou synchronizovány s UTC a jsou přístupné z jiných počítačů za účelem zajištění škálovatelnosti a robustnosti. Každý počítač má spuštěnou službu synchronizace času, která ví, jaké časové servery použít, a pravidelně kontroluje, zda je třeba opravit hodiny počítače, a v případě potřeby upraví čas. 

Hostitelé Azure jsou synchronizováni s interními časovými servery Microsoftu, které si berou čas ze zařízení Stratum 1 vlastněných microsoftem s anténami GPS. Virtuální počítače v Azure můžou buď záviset na jejich hostiteli, aby předali přesný čas *(čas hostitele)* na virtuální počítač, nebo virtuální počítač může přímo získat čas z časového serveru nebo kombinaci obou. 

Na samostatném hardwaru linuxový operační systém čte pouze hodiny hostitelského hardwaru při startu. Poté jsou hodiny udržovány pomocí časovače přerušení v jádře Linuxu. V této konfiguraci se hodiny posunou v průběhu času. V novějších distribucích Linuxu v Azure můžou virtuální počítače používat zprostředkovatele VMICTimeSync, který je součástí integračních služeb Linuxu (LIS), k častějšímu dotazování na aktualizace hodin z hostitele.

Interakce virtuálního počítače s hostitelem může také ovlivnit hodiny. Během [údržby zachování paměti](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)se virtuální míchají po dobu až 30 sekund. Například před zahájením údržby se hodiny virtuálního času zobrazí v 10:00:00 a vydrží 28 sekund. Po obnovení virtuálního počítače hodiny na virtuálním počítači by stále zobrazit 10:00:00 AM, což by bylo 28 sekund off. Chcete-li opravit, služba VMICTimeSync monitoruje, co se děje na hostiteli a vyzve ke změnám, které mají nastat na virtuálních počítačích kompenzovat.

Bez synchronizace času funguje, hodiny na virtuálním počítači by hromadit chyby. Pokud existuje pouze jeden virtuální virtuální ms, efekt nemusí být významný, pokud zatížení vyžaduje vysoce přesné měření času. Ale ve většině případů máme několik propojených virtuálních počítačů, které používají čas ke sledování transakcí a čas musí být konzistentní v celém nasazení. Když se čas mezi virtuálními virtuálními stránkami liší, můžete vidět následující efekty:

- Ověřování se nezdaří. Protokoly zabezpečení, jako je protokol Kerberos nebo technologie závislé na certifikátu, spoléhají na to, že čas je konzistentní v systémech.
- Je velmi těžké zjistit, co se stalo v systému, pokud protokoly (nebo jiná data) nesouhlasí s časem. Stejná událost by vypadala, jako by k ní došlo v různých časech, což ztěžuje korelaci.
- Pokud jsou hodiny vypnuté, může být fakturace vypočtena nesprávně.



## <a name="configuration-options"></a>Možnosti konfigurace

Obecně existují tři způsoby konfigurace synchronizace času pro vaše virtuální počítače s Linuxem hostované v Azure:

- Výchozí konfigurace pro image Azure Marketplace používá čas NTP i host-time VMICTimeSync. 
- Pouze pro hostitele pomocí Aplikace VMICTimeSync.
- Použijte jiný externí časový server s nebo bez použití host-time VMICTimeSync.


### <a name="use-the-default"></a>Použít výchozí

Ve výchozím nastavení je většina inamů Azure Marketplace pro Linux nakonfigurovaná pro synchronizaci ze dvou zdrojů: 

- NTP jako primární, který získá čas ze serveru NTP. Například obrázky Ubuntu 16.04 LTS Marketplace používají **ntp.ubuntu.com**.
- Služba VMICTimeSync jako sekundární, slouží ke komunikaci času hostitele virtuálních hostitelů a provést opravy po pozastavení virtuálního účtu pro údržbu. Hostitelé Azure používají zařízení Stratum 1 vlastněná microsoftem, aby udrželi přesný čas.

V novějších distribucích Linuxu používá služba VMICTimeSync protokol přesnosti (PTP), ale dřívější distribuce nemusí podporovat PTP a vrátí se do NTP pro získání času od hostitele.

Chcete-li potvrdit, že se `ntpq -p` ntp synchronizuje správně, spusťte příkaz.

### <a name="host-only"></a>Pouze pro hostitele 

Vzhledem k tomu, že servery NTP, jako jsou time.windows.com a ntp.ubuntu.com, jsou veřejné, synchronizace času s nimi vyžaduje odesílání provozu přes Internet. Různá zpoždění paketů mohou negativně ovlivnit kvalitu synchronizace času. Odebrání ntp přepnutím na synchronizaci pouze pro hostitele může někdy zlepšit výsledky synchronizace času.

Přepnutí na synchronizaci času pouze pro hostitele má smysl, pokud dochází k problémům se synchronizací času pomocí výchozí konfigurace. Vyzkoušejte synchronizaci jedinou hostitele, abyste zjistili, jestli by to zlepšilo synchronizaci času na vašem virtuálním počítači. 

### <a name="external-time-server"></a>Externí časový server

Pokud máte specifické požadavky na synchronizaci času, je také možnost použití externích časových serverů. Externí časové servery mohou poskytovat určitý čas, což může být užitečné pro testovací scénáře, zajištění rovnoměrnosti času se stroji hostovanými v datových centrech jiných společností nebo zpracování přestupných sekund zvláštním způsobem.

Externí časový server můžete kombinovat se službou VMICTimeSync a poskytovat tak výsledky podobné výchozí konfiguraci. Kombinace externího časového serveru s VMICTimeSync je nejlepší volbou pro řešení problémů, které mohou být příčinou, když jsou virtuální moduly pozastaveny z důvodu údržby. 

## <a name="tools-and-resources"></a>Nástroje a prostředky

Existují některé základní příkazy pro kontrolu konfigurace synchronizace času. Dokumentace pro distribuci Linuxu bude mít další podrobnosti o nejlepším způsobu konfigurace synchronizace času pro tuto distribuci.

### <a name="integration-services"></a>Integrační služby

Zkontrolujte, zda je načtena integrační služba (hv_utils).

```bash
lsmod | grep hv_utils
```
Měli byste vidět něco podobného:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Podívejte se, jestli je spuštěn daemon integračních služeb Hyper-V.

```bash
ps -ef | grep hv
```

Měli byste vidět něco podobného:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>Zkontrolujte PTP

V novějších verzích Linuxu je jako součást zprostředkovatele VMICTimeSync k dispozici zdroj hodin přesného časového protokolu (PTP). Ve starších verzích Red Hat Enterprise Linux nebo CentOS 7.x lze [služby Linux Integration Services](https://github.com/LIS/lis-next) stáhnout a použít k instalaci aktualizovaného ovladače. Při použití PTP bude linuxové zařízení ve formě /dev/ptp*x*. 

Podívejte se, které zdroje hodin PTP jsou k dispozici.

```bash
ls /sys/class/ptp
```

V tomto příkladu je vrácená hodnota *ptp0*, takže ji používáme ke kontrole názvu hodin. Chcete-li zařízení ověřit, zkontrolujte název hodin.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

To by mělo vrátit **hyperv**.

### <a name="chrony"></a>chrony

Na Ubuntu 19.10 a novějších verzích, Red Hat Enterprise Linux a CentOS 7.x, je [chrony](https://chrony.tuxfamily.org/) nakonfigurován tak, aby používal zdrojové hodiny PTP. Namísto chrony, starší linuxové verze používají daemon Network Time Protocol (ntpd), který nepodporuje ptp zdroje. Chcete-li povolit PTP v těchto verzích, musí být chrony ručně nainstalována a konfigurována (v chrony.conf) pomocí následujícího kódu:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Další informace o ubuntu a ntp naleznete v [tématu Synchronizace času](https://help.ubuntu.com/lts/serverguide/NTP.html).

Další informace o aplikacích Red Hat a NTP naleznete v [tématu Konfigurace protokolu NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp). 

Další informace o chrony, naleznete [v tématu Použití chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony).

Pokud jsou zdroje chrony i TimeSync povoleny současně, můžete jeden označit jako **preferovaný**, který nastaví druhý zdroj jako zálohu. Vzhledem k tomu, že služby NTP neaktualizují hodiny pro velké zkosení s výjimkou po dlouhé době, VMICTimeSync obnoví hodiny z pozastavených událostí virtuálního modulu mnohem rychleji než samotné nástroje založené na NTP.

Ve výchozím nastavení chronyd zrychluje nebo zpomaluje systémové hodiny opravit jakýkoli časový posun. Pokud se drift stane příliš velký, chrony nedokáže opravit drift. Chcete-li tento `makestep` problém překonat, parametr v **/etc/chrony.conf** lze změnit tak, aby platnost timesync, pokud posun překročí zadanou prahovou hodnotu.

 ```bash
makestep 1.0 -1
```

Zde chrony vynutí aktualizaci času, pokud je posun větší než 1 sekunda. Chcete-li použít změny, restartujte službu chronyd:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>systemd 

Na SUSE a Ubuntu zprávy před 19.10, synchronizace času je konfigurován pomocí [systemd](https://www.freedesktop.org/wiki/Software/systemd/). Další informace o Ubuntu naleznete v [tématu Synchronizace času](https://help.ubuntu.com/lts/serverguide/NTP.html). Další informace o SUSE naleznete v části 4.5.8 v [suse linuxové matné verzi 12 SP3 poznámky k verzi](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## <a name="next-steps"></a>Další kroky

Další informace naleznete [v tématu Přesný čas pro systém Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time).


