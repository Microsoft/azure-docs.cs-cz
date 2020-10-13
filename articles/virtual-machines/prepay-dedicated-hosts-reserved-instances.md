---
title: Platba za vyhrazené hostitele Azure za účelem úspory peněz
description: Naučte se koupit rezervované instance vyhrazené hostitele Azure a ušetřit tak náklady na výpočetní výkon.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 919ff7474dd64cfe3005975beb4cab6f77c177ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87825255"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>Úspora nákladů pomocí rezervací vyhrazených hostitelů Azure

Po potvrzení do rezervované instance vyhrazených hostitelů Azure můžete ušetřit peníze. Sleva rezervace se automaticky použije na počet spuštěných vyhrazených hostitelů, které odpovídají oboru a atributům rezervace. K získání slev nemusíte přiřazovat rezervaci k vyhrazenému hostiteli. Zakoupení rezervované instance pokrývá jenom výpočetní část vašeho využití a zahrnuje náklady na licencování softwaru. Podívejte se na [Přehled vyhrazených hostitelů Azure pro virtuální počítače](./windows/dedicated-hosts.md).

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Určení správné vyhrazené SKU hostitele před nákupem


Před nákupem rezervace byste měli určit, který vyhrazený hostitel potřebujete. SKU je definováno pro vyhrazeného hostitele reprezentujícího řadu a typ virtuálního počítače. 

