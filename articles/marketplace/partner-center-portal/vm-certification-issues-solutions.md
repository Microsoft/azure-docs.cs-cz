---
title: Certifikace virtuálních počítačů – problémy a řešení
description: Tento článek vysvětluje běžné chybové zprávy pro image virtuálních počítačů. Zabývá se taky souvisejícími řešeními.
author: v-miegge
ms.author: v-krmall
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
ms.date: 06/16/2020
ms.openlocfilehash: 7bd3f1a5b242ee5196e92456cb3fc8c97f8f5b27
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958527"
---
# <a name="virtual-machine-certification---issues-and-solutions"></a>Certifikace virtuálních počítačů – problémy a řešení

Když publikujete image virtuálních počítačů do Azure Marketplace, tým Azure ověří image virtuálního počítače, aby zajistila její možnosti spouštění, zabezpečení a kompatibility Azure. Pokud některý z testů s vysokou kvalitou selže, publikování se nezdaří a zobrazí se zpráva obsahující chybu.

Tento článek vysvětluje běžné chybové zprávy pro image virtuálních počítačů. Popisuje také související řešení:

> [!NOTE]
> Pokud máte dotazy nebo připomínky ke zlepšení, obraťte se na [podporu partnerského centra](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="approved-base-image"></a>Schválená základní image

Když odešlete žádost o opětovné publikování image s aktualizacemi, může testovací případ na částečný počet selhat. V takovém případě nebude vaše image schválená.

K této chybě dojde, pokud jste použili základní image, která patří jinému vydavateli a aktualizovali jste bitovou kopii. V takové situaci nebudete moct publikovat image.

Pokud chcete tento problém vyřešit, načtěte nejnovější image z Azure Marketplace a proveďte v této imagi změny. Pro zobrazení schválených základních imagí, kde můžete vyhledat svou image, se podívejte na tyto informace:

- [Linux – image](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows-images](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base))

## <a name="vm-extension-failure"></a>Selhání rozšíření virtuálního počítače

Pokud chcete zjistit, jestli vaše image podporuje rozšíření virtuálních počítačů, nebo ne, postupujte podle těchto kroků:

Povolit rozšíření virtuálních počítačů:

1. Vyberte virtuální počítač Linux.
2. Přejít na **nastavení diagnostiky**.
3. Pomocí aktualizace **účtu úložiště**povolte základní matice.
4. Vyberte **Uložit**.

   ![Povolit sledování na úrovni hosta.](./media/vm-certification-issues-solutions-1.png)

Ověřte, jestli jsou rozšíření virtuálních počítačů správně aktivované:

5. Přejít na kartu **rozšíření virtuálního počítače** na virtuálním počítači a ověřit **diagnostické rozšíření pro Linux**.
6. Pokud se stav **zřizování úspěšně zřídí** , test testovacího případu byl úspěšný.
7. Pokud se stav **zřizování nezdařil** , testovací případ rozšíření se nezdařil a je nutné nastavit zpřísněný příznak.

   ![Zřizování bylo úspěšné.](./media/vm-certification-issues-solutions-2.png)

   Pokud se rozšíření virtuálního počítače nepovede, [pomocí diagnostického rozšíření pro Linux monitorujte metriky a protokoly](../../virtual-machines/extensions/diagnostics-linux.md) , abyste je povolili. Pokud nechcete, aby bylo rozšíření virtuálního počítače povolené, obraťte se na tým podpory a požádejte ho, aby rozšíření zakázal.

## <a name="virtual-machine-provisioning-issue"></a>Problém zřizování virtuálních počítačů

Před odesláním vaší nabídky ověřte, zda je proces zřizování pro virtuální počítač striktně následován. Pokud chcete zobrazit formát JSON pro zřizování virtuálního počítače, přečtěte si [certifikát na virtuálním počítači Azure (VM) image](azure-vm-image-certification.md).

Problémy zřizování můžou zahrnovat tyto scénáře selhání:

|S.NO|error|reason|řešení|
|---|---|---|---|
|1|Neplatný virtuální pevný disk (VHD)|Pokud zadaná hodnota souboru cookie v zápatí VHD není správná, pak se virtuální pevný disk považuje za neplatný.|Znovu vytvořte image a odešlete žádost.|
|2|Neplatný typ objektu BLOB|Zřizování virtuálního počítače se nezdařilo, protože použitý blok je typ objektu BLOB místo typu stránky.|Znovu vytvořte image a odešlete žádost.|
|3|Časový limit zřizování nebo není správně zobecněný|Došlo k potížím s generalizací virtuálních počítačů.|Znovu vytvořte bitovou kopii pomocí generalizace a odešlete žádost.|

