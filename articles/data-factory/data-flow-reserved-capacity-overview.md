---
title: Úspora výpočetních nákladů s využitím rezervované kapacity
description: Naučte se, jak koupit Azure Data Factory rezervované kapacity toku dat, abyste ušetřili náklady na výpočetní výkon.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 01/25/2021
ms.openlocfilehash: 8bea1ff0dcb945e969553a4f643e289a5157f966
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811659"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>Úspora nákladů na prostředky s využitím rezervovaných Azure Data Factorych toků dat

V porovnání s cenami za průběžné platby můžete ušetřit peníze pomocí Azure Data Factory nákladů toku dat. Díky rezervované kapacitě provedete závazek za využití toku dat ADF po dobu jednoho nebo tří let, abyste získali značnou slevu za výpočetní náklady. Pokud si chcete koupit rezervovanou kapacitu, musíte zadat oblast Azure, výpočetní typ, počet jader a termín.

Nemusíte přiřadit rezervaci ke konkrétní továrně nebo prostředí Integration runtime. Stávající továrny nebo nově nasazené továrny automaticky získají výhodu. Když zakoupíte rezervaci, zavedete se k využití výpočetních nákladů toku dat za období jednoho nebo tří let. Jakmile si koupíte rezervaci, poplatky za výpočetní prostředky, které odpovídají atributům rezervace, se už nebudou účtovat podle tarifů průběžných plateb. 

Rezervovanou kapacitu můžete koupit v [Azure Portal](https://portal.azure.com). Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](https://docs.microsoft.com/azure/cost-management-billing/reservations/prepare-buy-reservation.md). Pro nákup rezervované kapacity platí:

- Musíte být v roli vlastníka alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.
- U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com) povolená možnost **Přidat rezervované instance**. Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném. Rezervovaná kapacita.

Další informace o tom, jak se zákazníkům z podnikových zákazníků a průběžné platby účtují poplatky za nákupy rezervací, najdete v tématu [vysvětlení využití rezervace Azure pro vaši registraci v podniku](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) a [pochopení využití rezervací Azure pro vaše předplatné](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage.md)s průběžnými platbami.

> [!NOTE]
> Zakoupená Rezervovaná kapacita neumožňuje předem přidělit ani rezervovat prostředky infrastruktury (virtuální počítače nebo clustery) pro vaše použití.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>Určení správných velikostí Azure IR potřebných před nákupem

Velikost rezervace by měla být založená na celkovém množství výpočetních toků používaných existujícími a již nasazenými toky dat pomocí stejné výpočetní vrstvy.

Řekněme například, že spouštíte kanál po hodinách s využitím paměti optimalizované s 32 jádry. Nyní byste měli mít v příští měsíci v úmyslu nasadit další kanál, který využívá obecné účely 64 jader. Řekněme taky, že víte, že tyto prostředky budete potřebovat aspoň 1 rok. V takovém případě byste si měli koupit rezervaci 32 jader na 1 rok pro paměťově optimalizované toky dat a pro obecné účely 64 jader na 1 rok.

## <a name="buy-reserved-capacity"></a>Nákup rezervované kapacity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Všechny služby** > **Rezervace**.
3. Vyberte **Přidat** a pak v podokně **rezervace nákupu** vyberte **toky dat ADF** , abyste si mohli koupit novou rezervaci pro toky dat ADF.
4. Vyplňte požadovaná pole a atributy, které jste vybrali, abyste získali rezervovanou slevu na kapacitu. Skutečný počet toků dat, které obdrží slevu, závisí na zvoleném rozsahu a množství.
5. Projděte si náklady na rezervaci kapacity v části **náklady** .
6. Vyberte **Koupit**.
7. Pokud chcete zobrazit stav nákupu, vyberte **Zobrazit tuto rezervaci** .

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

- [Vysvětlení slev za rezervace Azure](data-flow-understand-reservation-charges.md)
