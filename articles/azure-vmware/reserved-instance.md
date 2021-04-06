---
title: Úspora nákladů pomocí rezervované instance řešení Azure VMware
description: Naučte se koupit rezervovanou instanci pro řešení Azure VMware.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 227ea9274f70cc58468e7e0b3ddcf8239f5b7f5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550971"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Úspora nákladů pomocí řešení Azure VMware

Po potvrzení do rezervované instance [Řešení Azure VMware](introduction.md)ušetříte peníze.  Sleva rezervace se automaticky použije na spuštěné hostitele řešení VMware Azure, které odpovídají oboru a atributům rezervace. Zakoupení rezervované instance pokrývá jenom výpočetní část vašeho využití a zahrnuje náklady na licencování softwaru. 

## <a name="purchase-restriction-considerations"></a>Požadavky na omezení nákupu

Rezervované instance jsou k dispozici s některými výjimkami.

-   **Cloudy** – rezervace jsou k dispozici pouze v oblastech uvedených na stránce [Dostupné produkty podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Nedostatečná kvóta** – rezervace vymezená u jednoho nebo sdíleného předplatného musí mít v předplatném k dispozici kvótu hostitelů pro novou rezervovanou instanci. K vyřešení tohoto problému můžete [vytvořit žádost o zvýšení kvóty](enable-azure-vmware-solution.md) .

-   **Nárok na nabídku**– budete potřebovat [Azure smlouva Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md) s Microsoftem.

-   **Omezení kapacity** – Azure omezuje nákup nových rezervací pro skladové jednotky hostitele řešení Azure VMware z důvodu nízké kapacity v oblasti.

## <a name="buy-a-reservation"></a>Nákup rezervace

Rezervovanou instanci instance hostitele řešení Azure VMware můžete koupit v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Můžete zaplatit za rezervaci [předem nebo s měsíčními platbami](../cost-management-billing/reservations/prepare-buy-reservation.md).

Tyto požadavky se vztahují k zakoupení rezervované instance vyhrazeného hostitele:

-   Musíte být v roli *vlastníka* alespoň pro jedno předplatné EA nebo předplatné s tarifem průběžných plateb.

-   U předplatných EA musíte na [portálu EA](https://ea.azure.com/)povolit možnost **Přidat rezervované instance** . Pokud je tato možnost zakázaná, musíte být pro toto předplatné správcem EA.

-   U předplatného v rámci plánu Azure Cloud Solution Provider (CSP) musí partner zakoupit rezervované instance v Azure Portal pro zákazníka. 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>Nákup rezervovaných instancí pro předplatné EA

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte **Všechny služby** > **Rezervace**.

3. Vyberte **koupit hned** a pak vyberte **Řešení Azure VMware**.

4. Zadejte požadovaná pole. Vybrané atributy, které odpovídají spuštěným hostitelům řešení Azure VMware, mají nárok na slevu za rezervaci.  Mezi atributy patří SKU, oblasti (kde je to možné) a rozsah. Rozsah rezervace určuje, kde se úspory získané rezervací uplatní.

   Pokud máte smlouvu EA, můžete k rychlému přidávání instancí použít **možnost přidat další** . Možnost není k dispozici pro jiné typy předplatného.

   | Pole        |  Popis |
   | ------------ | ------------ |
   | Předplatné | Předplatné použité pro platbu za rezervaci. Náklady na kapacitu se účtují pomocí způsobu platby zvoleného pro toto předplatné. Typ předplatného musí být smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P), smlouva o zákaznících Microsoftu nebo individuální předplatné s tarify průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). Poplatky se strhávají z Azure Prepaymentu (dřív označovaný jako peněžní závazek), pokud je k dispozici, nebo se účtují jako nadlimitní využití. U předplatného s tarify průběžných plateb se poplatky účtují na platební kartu předplatného nebo na platební metodu faktury. |
   | Obor        | Rozsah rezervace může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete:<br><ul><li><b>Rozsah jedné skupiny prostředků</b> – aplikuje slevu na rezervované prostředky jenom na ty, které jsou ve vybrané skupině prostředků.</li><li><b>Rozsah jednoho předplatného</b> – aplikuje slevu na rezervované prostředky ve vybraném předplatném.</li><li><b>Sdílený rozsah</b> – použije slevu rezervace na odpovídající prostředky v oprávněných předplatných, která jsou v účetním kontextu. Pro zákazníky se smlouvou EA je účetním kontextem registrace. U individuálních předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.</li></ul>       |
   | Oblast       | Oblast Azure, která je pokrytá rezervací.   |
   | Velikost hostitele    | AV36    |
   | Označení         | Jeden rok nebo tři roky.  |
   | Množství     | Počet instancí, které se mají koupit v rámci rezervace Množství je počet spuštěných hostitelů řešení Azure VMware, které mohou získat fakturační slevu.    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>Nákup rezervovaných instancí pro předplatné CSP

Poskytovatelé CSP, kteří chtějí koupit rezervované instance pro své zákazníky, musí v [dokumentaci partnerského centra](/partner-center/azure-plan-manage)použít proceduru **správce jménem** (administrate). Další informace najdete ve videu [správce za uživatele (administrate)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) .

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com).

