---
title: Běžné problémy při certifikaci imagí virtuálních počítačů pro Azure Marketplace
description: Tento článek vysvětluje běžné chybové zprávy a problémy při testování a certifikaci imagí virtuálních počítačů pro Azure Marketplace. Také se zabývá souvisejícími řešeními.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 06/16/2020
ms.openlocfilehash: d724ef463d7c7ad237b5fd023e9c15f50de96f04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803462"
---
# <a name="common-issues-when-certifying-virtual-machine-images-for-azure-marketplace"></a>Běžné problémy při certifikaci imagí virtuálních počítačů pro Azure Marketplace

Když publikujete image virtuálního počítače do Azure Marketplace, tým Azure je ověří, aby zajistil, že se jedná o možnosti spouštění, zabezpečení a kompatibility Azure. Pokud některý z testů s vysokou kvalitou selže, publikování se nezdaří a zobrazí se chybová zpráva s popisem problému.

Tento článek vysvětluje běžné chybové zprávy během publikování imagí virtuálních počítačů společně se souvisejícími řešeními.

> [!NOTE]
> Pokud máte dotazy nebo připomínky ke zlepšení, kontaktujte prosím [podporu partnerského centra](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="approved-base-image"></a>Schválená základní image

Když odešlete žádost o opětovné publikování image s aktualizacemi, může testovací případ na částečný počet selhat. Pokud dojde k chybě, váš obrázek nebude schválený.

K této chybě dojde, když použijete základní bitovou kopii, která patří jinému vydavateli a aktualizovali jste bitovou kopii. V takové situaci nebudete moct publikovat image.

Chcete-li tento problém vyřešit, načtěte obrázek z Azure Marketplace a proveďte v něm změny. Další informace najdete v následujících článcích:

- [Image Linux](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Bitové kopie systému Windows](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)

> [!Note]
> Pokud používáte základní image Linux, která není z webu Marketplace, můžete posunout první oddíl o 2048 KB. To umožňuje, aby neformátované místo bylo možné použít k přidání nových fakturačních informací, a umožní službě Azure začít s publikováním virtuálního počítače na Marketplace.  

## <a name="vm-extension-failure"></a>Selhání rozšíření virtuálního počítače

Zkontrolujte, jestli vaše image podporuje rozšíření virtuálních počítačů.

Pokud chcete povolit rozšíření virtuálních počítačů, udělejte toto:

1. Vyberte virtuální počítač se systémem Linux.
1. Přejít na **nastavení diagnostiky**.
1. Pomocí aktualizace **účtu úložiště**povolte základní matice.
1. Vyberte **Uložit**.

   ![Povolení sledování na úrovni hosta](./media/vm-certification-issues-solutions-1.png)

Pokud chcete ověřit, jestli jsou rozšíření virtuálních počítačů správně aktivované, udělejte toto:

1. Na virtuálním počítači vyberte kartu **rozšíření virtuálního počítače** a pak ověřte stav **rozšíření Linux Diagnostics**.
    * Pokud je stav *zřizování úspěšné*, předává testovací případ rozšíření.  
    * Pokud se stav *zřizování nezdařil*, testový případ rozšíření se nezdařil a je nutné nastavit zpřísněný příznak.

      ![Snímek obrazovky zobrazující, že zřizování bylo úspěšné](./media/vm-certification-issues-solutions-2.png)

      Pokud se rozšíření virtuálního počítače nepovede, přečtěte si téma [použití diagnostického rozšíření systému Linux k monitorování metrik a protokolů](../../virtual-machines/extensions/diagnostics-linux.md) , které chcete povolit. Pokud nechcete, aby bylo rozšíření virtuálního počítače povolené, obraťte se na tým podpory a požádejte ho, aby ho zakázal.

## <a name="vm-provisioning-issue"></a>Problém zřizování virtuálních počítačů

Ujistěte se, že jste před odesláním nabídky ověřili, že jste provedli proces zřizování virtuálních počítačů striktně. Pokud chcete zobrazit formát JSON pro zřizování virtuálního počítače, přečtěte si téma [certifikace image virtuálního počítače Azure](azure-vm-image-certification.md).

Problémy zřizování můžou zahrnovat tyto scénáře selhání:

|Scénář|Chyba|Důvod|Řešení|
|---|---|---|---|
|1|Neplatný virtuální pevný disk (VHD)|Pokud je zadaná hodnota souboru cookie v zápatí VHD nesprávná, bude virtuální pevný disk považován za neplatný.|Znovu vytvořte bitovou kopii a odešlete žádost.|
|2|Neplatný typ objektu BLOB|Zřizování virtuálního počítače se nezdařilo, protože použitý blok je typ objektu BLOB místo typu stránky.|Znovu vytvořte bitovou kopii a odešlete žádost.|
|3|Časový limit zřizování nebo není správně zobecněný|Došlo k potížím s generalizací virtuálních počítačů.|Znovu vytvořte bitovou kopii pomocí generalizace a odešlete žádost.|

> [!NOTE]
> Další informace o generalizaci virtuálních počítačů najdete v těchto tématech:
> - [Dokumentace pro Linux](create-azure-vm-technical-asset.md#generalize-the-image)
> - [Dokumentace k Windows](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="software-compliance-for-windows"></a>Kompatibilita softwaru pro Windows

Pokud se vaše žádost o bitovou kopii systému Windows zamítla z důvodu problému s kompatibilitou softwaru, možná jste vytvořili bitovou kopii systému Windows s nainstalovanou instancí SQL serveru místo toho, aby se z Azure Marketplace přestala příslušná základní image verze SQL.

Nevytvářejte vlastní image Windows s nainstalovaným SQL serverem. Místo toho použijte z Azure Marketplace schválené základní image SQL (Enterprise/Standard/Web).

Pokud se pokoušíte nainstalovat sadu Visual Studio nebo jakýkoli produkt licencovaný pro Office, obraťte se na tým podpory, který vám poskytne předchozí schválení.

Další informace o výběru schválené základní třídy najdete v tématu [Vytvoření technických prostředků virtuálních počítačů Azure](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base).

## <a name="tool-kit-test-case-execution-failed"></a>Spuštění testovacího případu sady nástrojů se nezdařilo 

Sada nástrojů Microsoft Certification Toolkit vám může pomáhat při spouštění testovacích případů a ověření, že je váš virtuální pevný disk nebo Image kompatibilní s prostředím Azure.

Stáhněte si [sadu nástrojů Microsoft Certification Toolkit](azure-vm-image-certification.md).

## <a name="linux-test-cases"></a>Testovací případy pro Linux

V následující tabulce jsou uvedeny testovací případy pro Linux, které sada nástrojů spustí. Ověření testu je uvedeno v popisu.

|Scénář|Testovací případ|Description|
|---|---|---|
|1|Bash historii|Před vytvořením image virtuálního počítače by se měly vymazat soubory historie bash.|
|2|Verze agenta pro Linux|Je potřeba nainstalovat agenta Azure Linux 2.2.41 nebo novější.|
|3|Požadované parametry jádra|Ověřuje, že jsou nastavené následující parametry jádra: <br>Konzola = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|Odkládací oddíl na disku s operačním systémem|Ověřuje, že se na disku s operačním systémem odkládací oddíly nevytváří.|
|5|Kořenový oddíl na disku s operačním systémem|Vytvořte jeden kořenový oddíl pro disk s operačním systémem.|
|6|Verze OpenSSL|Verze OpenSSL by měla být v 0.9.8 nebo novější.|
|7|Verze Pythonu|Doporučujeme Python verze 2,6 nebo novější.|
|8|Interval – aktivní klient|Nastavte ClientAliveInterval na 180. V případě potřeby je možné aplikaci nastavit z 30 na 235. Pokud pro koncové uživatele povolíte SSH, musí být tato hodnota nastavena, jak je vysvětleno.|
|9|Architektura operačního systému|Podporují se jen 64bitové operační systémy.|
|10|Automatická aktualizace|Určuje, jestli je povolená Automatická aktualizace agenta pro Linux.|

### <a name="common-errors-found-while-executing-previous-test-cases"></a>Při provádění předchozích testovacích případů se našly běžné chyby.

V následující tabulce jsou uvedeny běžné chyby, které byly nalezeny při provádění předchozích testovacích případů:
 
|Scénář|Testovací případ|Chyba|Řešení|
|---|---|---|---|
|1|Testovací případ verze agenta pro Linux|Minimální verze agenta pro Linux je 2.2.41 nebo novější. Tento požadavek byl povinný od 1. května 2020.|Aktualizujte prosím verzi agenta pro Linux a měla by být 2,241 nebo novější. Další informace najdete na [stránce aktualizace verze agenta pro Linux](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).|
|2|Testovací případ historie bash|Pokud je velikost historie bash v odeslaném obrázku větší než 1 kilobajt (KB), zobrazí se chyba. Velikost je omezená na 1 KB, aby se zajistilo, že v souboru historie bash nejsou zachyceny žádné potenciálně citlivé informace.|Pokud chcete tento problém vyřešit, připojte VHD k jakémukoli jinému pracovnímu virtuálnímu počítači a proveďte požadované změny (například odstraňte soubory historie *. bash* ), aby se snížila velikost menší nebo rovna 1 KB.|
|3|Požadovaný parametr jádra pro testovací případ|Tato chyba se zobrazí, pokud hodnota pro **konzolu** není nastavená na **ttyS0**. Kontrolou spusťte následující příkaz:<br>`cat /proc/cmdline`|Nastavte hodnotu **Console** na **ttyS0**a odešlete požadavek znovu.|
|4|Testovací případ intervalu ClientAlive|Pokud výsledek sady nástrojů poskytne neúspěšný výsledek pro tento testovací případ, existuje nevhodná hodnota pro **ClientAliveInterval**.|Nastavte hodnotu parametru **ClientAliveInterval** na hodnotu menší nebo rovnou hodnotě 235 a potom požadavek odešlete znovu.|

### <a name="windows-test-cases"></a>Testovací případy Windows

V následující tabulce jsou uvedeny testovací případy systému Windows, které sada nástrojů spustí, spolu s popisem ověření testu:

|Scénář |Testovací případy|Description|
|---|---|---|---|
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

Pokud jste pocházeli při jakémkoli selhání s předchozími testovacími případy, přečtěte si sloupec **Popis** v tabulce pro řešení. Pokud potřebujete další informace, obraťte se na tým podpory. 

## <a name="data-disk-size-verification"></a>Ověření velikosti datového disku

Pokud je velikost jakékoli žádosti odeslané s datovým diskem větší než 1023 gigabajtů (GB), požadavek se neschválí. Toto pravidlo se vztahuje na systémy Linux a Windows.

Požadavek odešlete znovu o velikost menší nebo rovnou 1023 GB.

## <a name="os-disk-size-validation"></a>Ověřování velikosti disku s operačním systémem

Omezení velikosti disku operačního systému najdete v následujících pravidlech. Když odešlete jakoukoli žádost, ověřte, jestli je velikost disku operačního systému v rámci omezení pro Linux nebo Windows.

|Operační systém|Doporučená velikost VHD|
|---|---|
|Linux|30 GB až 1023 GB|
|Windows|30 GB až 250 GB|

Protože virtuální počítače umožňují přístup k základnímu operačnímu systému, zajistěte, aby velikost virtuálního pevného disku byla pro virtuální pevný disk dostatečně velká. Vzhledem k tomu, že disky nejsou rozšiřitelné bez výpadků, použijte velikost disku z 30 GB do 50 GB.

|Velikost virtuálního pevného disku|Skutečná obsazená velikost|Řešení|
|---|---|---|
|>500 tebibytes (TiB)|neuvedeno|Obraťte se na tým podpory se schválením výjimky.|
|250-500 TiB|Rozdíl velikosti objektu BLOB v >200 gibibajtech (GiB)|Obraťte se na tým podpory se schválením výjimky.|

> [!NOTE]
> Větší velikosti disků účtují vyšší náklady a během procesu instalace a replikace se bude účtovat zpoždění. Z důvodu tohoto zpoždění a nákladů může tým podpory vyhledat odůvodnění pro schválení výjimky.

## <a name="wannacry-patch-verification-test-for-windows"></a>Test ověření WannaCry opravy pro Windows

Aby se zabránilo potenciálnímu útoku souvisejícímu s virem WannaCry, zajistěte, aby všechny požadavky na image Windows byly aktualizované pomocí nejnovější opravy.

Chcete-li zjistit podrobnosti o operačním systému pro opravu Windows serveru a minimální verzi, kterou bude podporovat, přečtěte si následující tabulku: 

Verzi souboru bitové kopie lze ověřit z `C:\windows\system32\drivers\srv.sys` nebo `srv2.sys` .

> [!NOTE]
> Windows Server 2019 nemá žádné požadavky na povinnou verzi.

|Operační systém|Verze|
|---|---|
|Systém Windows obsluhuje 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|Není k dispozici|

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
|CoreOS stabilní 2079.6.0|4.19.43*|
||Beta verze 2135.3.1|4.19.50*|
||Alfa 2163.2.1|4.19.50*|
|Debian|Jessie (zabezpečení)|3.16.68 – 2|
||Jessie – porty|4.9.168-1 + deb9u3|
||Roztáhnout (zabezpečení)|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0 – 18 a deb9u1|
||Buster, SID (porty roztažení)|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>Velikost obrázku musí být v násobcích megabajtů.

Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnané na násobky 1 megabajtu (MB). Pokud váš virtuální pevný disk neodpovídá doporučené virtuální velikosti, váš požadavek může být zamítnutý.

Při převodu z nezpracovaného disku na VHD postupujte podle pokynů a ujistěte se, že velikost nezpracovaného disku je násobkem 1 MB. Další informace najdete v tématu [informace o neschválených distribucích](../../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Přístup k virtuálnímu počítači byl odepřen

Pokud při spuštění testovacích případů na virtuálním počítači postupujete mezi problémy s odepřeným přístupem, může to být kvůli nedostatečným oprávněním ke spuštění testovacích případů.

Zkontrolujte, zda je pro účet, na kterém běží vlastní testové případy, povolen správný přístup. Pokud přístup povolen není, povolte ho pro spuštění testovacích případů. Pokud nechcete povolit přístup, můžete sdílet výsledky pro samočinný test případu s týmem podpory.

## <a name="download-failure"></a>Chyba stahování
    
V následující tabulce najdete případné problémy, které vznikají při stahování image virtuálního počítače pomocí adresy URL sdíleného přístupového podpisu (SAS).

|Scénář|Chyba|Důvod|Řešení|
|---|---|---|---|
|1|Objekt BLOB se nenašel.|Virtuální pevný disk může být buď odstraněn, nebo přesunut ze zadaného umístění.|| 
|2|Používaný objekt BLOB|VHD používá jiný interní proces.|Virtuální pevný disk by měl být v používaném stavu, když ho stáhnete pomocí adresy URL SAS.|
|3|Neplatná adresa URL SAS|Přidružená adresa URL SAS pro virtuální pevný disk je nesprávná.|Získejte správnou adresu URL SAS.|
|4|Neplatný podpis|Přidružená adresa URL SAS pro virtuální pevný disk je nesprávná.|Získejte správnou adresu URL SAS.|
|6|Podmíněná hlavička HTTP|Adresa URL SAS není platná.|Získejte správnou adresu URL SAS.|
|7|Neplatný název VHD|Zkontrolujte, zda nějaké speciální znaky, například znak procenta (%) nebo uvozovky (") existují v názvu VHD.|Přejmenujte soubor VHD odebráním speciálních znaků.|

## <a name="first-mb-2048-kb-partition-only-for-linux"></a>Prvních MB (2048 KB) oddíl (pouze pro Linux)

Když odešlete VHD, ujistěte se, že první 2048 KB virtuálního pevného disku je prázdné. V opačném případě se vaše žádost nezdařila *.

>[!NOTE]
>* U určitých speciálních imagí, jako jsou ty, které jsou postavené na základních imagích Azure pro Windows a které se vycházejí z Azure Marketplace, zkontrolujeme fakturační značku a ignorujte oddíl MB, pokud se fakturační značka vyskytuje a odpovídá našim hodnotám, které jsou k dispozici.

## <a name="default-credentials"></a>Výchozí pověření

Vždy zajistěte, aby se výchozí pověření neodesílala u odeslaného virtuálního pevného disku. Přidání výchozích přihlašovacích údajů způsobí, že virtuální pevný disk bude více zranitelný vůči bezpečnostním hrozbám Místo toho vytvořte při odeslání VHD vlastní přihlašovací údaje.
  
## <a name="datadisk-mapped-incorrectly"></a>Nesprávně mapované datadisk

Pokud je žádost odeslána s více datovými disky, ale jejich pořadí není v pořadí, považuje se za problém s mapováním. Například pokud existují tři datové disky, pořadí číslování musí být *0, 1, 2*. Jakékoli jiné pořadí se považuje za problém s mapováním.

Odešlete žádost znovu se správným pořadím datových disků.

## <a name="incorrect-os-mapping"></a>Nesprávné mapování OS

Když je vytvořen obrázek, může být namapován na nebo přiřazen nesprávný popisek operačního systému. Pokud například při vytváření image vyberete jako součást názvu operačního systému možnost **Windows** , disk s operačním systémem by měl být nainstalován pouze se systémem Windows. Stejný požadavek platí pro Linux.

## <a name="vm-not-generalized"></a>Virtuální počítač není zobecněný.

Pokud se mají znovu použít všechny image z Azure Marketplace, musí být virtuální pevný disk s operačním systémem zobecněný.

* Pro **Linux**následující proces GENERALIZUJE virtuální počítač Linux a znovu ho nasadí jako samostatný virtuální počítač.

  V okně SSH zadejte následující příkaz: `sudo waagent -deprovision+user`

* Pro **Windows**můžete zobecnit bitové kopie systému Windows pomocí nástroje `sysreptool` .

Další informace o tomto nástroji najdete v tématu [Přehled nástroje pro přípravu systému (Sysprep)]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>Chyby datadisk

Pro řešení chyb souvisejících s datovým diskem použijte následující tabulku:

|Chyba|Důvod|Řešení|
|---|---|---|
|`DataDisk- InvalidUrl:`|K této chybě mohlo dojít z důvodu neplatného čísla logické jednotky (LUN) při odeslání nabídky.|Ověřte, že je pořadí čísel LUN pro datový disk v partnerském centru.|
|`DataDisk- NotFound:`|K této chybě může dojít kvůli tomu, že datový disk není umístěný na zadané adrese URL SAS.|Ověřte, že je datový disk umístěný na adrese URL SAS, která je zadaná v požadavku.|

## <a name="remote-access-issue"></a>Problém se vzdáleným přístupem

Pokud v imagi Windows není povolená možnost protokol RDP (Remote Desktop Protocol) (RDP), zobrazí se tato chyba. 

Než je odešlete, povolte přístup k protokolu RDP pro image Windows.

## <a name="bash-history-failed"></a>Nepovedlo se Bashovat historii

Tato chyba se zobrazí, pokud je velikost historie bash v odeslaném obrázku větší než 1 kilobajt (KB). Velikost je omezená na 1 KB, aby se zajistilo, že v souboru historie bash nejsou zachyceny žádné potenciálně citlivé informace.

Níže jsou uvedené kroky, jak odstranit historii bash.

Krok 1. Nasaďte virtuální počítač a klikněte na možnost spustit příkaz na Azure Portal.
![Spustit příkaz na Azure Portal](./media/vm-certification-issues-solutions-3.png)

Krok 2. Vyberte první možnost "RunShellScript" a spusťte následující příkaz.

Příkaz: CAT/dev/null > ~/.bash_history && History-c " ![ bash historie příkazu on Azure Portal](./media/vm-certification-issues-solutions-4.png)

Krok 3. Po úspěšném provedení příkazu restartujte virtuální počítač.

Krok 4: Generalizujte virtuální počítač, povezměte virtuální pevný disk image a zastavte virtuální počítač.

Krok 5.     Re-Submit generalizovaná bitová kopie.

## <a name="requesting-exceptions-custom-templates-on-vm-images-for-selective-tests"></a>Požadavky na výjimky (vlastní šablony) na image virtuálních počítačů pro selektivní testy

Vydavatelé se můžou obrátit na žádosti o výjimky pro několik testů provedených během certifikace virtuálního počítače. Výjimky jsou k dispozici ve výjimečných případech, pokud Vydavatel poskytuje důkaz pro podporu žádosti.
Certifikační tým si vyhrazuje právo odepřít nebo schválit výjimky v jakémkoli okamžiku.

V následujících částech budeme pohovořit o hlavních scénářích, kde jsou požadovány výjimky a jak vyžádat výjimku.

Scénáře pro výjimku

Existují tři scénáře/případy, kdy vydavatel obecně požadují tyto výjimky. 

* **Výjimka pro jeden nebo více testovacích případů:** Vydavatelé se můžou spojit s výjimkami žádosti o [podporu vydavatele na Marketplace](https://aka.ms/marketplacepublishersupport) pro testovací případy. 

* **Zamčené virtuální počítače/žádný kořenový přístup:** Někteří vydavatelé mají scénáře, kdy je potřeba, aby virtuální počítače byly uzamčené, protože mají na virtuálním počítači nainstalovaný software, jako jsou třeba brány firewall. 
       V takovém případě mohou vydavatelé stáhnout [Nástroj certifikovaný test](https://aka.ms/AzureCertificationTestTool) a poskytnout zprávu na [webu Marketplace podpora vydavatelů](https://aka.ms/marketplacepublishersupport) .


* **Vlastní šablony:** Někteří vydavatelé publikují image virtuálních počítačů, které pro nasazení virtuálních počítačů vyžadují vlastní šablonu ARM. V takovém případě se vydavatelé požadují, aby poskytovali vlastní šablony v rámci [podpory vydavatele na webu Marketplace](https://aka.ms/marketplacepublishersupport) , aby je bylo možné používat certifikační tým pro ověřování. 

### <a name="information-to-provide-for-exception-scenarios"></a>Informace, které je potřeba poskytnout pro scénáře výjimek

Vydavatelé se musí obrátit na podporu [vydavatele na webu Marketplace](https://aka.ms/marketplacepublishersupport) , který požaduje výjimky pro výše uvedený scénář, a další následující informace:

   1.   ID vydavatele – ID vydavatele na portálu partnerského centra
   2.   ID nabídky/název – ID nabídky/název, pro který se požaduje výjimka 
   3.   ID SKU/plánu – ID/SKU nabídky virtuálních počítačů, pro které se požaduje výjimka
   4.    Verze – verze nabídky virtuálních počítačů, pro kterou je požadována výjimka
   5.   Typ výjimky – testy, uzamčený virtuální počítač, vlastní šablony
   6.   Důvod žádosti – důvod pro tuto výjimku a informace o testech, které mají být vyloučeny 
   7. Časová osa – datum, do kterého byla tato výjimka požadována 
   8.   Příloha – připojte všechny dokumenty legitimace podle důležitosti. Pro zamčené virtuální počítače připojte testovací sestavu a pro vlastní šablony zadejte vlastní šablonu ARM jako přílohu. Nepovedlo se připojit sestavu pro zamčené virtuální počítače a vlastní šablonu ARM pro vlastní šablony. výsledkem bude odepření žádosti.


## <a name="next-steps"></a>Další kroky

Pokud máte dotazy nebo připomínky ke zlepšení, obraťte se na [podporu partnerského centra](https://partner.microsoft.com/support/v2/?stage=1).
