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
ms.openlocfilehash: cb959b94807678187363d3132ece273584f13a0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590709"
---
Ušetřete využití diskového úložiště Azure s rezervovanou kapacitou. Rezervace azure diskového úložiště v kombinaci s instancemi vyhrazených virtuálních počítačů Azure vám umožní snížit celkové náklady na virtuální počítač. Sleva rezervace je automaticky použita na odpovídající disky ve vybraném oboru rezervace. Z důvodu této automatické aplikace není nutné přiřadit rezervaci spravovanému disku, abyste získali slevy.

Slevy se uplatňují každou hodinu v závislosti na využití disku. Nevyužitá rezervovaná kapacita se nepřenáší. Slevy na rezervaci úložiště disků Azure se nevztahují na nespravované disky, ultra disky ani na spotřebu objektů blob stránky.

## <a name="determine-your-storage-needs"></a>Určení potřeb úložiště

Před zakoupením rezervace určete potřeby úložiště. V současné době jsou rezervace azure diskového úložiště k dispozici jenom pro vybrané skladové jednotky SSD Azure premium. Skladová položka prémiového ssd disku určuje velikost a výkon disku.

Při určování potřeb úložiště nemyslete na disky založené pouze na kapacitě. Například nemůžete mít rezervaci pro disk P40 a použít ji k zaplacení dvou menších disků P30. Při nákupu rezervace nakupujete rezervaci pouze pro celkový počet disků na skladovou položku.

Rezervace disku se provádí na disk SKU. V důsledku toho je spotřeba rezervace založena na jednotce skladových jednotek disku namísto poskytnuté velikosti.

Předpokládejme například, že si rezervujete jeden disk P40, který má 2 TiB zřízené kapacity úložiště. Také předpokládejme, že přidělujete pouze dva disky P30. Rezervace P40 v tomto případě neúčtuje spotřebu P30 a platíte sazbu průběžných plateb na discích P30.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Aspekty nákupu

Při zvažování nákupu rezervace disku doporučujeme následující postupy:

- Analyzujte informace o využití a zjistěte, které rezervace byste měli zakoupit. Ujistěte se, že sledujete využití v diskových skladových cenících míst namísto zřízené nebo použité kapacity disku.
- Zkontrolujte rezervaci disku spolu s rezervací virtuálních počítačů. Důrazně doporučujeme provádět rezervace pro využití virtuálních počítačů i využití disku pro maximální úspory. Můžete začít s určením správné rezervace virtuálního počítače a pak vyhodnotit rezervaci disku. Obecně platí, že budete mít standardní konfiguraci pro každou z vašich úloh. Server SQL Server může mít například dva datové disky P40 a jeden disk operačního systému P30.
  
  Tento druh vzoru vám může pomoci určit rezervovanou částku, kterou můžete zakoupit. Tento přístup můžete zjednodušit proces hodnocení a ujistěte se, že máte zarovnaný plán pro váš virtuální počítač a disky. Plán obsahuje aspekty, jako jsou odběry nebo oblasti.

## <a name="purchase-restrictions"></a>Omezení nákupu

Slevy na rezervaci nejsou momentálně k dispozici pro následující:

- Nespravované disky nebo objekty BLOB stránky.
- Standardní ssd disky nebo standardní pevné disky (pevné disky).
- SLOK SSD Premium menší než P30: P1, P2, P3, P4, P6, P10, P15 a P20 SSD SKU.
- Disky v oblastech Azure Government, Azure Germany nebo Azure China.

Ve výjimečných případech Azure omezuje nákup nových rezervací na podmnožinu diskových sousk z důvodu nízké kapacity v oblasti.

## <a name="buy-a-disk-reservation"></a>Koupit rezervaci disku

