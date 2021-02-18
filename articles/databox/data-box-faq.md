---
title: Microsoft Azure Data Box – nejčastější dotazy | Microsoft Docs
description: Obsahuje nejčastější dotazy a odpovědi pro Azure Data Box cloudové řešení, které umožňuje přenášet velké objemy dat do Azure.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/17/2021
ms.author: alkohli
ms.openlocfilehash: 46a60642e177a2bfb9e8fb996427ea45f6777468
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652781"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: nejčastější dotazy

Hybridní řešení Microsoft Azure Data Box umožňuje odesílat do Azure rychle, levně a bezpečně terabajty dat prostřednictvím přenosového zařízení. Tyto nejčastější dotazy obsahují otázky a odpovědi, které se týkají používání služby Data Box na webu Azure Portal.

Otázky a odpovědi jsou uspořádané do těchto kategorií:

- O službě
- Objednání zařízení
- Konfigurace a připojení 
- Sledování stavu
- Kopírování dat 
- Dodání zařízení
- Ověření a nahrání dat 
- Podpora řetězce opatrovnictví

## <a name="about-the-service"></a>O službě

### <a name="q-what-is-azure-data-box-service"></a>Otázka: Co je služba Azure Data Box? 
A.  Služba Azure Data Box je určená pro příjem dat offline. Tato služba spravuje celou řadu produktů s různou kapacitou úložiště, které všechny slouží k přenosu dat. 

### <a name="q-what-is-azure-data-box"></a>Otázka: Co je Azure Data Box?
A. Azure Data Box umožňuje rychlý, levný a zabezpečený přenos terabajtů dat do Azure. Zařízení Data Box si můžete objednat na webu Azure Portal. Společnost Microsoft se dodává s úložným zařízením o kapacitě 80 TB přes místní dopravce. 

Jakmile vám zařízení přijde, můžete ho rychle nastavit v místním webovém uživatelském rozhraní. Zkopírujte data ze serverů do zařízení nebo ze zařízení na servery a odešlete zařízení zpět do Azure. V případě importu v datovém centru Azure se vaše data automaticky nahrají ze zařízení do Azure. Celý proces se od začátku do konce sleduje ve službě Data Box na webu Azure Portal.

### <a name="q-when-should-i-use-data-box"></a>Otázka: Kdy mám použít Data Box?
A. Pokud máte 40 až 500 TB dat, která chcete přenést do systému Azure nebo z něj, doporučujeme vám použít Data Box. Pro velikosti dat < 40 TB použijte Data Box Disk a pro velikosti dat > 500 TB, zaregistrujte se [data box Heavy](data-box-heavy-overview.md).

### <a name="q-what-is-the-price-of-data-box"></a>Otázka: Jaká je cena zařízení Data Box?
A. Zařízení Data Box je k dispozici na 10 dnů za stanovený poplatek. Když při vytváření objednávky na webu Azure Portal vyberete model produktu, zobrazí se poplatek za zařízení. Platí také pro službu Azure Storage za standardní poplatky za expedici a poplatky. Objednávky exportu následují podobný cenový model jako u objednávek pro import, i když můžou platit další poplatky za výstup. 

