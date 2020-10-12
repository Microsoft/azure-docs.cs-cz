---
title: Rezervované ceny za výpočetní výkon – Azure Database for PostgreSQL – Citus (škálování)
description: Platba za Azure Database for PostgreSQL (Citus) výpočetních prostředků s rezervovanou kapacitou
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: a5ce99927ce4cd2b04b5dd5cb865299b4be84ecb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86519792"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Platba za Azure Database for PostgreSQL (Citus) výpočetních prostředků s rezervovanou kapacitou

Azure Database for PostgreSQL – Citus () nyní pomáhá ušetřit peníze pomocí předplatného pro výpočetní prostředky v porovnání s cenami průběžných plateb. Díky rezervované kapacitě Citus () můžete učinit předem svůj závazek na skupinu serverů Citus () po dobu jednoho nebo tří let, abyste získali významnou slevu na výpočetní náklady. Pokud si chcete koupit rezervovanou kapacitu (Citus), musíte zadat oblast Azure, podmínky rezervace a četnost fakturace.

> [!IMPORTANT]
> V tomto článku se dozvíte o rezervované kapacitě pro Azure Database for PostgreSQL – Citus (škálování). Informace o rezervované kapacitě pro Azure Database for PostgreSQL – jeden server najdete v tématu [platba za Azure Database for PostgreSQL – výpočetní prostředky na jednom serveru s rezervovanou kapacitou](/azure/postgresql/concept-reserved-pricing).

Nemusíte přiřazovat rezervaci konkrétním skupinám serverů Citus (). Už je spuštěná skupina serverů Citus () nebo ty, které se nově nasadily automaticky, získají výhody rezervovaných cen. Když si koupíte rezervaci, platíte za výpočetní náklady za jeden rok nebo tři roky. Jakmile si koupíte rezervaci, poplatky za výpočetní výkon (Citus), které odpovídají atributům rezervace, se už nebudou účtovat podle tarifů průběžných plateb. 

