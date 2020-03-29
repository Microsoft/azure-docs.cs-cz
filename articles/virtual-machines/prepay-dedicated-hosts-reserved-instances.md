---
title: Předplatit pro Azure Dedicated Hosts ušetřit peníze
description: Zjistěte, jak koupit vyhrazené instance vyhrazených hostitelů Azure, abyste ušetřili na výpočetních nákladech.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 57123abfe7f343a75d264d43afb88f9de1409e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207742"
---
# <a name="save-costs-with-a-reserved-instance-of-azure-dedicated-hosts"></a>Ušetřete náklady díky rezervované instanci vyhrazených hostitelů Azure

Když se zavážete k rezervované instanci vyhrazených hostitelů Azure, můžete ušetřit peníze. Sleva rezervace se automaticky použije na počet spuštěných vyhrazených hostitelů, které odpovídají rozsahu rezervace a atributům. Nemusíte přiřazovat rezervaci vyhrazenému hostiteli, abyste získali slevy. Nákup rezervované instance pokrývá pouze výpočetní část vašeho využití a zahrnuje náklady na licencování softwaru. Podívejte se na [přehled vyhrazených hostitelů Azure pro virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts).

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Určit správnou vyhrazenou hostitelskou skladovou položku před zakoupením


Než si koupíte rezervaci, měli byste určit, který vyhrazený hostitel potřebujete. Skladová položka je definována pro vyhrazeného hostitele představujícího řadu a typ virtuálního uživatele. 

Začněte tím, že půjdete přes podporované velikosti pro [virtuální počítač s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) k identifikaci řady virtuálních počítače.