Rezervace Azure Disk Storage můžete zakoupit na [webu Azure Portal](https://portal.azure.com/). Rezervaci můžete zaplatit předem nebo měsíčně. Další informace o nákupu s měsíčními platbami najdete v [tématu Nákupní rezervace s měsíčními platbami](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).

Chcete-li zakoupit rezervovanou kapacitu, postupujte takto:

1. Přejděte do podokna [Rezervace nákupu](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) na webu Azure Portal.

1. Chcete-li zakoupit rezervaci, vyberte **spravované disky Azure.**

    ![Podokno pro nákup rezervací](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Zadejte požadované hodnoty popsané v následující tabulce:

   |Element  |Popis  |
   |---------|---------|
   |**Rozsah**   |  Kolik předplatných může využít fakturační výhodu přidruženou k rezervaci. Tato hodnota také určuje, jak je rezervace použita pro konkrétní odběry. <br/><br/> Pokud vyberete **Sdílené**, sleva na rezervaci se použije na kapacitu úložiště Azure v každém předplatném v rámci fakturačního kontextu. Kontext fakturace je založen na tom, jak jste se zaregistrovali do Azure. Pro podnikové zákazníky je sdílený obor registrace a zahrnuje všechna předplatná v rámci registrace. U zákazníků s průběžnými platbami zahrnuje sdílený obor všechna jednotlivá předplatná s průběžnými platbami vytvořenými správcem účtu.  <br/><br/>  Pokud vyberete **jedno předplatné**, sleva na rezervaci se použije na kapacitu úložiště Azure ve vybraném předplatném. <br/><br/> Pokud vyberete **skupinu prostředků Single**, sleva na rezervaci se použije na kapacitu úložiště Azure ve vybraném předplatném a ve vybrané skupině prostředků tohoto předplatného. <br/><br/> Rozsah rezervace můžete změnit po zakoupení rezervace.  |
   |**Předplatné**  | Předplatné, které používáte k platbě za rezervaci úložiště Azure. Způsob platby u vybraného předplatného se používá při účtování nákladů. Předplatné musí být jedním z následujících typů:<br/><ul><li> Enterprise Agreement (nabídka čísel MS-AZR-0017P a MS-AZR-0148P). U předplatného Enterprise se poplatky odečtou ze zůstatku peněžního závazku registrace nebo se účtují jako nadbytečné.</li><br/><li>Individuální předplatné s průběžnými platbami (nabídka čísel MS-AZR-0003P a MS-AZR-0023P). U individuálního předplatného s průběžnými platbami se poplatky účtují na základě platební karty nebo způsobu platby faktury v rámci předplatného.</li></ul>    |
   | **Disky** | Skladová položka, kterou chcete vytvořit. |
   | **Oblasti** | Oblast, kde je rezervace v platnosti. |
   | **Frekvence fakturace** | Jak často se účtuje rezervace. Možnosti zahrnují **měsíční** a **předem .** |

    ![Podokno pro výběr produktu, který chcete zakoupit.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Po zadání hodnot pro rezervaci se na portálu Azure zobrazí náklady. Portál také zobrazuje procento slevy oproti fakturaci s průběžnou platbou. Chcete-li pokračovat v podokně **Rezervace nákupu,** vyberte **možnost Další.**

1. V podokně **Nákupní rezervace** můžete pojmenovat svou rezervaci a vybrat celkové množství rezervací, které chcete provést. Počet rezervací se mapuje na počet disků. Chcete-li například rezervovat sto disků, zadejte hodnotu **Množství** **100**.

1. Zkontrolujte celkové náklady na rezervaci.

    ![Podokno Rezervace nákupu](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Po zakoupení rezervace se automaticky použije na všechny existující prostředky diskového úložiště, které odpovídají podmínkám rezervace. Pokud jste ještě nevytvořili žádné prostředky úložiště disků, rezervace platí vždy, když vytvoříte prostředek, který odpovídá podmínkám rezervace. V obou případech začíná doba rezervace bezprostředně po úspěšném nákupu.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace můžete zrušit, vyměnit nebo refundovat v rámci určitých omezení. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="expiration-of-a-reservation"></a>Vypršení rezervace

Po vypršení platnosti rezervace se bude účtovat jakákoli kapacita úložiště disků Azure, kterou v rámci této rezervace použijete, s průběžnou sazbou. Rezervace se neprodlužují automaticky.

30 dní před vypršením rezervace a znovu v den vypršení platnosti obdržíte e-mailové oznámení. Chcete-li pokračovat v využití úspor nákladů, které poskytuje rezervace, obnovte ji nejpozději k datu vypršení platnosti.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Co jsou rezervace Azure?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Principy uplatnění slevy za rezervaci ve službě Azure Disk Storage](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
