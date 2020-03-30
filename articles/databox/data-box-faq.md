---
title: Microsoft Azure Data Box – nejčastější dotazy | Microsoft Docs
description: Obsahuje často kladené otázky a odpovědi pro Azure Data Box, cloudové řešení, které vám umožní přenášet velké množství dat do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 09/03/2019
ms.author: alkohli
ms.openlocfilehash: 73256aef19a03c4c971be5fc9e69f988ef5a831a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438611"
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
A. Azure Data Box umožňuje rychlý, levný a bezpečný přenos terabajtů dat do Azure. Zařízení Data Box si můžete objednat na webu Azure Portal. Společnost Microsoft vám prostřednictvím regionálního operátora dodává úložné zařízení o využitelné kapacitě 80 TB. 

Jakmile vám zařízení přijde, můžete ho rychle nastavit v místním webovém uživatelském rozhraní. Zkopírujte data ze serverů do zařízení a pošlete ho zpět do Azure. V datacentru Azure se vaše data nahrají ze zařízení do Azure automaticky. Celý proces se od začátku do konce sleduje ve službě Data Box na webu Azure Portal.

### <a name="q-when-should-i-use-data-box"></a>Otázka: Kdy mám použít Data Box?
A. Data Box pro vás bude výhodný, pokud máte 40–500 TB dat, která chcete přenést do Azure. U velikostí dat < 40 TB použijte disk datové schránky a pro velikosti dat > 500 TB zaregistrujte si [data box heavy](data-box-heavy-overview.md).