Rezervace nepokrývá software, sítě ani poplatky za úložiště spojené se skupinami serverů Citus (). Na konci podmínky rezervace vyprší platnost fakturačního přínosu a skupiny serverů ve velkém měřítku (Citus) se účtují podle ceny za průběžné platby. Rezervace se neobnovují. Informace o cenách najdete v části [Azure Database for PostgreSQL – Citus (nabídka vyhrazené kapacity)](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

Rezervovanou kapacitu Citus () můžete koupit v [Azure Portal](https://portal.azure.com/). Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](https://docs.microsoft.com/azure/cost-management-billing/reservations/monthly-payments-reservations). Zakoupení rezervované kapacity:

* Musíte být v roli vlastníka aspoň u jednoho smlouva Enterprise (EA) nebo v jednotlivých předplatných s tarify průběžných plateb.
* V případě předplatných smlouva Enterprise musí být na [portálu EA](https://ea.azure.com/)povolené **Přidat rezervované instance** . Nebo, pokud je toto nastavení zakázané, musíte být správcem smlouva Enterprise v předplatném.
* V případě programu Cloud Solution Provider (CSP) může koupit rezervovanou kapacitu (Citus).

Informace o tom, jak smlouva Enterprise zákazníkům a zákazníkům s průběžnými platbami se účtují za nákupy rezervací, najdete tady:
- [Vysvětlení využití rezervace Azure pro registraci smlouva Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
- [Vysvětlení využití rezervace Azure pro předplatné s průběžnými platbami](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

## <a name="determine-the-right-server-group-size-before-purchase"></a>Určení správné velikosti skupiny serverů před nákupem

Velikost rezervace vychází z celkového množství výpočtů využívaných existujícím nebo již nasazeným koordinátorem a pracovními uzly ve skupinách serverů Citus () v konkrétní oblasti.

Předpokládejme například, že máte spuštěnou jednu skupinu serverů Citus () s 16 vCore koordinátorem a 3 8 vCore pracovních uzlů. Dále budeme předpokládat, že plánujete nasadit do příštího měsíce skupinu serverů Citus (Next Scale) s vCore koordinátorem 32 a 2 4 vCore pracovních uzlů. Řekněme taky, že tyto prostředky potřebujete aspoň na jeden rok.

V takovém případě si můžete koupit jednoletou rezervaci pro:

* Celkový počet 16 virtuální jádra + 32 virtuální jádra = 48 virtuální jádra pro uzly koordinátora
* Celkový počet 3 uzlů × 8 virtuální jádra + 2 uzlů × 4 virtuální jádra = 24 + 8 = 32 virtuální jádra pro pracovní uzly

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Koupit Azure Database for PostgreSQL rezervovanou kapacitu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte **Všechny služby** > **Rezervace**.
1. Vyberte **Přidat**. V podokně **rezervace nákupu** vyberte **Azure Database for PostgreSQL** a Zakupte novou rezervaci pro databáze PostgreSQL.
1. Vyberte **výpočetní typ Citus ()** , který chcete koupit, a klikněte na **Vybrat**.
1. Zkontrolujte množství pro vybraný typ výpočtu na kartě **produkty** .
1. Pokračujte na kartu **koupit + revize** a dokončete nákup.

V následující tabulce jsou popsána povinná pole.

| Pole        | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Předplatné | Předplatné použité pro platbu Azure Database for PostgreSQL rezervované rezervace kapacity. Platební metodou předplatného se účtují náklady na front-end pro rezervaci rezervované kapacity Azure Database for PostgreSQL. Typ předplatného musí být smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P) nebo samostatná smlouva s cenami průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). U předplatného smlouva Enterprise se poplatky odečtou z bilance peněžního závazku registrace nebo se účtují jako nadlimitní využití. U jednotlivých předplatných s průběžnými platbami se poplatky účtují na základě platební karty nebo platby na faktuře v předplatném.                                                                                  |
| Rozsah        | Obor rezervace vCore může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete **Shared (sdílená**), použije se sleva na Vcore rezervací na skupiny serverů Citus (), které jsou spuštěné v rámci vašeho fakturačního kontextu. Pro smlouva Enterprise zákazníky je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami se sdíleným oborem jsou všechna předplatná s průběžnými platbami vytvořená správcem účtu. Pokud vyberete **jedno předplatné**, použije se pro skupiny serverů Citus (Vcore) v tomto předplatném sleva za rezervaci. Když vyberete **jednu skupinu prostředků**, použije se sleva rezervace na skupiny serverů Citus () ve vybraném předplatném a v rámci daného předplatného. |
| Oblast       | Oblast Azure, která je pokrytá Azure Database for PostgreSQL – Citus (rezervace kapacity rezervované na úrovni)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Označení         | Jeden rok nebo tři roky.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Množství     | Množství výpočetních prostředků, které se zakoupí v rámci rezervované rezervace kapacity (Citus). Konkrétně se virtuální jádra počet koordinátorů nebo pracovních uzlů ve vybrané oblasti Azure, které jsou rezervované a které obdrží fakturační slevu. Například pokud používáte (nebo naplánujete spuštění) skupiny serverů Citus (s celkovou kapacitou pro výpočetní výkon 64 uzel virtuální jádra a 32 pracovních uzlů virtuální jádra v oblasti Východní USA, určete množství jako 64 a 32 pro koordinátora a pracovní uzly, abyste mohli maximalizovat výhody pro všechny servery.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [samoobslužné výměny a refundace u rezervací Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>flexibilita velikosti vCore

flexibilita velikosti vCore pomáhá při horizontálním navýšení nebo snížení kapacity koordinátora a pracovních uzlů v rámci oblasti, aniž by došlo ke ztrátě výhod vyhrazené kapacity.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Sleva rezervovaného vCore se automaticky aplikuje na počet skupin serverů Citus (), které odpovídají Azure Database for PostgreSQL rozsahu rezervace rezervované kapacity a atributů. Pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo rozhraní API můžete aktualizovat rozsah rezervovaných rezervací kapacity Azure Database for PostgreSQL – Citus (škálování kapacity).

Další informace o rezervacích Azure najdete v následujících článcích:

* [Co jsou rezervace Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Správa rezervací Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Vysvětlení slevy za rezervaci Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Vysvětlení využití rezervací pro předplatné s průběžnými platbami](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Vysvětlení použití rezervací pro registraci smlouva Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Rezervace Azure v programu partner Center Cloud Solution Provider](https://docs.microsoft.com/partner-center/azure-reservations)