> [!NOTE]
> Pro dokumentaci týkající se generalizace virtuálních počítačů použijte tyto odkazy:
> - [Linux](create-azure-vm-technical-asset.md#generalize-the-image))
> - [Systém Windows](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep))

## <a name="software-compliance-for-windows"></a>Kompatibilita softwaru pro Windows

Pokud se vaše žádost o image Windows zamítla kvůli kompatibilitě softwaru, pak jste možná vytvořili image Windows s nainstalovaným SQL serverem a nemusíte z Azure Marketplace přebírat příslušnou základní image SQL.

Nevytvářejte vlastní image Windows s nainstalovaným SQL serverem. Místo toho použijte z Azure Marketplace schválené základní image SQL (Enterprise/Standard/Web).

Pokud se pokoušíte nainstalovat sadu Visual Studio nebo libovolný produkt licencovaný na Office, obraťte se na tým podpory, který vám poskytne předchozí schválení.

Další informace najdete v podrobnějších informacích o tom, jak si můžete vybrat schválený základ pro [Vytvoření technických prostředků virtuálních počítačů Azure](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base).

## <a name="tool-kit-test-case-execution-failed"></a>Spuštění testovacího případu sady nástrojů se nezdařilo

Sada nástrojů Microsoft Certification Toolkit vám pomůže provést testovací případy a ověřit, jestli je váš virtuální pevný disk nebo Image kompatibilní s prostředím Azure.

Stáhněte si [sadu nástrojů Microsoft Certification Toolkit](azure-vm-image-certification.md).

## <a name="linux-test-cases"></a>Testovací případy pro Linux

Níže jsou uvedené testovací případy pro Linux, které sada nástrojů spustí. Ověření testu je uvedeno v popisu.

|Zdroj č.|testovací případy|description|
|---|---|---|
|1|Bash historii|Před vytvořením image virtuálního počítače by se měly vymazat soubory historie bash.|
|2|Verze agenta pro Linux|Je potřeba nainstalovat agenta Azure Linux 2.2.41 a novější.|
|3|Požadované parametry jádra|Ověří, jestli jsou nastavené tyto parametry jádra: <br>Konzola = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|Odkládací oddíl na disku s operačním systémem|Ověřuje, že se na disku s operačním systémem odkládací oddíly nevytváří.|
|5|Kořenový oddíl na disku s operačním systémem|Vytvořte jeden kořenový oddíl pro disk s operačním systémem.|
|6|Verze OpenSSL|Verze OpenSSL by měla být větší nebo rovna hodnotě v 0.9.8.|
|7|Verze Pythonu|Doporučujeme Python verze 2.6 + důrazně.|
|8|Interval – aktivní klient|Nastavte ClientAliveInterval na 180. V případě potřeby je možné tuto aplikaci nastavit mezi 30 až 235. Pokud pro koncové uživatele povolíte SSH, musí být tato hodnota nastavena, jak je vysvětleno.|
|9|Architektura operačního systému|Podporují se jen 64bitové operační systémy.|
|10|Automatická aktualizace|Určuje, jestli je povolená Automatická aktualizace agenta pro Linux.|

### <a name="common-errors-found-while-executing-the-previous-test-cases"></a>Při provádění předchozích testovacích případů se našly běžné chyby.

Při provádění předchozích testovacích případů byly zjištěny běžné chyby.
 
|S.NO|testovací případ|error|řešení|
|---|---|---|---|
|1|Testovací případ verze agenta pro Linux|Minimální verze agenta pro Linux je 2,241 nebo vyšší. Tento požadavek byl povinný od 1. května 2020.|Aby bylo možné [Odeslat žádost](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support), musí být bitová kopie aktualizována s požadovanou verzí.|
|2|Testovací případ historie bash|Pokud je velikost historie bash v odeslaném obrázku větší než 1 KB, zobrazí se chyba. Velikost je omezená na 1 KB, aby se zajistilo, že v souboru historie bash nejsou zachyceny žádné potenciálně citlivé informace.|Pokud chcete tento problém vyřešit, připojte VHD k jakémukoli jinému pracovnímu virtuálnímu počítači a proveďte nějaké změny (například odstraňte `.bash` soubory historie), které chcete zmenšit, aby velikost menší nebo rovna 1 KB.|
|3|Požadovaný parametr jádra pro testovací případ|Tato chyba se zobrazí, pokud hodnota pro **konzolu** není nastavená na **ttyS0**. Kontrolu provedete provedením příkazu:<br>`cat /proc/cmdline`|Nastavte hodnotu pro **Console** na **ttyS0** a odešlete požadavek znovu.|
|4|Testovací případ intervalu ClientAlive|Pokud výsledek sady nástrojů poskytne neúspěšný výsledek pro tento testovací případ, existuje nevhodná hodnota pro **ClientAliveInterval**.|Nastavte hodnotu parametru **ClientAliveInterval** na hodnotu menší nebo rovnou hodnotě 235 a odešlete požadavek znovu.|

