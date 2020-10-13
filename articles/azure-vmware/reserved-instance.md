---
title: Úspora nákladů pomocí rezervované instance řešení Azure VMware
description: Naučte se koupit rezervovanou instanci pro řešení Azure VMware.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: fa354d4fbfef868ea1e6783656be7871669f200d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951413"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Úspora nákladů pomocí řešení Azure VMware

Když se potvrdíte do rezervované instance řešení Azure VMware, můžete ušetřit peníze. Sleva rezervovaného prostředí se automaticky použije na počet spuštěných hostitelů řešení Azure VMware, které odpovídají oboru a atributům rezervace. K získání slev nemusíte přiřazovat rezervaci k vyhrazenému hostiteli. Zakoupení rezervované instance pokrývá jenom výpočetní část vašeho využití a zahrnuje náklady na licencování softwaru. Podívejte se na [Přehled řešení Azure VMware](introduction.md).

## <a name="purchase-restriction-considerations"></a>Požadavky na omezení nákupu

Rezervované instance jsou k dispozici s některými výjimkami.

-   **Cloudy**   – Rezervace jsou k dispozici pouze v oblastech uvedených na stránce [produkty dostupné podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Nedostatečná kvóta**   – Rezervace, která je vymezená pro jedno nebo sdílené předplatné, musí mít v předplatném k dispozici kvótu hostitelů pro novou rezervovanou instanci. K vyřešení tohoto problému můžete [vytvořit žádost o zvýšení kvóty](enable-azure-vmware-solution.md) .

-   **Nárok na nabídku**– budete potřebovat [Azure smlouva Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md)   s Microsoftem.

-   **Omezení kapacity**   – Ve výjimečných případech Azure omezuje nákup nových rezervací pro skladové jednotky hostitele řešení Azure VMware z důvodu nízké kapacity v oblasti.

## <a name="buy-a-reservation"></a>Nákup rezervace

Rezervovanou instanci instance hostitele řešení Azure VMware můžete koupit v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Platíte za rezervaci [předem nebo s měsíčními platbami](../cost-management-billing/reservations/prepare-buy-reservation.md).

Tyto požadavky se vztahují k zakoupení rezervované instance vyhrazeného hostitele:

-   Musíte být v roli vlastníka alespoň pro jedno předplatné EA nebo předplatné s tarifem průběžných plateb.

-   Pro předplatná EA **Add Reserved Instances**   musí být na [portálu EA](https://ea.azure.com/)povolená možnost Přidat rezervované instance. Pokud je toto nastavení zakázané, musíte být správcem smlouvy Enterprise pro příslušné předplatné.

Postup zakoupení instance:

1. Přihlaste se na  [Azure Portal](https://portal.azure.com/).

2. Vyberte **všechny**   >  **rezervace**služeb.

3. Vyberte **Přidat**a   Zakupte novou rezervaci a pak vyberte **Řešení Azure VMware**.

4. Vyplňte požadovaná pole. Pokud chcete získat slevu rezervace, spusťte hostitele řešení Azure VMware, které odpovídají vybraným atributům. Skutečný počet hostitelů řešení Azure VMware, které obdrží slevu, závisí na zvoleném rozsahu a množství.

   Pokud máte smlouvu EA, můžete k rychlému přidání dalších instancí použít **možnost přidat další**   . Možnost není k dispozici pro jiné typy předplatného.

   | Pole        |  Popis |
   | ------------ | ------------ |
   | Předplatné | Předplatné použité pro platbu za rezervaci. Náklady na kapacitu se účtují pomocí způsobu platby zvoleného pro toto předplatné. Typ předplatného musí být smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P), zákaznická smlouva Microsoftu nebo jednotlivá předplatná s tarify průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). Poplatky se strhávají z peněžního zůstatku, pokud je k dispozici, nebo se účtují jako nadlimitní využití. U předplatného s tarify průběžných plateb se poplatky účtují na základě platební karty nebo platby na faktuře v rámci předplatného. |
   | Rozsah        | Rozsah rezervace může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete:<br><ul><li><b>Obor jedné skupiny prostředků – aplikuje slevu na rezervované prostředky jenom na ty, které jsou ve vybrané skupině prostředků.</li><li><b>Rozsah jednoho předplatného – aplikuje slevu na rezervované prostředky ve vybraném předplatném.</li><li><b>Sdílený rozsah – použije slevu rezervace na odpovídající prostředky v oprávněných předplatných, která jsou v účetním kontextu. Pro zákazníky se smlouvou EA je účetním kontextem registrace. U individuálních předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.</li></ul>       |
   | Oblast       | Oblast Azure, která je pokrytá rezervací.   |
   | Velikost hostitele    | AV36    |
   | Označení         | Jeden rok nebo tři roky.  |
   | Množství     | Počet instancí zakoupených v rámci rezervace. Množství je počet spuštěných hostitelů řešení Azure VMware, které mohou získat fakturační slevu.    |

## <a name="usage-data-and-reservation-utilization"></a>Využití a data využití rezervací

V datech o využití se pro využití, které získá slevu za rezervaci, zobrazuje platná nulová cena. Můžete zjistit, která instance řešení Azure VMware obdržela slevu rezervace pro každou rezervaci.

Další informace o tom, jak se v datech o využití zobrazují slevy, a Vy jste zákazníkem EA, najdete v tématu [vysvětlení využití rezervace Azure pro vaši podnikovou registraci](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md). Pokud máte individuální předplatné, přečtěte si téma [vysvětlení využití rezervace Azure pro předplatné](../cost-management-billing/reservations/understand-reserved-instance-usage.md)s průběžnými platbami.

## <a name="change-a-reservation-after-purchase"></a>Změna rezervace po nákupu

V zakoupené rezervaci můžete provádět následující typy změn:

-   Aktualizace rozsahu rezervace

-   Flexibilita velikosti instance (Pokud je k dispozici)

-   Vlastnictví

Rezervaci můžete také rozdělit do menších bloků dat nebo do rezervací sloučení. Žádná z těchto změn nezpůsobí novou komerční transakci ani nemění koncové datum rezervace.

>[!NOTE]
>Po zakoupení rezervace nebudete moci provádět následující typy změn přímo:
>
> - Existující oblast rezervace
> - SKU
> - Množství
> - Doba trvání
>
>Můžete ale *vyměnit*   rezervaci, pokud chcete provést změny.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu věnovaném [výměnám samoobslužných služeb a refundacím pro Azure reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).