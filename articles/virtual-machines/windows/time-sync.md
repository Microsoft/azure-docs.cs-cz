---
title: Synchronizace času pro virtuální počítače s Windows v Azure
description: Synchronizace času pro virtuální počítače s Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: dd2ae2159c43da6a049d67cae739f111eba682c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74534455"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Synchronizace času pro virtuální počítače s Windows v Azure

Synchronizace času je důležitá pro zabezpečení a korelaci událostí. Někdy se používá pro implementaci distribuovaných transakcí. Časové přesnosti mezi více počítačovými systémy je dosaženo prostřednictvím synchronizace. Synchronizace může být ovlivněna více věcmi, včetně restartování a síťového provozu mezi zdrojem času a počítačem, který načítá čas. 

Azure teď podporuje infrastruktura se systémem Windows Server 2016. Windows Server 2016 vylepšil algoritmy používané ke spravení času a stavu místních hodin pro synchronizaci s utc.  Windows Server 2016 také vylepšil službu VMICTimeSync, která určuje, jak se virtuální ms synchronizují s hostitelem pro přesný čas. Vylepšení zahrnují přesnější počáteční čas na spuštění virtuálního počítači nebo obnovení virtuálního počítači a opravu latence přerušení pro ukázky poskytované pro čas Windows (W32time). 