### <a name="windows-test-cases"></a>Testovací případy Windows

Níže jsou uvedené testovací případy systému Windows, které sada nástrojů spustí. Ověření testu je uvedeno v popisu.

|Zdroj č.|testovací případy|description|
|---|---|---|---|
|1|Architektura operačního systému|Azure podporuje jenom 64 bitových operačních systémů.|
|2|Závislost uživatelského účtu|Provádění aplikace by nemělo být závislé na účtu správce.|
|3|Cluster s podporou převzetí služeb při selhání|Funkce Clustering s podporou převzetí služeb při selhání ve Windows serveru se zatím nepodporuje. aplikace by neměla být závislá na této funkci.|
|4|PROTOKOLŮ|Protokol IPv6 se v prostředí Azure ještě nepodporuje. Aplikace by neměla být závislá na této funkci.|
|5|DHCP|Role serveru Dynamic Host Configuration Protocol zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|6|Hyper-V|Role serveru Hyper-V zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|7|Vzdálený přístup|Role serveru vzdáleného přístupu (Direct Access) zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|8|Služby Rights Management|Rights Management služby. Role serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|9|Služba pro nasazení systému Windows|Služba pro nasazení systému Windows. Role serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|10|BitLocker Drive Encryption|Nástroj BitLocker Drive Encryption se na pevném disku operačního systému nepodporuje, ale můžete ho použít na datových discích.|
|11|Názvový server internetového úložiště|Funkce názvového serveru pro Internet Storage zatím není podporována. Aplikace by neměla být závislá na této funkci.|
|12|Multipath I/O|Funkce Multipath I/O. Tato funkce serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|13|Vyrovnávání zatížení sítě|Vyrovnávání zatížení sítě. Tato funkce serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|14|Protokol PNRP (Peer Name Resolution Protocol)|Protokol PNRP (Peer Name Resolution Protocol). Tato funkce serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|15|Služby SNMP|Funkce služby SNMP zatím není podporována. Aplikace by neměla být závislá na této funkci.|
|16|Služba Windows Internet Name Service|Služba Windows Internet Name Service. Tato funkce serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|
|17|Služba bezdrátové sítě LAN|Služba bezdrátové sítě LAN. Tato funkce serveru zatím není podporovaná. Aplikace by neměla být závislá na této funkci.|

Pokud jste pocházeli při jakémkoli selhání s výše uvedenými testovacími případy, přečtěte si sloupec **Popis** v předchozí tabulce pro toto řešení. Pokud potřebujete další informace, obraťte se na tým podpory. 

## <a name="data-disk-size-verification"></a>Ověření velikosti datového disku

Pokud je velikost jakékoli žádosti odeslané s datovým diskem větší než 1023 GB, bude tato žádost schválena. Toto pravidlo platí pro Linux & Windows.

požadavek odešlete znovu o velikost menší nebo rovnou 1023 GB.

## <a name="os-disk-size-validation"></a>Ověřování velikosti disku s operačním systémem

Omezení velikosti disku operačního systému najdete v následujících pravidlech. Když odešlete jakoukoli žádost, ověřte, jestli je velikost disku operačního systému v rámci omezení pro Linux nebo Windows.

|Operační systém|Doporučená velikost VHD|
|---|---|
|Linux|30 GB až 1023 GB|
|Windows|30 GB až 250 GB|

Protože virtuální počítače umožňují přístup k základnímu operačnímu systému, zajistěte, aby velikost virtuálního pevného disku byla pro virtuální pevný disk dostatečně velká. Vzhledem k tomu, že disky nejsou rozšiřitelné bez výpadků, použijte velikost disku v rozmezí 30 až 50 GB.