### <a name="q-what-is-the-price-of-data-box"></a>Otázka: Jaká je cena zařízení Data Box?
A. Zařízení Data Box je k dispozici na 10 dnů za stanovený poplatek. Když při vytváření objednávky na webu Azure Portal vyberete model produktu, zobrazí se poplatek za zařízení. Také dodání je zdarma, ale platí se za úložiště Azure. Další informace nejdete v článku o [cenách služeb Azure Data Box](https://azure.microsoft.com/pricing/details/storage/databox/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>Otázka: Jaký maximální objem dat můžu naráz přenést prostřednictvím zařízení Data Box?
A. Data Box má hrubou kapacitu 100 TB a využitelnou kapacitu 80 TB. Pomocí datové schránky můžete přenášet až 80 TB dat. Pokud chcete přenést více dat, musíte objednat více zařízení.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>Otázka: Jak zjistím, jestli je Data Box dostupný v mojí oblasti? 
A.  Informace o tom, které země nebo oblasti je k dispozici, naleznete v [části Dostupnost oblasti](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>Otázka: V jakých oblastech můžu k ukládání dat použít Data Box?
A. Datová schránka je podporována pro všechny oblasti v USA, západní Evropě, severní Evropě, Francii, Velké Británii, Japonsku, Austrálii a Kanadě. Další informace najdete v části [Regionální dostupnost](data-box-overview.md#region-availability).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>Otázka: Na koho se mám obrátit v případě potíží s Data Boxem?
A. V případě potíží s Data Boxem se prosím [obraťte na podporu Microsoftu](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-have-lost-my-data-box-is-there-a-lost-device-charge"></a>Otázka: Ztratil jsem datovou schránku. Došlo ke ztrátě zařízení poplatek?
A. Ano. Dojde ke ztrátě nebo poškození zařízení. Tento poplatek je uveden na [stránce Ceny,](https://azure.microsoft.com/pricing/details/storage/databox/) stejně jako v [produktových podmínkách služby](https://www.microsoft.com/licensing/product-licensing/products).


## <a name="order-device"></a>Objednání zařízení

### <a name="q-how-do-i-get-data-box"></a>Otázka: Jak získám Data Box? 
A.  Pokud chcete získat Azure Data Box, přihlaste se na webu Azure Portal a objednejte si zařízení Data Box. Zadejte svoje kontaktní údaje a podrobnosti o oznámení. Po vystavení objednávky vám Data Box dodáme do 10 dnů (podle dostupnosti). Další informace nejdete v článku o [objednání zařízení Data Box](data-box-deploy-ordered.md).

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>Otázka: Nemůžu na webu Azure Portal vytvořit objednávku na Data Box. Čím to může být?
A. Pokud vám nejde vystavit objednávka zařízení Data Box, může to být typem předplatného nebo přístupem. 

Zkontrolujte si předplatné. Data Box je dostupný jenom zákazníkům se smlouvou Enterprise (EA) nebo poskytovatelům Cloud Solution Provider (CSP). Pokud máte jiný typ předplatného, kontaktujte podporu Microsoftu a předplatné upgradujte.

Pokud máte podporovaný typ předplatného, zkontrolujte svou úroveň přístupu k předplatnému. K vytvoření objednávky musíte být přispěvatel nebo vlastník předplatného.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>Otázka: Objednal(a) jsem několik zařízení Data Box. Nemůžu vytvářet další objednávky. Čím to může být?
A. U každého předplatného je povolených maximálně 5 aktivních objednávek ve stejné obchodní oblasti (vybraná kombinace země a oblasti). Pokud potřebujete objednat další zařízení, obraťte se na podporu Microsoftu a požádejte u svého předplatného o zvýšení limitu.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>Otázka: Když se pokouším vytvořit objednávku, zobrazí se mi oznámení, že služba Data Box není k dispozici. Co to znamená?
A. Znamená to, že pro vámi vybranou kombinaci země a oblasti není služba Data Box k dispozici. Pokud tuto kombinaci změníte, pravděpodobně budete mít službu Data Box k dispozici. Seznam oblastí, kde je služba dostupná, najdete v části o [regionální dostupnosti služby Data Box](data-box-overview.md#region-availability).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>Otázka: Před několika dny jsem si objednal(a) zařízení Data Box. Kdy mi Data Box přijde?
A. Když vystavíte objednávku, zkontrolujeme, jestli je pro objednávku zařízení k dispozici. Pokud tomu tak je, expedujeme ho do 10 dnů. Může se stát, že v určitých obdobích se poptávka zvýší. V takovém případě zařadíme vaši objednávku do fronty. Změnu jejího stavu můžete sledovat na webu Azure Portal. Pokud objednávku nevyřídíme do 90 dnů, bude automaticky zrušena.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>Otázka: Mám Data Box plný a potřebuji objednat další. Existuje nějaký rychlý způsob, jak tuto objednávku vytvořit?
A. Můžete svoji předchozí objednávku naklonovat. Naklonováním se vytvoří stejná objednávka, jako byla ta předchozí. Podrobnosti této objednávky však můžete upravit. Nebudete tedy muset znovu zadávat adresu, kontaktní údaje a podrobnosti o oznámení.

## <a name="configure-and-connect"></a>Konfigurace a připojení

### <a name="q-how-do-i-unlock-the-data-box"></a>Otázka: Jak se Data Box odemyká? 
A.  Na webu Azure Portal přejděte k objednávce zařízení Data Box a otevřete **Detaily zařízení**. Zkopírujte si heslo pro odemčení. Toto heslo použijte při přihlášení k místnímu webovému uživatelskému rozhraní Data Boxu. Další informace najdete v [kurzu věnovanému rozbalení, zapojení a připojení k Azure Data Boxu](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>Otázka: Můžu se k zařízení Data Box připojit a kopírovat do něj data z hostitelského počítače s Linuxem?
A.  Ano. Data Box můžete připojit ke klientům SMB a NFS. Další informace získáte, když přejdete na seznam [podporovaných operačních systémů](data-box-system-requirements.md) hostitelského počítače.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Otázka: Data Box jsme odeslali, ale rozhodli jsme se objednávku zrušit. Proč není dostupné tlačítko pro zrušení?
A.  Objednávka se dá zrušit po objednání zařízení Data Box, dokud není objednávka zpracovaná. Zpracovanou objednávku zařízení Data Box nemůžete zrušit. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>Otázka: Můžu Data Box současně připojit k více hostitelským počítačům, ze kterých chci přenášet data?
A. Ano. K zařízení Data Box může být připojených více hostitelských počítačů, ze kterých budete přenášet data, a několik úloh kopírování může běžet současně. Další informace nejdete v [kurzu o kopírování dat do služby Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>Otázka: Mohu se připojit k oběma rozhraním 10 GbE na datové schránce pro přenos dat?
A. Ano. Obě rozhraní 10 GbE lze připojit k datové schránce a kopírovat data současně. Další informace o kopírování dat najdete [v kurzu: Kopírování dat do Datové schránky Azure](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>Otázka: Na předním ovládacím panelu svítí dioda, která signalizuje chybu systému. Co bych měl/a dělat?
A. Pokud svítí dioda, která signalizuje chybu systému, znamená to, že systém není v pořádku. Další kroky [vám poskytne podpora společnosti Microsoft.](data-box-disk-contact-microsoft-support.md)

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>Otázka: Nemám přístup k heslu pro odemčení Data Boxu na webu Azure Portal. Čím to může být?
A. Pokud nemůžete získat přístup k heslu pro odemčení, které je na webu Azure Portal, zkontrolujte oprávnění v předplatném a v účtu úložiště. Ověřte, že na úrovni skupiny prostředků máte oprávnění přispěvatele nebo vlastníka. Pokud ne, pak musíte mít alespoň oprávnění data box operátor role vidět přístupové údaje.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>Otázka: Je konfigurace kanálu portu v datové schránce podporována? Co takhle MPIO?
A. Nepodporujeme konfiguraci kanálu portu, konfiguraci Multipath IO (MPIO) ani konfiguraci vLAN v datové schránce.

## <a name="track-status"></a>Sledování stavu

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>Otázka: Jak můžu sledovat Data Box od okamžiku vystavení objednávky až do zpětného odeslání zařízení? 
A.  Stav objednávky Data Boxu můžete sledovat na webu Azure Portal. Při vytváření objednávky se zobrazuje také výzva k zadání e-mailové adresy pro oznámení. Pokud jste tuto adresu zadali, budete prostřednictvím e-mailu dostávat oznámení o všech změnách stavu dané objednávky. Další informace o [konfiguraci e-mailových adres pro oznámení](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>Otázka: Jak můžu zařízení vrátit? 
A.  Microsoft zobrazuje expediční štítek na displeji s elektronickým inkoustem. Pokud se expediční štítek na displeji s elektronickým inkoustem nezobrazuje, přejděte na **Přehled > Stáhnout expediční štítek**. Stáhněte a vytiskněte tento štítek, vložte ho do průhledného plastového obalu na zařízení a předejte zařízení svému dopravci. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>Otázka: Přišlo mi e-mailem oznámení, že zařízení dorazilo do datacentra Azure. Jak zjistím, jestli probíhá nahrávání dat?
A. Na webu Azure Portal přejděte k objednávce Data Boxu a pak přejděte na **Přehled**. Pokud se data začala nahrávat do Azure, zobrazí se v pravém podokně průběh kopírování. 

## <a name="migrate-data"></a>Migrace dat

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>Otázka: Kolik dat se vejde do zařízení Data Box?  
A.  Data Box má využitelné úložiště o kapacitě 80 TB. Jedno zařízení Data Box můžete použít pro data o velikosti 40–80 TB. Pro větší velikosti dat až 500 TB můžete objednat více zařízení Datové schránky. Pokud data přesahují 500 TB, zaregistrujte se do služby Data Box Heavy.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>Otázka: Jak velké objekty blob bloku a objekty blob stránky Data Box podporuje? 
A.  Maximální velikosti se řídí omezeními služby Azure Storage. Maximální velikost objektu blob bloku je přibližně 4,768 TiB a maximální velikost objektu blob stránky je 8 TiB. Další informace najdete v tématu [Škálovatelnost a cíle výkonu pro úložiště objektů Blob](../storage/blobs/scalability-targets.md).

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Otázka: Jak zajistím, aby byla data při přenosu zabezpečená? 
A. Data Box má implementovaných několik bezpečnostní funkcí, kterými je zařízení zabezpečené během přepravy. Jsou to například pečetě, hardwarová a softwarová detekce nedovolené manipulace a heslo pro odemčení zařízení. Další informace najdete v článku o [zabezpečení a ochraně dat ve službě Azure Data Box](data-box-security.md).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>Otázka: Jak zkopíruji data do Data Boxu? 
A.  Pokud používáte klienta SMB, můžete k přetažení a zkopírování dat do zařízení použít SMB kopírovací nástroj, jako je Robocopy, Diskboss nebo Průzkumník souborů Windows. 

Pokud používáte klienta NSF, můžete použít [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) nebo [Ultracopier](https://ultracopier.first-world.info/). 

Další informace nejdete v [kurzu o kopírování dat do služby Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Otázka: Máte nějaké tipy pro zrychlení kopírování dat?
A.  Pokud chcete zrychlit proces kopírování:

- Použijte pro kopírování dat více streamů. Například v Robocopy použijte možnost více vláken. Další informace o přesných používaných příkazech najdete v [kurzu o kopírování dat do služby Azure Data Box a jejich ověření](data-box-deploy-copy-data.md).
- Použijte více relací.
- Místo kopírování dat přes sdílenou síťovou složku (při kterém můžete být omezeni rychlostí sítě) zajistěte, aby byla data přímo na počítači, ke kterému je zařízení Data Box připojené.
- Proveďte srovnávací testy výkonu počítače, který se používá ke kopírování dat. Pro srovnávací testy výkonu hardwaru serveru si stáhněte a používejte [nástroj Bluestop FIO](https://ci.appveyor.com/project/axboe/fio). Vyberte nejnovější sestavení x86 nebo x64, vyberte kartu **Artefakty** a stáhněte si MSI.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM’s disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>Otázka: Můžu se zařízením Data Box používat více účtů úložiště?
A.  Ano. Data Box podporuje maximálně 10 účtů úložišť, úložišť pro obecné účely, klasických úložišť nebo úložišť objektů blob. Podporují se horké i studené objekty blob. 


## <a name="ship-device"></a>Dodání zařízení

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>Otázka: Zařízení přišlo, ale zdá se být poškozené. Co bych měl/a dělat?
A. Pokud zařízení přišlo poškozené nebo s ním někdo nedovoleným způsobem manipuloval, nepoužívejte ho. [Obraťte se na podporu Microsoftu](data-box-disk-contact-microsoft-support.md) a co nejdříve zařízení vraťte. Můžete také vystavit novou objednávku náhradního zařízení Data Box. V uvedeném případě vám náhradní zařízení nebudeme účtovat.

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>Otázka: Mohu si svou objednávku datové schránky vyzvednout sám? Mohu datovou schránku vrátit prostřednictvím zvoleného operátora?
A. Ano. Společnost Microsoft také nabízí samoobslužnou dopravu pouze v oblasti US Gov. Při zadávání objednávky datové schránky můžete zvolit možnost samořízené dopravy. Chcete-li zařízení Data Box vyzvednout, postupujte takto:
    
1. Po objednání je objednávka zpracována a datová schránka připravena. E-mailem budete upozorněni, že vaše objednávka je připravena k vyzvednutí. 
2. Jakmile je objednávka připravená k vyzvednutí, přejděte na svůj řád na webu Azure portal a přejděte do okna **Přehled.** 
3. Na webu Azure Portal se zobrazí oznámení s kódem. Pošlete e-mail týmu [Azure Data Box Operations](mailto:adbops@microsoft.com) a poskytněte mu kód. Tým poskytne místo a naplánuje datum a čas vyzvednutí. Musíte zavolat týmu do 5 pracovních dnů poté, co obdržíte e-mailové oznámení.

Po dokončení kopírování dat vraťte zařízení následujícím způsobem:

1. Jakmile je kopie dat dokončena bez chyb, **spusťte spustit připravit k odeslání**. Po dokončení přípravy obdržíte kód v místním webovém uživatelském rozhraní zařízení. Zkopírujte a uložte kód.
2. Vypněte zařízení a vyjměte připojovací kabely.
3. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
4. Pošlete e-mail týmu [Azure Data Box Operations](mailto:adbops@microsoft.com) a poskytněte mu kód, který jste si uložili dříve. Poskytnou vám informace o tom, kde a kdy zařízení vysadit.

### <a name="q-will-my-data-box-devices-cross-country-borders-during-shipping"></a>Otázka: Překročí moje zařízení datové schránky během přepravy hranice země?
A. Všechna zařízení datové schránky jsou dodávána ze stejné země jako jejich cíl a nepřekročí žádné mezinárodní hranice. Jedinou výjimkou jsou objednávky v Evropské unii (EU), kde mohou zařízení dodávat do a z kterékoli země EU. To platí jak pro datovou schránku, tak pro zařízení Data Box Heavy.

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>Otázka: Objednal(a) jsem si datovou schránku na východě USA, ale obdržel jsem zařízení, které bylo odesláno z místa v USA – západ. Kam mám zařízení vrátit?
A. Snažíme se vám co nejrychleji dostat zařízení Data Box. Upřednostňujeme zásilku z datového centra, které je nejblíže k umístění vašeho účtu úložiště, ale dodáme zařízení z libovolného datového centra Azure, které má k dispozici inventář. Vaše datová schránka by měla být vrácena na stejné místo, odkud byla odeslána, jak je uvedeno na přepravním štítku.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>Otázka: Na displeji s elektronickým inkoustem se nezobrazuje expediční štítek pro vrácení zásilky. Co bych měl/a dělat?
A. Pokud se na displeji s elektronickým inkoustem nezobrazuje štítek pro vrácení zásilky, postupujte takto:
- Odstraňte starý expediční štítek a všechny ostatní štítky, které se týkají minulé přepravy.
- Na webu Azure Portal přejděte ke své objednávce. Přejděte na **Přehled** > **Stáhnout expediční štítek**. Další informace najdete v článku o [stažení expedičního štítku](data-box-portal-admin.md#download-shipping-label).
- Vytiskněte expediční štítek a vložte ho do průhledného plastového obalu, který je připevněný k zařízení. 
- Expediční štítek musí být dobře viditelný. 

### <a name="q-how-is-my-data-protected-during-transit"></a>Otázka: Jak jsou data během přenosu chráněná? 
A.  Během přepravy jsou data v zařízení Data Box chráněná následujícími funkcemi:
 - Disky zařízení Data Box používají šifrování AES-256 a jsou zašifrované. 
 - Zařízení je zamčené a k jeho odemčení je potřeba zadat heslo, aby byl možný přístup k datům.
Další informace najdete v části o [bezpečnostních funkcích zařízení Data Box](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>Otázka: Dokončili jsme přípravu zařízení k odeslání a zařízení jsme vypnuli. Můžeme do Data Boxu přidat další data?
A. Ano. Zařízení můžete znovu zapnout a přidat do něj další data. Až data nakopírujete, budete muset znovu spustit **přípravu k odeslání**.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>Otázka: Dostal jsem své zařízení a to není bootování? Jak zařízení znovu zasuzuji zpět?
A. Pokud se vaše zařízení nezavádí, přejděte na svůj pořádek na webu Azure Portal. Stáhněte si přepravní štítek a připevněte jej na zařízení. Další informace najdete v článku o [stažení expedičního štítku](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Ověření a nahrání

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>Otázka: Za jak dlouho po vrácení Data Boxu budu mít přístup ke svým datům v Azure? 
A.  Jakmile se u objednávky zařízení **Data Copy** zobrazí stav **dokončeno**, měli byste mít přístup ke svým datům.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Otázka: Kde v Azure se moje data po nahrání nachází?
A.  Když zkopírujete data do datové schránky, v závislosti na tom, jestli jsou data objekt blob bloku nebo objekt blob stránky nebo soubory Azure, data se nahrají do jedné z následujících cest ve vašem účtu Azure Storage.
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  Alternativně můžete přejít na svůj účet Azure Storage na webu Azure Portal a dokončit navigaci tam.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Otázka: Zjistil(a) jsem, že jsem nedodržel(a) požadavky Azure na názvy kontejnerů. Znamená to, že nahrání mých dat do Azure se nezdaří?
A.  Pokud mají názvy kontejnerů velká písmena, budou tyto názvy automaticky převedeny na malá písmena. Pokud názvy nevyhovují z jiných důvodů (speciální znaky, jiné jazyky atd.), nahrání se nepodaří. Další informace o osvědčených postupech při pojmenovávání sdílených složek, kontejnerů a souborů najdete zde:
- [Pojmenování sdílených složek a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Zvyklosti u objektů blob bloku a objektů blob stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>Otázka: Jak ověřím data, která zkopíruji do zařízení Data Box?
A.  Když po zkopírování dat spustíte funkci **Připravit k odeslání**, data se ověří. Při ověřování generuje Data Box seznam souborů a kontrolní součty dat. Můžete si stáhnout seznam souborů a ověřit seznam proti souborům ve zdrojových datech. Další informace najdete v popisu [přípravy k odeslání](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>Otázka: Co se stane s mými daty po vrácení Data Boxu?
A.  Jakmile se data zkopírují do Azure, budou z disků zařízení Data Box bezpečně vymazána podle pokynů normy NIST SP 800-88 Revision 1. Další informace najdete v části o [vymazání dat ze zařízení Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Sestava auditování

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Jak služba Azure Data Box podporuje řetězec opatrovnictví?
A.  Služba Azure Data Box nativně nabízí sestavy, které můžete použít k evidenci řetězce opatrovnictví. Protokoly auditu a kopírování jsou k dispozici ve vašem účtu úložiště v Azure a po dokončení objednávky si na webu Azure Portal můžete [stáhnout historii objednávky](data-box-portal-admin.md#download-order-history).


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Jaký typ sestav je k dispozici pro řetězec opatrovnictví?
A.  Řetězec opatrovnictví podporují následující sestavy:

- Dopravní logistika od SPOLEČNOSTI UPS.
- Protokolování zapnutí a přístupu uživatelů ke sdíleným složkám
- Soubor manifestu s 64bitovou kontrolou CRC (CRC-64) nebo s kontrolním součtem každého souboru úspěšně ingestovaného do zařízení Data Box
- Sestava souborů, které se nepodařilo nahrát do účtu Azure Storage
- Sanitarizace zařízení Data Box (podle norem NIST 800 88R1) po zkopírování dat do účtu Azure Storage.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Jsou protokoly sledování operátora (od společnosti UPS) k dispozici? 
A.  Protokoly dopravců o sledování zásilky jsou zaznamenané v historii objednávky Data Boxu. Tuto sestavu máte k dispozici po vrácení zařízení do datacentra Azure a vymazání dat z disků zařízení. Pokud potřebujete informace hned, přejděte rovnou na webové stránky dopravce a zadejte číslo sledované zásilky, abyste získali informace o sledování.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Můžu dopravit Data Box do datacentra Azure? 
A.  Ne. V současné době Datové centrum Azure nepřijímá doručení datové schránky od zákazníků nebo od jiných operátorů než UPS.


## <a name="next-steps"></a>Další kroky

- Přečtěte si [systémové požadavky služby Data Box](data-box-system-requirements.md).
- Seznamte se s [omezeními služby Data Box](data-box-limits.md).
- Rychle nasaďte [Azure Data Box](data-box-quickstart-portal.md) na webu Azure Portal.
