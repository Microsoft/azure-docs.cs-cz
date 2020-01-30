---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847131"
---
V kombinaci s rezervovanými instancemi virtuálních počítačů můžete snížit celkové náklady na virtuální počítače a ušetřit tak využití jednotek SSD (Solid-State Drive) s vyhrazenou kapacitou. Sleva rezervace se automaticky použije na příslušné disky ve vybraném oboru rezervací, takže nemusíte přiřazovat slevy ke spravovanému disku a získat slevy. Slevy se aplikují každou hodinu na využití disku a nevyužitá Rezervovaná kapacita se neuskuteční. Sleva za rezervované disky spravovaného disku neplatí pro nespravované disky, Ultra disks nebo využití objektů blob stránky.

## <a name="determine-your-storage-needs"></a>Určení potřeb úložiště

Než zakoupíte rezervaci, měli byste určit, jaké jsou vaše požadavky na úložiště. V současné době je rezervace disku dostupná jenom pro vybrané SKU jednotek SSD. SKU jednotky SSD úrovně Premium určuje velikost a výkon disku. Při určování potřeb úložiště nedoporučujeme, aby se disky nablížily stejně jako celková kapacita, nemůžete použít rezervaci pro větší disk (například P40) a použít ho k placení dvou menších disků (P30). Při nákupu rezervace kupujete jenom celkový počet disků na SKU.

Rezervace disku se provádí na diskovou jednotku, takže se spotřeba rezervace vychází z jednotky SKU disku namísto zadané velikosti. Pokud jste například rezervovali 1 P40 ze 2 TiB zřízené kapacity, ale pouze přidělené 2 disky P30, tato dvě P30ová spotřeba se neúčtují pro P40 rezervaci a budete platit sazbu průběžných plateb.



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Předpoklady nákupu

Při zvažování nákupu rezervovaného disku doporučujeme následující osvědčené postupy:

- Analýza informací o použití vám pomůže určit, které rezervace byste měli koupit. Ujistěte se, že sledujete využití disků SKU místo zřízené nebo využité kapacity disku. 
- Projděte si rezervaci disku společně s vaší rezervací virtuálního počítače. Důrazně doporučujeme vystavit rezervaci pro virtuální počítače a využití disku pro maximální ukládání. Můžete začít určením správné rezervace virtuálních počítačů a pak vyhodnotit rezervaci disku odpovídajícím způsobem. Obecně platí, že pro každou úlohu budete mít standardní konfiguraci, například SQL Server může mít dva disky P40 a jeden disk s operačním systémem P30. Tento druh vzoru vám může přispět k určení množství rezervací, které můžete koupit. Tento přístup může zjednodušit proces vyhodnocení a zároveň se ujistit, že máte zarovnaný plán pro virtuální počítače i disky z podmínek předplatných, oblastí a dalších. 

## <a name="purchase-restrictions"></a>Omezení nákupu

Slevy za rezervované jednotky nejsou aktuálně k dispozici pro následující disky:
- Nespravované disky/objekty blob stránky
- SSD úrovně Standard nebo standardní HDD
- SSD úrovně Premium skladové položky menší než P30 – rezervace nejsou k dispozici pro skladové položky SSD úrovně Premium/P2/P3/P4/P6/P10/P15/P20.
- Cloudy – rezervace nejsou dostupné pro nákup v oblastech Azure gov, Německo nebo Čína.
- Omezení kapacity – Azure omezuje nákup nových rezervací pro podmnožinu SKU disků z důvodu nízké kapacity v oblasti.

## <a name="buy-a-disk-reservation"></a>Nákup rezervovaného disku

