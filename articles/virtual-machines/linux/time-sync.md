---
title: Čas synchronizace pro virtuální počítače se systémem Linux v Azure
description: Čas synchronizace pro virtuální počítače se systémem Linux.
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: 18c8570a8066985cab5263c4779787062dc32d75
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552639"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Čas synchronizace pro virtuální počítače se systémem Linux v Azure

Synchronizace času je důležitá pro korelaci zabezpečení a událostí. Někdy se používá pro implementaci distribuovaných transakcí. Časová přesnost mezi několika počítači se dosahuje prostřednictvím synchronizace. Synchronizace může být ovlivněna několika akcemi, včetně restartování a síťového provozu mezi zdrojem času a počítačem, který načítá čas. 

Azure je zajištěn infrastrukturou, na které běží Windows Server 2016. Systém Windows Server 2016 má vylepšené algoritmy používané pro správný čas a podmínky, že se místní hodiny synchronizují s časem UTC.  Funkce přesný čas systému Windows Server 2016 významně vylepšuje, jak služba VMICTimeSync řídí virtuálním počítačům s hostitelem pro přesný čas. Mezi vylepšení patří přesnější počáteční čas při spuštění virtuálního počítače nebo oprava latence pro obnovení virtuálního počítače a přerušení. 

> [!NOTE]
> Rychlý přehled služby Systémový čas najdete v tomto [videu s přehledem vysoké úrovně](https://aka.ms/WS2016TimeVideo).
>
> Další informace najdete v tématu [přesný čas pro Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Přehled

Přesnost na hodiny počítače je měřená v tom, jak je čas počítače v hodinách na standard UTC (Coordinated Universal Time). UTC je definovaná ve více národních vzorích přesných atomických hodin, které můžou být v 300 letech v rozmezí od jedné sekundy. Čtení UTC je ale přímo vyžaduje specializovaný hardware. Místo toho jsou časové servery synchronizované s časem UTC a jsou dostupné z jiných počítačů, abyste zajistili škálovatelnost a odolnost. Každý počítač má spuštěnou časovou synchronizační službu, která ví, jaké časové servery se mají použít, a pravidelně kontroluje, jestli je potřeba opravit hodiny počítače, a v případě potřeby upraví čas. 

Hostitelé Azure se synchronizují s interními servery Microsoftu, které přijímají čas od zařízení vrstvy 1 vlastněných společností Microsoft a s anténami GPS. Virtuální počítače v Azure můžou buď záviset na svém hostiteli, aby předávali přesný čas (*čas hostitele*) na virtuálním počítači nebo aby se virtuální počítač mohl dostat přímo na časový server, nebo kombinaci obou. 

Na samostatném hardwaru přečte operační systém Linux jenom hardwarové hodiny hostitele při spuštění. Pak se hodiny uchovávají pomocí časovače přerušení v jádře Linux. V této konfiguraci se hodiny posunou časem. V novějších distribucích pro Linux v Azure můžou virtuální počítače používat poskytovatele VMICTimeSync, který je zahrnutý v systému Linux Integration Services (LIS), a dotazovat se na aktualizace hodin od hostitele častěji.

Interakce virtuálních počítačů s hostitelem může také ovlivnit hodiny. Během [údržby paměti](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)se virtuální počítače pozastaví po dobu až 30 sekund. Například před zahájením údržby se hodiny na virtuálním počítači zobrazí 10:00:00 a trvá 28 sekund. Po obnovení virtuálního počítače se v hodinách na VIRTUÁLNÍm počítači stále zobrazuje 10:00:00, což bude 28 sekund vypnuto. Za tímto účelem služba VMICTimeSync monitoruje, co se děje na hostiteli, a vyzývá k tomu, aby se změny projevily na virtuálních počítačích, které se mají kompenzovat.

Při nesprávném provedení synchronizace by hodiny na virtuálním počítači nashromáždily chyby. Pokud existuje jenom jeden virtuální počítač, efekt nemusí být významný, pokud úloha nevyžaduje vysoce přesný Timekeeping. Ve většině případů máme několik propojených virtuálních počítačů, které používají čas ke sledování transakcí a dobu, po kterou je potřeba zajistit konzistenci v celém nasazení. Pokud je čas mezi virtuálními počítači jiný, můžete zobrazit následující důsledky:

- Ověřování se nezdaří. Protokoly zabezpečení, jako je Kerberos nebo technologie závislá na certifikátech, spoléhají na čas, který je konzistentní napříč systémy.
- Je velmi obtížné zjistit, co se v systému stalo, pokud protokoly (nebo jiná data) nesouhlasí včas. Stejná událost by vypadala v různou dobu, což by mohlo ztížit souvislost.
- Pokud je čas vypnutý, může se fakturace vypočítat nesprávně.



## <a name="configuration-options"></a>Možnosti konfigurace

Existují všeobecně tři způsoby konfigurace času synchronizace pro virtuální počítače se systémem Linux hostované v Azure:

- Výchozí konfigurace Azure Marketplace imagí používá čas NTP i čas hostitele VMICTimeSync. 
- Pouze hostitel s použitím VMICTimeSync.
- Použijte jiný externí časový server s nebo bez použití VMICTimeSync v čase hostitele.


### <a name="use-the-default"></a>Použít výchozí

Ve výchozím nastavení je většina Azure Marketplace imagí pro Linux nakonfigurovaná tak, aby se synchronizovala ze dvou zdrojů: 

- NTP jako primární, který získává čas od serveru NTP. Například Image Ubuntu 16,04 LTS na webu Marketplace používají **NTP.Ubuntu.com**.
- Služba VMICTimeSync jako sekundární, která slouží ke komunikaci času hostitele s virtuálními počítači a provádí opravy po pozastavení virtuálního počítače za účelem údržby. Hostitelé Azure používají zařízení vrstvy 1 vlastněná společností Microsoft k udržení přesného času.

V novějších distribucích systému Linux služba VMICTimeSync poskytuje zdroj hardwarových hodin protokolu PTP (Precision Time Protocol), ale starší distribuce nemusí tento zdroj hodin poskytnout a vrátí se zpět do NTP pro získání času od hostitele.

Pokud chcete potvrdit, že se NTP synchronizuje správně, spusťte `ntpq -p` příkaz.

### <a name="host-only"></a>Pouze hostitel 

Vzhledem k tomu, že servery NTP, jako jsou time.windows.com a ntp.ubuntu.com, jsou veřejné, synchronizace času s nimi vyžaduje posílání přenosů přes Internet. Proměnlivé zpoždění paketů může negativně ovlivnit kvalitu času synchronizace. Odebrání NTP pomocí přepnutí na synchronizaci pouze s hostitelem může někdy zlepšit výsledky synchronizace času.

Přechod na čas pouze hostitele má smysl, pokud zaznamenáte problémy s synchronizací pomocí výchozí konfigurace. Vyzkoušejte jenom synchronizaci hostitele, abyste zjistili, jestli by se vylepšila doba synchronizace na vašem VIRTUÁLNÍm počítači. 

### <a name="external-time-server"></a>Externí časový server

Pokud máte specifické požadavky na synchronizaci času, existuje také možnost použití externích časových serverů. Externí časové servery můžou poskytovat určitý čas, což může být užitečné pro testovací scénáře, což zajišťuje jednotnou časovou prodlevu u počítačů hostovaných v datových centrech jiných než Microsoftu, nebo speciálním způsobem zpracování přestupných sekund.

Můžete zkombinovat externí časový server se službou VMICTimeSync a poskytnout výsledky podobné výchozí konfiguraci. Kombinování externího časového serveru s VMICTimeSync je nejlepší možností pro řešení problémů, které mohou nastat při pozastavení virtuálních počítačů za účelem údržby. 

## <a name="tools-and-resources"></a>Nástroje a prostředky

K dispozici jsou některé základní příkazy pro kontrolu konfigurace synchronizace času. Dokumentace pro distribuci systému Linux bude obsahovat další podrobnosti o tom, jak nejlépe nakonfigurovat synchronizaci času pro tuto distribuci.

### <a name="integration-services"></a>Integrační služby

Zkontrolujte, zda je načtena integrační služba (hv_utils).

```bash
lsmod | grep hv_utils
```
Mělo by se zobrazit něco podobného:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Zkontrolujte, zda je spuštěn démon služby Hyper-V Integration Services.

```bash
ps -ef | grep hv
```

Mělo by se zobrazit něco podobného:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp-clock-source"></a>Kontrolovat zdroj hodin PTP

V novějších verzích systému Linux je zdroj hodin protokolu PTP (Precision Time Protocol) dostupný jako součást poskytovatele VMICTimeSync. Ve starších verzích Red Hat Enterprise Linux nebo CentOS 7. x můžete stáhnout a použít k instalaci aktualizovaného ovladače [služby Linux Integration Services](https://github.com/LIS/lis-next) . Když je k dispozici zdroj hodin PTP, bude zařízení se systémem Linux ve tvaru/dev/PTP *x*. 

Podívejte se, které zdroje s hodinami PTP jsou k dispozici.

```bash
ls /sys/class/ptp
```

V tomto příkladu je vrácená hodnota *ptp0*, takže ji používáme ke kontrole názvu hodin. Pokud chcete zařízení ověřit, zkontrolujte název hodin.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Mělo by se vrátit `hyperv` .

### <a name="chrony"></a>chrony

V Ubuntu 19,10 a novějších verzích Red Hat Enterprise Linux a CentOS 8. x je [Chrony](https://chrony.tuxfamily.org/) nakonfigurovaná tak, aby používala zdrojové hodiny PTP. Místo Chrony starší verze systému Linux používají ntpd (Network Time Protocol Daemon), který nepodporuje zdroje přes PTP. Aby bylo možné v těchto verzích povolit PTP, musí být Chrony ručně nainstalovaná a nakonfigurovaná (Chrony. conf) pomocí následujícího kódu:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Další informace o Ubuntu a NTP najdete v tématu [synchronizace času](https://ubuntu.com/server/docs/network-ntp).

Další informace o Red Hat a NTP najdete v tématu [Konfigurace NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP). 

Další informace o Chrony najdete v tématu [použití Chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony).

Pokud jsou současně povoleny současně Chrony i VMICTimeSync zdroje, můžete jeden označit jako **upřednostňovaný**, což nastaví druhý zdroj jako zálohu. Vzhledem k tomu, že služby NTP neaktualizují hodiny pro rozsáhlá zkosení s výjimkou po dlouhou dobu, VMICTimeSync obnoví hodiny z pozastavených událostí virtuálního počítače mnohem rychleji než samostatné nástroje založené na NTP.

Ve výchozím nastavení chronyd zrychluje nebo zpomaluje systémové hodiny, aby se opravila doba posunu. Pokud se posun přestane příliš velký, Chrony neodstraní posun. Pro překonání tohoto `makestep` parametru může být parametr v **/etc/Chrony.conf** změněn tak, aby se vynutila časová synchronizace, pokud posun překročí zadanou prahovou hodnotu.

 ```bash
makestep 1.0 -1
```

V tomto případě Chrony vynutí aktualizaci času, pokud je posun větší než 1 sekunda. Chcete-li použít změny, restartujte službu chronyd:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>systemd 

V SUSE a Ubuntu verzích před 19,10 se synchronizace času konfiguruje pomocí [systému](https://www.freedesktop.org/wiki/Software/systemd/). Další informace o Ubuntu najdete v tématu [synchronizace času](https://help.ubuntu.com/lts/serverguide/NTP.html). Další informace o SUSE najdete v části 4.5.8 v [poznámkách k verzi SUSE Linux Enterprise Server 12 SP3](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [přesný čas pro Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time).


