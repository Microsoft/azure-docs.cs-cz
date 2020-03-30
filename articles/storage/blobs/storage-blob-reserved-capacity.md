---
title: Optimalizace nákladů pro úložiště objektů Blob s rezervovanou kapacitou – Azure Storage
description: Přečtěte si o nákupu rezervované kapacity Azure Storage, abyste ušetřili náklady na blokové objekty blob a prostředky Azure Data Lake Storage Gen2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e73686629de8481f6a37e5bfafc9b723206b4853
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351030"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Optimalizace nákladů na službu Blob Storage s využitím rezervované kapacity

S rezervovanou kapacitou Azure Storage můžete ušetřit peníze na nákladech na úložiště pro data objektů blob. Vyhrazená kapacita Azure Storage vám nabízí slevu na kapacitu pro objekty BLOB bloku a pro data Azure Data Lake Storage Gen2 ve standardních účtech úložiště, když se zavážete k rezervaci na jeden rok nebo tři roky. Rezervace poskytuje pevnou kapacitu pro uložení po dobu trvání rezervace.

Rezervovaná kapacita Azure Storage může výrazně snížit náklady na kapacitu pro objekty BLOB bloku a data Azure Data Lake Storage Gen2. Dosažené úspory nákladů závisí na době trvání rezervace, celkové kapacitě, kterou se rozhodnete rezervovat, a na úrovni přístupu a typu redundance, kterou jste si vybrali pro svůj účet úložiště. Rezervovaná kapacita poskytuje fakturační slevu a nemá vliv na stav prostředků úložiště Azure.

