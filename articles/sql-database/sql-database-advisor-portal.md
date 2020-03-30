---
title: Použití doporučení k výkonu
description: Na webu Azure Portal můžete najít doporučení pro výkon, která můžou optimalizovat výkon databáze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: b0452d51dc472e100ef52536d8e3814ff395292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214178"
---
# <a name="find-and-apply-performance-recommendations"></a>Vyhledání a použití doporučení pro výkon

Na webu Azure Portal můžete najít doporučení pro výkon, která můžou optimalizovat výkon databáze Azure SQL database, nebo opravit nějaký problém zjištěný ve vaší pracovní zátěži. Stránka **doporučení výkonu** na Webu Azure Portal vám umožňuje najít nejlepší doporučení na základě jejich potenciálního dopadu.

## <a name="viewing-recommendations"></a>Zobrazení doporučení

Chcete-li zobrazit a použít doporučení pro výkon, potřebujete v Azure správná oprávnění [k řízení přístupu na základě rolí.](../role-based-access-control/overview.md) **Reader**, **SQL DB přispěvatel** oprávnění jsou vyžadována pro zobrazení doporučení a **vlastník**, SQL **DB přispěvatel** oprávnění jsou vyžadovány k provedení jakékoli akce; vytvořit nebo přetažení indexů a zrušit vytváření indexu.

Pomocí následujících kroků vyhledejte doporučení k výkonu na webu Azure Portal:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Přejděte do databáze**SQL** **všech služeb** > a vyberte databázi.
3. Přejděte na **doporučení výkonu** a zobrazte dostupná doporučení pro vybranou databázi.

Doporučení pro výkon jsou uvedena v tabulce podobné té, která je znázorněna na následujícím obrázku:

![Doporučení](./media/sql-database-advisor-portal/recommendations.png)

Doporučení jsou seřazena podle jejich potenciálního dopadu na výkon do následujících kategorií:

| Dopad | Popis |
|:--- |:--- |
| Vysoká |Doporučení s vysokým dopadem by měla mít největší dopad na výkonnost. |
| Střednědobé používání |Doporučení se středním dopadem by měla zlepšit výkon, ale ne podstatně. |
| Nízká |Doporučení s nízkým dopadem by měla poskytovat lepší výkon než bez, ale zlepšení nemusí být významná. |

> [!NOTE]
> Azure SQL Database potřebuje sledovat aktivity alespoň jeden den, aby bylo možné identifikovat některá doporučení. Azure SQL Database můžete snadněji optimalizovat pro konzistentní vzorce dotazu, než může pro náhodné spotty shluky aktivity. Pokud doporučení nejsou aktuálně k dispozici, stránka **Doporučení výkonu** obsahuje zprávu vysvětlující proč.

Můžete také zobrazit stav historických operací. Chcete-li zobrazit další informace, vyberte doporučení nebo stav.

Tady je příklad doporučení "Vytvořit index" na webu Azure Portal.

