---
title: Vysvětlení slevy za Azure Reserved VM Instances
description: Zjistěte, jak se na běžící virtuální počítače uplatňuje sleva za rezervované instance virtuálních počítačů Azure.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: e9ee1d9ff8145051bbbe6b65004f7f358b5fdade
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132138"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Jak se na virtuální počítače uplatňuje sleva za rezervaci Azure

Když si zakoupíte rezervovanou instanci virtuálního počítače Azure, sleva za rezervaci automaticky se uplatňuje na virtuální počítače, které vyhovují rezervovaným atributům a množství. Rezervace pokrývá náklady na výpočetní prostředky virtuálních počítačů.

Sleva za rezervaci se uplatňuje na základní virtuální počítače, které zakoupíte ve službě Azure Marketplace.

Informace o záložní kapacitě služby SQL Database najdete v tématu [Vysvětlení slevy za rezervované instance Azure](../reservations/understand-reservation-charges.md).

Následující tabulka ukazuje náklady na virtuální počítač po zakoupení rezervované instance virtuálního počítače. Poplatky za úložiště a sítě se vám budou ve všech případech účtovat za použití normálních sazeb.

| Typ virtuálního počítače  | Poplatky s rezervovanou instancí virtuálního počítače |
|-----------------------|--------------------------------------------|
|Virtuální počítače s Linuxem bez dalšího softwaru | Rezervace pokrývá náklady na infrastrukturu virtuálních počítačů.|
|Virtuální počítače s Linuxem včetně poplatků za software (například Red Hat) | Rezervace pokrývá náklady na infrastrukturu. Účtují se vám poplatky za další software.|
|Virtuální počítače s Windows bez dalšího softwaru |Rezervace pokrývá náklady na infrastrukturu. Účtují se vám poplatky za software Windows.|
|Virtuální počítače s Windows včetně dalšího softwaru (například SQL Server) | Rezervace pokrývá náklady na infrastrukturu. Účtují se vám poplatky za software Windows a další software.|
|Virtuální počítače s Windows s programem [Zvýhodněné hybridní využití Azure](../../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Rezervace pokrývá náklady na infrastrukturu. Náklady na software Windows pokrývá program Zvýhodněné hybridní využití Azure. Veškerý další software se účtuje samostatně.|

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

*Nevyužité slevy se nenahrazují*. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny vám nemůžeme převést.

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny *přijdete*.

## <a name="reservation-discount-for-non-windows-vms"></a>Sleva za rezervaci pro virtuální počítače s jiným systémem než Windows

 Sleva za rezervaci v Azure se na spuštěné instance virtuálních počítačů uplatňuje po hodinách. Rezervace, které jste zakoupili, se přiřadí ke spotřebě spuštěných virtuálních počítačů a uplatní se na ni sleva za rezervaci. U virtuálních počítačů, které neběží celou hodinu, se rezervace zaplní z jiných virtuálních počítačů, které rezervaci nemají, včetně souběžně spuštěných virtuálních počítačů. Na konci dané hodiny se uplatnění rezervace na virtuální počítače za danou hodinu uzamkne. Pokud některý virtuální počítač neběží celou hodinu nebo danou rezervovanou hodinu nezaplní souběžně spuštěné virtuální počítače, rezervace na danou hodinu se nevyužije celá. Uplatňování rezervace na fakturovatelné využití virtuálních počítačů znázorňuje následující graf. Graf počítá s jednou zakoupenou rezervací a dvěma vyhovujícími instancemi virtuálních počítačů.

![Snímek obrazovky znázorňující jednu uplatněnou rezervaci a dvě vyhovující instance virtuálních počítačů](./media/understand-vm-reservation-charges/billing-reserved-vm-instance-application.png)

1. Každé využití nad limit rezervace se účtuje pomocí standardních sazeb pro průběžné platby. Za využití do limitu rezervace se vám neúčtují žádné poplatky, protože už jste toto využití zaplatili při zakoupení rezervace.
2. Za 1. hodinu běží instance 1 po dobu 0,75 hodiny a instance 2 po dobu 0,5 hodiny. Celkové využití za 1. hodinu je 1,25 hodiny. Za 0,25 hodiny nad limit se vám naúčtuje běžná sazba pro průběžné platby.
3. Za 2. a 3. hodinu běží obě instance po dobu 1 hodiny. Jednu instanci pokryje rezervace a u druhé se vám naúčtuje sazba pro průběžné platby.
4. Za 4. hodinu běží instance 1 po dobu 0,5 hodiny a instance 2 po dobu 1 hodiny. Instanci 1 plně pokryje rezervace a 0,5 hodiny chodu instance 2 také. Za 0,5 hodiny nad limit se vám naúčtuje běžná sazba pro průběžné platby.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací](../reservations/understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Sleva za rezervaci pro virtuální počítače s Windows

Pokud používáte instance virtuálních počítačů s Windows, rezervace pokrývá náklady na infrastrukturu. Rezervace se u virtuálních počítačů s Windows uplatňuje na náklady na infrastrukturu virtuálních počítačů stejným způsobem jako u virtuálních počítačů s jiným systémem než Windows. Poplatky za software Windows se vám účtují odděleně na základě využívání vCPU. Podívejte se na [náklady na software Windows s rezervacemi](../reservations/reserved-instance-windows-software-costs.md). Náklady na licence Windows můžete pokrýt pomocí programu [Zvýhodněné hybridní využití Azure pro Windows Server](../../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Sleva se může vztahovat na různé velikosti

Pokud si koupíte rezervovanou instanci virtuálního počítače a vyberete možnost **optimalizace pro flexibilní velikost instance**, pokrytí slevou se využije pro zvolenou velikost virtuálního počítače. Může se vztahovat i na jiné velikosti virtuálních počítačů, které jsou ve stejné skupině flexibilních velikostí instancí. Další informace najdete v tématu [Flexibilita velikosti virtuálních počítačů s rezervovanými instancemi virtuálních počítačů](../../virtual-machines/reserved-vm-instance-size-flexibility.md).

## <a name="premium-storage-vms-dont-get-non-premium-discounts"></a>Virtuální počítače služby Premium Storage nezískávají slevy jiného typu než Premium

Tady je příklad. Předpokládejme, že jste si koupili rezervaci pro pět virtuálních počítačů Standard_D1. Sleva za rezervace se vztahuje jenom na virtuální počítače Standard_D1 nebo jiné virtuální počítače ve stejné rodině instancí. Tato sleva se nevztahuje na virtuální počítače Standard_DS1 ani na jiné velikosti ve skupině flexibilních velikostí instancí DS1.

Při uplatňování slevy za rezervaci se nebere v úvahu měřič používaný pro virtuální počítače a vyhodnocuje se jenom typ služby. Podívejte se na hodnotu `ServiceType` v `AdditionalInfo` a určete řadu/skupinu flexibility instance pro vaše virtuální počítače. Tyto hodnoty najdete v souboru CSV s informacemi o využití.

Řadu/skupinu flexibility instance pro rezervaci nemůžete po jejím zakoupení měnit přímo. Můžete ale *vyměnit* rezervaci virtuálního počítače z jedné řady/skupiny flexibility instance za jinou.

## <a name="services-that-get-vm-reservation-discounts"></a>Služby, které získávají slevy za rezervaci virtuálních počítačů

Rezervace virtuálních počítačů se můžou vztahovat na využití virtuálních počítačů z několika služeb, nejen na vaše nasazení virtuálních počítačů. To, které prostředky získávají slevy za rezervované instance, se liší v závislosti na nastavení flexibility velikosti instance.

### <a name="instance-size-flexibility-setting"></a>Nastavení flexibility velikosti instance

Nastavení flexibility velikosti instance určuje, které služby mají získat slevy za rezervované instance.

Když má položka *ConsumedService* hodnotu `Microsoft.Compute`, slevy za rezervace se automaticky uplatňují na veškeré využití vyhovujících virtuálních počítačů bez ohledu na to, jestli je nastavení zapnuté, nebo vypnuté. Proto si u hodnoty *ConsumedService* kontrolujte údaje o využití. Možné příklady:

- Virtuální počítače
- Škálovací sady virtuálních počítačů
- Služba kontejneru
- Nasazení služby Azure Batch (v režimu předplatných uživatelů)
- Azure Kubernetes Service (AKS)
- Service Fabric

Když je nastavení zapnuté a položka *ConsumedService* má některou z následujících hodnot, na veškeré využití vyhovujících virtuálních počítačů se automaticky uplatňují slevy za rezervace:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

V datech využití si zkontrolujte hodnotu *ConsumedService*, abyste zjistili, jestli má dané využití nárok na slevy za rezervace.

Další informace o flexibilitě velikosti instance najdete v tématu [Flexibilita velikosti virtuálních počítačů s rezervovanými instancemi virtuálních počítačů](../../virtual-machines/reserved-vm-instance-size-flexibility.md).


## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace v Azure?](../reservations/save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Správa rezervací v Azure](../reservations/manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../reservations/understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](../reservations/understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervací u předplatných CSP](/partner-center/azure-reservations)
- [Náklady na software pro Windows nezahrnuté v rezervacích](../reservations/reserved-instance-windows-software-costs.md)