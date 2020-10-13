---
title: Čas synchronizace pro virtuální počítače s Windows v Azure
description: Čas synchronizace pro virtuální počítače s Windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 830bdd45be4b0365ac45bc3ea366b99a34882a4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871475"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Čas synchronizace pro virtuální počítače s Windows v Azure

Synchronizace času je důležitá pro korelaci zabezpečení a událostí. Někdy se používá pro implementaci distribuovaných transakcí. Časová přesnost mezi několika počítači se dosahuje prostřednictvím synchronizace. Synchronizace může být ovlivněna několika akcemi, včetně restartování a síťového provozu mezi zdrojem času a počítačem, který načítá čas. 

Azure je teď zálohovaný infrastrukturou, na které běží Windows Server 2016. Systém Windows Server 2016 má vylepšené algoritmy používané pro správný čas a podmínky, že se místní hodiny synchronizují s časem UTC.  Windows Server 2016 taky vylepšuje službu VMICTimeSync, která určuje, jak se virtuální počítače synchronizují s hostitelem přes přesný čas. Mezi vylepšení patří přesnější počáteční čas při spuštění virtuálního počítače nebo obnovení virtuálního počítače a oprava latence u vzorků poskytovaných v systému Windows Time (W32Time). 


>[!NOTE]
>Rychlý přehled služby Systémový čas najdete v tomto [videu s přehledem vysoké úrovně](https://aka.ms/WS2016TimeVideo).
>
> Další informace najdete v tématu [přesný čas pro Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Přehled

Přesnost na hodiny počítače je měřená v tom, jak je čas počítače v hodinách na standard UTC (Coordinated Universal Time). UTC je definovaná ve více národních vzorích přesných atomických hodin, které můžou být v 300 letech v rozmezí od jedné sekundy. Čtení UTC je ale přímo vyžaduje specializovaný hardware. Místo toho jsou časové servery synchronizované s časem UTC a jsou dostupné z jiných počítačů, abyste zajistili škálovatelnost a odolnost. Každý počítač má spuštěnou časovou synchronizační službu, která ví, jaké časové servery se mají použít, a pravidelně kontroluje, jestli je potřeba opravit hodiny počítače, a v případě potřeby upraví čas. 

Hostitelé Azure se synchronizují s interními servery Microsoftu, které přijímají čas od zařízení vrstvy 1 vlastněných společností Microsoft a s anténami GPS. Virtuální počítače v Azure můžou buď záviset na svém hostiteli, aby předávali přesný čas (*čas hostitele*) na virtuálním počítači nebo aby se virtuální počítač mohl dostat přímo na časový server, nebo kombinaci obou. 

Interakce virtuálních počítačů s hostitelem může také ovlivnit hodiny. Během [údržby paměti](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)se virtuální počítače pozastaví po dobu až 30 sekund. Například před zahájením údržby se hodiny na virtuálním počítači zobrazí 10:00:00 a trvá 28 sekund. Po obnovení virtuálního počítače se v hodinách na VIRTUÁLNÍm počítači stále zobrazuje 10:00:00, což bude 28 sekund vypnuto. Za tímto účelem služba VMICTimeSync monitoruje, co se děje na hostiteli, a vyzývá k tomu, aby se změny projevily na virtuálních počítačích, které se mají kompenzovat.

Služba VMICTimeSync funguje buď v režimu vzorkování, nebo v režimu synchronizace a bude mít vliv pouze na hodiny posunuté. V ukázkovém režimu, který vyžaduje, aby byl spuštěný W32Time, služba VMICTimeSync dotazuje hostitele každých 5 sekund a poskytuje časové ukázky pro službu W32Time. Přibližně každých 30 sekund služba W32Time bere nejnovější časový vzor a používá ho k ovlivnění času hosta. Režim synchronizace se aktivuje, pokud byl host obnovený, nebo pokud se hodiny hosta posunou více než 5 sekund za hodinami hostitele. V případech, kdy je služba W32Time správně spuštěná, by neměla nikdy nastat druhý případ.

Při nesprávném provedení synchronizace by hodiny na virtuálním počítači nashromáždily chyby. Pokud existuje jenom jeden virtuální počítač, efekt nemusí být významný, pokud úloha nevyžaduje vysoce přesný Timekeeping. Ve většině případů máme několik propojených virtuálních počítačů, které používají čas ke sledování transakcí a dobu, po kterou je potřeba zajistit konzistenci v celém nasazení. Pokud je čas mezi virtuálními počítači jiný, můžete zobrazit následující důsledky:

- Ověřování se nezdaří. Protokoly zabezpečení, jako je Kerberos nebo technologie závislá na certifikátech, spoléhají na čas, který je konzistentní napříč systémy. 
- Je velmi obtížné zjistit, co se v systému stalo, pokud protokoly (nebo jiná data) nesouhlasí včas. Stejná událost by vypadala v různou dobu, což by mohlo ztížit souvislost.
- Pokud je čas vypnutý, může se fakturace vypočítat nesprávně.

Nejlepší výsledky pro nasazení systému Windows jsou dosaženy pomocí systému Windows Server 2016 jako hostovaný operační systém, který zajišťuje, že můžete použít nejnovější vylepšení při synchronizaci času.

## <a name="configuration-options"></a>Možnosti konfigurace

Existují tři možnosti konfigurace času synchronizace pro virtuální počítače s Windows hostované v Azure:

- Čas hostitele a time.windows.com. Toto je výchozí konfigurace používaná v Azure Marketplacech imagí.
- Pouze hostitel.
- Použijte jiný externí časový server s nebo bez použití času hostitele.


### <a name="use-the-default"></a>Použít výchozí

Ve výchozím nastavení jsou image virtuálních počítačů s operačním systémem Windows nakonfigurované pro W32Time, aby se synchronizovaly ze dvou zdrojů: 

- Zprostředkovatel klienta NTP, který získává informace z time.windows.com.
- Služba VMICTimeSync, která slouží ke komunikaci času hostitele s virtuálními počítači a provádí opravy po pozastavení virtuálního počítače za účelem údržby. Hostitelé Azure používají zařízení vrstvy 1 vlastněná společností Microsoft k udržení přesného času.

Služba W32Time upřednostňuje poskytovatele času v následujícím pořadí: úroveň vrstvy, zpoždění kořene, odmocninu a časový posun. Ve většině případů by služba W32Time na virtuálním počítači Azure chtěla preferovat čas hostitele z důvodu vyhodnocení, že by to vedlo k porovnání obou zdrojů času. 

V případě počítačů připojených k doméně doména sama o sobě naváže časovou hierarchii synchronizace, ale kořen doménové struktury pořád potřebuje čas od někam a následující požadavky by pořád obsahovaly hodnotu true.


### <a name="host-only"></a>Pouze hostitel 

Vzhledem k tomu, že time.windows.com je veřejným serverem NTP, synchronizuje se s ním čas, který vyžaduje posílání přenosů přes Internet, a proměnlivé zpoždění paketů může negativně ovlivňovat kvalitu času synchronizace. Odebrání time.windows.com přepnutím na synchronizaci jenom s hostitelem může někdy vylepšit výsledky synchronizace času.

Přechod na čas pouze hostitele má smysl, pokud zaznamenáte problémy s synchronizací pomocí výchozí konfigurace. Vyzkoušejte jenom synchronizaci hostitele, abyste zjistili, jestli by se vylepšila doba synchronizace virtuálního počítače. 

### <a name="external-time-server"></a>Externí časový server

Pokud máte specifické požadavky na synchronizaci času, existuje také možnost použití externích časových serverů. Externí časové servery můžou poskytovat určitý čas, což může být užitečné pro testovací scénáře, což zajišťuje jednotnou časovou prodlevu u počítačů hostovaných v datových centrech jiných než Microsoftu, nebo speciálním způsobem zpracování přestupných sekund.

Můžete zkombinovat externí servery se službou VMICTimeSync a VMICTimeProvider, aby poskytovaly podobné výsledky jako výchozí konfigurace. 

## <a name="check-your-configuration"></a>Ověření konfigurace


Ověřte, jestli je zprostředkovatel času NTP nakonfigurovaný tak, aby používal explicitní servery NTP (NTP) nebo synchronizaci času v doméně (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Pokud virtuální počítač používá protokol NTP, zobrazí se následující výstup:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Chcete-li zjistit, jaký časový server poskytovatel času služby NTP používá, zadejte na příkazovém řádku se zvýšenými oprávněními následující příkaz:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Pokud virtuální počítač používá výchozí hodnotu, bude výstup vypadat takto:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Zjistíte, jaký čas aktuálně používá poskytovatel.

```
w32tm /query /source
```


Tady je výstup, který vidíte a co by to znamenalo:
    
- **time.Windows.com** – ve výchozí konfiguraci služba W32Time Získá čas od time.Windows.com. Kvalita synchronizace času závisí na připojení k Internetu a je ovlivněná zpožděními paketů. Toto je obvyklý výstup, který byste získali na fyzickém počítači.
- **Zprostředkovatel synchronizace pro vnitropodnikový čas virtuálního počítače**  – virtuální počítač se synchronizuje od hostitele. Toto je obvyklý výstup, který byste získali na virtuálním počítači běžícím na Azure. 
- *Váš doménový server* – aktuální počítač je v doméně a doména definuje časovou hierarchii synchronizace.
- *Některý jiný server* – služba W32Time byla explicitně nakonfigurovaná tak, aby získala čas od jiného serveru. Kvalita synchronizace času závisí na tomto časovém serveru.
- **Místní paměť CMOS** – hodiny jsou nesynchronizovány. Tento výstup můžete získat v případě, že služba W32Time nemá dostatek času na spuštění po restartování nebo když nejsou k dispozici všechny nakonfigurované zdroje času.


## <a name="opt-in-for-host-only-time-sync"></a>Výslovný souhlas jenom pro synchronizaci času hostitele

Azure neustále pracuje na vylepšení synchronizace času na hostitelích a může zaručit, že se veškerá infrastruktura synchronizace času společně umístěného v datacentrech vlastněných společností Microsoft. Pokud máte problémy s synchronizací s výchozím nastavením, které preferuje použití time.windows.com jako primárního zdroje času, můžete použít následující příkazy k tomu, abyste se mohli přihlásit k synchronizaci času jenom hostitele.

Označte poskytovatele VMIC jako povolený. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Označte poskytovatele klienta NTP jako zakázaný.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Restartujte službu W32Time.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Virtuální počítače s Windows Serverem 2012 a R2 

Systémy Windows Server 2012 a Windows Server 2012 R2 mají různá výchozí nastavení pro časovou synchronizaci. Služba W32Time ve výchozím nastavení je konfigurována způsobem, který preferuje nízké nároky služby na přesný čas. 

Pokud chcete přesunout nasazení Windows Serveru 2012 a 2012 R2, aby používala novější výchozí hodnoty, které upřednostňují přesný čas, můžete použít následující nastavení.

Aktualizujte intervaly dotazování a aktualizace W32Time tak, aby odpovídaly nastavení Windows serveru 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Aby služba W32Time mohla používat nové intervaly cyklického dotazování, musí být NtpServers označeny jako používané. Pokud jsou servery opatřené příponou 0x1 bitflag, které by tento mechanismus potlačily a služba W32Time místo toho použila SpecialPollInterval. Zajistěte, aby zadané servery NTP buď používaly příznak 0x8, nebo příznak bez příznaku.

Ověřte, jaké příznaky se používají pro používané servery NTP.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Další kroky

Níže jsou uvedeny odkazy na Další informace o čase synchronizace:

- [Nástroje a nastavení služby Systémový čas](/windows-server/networking/windows-time-service/windows-time-service-tools-and-settings)
- [Vylepšení Windows serveru 2016 ](/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Přesný čas pro Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time)
- [Podpora hranice pro konfiguraci služby Systémový čas pro prostředí s vysokou přesností](/windows-server/networking/windows-time-service/support-boundary)
