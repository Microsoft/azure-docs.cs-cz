---
title: Rezervované ceny za výpočetní výkon – Azure Database for PostgreSQL – Citus (škálování)
description: Platba za Azure Database for PostgreSQL (Citus) výpočetních prostředků s rezervovanou kapacitou
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: dcf38a3bcd41ba41bb7cf3b16d022a7a45734c4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85217993"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Platba za Azure Database for PostgreSQL (Citus) výpočetních prostředků s rezervovanou kapacitou

Azure Database for PostgreSQL – Citus () nyní pomáhá ušetřit peníze pomocí předplatného pro výpočetní prostředky v porovnání s cenami průběžných plateb. Díky rezervované kapacitě Citus () můžete učinit předem svůj závazek na skupinu serverů Citus () po dobu jednoho nebo tří let, abyste získali významnou slevu na výpočetní náklady. Pokud si chcete koupit rezervovanou kapacitu (Citus), musíte zadat oblast Azure, podmínky rezervace a četnost fakturace.

> [!IMPORTANT]
> Tato stránka se týká rezervované kapacity Azure Database for PostgreSQL – Citus (škálování). Informace o rezervované kapacitě pro Azure Database for PostgreSQL – jeden server najdete na [této stránce](/azure/postgresql/concept-reserved-pricing) .