Rezervace disku Azure si můžete koupit prostřednictvím [Azure Portal](https://portal.azure.com/). Můžete buď zaplatit za rezervaci předem nebo s měsíčními platbami. Další informace o nákupu s měsíčními platbami najdete v tématu [Nákup rezervací s měsíčními platbami](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).

K zakoupení rezervované kapacity použijte následující postup:

1. V Azure Portal přejděte do okna [rezervace nákupu](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) .
1. Pokud si chcete koupit rezervaci, vyberte **Azure Managed disks** .

    ![disks-Reserved-purchase-reservation. png](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. Vyplňte požadovaná pole, jak je popsáno v následující tabulce:

   |Pole  |Popis  |
   |---------|---------|
   |**Rozsah**   |  Označuje, kolik předplatných může využívat fakturační výhody spojené s rezervací. Také určuje, jak se rezervace aplikují na konkrétní předplatná. <br/><br/> Pokud vyberete **Shared (sdílená**), použije se sleva rezervace pro Azure Storage kapacity v jakémkoli předplatném v rámci vašeho fakturačního kontextu. Fakturační kontext vychází z toho, jak jste se zaregistrovali do Azure. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami, sdílený obor zahrnuje všechna jednotlivá předplatná s tarify průběžných plateb vytvořenými správcem účtu.  <br/><br/>  Pokud vyberete **jedno předplatné**, použije se sleva rezervace pro Azure Storage kapacity ve vybraném předplatném. <br/><br/> Když vyberete **jednu skupinu prostředků**, použije se sleva rezervace pro Azure Storage kapacity ve vybraném předplatném a v rámci daného předplatného. <br/><br/> Po zakoupení rezervace můžete obor rezervací změnit.  |
   |**Předplatné**  | Předplatné, které se používá k placení Azure Storage rezervace. Způsob platby ve vybraném předplatném se používá při zpoplatnění nákladů. Předplatné musí být jeden z následujících typů: <br/><br/>  Smlouva Enterprise (počet nabídek: MS-AZR-0017P nebo MS-AZR-0148P): u podnikového předplatného se poplatky odečtou z bilance peněžního závazku registrace nebo se účtují jako nadlimitní využití. <br/><br/> Individuální předplatné s tarify průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P): u jednotlivých předplatných s tarify pro průběžné platby se poplatky účtují na základě platební karty nebo platby faktury v předplatném.    |
   | **Disky** | Skladová položka, kterou chcete vytvořit. |
   | **Oblast** | Oblast, ve které je rezervace platná. |
   | **Četnost fakturace** | Určuje, jak často se účtuje účet pro rezervaci. Mezi možnosti patří *měsíční* nebo *předem*. |

    ![Premium-SSD-Reserved-purchase-Selection. png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Po výběru parametrů pro rezervaci Azure Portal zobrazí náklady. Na portálu se také zobrazuje procento slevy při fakturaci s průběžnými platbami. Kliknutím na tlačítko **Další** přejdete na okno **rezervace nákupu** .

1. V podokně **rezervace nákupu** můžete pojmenovat rezervaci a vybrat celkové množství rezervací, které chcete vytvořit. Počet rezervací mapovaných na počet disků. Pokud byste například chtěli rezervovat stovky disků, změnili jste **množství** na 100.
1. Zkontrolujte celkové náklady na rezervaci.

    ![Premium-SSD-Reserved-Selecting-SKU-Total-Purchase. png](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Po zakoupení rezervované rezervace se automaticky použije na všechny existující prostředky diskového úložiště Azure, které odpovídají podmínkám rezervace. Pokud jste ještě nevytvořili žádné prostředky diskového úložiště Azure, bude tato rezervace platit pokaždé, když vytvoříte prostředek, který bude odpovídat podmínkám rezervace. V obou případech je termín rezervace zahájen ihned po úspěšném nákupu.

## <a name="exchange-or-refund-a-reservation"></a>Výměna nebo refundace rezervace

S určitými omezeními můžete vyměňovat nebo vrátit rezervaci.

Chcete-li vyměnit nebo vrátit rezervaci, přejděte k podrobnostem o rezervacích v Azure Portal. Vyberte možnost **Exchange nebo refundace**a podle pokynů odešlete žádost o podporu. Po zpracování žádosti vám Microsoft pošle e-mail, aby se potvrdil dokončení žádosti.

Další informace o Azure Reservationsch zásadách najdete v tématu [samoobslužné výměny a refundace pro Azure reservations](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Výměna rezervace

Výměna rezervace vám umožní získat poměrnou refundaci na základě nevyužité části rezervace. Tuto refundaci pak můžete uplatnit na kupní cenu nové rezervace disku Azure.
Počet výměn, které můžete provést, není nijak omezený. Za výměnu se navíc neplatní žádný poplatek. Nová rezervace, kterou zakoupíte, musí být stejná nebo větší než poměrná hodnota kreditu z původní rezervace. Rezervace disku Azure se dá vyměnit jenom na jinou rezervaci disku Azure, a ne pro rezervaci pro žádnou jinou službu Azure.

### <a name="refund-a-reservation"></a>Refundace rezervace

Rezervaci disku Azure můžete kdykoli zrušit. Pokud akci zrušíte, obdržíte refundaci na základě zbývající podmínky rezervace, odečtený poplatek za předčasné ukončení od 12%. Maximální refundace za rok je $50 000.
Zrušení rezervace okamžitě ukončí rezervaci a vrátí zbývající měsíce společnosti Microsoft. Zbývající poměrný zůstatek minus poplatek se vrátí do původní formy nákupu.

## <a name="expiration-of-a-reservation"></a>Vypršení platnosti rezervace

Po vypršení platnosti rezervace se všechny kapacity disku Azure, které v rámci této rezervace využijete, účtují podle tarifu průběžných plateb. Rezervace se automaticky neprodlužují.
Před vypršením platnosti rezervace obdržíte e-mailem oznámení 30 dnů a znovu k datu vypršení platnosti. Pokud chcete pokračovat v využívání výhod úspory nákladů, které poskytuje rezervace, obnovte ji později než datum vypršení platnosti.

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Co jsou rezervace Azure?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Pochopení způsobu použití slevy rezervace pro Azure Disk Storage](../articles/cost-management-billing/reservations/understand-disk-reservations.md)