|Velikost virtuálního pevného disku|Skutečná obsazená velikost|řešení|
|---|---|---|
|>500 TiB|Není k dispozici|obraťte se na tým podpory se schválením výjimky.|
|250-500 TiB|>200 GiB se liší od velikosti objektu BLOB|obraťte se na tým podpory se schválením výjimky.|

> [!NOTE]
> Větší velikosti disků účtují vyšší náklady a během kroků zřizování a replikace se bude účtovat zpoždění. Z důvodu tohoto zpoždění a nákladů může tým podpory vyhledat odůvodnění pro schválení výjimky.

## <a name="wannacry-patch-verification-test-for-windows"></a>Test ověření WannaCry opravy pro Windows

Aby se zabránilo potenciálnímu útoku souvisejícímu se WannaCry virem, zajistěte, aby všechny požadavky na image Windows byly aktualizované pomocí nejnovější opravy.

Pokud chcete zjistit verzi opravenou v systému Windows Server, přečtěte si v následující tabulce Podrobnosti o operačním systému a minimální verzi, kterou bude podporovat. 

Verzi souboru bitové kopie lze ověřit z `C:\windows\system32\drivers\srv.sys` nebo `srv2.sys` .

> [!NOTE]
> WindowsServer2019 nemá žádné požadavky na povinnou verzi.

|Operační systém|verze|
|---|---|
|WindowsServer2008R2|6.1.7601.23689|
|WindowsServer2012|6.2.9200.22099|
|WindowsServer2012R2|6.3.9600.18604|
|WindowsServer2016|10.0.14393.953|
|WindowsServer2019|NA|

## <a name="sack-vulnerability-patch-verification"></a>Ověření opravy ohrožení zabezpečení pro parametr

Když odešlete image pro Linux, váš požadavek může být odmítnut z důvodu problémů s verzí jádra.

Aktualizujte jádro pomocí schválené verze a odešlete požadavek znovu. Schválenou verzi jádra můžete najít v následující tabulce. Číslo verze by mělo být rovno nebo větší než hodnota uvedená níže.

Pokud bitová kopie není nainstalovaná s některou z následujících verzí jádra, aktualizujte si Image pomocí správných oprav. Další informace najdete na následujících odkazech. Po aktualizaci obrázku pomocí těchto požadovaných oprav vyžádejte od týmu podpory potřebné schválení:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|Řada OS|verze|jádra|
|---|---|---|
|Ubuntu|14,04 LTS|4.4.0-151| 
||14,04 LTS|4.15.0-1049 – * – Azure|
||16,04 LTS|4.15.0-1049|
||18,04 LTS|4.18.0 – 1023|
||18,04 LTS|5.0.0-1025|
||18,10|4.18.0 – 1023|
||19,04|5.0.0 – 1010|
||19,04|5.3.0 – 1004|
|RHEL a cent OS|6,10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7.4|3.10.0-693.50.3|
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
|CoreOS stabilní 2079.6.0|4.19.43*|
||Beta verze 2135.3.1|4.19.50*|
||Alfa 2163.2.1|4.19.50*|
|Debian|Jessie (zabezpečení)|3.16.68 – 2|
||Jessie – porty|4.9.168-1 + deb9u3|
||Roztáhnout (zabezpečení)|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0 – 18 a deb9u1|
||Buster, SID (porty roztažení)|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>Velikost obrázku musí být v násobcích megabajtů.

Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na více než 1 MB. Pokud váš virtuální pevný disk neodpovídá doporučené virtuální velikosti, váš požadavek může být zamítnutý.

Při převodu z nezpracovaného disku na virtuální pevný disk postupujte podle pokynů a je nutné zajistit, aby velikost nezpracovaného disku byla násobkem 1 MB. Další informace najdete v tématu [informace o neschválených distribucích](../../virtual-machines/linux/create-upload-generic.md) .

## <a name="vm-access-denied"></a>Přístup k virtuálnímu počítači byl odepřen

Pokud při provádění testovacích případů na virtuálním počítači provedete problémy s odepřeným přístupem, může to být způsobeno nedostatečnými oprávněními ke spuštění testovacích případů.

Zkontrolujte, zda je pro účet, na kterém provádíte vlastní testovací případy, povolen správný přístup. Pokud ne, povolte přístup pro spuštění testovacích případů. Pokud nechcete povolit přístup, můžete sdílet výsledky pro samočinný test případu s týmem podpory.