>[!NOTE]
>Rychlý přehled služby Systémový čas naleznete v tomto [video přehledu na vysoké úrovni](https://aka.ms/WS2016TimeVideo).
>
> Další informace naleznete [v tématu Přesný čas pro systém Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Přehled

Přesnost počítačových hodin je měřena podle toho, jak blízko jsou hodiny počítače k časovému standardu Koordinovaný světový čas (UTC). UTC je definována nadnárodním vzorkem přesných atomových hodin, které mohou být vypnuty pouze o jednu sekundu za 300 let. Čtení UTC však přímo vyžaduje specializovaný hardware. Místo toho časové servery jsou synchronizovány s UTC a jsou přístupné z jiných počítačů za účelem zajištění škálovatelnosti a robustnosti. Každý počítač má spuštěnou službu synchronizace času, která ví, jaké časové servery použít, a pravidelně kontroluje, zda je třeba opravit hodiny počítače, a v případě potřeby upraví čas. 

Hostitelé Azure jsou synchronizováni s interními časovými servery Microsoftu, které si berou čas ze zařízení Stratum 1 vlastněných microsoftem s anténami GPS. Virtuální počítače v Azure můžou buď záviset na jejich hostiteli, aby předali přesný čas *(čas hostitele)* na virtuální počítač, nebo virtuální počítač může přímo získat čas z časového serveru nebo kombinaci obou. 

Interakce virtuálního počítače s hostitelem může také ovlivnit hodiny. Během [údržby zachování paměti](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)se virtuální míchají po dobu až 30 sekund. Například před zahájením údržby se hodiny virtuálního času zobrazí v 10:00:00 a vydrží 28 sekund. Po obnovení virtuálního počítače hodiny na virtuálním počítači by stále zobrazit 10:00:00 AM, což by bylo 28 sekund off. Chcete-li opravit, služba VMICTimeSync monitoruje, co se děje na hostiteli a vyzve ke změnám, které mají nastat na virtuálních počítačích kompenzovat.

Služba VMICTimeSync pracuje v ukázkovém nebo synchronizačním režimu a ovlivní pouze hodiny vpřed. V ukázkovém režimu, který vyžaduje spuštění w32času, služba VMICTimeSync každých 5 sekund provede dotazování hostitele a poskytuje vzorky času w32time. Přibližně každých 30 sekund služba W32time odebere poslední ukázku času a použije ji k ovlivnění hodin hosta. Režim synchronizace se aktivuje, pokud byl host obnoven nebo pokud hodiny hosta uběhnou více než 5 sekund za hodinami hostitele. V případech, kdy je služba W32time správně spuštěna, by se tento případ nikdy neměl stát.

Bez synchronizace času funguje, hodiny na virtuálním počítači by hromadit chyby. Pokud existuje pouze jeden virtuální virtuální ms, efekt nemusí být významný, pokud zatížení vyžaduje vysoce přesné měření času. Ale ve většině případů máme několik propojených virtuálních počítačů, které používají čas ke sledování transakcí a čas musí být konzistentní v celém nasazení. Když se čas mezi virtuálními virtuálními stránkami liší, můžete vidět následující efekty:

- Ověřování se nezdaří. Protokoly zabezpečení, jako je protokol Kerberos nebo technologie závislé na certifikátu, spoléhají na to, že čas je konzistentní v systémech. 
- Je velmi těžké zjistit, co se stalo v systému, pokud protokoly (nebo jiná data) nesouhlasí s časem. Stejná událost by vypadala, jako by k ní došlo v různých časech, což ztěžuje korelaci.
- Pokud jsou hodiny vypnuté, může být fakturace vypočtena nesprávně.

Nejlepších výsledků pro nasazení systému Windows lze dosáhnout použitím systému Windows Server 2016 jako hostovaného operačního systému, který zajišťuje, že můžete používat nejnovější vylepšení synchronizace času.

## <a name="configuration-options"></a>Možnosti konfigurace

Existují tři možnosti konfigurace synchronizace času pro vaše virtuální počítače s Windows hostované v Azure:

- Čas hostitele a time.windows.com. Toto je výchozí konfigurace používaná v ibi Azure Marketplace.
- Pouze pro hostitele.
- Použijte jiný externí časový server s časem hostitele nebo bez něj.


### <a name="use-the-default"></a>Použít výchozí

Ve výchozím nastavení jsou bitové kopie virtuálního počítače operačního systému Windows nakonfigurovány pro w32time pro synchronizaci ze dvou zdrojů: 

- Zprostředkovatel ntpclient, který získává informace z time.windows.com.
- Služba VMICTimeSync, která slouží ke komunikaci času hostitele virtuálním kvitům a provádět opravy po pozastavení virtuálního účtu z důvodu údržby. Hostitelé Azure používají zařízení Stratum 1 vlastněná microsoftem, aby udrželi přesný čas.

w32time preferuje poskytovatele času v následujícím pořadí podle priority: úroveň vrstvy, zpoždění kořene, disperze kořene, časový posun. Ve většině případů w32time by raději time.windows.com hostitele, protože time.windows.com hlásí nižší vrstvu. 

U počítačů spojených s doménou doména sama vytváří hierarchii synchronizace času, ale kořenová doménová struktura stále potřebuje odněkud nějakou dobu trvat a následující aspekty by stále platily.


### <a name="host-only"></a>Pouze pro hostitele 

Vzhledem k tomu, že time.windows.com je veřejný server NTP, synchronizace času s ním vyžaduje odesílání provozu přes internet, může různá zpoždění paketů negativně ovlivnit kvalitu synchronizace času. Odebrání time.windows.com přepnutím na synchronizaci pouze pro hostitele může někdy zlepšit výsledky synchronizace času.

Přepnutí na synchronizaci času pouze pro hostitele má smysl, pokud dochází k problémům se synchronizací času pomocí výchozí konfigurace. Vyzkoušejte synchronizaci jedinou hostitele, abyste zjistili, jestli by to zlepšilo synchronizaci času na virtuálním počítači. 

### <a name="external-time-server"></a>Externí časový server

Pokud máte specifické požadavky na synchronizaci času, je také možnost použití externích časových serverů. Externí časové servery mohou poskytovat určitý čas, což může být užitečné pro testovací scénáře, zajištění rovnoměrnosti času se stroji hostovanými v datových centrech jiných společností nebo zpracování přestupných sekund zvláštním způsobem.

Externí servery můžete kombinovat se službou VMICTimeSync a službou VMICTimeProvider a poskytovat výsledky podobné výchozí konfiguraci. 

## <a name="check-your-configuration"></a>Zkontrolujte konfiguraci


Zkontrolujte, zda je poskytovatel času ntpklienta nakonfigurován tak, aby používal explicitní servery NTP nebo synchronizaci času domény (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Pokud virtuální virtuální byl pomocí NTP, zobrazí se následující výstup:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Chcete-li zjistit, jaký časový server používá zprostředkovatel času ntpklienta, na příkazovém řádku se zvýšenými oprávněními:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Pokud virtuální virtuální jazyk používá výchozí, výstup bude vypadat takto:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Chcete-li zjistit, jaký zprostředkovatel času se aktuálně používá.

```
w32tm /query /source
```


Zde je výstup, který byste mohli vidět, a co by to znamenalo:
    
- **time.windows.com** - ve výchozí konfiguraci w32time získá čas od time.windows.com. Kvalita synchronizace času závisí na připojení k internetu a je ovlivněna zpožděním paketů. Toto je obvyklý výstup z výchozího nastavení.
- **Zprostředkovatel synchronizace mezivirtuálního měničového** virtuálního měna – virtuální modul synchronizuje čas z hostitele. To je obvykle výsledek, pokud se přihlásíte k synchronizaci času pouze hostitele nebo ntpserver není v současné době k dispozici. 
- *Váš doménový server* - aktuální počítač je v doméně a doména definuje hierarchii synchronizace času.
- *Některý jiný server* - w32time byl explicitně nakonfigurován tak, aby získal čas z tohoto jiného serveru. Kvalita synchronizace času závisí na této kvalitě časového serveru.
- **Místní Hodiny CMOS** - hodiny jsou nesynchronizované. Tento výstup můžete získat, pokud w32čas neměl dostatek času na spuštění po restartu nebo pokud nejsou k dispozici všechny nakonfigurované zdroje času.


## <a name="opt-in-for-host-only-time-sync"></a>Přihlášení pro synchronizaci času pouze pro hostitele

Azure neustále pracuje na zlepšování synchronizace času na hostitelích a může zaručit, že infrastruktura synchronizace po celou dobu se bude přiřazovat v datových centrech vlastněných Microsoftem. Pokud máte problémy se synchronizací času s výchozím nastavením, které upřednostňuje použití time.windows.com jako primárního zdroje času, můžete použít následující příkazy k přihlášení k synchronizaci času pouze hostitele.

Označte zprostředkovatele VMIC jako povoleného. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Označte zprostředkovatele ntpklienta jako zakázaného.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Restartujte službu w32time.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Virtuální servery Windows Server 2012 a R2 

Windows Server 2012 a Windows Server 2012 R2 mají různá výchozí nastavení pro synchronizaci času. W32time ve výchozím nastavení je nakonfigurován způsobem, který upřednostňuje nízkou režii služby před přesným časem. 

Pokud chcete přesunout nasazení windows serveru 2012 a 2012 R2 tak, aby používala novější výchozí hodnoty, které preferují přesný čas, můžete použít následující nastavení.

Aktualizujte intervaly dotazování a aktualizace w32time tak, aby odpovídaly nastavení systému Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Aby w32čas mohli používat nové intervaly dotazování, ntpservers být označeny jako jejich použití. Pokud jsou servery anotovány s maskou 0x1 bitflag, které by přepsat tento mechanismus a w32time by použít SpecialPollInterval místo. Ujistěte se, že zadané servery NTP používají příznak 0x8 nebo vůbec žádný příznak:

Zkontrolujte, jaké příznaky se používají pro použité servery NTP.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Další kroky

Níže jsou uvedeny odkazy na další podrobnosti o synchronizaci času:

- [Nástroje a nastavení služby Systémový čas](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Vylepšení Systému Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Přesný čas pro Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Podpora hranice pro konfiguraci služby Systémový čas pro prostředí s vysokou přesností](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)


