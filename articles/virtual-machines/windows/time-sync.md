---
title: Čas synchronizace pro virtuální počítače s Windows v Azure | Dokumentace Microsoftu
description: Čas synchronizace pro virtuální počítače s Windows.
services: virtual-machines-windows
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/017/2018
ms.author: zarhoads
ms.openlocfilehash: ad5ceeef170e38bf6368c54894b20245d10b74ee
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578191"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Čas synchronizace pro virtuální počítače s Windows v Azure

Synchronizace času je důležitá pro zabezpečení a korelace událostí. Někdy se používá pro implementaci distribuovaných transakcí. Časové přesnosti mezi více počítačů se dosahuje prostřednictvím synchronizace. Synchronizace může být ovlivněno více věcí, včetně síťových přenosů mezi zdrojem času a počítačem načítání času a restartování počítače. 

Azure je nyní podporovaný službou infrastruktury s Windows serverem 2016. Windows Server 2016 obsahuje vylepšení algoritmy používané k opravte čas a podmínka vyhodnocena jako místní hodiny tak, aby synchronizovat s časem UTC.  Windows Server 2016 také vylepšené VMICTimeSync služba, která řídí, jak synchronizovat virtuální počítače s hostitelem pro přesný čas. Mezi vylepšení patří přesnější počátečního času na spuštění virtuálního počítače nebo obnovení virtuálního počítače a oprava čekací doba přerušení pro ukázky Windows čas (W32time) k dispozici. 


