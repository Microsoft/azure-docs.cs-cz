---
title: Optimalizace nákladů na soubory Azure s rezervovanou kapacitou
titleSuffix: Azure Files
description: Přečtěte si, jak ušetřit náklady na nasazení sdílené složky Azure pomocí rezervované kapacity souborů Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 918320cdb24442e551249e4e67d65e4ba85846c8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027564"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>Optimalizace nákladů na soubory Azure s rezervovanou kapacitou
Můžete ušetřit peníze na náklady na úložiště pro sdílené složky Azure pomocí rezervací kapacity. Rezervovaná kapacita souborů Azure vám nabídne slevu za kapacitu za úložiště při potvrzení na rezervaci buď po dobu jednoho roku, nebo tři roky. Rezervace poskytuje pevnou velikost kapacity úložiště pro podmínky rezervace.

Vyhrazená kapacita souborů Azure může významně snížit náklady na kapacitu pro ukládání dat ve sdílených složkách Azure. Velikost, kterou ušetříte, bude záviset na době trvání vaší rezervace, celkové kapacitě, kterou si zvolíte, a nastavení vrstvy a redundance, které jste zvolili pro sdílené složky Azure. Rezervovaná kapacita poskytuje fakturační slevu a nemá vliv na stav sdílených složek Azure.

