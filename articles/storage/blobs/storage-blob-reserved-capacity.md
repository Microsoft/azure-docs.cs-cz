---
title: Optimalizujte náklady na úložiště objektů BLOB s využitím rezervované kapacity – Azure Storage
description: Přečtěte si o nákupu Azure Storage rezervované kapacity za účelem úspory nákladů na objekty blob bloku a prostředky Azure Data Lake Storage Gen2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/01/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c06bbc412a51fc919b862aeb3f62ec58feec89cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84259197"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Optimalizace nákladů na službu Blob Storage s využitím rezervované kapacity

Můžete ušetřit peníze za náklady na úložiště dat objektů BLOB s využitím Azure Storage rezervované kapacity. Azure Storage Rezervovaná kapacita nabízí slevu na kapacitu pro objekty blob bloku a pro Azure Data Lake Storage Gen2 data v účtech úložiště Standard, když potvrdíte rezervaci za jeden rok nebo tři roky. Rezervace poskytuje pevnou velikost kapacity úložiště pro podmínky rezervace.

Azure Storage Rezervovaná kapacita může významně snížit náklady na kapacitu pro objekty blob bloku a Azure Data Lake Storage Gen2 data. Úspora nákladů závisí na době trvání vaší rezervace, celkové kapacitě, kterou se rozhodnete rezervovat, a na úrovni přístupu a typu redundance, kterou jste zvolili pro váš účet úložiště. Rezervovaná kapacita poskytuje fakturační slevu a nemá vliv na stav vašich Azure Storagech prostředků.

Informace o cenách rezervací služby Azure Storage najdete v tématech [Ceny objektů blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/) a [Ceny služby Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Azure Storage podmínek rezervace

V následujících částech jsou popsány požadavky Azure Storage rezervace.

### <a name="reservation-capacity"></a>Kapacita rezervace

Můžete zakoupit Azure Storage rezervovanou kapacitu v jednotkách od 100 TB do 1 PB za měsíc po dobu jednoho roku nebo tří let.

### <a name="reservation-scope"></a>Rozsah rezervace

Azure Storage Rezervovaná kapacita je k dispozici pro jedno předplatné nebo pro více předplatných (sdílený rozsah). V případě oboru na jedno předplatné se sleva za rezervaci použije jenom pro vybrané předplatné. Při vymezení více předplatných se sleva rezervace sdílí v rámci těchto předplatných v rámci fakturačního kontextu zákazníka.

Když si koupíte Azure Storage rezervovanou kapacitu, můžete použít rezervaci pro objekty blob bloku i Azure Data Lake Storage Gen2 data. Rezervace se aplikuje na vaše využití v rámci zakoupeného oboru a nedá se omezit na konkrétní účet úložiště, kontejner nebo objekt v rámci předplatného.

Rezervace Azure Storage pokrývá jenom množství dat, která jsou uložená v rámci předplatného nebo sdílené skupiny prostředků. V rezervaci nejsou zahrnuty poplatky za předčasné odstranění, provoz, šířku pásma a přenos dat. Jakmile si koupíte rezervaci, účtují se poplatky za kapacitu odpovídající atributům rezervace za zlevněné sazby, a ne podle tarifů průběžných plateb. Další informace o rezervacích Azure najdete v tématu [co jsou Azure reservations?](/azure/billing/billing-save-compute-costs-reservations).

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Podporované typy účtů, úrovně a možnosti redundance

Azure Storage Rezervovaná kapacita je dostupná pro prostředky ve standardních účtech úložiště, včetně účtů pro obecné účely v2 (GPv2) a BLOB Storage.

Pro rezervace se podporují všechny úrovně přístupu (horká, studená a archivní). Další informace o úrovních přístupu najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md).

Pro rezervace se podporují všechny typy redundance. Další informace o možnostech redundance najdete v tématu [Azure Storage redundance](../common/storage-redundancy.md).

> [!NOTE]
> Azure Storage Rezervovaná kapacita není k dispozici pro účty úložiště úrovně Premium, účty úložiště pro obecné účely V1 (GPv1), Azure Data Lake Storage Gen1, objekty blob stránky, úložiště front Azure, úložiště tabulek Azure nebo soubory Azure.  

### <a name="security-requirements-for-purchase"></a>Požadavky na zabezpečení pro nákup