>[!NOTE]
>Získejte rychlý přehled služby Windows čas, podívejte se na to [vysoké úrovně video s přehledem](https://aka.ms/WS2016TimeVideo).
>
> Další informace najdete v tématu [přesný čas pro Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Přehled

Přesnost pro clock počítače je Erlenmeyerovy na to, jak blízko hodiny v počítači, je čas standardu koordinovaný univerzální čas (UTC). Čas UTC je definována nadnárodní vzorek přesné ten hodiny, které lze vypnout podle jedné sekundy v 300 let. Ale čtení UTC přímo vyžaduje speciální hardware. Místo toho časových serverů se synchronizují na čas UTC a jsou přístupné z jiných počítačů zajistit škálovatelnost a odolnost. Každý počítač má čas spuštění, které služba synchronizace ví, co čas serverů k používání a pravidelně kontroluje, pokud čas počítače je třeba opravit a v případě potřeby upraví čas. 

Azure hostitelé jsou synchronizovány do interní časových serverů Microsoft, které jejich nespěchejte ze zařízení vlastněných společností Microsoft vrstvě 1 s antény GPS. Virtuální počítače v Azure můžete buď závisí na jejich hostitele k předání přesný čas (*hostovat čas*) k virtuálnímu počítači nebo virtuálnímu počítači můžete přímo získat čas od času serveru nebo kombinaci obojího. 

Virtuální počítač interakce s hostitelem může také ovlivnit hodin. Během [Údržba pro zachování paměti](maintenance-and-updates.md#memory-preserving-maintenance), virtuální počítače jsou pozastaven po dobu až 30 sekund. Například před začátkem údržby ukazuje, 10:00:00: 00 hodiny virtuálního počítače a trvá 28 sekundách. Po návratu virtuálního počítače na hodiny na virtuálním počítači by stále zobrazit 10:00:00: 00, kterou by 28 sekundách vypnout. Aby správná, službu VMICTimeSync monitoruje co se děje v hostiteli a pokynů pro změny provést na virtuálních počítačích odpovídajícím způsobem upravit.

Služba VMICTimeSync funguje v režimu ukázkový nebo synchronizace a ovlivní pouze hodin dopředu. V režim vzorkování, který vyžaduje W32time běžet, služba VMICTimeSync každých 5 sekund dotazuje hostitele a poskytuje time – ukázky W32time. Přibližně každých 30 sekund, služba W32time získá nejnovější čas vzorek a použije ho k ovlivnění hosta hodiny. Režim synchronizace aktivuje, pokud byl obnoven hosta nebo hodiny guest drifts za hodiny hostiteli více než 5 sekund. V případech, kde je správně spuštěna služba W32time by měl druhém případě nikdy nemělo stát.

Bez synchronizace pracovní doby, hodiny na virtuálním počítači by accumulate chyby. Pokud existuje jenom jeden virtuální počítač, pokud úloha vyžaduje velmi přesné měřidlo času nemusí být významné efekt. Ale ve většině případů budeme mít více, propojených virtuálních počítačů, které používají ke sledování transakcí a času musí být konzistentní v rámci celého nasazení čas. Když je jiný čas mezi virtuálními počítači, je možné, že uvidíte v následujících efektů:

- Ověření se nezdaří. Protokoly zabezpečení, jako je protokol Kerberos nebo certifikát závislé na technologii využívají čas je konzistentní napříč systémy. 
- Je velmi obtížné zjistit, co se nestalo v systému protokolů (nebo jiná data) Nesouhlasím včas. Stejnou událost bude vypadat jako došlo k chybě, a v různých časech, provedete korelace obtížné.
- Pokud hodin je vypnuté, může nesprávně počítá fakturace.

Nejlepší výsledky pro nasazení Windows se dosahuje pomocí Windows serveru 2016 jako hostovaný operační systém, který zajišťuje, že používáte nejnovější vylepšení v synchronizaci času.

## <a name="configuration-options"></a>Možnosti konfigurace

Existují tři možnosti pro konfiguraci synchronizace času pro virtuální počítače Windows hostované v Azure:

- Časem hostitele a time.windows.com. Toto je výchozí konfigurace použitá při Image Azure Marketplace.
- Pouze hostitele.
- Použijte jiný, externí čas serveru s nebo bez použití časem hostitele.


### <a name="use-the-default"></a>Použít výchozí

Image virtuálních počítačů s operačním systémem Windows jsou ve výchozím nastavení nakonfigurované pro w32time dělat synchronizaci ze dvou zdrojů: 

- Poskytovatel NTP, který získává informace z time.windows.com.
- Službu VMICTimeSync používanou ke komunikaci časem hostitele do virtuálních počítačů a provádět opravy po virtuální počítač je pozastavený kvůli údržbě. Azure hostitele sloužit k udržení přesný čas zařízení vlastněných společností Microsoft vrstvu 1.

Služba W32Time přejete poskytovateli čas v následujícím pořadí podle priority: úroveň vrstvě, kořenové zpoždění, kořenové rozptýlení, časovým posunem. Ve většině případů w32time přejete time.windows.com hostitele protože time.windows.com sestavy nižší vrstvě. 

Pro počítače připojené k doméně samotné domény vytvoří časovou hierarchii synchronizace, ale kořenová doména doménové struktury je stále potřeba trvat dobu odněkud a následující aspekty, by uchovával stále platí.


### <a name="host-only"></a>Pouze pro hostitele 

Protože time.windows.com je veřejný server NTP, synchronizaci času s ním vyžaduje odesílání provozu přes internet, různou paketů zpoždění může negativně ovlivnit kvality synchronizace času. Odebrání time.windows.com přepnutím synchronizace jenom pro hostitele se někdy můžete zkrátit dobu synchronizovat výsledky.

Přepnutí na jen pro hostitele čas synchronizace umožňuje smysl, pokud dochází k synchronizaci problémy pomocí výchozí konfigurace. Vyzkoušejte si jen hostitel synchronizace zobrazíte Pokud, které by mohly zlepšit synchronizace času na virtuálním počítači. 

### <a name="external-time-server"></a>Externí čas serveru

Pokud máte požadavky na konkrétní čas synchronizace, je také možnost použití externí časových serverů. Externí čas servery mohou poskytovat určitou dobu, což může být užitečné pro scénáře testování, zajistit jednotnost čas s počítače hostované v datových centrech jiného subjektu než Microsoft nebo zpracování přestupné sekundy zvláštním způsobem.

Externí servery můžete zkombinovat s VMICTimeSync služby a VMICTimeProvider poskytnout výsledky, podobně jako výchozí konfiguraci. 

## <a name="check-your-configuration"></a>Zkontrolujte konfiguraci


Zaškrtněte, pokud zprostředkovatele času NTP konfigurován pro použití explicitní servery NTP (NTP) nebo synchronizace času domény (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Pokud virtuální počítač používá NTP, zobrazí se následující výstup:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Zobrazíte jaké čas serveru zprostředkovatele času NTP používá, zadejte příkazový řádek se zvýšenými oprávněními:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Pokud virtuální počítač používá výchozí, výstup bude vypadat takto:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Pokud chcete zobrazit čas zprostředkovatele se aktuálně používá.

```
w32tm /query /source
```


Zde je výstup, který je možné, uvidíte a co to znamenalo:
    
- **Time.Windows.com** – ve výchozím nastavení by w32time z time.windows.com získat čas. Kvality synchronizace času závisí na připojení k Internetu k němu a má vliv zpoždění paketů. Toto je obvykle výstup z výchozí nastavení.
- **Virtuální počítač IC doba synchronizace zprostředkovatele** – virtuální počítač se synchronizuje časem z hostitele. Obvykle jde výsledek, pokud můžete vyjádřit výslovný souhlas pro synchronizaci času pouze pro hostitele nebo server NTP není v tuto chvíli k dispozici. 
- *Váš server domény* – aktuální počítač je v doméně a doméně definuje časovou hierarchii synchronizace.
- *Některé server* -w32time explicitně nenakonfigurovali k načtení času z tohoto jiného serveru. Čas synchronizace kvalita závisí na kvalita čas serveru.
- **Místních systémových hodin** – hodiny nejsou synchronizovány. Získáte tento výstup w32time neměl dostatek času na spuštění po restartování nebo všechny zdroje nakonfigurovaném čase nejsou k dispozici.


## <a name="opt-in-for-host-only-time-sync"></a>Vyjádřit výslovný souhlas pro synchronizaci času pouze pro hostitele

Azure soustavně pracujeme na vylepšení synchronizace času na hostitelích a může zaručit, že všechny infrastruktury synchronizace času seřazena v datových centrech společnosti Microsoft. Pokud máte čas synchronizace problémy s výchozím nastavení, které dává přednost použití time.windows.com jako zdroj primární času, můžete použít následující příkazy k vyjádření výslovného souhlasu pro synchronizaci času pouze pro hostitele.

Poskytovateli VMIC označte jako povolené. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Zprostředkovatel NTP označte jako zakázané.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Restartujte službu w32time.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Virtuální počítače R2 a Windows Server 2012 

Windows Server 2012 a Windows Server 2012 R2 mít různé výchozí nastavení pro synchronizaci času. Služba w32time ve výchozím nastavení je nakonfigurovaný tak, aby upřednostňuje nízké režijní náklady na služby přes přesný čas. 

Pokud chcete přesunout vašeho systému Windows Server 2012 a 2012 R2 nasazení novější ponechte výchozí nastavení, které dáváte přednost přesný čas, můžete použít následující nastavení.

Aktualizovat w32time dotazování a aktualizaci intervalech tak, aby odpovídaly nastavení systému Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Pro w32time bude moct používat nové intervaly cyklického dotazování NtpServers označit jako jejich používání. Pokud servery jsou označena s maskou bitových parametrů 0x1, tento mechanismus, který by se mělo přepsat a služba w32time by místo toho použít SpecialPollInterval. Ujistěte se, že zadané servery NTP, které jsou buď pomocí příznaku 0x8 nebo bez příznaku vůbec:

Zkontrolujte, jaké příznaky jsou používány pro použité servery NTP.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Další postup

Níže jsou uvedeny odkazy na další podrobnosti o synchronizace času:

- [Windows čas nástroje a nastavení služby](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Vylepšení systému Windows Server 2016 ](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Přesný čas pro systém Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Hranice podpory ke konfiguraci služby Windows čas pro prostředí s vysokou přesností](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)