Informace o cenách rezervací služby Azure Storage najdete v tématech [Ceny objektů blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/) a [Ceny služby Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Podmínky rezervace pro Azure Storage

V následujících částech jsou popsány podmínky rezervace úložiště Azure.

### <a name="reservation-capacity"></a>Rezervační kapacita

Rezervovanou kapacitu služby Azure Storage můžete zakoupit v jednotkách 100 TB a 1 PB měsíčně na jednoleté nebo tříleté období.

### <a name="reservation-scope"></a>Rozsah rezervace

Vyhrazená kapacita Azure Storage je dostupná pro jedno předplatné nebo pro více předplatných (sdílený obor). Pokud je rozsah na jedno předplatné, sleva rezervace se použije pouze na vybrané předplatné. Při vymezeném rozsahem více předplatných je sleva na rezervaci sdílena mezi těmito předplatnými v kontextu fakturace zákazníka.

Když si zakoupíte rezervovanou kapacitu Azure Storage, můžete použít rezervaci pro data blob bloku i Azure Data Lake Storage Gen2. Rezervace se použije na vaše využití v rámci zakoupeného oboru a nemůže být omezena na konkrétní účet úložiště, kontejner nebo objekt v rámci předplatného. Rezervaci nelze rozdělit mezi více předplatných.

Rezervace azure storage zahrnuje jenom množství dat, která jsou uložená v předplatném nebo sdílené skupině prostředků. Včasné odstranění, operace, šířka pásma a poplatky za přenos dat nejsou zahrnuty v rezervaci. Jakmile si zakoupíte rezervaci, poplatky za kapacitu, které odpovídají atributům rezervace, se účtují podle diskontních sazeb namísto sazby s průběžným platbou. Další informace o rezervacích Azure najdete v tématu [Co jsou rezervace Azure?](/azure/billing/billing-save-compute-costs-reservations).

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Podporované typy účtů, úrovně a možnosti redundance

Rezervovaná kapacita Služby Azure Storage je dostupná pro prostředky ve standardních účtech úložiště, včetně účtů úložiště pro obecné účely v2 (GPv2) a objektů Blob.

Pro rezervace jsou podporovány všechny úrovně přístupu (horké, studené a archivní). Další informace o úrovních přístupu najdete v [tématu úložiště objektů blob Azure: horké, studené a archivní úrovně přístupu](storage-blob-storage-tiers.md).

Pro rezervace jsou podporovány všechny typy redundance. Další informace o možnostech redundance najdete [v tématu redundance azure storage](../common/storage-redundancy.md).

> [!NOTE]
> Rezervovaná kapacita Úložiště Azure není dostupná pro účty úložiště s prémií, účty úložiště pro obecné účely v1 (GPv1), Azure Data Lake Storage Gen1, objekty BLOB stránky, úložiště front Azure, úložiště Azure Table nebo soubory Azure.  

### <a name="security-requirements-for-purchase"></a>Bezpečnostní požadavky pro nákup

Zakoupení rezervované kapacity:

- Musíte být v roli **vlastníka** alespoň pro jedno podnikové nebo individuální předplatné s průběžnými platbami.
- U předplatných Enterprise musí být na portálu EA povolena možnost **Přidat rezervované instance.** Nebo pokud je toto nastavení zakázané, musíte být správcem EA v předplatném.
- V případě programu zprostředkovatele cloudových řešení (CSP) mohou rezervovanou kapacitu úložiště objektů blob Azure zakoupit pouze agenti správce nebo prodejci.

## <a name="determine-required-capacity-before-purchase"></a>Určení požadované kapacity před nákupem

Když si zakoupíte rezervaci úložiště Azure, musíte pro rezervaci zvolit oblast, úroveň přístupu a možnost redundance. Vaše rezervace je platná pouze pro data uložená v této oblasti, úroveň přístupu a úroveň redundance. Předpokládejme například, že zakoupíte rezervaci pro data v USA – západ pro horkou vrstvu pomocí zónově redundantního úložiště (ZRS). Stejnou rezervaci nelze použít pro data v USA – východ, data v archivní vrstvě nebo data v geograficky redundantním úložišti (GRS). Můžete si však zakoupit další rezervaci pro vaše další potřeby.  

Rezervace jsou dnes k dispozici pro 100 TB nebo 1 PB bloky, s vyššími slevami na 1 BLOKY PB. Když si zakoupíte rezervaci na webu Azure Portal, microsoft vám může poskytnout doporučení na základě vašeho předchozího využití, která vám pomůžou určit, kterou rezervaci byste měli zakoupit.

## <a name="purchase-azure-storage-reserved-capacity"></a>Nákup rezervované kapacity úložiště Azure

Rezervovanou kapacitu Azure Storage můžete zakoupit na [webu Azure Portal](https://portal.azure.com). Za rezervaci se platí předem nebo prostřednictvím měsíčních plateb. Další informace o nákupu s měsíčními platbami najdete v [tématu Nákup rezervací Azure s platbami předem nebo měsíčně](/azure/billing/billing-monthly-payments-reservations).

Nápovědu k identifikaci podmínek rezervace, které jsou pro váš scénář správné, [najdete v tématu Principy slevy na rezervovanou kapacitu služby Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md).

Chcete-li zakoupit rezervovanou kapacitu, postupujte takto:

1. Přejděte do podokna [Rezervace nákupu](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) na webu Azure Portal.  
1. Vyberte **Azure Blob Storage** a zakoupíte novou rezervaci.  
1. Vyplňte požadovaná pole popsaná v následující tabulce:

    ![Snímek obrazovky znázorňující, jak zakoupit rezervovanou kapacitu](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Pole  |Popis  |
   |---------|---------|
   |**Rozsah**   |  Označuje, kolik předplatných může využít fakturační výhodu přidruženou k rezervaci. Také určuje, jak se rezervace použije na konkrétní odběry. <br/><br/> Pokud vyberete **Sdílené**, sleva na rezervaci se použije na kapacitu úložiště Azure v libovolném předplatném v rámci fakturačního kontextu. Kontext fakturace je založen na tom, jak jste se zaregistrovali do Azure. Pro podnikové zákazníky je sdílený obor registrace a zahrnuje všechna předplatná v rámci registrace. U zákazníků s průběžnými platbami zahrnuje sdílený obor všechna jednotlivá předplatná s průběžnými platbami vytvořenými správcem účtu.  <br/><br/>  Pokud vyberete **jedno předplatné**, sleva na rezervaci se použije na kapacitu úložiště Azure ve vybraném předplatném. <br/><br/> Pokud vyberete **skupinu prostředků Single**, sleva na rezervaci se použije na kapacitu úložiště Azure ve vybraném předplatném a vybranou skupinu prostředků v rámci tohoto předplatného. <br/><br/> Rozsah rezervace můžete změnit po zakoupení rezervace.  |
   |**Předplatné**  | Předplatné, které se používá k platbě za rezervaci úložiště Azure. Způsob platby u vybraného předplatného se používá při účtování nákladů. Předplatné musí být jedním z následujících typů: <br/><br/>  Smlouva Enterprise Agreement (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P): U předplatného Enterprise se poplatky odečtou z peněžního zůstatku závazku zápisu nebo se účtují jako nadbytku. <br/><br/> Individuální předplatné s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P): U individuálního předplatného s průběžnými platbami se poplatky účtují na základě platební karty nebo způsobu platby faktury u předplatného.    |
   | **Oblasti** | Oblast, kde je rezervace v platnosti. |
   | **Access tier (Vrstva přístupu)** | Úroveň přístupu, kde je rezervace v platnosti. Možnosti zahrnují *hot*, *cool*nebo *archiv .* Další informace o úrovních přístupu najdete v [tématu Úložiště objektů blob Azure: horké, studené a archivní úrovně přístupu](storage-blob-storage-tiers.md). |
   | **Redundance** | Možnost redundance pro rezervaci. Možnosti zahrnují *LRS*, *ZRS*, *GRS*a *RA-GZRS*. Další informace o možnostech redundance najdete [v tématu redundance azure storage](../common/storage-redundancy.md). |
   | **Frekvence fakturace** | Označuje, jak často se účtu účtu je účtována rezervace. Možnosti zahrnují *měsíční* nebo *předem .* |
   | **Velikost** | Oblast, kde je rezervace v platnosti. |
   |**Termín**  | Jeden rok nebo tři roky.   |

1. Po výběru parametrů pro rezervaci se na portálu Azure zobrazí náklady. Portál také zobrazuje procento slevy oproti fakturaci s průběžnou platbou.

1. V podokně **Nákupní rezervace** zkontrolujte celkové náklady na rezervaci. Můžete také zadat název rezervace.

    ![Snímek obrazovky s postupem zakoupení rezervace](media/storage-blob-reserved-capacity/purchase-reservations.png)

Po zakoupení rezervace se automaticky použije na všechny existující objekty blob bloku Azure Storage nebo prostředky Azure Data Lake Storage Gen2, které odpovídají podmínkám rezervace. Pokud jste ještě nevytvořili žádné prostředky Azure Storage, rezervace se použije vždy, když vytvoříte prostředek, který odpovídá podmínkám rezervace. V obou případech začíná termín rezervace bezprostředně po úspěšném nákupu.

## <a name="exchange-or-refund-a-reservation"></a>Výměna nebo refundace rezervace

Můžete si vyměnit nebo vrátit peníze za rezervaci s určitými omezeními. Tato omezení jsou popsána v následujících částech.

Chcete-li si rezervaci vyměnit nebo vrátit, přejděte na podrobnosti rezervace na webu Azure Portal. Vyberte **Možnost Výměna** nebo **Refundace**a podle pokynů odešlete žádost o podporu. Po zpracování žádosti vám společnost Microsoft zašle e-mail s potvrzením dokončení žádosti.

Další informace o zásadách rezervace Azure najdete [v tématu Samoobslužné výměny a refundace pro rezervace Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Výměna rezervace

Výměna rezervace vám umožní získat poměrnou náhradu na základě nevyužité části rezervace. Refundaci pak můžete použít na nákupní cenu nové rezervace Azure Storage.

Počet výměn, které můžete provést, není nijak omezen. Navíc neexistuje žádný poplatek spojený s výměnou. Nová rezervace, kterou zakoupíte, musí mít stejnou nebo vyšší hodnotu než poměrný kredit z původní rezervace. Rezervaci úložiště Azure můžete vyměnit jenom za jinou rezervaci úložiště Azure a ne za rezervaci pro jakoukoli jinou službu Azure.

### <a name="refund-a-reservation"></a>Refundace rezervace

Rezervaci úložiště Azure můžete kdykoli zrušit. Když rezervaci zrušíte, obdržíte poměrnou refundaci na základě zbývající doby rezervace po 12% poplatku za předčasné ukončení. Maximální refundace za rok je 50 000 USD.

Zrušení rezervace okamžitě ukončí rezervaci a vrátí zbývající měsíce společnosti Microsoft. Zbývající poměrný zůstatek po odečtení poplatku bude vrácen do původní formy nákupu.

## <a name="expiration-of-a-reservation"></a>Vypršení rezervace

Když vyprší platnost rezervace, všechny kapacity úložiště Azure, které používáte v rámci této rezervace se účtuje na pay-as-you go sazby. Rezervace se neprodlužují automaticky.

Obdržíte e-mailové oznámení 30 dní před vypršením rezervace a znovu v den vypršení platnosti. Chcete-li pokračovat v využití úspor nákladů, které poskytuje rezervace, obnovte ji nejpozději k datu vypršení platnosti.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Co jsou rezervace Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Vysvětlení uplatňování slevy za rezervaci ve službě Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md)