![Vytvořit index](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Uplatňování doporučení

Azure SQL Database poskytuje úplnou kontrolu nad tím, jak jsou povolena doporučení pomocí některé z následujících tří možností:

* Jednotlivá doporučení aplikujte po jednom.
* Povolte automatické ladění, chcete-li automaticky použít doporučení.
* Chcete-li implementovat doporučení ručně, spusťte doporučený skript T-SQL proti databázi.

Vyberte libovolné doporučení, chcete-li zobrazit jeho podrobnosti, a potom klepněte na tlačítko **Zobrazit skript** a zkontrolujte přesné podrobnosti o tom, jak je doporučení vytvořeno.

Databáze zůstane online, zatímco doporučení je použita – pomocí doporučení výkonu nebo automatické ladění nikdy trvá databázi offline.

### <a name="apply-an-individual-recommendation"></a>Použití individuálního doporučení

Můžete prohlížet a přijímat doporučení jeden po druhém.

1. Na stránce **Doporučení** vyberte doporučení.
2. Na stránce **Podrobnosti** klikněte na **tlačítko Použít.**

   ![Použít doporučení](./media/sql-database-advisor-portal/apply.png)

Vybrané doporučení jsou použity v databázi.

### <a name="removing-recommendations-from-the-list"></a>Odebrání doporučení ze seznamu

Pokud seznam doporučení obsahuje položky, které chcete ze seznamu odebrat, můžete doporučení zahodit:

1. Vyberte doporučení v seznamu **doporučení** otevřít podrobnosti.
2. Na stránce **Podrobnosti** klikněte na **Zahodit.**

V případě potřeby můžete zahodit položky přidat zpět do seznamu **Doporučení:**

1. Na stránce **Doporučení** klikněte na **Zobrazit zahozeno**.
2. Vyberte zahozenou položku ze seznamu a zobrazte její podrobnosti.
3. Případně klepněte na tlačítko **Vrátit zahodit** a přidejte index zpět do hlavního seznamu **doporučení**.

> [!NOTE]
> Vezměte prosím na vědomí, že pokud je povoleno [automatické ladění](sql-database-automatic-tuning.md) databáze SQL a pokud jste ručně zahodili doporučení ze seznamu, toto doporučení se nikdy nepoužije automaticky. Zahození doporučení je praktický způsob, jak mohou mít uživatelé povoleno automatické ladění v případech, kdy vyžaduje, aby konkrétní doporučení nemělo být použito.
> Toto chování můžete vrátit přidáním zahozených doporučení zpět do seznamu Doporučení výběrem možnosti Vrátit zahodit.

### <a name="enable-automatic-tuning"></a>Povolení automatického ladění

Azure SQL Database můžete nastavit tak, aby automaticky implementovala doporučení. Jakmile budou doporučení k dispozici, budou automaticky použita. Stejně jako u všech doporučení spravovaných službou, pokud dopad na výkon je negativní, doporučení se vrátí.

1. Na stránce **Doporučení** klikněte na **Automatizovat**:

   ![Nastavení poradce](./media/sql-database-advisor-portal/settings.png)
2. Vyberte akce, které chcete automatizovat:

   ![Doporučené indexy](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Vezměte prosím na vědomí, **že možnost DROP_INDEX** není v současné době kompatibilní s aplikacemi pomocí přepínání oddílů a rady pro index.

Po výběru požadované konfigurace klepněte na tlačítko Použít.

### <a name="manually-apply-recommendations-through-t-sql"></a>Ruční použití doporučení prostřednictvím T-SQL

Vyberte libovolné doporučení a klepněte na tlačítko **Zobrazit skript**. Spusťte tento skript proti databázi ručně použít doporučení.

*Indexy, které jsou ručně spuštěny nejsou sledovány a ověřeny pro dopad na výkon služby,* takže je navrženo, abyste sledovat tyto indexy po vytvoření ověřit, že poskytují zvýšení výkonu a upravit nebo odstranit v případě potřeby. Podrobnosti o vytváření indexů naleznete v [tématu CREATE INDEX (Transact-SQL).](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) Kromě toho zůstanou ručně použitá doporučení aktivní a zobrazí se v seznamu doporučení pro 24-48 hodin. před tím, než je systém automaticky stáhne. Pokud chcete doporučení odebrat dříve, můžete ho ručně zahodit.

### <a name="canceling-recommendations"></a>Zrušení doporučení

Doporučení, která jsou ve stavu **Čeká na vyřízení**, **Ověření**nebo **Úspěch,** lze zrušit. Doporučení se stavem **Provádění** nelze zrušit.

1. Vyberte doporučení v oblasti **Historie ladění** a otevřete stránku **podrobností doporučení.**
2. Klepnutím na tlačítko **Storno** přerušíte proces použití doporučení.

## <a name="monitoring-operations"></a>Monitorovací operace

Použití doporučení se nemusí stát okamžitě. Portál poskytuje podrobnosti o stavu doporučení. Následující jsou možné stavy, které index může být v:

| Status | Popis |
|:--- |:--- |
| Čekající na vyřízení |Příkaz Použít doporučení byl přijat a je naplánován o spuštění. |
| Provádění |Doporučení se uplatňuje. |
| Ověřování |Doporučení bylo úspěšně použito a služba měří výhody. |
| Úspěch |Doporučení bylo úspěšně aplikováno a přínosy byly měřeny. |
| Chyba |Během procesu použití doporučení došlo k chybě. Může se jedná o přechodný problém nebo případně změnu schématu do tabulky a skript již není platný. |
| Návrat |Doporučení bylo použito, ale bylo považováno za nevýkonné a je automaticky vráceno. |
| Vráceny |Doporučení bylo vráceno. |

Kliknutím na doporučení v průběhu procesu ze seznamu zobrazíte další informace:

![Doporučené indexy](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Vrácení doporučení

Pokud jste použili doporučení výkonu použít doporučení (což znamená, že jste ručně spustit skript T-SQL), automaticky vrátí změnu, pokud zjistí, že dopad na výkon je negativní. Pokud z nějakého důvodu chcete doporučení jednoduše vrátit, můžete provést následující kroky:

1. Vyberte úspěšně použité doporučení v oblasti **historie ladění.**
2. Na stránce **podrobností doporučení** klikněte na **Vrátit.**

![Doporučené indexy](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Sledování dopadu doporučení indexu na výkon

Po úspěšné implementaci doporučení (v současné době pouze operace indexu a parametrizaci doporučení dotazů) můžete kliknutím na **Query Insights** na stránce s podrobnostmi doporučení otevřít [přehledy výkonu dotazů](sql-database-query-performance.md) a zobrazit dopad na výkon vašich hlavních dotazů.

![Sledování dopadu na výkon](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Souhrn

Azure SQL Database poskytuje doporučení pro zlepšení výkonu databáze SQL. Poskytnutím skriptů T-SQL získáte pomoc při optimalizaci databáze a nakonec ke zlepšení výkonu dotazů.

## <a name="next-steps"></a>Další kroky

Sledujte svá doporučení a pokračujte v jejich použití k upřesnění výkonu. Databázové úlohy jsou dynamické a neustále se mění. Azure SQL Database pokračuje v monitorování a poskytování doporučení, která mohou potenciálně zlepšit výkon vaší databáze.

* Další informace o automatickém ladění v Azure SQL Database najdete v tématu [Automatické ladění.](sql-database-automatic-tuning.md)
* Přehled doporučení pro výkon azure sql database u článku najdete v článku Doporučení pro [výkon.](sql-database-advisor.md)
* Informace o zobrazení dopadu na výkon vašich hlavních dotazů najdete v tématu [Přehledy výkonu dotazů.](sql-database-query-performance.md)

## <a name="additional-resources"></a>Další zdroje

* [Úložiště dotazů](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Řízení přístupu založené na rolích](../role-based-access-control/overview.md)