## <a name="download-failure"></a>Chyba stahování
    
V následující tabulce najdete případné problémy při stahování image virtuálního počítače pomocí adresy URL SAS.

|S.NO|error|reason|řešení|
|---|---|---|---|
|1|Objekt BLOB se nenašel.|Virtuální pevný disk se může buď odstranit, nebo přesunout ze zadaného umístění.|| 
|2|Používaný objekt BLOB|Virtuální pevný disk používá jiný interní proces.|Virtuální pevný disk by měl být při stahování pomocí adresy URL SAS v používaném stavu.|
|3|Neplatná adresa URL SAS|Přidružená adresa URL SAS pro tento virtuální pevný disk je nesprávná.|Získejte správnou adresu URL SAS.|
|4|Neplatný podpis|Přidružená adresa URL SAS pro tento virtuální pevný disk je nesprávná.|Získejte správnou adresu URL SAS.|
|6|Podmíněná hlavička HTTP|Neplatná adresa URL SAS|Získejte správnou adresu URL SAS.|
|7|Neplatný název VHD|Ověřte, zda **%** v názvu VHD existují speciální znaky, například nebo **""** .|Přejmenování souboru VHD odebráním speciálních znaků|

## <a name="first-1-mb-partition"></a>První oddíl 1 MB

Při odesílání virtuálního pevného disku se ujistěte, že první oddíl 1 MB virtuálního pevného disku je prázdný. V opačném případě se vaše žádost nezdaří.

## <a name="default-credentials"></a>Výchozí pověření

Vždy zajistěte, aby se výchozí pověření neodesílala u odeslaného virtuálního pevného disku. Přidání výchozích přihlašovacích údajů způsobí, že virtuální pevný disk bude více zranitelný vůči bezpečnostním hrozbám Místo toho vytvořte při odesílání VHD vlastní přihlašovací údaje.
  
## <a name="datadisk-mapped-incorrectly"></a>Nesprávně mapované datadisk

Pokud je žádost odeslána s více datovými disky, ale jejich pořadí není v pořadí, považuje se za problém s mapováním. Například pokud existují tři datové disky, pořadí číslování musí být **0, 1, 2**. Jakékoli jiné pořadí bude považováno za problém s mapováním.

odešlete žádost znovu se správným pořadím datových disků.

## <a name="incorrect-os-mapping"></a>Nesprávné mapování OS

Když se vytvoří obrázek, může být namapovaný nebo přiřazený k nesprávnému popisku operačního systému. Pokud je například při vytváření image vybraná možnost **Windows** jako součást názvu operačního systému, disk s operačním systémem by měl být nainstalovaný jenom s Windows. Totéž platí pro Linux.

## <a name="vm-not-generalized"></a>Virtuální počítač není zobecněný.

Pokud se mají znovu použít všechny image vybrané z Azure Marketplace, musí být virtuální pevný disk s operačním systémem zobecněný.

Linux:

Následující proces generalizuje virtuální počítač Linux a znovu ho nasadí jako samostatný virtuální počítač.

V okně SSH zadejte následující příkaz:`sudo waagent -deprovision+user`

Windows:

Bitové kopie systému Windows jsou zobecněny pomocí `sysreptool` .

Další informace o tomto nástroji najdete v článku [Nástroj Sysprep (Příprava systému) – přehled]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) .

## <a name="datadisk-error"></a>Chyba datadisk

V následující tabulce najdete řešení chyb souvisejících s datovým diskem.

|Chyba|reason|řešení|
|---|---|---|
|`DataDisk- InvalidUrl:`|Tato chyba může být způsobena neplatným číslem při odeslání nabídky logické jednotce (LUN).|Ověřte, že je pořadí čísel LUN pro datový disk v partnerském centru.|
|`DataDisk- NotFound:`|Tato chyba může být způsobená tím, že datový disk není umístěný na zadané adrese URL SAS.|Ověřte, že je datový disk umístěný na adrese URL SAS zadané v požadavku.|

## <a name="remote-access-issue"></a>Problém se vzdáleným přístupem

Pokud není možnost protokolu RDP pro bitovou kopii systému Windows povolena, zobrazí se tato chyba. 

Před odesláním povolte možnost přístup k protokolu RDP pro image Windows.

## <a name="next-steps"></a>Další kroky

Pokud máte dotazy nebo připomínky ke zlepšení, obraťte se na [podporu partnerského centra](https://partner.microsoft.com/support/v2/?stage=1).