Dále zkontrolujte, jestli je podporována na vyhrazených hostitelích Azure. [Stránka o cenách vyhrazených hostitelů Azure](https://aka.ms/ADHPricing) má úplný seznam vyhrazených hostitelů slok, informace o procesoru a různé možnosti ocenění (včetně rezervovaných instancí).

Můžete najít několik skum, které podporují řadu virtuálních počítačů (s různými typy). Identifikujte nejlepší skladovou položku porovnáním kapacity hostitele (počet virtuálních procesorů). Všimněte si, že budete moci použít rezervace na více vyhrazených hostitelů skum podporující stejné řady virtuálních her (například DSv3_Type1 a DSv3_Type2), ale ne napříč různými řadami virtuálních her (jako je DSv3 a ESv3).



## <a name="purchase-restriction-considerations"></a>Aspekty omezení nákupu

Rezervované instance jsou k dispozici pro většinu vyhrazených velikostí hostitelů, s některými výjimkami.

Slevy na rezervaci se nevztahují na následující:

- **Cloudy** – Rezervace nelze zakoupit v oblastech Německa nebo Číny.

- **Nedostatečná kvóta** – rezervace, která je vymezena na jedno předplatné, musí mít kvótu virtuálního procesoru dostupnou v předplatném pro novou rezervovanou instanci. Pokud má například cílové předplatné limit kvóty 10 virtuálních procesorů pro DSv3-Series, nelze si koupit rezervace vyhrazená hostitelé podporující tuto řadu. Kontrola kvót pro rezervace zahrnuje virtuální chody a vyhrazená hostitelé, která jsou už nasazená v předplatném. K vyřešení tohoto problému můžete [vytvořit požadavek na](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) zvýšení kvóty.

- **Omezení kapacity** – ve výjimečných případech Azure omezuje nákup nových rezervací pro podmnožinu vyhrazených hostitelských skum, z důvodu nízké kapacity v oblasti.

## <a name="buy-a-reservation"></a>Nákup rezervace

Rezervovanou instanci instance Azure Dedicated Host si můžete zakoupit na [webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Zaplaťte rezervaci [předem nebo měsíčně](https://docs.microsoft.com/azure/billing/billing-monthly-payments-reservations). Tyto požadavky platí pro nákup vyhrazené instance vyhrazeného hostitele:

- Musíte být v roli vlastníka alespoň pro jedno předplatné EA nebo předplatné s průběžnou sazbou.

- U předplatných EA musí být na [portálu EA](https://ea.azure.com/)povolena možnost **Přidat rezervované instance**. Pokud je toto nastavení zakázané, musíte být správcem smlouvy Enterprise pro příslušné předplatné.

- V programu Zprostředkovatel cloudových řešení (CSP) mohou rezervace zakoupit pouze správci nebo prodejci.

Zakoupení instance:

1. Přihlaste se na  [Azure Portal](https://portal.azure.com/).

2. Vyberte **všechny služby** \> **Rezervace**.

3. Vyberte **Přidat,** chcete-li zakoupit novou rezervaci, a klepněte na **položku Vyhrazené hostitele**.

4. Vyplňte požadovaná pole. Spuštění instance vyhrazené hostitelé, které odpovídají atributy, které vyberete nárok získat slevu rezervace. Skutečný počet instancí vyhrazeného hostitele, které získají slevu, závisí na vybraném oboru a množství.

Pokud máte smlouvu eA, můžete pomocí **možnosti** Přidat další rychle přidat další instance. Tato možnost není k dispozici pro jiné typy předplatného.

| **Pole**           | **Popis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Předplatné        | Předplatné slouží k zaplacení rezervace. Za způsob platby v rámci předplatného se účtují náklady na rezervaci. Typ předplatného musí být smlouva enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P) nebo Smlouva se zákazníkem společnosti Microsoft nebo individuální předplatné s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). Poplatky se odečtou ze zůstatku peněžního závazku, pokud jsou k dispozici, nebo jsou účtovány jako nadbytečné. U předplatného s průběžnými platbami se poplatky účtují na základě platební karty nebo způsobu platby faktury v rámci předplatného. |
| Rozsah               | Obor rezervace může zahrnovat jedno předplatné nebo více předplatných (sdílený obor). Pokud vyberete:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Region (Oblast)              | Oblast Azure, která je pokryta rezervace.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Vyhrazená velikost hostitele | Velikost instance vyhrazenéhostitele.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Označení                | Jeden rok nebo tři roky.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Množství            | Počet instancí zakoupených v rámci rezervace. Množství je počet spuštěných instancí vyhrazeného hostitele, které mohou získat fakturační slevu.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Obor skupiny jednoho prostředku** – Použije slevu rezervace pouze na odpovídající prostředky ve vybrané skupině prostředků.

- **Obor jednoho předplatného** – použije slevu rezervace na odpovídající prostředky ve vybraném předplatném.

- **Sdílený obor** – Použije slevu na rezervaci na odpovídající prostředky ve způsobilých předplatných, které jsou v kontextu fakturace. Pro zákazníky EA je fakturační kontext registrace. U individuálních předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.

## <a name="usage-data-and-reservation-utilization"></a>Údaje o využití a využití rezervací

V datech o využití se pro využití, které získá slevu za rezervaci, zobrazuje platná nulová cena. Můžete zjistit, která instance virtuálního virtuálního soudu obdržela slevu na rezervaci pro každou rezervaci.

Další informace o tom, jak se slevy na rezervace zobrazují v datech o využití, najdete v [tématu Principy využití rezervací Azure pro registraci](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) enterprise, pokud jste zákazníkem EA. Pokud máte individuální předplatné, [přečtěte si informace o využití rezervací Azure pro vaše předplatné s průběžným platbou](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).

## <a name="change-a-reservation-after-purchase"></a>Změna rezervace po zakoupení

V zakoupené rezervaci můžete provádět následující typy změn:

- Aktualizace rozsahu rezervace

- Flexibilita velikosti instance (pokud je k dispozici)

- Vlastnictví

Můžete také rozdělit rezervaci na menší bloky a sloučit již rozdělené rezervace. Žádná ze změn nezpůsobí novou obchodní transakci ani nezmění koncové datum rezervace.

Po zakoupení nelze provést následující typy změn:

- Oblast existující rezervace

- Skladová jednotka (SKU)

- Množství

- Doba trvání

Pokud však chcete provést změny, můžete si rezervaci *vyměnit.* 

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete [v tématu Samoobslužné výměny a refundace pro rezervace Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak spravovat rezervaci, najdete v tématu [Správa rezervací Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance).

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Používání vyhrazených hostitelů Azure](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Vyhrazené ceny hostitelů](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Správa rezervací v Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [Vysvětlení způsobu uplatnění slevy za rezervaci](https://docs.microsoft.com/azure/billing/billing-understand-vm-reservation-charges)

- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [Vysvětlení využití rezervací u smlouvy Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Náklady na software systému Windows nejsou zahrnuty v rezervacích](https://docs.microsoft.com/azure/billing/billing-reserved-instance-windows-software-costs)

- [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)