2. Vyberte **CSP** pro přístup k oblasti **Customers** .

3. Rozbalte položku Podrobnosti o zákazníkovi a vyberte **portál pro správu Microsoft Azure**. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Oblast pro zákazníky partnerského centra Microsoftu" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. V Azure Portal vyberte **všechny**  >  **rezervace** služeb.

5. Vyberte **koupit hned** a pak vyberte **Řešení Azure VMware**.

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="portál Microsoft Azure rezervace" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. Zadejte požadovaná pole. Vybrané atributy, které odpovídají spuštěným hostitelům řešení Azure VMware, mají nárok na slevu za rezervaci.  Mezi atributy patří SKU, oblasti (kde je to možné) a rozsah. Rozsah rezervace určuje, kde se úspory získané rezervací uplatní.

   | Pole        |  Popis |
   | ------------ | ------------ |
   | Předplatné | Předplatné použité pro platbu za rezervaci. Náklady na kapacitu se účtují pomocí způsobu platby zvoleného pro toto předplatné. Typ předplatného musí mít nárok na jeden, což je v tomto případě předplatné CSP.|
   | Obor        | Rozsah rezervace může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete:<br><ul><li><b>Rozsah jedné skupiny prostředků</b> – aplikuje slevu na rezervované prostředky jenom na ty, které jsou ve vybrané skupině prostředků.</li><li><b>Rozsah jednoho předplatného</b> – aplikuje slevu na rezervované prostředky ve vybraném předplatném.</li><li><b>Sdílený rozsah</b> – použije slevu rezervace na odpovídající prostředky v oprávněných předplatných, která jsou v účetním kontextu. Pro zákazníky se smlouvou EA je účetním kontextem registrace. U individuálních předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.</li></ul>       |
   | Oblast       | Oblast Azure, která je pokrytá rezervací.   |
   | Velikost hostitele    | AV36    |
   | Označení         | Jeden rok nebo tři roky.  |
   | Množství     | Počet instancí, které se mají koupit v rámci rezervace Množství je počet spuštěných hostitelů řešení Azure VMware, které mohou získat fakturační slevu.     |

Další informace o tom, jak zobrazit zakoupené rezervace pro zákazníka, najdete v článku [zobrazení rezervací Azure jako poskytovatele Cloud Solution Provider (CSP)](../cost-management-billing/reservations/how-to-view-csp-reservations.md) .

## <a name="usage-data-and-reservation-usage"></a>Data o využití a využití rezervací

Vaše využití, které získává slevu na rezervaci, má platnou cenu s hodnotou nula. Můžete zjistit, která instance řešení Azure VMware obdržela slevu rezervace pro každou rezervaci.

Další informace o tom, jak se zobrazují slevy za rezervované údaje o využití:

- Informace pro zákazníky se smlouvou EA najdete v tématu [vysvětlení využití rezervace Azure pro podnikovou registraci](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) .
- Informace o jednotlivých předplatných najdete v tématu [vysvětlení využití rezervace Azure pro předplatné](../cost-management-billing/reservations/understand-reserved-instance-usage.md) s průběžnými platbami.

## <a name="change-a-reservation-after-purchase"></a>Změna rezervace po nákupu

Tyto změny rezervace můžete provést po nákupu:

-   Aktualizace rozsahu rezervace

-   Flexibilita velikosti instance (Pokud je k dispozici)

-   Vlastnictví

Rezervaci můžete také rozdělit do menších bloků dat nebo do rezervací sloučení. Žádná z těchto změn nezpůsobí novou komerční transakci ani nemění koncové datum rezervace.

Podrobnosti o rezervacích spravovaných zprostředkovatelem CSP najdete v tématu [prodej Microsoft Azure rezervacích zákazníkům pomocí partnerských Center, Azure Portal nebo rozhraní API](/partner-center/azure-reservations).



>[!NOTE]
>Po zakoupení rezervace nebudete moci provádět tyto typy změn přímo:
>
> - Existující oblast rezervace
> - SKU
> - Množství
> - Doba trvání
>
>Můžete ale *vyměnit* rezervaci, pokud chcete provést změny.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

Poskytovatelé CSP můžou zrušit, vyměnit nebo vrátit refundaci s určitými omezeními, která si zakoupili pro zákazníka. Další informace najdete v tématu [Správa, zrušení, výměna nebo refundace Microsoft Azure rezervacích pro zákazníky](/partner-center/azure-reservations-manage).

## <a name="next-steps"></a>Další kroky

Teď, když jste si pokryli, že jste si koupili rezervovanou instanci řešení Azure VMware, možná se budete chtít dozvědět něco:

- [Vytváření posouzení řešení Azure VMware](../migrate/how-to-create-azure-vmware-solution-assessment.md).
- [Správa DHCP pro řešení Azure VMware](manage-dhcp.md).
- [Správa životního cyklu virtuálních počítačů řešení Azure VMware](lifecycle-management-of-azure-vmware-solution-vms.md).
 