Informace o cenách kapacity rezervace pro soubory Azure najdete v tématu [ceny za Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="reservation-terms-for-azure-files"></a>Podmínkami rezervací pro soubory Azure
V následujících částech jsou popsány požadavky na rezervaci kapacity souborů Azure.

### <a name="reservation-capacity"></a>Kapacita rezervace
Můžete koupit rezervované kapacity souborů Azure v jednotkách 10 TiB a 100 TiB za měsíc po dobu jednoho roku nebo tří let.

### <a name="reservation-scope"></a>Rozsah rezervace
Rezervovaná kapacita souborů Azure je dostupná pro jedno předplatné nebo pro více předplatných (sdílený rozsah). V případě oboru na jedno předplatné se sleva za rezervaci použije jenom pro vybrané předplatné. Při vymezení více předplatných se sleva rezervace sdílí v rámci těchto předplatných v rámci fakturačního kontextu zákazníka. Rezervace se vztahuje na vaše využití v rámci zakoupeného oboru a nedá se omezit na konkrétní účet úložiště, kontejner nebo objekt v rámci předplatného.

Rezervace kapacity pro Azure Files pokrývá jenom množství dat, která jsou uložená v rámci předplatného nebo sdílené skupiny prostředků. V rezervaci nejsou zahrnuté poplatky za transakce, šířku pásma a přenos dat. Jakmile si koupíte rezervaci, účtují se poplatky za kapacitu, které odpovídají atributům rezervace, na základě zlevněných sazeb místo sazeb za průběžné platby. Další informace o rezervacích Azure najdete v tématu [co jsou Azure reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md).

### <a name="supported-tiers-and-redundancy-options"></a>Podporované úrovně a možnosti redundance
Rezervovaná kapacita souborů Azure je dostupná jenom pro standardní sdílené složky nasazené v účtech úložiště pro obecné účely verze 2 (GPv2). Rezervovaná kapacita není k dispozici pro sdílené složky Azure v úrovni Premium nebo transakce optimalizované pro transakce.

V současné době se rezervace podporují jenom sdílené složky Azure v horké a studené vrstvě. Všechny služby redundance úložiště podporují rezervace. Další informace o možnostech redundance najdete v tématu [redundance souborů Azure](storage-files-planning.md#redundancy).

### <a name="security-requirements-for-purchase"></a>Požadavky na zabezpečení pro nákup
Zakoupení rezervované kapacity:

- Musíte být v roli **vlastníka** alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.
- U podnikových předplatných se musí na portálu EA povolit možnost **Přidat rezervované instance** . Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném.
- Pro program poskytovatele Cloud Solution Provider (CSP) můžou koupit rezervované kapacity souborů Azure jenom agenti správce nebo prodejní agenti.

## <a name="determine-required-capacity-before-purchase"></a>Určení požadované kapacity před nákupem
Když si koupíte rezervaci souborů Azure, musíte pro rezervaci vybrat možnost oblast, úroveň a redundance. Vaše rezervace je platná jenom pro data uložená v této oblasti, úrovni a redundanci. Předpokládejme například, že zakoupíte rezervaci pro data v USA – západ pro horkou úroveň pomocí zóny redundantního úložiště (ZRS). Tato rezervace nebude platit pro data v USA – východ, data ve studené vrstvě nebo data v geograficky redundantním úložišti (GRS). Můžete si ale koupit další rezervaci za vaše další potřeby.  

Rezervace jsou k dispozici pro 10 bloků TiB nebo 100 TiB s vyššími slevami pro 100 TiB bloků. Když si zakoupíte rezervaci v Azure Portal, Microsoft vám může poskytnout doporučení na základě vašeho předchozího použití, které vám pomůže určit, kterou rezervaci byste měli koupit.

## <a name="purchase-azure-files-reserved-capacity"></a>Koupit rezervované kapacity souborů Azure
Pomocí [Azure Portal](https://portal.azure.com)můžete koupit rezervované kapacity souborů Azure. Za rezervaci se platí předem nebo prostřednictvím měsíčních plateb. Další informace o nákupu s měsíčními platbami najdete v tématu [Nákup rezervací Azure s využitím předem nebo měsíčních plateb](../../cost-management-billing/reservations/prepare-buy-reservation.md).

Nápovědu k identifikaci podmínek rezervací, které jsou pro váš scénář nejvhodnější, najdete v tématu [pochopení Azure Storage zvýhodněné slevy na rezervované kapacity](../../cost-management-billing/reservations/understand-storage-charges.md).

K zakoupení rezervované kapacity použijte následující postup:

1. V Azure Portal přejděte do okna [rezervace nákupu](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) .  
1. Vyberte **soubory Azure** , abyste si koupili novou rezervaci.  
1. Vyplňte požadovaná pole, jak je popsáno v následující tabulce:

    ![Snímek obrazovky ukazující, jak koupit rezervovanou kapacitu](./media/files-reserve-capacity/select-reserved-capacity.png)

   |Pole  |Popis  |
   |---------|---------|
   |**Rozsah**   |  Označuje, kolik předplatných může využívat fakturační výhody spojené s rezervací. Také určuje, jak se rezervace aplikují na konkrétní předplatná. <br/><br/> Pokud vyberete **Shared**, použije se pro kapacitu služby soubory Azure ve všech předplatných v rámci vašeho fakturačního kontextu sleva rezervace. Fakturační kontext vychází z toho, jak jste se zaregistrovali do Azure. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami, sdílený obor zahrnuje všechna jednotlivá předplatná s tarify průběžných plateb vytvořenými správcem účtu.  <br/><br/>  Pokud vyberete **jedno předplatné**, na kapacitu služby soubory Azure ve vybraném předplatném se použije sleva na rezervaci. <br/><br/> Když vyberete **jednu skupinu prostředků**, použije se sleva rezervace na kapacitu souborů Azure ve vybraném předplatném a v rámci daného předplatného. <br/><br/> Po zakoupení rezervace můžete obor rezervací změnit.  |
   |**Předplatné**  | Předplatné, které se používá k platbám za rezervaci souborů Azure Způsob platby ve vybraném předplatném se používá při zpoplatnění nákladů. Předplatné musí být jeden z následujících typů: <br/><br/>  Smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P): u podnikového předplatného se poplatky odečtou z předplacených plateb Azure (dříve označované jako peněžní závazek), nebo se účtují jako překročení částky. <br/><br/> Individuální předplatné s tarify průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P): u jednotlivých předplatných s tarify pro průběžné platby se poplatky účtují na základě platební karty nebo platby faktury v předplatném.    |
   | **Oblast** | Oblast, ve které je rezervace platná. |
   | **Úroveň** | Úroveň, pro kterou je rezervace platná. Mezi možnosti patří *horká* a *studená*. |
   | **Redundance** | Možnost redundance pro rezervaci. Mezi možnosti patří *LRS*, *ZRS*, *GRS* a *GZRS*. Další informace o možnostech redundance najdete v tématu [redundance souborů Azure](storage-files-planning.md#redundancy). |
   | **Četnost fakturace** | Určuje, jak často se účtuje účet pro rezervaci. Mezi možnosti patří *měsíční* nebo *předem*. |
   | **Velikost** | Množství kapacity, která se má vyhradit. |
   |**Označení**  | Jeden rok nebo tři roky.   |

1. Po výběru parametrů pro rezervaci Azure Portal zobrazí náklady. Na portálu se také zobrazuje procento slevy při fakturaci s průběžnými platbami.

1. V okně **rezervace nákupu** Zkontrolujte celkové náklady na rezervaci. Můžete také zadat název rezervace.

Po zakoupení rezervované rezervace se automaticky použije pro všechny existující sdílené složky Azure, které odpovídají podmínkám rezervace. Pokud jste zatím nevytvořili žádné sdílené složky Azure, bude rezervace platit pokaždé, když vytvoříte prostředek, který odpovídá podmínkám rezervace. V obou případech je termín rezervace zahájen ihned po úspěšném nákupu.

## <a name="exchange-or-refund-a-reservation"></a>Výměna nebo refundace rezervace
Můžete vyměňovat nebo refundaci rezervací s určitými omezeními. Tato omezení jsou popsaná v následujících částech.

Chcete-li vyměnit nebo vrátit rezervaci, přejděte k podrobnostem o rezervacích v Azure Portal. Vyberte možnost **Exchange** nebo **refundace** a podle pokynů odešlete žádost o podporu. Po zpracování žádosti vám Microsoft pošle e-mail, aby se potvrdil dokončení žádosti.

Další informace o Azure Reservationsch zásadách najdete v tématu [samoobslužné výměny a refundace pro Azure reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Výměna rezervace
Výměna rezervace vám umožní získat poměrnou refundaci na základě nevyužité části rezervace. Tuto refundaci pak můžete uplatnit na nákupní cenu nové rezervace souborů Azure.

Počet výměn, které můžete vytvořit, není nijak omezený. K Exchangi se navíc nevztahují žádné poplatky. Nová rezervace, kterou zakoupíte, musí být stejná nebo větší než poměrná hodnota kreditu z původní rezervace. Rezervace souborů Azure se dá vyměnit jenom pro jinou rezervaci souborů Azure, a ne pro rezervaci pro žádnou jinou službu Azure.

### <a name="refund-a-reservation"></a>Refundace rezervace
Rezervaci souborů Azure můžete kdykoli zrušit. Když akci zrušíte, obdržíte průběžnou refundaci na základě zbývající podmínky rezervace, minus poplatek za předčasné ukončení od 12%. Maximální refundace za rok je $50 000.

Zrušení rezervace okamžitě ukončí rezervaci a vrátí zbývající měsíce společnosti Microsoft. Zbývající poměrný zůstatek minus poplatek se vrátí do původní formy nákupu.

## <a name="expiration-of-a-reservation"></a>Vypršení platnosti rezervace
Po vypršení platnosti rezervace se všechny kapacity souborů Azure, které v rámci této rezervace využijete, účtují podle tarifu průběžných plateb. Rezervace se neprodlužují automaticky.

Před vypršením platnosti rezervace obdržíte e-mailem oznámení 30 dnů a znovu k datu vypršení platnosti. Pokud chcete pokračovat v využívání výhod úspory nákladů, které poskytuje rezervace, obnovte ji později než datum vypršení platnosti.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás
Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- [Co jsou rezervace Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Vysvětlení způsobu použití slev pro rezervace u služeb Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md)