---
title: Řešení potíží s certifikacem virtuálních počítačů pro Azure Marketplace
description: Řešení běžných problémů souvisejících s testováním a Certifikováním imagí virtuálních počítačů pro Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: mathapli
ms.author: mathapli
ms.date: 01/18/2021
ms.openlocfilehash: adcd91d58b3bb5fde3ffa81c828c58d4b6db48d4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721153"
---
# <a name="troubleshoot-virtual-machine-certification"></a>Řešení potíží s certifikací virtuálního počítače

Když publikujete image virtuálního počítače do Azure Marketplace, tým Azure je ověří, aby zajistil, že je spustitelný, zabezpečený a kompatibilní s Azure. Pokud vaše image virtuálního počítače neprojde jakýmkoli testem vysoce kvalitních testů, nebude se publikovat. Zobrazí se chybová zpráva s popisem problému.

Tento článek vysvětluje běžné chybové zprávy během publikování imagí virtuálních počítačů společně se souvisejícími řešeními.

> [!NOTE]
> Pokud máte dotazy k tomuto článku nebo návrhy na vylepšení, obraťte se na [podporu partnerského centra](https://aka.ms/marketplacepublishersupport).

## <a name="vm-extension-failure"></a>Selhání rozšíření virtuálního počítače

Zkontrolujte, jestli vaše image podporuje rozšíření virtuálních počítačů.

Povolení rozšíření virtuálních počítačů:

1. Vyberte virtuální počítač se systémem Linux.
1. Přejít na **nastavení diagnostiky**.
1. Pomocí aktualizace **účtu úložiště** povolte základní matice.
1. Vyberte **Uložit**.

   ![Snímek obrazovky, který ukazuje, jak povolit monitorování na úrovni hosta.](./media/create-vm/vm-certification-issues-solutions-1.png)

Ověření, jestli jsou rozšíření virtuálních počítačů správně aktivované:

1. Na virtuálním počítači vyberte kartu **rozšíření virtuálního počítače** a pak ověřte stav **rozšíření Linux Diagnostics**.
1. Ověřte stav zřizování.

   - Pokud je stav *zřizování úspěšné*, předává testovací případ rozšíření.  
   - Pokud se stav *zřizování nezdařil*, testový případ rozšíření se nezdařil a je nutné nastavit zpřísněný příznak.

   ![Snímek obrazovky zobrazující, že zřizování bylo úspěšné](./media/create-vm/vm-certification-issues-solutions-2.png)

   Pokud se rozšíření virtuálního počítače nepovede, přečtěte si téma [použití diagnostického rozšíření systému Linux k monitorování metrik a protokolů](../virtual-machines/extensions/diagnostics-linux.md) , které chcete povolit. Pokud nechcete, aby bylo rozšíření virtuálního počítače povolené, obraťte se na tým podpory a požádejte ho, aby ho zakázal.

## <a name="vm-provisioning-issue"></a>Problém zřizování virtuálních počítačů

Ujistěte se, že jste před odesláním nabídky ověřili, že jste provedli proces zřizování virtuálních počítačů striktně. Pokud chcete zobrazit formát JSON pro zřizování virtuálního počítače, přečtěte si téma [test image virtuálního počítače](azure-vm-image-test.md).

Problémy zřizování můžou zahrnovat tyto scénáře selhání:

|Scenario|Chyba|Důvod|Řešení|
|---|---|---|---|
|1|Neplatný virtuální pevný disk (VHD)|Pokud je zadaná hodnota souboru cookie v zápatí VHD nesprávná, bude virtuální pevný disk považován za neplatný.|Znovu vytvořte bitovou kopii a odešlete žádost.|
|2|Neplatný typ objektu BLOB|Zřizování virtuálního počítače se nezdařilo, protože použitý blok je typ objektu BLOB místo typu stránky.|Znovu vytvořte bitovou kopii a odešlete žádost.|
|3|Časový limit zřizování nebo není správně zobecněný|Došlo k potížím s generalizací virtuálních počítačů.|Znovu vytvořte bitovou kopii pomocí generalizace a odešlete žádost.|
|

> [!NOTE]
> Další informace o generalizaci virtuálních počítačů najdete v těchto tématech:
> - [Dokumentace pro Linux](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Dokumentace k Windows](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="vhd-specifications"></a>Specifikace VHD

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix soubor cookie a další specifikace VHD

Řetězec ' conectix ' je součástí specifikace VHD. Je definovaný jako soubor cookie o velikosti 8 bajtů v zápatí VHD, který identifikuje tvůrce souboru. Tento soubor cookie mají všechny soubory VHD vytvořené Microsoftem.

Objekt BLOB ve formátu VHD by měl mít formát 512-byte v tomto formátu:

|Pole zápatí pevného disku|Velikost (v bajtech)|
|---|---|
Soubor|8
Funkce|4
Verze formátu souboru|4
Posun dat|8
Časové razítko|4
Aplikace autora|4
Verze Tvůrce|4
Tvůrce hostitelského operačního systému|4
Původní velikost|8
Aktuální velikost|8
Geometrie disku|4
Typ disku|4
Kontrolní součet|4
Jedinečné ID|16
Uložený stav|1
Vyhrazené|427
|

### <a name="vhd-specifications"></a>Specifikace VHD

Aby se zajistilo hladké prostředí pro publikování, ujistěte se, že virtuální pevný disk splňuje následující kritéria:

- Soubor cookie obsahuje řetězec "conectix".
- Typ disku je pevný.
- Virtuální pevný disk má velikost alespoň 20 MB.
- Virtuální pevný disk je zarovnán. Virtuální velikost musí být násobkem 1 MB.
- Délka objektu BLOB VHD se rovná virtuální velikosti a délce zápatí VHD (512).

Stáhněte si [specifikaci VHD](https://www.microsoft.com/download/details.aspx?id=23850).

## <a name="software-compliance-for-windows"></a>Kompatibilita softwaru pro Windows

Pokud se vaše žádost o image Windows zamítla kvůli problému s kompatibilitou softwaru, možná jste vytvořili image Windows s nainstalovanou instancí SQL Server. Místo toho musíte z Azure Marketplace použít příslušnou základní image verze SQL Server.

Nevytvářejte vlastní image Windows s nainstalovanou SQL Server. Použijte schválené základní image SQL Server (Enterprise/Standard/Web) z Azure Marketplace.

Pokud se pokoušíte nainstalovat sadu Visual Studio nebo jakýkoli produkt licencovaný pro Office, obraťte se na tým podpory, který vám poskytne předchozí schválení.

Další informace o výběru schválené základní třídy najdete v tématu [Vytvoření virtuálního počítače ze schválené základny](azure-vm-create-using-approved-base.md).

## <a name="toolkit-test-case-execution-failed"></a>Spuštění testovacího případu sady nástrojů se nezdařilo.

Sada nástrojů Microsoft Certification Toolkit vám může pomáhat při spouštění testovacích případů a ověření, že je váš virtuální pevný disk nebo Image kompatibilní s prostředím Azure.

Stáhněte si [sadu nástrojů Microsoft Certification Toolkit](azure-vm-image-test.md).

### <a name="linux-test-cases"></a>Testovací případy pro Linux

V následující tabulce jsou uvedeny testovací případy pro Linux, které sada nástrojů spustí. Ověření testu je uvedeno v popisu.

|Scenario|Testovací případ|Popis|
|---|---|---|
|1|Bash historii|Před vytvořením image virtuálního počítače by se měly vymazat soubory historie bash.|
|2|Verze agenta pro Linux|Je potřeba nainstalovat agenta Azure Linux 2.2.41 nebo novější.|
|3|Požadované parametry jádra|Ověřuje, že jsou nastavené následující parametry jádra: <br>Konzola = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300 |
|4|Odkládací oddíl na disku s operačním systémem|Ověřuje, že se na disku s operačním systémem odkládací oddíly nevytváří.|
|5|Kořenový oddíl na disku s operačním systémem|Vytvořte jeden kořenový oddíl pro disk s operačním systémem.|
|6|Verze OpenSSL|Verze OpenSSL by měla být v 0.9.8 nebo novější.|
|7|Python version (Verze Pythonu)|Doporučujeme Python verze 2,6 nebo novější.|
|8|Interval – aktivní klient|Nastavte ClientAliveInterval na 180. V případě potřeby je možné aplikaci nastavit z 30 na 235. Pokud pro koncové uživatele povolíte SSH, musí být tato hodnota nastavena, jak je vysvětleno.|
|9|Architektura operačního systému|Podporují se jen 64bitové operační systémy.|
|10|Automatická aktualizace|Určuje, jestli je povolená Automatická aktualizace agenta pro Linux.|
|

### <a name="common-test-case-errors"></a>Běžné chyby testovacího případu

V následující tabulce najdete běžné chyby, které se můžou zobrazit při spuštění testovacích případů:

| Scenario | Testovací případ | Chyba | Řešení |
| --- | --- | --- | --- |
| 1 | Testovací případ verze agenta pro Linux | Minimální verze agenta pro Linux je 2.2.41 nebo novější. Tento požadavek byl povinný od 1. května 2020. | Aktualizujte verzi agenta pro Linux. Měl by být 2,241 nebo novější. Další informace najdete na [stránce aktualizace verze agenta pro Linux](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support). |
| 2 | Testovací případ historie bash | Pokud je velikost historie bash v odeslaném obrázku větší než 1 kilobajt (KB), dojde k chybě. Velikost je omezená na 1 KB, aby se zajistilo, že soubor historie bash neobsahuje žádné potenciálně citlivé informace. | Vyřešte připojením virtuálního pevného disku k jinému pracovnímu virtuálnímu počítači a proveďte změny a zmenšete velikost na 1 KB nebo méně. Odstraňte například `.bash` soubory historie. |
| 3 | Požadovaný parametr jádra pro testovací případ | Tato chyba se zobrazí, pokud hodnota pro `console` není nastavená na `ttyS0` . Kontrolou spusťte následující příkaz: <br /> `cat /proc/cmdline` | Nastavte hodnotu pro `console` na a `ttyS0` odešlete požadavek znovu. |
| 4 | Testovací případ intervalu ClientAlive | Pokud sada nástrojů poskytuje pro tento testovací případ neúspěšný výsledek, existuje nevhodná hodnota pro `ClientAliveInterval` . | Nastavte hodnotu pro `ClientAliveInterval` na hodnotu menší nebo rovnou 235 a potom žádost znovu odešlete. |
|

### <a name="windows-test-cases"></a>Testovací případy Windows

V následující tabulce jsou uvedeny testovací případy systému Windows, které sada nástrojů spustí, spolu s popisem ověření testu:

|Scenario |Testovací případy|Popis|
|---|---|---|
|1|Architektura operačního systému|Azure podporuje jenom 64 operačních systémů.|
|2|Závislost uživatelského účtu|Spuštění aplikace by nemělo být závislé na účtu správce.|
|3|Cluster s podporou převzetí služeb při selhání|Funkce Clustering s podporou převzetí služeb při selhání ve Windows serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|4|PROTOKOLŮ|Protokol IPv6 se v prostředí Azure ještě nepodporuje. Aplikace by neměla být závislá na této funkci.|
|5|DHCP|Role serveru Dynamic Host Configuration Protocol zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|6|Hyper-V|Role serveru Hyper-V zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|7|Vzdálený přístup|Role serveru vzdáleného přístupu (Direct Access) zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|8|Služby Rights Management|Rights Management služby. Role serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|9|Služba pro nasazení systému Windows|Služba pro nasazení systému Windows. Role serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|10|BitLocker Drive Encryption|Nástroj BitLocker Drive Encryption se na pevném disku operačního systému nepodporuje, ale je možné ho použít na datových discích.|
|11|Názvový server internetového úložiště|Funkce názvového serveru pro Internet Storage zatím není podporována. Aplikace by neměla být závislá na této funkci.|
|12|Multipath I/O|Funkce Multipath I/O. Tato funkce serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|13|Vyrovnávání zatížení sítě|Vyrovnávání zatížení sítě. Tato funkce serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|14|Protokol PNRP (Peer Name Resolution Protocol)|Protokol PNRP (Peer Name Resolution Protocol). Tato funkce serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|15|Služby SNMP|Funkce služeb SNMP (Simple Network Management Protocol) se zatím nepodporuje. Aplikace by neměla být závislá na této funkci.|
|16|Služba Windows Internet Name Service|Služba Windows Internet Name Service. Tato funkce serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|17|Služba bezdrátové sítě LAN|Služba bezdrátové sítě LAN. Tato funkce serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|

Pokud jste pocházeli při jakémkoli selhání s předchozími testovacími případy, přečtěte si sloupec **Popis** v tabulce pro řešení. Pokud chcete získat další informace, obraťte se na tým podpory.

## <a name="data-disk-size-verification"></a>Ověření velikosti datového disku

Požadavky na datový disk o velikosti větší než 1023 gigabajty (GB) nebudou schváleny. Toto pravidlo se vztahuje na systémy Linux a Windows.

Požadavek odešlete znovu o velikost menší nebo rovnou 1023 GB.

## <a name="os-disk-size-validation"></a>Ověřování velikosti disku s operačním systémem

Omezení velikosti disku operačního systému najdete v následujících pravidlech. Když odešlete jakoukoli žádost, ověřte, jestli je velikost disku operačního systému v rámci omezení pro Linux nebo Windows.

|Operační systém|Doporučená velikost VHD|
|---|---|
|Linux|1 GB až 1023 GB|
|Windows|30 GB až 250 GB|
|

Protože virtuální počítače umožňují přístup k základnímu operačnímu systému, zajistěte, aby velikost virtuálního pevného disku byla pro virtuální pevný disk dostatečně velká. Disky nejsou rozšiřitelné bez výpadků. Použijte velikost disku od 30 GB do 50 GB.

|Velikost virtuálního pevného disku|Skutečná obsazená velikost|Řešení|
|---|---|---|
|>500 tebibytes (TiB)|Není k dispozici|Obraťte se na tým podpory se schválením výjimky.|
|250-500 TiB|Rozdíl velikosti objektu BLOB v >200 gibibajtech (GiB)|Obraťte se na tým podpory se schválením výjimky.|
|

> [!NOTE]
> Větší velikosti disků účtují vyšší náklady a během procesu instalace a replikace budou mít za následek zpoždění. Z důvodu tohoto zpoždění a nákladů může tým podpory vyhledat odůvodnění pro schválení výjimky.

## <a name="wannacry-patch-verification-test-for-windows"></a>Test ověření WannaCry opravy pro Windows

Aby se zabránilo potenciálnímu útoku souvisejícímu s virem WannaCry, zajistěte, aby všechny požadavky na image Windows byly aktualizované pomocí nejnovější opravy.

Verzi souboru bitové kopie můžete ověřit z `C:\windows\system32\drivers\srv.sys` nebo `srv2.sys` .

Následující tabulka uvádí minimální opravenou verzi Windows serveru:

|Operační systém|Verze|
|---|---|
|Systém Windows obsluhuje 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|
|

> [!NOTE]
> Windows Server 2019 nemá žádné požadavky na povinnou verzi.

## <a name="sack-vulnerability-patch-verification"></a>Ověření opravy ohrožení zabezpečení pro parametr

Když odešlete image pro Linux, váš požadavek může být odmítnut z důvodu problémů s verzí jádra.

Aktualizujte jádro pomocí schválené verze a odešlete požadavek znovu. Schválenou verzi jádra můžete najít v následující tabulce. Číslo verze by mělo být rovno nebo větší než zde uvedené číslo.

Pokud bitová kopie není nainstalovaná s některou z následujících verzí jádra, aktualizujte ji pomocí správných oprav. Po aktualizaci obrázku pomocí těchto požadovaných oprav vyžádejte od týmu podpory potřebné schválení:

- CVE-2019-11477
- CVE-2019-11478
- CVE-2019-11479

|Řada OS|Verze|jádro|
|---|---|---|
|Ubuntu|14,04 LTS|4.4.0-151| 
||14,04 LTS|4.15.0-1049 – \* Azure|
||16,04 LTS|4.15.0-1049|
||18,04 LTS|4.18.0 – 1023|
||18,04 LTS|5.0.0-1025|
||18,10|4.18.0 – 1023|
||19,04|5.0.0 – 1010|
||19,04|5.3.0 – 1004|
|RHEL a cent OS|6,10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7,4|3.10.0-693.50.3|
||7,5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7.7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0 – 147|
||"7-RAW" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL-SAP 7,4|Bude doplněno|
||RHEL-SAP 7,5|Bude doplněno|
|SLES|SLES11SP4 (včetně SAP)|3.0.101-108.95.2|
||SLES12SP1 pro SAP|3.12.74-60.64.115.1|
||SLES12SP2 pro SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (jádro – Azure)|
||SLES12SP3 pro SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (jádro – Azure)|
||SLES12SP4 pro SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (jádro – Azure)|
||SLES15 pro SAP|4.12.14-5.30.1 (jádro – Azure)|
||SLES15SP1|4.12.14-5.30.1 (jádro – Azure)|
|Oracle|6,10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0 – 7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK následuje po RHEL.|
||7.6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS stabilní 2079.6.0|4.19.43\*|
||Beta verze 2135.3.1|4.19.50\*|
||Alfa 2163.2.1|4.19.50\*|
|Debian|Jessie (zabezpečení)|3.16.68 – 2|
||Jessie – porty|4.9.168-1 + deb9u3|
||Roztáhnout (zabezpečení)|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0 – 18 a deb9u1|
||Buster, SID (porty roztažení)|4.19.37-5|
|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>Velikost obrázku musí být v násobcích megabajtů.

Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnané na násobky 1 megabajtu (MB). Pokud váš virtuální pevný disk neodpovídá doporučené virtuální velikosti, váš požadavek může být zamítnutý.

Při převodu z nezpracovaného disku na VHD postupujte podle pokynů. Ujistěte se, že velikost nezpracovaného disku je násobkem 1 MB. Další informace najdete v tématu [informace o neschválených distribucích](../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Přístup k virtuálnímu počítači byl odepřen

Problém s _odepřením přístupu_ pro spuštění testovacího případu na virtuálním počítači může být způsoben nedostatečnými oprávněními.

Zkontrolujte, zda jste povolili správný přístup k účtu, na kterém jsou spuštěny vlastní testovací případy. Povolit přístup pro spuštění testovacích případů, pokud není povolen. Pokud nechcete povolit přístup, můžete sdílet výsledky pro samočinný test případu s týmem podpory.

Odeslání žádosti s imagí zakázaného SSH pro proces certifikace:

1. Spusťte [nejnovější nástroj pro testování certifikace pro virtuální počítače Azure](https://aka.ms/AzureCertificationTestTool) na imagi.

2. Vyvolejte [lístek podpory](https://aka.ms/marketplacepublishersupport). Nezapomeňte připojit sestavu sady nástrojů a poskytnout podrobnosti nabídky:
   - Název nabídky
   - Název vydavatele
   - ID/SKU a verze plánu

3. Odešlete žádost o certifikaci znovu.

## <a name="download-failure"></a>Chyba stahování

V následující tabulce najdete případné problémy, které vznikají při stažení image virtuálního počítače s adresou URL sdíleného přístupového podpisu (SAS).

|Chyba|Důvod|Řešení|
|---|---|---|
|Objekt BLOB se nenašel.|Virtuální pevný disk může být buď odstraněn, nebo přesunut ze zadaného umístění.|| 
|Používaný objekt BLOB|VHD používá jiný interní proces.|Virtuální pevný disk by měl být v používaném stavu, když ho stáhnete s adresou URL SAS.|
|Neplatná adresa URL SAS|Přidružená adresa URL SAS pro virtuální pevný disk je nesprávná.|Získejte správnou adresu URL SAS.|
|Neplatný podpis|Přidružená adresa URL SAS pro virtuální pevný disk je nesprávná.|Získejte správnou adresu URL SAS.|
|Podmíněná hlavička HTTP|Adresa URL SAS není platná.|Získejte správnou adresu URL SAS.|
|Neplatný název VHD|Zkontrolujte, jestli v názvu VHD existují nějaké speciální znaky, jako je například znak procenta `%` nebo uvozovky `"` .|Přejmenujte soubor VHD odebráním speciálních znaků.|
|

## <a name="first-partition-starts-at-1-mb-2048-sectors"></a>První oddíl začíná 1 MB (2048 sektorů).

Pokud [vytváříte vlastní image](azure-vm-create-using-own-image.md), ujistěte se, že první 2048 sektorů disku s operačním systémem (1 MB) je prázdný. V opačném případě se publikování nezdaří. Tento požadavek platí jenom pro disk s operačním systémem (ne datové disky). Pokud sestavíte image [ze schválené základny](azure-vm-create-using-approved-base.md), můžete tento požadavek přeskočit.

### <a name="create-a-1-mb-2048-sectors-each-sector-of-512-bytes-partition-on-an-empty-vhd"></a>Vytvořte oddíl o velikosti 1 MB (2048 sektorů, každý sektor 512 bajtů) na prázdném virtuálním pevném disku.

Tyto kroky platí jenom pro Linux.

1. Vytvořte libovolný typ virtuálního počítače se systémem Linux, například Ubuntu, cent OS nebo jiné. Vyplňte požadovaná pole a vyberte **Další: disky >**.

   ![Snímek obrazovky, který ukazuje stránku vytvořit virtuální počítač s zvýrazněným tlačítkem Další: disky.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Vytvořte pro virtuální počítač nespravovaný disk.

   Buď použijte výchozí hodnoty, nebo zadejte libovolnou hodnotu pro pole jako síťové rozhraní, NSG a veřejnou IP adresu.

   ![Obrázek obrazovky stránky datové disky v toku vytvořit virtuální počítač.](./media/create-vm/vm-certification-issues-solutions-16.png)

1. Po vytvoření virtuálního počítače v levém podokně vyberte **disky** .

   ![Snímek obrazovky ukazující, jak vybrat disky pro virtuální počítač](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Připojte virtuální pevný disk jako datový disk k VIRTUÁLNÍmu počítači pro vytvoření tabulky oddílů.

   1. Vyberte **Přidat**  >  **existující objekt BLOB** datadisk.

      ![Snímek obrazovky ukazující, jak přidat datový disk do virtuálního pevného disku.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Najděte svůj účet úložiště VHD.
   1. Vyberte **kontejner** a pak vyberte svůj virtuální pevný disk.
   1. Vyberte **OK**.

      ![Snímek obrazovky se stránkou připojit nespravovaný disk](./media/create-vm/vm-certification-issues-solutions-19.png)

      Virtuální pevný disk se přidá jako datový disk LUN 0.

   1. Restartujte virtuální počítač.

1. Po restartování virtuálního počítače se přihlaste k virtuálnímu počítači pomocí výstupu nebo jiného klienta a spuštěním `sudo  -i` příkazu získáte přístup ke kořenu.

   ![Snímek obrazovky s příkazovým řádkem klienta výstupu ukazujícím sudo-i](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Vytvořte oddíl na virtuálním pevném disku.

   1. Zadejte `fdisk /dev/sdb` příkaz.
   1. Pokud chcete zobrazit existující seznam oddílů z virtuálního pevného disku, zadejte `p` .
   1. Zadejte `d` , pokud chcete odstranit všechny existující oddíly dostupné ve vašem virtuálním pevném disku. Pokud není to nutné, můžete tento krok přeskočit.

      ![Snímek obrazovky s příkazovým řádkem klienta pro výstup ukazující příkazy pro odstranění existujících oddílů.](./media/create-vm/vm-certification-issues-solutions-21.png)

   1. Zadejte pro `n` Vytvoření nového oddílu a vyberte možnost `p` pro (primární oddíl).

   1. Jako hodnotu _prvního sektoru_ zadejte 2048. Jako výchozí hodnotu můžete opustit _poslední sektor_ .

      >[!IMPORTANT]
      >Všechna existující data budou smazána do 2048 sektorů (každý sektor 512 bajtů). Zálohování virtuálního pevného disku před vytvořením nového oddílu.

      ![Snímek obrazovky s příkazovým řádkem klienta výstupu znázorňující příkazy a výstup pro vymazané data.](./media/create-vm/vm-certification-issues-solutions-22.png)

   1. Zadáním `w` potvrďte vytváření oddílů.

      ![Snímek obrazovky s příkazovým řádkem klienta pro výstup ukazující příkazy pro vytvoření oddílu](./media/create-vm/vm-certification-issues-solutions-23.png)

   1. Tabulku oddílů můžete ověřit spuštěním příkazu `n fdisk /dev/sdb` a zadáním `p` . Uvidíte, že je vytvořen oddíl s hodnotou posunu 2048.

      ![Snímek obrazovky s příkazovým řádkem klienta výstupu znázorňující příkazy pro vytvoření posunu 2048.](./media/create-vm/vm-certification-issues-solutions-24.png)

1. Odpojte virtuální pevný disk od virtuálního počítače a odstraňte virtuální počítač.

### <a name="create-a-1-mb-2048-sectors-each-sector-of-512-bytes-partition-by-moving-existing-data-on-vhd"></a>Vytvoření oddílu s 1 MBm (2048 sektorů, každého sektoru 512 bajtů) přesunutím stávajících dat na VHD

Tyto kroky platí jenom pro Linux.

1. Vytvořte libovolný typ virtuálního počítače se systémem Linux, například Ubuntu, cent OS nebo jiné. Vyplňte požadovaná pole a vyberte **Další: disky >**.

   ![Snímek obrazovky, který ukazuje stránku vytvořit virtuální počítač s zvýrazněným tlačítkem Další: disky.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Vytvořte pro virtuální počítač nespravovaný disk.

   ![Obrázek obrazovky stránky datové disky v toku vytvořit virtuální počítač.](./media/create-vm/vm-certification-issues-solutions-16.png)

   Buď použijte výchozí hodnoty, nebo zadejte libovolnou hodnotu pro pole jako síťové rozhraní, NSG a veřejnou IP adresu.

1. Po vytvoření virtuálního počítače v levém podokně vyberte **disky** .

   ![Snímek obrazovky ukazující, jak vybrat disky pro virtuální počítač](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Připojte virtuální pevný disk jako datový disk k VIRTUÁLNÍmu počítači pro vytvoření tabulky oddílů.

   1. Připojte virtuální pevný disk jako datový disk k VIRTUÁLNÍmu počítači pro vytvoření tabulky oddílů.

   1. Vyberte **Přidat**  >  **existující objekt BLOB** datadisk.

      ![Snímek obrazovky ukazující, jak přidat datový disk do virtuálního pevného disku.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Najděte svůj účet úložiště VHD.
   1. Vyberte **kontejner** a pak vyberte svůj virtuální pevný disk.
   1. Vyberte **OK**.

      ![Snímek obrazovky se stránkou připojit nespravovaný disk](./media/create-vm/vm-certification-issues-solutions-19.png)

      Virtuální pevný disk se přidá jako datový disk LUN 0.

   1. Restartujte virtuální počítač.

1. Přihlaste se k virtuálnímu počítači pomocí výstupu nebo jiného klienta a spusťte `sudo  -i` příkaz pro získání přístupu root.

   ![Snímek obrazovky s příkazovým řádkem klienta výstupu ukazující přihlášení a sudo-i](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Spusťte příkaz `echo '+1M,' | sfdisk --move-data /dev/sdc -N 1`.

   ![Snímek obrazovky příkazového řádku klienta pro výstup ukazující spuštění příkazů](./media/create-vm/vm-certification-issues-solutions-25.png)

   >[!NOTE]
   >Dokončení tohoto příkazu může trvat delší dobu, protože závisí na velikosti disku.

1. Odpojte virtuální pevný disk od virtuálního počítače a odstraňte virtuální počítač.


## <a name="default-credentials"></a>Výchozí pověření

Nikdy Neodesílat výchozí pověření pomocí odeslaného virtuálního pevného disku. Přidání výchozích přihlašovacích údajů způsobí, že virtuální pevný disk bude více zranitelný vůči bezpečnostním hrozbám Místo toho vytvořte při odeslání VHD vlastní přihlašovací údaje.
  
## <a name="datadisk-mapped-incorrectly"></a>Nesprávně mapované datadisk

K problémům s mapováním může dojít, když je žádost odeslána s více datovými disky, které nejsou v sekvenci. Například pořadí číslování pro tři datové disky musí být *0, 1, 2*. Jakékoli jiné pořadí se považuje za problém s mapováním.

Odešlete žádost znovu se správným pořadím datových disků.

## <a name="incorrect-os-mapping"></a>Nesprávné mapování OS

Když je vytvořen obrázek, může být namapován na nebo přiřazen nesprávný popisek operačního systému. Pokud například při vytváření image vyberete jako součást názvu operačního systému možnost **Windows** , disk s operačním systémem by měl být nainstalován pouze se systémem Windows. Stejný požadavek platí pro Linux.

## <a name="vm-not-generalized"></a>Virtuální počítač není zobecněný.

Pokud se mají znovu použít všechny image z Azure Marketplace, musí být virtuální pevný disk s operačním systémem zobecněný.

- Pro **Linux** následující proces GENERALIZUJE virtuální počítač Linux a znovu ho nasadí jako samostatný virtuální počítač.

  V okně SSH zadejte následující příkaz: `sudo waagent -deprovision+user` .

- Pro **Windows** můžete zobecnit bitové kopie systému Windows pomocí nástroje `sysreptool` .

  Další informace o tomto `sysreptool` nástroji najdete v tématu [Přehled nástroje pro přípravu systému (Sysprep)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>Chyby datadisk

Pro řešení chyb souvisejících s datovým diskem použijte následující tabulku:

|Chyba|Důvod|Řešení|
|---|---|---|
|`DataDisk- InvalidUrl:`|K této chybě může dojít při odeslání nabídky z důvodu neplatné logické jednotky (LUN).|Ověřte, že je pořadí čísel LUN pro datový disk v partnerském centru.|
|`DataDisk- NotFound:`|K této chybě může dojít, protože datový disk není umístěný na zadané adrese URL SAS.|Ověřte, že je datový disk umístěný na zadané adrese URL SAS.|

## <a name="remote-access-issue"></a>Problém se vzdáleným přístupem

Tato chyba se zobrazí, pokud není možnost protokol RDP (Remote Desktop Protocol) (RDP) pro bitovou kopii systému Windows povolena.

Než je odešlete, povolte přístup k protokolu RDP pro image Windows.

## <a name="bash-history-failed"></a>Nepovedlo se Bashovat historii

Tato chyba se zobrazí, pokud je velikost historie bash v odeslaném obrázku větší než 1 kilobajt (KB). Velikost je omezena na 1 KB, aby bylo možné soubor omezit obsahující potenciálně citlivé informace.

Odstranění historie bash:

1. Nasaďte virtuální počítač a na Azure Portal vyberte možnost **Spustit příkaz** .

   ![Snímek obrazovky Azure Portal s možností ' Run Command ' v levém podokně.](./media/create-vm/vm-certification-issues-solutions-3.png)

1. Vyberte první možnost **RunShellScript** a pak spusťte příkaz: `cat /dev/null > ~/.bash_history && history -c` .

   ![Snímek obrazovky se stránkou Run Command Script na Azure Portal](./media/create-vm/vm-certification-issues-solutions-4.png)

1. Po úspěšném spuštění příkazu restartujte virtuální počítač.

1. Generalizujte virtuální počítač, povezměte virtuální pevný disk image a virtuální počítač zastavte.

1. Znovu odešlete zobecněnou bitovou kopii.

## <a name="request-an-exception-on-vm-images-for-select-tests"></a>Vyžádat výjimku z imagí virtuálních počítačů pro vybrané testy

Vydavatelé můžou vyžádat výjimky pro několik testů provedených během certifikace virtuálních počítačů. Výjimky jsou k dispozici ve výjimečných případech, pokud Vydavatel poskytuje důkaz pro podporu žádosti. Certifikační tým si vyhrazuje právo odepřít nebo schválit výjimky kdykoli.

Tato část popisuje obecné scénáře, ve kterých vydavatelé požadují výjimku a jak si ji vyžádat.

### <a name="scenarios-for-exception"></a>Scénáře pro výjimku

Vydavatelé obecně vyžadují výjimky v následujících případech:

- **Výjimka pro jeden nebo více testovacích případů**. Kontaktujte [podporu partnerského centra](https://aka.ms/marketplacepublishersupport) a vyžádejte si výjimky pro testovací případy.

- **Uzamčené virtuální počítače/žádný kořenový přístup**. Někteří vydavatelé mají scénáře, kde je potřeba zamknout virtuální počítače, protože mají na virtuálním počítači nainstalovaný software, jako jsou třeba brány firewall. V takovém případě si stáhněte [certifikovaný testovací nástroj](https://aka.ms/AzureCertificationTestTool) a odešlete sestavu na [podporu partnerského centra](https://aka.ms/marketplacepublishersupport).

- **Vlastní šablony**. Někteří vydavatelé publikují image virtuálních počítačů, které pro nasazení virtuálních počítačů vyžadují vlastní šablonu Azure Resource Manager (ARM). V takovém případě odešlete vlastní šablony na [podporu partnerského centra](https://aka.ms/marketplacepublishersupport) , aby ji Certifikační tým mohl použít k ověření.

### <a name="information-to-provide-for-exception-scenarios"></a>Informace, které je potřeba poskytnout pro scénáře výjimek

Obraťte se na [podporu partnerského centra](https://aka.ms/marketplacepublishersupport) a požádejte o výjimku pro jeden z těchto scénářů a uveďte následující informace:

- **ID vydavatele** Zadejte ID vydavatele centrálního portálu partnera.
- **ID nabídky/název** Zadejte ID nabídky nebo název.
- **ID SKU/plánu**. Zadejte ID nebo SKU plánu nabídky VM.
- **Verze:** Zadejte verzi nabídky virtuálního počítače, která vyžaduje výjimku.
- **Typ výjimky**. Vyberte si z testů, uzamčených virtuálních počítačů nebo vlastních šablon.
- **Důvod žádosti** Zahrňte důvod žádosti o výjimku a veškeré informace o výjimkách testu.
- **Časová osa**. Zadejte koncové datum pro vaši výjimku.
- **Příloha**. Přiložené důležité dokumenty k legitimaci:

  - Pro zamčené virtuální počítače připojte testovací sestavu.
  - Pro vlastní šablony zadejte vlastní šablonu ARM jako přílohu.

  Pokud tyto přílohy neuvedete, bude vaše žádost zamítnutá.

## <a name="address-a-vulnerability-or-an-exploit-in-a-vm-offer"></a>Řešení ohrožení zabezpečení nebo zneužití v nabídce virtuálních počítačů

Tato část popisuje, jak poskytnout novou bitovou kopii virtuálního počítače, když se v jedné z vašich imagí virtuálních počítačů zjistí ohrožení zabezpečení nebo zneužití. Týká se jenom nabídek virtuálních počítačů Azure publikovaných na Azure Marketplace.

> [!NOTE]
> Poslední bitovou kopii virtuálního počítače nemůžete odebrat z plánu nebo zrušit prodej posledního plánu pro nabídku.

Proveďte jednu z následujících akcí:

- Pokud máte novou image virtuálního počítače, která by nahradila zranitelnou bitovou kopii virtuálního počítače, přečtěte si téma [poskytnutí pevné image virtuálního počítače](#provide-a-fixed-vm-image).
- Pokud nemáte novou image virtuálního počítače, která by nahradila jedinou image virtuálního počítače v plánu, nebo pokud jste s plánem hotovi, [zastavíte prodej tohoto plánu](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).
- Pokud v nabídce nehodláte nahradit jedinou image virtuálního počítače, doporučujeme [tuto nabídku přestat prodávat](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="provide-a-fixed-vm-image"></a>Zadání pevné image virtuálního počítače

Poskytnutí pevné image virtuálního počítače k nahrazení image virtuálního počítače, která má ohrožení zabezpečení nebo zneužití:

1. Zadejte novou image virtuálního počítače pro řešení ohrožení zabezpečení nebo zneužití.
1. Odstraňte image virtuálního počítače s chybou zabezpečení nebo zneužití.
1. Znovu publikujte nabídku.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Zadejte novou image virtuálního počítače pro řešení ohrožení zabezpečení nebo zneužití.

Chcete-li provést tento postup, připravte technické prostředky pro bitovou kopii virtuálního počítače, kterou chcete přidat. Další informace najdete v tématu [Vytvoření virtuálního počítače pomocí schválené základny](azure-vm-create-using-approved-base.md) nebo [Vytvoření virtuálního počítače pomocí vlastní image](azure-vm-create-using-own-image.md) a [vygenerování identifikátoru URI SAS pro vaši image virtuálního počítače](azure-vm-get-sas-uri.md).

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
1. V levém podokně vyberte přehled **komerčního tržiště**  >  .
1. Ve sloupci **alias nabídky** vyberte nabídku.
1. Na kartě **Přehled plánu** ve sloupci **název** vyberte příslušný plán.
1. Na kartě **Technická konfigurace** v části **image virtuálních počítačů** vyberte **+ Přidat image virtuálního počítače**.

   > [!NOTE]
   > V jednom okamžiku můžete přidat jenom jednu image virtuálního počítače do jednoho plánu. Chcete-li přidat více imagí virtuálních počítačů, před přidáním další image virtuálního počítače nejprve publikujte první z nich.

1. V zobrazených oknech zadejte novou verzi disku a image virtuálního počítače.
1. Vyberte **Uložit koncept**.

Dále odeberte image virtuálního počítače s chybou zabezpečení.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>Odebrání image virtuálního počítače s chybou zabezpečení nebo zneužitím

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V levém podokně vyberte přehled **komerčního tržiště**  >  .
3. Ve sloupci **alias nabídky** vyberte nabídku.
4. Na kartě **Přehled plánu** ve sloupci **název** vyberte příslušný plán.
5. Na kartě **Technická konfigurace** v části **image virtuálních počítačů** vedle image virtuálního počítače, kterou chcete odebrat, vyberte **Odebrat image virtuálního počítače**.
6. V dialogovém okně vyberte **pokračovat**.
7. Vyberte **Uložit koncept**.

Potom nabídku znovu publikujte.

#### <a name="republish-the-offer"></a>Opětovné publikování nabídky

1. Vyberte možnost **zkontrolovat a publikovat**.
2. Pokud potřebujete poskytnout certifikačnímu týmu nějaké informace, přidejte ho do pole **poznámky pro certifikaci** .
3. Vyberte **Publikovat**.

Chcete-li dokončit proces publikování, přečtěte si téma [Revize a publikování nabídek](review-publish-offer.md).

### <a name="vm-images-with-limited-access-or-requiring-custom-templates"></a>Image virtuálních počítačů s omezeným přístupem nebo vyžadováním vlastních šablon

#### <a name="locked-down-or-ssh-disabled-offer"></a>Uzamčené (nebo) nabídka zakázaného SSH

  Bitové kopie, které jsou publikovány pomocí SSH Disabled (pro Linux) nebo RDP Disabled (pro Windows), jsou považovány za zamčené virtuální počítače. Existují zvláštní obchodní scénáře, které můžou vydavatelé jenom omezit přístup jenom na několik uživatelů. Při ověřování se můžou uzamknout virtuální počítače, které neumožňují provedení určitých certifikačních příkazů.


#### <a name="custom-templates"></a>Vlastní šablony

   Obecně platí, že všechny bitové kopie, které jsou publikovány v rámci jednotlivých virtuálních počítačů, budou následovat po šabloně Standard ARM pro nasazení. Existují však situace, kdy může vydavatel vyžadovat přizpůsobení při nasazování virtuálních počítačů (např. více síťových adaptérů, které mají být nakonfigurovány).
    
   V závislosti na následujících scénářích (nevyčerpávající) budou vydavatelé používat vlastní šablony pro nasazení virtuálního počítače:

   * Virtuální počítač vyžaduje další síťové podsítě.
   * Další metadata, která se mají vložit do šablony ARM
   * Příkazy, které jsou předpokladem pro spuštění šablony ARM.

### <a name="vm-extensions"></a>Rozšíření virtuálních počítačů   

   Rozšíření virtuálních počítačů Azure jsou malé aplikace, které na virtuálních počítačích Azure umožňují provádět úlohy konfigurace a automatizace po nasazení. Pokud virtuální počítač vyžaduje například instalaci softwaru, antivirovou ochranu nebo spuštění interního skriptu, je možné pro tento účel použít rozšíření virtuálního počítače. 

   Ověření rozšíření virtuálních počítačů se systémem Linux vyžadují, aby byla součástí bitové kopie následující:
* Větší 2.2.41 agenta Azure Linux
* Verze Pythonu nad 2,8 


Další informace najdete v [rozšíření virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux).
     
## <a name="next-steps"></a>Další kroky

- [Konfigurace vlastností nabídek virtuálních počítačů](azure-vm-create-properties.md)
- [Ceny aktivního tržiště](partner-center-portal/marketplace-rewards.md)
- Pokud máte dotazy nebo připomínky ke zlepšení, obraťte se na [podporu partnerského centra](https://aka.ms/marketplacepublishersupport).
 
