---
title: Co jsou Azure rezervace? | Dokumenty Microsoft
description: Přečtěte si o Azure rezervace a ceny a Ušetřete na virtuálních počítačů, databází SQL a další náklady na prostředky.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 93c11852a11e0bb57a0b92090368298fc14b8c2a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626300"
---
# <a name="what-are-azure-reservations"></a>Co jsou Azure rezervace?

Šetřete peníze optimalizací platíte předem za jeden rok nebo tři roky virtuální počítač nebo databázi SQL Azure rezervace pomáhá výpočetní kapacitu. Platíte předem, můžete získat slevu na prostředky, které používáte. Azure rezervace může výrazně snížit náklady na výpočetní výkon databáze SQL vaší virtuálního počítače – až 72 procent u průběžných plateb – s jeden rok nebo tři roky předem k ničemu zavázat. rezervace poskytovat fakturační slevy a neovlivní jejich běhový stav virtuálního počítače nebo databáze SQL.

Můžete si koupit rezervaci [webu Azure portal](https://aka.ms/reservations). Další informace najdete v následujících tématech:

- [Předplatit si virtuální počítače se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předem za výpočetní prostředky, SQL Database s Azure SQL Database vyhrazené kapacity](../sql-database/sql-database-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Proč si mám koupit rezervaci?

Pokud máte virtuální počítače nebo databáze SQL, které běží dlouhou dobu, nákup rezervace nabízí cenově nejvýhodnější možnost. Například pokud spustíte průběžně čtyři instance služby bez rezervace, bude vám účtována za průběžné platby. Pokud si koupíte rezervace pro tyto prostředky, získáte okamžitě sleva za rezervaci. Prostředky jsou již účtovat podle tarifů průběžných plateb.

## <a name="what-charges-does-a-reservation-cover"></a>Co poplatky se titulní rezervace?

- Rezervovaná Instance virtuálního počítače: Rezervaci pokrývá jenom náklady na výpočetní výkon virtuálního počítače. Nezahrnuje další poplatky za software, sítě nebo úložiště.
- Databáze SQL vyhrazený vCore: pouze náklady na výpočetní výkon jsou součástí rezervaci. Licence se fakturuje samostatně.

Pro virtuální počítače Windows a SQL Database, může zahrnovat náklady na licencování s [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Kdo má nárok na nákup rezervace?

Zákazníci s těmito typy předplatných Azure, můžete si koupit rezervaci:

- Typ nabídky předplatného se smlouvou Enterprise (MS-AZR-0017P).
- Typ nabídky předplatného [s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Na předplatné, které chcete koupit rezervaci, musíte mít roli "Vlastník". K nákupu rezervace v podnikového zápisu, musíte povolit správce rozlehlé sítě nákup rezervace na portálu EA. Standardně je toto nastavení povolené.
- Partnery cloud Solution Provider (CSP) můžete pomocí webu Azure portal nebo [partnerského centra](https://docs.microsoft.com/partner-center/azure-reservations) k nákupu Azure rezervace. 

Sleva za rezervaci Azure platí jenom pro virtuální počítače nebo databáze SQL přidružené typy předplatného Enterprise, s průběžnými platbami nebo poskytovatel CSP.

## <a name="how-is-a-reservation-billed"></a>Jak se účtuje rezervace?

Rezervace jsou zpoplatněné nad rámec platby vázané na předplatné. Pokud máte předplatné Enterprise, náklady na rezervaci se odečte od váš zůstatek peněžního závazku. Pokud váš zůstatek peněžního závazku využívání služeb nezahrnuje náklady na rezervaci, bude se vám účtovat Nadlimitní využití. Pokud máte předplatné s průběžnými platbami, platební karty, kterou máte v účtu je účtována okamžitě. Bude se vám účtovat na faktuře uvidíte poplatky na další faktuře.

## <a name="how-is-the-reservation-discount-applied"></a>Jak se použije slevu na rezervaci?

Sleva za rezervaci se vztahuje na využití prostředků, která odpovídá atributy, které jste vybrali při nákupu rezervace. Atributy zahrnout oboru kde odpovídající virtuální počítače, databáze SQL nebo spuštění dalších prostředků. Například pokud chcete sleva za rezervaci pro čtyři virtuální počítače Standard D2 v oblasti západní USA, vyberte předplatné, ve kterém běží virtuální počítače. Pokud jsou virtuální počítače spuštěné v různých předplatných v rámci vaší registrace účtu, vyberte obor jako sdílené. Sdílený obor umožňuje sleva za rezervaci použít napříč předplatnými. Rozsah můžete změnit po nákupu rezervace. Další informace najdete v tématu [rezervace v Azure spravovat](billing-manage-reserved-vm-instance.md).

Sleva za rezervaci se vztahuje pouze na virtuální počítače nebo databáze SQL přidružené enterprise nebo předplatné s průběžnými platbami typy. Virtuální počítače nebo databáze SQL, na kterých běží v rámci předplatného s jinými typy nabídky nepřijímají sleva za rezervaci. Pro podnikové registrace přihlášení k odběru enterprise pro vývoj/testování nejsou nárok na výhody rezervace.

Abyste lépe pochopili, jak ovlivňuje rezervace virtuálních počítačů nebo fakturace databáze SQL, najdete v článku [pochopit, jak se sleva za rezervaci použije](billing-understand-vm-reservation-charges.md).

## <a name="what-happens-when-the-reservation-term-expires"></a>Co se stane, když vyprší platnost období rezervace?

Na konci období rezervace fakturace slevy vyprší a virtuální počítač, databáze SQL nebo další prostředky se účtují cenu platit jako můžete přejít. Azure rezervace není automatického obnovení. Pokračovat v uplatňování slevy fakturace, musíte koupit novou rezervaci pro rezervaci oprávněných služeb.

## <a name="next-steps"></a>Další postup

Zahájení ukládání na virtuálních počítačích po zakoupení [rezervované Instance virtuálního počítače](../virtual-machines/windows/prepay-reserved-vm-instances.md) nebo [SQL Database rezervované kapacity](../sql-database/sql-database-reserved-capacity.md).

Další informace o rezervacích, naleznete v následujících článcích:

- [Správa Azure rezervace](billing-manage-reserved-vm-instance.md)
- [Vysvětlení, jak se sleva za rezervaci použije](billing-understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)
- [Rezervace Azure v programu Partnerské centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
