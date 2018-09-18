---
title: Čas synchronizace pro virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Čas synchronizace pro virtuální počítače s Linuxem.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: f79b1d4c1afc4d5a516a46a9bf6cb1790034b279
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986298"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Čas synchronizace pro virtuální počítače s Linuxem v Azure

Synchronizace času je důležitá pro zabezpečení a korelace událostí. Někdy se používá pro implementaci distribuovaných transakcí. Časové přesnosti mezi více počítačů se dosahuje prostřednictvím synchronizace. Synchronizace může být ovlivněno více věcí, včetně síťových přenosů mezi zdrojem času a počítačem načítání času a restartování počítače. 

Azure je zajištěná infrastruktury s Windows serverem 2016. Windows Server 2016 obsahuje vylepšení algoritmy používané k opravte čas a podmínka vyhodnocena jako místní hodiny tak, aby synchronizovat s časem UTC.  Funkce systému Windows Server 2016 přesnému času výrazně zlepšil jak VMICTimeSync službu, která řídí virtuální počítače s hostitelem pro přesný čas. Mezi vylepšení patří přesnější počátečního času na spuštění virtuálního počítače nebo obnovení virtuálního počítače a oprava čekací doba přerušení. 

>[!NOTE]
>Získejte rychlý přehled služby Windows čas, podívejte se na to [vysoké úrovně video s přehledem](https://aka.ms/WS2016TimeVideo).
>
> Další informace najdete v tématu [přesný čas pro Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Přehled

Přesnost pro clock počítače je Erlenmeyerovy na to, jak blízko hodiny v počítači, je čas standardu koordinovaný univerzální čas (UTC). Čas UTC je definována nadnárodní vzorek přesné ten hodiny, které lze vypnout podle jedné sekundy v 300 let. Ale čtení UTC přímo vyžaduje speciální hardware. Místo toho časových serverů se synchronizují na čas UTC a jsou přístupné z jiných počítačů zajistit škálovatelnost a odolnost. Každý počítač má čas spuštění, které služba synchronizace ví, co čas serverů k používání a pravidelně kontroluje, pokud čas počítače je třeba opravit a v případě potřeby upraví čas. 

Azure hostitelé jsou synchronizovány do interní časových serverů Microsoft, které jejich nespěchejte ze zařízení vlastněných společností Microsoft vrstvě 1 s antény GPS. Virtuální počítače v Azure můžete buď závisí na jejich hostitele k předání přesný čas (*hostovat čas*) k virtuálnímu počítači nebo virtuálnímu počítači můžete přímo získat čas od času serveru nebo kombinaci obojího. 

Na samostatné hardwaru, operačních systémů Linux četl pouze hostitelský hardware hodiny na spuštění. Potom se udržuje hodin pomocí časovače přerušení v linuxového jádra. V této konfiguraci bude odchylek hodiny v čase. V novější distribuce Linuxu v Azure můžete použít virtuální počítače poskytovateli VMICTimeSync součástí integrační služby Linuxu (LIS), k dotazování na aktualizace hodin od hostitele častěji.

Virtuální počítač interakce s hostitelem může také ovlivnit hodin. Během [Údržba pro zachování paměti](maintenance-and-updates.md#memory-preserving-maintenance), virtuální počítače jsou pozastaven po dobu až 30 sekund. Například před začátkem údržby ukazuje, 10:00:00: 00 hodiny virtuálního počítače a trvá 28 sekundách. Po návratu virtuálního počítače na hodiny na virtuálním počítači by stále zobrazit 10:00:00: 00, kterou by 28 sekundách vypnout. Aby správná, službu VMICTimeSync monitoruje co se děje v hostiteli a pokynů pro změny provést na virtuálních počítačích odpovídajícím způsobem upravit.

Bez synchronizace pracovní doby, hodiny na virtuálním počítači by accumulate chyby. Pokud existuje jenom jeden virtuální počítač, pokud úloha vyžaduje velmi přesné měřidlo času nemusí být významné efekt. Ale ve většině případů budeme mít více, propojených virtuálních počítačů, které používají ke sledování transakcí a času musí být konzistentní v rámci celého nasazení čas. Když je jiný čas mezi virtuálními počítači, je možné, že uvidíte v následujících efektů:

- Protokoly zabezpečení, jako je protokol Kerberos nebo certifikát závislé na technologii využívají čas je konzistentní napříč systémy. 
- Je velmi obtížné zjistit, co se nestalo v systému protokolů (nebo jiná data) Nesouhlasím včas. Stejnou událost bude vypadat jako došlo k chybě, a v různých časech, provedete korelace obtížné.
- Pokud hodin je vypnuté, může nesprávně počítá fakturace.



## <a name="configuration-options"></a>Možnosti konfigurace

Existují obecně tři způsoby, jak nakonfigurovat synchronizaci pro virtuální počítače Linux hostované v Azure:

- Výchozí konfigurace pro Image Azure Marketplace používá času NTP a VMICTimeSync hostitele time. 
- Hostitel jen pomocí VMICTimeSync.
- Použijte jiný, externí čas serveru s nebo bez použití VMICTimeSync hostitele time.


### <a name="use-the-default"></a>Použít výchozí

Ve výchozím nastavení, se většina imagí Azure Marketplace pro Linux nakonfigurovaný k synchronizaci ze dvou zdrojů: 

- NTP jako primární, která získá dobu ze serveru NTP. Například Ubuntu 16.04 LTS Marketplace Image použijte **ntp.ubuntu.com**.
- Služba VMICTimeSync jako sekundární, používá ke komunikaci časem hostitele do virtuálních počítačů a provádět opravy po virtuální počítač je pozastavený kvůli údržbě. Azure hostitele sloužit k udržení přesný čas zařízení vlastněných společností Microsoft vrstvu 1.

V novější distribuce Linuxu služba VMICTimeSync používá protokol čas přesnosti (PTP), ale nemusí podporovat PTP a bude fall obnovení NTP pro získání čas z hostitele starší distribuce.

Pokud chcete potvrdit, správně synchronizuje NTP, spusťte `ntpq -p` příkazu.

### <a name="host-only"></a>Pouze pro hostitele 

Vzhledem k tomu, že servery NTP, které jako time.windows.com a ntp.ubuntu.com jsou veřejné, synchronizuje čas s nimi vyžaduje odesílání provozu přes internet. Různé paketů zpoždění může negativně ovlivnit kvality synchronizace času. Odebrání NTP přepnutím synchronizace jenom pro hostitele se někdy můžete zkrátit dobu synchronizovat výsledky.

Přepnutí na jen pro hostitele čas synchronizace umožňuje smysl, pokud dochází k synchronizaci problémy pomocí výchozí konfigurace. Vyzkoušejte si jen pro hostitele synchronizace zobrazíte, pokud, které by mohly zlepšit synchronizace času na vašem virtuálním počítači. 

### <a name="external-time-server"></a>Externí čas serveru

Pokud máte požadavky na konkrétní čas synchronizace, je také možnost použití externí časových serverů. Externí čas servery mohou poskytovat určitou dobu, což může být užitečné pro scénáře testování, zajistit jednotnost čas s počítače hostované v datových centrech jiného subjektu než Microsoft nebo zpracování přestupné sekundy zvláštním způsobem.

Externí čas serveru můžete zkombinovat s služba VMICTimeSync poskytnout výsledky, podobně jako výchozí konfiguraci. Kombinování externí čas serveru s VMICTimeSync je nejvhodnější volbou pro řešení problémů, které může být příčinou, když virtuální počítače jsou pozastavena kvůli údržbě. 

## <a name="tools-and-resources"></a>Nástroje a prostředky

Existují některé základní příkazy pro kontrolu konfigurace synchronizace času. Dokumentaci pro distribuce Linuxu, bude mít další podrobnosti na nejlepší způsob, jak nakonfigurovat synchronizaci času pro příslušné distribuci.

### <a name="integration-services"></a>Integrační služby

Zkontrolujte, zda je načtena služba pro integraci (hv_utils).

```bash
lsmod | grep hv_utils
```
By měl vypadat nějak takto:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Zobrazit, zda je spuštěn démon Hyper-V integrační služby.

```bash
ps -ef | grep hv
```

By měl vypadat nějak takto:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>Vyhledat PTP

V novějších verzích systému Linux, zdroj hodin přesnost čas PTP (Protocol) je k dispozici jako součást VMICTimeSync zprostředkovatele. Ke starším verzím rozhraní Red Hat Enterprise Linux nebo CentOS 7.x [integrační služby Linuxu](https://github.com/LIS/lis-next) je možné stáhnout a použít k instalaci aktualizované ovladače. Při použití PTP Linuxovému zařízení bude mít formuláře/dev/ptp*x*. 

Zjistěte, jaké zdroje PTP hodiny jsou k dispozici.

```bash
ls /sys/class/ptp
```

V tomto příkladu je vrácená hodnota *ptp0*, takže použijeme, který Pokud chcete zkontrolovat název hodin. Pokud chcete ověřit zařízení, zkontrolujte název hodiny.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Měla by se vrátit **hyperv**.

### <a name="chrony"></a>chrony

Na Red Hat Enterprise Linux a CentOS 7.x, [chrony](https://chrony.tuxfamily.org/) nakonfigurován na použití hodin PTP zdroje. Démon Network Time Protocol (ntpd) nepodporuje PTP zdroje, takže pomocí **chronyd** se doporučuje. Pokud chcete povolit PTP, aktualizujte **chrony.conf**.

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Další informace o Red Hat a NTP, naleznete v tématu [konfigurace NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp). 

Další informace o chrony najdete v tématu [pomocí chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony).

Pokud chrony a TimeSync zdroje jsou povolené současně, můžete označit jako jeden **raději** které nastaví jiný zdroj jako záložní. Protože NTP služby neaktualizují účtovat poplatky za velké odchylek s výjimkou po dlouhou dobu, VMICTimeSync obnoví hodiny z pozastaveného virtuálního počítače událostí mnohem rychleji než nástrojů založených na protokolu NTP samostatně.


### <a name="systemd"></a>systemd 

Na Ubuntu nebo SUSE čas sync je nakonfigurovaná pomocí [systemd](https://www.freedesktop.org/wiki/Software/systemd/). Další informace o Ubuntu, naleznete v tématu [synchronizaci času](https://help.ubuntu.com/lts/serverguide/NTP.html). Další informace o operačním systémem SUSE, naleznete v tématu v části 4.5.8 [operačním systémem SUSE Linux Enterprise Server 12 SP3 zpráva k vydání verze](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).



## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [přesný čas pro Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time).


