---
title: Optimalizace nákladů na Azure Disk Storage s rezervacemi
description: Přečtěte si o nákupu Azure Disk Storage rezervacích za účelem úspory nákladů na spravované disky SSD úrovně Premium.
author: roygara
ms.author: rogarana
ms.date: 01/30/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 9a6a56491a327d5d4eafc2c05dc2b54b137096b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88752061"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>Snížení nákladů pomocí rezervací disků Azure

Pomocí rezervované kapacity ušetříte Azure Disk Storage využití. Azure Disk Storage rezervace v kombinaci s Azure Reserved Virtual Machine Instances vám umožní snížit celkový počet nákladů na virtuální počítač (VM). Sleva rezervace se automaticky použije na příslušné disky ve vybraném oboru rezervací. Z důvodu této automatické aplikace nemusíte ke spravovanému disku přiřadit rezervaci a získat slevy.

Slevy se aplikují každou hodinu v závislosti na využití disku. Nevyužitá Rezervovaná kapacita se neprovádí. Slevy Azure Disk Storage rezervované jednotky neplatí pro nespravované disky, Ultra disks nebo využití objektů blob stránky.

## <a name="determine-your-storage-needs"></a>Určení potřeb úložiště

Než si zakoupíte rezervaci, určete své požadavky na úložiště. V současné době jsou rezervace Azure Disk Storage k dispozici pouze pro výběr SKU Azure Premium SSD. SKU jednotky SSD úrovně Premium určuje velikost a výkon disku.

Při určování potřeb úložiště si nemyslete na discích, které jsou založené jenom na kapacitě. Například nemůžete mít rezervaci pro disk P40 a použijete ho k placení na dva menší disky P30. Při nákupu rezervace si koupíte jenom rezervaci pro celkový počet disků na SKU.

Rezervace disku je provedena na diskovou jednotku. V důsledku toho je vyhrazená spotřeba založená na jednotce SKU disku namísto zadané velikosti.

Předpokládejme například, že rezervujete jeden disk P40, který má 2 TiBy zřízené kapacity úložiště. Předpokládejme také, že přidělíte pouze dva P30 disky. V takovém případě P40 rezervace nevytváří účet pro P30 spotřebu a platíte sazbu průběžných plateb na disky P30.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Důležité informace o nákupu

Při zvažování nákupu rezervovaného disku doporučujeme následující postupy:

- Analýza informací o použití vám pomůže určit, které rezervace byste měli koupit. Ujistěte se, že sledujete využití diskových SKU místo zřízené nebo využité kapacity disku.
- Projděte si rezervaci disku společně s vaší rezervací virtuálního počítače. Důrazně doporučujeme, abyste pro maximum úspory prováděli rezervace pro jak využití virtuálních počítačů, tak využití disku. Můžete začít určením správné rezervace virtuálních počítačů a pak vyhodnotit rezervaci disku. Obecně platí, že pro každou z vašich úloh bude k dispozici standardní konfigurace. Například server SQL Server může mít dva datové disky P40 a jeden disk s operačním systémem P30.
  
  Tento druh vzoru vám pomůže určit rezervovanou částku, kterou si můžete koupit. Tento přístup může zjednodušit proces vyhodnocení a zajistit, že máte zarovnaný plán pro virtuální počítač i disky. Plán obsahuje okolnosti, jako jsou předplatná nebo oblasti.

## <a name="purchase-restrictions"></a>Omezení nákupu

Slevy za rezervované nyní nejsou k dispozici pro následující:

- Nespravované disky nebo objekty blob stránky.
- Standardní SSD nebo standardní jednotky pevného disku (HDD).
- SSD úrovně Premium SKU menší než P30: P1, P2, P3, P4, P6, P10, P15 a P20 SKU SSD.
- Disky v oblastech Azure Government, Azure Německo nebo Azure Čína.

Ve výjimečných případech Azure omezuje nákup nových rezervací na podmnožinu diskových SKU z důvodu nízké kapacity v oblasti.

## <a name="buy-a-disk-reservation"></a>Nákup rezervovaného disku