Pokud potřebujete další informace, přečtěte si [Azure Data box ceny](https://azure.microsoft.com/pricing/details/storage/databox/) a [náklady na výstup](https://azure.microsoft.com/pricing/details/bandwidth/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>Otázka: Jaký maximální objem dat můžu naráz přenést prostřednictvím zařízení Data Box?
A. Data Box má hrubou kapacitu 100 TB a využitelnou kapacitu 80 TB. Pomocí Data Box můžete přenést až 80 TB dat. Pokud chcete přenést více dat, musíte objednat více zařízení.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>Otázka: Jak zjistím, jestli je Data Box dostupný v mojí oblasti? 
A.  Informace o tom, které země nebo oblasti jsou Data Box k dispozici, najdete v části [dostupnost oblasti](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>Otázka: V jakých oblastech můžu k ukládání dat použít Data Box?
A. Data Box se podporuje pro všechny oblasti v USA, Západní Evropa, Severní Evropa, Francie, Spojené království, Japonsko, Austrálie a Kanada. Další informace najdete v [oblasti dostupnost oblasti](data-box-overview.md#region-availability).

### <a name="q-how-can-i-import-source-data-at-my-location-in-a-particular-country-to-an-azure-region-in-a-different-countryregion-or-export-data-from-an-azure-region-in-one-country-to-a-different-countryregion"></a>Otázka: Jak můžu importovat zdrojová data v dané zemi do konkrétní země do oblasti Azure v jiné zemi nebo oblasti nebo exportovat data z oblasti Azure v jedné zemi do jiné země nebo oblasti?

Data Box podporuje příjem dat nebo výstup pouze v rámci stejné země nebo oblasti jako cíl a nepřekračuje žádné mezinárodní hranice. Jediná výjimka je určena pro objednávky v Evropské unii (EU), kde se datová pole dají dodávat do a z libovolné země nebo oblasti EU.

Pokud jste například ve scénáři importu měli zdrojová data v Kanadě, že jste chtěli přejít na účet úložiště Azure Západní USA, můžete to dosáhnout následujícím způsobem:

1. V Kanadě zvolte účet úložiště v Kanadě, Data Box objednat. Zařízení se dodává z datacentra Azure v Kanadě po dodací adresu (v Kanadě), která je k dispozici během vytváření objednávky.

2. Jakmile se Prem data zkopíruje do Data Box, vraťte zařízení do datacentra Azure v Kanadě. Data v Data Box pak se nahrají do cílového účtu úložiště v oblasti Kanada Azure zvolené během vytváření objednávky.

3. Pak můžete použít nástroj jako AzCopy ke zkopírování dat do účtu úložiště v Západní USA. Tento krok se započítá se [standardními](https://azure.microsoft.com/pricing/details/storage/) [poplatky za úložiště a šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/) , které nejsou zahrnuté do data box fakturace.

### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>Otázka: Jak můžu obnovit data v případě, že dojde k chybě celé oblasti?

A. V extrémních situacích, kdy dojde ke ztrátě oblasti z důvodu významné havárie, může společnost Microsoft zahájit místní převzetí služeb při selhání. V tomto případě se nevyžaduje žádná akce v této části. Vaše objednávka bude splněna v oblasti převzetí služeb při selhání, pokud se nachází v rámci stejné země nebo obchodní hranice. Některé oblasti Azure ale nemají spárované oblasti ve stejné geografické ani obchodní hranici. Pokud dojde k havárii v některé z těchto oblastí, bude nutné znovu vytvořit pořadí Data Box z jiné oblasti, která je k dispozici, a zkopírovat data do Azure v nové oblasti. Další informace viz [Provozní kontinuita a zotavení po havárii (BCDR): Spárované oblasti Azure](../best-practices-availability-paired-regions.md).

### <a name="q-who-should-i-contact-if-i-come-across-any-issues-with-data-box"></a>Otázka: Koho se mám obrátit v případě problémů s Data Box?
A. Pokud dodržíte všechny problémy s Data Box, [obraťte](data-box-disk-contact-microsoft-support.md)se na podpora Microsoftu.

### <a name="q-i-lost-my-data-box-is-there-a-lost-device-charge"></a>Otázka: Ztratil jsem Data Box. Dojde ke ztrátě poplatků za zařízení?
A. Ano. Ztracené nebo poškozené zařízení je zpoplatněné. Tento poplatek se vztahuje na [stránku s cenami](https://azure.microsoft.com/pricing/details/storage/databox/) a na [produktové podmínkami](https://www.microsoft.com/licensing/product-licensing/products).


## <a name="order-device"></a>Objednání zařízení

### <a name="q-how-do-i-get-data-box"></a>Otázka: Jak získám Data Box? 
A.  Pokud chcete získat Azure Data Box, přihlaste se na webu Azure Portal a objednejte si zařízení Data Box. Zadejte svoje kontaktní údaje a podrobnosti o oznámení. Po vystavení objednávky vám Data Box dodáme do 10 dnů (podle dostupnosti). Další informace nejdete v článku o [objednání zařízení Data Box](data-box-deploy-ordered.md).

### <a name="q-i-couldnt-create-a-data-box-order-in-the-azure-portal-why"></a>Otázka: Nemohu vytvořit Data Box pořadí v Azure Portal. Proč?
A. Pokud nemůžete vytvořit objednávku Data Box, dojde k problému s vaším typem nebo přístupem k předplatnému.

Zkontrolujte si předplatné. Data Box je dostupný jenom zákazníkům se smlouvou Enterprise (EA) nebo poskytovatelům Cloud Solution Provider (CSP). Pokud některý z těchto typů předplatných nemáte, kontaktujte podpora Microsoftu k upgradu vašeho předplatného.

Pokud máte podporovaný typ předplatného, zkontrolujte svou úroveň přístupu k předplatnému. K vytvoření objednávky musíte být přispěvatel nebo vlastník předplatného.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>Otázka: Jak dlouho bude pořadí trvat, než se v objednávce vytvoří data nahraná do Azure?

A. Následující Odhadované doby realizace pro každou fázi zpracování objednávek vám poskytnou dobrý nápad na to, co očekávat.  

Tyto doby realizace jsou *odhady*. Čas pro každou fázi zpracování objednávky je ovlivněn zatížením datového centra, souběžnými objednávkami a dalšími podmínkami v oblasti životního prostředí.

**Odhadované doby realizace Data Boxho pořadí:**

1. Pořadí Data Box: několik minut, z portálu
2. Přidělení a příprava zařízení: 1-2 pracovních dní v závislosti na dostupnosti inventáře a dalších objednávkách, které čekají na splnění
3. Doprava: 2–3 pracovní dny
4. Kopírování dat na zákaznických webech: závisí na povaze dat, velikosti a počtu souborů.
5. Vrácení: 2–3 pracovní dny
6. Zpracovává se zařízení v datacentru: 1-2 pracovních dnů, s výjimkou dalších objednávek, které čekají na zpracování.
7. Nahrávání dat do Azure: začíná ihned po dokončení zpracování a připojení zařízení. Doba nahrávání závisí na charakteru dat, velikosti a počtu souborů.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-cant-create-any-additional-orders-why"></a>Otázka: Objednal(a) jsem několik zařízení Data Box. Nemůžu vytvořit další objednávky. Proč?
A. Povolujeme maximálně pět aktivních objednávek na předplatné na hranici obchodu (kombinace země a oblasti vybraná). Pokud potřebujete objednat další zařízení, obraťte se na podporu Microsoftu a požádejte u svého předplatného o zvýšení limitu.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>Otázka: Když se pokouším vytvořit objednávku, zobrazí se mi oznámení, že služba Data Box není k dispozici. Co to znamená?
A. Služba Data Box není k dispozici pro kombinaci země a oblasti, kterou jste vybrali. Pokud tuto kombinaci změníte, pravděpodobně budete mít službu Data Box k dispozici. Seznam oblastí, kde je služba dostupná, najdete v části o [regionální dostupnosti služby Data Box](data-box-overview.md#region-availability).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>Otázka: Před několika dny jsem si objednal(a) zařízení Data Box. Kdy mi Data Box přijde?
A. Když vystavíte objednávku, zkontrolujeme, jestli je pro objednávku zařízení k dispozici. Pokud tomu tak je, expedujeme ho do 10 dnů. Může se stát, že v určitých obdobích se poptávka zvýší. V takovém případě zařadíme vaši objednávku do fronty. Změnu jejího stavu můžete sledovat na webu Azure Portal. Pokud objednávku nevyřídíme do 90 dnů, bude automaticky zrušena.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>Otázka: Naplnili jsem Data Box daty a je potřeba objednat další. Existuje nějaký rychlý způsob, jak tuto objednávku vytvořit?
A. Můžete svoji předchozí objednávku naklonovat. Naklonováním se vytvoří stejná objednávka, jako byla ta předchozí. Podrobnosti této objednávky však můžete upravit. Nebudete tedy muset znovu zadávat adresu, kontaktní údaje a podrobnosti o oznámení. Klonování je povoleno pouze pro objednávky importu.

## <a name="configure-and-connect"></a>Konfigurace a připojení

### <a name="q-how-do-i-unlock-the-data-box"></a>Otázka: Jak se Data Box odemyká? 
A.  Na webu Azure Portal přejděte k objednávce zařízení Data Box a otevřete **Detaily zařízení**. Zkopírujte si heslo pro odemčení. Toto heslo použijte při přihlášení k místnímu webovému uživatelskému rozhraní Data Boxu. Další informace najdete v [kurzu věnovanému rozbalení, zapojení a připojení k Azure Data Boxu](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>Otázka: Můžu se k zařízení Data Box připojit a kopírovat do něj data z hostitelského počítače s Linuxem?
A.  Ano. Data Box můžete připojit ke klientům SMB a NFS. Další informace získáte, když přejdete na seznam [podporovaných operačních systémů](data-box-system-requirements.md) hostitelského počítače.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Otázka: Data Box jsme odeslali, ale rozhodli jsme se objednávku zrušit. Proč není dostupné tlačítko pro zrušení?
A.  Objednávka se dá zrušit po objednání zařízení Data Box, dokud není objednávka zpracovaná. Zpracovanou objednávku zařízení Data Box nemůžete zrušit. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>Otázka: Můžu Data Box současně připojit k více hostitelským počítačům, ze kterých chci přenášet data?
A. Ano. K zařízení Data Box může být připojených více hostitelských počítačů, ze kterých budete přenášet data, a několik úloh kopírování může běžet současně. Další informace nejdete v [kurzu o kopírování dat do služby Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>Otázka: Můžu se k přenosu dat připojit k rozhraní 10 GbE na Data Box?
A. Ano. Rozhraní 10 GbE lze v Data Box připojit ke kopírování dat současně. Další informace o tom, jak kopírovat data, najdete v tématu [kurz: kopírování dat do Azure Data box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why?
A.

### Q. I could not set up Data Box using a Static IP address. Why?
A.

### Q. I could not set up Data Box on a private network. Why?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>Otázka: Na předním ovládacím panelu svítí dioda, která signalizuje chybu systému. Co bych měl/a dělat?
A. Pod tlačítkem napájení na přední straně Data Boxu jsou dvě indikátory LED. Nejhorším světlem je indikátor systémové chyby, který označuje, jestli je váš systém v pořádku.

Indikátor systémové chyby, který je červený, může ukazovat na některý z následujících problémů:
- Selhání ventilátoru
- Teplota procesoru je vysoká.
- Teplota základní desky je vysoká.
- Duální chyba modulu vložené paměti (DIMM) Chyba při připojování kódu (ECC)

Proveďte tyto kroky:
1. Ověřte, zda ventilátor funguje.
2. Přesuňte zařízení do umístění s větším tokem.

Pokud je indikátor chyby systému stále zapnutý, obraťte se na [Podpora Microsoftu](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why"></a>Otázka: Nemám přístup k heslu pro odemčení Data Boxu na webu Azure Portal. Proč?
A. Pokud nemůžete získat přístup k heslu pro odemknutí v Azure Portal, ověřte oprávnění ve vašem předplatném a účtu úložiště. Ověřte, že na úrovni skupiny prostředků máte oprávnění přispěvatele nebo vlastníka. Abyste mohli zobrazit přihlašovací údaje pro přístup, musíte mít aspoň Data Box oprávnění role operátora.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>Otázka: Podporuje se Data Box konfigurace kanálu portů? Jak se funkce MPIO týká?
A. Na Data Box nepodporujeme konfiguraci kanálu portů, konfiguraci funkce Multipath v/v (MPIO) nebo konfiguraci sítě vLAN.

## <a name="track-status"></a>Sledování stavu

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>Otázka: Jak můžu sledovat Data Box od okamžiku vystavení objednávky až do zpětného odeslání zařízení? 
A.  Stav objednávky Data Boxu můžete sledovat na webu Azure Portal. Při vytváření objednávky budete vyzváni k zadání e-mailové zprávy s oznámením. Pokud jste ho zadali, budete upozorněni e-mailem o všech změnách stavu objednávky. Další informace o [konfiguraci e-mailových adres pro oznámení](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>Otázka: Jak můžu zařízení vrátit? 
A.  Microsoft zobrazuje expediční štítek na displeji s elektronickým inkoustem. Pokud se expediční štítek nezobrazuje na displeji elektronického inkoustu, přečtěte si **přehled > stáhnout expediční štítek**. Stáhněte a vytiskněte popisek, vložte popisek do značky Clear plast na zařízení a uvolněte ho v umístění přepravce. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>Otázka: Přišlo mi e-mailem oznámení, že zařízení dorazilo do datacentra Azure. Jak zjistím, jestli probíhá nahrávání dat?
A. Na webu Azure Portal přejděte k objednávce Data Boxu a pak přejděte na **Přehled**. Pokud se data začala nahrávat do Azure, zobrazí se v pravém podokně průběh kopírování. 

## <a name="migrate-data"></a>Migrace dat

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>Otázka: Kolik dat se vejde do zařízení Data Box?  
A.  Data Box má využitelné úložiště o kapacitě 80 TB. Jedno zařízení Data Box můžete použít pro data o velikosti 40–80 TB. Pro větší velikosti dat až do 500 TB můžete objednat více Data Boxch zařízení. Pokud data přesahují 500 TB, zaregistrujte se do služby Data Box Heavy.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>Otázka: Jak velké objekty blob bloku a objekty blob stránky Data Box podporuje? 
A.  Maximální velikosti se řídí omezeními služby Azure Storage. Maximální velikost objektu blob bloku je přibližně 4,768 TiB a maximální velikost objektu blob stránky je 8 TiB. Další informace najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../storage/blobs/scalability-targets.md).

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Otázka: Jak zajistím, aby byla data při přenosu zabezpečená? 
A. Data Box má implementovaných několik bezpečnostní funkcí, kterými je zařízení zabezpečené během přepravy. Jsou to například pečetě, hardwarová a softwarová detekce nedovolené manipulace a heslo pro odemčení zařízení. Další informace najdete v článku o [zabezpečení a ochraně dat ve službě Azure Data Box](data-box-security.md).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>Otázka: Jak zkopíruji data do Data Boxu? 
A.  Pokud používáte klienta SMB, můžete ke zkopírování dat do zařízení použít nástroj pro kopírování SMB, jako `Robocopy` je, `Diskboss` nebo dokonce přetahování pomocí nástroje Windows File Explorer. 

Pokud používáte klienta NSF, můžete použít [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) nebo [Ultracopier](https://ultracopier.first-world.info/). 

Další informace nejdete v [kurzu o kopírování dat do služby Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Otázka: Máte nějaké tipy pro zrychlení kopírování dat?
A.  Pokud chcete zrychlit proces kopírování:

- Použijte pro kopírování dat více streamů. Například s pomocí `Robocopy` použijte možnost s více vlákny. Další informace o přesných používaných příkazech najdete v [kurzu o kopírování dat do služby Azure Data Box a jejich ověření](data-box-deploy-copy-data.md).
- Použijte více relací.
- Místo kopírování přes sdílenou síťovou složku (kde rychlosti sítě může omezit rychlost kopírování) uložte data místně na počítači, ke kterému je Data Box připojená.
- Proveďte srovnávací testy výkonu počítače, který se používá ke kopírování dat. Stáhněte a použijte [ `Bluestop` `FIO` Nástroj](https://ci.appveyor.com/project/axboe/fio) k testování výkonu hardwaru serveru. Vyberte nejnovější sestavení x86 nebo x64, vyberte kartu **artefakty** a Stáhněte soubor MSI.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM's disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>Otázka: Můžu se zařízením Data Box používat více účtů úložiště?
A.  Ano. Data Box podporuje maximálně 10 účtů úložišť, úložišť pro obecné účely, klasických úložišť nebo úložišť objektů blob. Podporují se horké i studené objekty blob.


## <a name="ship-device"></a>Dodání zařízení

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>Otázka: Zařízení bylo doručeno, ale zdá se, že zařízení je poškozené. Co bych měl/a dělat?
A. Pokud vaše zařízení dorazilo do poškozeného nebo se nejedná o manipulaci, zařízení nepoužívejte. [Kontaktujte podpora Microsoftu](data-box-disk-contact-microsoft-support.md) a vraťte zařízení co nejrychleji. Můžete také vystavit novou objednávku náhradního zařízení Data Box. V takovém případě se vám nebude účtovat náhrada za náhradní zařízení.

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>Otázka: Můžu Data Box objednávku vybrat? Můžu Data Box vrátit přes dopravce, který zvolím?
A. Ano. Microsoft také nabízí samostatně spravované dodávky. Při umísťování Data Box pořadí můžete zvolit možnost samostatně spravovaná dodávka. Další informace najdete v tématu [osobní spravované odeslání pro data box](data-box-portal-customer-managed-shipping.md).

### <a name="q-will-my-data-box-devices-cross-countryregion-borders-during-shipping"></a>Otázka: Budou mít zařízení Data Box během expedice ohraničení mezi země a oblast?
A. Všechna Data Box zařízení se dodávají v rámci stejné země nebo oblasti jako jejich cíl a nebudou se předávat mezi žádné mezinárodní hranice. Jedinou výjimkou jsou objednávky v Evropské unii (EU), kde se můžou zařízení dodávat do a z libovolné země nebo oblasti EU. To platí jak pro Data Box, tak pro Data Box Heavyá zařízení.

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>Otázka: Objednal (a) jsem Data Box v USA – východ, ale dostal jsem zařízení, které bylo odesláno z umístění v USA – západ. Kam se má zařízení vrátit?
A. Snažíme se co nejrychleji získat Data Box zařízení. Podáváme prioritu dodávky z datového centra nejbližšího k vašemu umístění účtu úložiště, ale odešlete zařízení z libovolného datacentra Azure, které má dostupný inventář. Váš Data Box by měl být vrácen do stejného umístění, ze kterého byl dodán, jak je zobrazeno v expedičním štítku.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>Otázka: Na displeji s elektronickým inkoustem se nezobrazuje expediční štítek pro vrácení zásilky. Co bych měl/a dělat?
A. Pokud zobrazení E-Ink nezobrazuje popisek dodávky zpět, proveďte následující kroky:
- Odstraňte starý expediční štítek a všechny ostatní štítky, které se týkají minulé přepravy.
- Na webu Azure Portal přejděte ke své objednávce. Přejděte na **Přehled** > **Stáhnout expediční štítek**. Další informace najdete v článku o [stažení expedičního štítku](data-box-portal-admin.md#download-shipping-label).
- Vytiskněte expediční štítek a vložte ho do průhledného plastového obalu, který je připevněný k zařízení. 
- Expediční štítek musí být dobře viditelný. 

### <a name="q-how-is-my-data-protected-during-transit"></a>Otázka: Jak jsou data během přenosu chráněná? 
A.  Během přepravy jsou data v zařízení Data Box chráněná následujícími funkcemi:
 - Disky zařízení Data Box používají šifrování AES-256 a jsou zašifrované. 
 - Zařízení je zamčené a k jeho odemčení je potřeba zadat heslo, aby byl možný přístup k datům.
Další informace najdete v části o [bezpečnostních funkcích zařízení Data Box](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-for-my-import-order-and-shut-down-the-device-can-i-still-add-more-data-to-the-data-box"></a>Otázka: Dokončil (a) jsem přípravu na expedici pro svůj způsob importu a vypnutí zařízení. Můžu ještě do Data Box přidat další data?
A. Ano. Zařízení můžete znovu zapnout a přidat do něj další data. Až data nakopírujete, budete muset znovu spustit **přípravu k odeslání**.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>Otázka: Zobrazil jsem jsem moje zařízení a nespouští se? Návody zařízení odeslat zpátky?
A. Pokud se vaše zařízení nespouští, v Azure Portal pokračujte na objednávku. Stáhněte si expediční štítek a připojte ho k zařízení. Další informace najdete v článku o [stažení expedičního štítku](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Ověření a nahrání

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>Otázka: Jak brzy mohu získat přístup k datům v Azure, jakmile Data Box dodali zpět? 
A.  Jakmile se u objednávky zařízení **Data Copy** zobrazí stav **dokončeno**, měli byste mít přístup ke svým datům.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Otázka: Kde v Azure se moje data po nahrání nachází?
A.  Po zkopírování dat do Data Box v závislosti na tom, jestli data blokují objekty blob nebo objekty blob stránky nebo soubory Azure, se data nahrají na jednu z následujících cest v účtu Azure Storage:
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  Alternativně můžete přejít na účet služby Azure Storage v Azure Portal a přejít tam.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Otázka: Zjistil(a) jsem, že jsem nedodržel(a) požadavky Azure na názvy kontejnerů. Znamená to, že nahrání mých dat do Azure se nezdaří?
A.  Pokud mají názvy kontejnerů velká písmena, budou tyto názvy automaticky převedeny na malá písmena. Pokud názvy nevyhovují z jiných důvodů (speciální znaky, jiné jazyky atd.), nahrání se nepodaří. Další informace o pojmenování sdílených složek, kontejnerů a souborů najdete v těchto pokynech:
- [Pojmenování sdílených složek a odkazování na ně](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Zvyklosti u objektů blob bloku a objektů blob stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>Otázka: Jak ověřím data, která zkopíruji do zařízení Data Box?
A.  Když po zkopírování dat spustíte funkci **Připravit k odeslání**, data se ověří. Při ověřování generuje Data Box seznam souborů a kontrolní součty dat. Můžete si stáhnout seznam souborů a ověřit seznam souborů ve zdrojových datech. Další informace najdete v popisu [přípravy k odeslání](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-return-the-data-box"></a>Otázka: Co se stane s daty po vrácení Data Box?
A.  Jakmile se data zkopírují do Azure, budou z disků zařízení Data Box bezpečně vymazána podle pokynů normy NIST SP 800-88 Revision 1. Další informace najdete v části o [vymazání dat ze zařízení Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Sestava auditování

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Jak služba Azure Data Box podporuje řetězec opatrovnictví?
A.  Služba Azure Data Box nativně nabízí sestavy, které můžete použít k evidenci řetězce opatrovnictví. Protokoly auditu a kopírování jsou k dispozici ve vašem účtu úložiště v Azure a po dokončení objednávky si na webu Azure Portal můžete [stáhnout historii objednávky](data-box-portal-admin.md#download-order-history).


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Jaký typ sestav je k dispozici pro řetězec opatrovnictví?
A.  Řetězec opatrovnictví podporují následující sestavy:

- Přenosová logistika ze zdroje UPS.
- Protokolování zapnutí a přístupu uživatelů ke sdíleným složkám
- Soubor kusovníku nebo manifestu s 64 bitovou cyklickou redundantní kontrolou (CRC-64) nebo kontrolní součet pro každý soubor, který byl úspěšně přijat do Data Box.
- Sestava souborů, které se nepodařilo nahrát do účtu Azure Storage
- Sanitarizace zařízení Data Box (podle norem NIST 800 88R1) po zkopírování dat do účtu Azure Storage.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Jsou k dispozici protokoly sledování nosné frekvence (ze zdroje UPS)? 
A.  Protokoly dopravců o sledování zásilky jsou zaznamenané v historii objednávky Data Boxu. Tuto sestavu máte k dispozici po vrácení zařízení do datacentra Azure a vymazání dat z disků zařízení. Pro okamžité potřeby můžete také přejít přímo na web dopravce s číslem sledování objednávky a získat informace o sledování.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Můžu dopravit Data Box do datacentra Azure? 
A.  No. Pokud jste zvolili spravované odeslání Microsoftem, nemůžete data přenést. Datacenter Azure v současné době nepřijímá doručování Data Box od zákazníků nebo od jiných dopravců než ze zdroje UPS.

Pokud jste zvolili samoobslužné expedici, můžete Data Box z datového centra Azure vybrat nebo odpustit.


## <a name="next-steps"></a>Další kroky

- Přečtěte si [systémové požadavky služby Data Box](data-box-system-requirements.md).
- Seznamte se s [omezeními služby Data Box](data-box-limits.md).
- Rychle nasaďte [Azure Data Box](data-box-quickstart-portal.md) na webu Azure Portal.