Nemusíte přiřazovat rezervaci konkrétním skupinám serverů Citus (). Již spuštěná skupina serverů Citus () nebo ty, které jsou nově nasazené, automaticky získají výhody rezervovaných cen. Když si koupíte rezervaci, platíte za výpočetní náklady po dobu jednoho nebo tří let. Jakmile si koupíte rezervaci, poplatky za výpočetní výkon (Citus), které odpovídají atributům rezervace, se už nebudou účtovat podle tarifů průběžných plateb. Rezervace nepokrývá software, sítě ani poplatky za úložiště spojené se skupinami serverů Citus (). Na konci podmínky rezervace vyprší platnost fakturačního přínosu a skupiny serverů ve velkém měřítku (Citus) se účtují podle ceny za průběžné platby. Rezervace se neobnovují. Informace o cenách najdete v části [Azure Database for PostgreSQL – Citus (nabídka vyhrazené kapacity)](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

Rezervovanou kapacitu Citus () můžete koupit v [Azure Portal](https://portal.azure.com/). Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](https://docs.microsoft.com/azure/cost-management-billing/reservations/monthly-payments-reservations). Zakoupení rezervované kapacity:

* Musíte být v roli vlastníka alespoň u jednoho podnikového nebo individuálního předplatného s tarify průběžných plateb.
* U předplatných se smlouvou Enterprise musí být na webu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance**. Nebo, pokud je toto nastavení zakázané, musíte být správce EA v předplatném.
* V případě programu Cloud Solution Provider (CSP) může koupit rezervovanou kapacitu (Citus).

Podrobnosti o tom, jak se zákazníci s průběžnými platbami a průběžné platby účtují za nákupy rezervací, najdete v tématu [vysvětlení využití rezervace Azure pro vaši podnikovou registraci](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) a [pochopení využití rezervace Azure pro vaše předplatné](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)s průběžnými platbami.

## <a name="determine-the-right-server-group-size-before-purchase"></a>Určení správné velikosti skupiny serverů před nákupem

Velikost rezervace by měla být založená na celkovém počtu výpočtů používaných stávajícím nebo již nasazeným koordinátorem a pracovními uzly ve skupinách serverů Citus () v konkrétní oblasti.

Řekněme například, že používáte jednu skupinu serverů Citus () s 16 vCore koordinátorem a tři 8 vCore pracovních uzlů. Dále budeme předpokládat, že plánujete nasadit do příštího měsíce skupinu serverů Citus (Next Scale) s vCore koordinátorem 32 a dvěma 4 vCore pracovními uzly. Řekněme, že tyto prostředky budete potřebovat aspoň jeden rok.

V takovém případě byste měli zakoupit rezervaci na jednom roce pro

* Celkový počet 16 virtuální jádra + 32 virtuální jádra = 48 virtuální jádra pro uzly koordinátora
* Celkový počet tří uzlů × 8 virtuální jádra + 2 uzlů × 4 virtuální jádra = 24 + 8 = 32 virtuální jádra pro pracovní uzly

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Koupit Azure Database for PostgreSQL rezervovanou kapacitu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **Všechny služby** > **Rezervace**.
3. Vyberte **Přidat** a pak v podokně rezervace nákupu vyberte **Azure Database for PostgreSQL** k nákupu nové rezervace pro databáze PostgreSQL.
4. Vyberte výpočetní typ Citus (), který chcete koupit, a klikněte na tlačítko **Vybrat** .
5. Zkontrolujte množství vybraného výpočetního typu na kartě **produkty** .
4. K dokončení nákupu přejděte na kartu **koupit + revize** .

V následující tabulce jsou popsána povinná pole.

| Pole        | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Předplatné | Předplatné použité pro platbu Azure Database for PostgreSQL rezervované rezervace kapacity. Platební metodou předplatného se účtují náklady na front-end pro rezervaci rezervované kapacity Azure Database for PostgreSQL. Typ předplatného musí být smlouva Enterprise (číslo nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo samostatná smlouva s cenami s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). U podnikového předplatného se poplatky odečtou z bilance peněžního závazku registrace nebo se účtují jako nadlimitní využití. U jednotlivých předplatných s průběžnými platbami se poplatky účtují na základě platební karty nebo platby na faktuře v předplatném.                                                                                  |
| Rozsah        | Obor rezervace vCore může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete: <br><br> **Sdílená,** vCoreá sleva je použita na skupiny serverů Citus (), které jsou spuštěny v libovolném předplatném v rámci vašeho fakturačního kontextu. U podnikových zákazníků je sdíleným oborem registrace a zahrnuje všechna předplatná v rámci registrace.  U zákazníků s průběžnými platbami jsou v rozsahu Sdílený všechna předplatná s průběžnými platbami vytvořená správcem účtu. <br><br> U **jednoho předplatného** se pro skupiny serverů Citus (Vcore) v tomto předplatném používá sleva za rezervaci. <br><br> **Jedna skupina prostředků** – sleva rezervace se použije na skupiny serverů Citus () ve vybraném předplatném a v rámci daného předplatného. |
| Oblast       | Oblast Azure, která je pokrytá Azure Database for PostgreSQL – Citus (rezervace kapacity rezervované na úrovni)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Pojem         | Jednoletá nebo tři roky.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Množství     | Množství výpočetních prostředků, které se zakoupí v rámci rezervované rezervace kapacity (Citus). Konkrétně se virtuální jádra počet koordinátorů nebo pracovních uzlů ve vybrané oblasti Azure, které jsou rezervované a které obdrží fakturační slevu. Například pokud používáte (nebo plánujete spustit) skupiny serverů Citus (s celkovou kapacitou pro výpočetní výkon 64 uzel virtuální jádra a 32 pracovních uzlů virtuální jádra v oblasti Východní USA, pak byste zadali množství jako 64 a 32 pro koordinátora a pracovní uzly, abyste mohli maximalizovat výhody pro všechny servery.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>flexibilita velikosti vCore

flexibilita velikosti vCore pomáhá při horizontálním navýšení nebo snížení kapacity koordinátora a pracovních uzlů v rámci oblasti, aniž by došlo ke ztrátě výhod vyhrazené kapacity.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Sleva rezervovaného vCore se automaticky aplikuje na počet skupin serverů Citus (), které odpovídají Azure Database for PostgreSQL rozsahu rezervace rezervované kapacity a atributů. Rozsah rezervace rezervované kapacity PostgreSQL Azure Database for – Citus () můžete aktualizovat prostřednictvím Azure Portal, PowerShellu, CLI nebo prostřednictvím rozhraní API.

Další informace o rezervacích Azure najdete v následujících článcích:

* [Co jsou rezervace Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Správa rezervací Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Vysvětlení slev za rezervace Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Vysvětlení využití rezervací u předplatného s průběžnými platbami](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Vysvětlení využití rezervací u smlouvy Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