Můžete zakoupit Azure Disk Storage rezervace prostřednictvím [Azure Portal](https://portal.azure.com/). Můžete zaplatit za rezervaci buď předem, nebo s měsíčními platbami. Další informace o nákupu s měsíčními platbami najdete v tématu [Nákup rezervací s měsíčními platbami](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments).

K zakoupení rezervované kapacity použijte následující postup:

1. V Azure Portal přejdete do podokna [Nákupní rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) .

1. Pokud si chcete koupit rezervaci, vyberte **Azure Managed disks** .

    ![Podokno pro nákup rezervací](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Zadejte požadované hodnoty popsané v následující tabulce:

   |Element  |Popis  |
   |---------|---------|
   |**Rozsah**   |  Kolik předplatných může využívat fakturační výhody spojené s rezervací. Tato hodnota také určuje, jak se rezervace použije u konkrétních předplatných. <br/><br/> Pokud vyberete **Shared (sdílená**), použije se sleva rezervace pro Azure Storage kapacity v rámci každého předplatného v rámci vašeho fakturačního kontextu. Fakturační kontext vychází z toho, jak jste se zaregistrovali do Azure. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami, sdílený obor zahrnuje všechna jednotlivá předplatná s tarify průběžných plateb vytvořenými správcem účtu.  <br/><br/>  Pokud vyberete **jedno předplatné**, použije se sleva rezervace pro Azure Storage kapacity ve vybraném předplatném. <br/><br/> Když vyberete **jednu skupinu prostředků**, použije se sleva rezervace pro Azure Storage kapacity ve vybraném předplatném a v vybrané skupině prostředků tohoto předplatného. <br/><br/> Po zakoupení rezervace můžete obor rezervací změnit.  |
   |**Předplatné**  | Předplatné, které používáte pro platbu za Azure Storage rezervaci. Způsob platby ve vybraném předplatném se používá při zpoplatnění nákladů. Předplatné musí být jeden z následujících typů:<br/><ul><li> Smlouva Enterprise (nabídka čísla MS-AZR-0017P a MS-AZR-0148P). U podnikového předplatného se poplatky odečtou z bilance peněžního závazku registrace nebo se účtují jako nadlimitní využití.</li><br/><li>Individuální předplatné s tarify průběžných plateb (nabídky Numbers MS-AZR-0003P a MS-AZR-0023P). U jednotlivých předplatných s tarify průběžných plateb se poplatky účtují na základě platební karty nebo platby na faktuře v předplatném.</li></ul>    |
   | **Disky** | Skladová položka, kterou chcete vytvořit. |
   | **Oblast** | Oblast, ve které je rezervace platná. |
   | **Četnost fakturace** | Jak často se účtuje účet pro rezervaci. Mezi možnosti patří **měsíční** a **předem**. |

    ![Podokno pro výběr produktu, který chcete purchase.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Po zadání hodnot pro rezervaci Azure Portal zobrazí náklady. Na portálu se také zobrazuje procento slevy při fakturaci s průběžnými platbami. Kliknutím na tlačítko **Další** pokračujte v podokně **rezervace nákupu** .

1. V podokně **rezervace nákupu** můžete pojmenovat rezervaci a vybrat celkové množství rezervací, které chcete vytvořit. Počet rezervací se mapuje na počet disků. Pokud například chcete rezervovat stovky disků, zadejte hodnotu **množství** **100**.

1. Zkontrolujte celkové náklady na rezervaci.

    ![Podokno rezervace nákupu](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Po zakoupení rezervace se automaticky použije na všechny existující Disk Storage prostředky, které odpovídají podmínkám rezervací. Pokud jste ještě nevytvořili žádné prostředky Disk Storage, rezervace se použije pokaždé, když vytvoříte prostředek, který odpovídá podmínkám rezervace. V obou případech se termín rezervace začíná ihned po úspěšném nákupu.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace můžete v určitých omezeních zrušit, vyměnit nebo vrátit. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="expiration-of-a-reservation"></a>Vypršení platnosti rezervace

Po vypršení platnosti rezervace se veškerá Azure Disk Storage kapacita, kterou použijete v rámci této rezervace, účtuje podle tarifu průběžných plateb. Rezervace se neprodlužují automaticky.

Po 30 dnech se vám pošle e-mailové oznámení před vypršením platnosti rezervace a znovu k datu vypršení platnosti. Pokud chcete pokračovat v využívání výhod úspory nákladů, které poskytuje rezervace, obnovte ji později než datum vypršení platnosti.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Co jsou rezervace Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Pochopení způsobu použití slevy rezervace pro Azure Disk Storage](../cost-management-billing/reservations/understand-disk-reservations.md)
