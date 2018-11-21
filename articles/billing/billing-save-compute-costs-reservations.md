---
title: Co jsou rezervace Azure? | Dokumenty Microsoft
description: Přečtěte si o Azure rezervace a ceny a Ušetřete na virtuálních počítačů, databází SQL, Azure Cosmos DB a další náklady na prostředky.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: cwatson
ms.openlocfilehash: 1d79ca20c73d5dc49781be317112cfb59d349fa3
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276223"
---
# <a name="what-are-azure-reservations"></a>Co jsou rezervace Azure?

Azure rezervace umožňuje ušetřit platíte předem za jeden rok nebo tři roky z virtuálního počítače, SQL Database výpočetní kapacitu, propustnost služby Azure Cosmos DB nebo další prostředky Azure. Platíte předem, můžete získat slevu na prostředky, které používáte. Rezervace může výrazně snížit virtuálního počítače SQL database výpočetní služby Azure Cosmos DB, nebo jiný prostředek stojí až 72 % oproti průběžným platbám. Rezervace poskytovat fakturační slevy a neovlivní jejich běhový stav vašich prostředků.

Můžete si koupit rezervaci [webu Azure portal](https://aka.ms/reservations). Další informace najdete v následujících tématech:

- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Předplatíte prostředky Azure Cosmos DB pomocí služby Azure Cosmos DB vyhrazené kapacity](../cosmos-db/cosmos-db-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Proč si mám koupit rezervaci?

Pokud máte virtuální počítače, služby Azure Cosmos DB nebo databází SQL, které běží dlouhou dobu, nákup rezervace nabízí cenově nejvýhodnější možnost. Například pokud spustíte průběžně čtyři instance služby bez rezervace, bude vám účtována za průběžné platby. Pokud si koupíte rezervace pro tyto prostředky, získáte okamžitě sleva za rezervaci. Prostředky jsou již účtovat podle tarifů průběžných plateb.

## <a name="what-charges-does-a-reservation-cover"></a>Co poplatky se titulní rezervace?

- Rezervovaná Instance virtuálního počítače: Rezervaci pokrývá jenom náklady na výpočetní výkon virtuálního počítače. Nezahrnuje další poplatky za software, sítě nebo úložiště.
- Databáze SQL vyhrazený vCore: pouze náklady na výpočetní výkon jsou součástí rezervaci. Licence se fakturuje samostatně.
- Azure Cosmos DB rezervované kapacity: rezervaci pokrývá zajištěné propustnosti pro vaše prostředky, nezahrnuje poplatky za úložiště a sítě. 

Pro virtuální počítače Windows a SQL Database, může zahrnovat náklady na licencování s [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Kdo má nárok na nákup rezervace?

Zákazníci s těmito typy předplatných Azure, můžete si koupit rezervaci:

- Typ nabídky předplatného se smlouvou Enterprise (MS-AZR-0017P).
- Typ nabídky předplatného [s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Na předplatné, které chcete koupit rezervaci, musíte mít roli "Vlastník".
- Partnery cloud Solution Provider (CSP) můžete pomocí webu Azure portal nebo [partnerského centra](https://docs.microsoft.com/partner-center/azure-reservations) k nákupu rezervace Azure.

Sleva za rezervaci se vztahuje pouze na prostředky spojené s typy předplatného Enterprise, s průběžnými platbami nebo poskytovatel CSP.

## <a name="how-is-a-reservation-billed"></a>Jak se účtuje rezervace?

Rezervace jsou zpoplatněné nad rámec platby vázané na předplatné. Pokud máte předplatné Enterprise, náklady na rezervaci se odečte od váš zůstatek peněžního závazku. Pokud váš zůstatek peněžního závazku využívání služeb nezahrnuje náklady na rezervaci, bude se vám účtovat Nadlimitní využití. Pokud máte předplatné s průběžnými platbami, platební karty, kterou máte v účtu je účtována okamžitě. Bude se vám účtovat na faktuře uvidíte poplatky na další faktuře.

## <a name="how-is-the-reservation-discount-applied"></a>Jak se použije slevu na rezervaci?

Sleva za rezervaci se vztahuje na využití prostředků, která odpovídá atributy, které jste vybrali při nákupu rezervace. Atributy zahrnout rozsahu, kde spustit odpovídající virtuální počítače, databáze SQL, Azure Cosmos DB nebo jiné prostředky. Například pokud chcete sleva za rezervaci pro čtyři virtuální počítače Standard D2 v oblasti západní USA, vyberte předplatné, ve kterém běží virtuální počítače. Pokud jsou virtuální počítače spuštěné v různých předplatných v rámci vaší registrace účtu, vyberte obor jako sdílené. Sdílený obor umožňuje sleva za rezervaci použít napříč předplatnými. Rozsah můžete změnit po nákupu rezervace. Další informace najdete v tématu [spravovat Azure rezervace](billing-manage-reserved-vm-instance.md).

Sleva za rezervaci se vztahuje pouze na prostředky spojené s typy předplatného Enterprise, s průběžnými platbami nebo poskytovatel CSP. Prostředky, které běží v rámci předplatného s jinými typy nabídky nepřijímají sleva za rezervaci. Pro podnikové registrace přihlášení k odběru enterprise pro vývoj/testování nejsou nárok na výhody rezervace.

Abyste lépe pochopili, jak ovlivňuje rezervace fakturace, najdete v následujících tématech:

-  [Vysvětlení, že slevách na využití platformy Azure Reserved VM Instances](billing-understand-vm-reservation-charges.md)
- [Vysvětlení sleva za rezervaci Azure](billing-understand-vm-reservation-charges.md)
- [Vysvětlení sleva za rezervaci služby Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md)
- [Vysvětlení sleva za rezervaci Azure a použití pro SUSE](billing-understand-suse-reservation-charges.md)

## <a name="what-happens-when-the-reservation-term-expires"></a>Co se stane, když vyprší platnost období rezervace?

Na konci období rezervace fakturace slevy vyprší a virtuální počítač, SQL database, Azure Cosmos DB nebo jiný prostředek se účtuje ve výši platby jako můžete přejít cena. Azure rezervace není automatického obnovení. Pokračovat v uplatňování slevy fakturace, musíte koupit novou rezervaci pro opravňující služby a softwaru.

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>Sleva za vztahuje na různých velikostí s flexibilitou velikost instance

Při nákupu rezervace sleva provést u ostatních instancí s atributy, které jsou ve stejné skupině velikostí. Flexibilita pokrytí slevy závisí na typu rezervace a atributy, které můžete vybrat při nákupu rezervace.

- Rezervované instance virtuálních počítačů: Při nákupu rezervace, pokud vyberete **optimalizovaná pro**: **instance velikost flexibilitu**, pokrytí slevy závisí na velikosti virtuálního počítače, vyberete. Rezervaci můžete použít velikosti virtuálních počítačů (VM) ve stejné skupině velikost series. Další informace najdete v tématu [flexibilitu velikost virtuálního počítače s rezervovanými instancemi virtuálních počítačů](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Plán softwaru SUSE Linux Enterprise: pokrytí slevy závisí na virtuální procesory virtuálních počítačů, ve kterém běží SUSE software. Další informace najdete v tématu [pochopit, jak se použije slevu plán softwaru SUSE Linux Enterprise](billing-understand-suse-reservation-charges.md).
- SQL Database rezervované kapacity: pokrytí slevy závisí na úroveň výkonu můžete vybrat. Další informace najdete v tématu [pochopit, jak se použije sleva za rezervaci Azure](billing-understand-reservation-charges.md).
- Azure Cosmos DB rezervované kapacity: pokrytí slevy závisí na zřízenou propustnost. Další informace najdete v tématu [vysvětlení, používání Azure Cosmos DB sleva za rezervaci](billing-understand-cosmosdb-reservation-charges.md).

## <a name="next-steps"></a>Další postup

Zahájení ukládání na virtuálních počítačích po zakoupení [rezervované Instance virtuálního počítače](../virtual-machines/windows/prepay-reserved-vm-instances.md), [SQL Database rezervované kapacity](../sql-database/sql-database-reserved-capacity.md), nebo [služby Azure Cosmos DB rezervované kapacity](../cosmos-db/cosmos-db-reserved-capacity.md).

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)
- [Rezervace Azure v programu Partnerské centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