Zakoupení rezervované kapacity:

- Musíte být v roli **vlastníka** alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.
- U podnikových předplatných se musí na portálu EA povolit možnost **Přidat rezervované instance** . Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném.
- V případě programu Cloud Solution Provider (CSP) si můžou koupit Azure Blob Storage rezervovanou kapacitu jenom agenti správce nebo prodejní agenti.

## <a name="determine-required-capacity-before-purchase"></a>Určení požadované kapacity před nákupem

Když si koupíte Azure Storage rezervaci, musíte pro rezervaci zvolit oblast, úroveň přístupu a možnost redundance. Vaše rezervace je platná jenom pro data uložená v této oblasti, úrovni přístupu a na úrovni redundance. Předpokládejme například, že zakoupíte rezervaci pro data v USA – západ pro horkou úroveň pomocí zóny redundantního úložiště (ZRS). Nemůžete použít stejnou rezervaci pro data v USA – východ, data v archivní úrovni nebo data v geograficky redundantním úložišti (GRS). Můžete si ale koupit další rezervaci za vaše další potřeby.  

Rezervace jsou dnes k dispozici pro 100 TB nebo 1 PB bloků s vyššími slevami pro 1 PB bloků. Když si zakoupíte rezervaci v Azure Portal, Microsoft vám může poskytnout doporučení na základě vašeho předchozího použití, které vám pomůže určit, kterou rezervaci byste měli koupit.

## <a name="purchase-azure-storage-reserved-capacity"></a>Koupit Azure Storage rezervovanou kapacitu

Rezervovanou kapacitu můžete zakoupit Azure Storage [Azure Portal](https://portal.azure.com). Za rezervaci se platí předem nebo prostřednictvím měsíčních plateb. Další informace o nákupu s měsíčními platbami najdete v tématu [Nákup rezervací Azure s využitím předem nebo měsíčních plateb](/azure/billing/billing-monthly-payments-reservations).

Nápovědu k identifikaci podmínek rezervací, které jsou pro váš scénář nejvhodnější, najdete v tématu [pochopení Azure Storage zvýhodněné slevy na rezervované kapacity](../../cost-management-billing/reservations/understand-storage-charges.md).

K zakoupení rezervované kapacity použijte následující postup:

1. Přejděte do podokna [Nákupní rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) v Azure Portal.  
1. Vyberte **Azure Blob Storage** k nákupu nové rezervace.  
1. Vyplňte požadovaná pole, jak je popsáno v následující tabulce:

    ![Snímek obrazovky ukazující, jak koupit rezervovanou kapacitu](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Pole  |Popis  |
   |---------|---------|
   |**Rozsah**   |  Označuje, kolik předplatných může využívat fakturační výhody spojené s rezervací. Také určuje, jak se rezervace aplikují na konkrétní předplatná. <br/><br/> Pokud vyberete **Shared (sdílená**), použije se sleva rezervace pro Azure Storage kapacity v jakémkoli předplatném v rámci vašeho fakturačního kontextu. Fakturační kontext vychází z toho, jak jste se zaregistrovali do Azure. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami, sdílený obor zahrnuje všechna jednotlivá předplatná s tarify průběžných plateb vytvořenými správcem účtu.  <br/><br/>  Pokud vyberete **jedno předplatné**, použije se sleva rezervace pro Azure Storage kapacity ve vybraném předplatném. <br/><br/> Když vyberete **jednu skupinu prostředků**, použije se sleva rezervace pro Azure Storage kapacity ve vybraném předplatném a v rámci daného předplatného. <br/><br/> Po zakoupení rezervace můžete obor rezervací změnit.  |
   |**Předplatné**  | Předplatné, které se používá k placení Azure Storage rezervace. Způsob platby ve vybraném předplatném se používá při zpoplatnění nákladů. Předplatné musí být jeden z následujících typů: <br/><br/>  Smlouva Enterprise (počet nabídek: MS-AZR-0017P nebo MS-AZR-0148P): u podnikového předplatného se poplatky odečtou z bilance peněžního závazku registrace nebo se účtují jako nadlimitní využití. <br/><br/> Individuální předplatné s tarify průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P): u jednotlivých předplatných s tarify pro průběžné platby se poplatky účtují na základě platební karty nebo platby faktury v předplatném.    |
   | **Oblast** | Oblast, ve které je rezervace platná. |
   | **Úroveň přístupu** | Úroveň přístupu, kde má být rezervace platná. Mezi možnosti patří *horká*, *studená*nebo *archivní*. Další informace o úrovních přístupu najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md). |
   | **Redundance** | Možnost redundance pro rezervaci. Mezi možnosti patří *LRS*, *ZRS*, *GRS*, *GZRS*, *RA-GRS*a *RA-GZRS*. Další informace o možnostech redundance najdete v tématu [Azure Storage redundance](../common/storage-redundancy.md). |
   | **Četnost fakturace** | Určuje, jak často se účtuje účet pro rezervaci. Mezi možnosti patří *měsíční* nebo *předem*. |
   | **Velikost** | Oblast, ve které je rezervace platná. |
   |**Označení**  | Jeden rok nebo tři roky.   |

