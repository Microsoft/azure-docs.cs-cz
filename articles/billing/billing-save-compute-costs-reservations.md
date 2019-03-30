---
title: Co jsou rezervace Azure? | Dokumenty Microsoft
description: Přečtěte si o Azure rezervace a ceny a Ušetřete na virtuálních počítačů, databází SQL, Azure Cosmos DB a další náklady na prostředky.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: banders
ms.openlocfilehash: 1349a05e1dd235c7b375335ae2c9fed16170a61f
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649386"
---
# <a name="what-are-azure-reservations"></a>Co jsou rezervace Azure?

Azure rezervací můžete ušetřit platíte předem za jeden rok nebo tři roky virtuálních počítačů, SQL Database výpočetní kapacitu, propustnost služby Azure Cosmos DB nebo další prostředky Azure. Platíte předem, můžete získat slevu na prostředky, které používáte. Rezervace může výrazně snížit virtuálního počítače SQL database výpočetní služby Azure Cosmos DB, nebo jiný prostředek stojí až 72 % oproti průběžným platbám. Rezervace poskytovat fakturační slevy a neovlivní jejich běhový stav vašich prostředků.

Můžete si koupit rezervaci [webu Azure portal](https://aka.ms/reservations). Další informace najdete v následujících článcích:

Plány služby:
- [Virtuální počítače se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Rezervované kapacity prostředků Azure Cosmos DB pomocí služby Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Rezervované kapacity výpočetních prostředků SQL Database s využitím Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)

Softwarové plány:
- [Plány softwaru Red Hat z Azure rezervací](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Plány softwaru SUSE z Azure rezervací](../virtual-machines/linux/prepay-suse-software-charges.md)

## <a name="why-buy-a-reservation"></a>Proč si koupit rezervaci?

Pokud máte virtuální počítače, služby Azure Cosmos DB nebo databází SQL, které běží dlouhou dobu, nákup rezervace nabízí cenově nejvýhodnější možnost. Například při spuštění průběžně čtyři instance služby bez rezervace, můžete se účtovat tarify průběžných plateb. Pokud zakoupíte rezervaci pro tyto prostředky, získáte okamžitě sleva za rezervaci. Prostředky jsou již účtovat podle tarifů průběžných plateb.

## <a name="charges-covered-by-reservation"></a>Poplatky za předmětem rezervace

Plány služby:

- Rezervovaná Instance virtuálního počítače: Rezervace pokrývá jenom náklady na výpočetní výkon virtuálního počítače. Nezahrnuje další poplatky za software, sítě nebo úložiště.
- Azure Cosmos DB rezervované kapacity: Rezervace se věnuje zajištěné propustnosti pro vaše prostředky. Nezahrnuje úložiště a sítě poplatky.
- Databáze SQL vyhrazený vCore: Výpočetní náklady jsou součástí rezervaci. Licence se fakturuje samostatně.
- Azure Cosmos DB rezervované kapacity: Rezervace pokrývá zajištěné propustnosti pro vaše prostředky, nezahrnuje poplatky za úložiště a sítě.

Pro virtuální počítače Windows a SQL Database, může zahrnovat náklady na licencování s [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Kdo má nárok na nákup rezervace?

Koupit plán, musíte mít roli vlastníka předplatného v podniku (MS-AZR - 0017P nebo MS-AZR - 0148P) nebo předplatné s průběžnými platbami (MS-AZR - 003P nebo MS-AZR - 0023P). Poskytovatele cloud solution Provider, můžete pomocí webu Azure portal nebo [partnerského centra](/partner-center/azure-reservations) nákupu rezervace Azure.

Zákazníkům se smlouvou EA může omezit nákupy správcům EA zakázáním **přidat Reserved Instances** možnost přes portál EA. Správce EA musí být vlastníkem předplatného pro alespoň jedno předplatné EA si koupit rezervaci. Možnost je užitečná pro podniky, které mají centralizovaný tým k nákupu rezervace pro jiné nákladových středisek. Po nákupu můžete přidat centralizované týmy cost center vlastníky rezervace. Vlastníci můžete pak určit obor rezervace svá předplatná. Centrální tým nemusí mít přístup vlastníka předplatného se nákup rezervace.

Sleva za rezervaci se vztahuje pouze na prostředky spojené s typy předplatného Enterprise, s průběžnými platbami nebo poskytovatel CSP.

## <a name="how-is-a-reservation-billed"></a>Jak se účtuje rezervace?

Rezervace jsou zpoplatněné nad rámec platby vázané na předplatné. Pokud máte předplatné Enterprise, náklady na rezervaci se odečte od váš zůstatek peněžního závazku. Pokud váš zůstatek peněžního závazku využívání služeb nezahrnuje náklady na rezervaci, bude se vám účtovat Nadlimitní využití. Pokud máte předplatné s průběžnými platbami, platební karty, kterou máte v účtu je účtována okamžitě. Bude se vám účtovat na faktuře uvidíte poplatky na další faktuře.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva ve výši rezervace

Sleva za rezervaci se vztahuje na využití prostředků, která odpovídá atributy, které jste vybrali při nákupu rezervace. Atributy zahrnout rozsahu, kde spustit odpovídající virtuální počítače, databáze SQL, Azure Cosmos DB nebo jiné prostředky. Například pokud chcete sleva za rezervaci pro čtyři virtuální počítače Standard D2 v oblasti západní USA, vyberte předplatné, ve kterém běží virtuální počítače. Pokud jsou virtuální počítače spuštěné v různých předplatných v rámci vaší registrace účtu, vyberte obor jako sdílené. Sdílený obor umožňuje sleva za rezervaci použít napříč předplatnými. Rozsah můžete změnit po nákupu rezervace. Další informace najdete v tématu [spravovat Azure rezervace](billing-manage-reserved-vm-instance.md).

Sleva za rezervaci se vztahuje pouze na prostředky spojené s typy předplatného Enterprise, s průběžnými platbami nebo poskytovatel CSP. Prostředky, které běží v rámci předplatného s jinými typy nabídky nepřijímají sleva za rezervaci.

Abyste lépe pochopili, jak ovlivňuje rezervace fakturace, najdete v následujících článcích:

Plány služby:

- [Vysvětlení, že slevách na využití platformy Azure Reserved VM Instances](billing-understand-vm-reservation-charges.md)
- [Vysvětlení sleva za rezervaci Azure](billing-understand-vm-reservation-charges.md)
- [Vysvětlení sleva za rezervaci služby Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md)

Softwarové plány:

- [Vysvětlení sleva za rezervaci Azure a použití pro Red Hat](billing-understand-rhel-reservation-charges.md)
- [Vysvětlení sleva za rezervaci Azure a použití pro SUSE](billing-understand-suse-reservation-charges.md)

## <a name="when-the-reservation-term-expires"></a>Vypršení platnosti období rezervace

Na konci období rezervace fakturace slevy vyprší a virtuální počítač, SQL database, Azure Cosmos DB nebo jiný prostředek se účtuje ve výši platby jako můžete přejít cena. Azure rezervace není automatického obnovení. Pokračovat v uplatňování slevy fakturace, musíte koupit novou rezervaci pro opravňující služby a softwaru.

## <a name="discount-applies-to-different-sizes"></a>Sleva za vztahuje na různé velikosti

Při nákupu rezervace sleva provést u ostatních instancí s atributy, které jsou ve stejné skupině velikostí. Tato funkce se označuje jako velikost flexibilita instancí. Flexibilita pokrytí slevy závisí na typu rezervace a atributy, které můžete vybrat při nákupu rezervace.

Plány služby:

- Rezervované instance virtuálních počítačů: Když koupit rezervaci a vyberete **optimalizovaná pro**: **instance velikost flexibilitu**, pokrytí slevy závisí na velikosti virtuálního počítače, vyberete. Rezervaci můžete použít velikosti virtuálních počítačů (VM) ve stejné skupině velikost series. Další informace najdete v tématu [flexibilitu velikost virtuálního počítače s rezervovanými instancemi virtuálních počítačů](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- SQL Database rezervované kapacity: Pokrytí slevy závisí na úrovni výkonu, kterou vyberete. Další informace najdete v tématu [pochopit, jak se použije sleva za rezervaci Azure](billing-understand-reservation-charges.md).
- Azure Cosmos DB rezervované kapacity: Pokrytí slevy závisí na zřízenou propustnost. Další informace najdete v tématu [vysvětlení, používání Azure Cosmos DB sleva za rezervaci](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- Zahájení ukládání na virtuálních počítačích po zakoupení [rezervované Instance virtuálního počítače](../virtual-machines/windows/prepay-reserved-vm-instances.md), [SQL Database rezervované kapacity](../sql-database/sql-database-reserved-capacity.md), nebo [služby Azure Cosmos DB rezervované kapacity](../cosmos-db/cosmos-db-reserved-capacity.md).
- Další informace o rezervacích Azure najdete v následujících článcích:
    - [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
    - [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
    - [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)
    - [Rezervace Azure v programu Partnerské centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