Začněte tím, že překročíte podporované velikosti pro [virtuální počítač s Windows](./sizes.md) nebo [Linux](./sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a Identifikujte řadu virtuálních počítačů.

Pak ověřte, jestli je ve vyhrazených hostitelích Azure podporovaná. Stránka s [cenami vyhrazené hostitele Azure](https://aka.ms/ADHPricing) obsahuje úplný seznam vyhrazených SKU pro vyhrazené hostitele, informace o procesoru a různé cenové možnosti (včetně rezervovaných instancí).

Může se stát, že několik SKU podporuje řadu virtuálních počítačů (s různými typy). Identifikujte nejlepší SKU tím, že porovnáte kapacitu hostitele (počet vCPU). Všimněte si, že budete moci použít rezervaci na několik vyhrazených hostitelů, které podporují stejné řady virtuálních počítačů (například DSv3_Type1 a DSv3_Type2), ale ne napříč různými řadami virtuálních počítačů (jako je DSv3 a ESv3).



## <a name="purchase-restriction-considerations"></a>Požadavky na omezení nákupu

Rezervované instance jsou k dispozici pro většinu vyhrazených velikostí hostitelů s některými výjimkami.

Slevy za rezervace se nevztahují na následující:

- **Cloudy**   – Rezervace nejsou k dispozici pro nákup v oblastech Německo a Čína.

- **Nedostatečná kvóta**   – Rezervace, která je vymezená pro jedno předplatné, musí mít v předplatném k dispozici vCPUou kvótu pro novou rezervovanou instanci. Pokud má například cílové předplatné maximální kvótu 10 vCPU pro DSv3-Series, nemůžete koupit vyhrazené hostitele rezervace, které tuto řadu podporují. Kontroly kvóty pro rezervace zahrnují virtuální počítače a vyhrazené hostitele, kteří jsou již nasazeni v rámci předplatného. K vyřešení tohoto problému můžete [vytvořit žádost o zvýšení kvóty](../azure-portal/supportability/resource-manager-core-quotas-request.md)   .

- **Omezení kapacity**   – Ve výjimečných případech Azure omezuje nákup nových rezervací pro podmnožinu vyhrazených SKU hostitelů z důvodu nízké kapacity v oblasti.

## <a name="buy-a-reservation"></a>Nákup rezervace

Rezervovanou instanci instance vyhrazeného hostitele Azure si můžete koupit v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Platíte za rezervaci [předem nebo s měsíčními platbami](../cost-management-billing/reservations/prepare-buy-reservation.md). Tyto požadavky se vztahují k zakoupení rezervované instance vyhrazeného hostitele:

- Musíte být v roli vlastníka alespoň pro jedno předplatné EA nebo předplatné s tarifem průběžných plateb.

- Pro předplatná EA **Add Reserved Instances**   musí být na [portálu EA](https://ea.azure.com/)povolená možnost Přidat rezervované instance. Pokud je toto nastavení zakázané, musíte být správcem smlouvy Enterprise pro příslušné předplatné.

- Pro program poskytovatele Cloud Solution Provider (CSP) mohou koupit rezervace pouze agenti správce nebo prodejní agenti.

Postup zakoupení instance:

1. Přihlaste se na  [Azure Portal](https://portal.azure.com/).

2. Vyberte **všechny**   \>  **rezervace**služeb.

3. Vyberte **Přidat**a   Zakupte novou rezervaci a pak klikněte na **vyhrazené hostitele**.

4. Vyplňte požadovaná pole. Spouštění vyhrazených hostitelů instance, které odpovídají vybraným atributům, opravňují k získání slevy rezervace. Skutečný počet vyhrazených instancí hostitele, které získají slevu, závisí na zvoleném rozsahu a množství.

Pokud máte smlouvu EA, můžete k rychlému přidání dalších instancí použít **možnost přidat další**   . Možnost není k dispozici pro jiné typy předplatného.

| **Pole**           | **Popis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Předplatné        | Předplatné použité pro platbu za rezervaci. Náklady na kapacitu se účtují pomocí způsobu platby zvoleného pro toto předplatné. Typ předplatného musí být smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P), zákaznická smlouva Microsoftu nebo jednotlivá předplatná s tarify průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). Poplatky se strhávají z peněžního zůstatku, pokud je k dispozici, nebo se účtují jako nadlimitní využití. U předplatného s tarify průběžných plateb se poplatky účtují na základě platební karty nebo platby na faktuře v rámci předplatného. |
| Rozsah               | Rozsah rezervace může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Oblast              | Oblast Azure, která je pokrytá rezervací.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Vyhrazená velikost hostitele | Velikost vyhrazených instancí hostitele.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Označení                | Jeden rok nebo tři roky.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Množství            | Počet instancí zakoupených v rámci rezervace. Množství je počet spuštěných vyhrazených instancí hostitele, které mohou získat fakturační slevu.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- Obor skupiny s **jedním prostředkem**   – Aplikuje slevu rezervace na vyhovující prostředky ve vybrané skupině prostředků.

- Rozsah jednoho předplatného **Single subscription scope**   – Použije slevu rezervace na vyhovující prostředky ve vybraném předplatném.

- **Sdílený rozsah**   – Použije slevu rezervace na odpovídající prostředky v oprávněných předplatných, která jsou v účetním kontextu. Pro zákazníky se smlouvou EA je účetním kontextem registrace. U individuálních předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.

## <a name="usage-data-and-reservation-utilization"></a>Využití a data využití rezervací

V datech o využití se pro využití, které získá slevu za rezervaci, zobrazuje platná nulová cena. Můžete zjistit, která instance virtuálního počítače obdržela slevu za rezervaci pro každou rezervaci.

Další informace o tom, jak se v datech využití zobrazují slevy, najdete v tématu [vysvětlení využívání rezervací Azure pro vaši podnikovou registraci](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md),   Pokud jste zákazníkem EA. Pokud máte individuální předplatné, přečtěte si téma [vysvětlení využití rezervace Azure pro předplatné](../cost-management-billing/reservations/understand-reserved-instance-usage.md)s průběžnými platbami.

## <a name="change-a-reservation-after-purchase"></a>Změna rezervace po nákupu

V zakoupené rezervaci můžete provádět následující typy změn:

- Aktualizace rozsahu rezervace

- Flexibilita velikosti instance (Pokud je k dispozici)

- Vlastnictví

Rezervaci můžete také rozdělit do menších bloků dat a sloučení už rozdělené rezervace. Žádná z těchto změn nezpůsobí novou komerční transakci ani nemění koncové datum rezervace.

Po nákupu nemůžete provést následující typy změn přímo:

- Existující oblast rezervace

- SKU

- Množství

- Doba trvání

Můžete ale *vyměnit*   rezervaci, pokud chcete provést změny.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu věnovaném [výměnám samoobslužných služeb a refundacím pro Azure reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc,  [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak spravovat rezervaci, najdete v tématu [správa Azure reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [Používání služby Azure Dedicated Host](./windows/dedicated-hosts.md)

- [Ceny služby Dedicated Host](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Správa rezervací v Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [Vysvětlení způsobu uplatnění slevy za rezervaci](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [Vysvětlení využití rezervací u smlouvy Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [Náklady na software pro Windows nezahrnuté v rezervacích](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](/partner-center/azure-reservations)