1. Po výběru parametrů pro rezervaci Azure Portal zobrazí náklady. Na portálu se také zobrazuje procento slevy při fakturaci s průběžnými platbami.

1. V podokně **rezervace nákupu** Zkontrolujte celkové náklady na rezervaci. Můžete také zadat název rezervace.

    ![Snímek obrazovky, který ukazuje, jak zakoupit rezervaci](media/storage-blob-reserved-capacity/purchase-reservations.png)

Po zakoupení rezervované rezervace se automaticky použije na všechny existující Azure Storage objekty blob bloku nebo Azure Data Lake Storage Gen2 prostředky, které odpovídají podmínkám rezervace. Pokud jste ještě nevytvořili žádné Azure Storage prostředky, rezervace se použije vždy, když vytvoříte prostředek, který odpovídá podmínkám rezervace. V obou případech je termín rezervace zahájen ihned po úspěšném nákupu.

## <a name="exchange-or-refund-a-reservation"></a>Výměna nebo refundace rezervace

Můžete vyměňovat nebo refundaci rezervací s určitými omezeními. Tato omezení jsou popsaná v následujících částech.

Chcete-li vyměnit nebo vrátit rezervaci, přejděte k podrobnostem o rezervacích v Azure Portal. Vyberte možnost **Exchange** nebo **refundace**a podle pokynů odešlete žádost o podporu. Po zpracování žádosti vám Microsoft pošle e-mail, aby se potvrdil dokončení žádosti.

Další informace o Azure Reservationsch zásadách najdete v tématu [samoobslužné výměny a refundace pro Azure reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Výměna rezervace

Výměna rezervace vám umožní získat poměrnou refundaci na základě nevyužité části rezervace. Tuto refundaci pak můžete uplatnit na kupní cenu nové rezervace Azure Storage.

Počet výměn, které můžete vytvořit, není nijak omezený. K Exchangi se navíc nevztahují žádné poplatky. Nová rezervace, kterou zakoupíte, musí být stejná nebo větší než poměrná hodnota kreditu z původní rezervace. Rezervaci Azure Storage je možné vyměňovat jenom pro jinou rezervaci Azure Storage a ne pro rezervaci pro žádnou jinou službu Azure.

### <a name="refund-a-reservation"></a>Refundace rezervace

Rezervaci Azure Storage můžete kdykoli zrušit. Když akci zrušíte, obdržíte průběžnou refundaci na základě zbývající podmínky rezervace, minus poplatek za předčasné ukončení od 12%. Maximální refundace za rok je $50 000.

Zrušení rezervace okamžitě ukončí rezervaci a vrátí zbývající měsíce společnosti Microsoft. Zbývající poměrný zůstatek minus poplatek se vrátí do původní formy nákupu.

## <a name="expiration-of-a-reservation"></a>Vypršení platnosti rezervace

Po vypršení platnosti rezervace se veškerá Azure Storage kapacita, kterou v rámci této rezervace použijete, účtuje podle tarifu průběžných plateb. Rezervace se neprodlužují automaticky.

Před vypršením platnosti rezervace obdržíte e-mailem oznámení 30 dnů a znovu k datu vypršení platnosti. Pokud chcete pokračovat v využívání výhod úspory nákladů, které poskytuje rezervace, obnovte ji později než datum vypršení platnosti.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Co jsou rezervace Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Vysvětlení uplatňování slevy za rezervaci ve službě Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md)
