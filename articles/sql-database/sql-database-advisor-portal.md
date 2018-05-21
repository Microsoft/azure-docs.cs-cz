---
title: Použít výkonu doporučení – Azure SQL Database | Microsoft Docs
description: Použití portálu Azure k vyhledání doporučení výkonu, které můžete optimalizovat výkon vaší databáze SQL Azure.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 3361519c260fe842ae362814cbee62aa9257b9f8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="find-and-apply-performance-recommendations"></a>Najít a použít doporučení výkonu

Portálu Azure můžete použít k vyhledání doporučení výkonu, které můžete optimalizovat výkon vaší databáze SQL Azure nebo opravte některé problém uvedený v vaše úlohy. **Výkon doporučení** stránka na portálu Azure umožňuje najít nejvyšší doporučení podle jejich potenciální vliv. 

## <a name="viewing-recommendations"></a>Zobrazení doporučení

Pokud chcete zobrazit a použít doporučení pro optimální výkon, je nutné správný [řízení přístupu na základě role](../role-based-access-control/overview.md) oprávnění v Azure. **Čtečka**, **Přispěvatel databází SQL** zobrazíte doporučení, jsou vyžadována oprávnění a **vlastníka**, **Přispěvatel databází SQL** se vyžadují oprávnění k provádět žádné akce; Vytvořit nebo vyřadit indexy a zrušit vytváření indexu.

Najít výkonu doporučení na portálu Azure pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte na **všechny služby** > **databází SQL**a vyberte svou databázi.
3. Přejděte na **výkonu doporučení** zobrazíte doporučení k dispozici pro vybranou databázi.

Výkon doporučení jsou uvedené v tabulce podobně jako na následujícím obrázku:

![Doporučení](./media/sql-database-advisor-portal/recommendations.png)

Doporučení jsou seřazené podle jejich potenciální dopad na výkon do následujících kategorií:

| Dopad | Popis |
|:--- |:--- |
| Vysoký |Vysoký dopad na doporučení by měl poskytovat nejvýraznější dopad na výkon. |
| Střednědobé používání |Střední dopad doporučení by měla zvýšit výkon, ale není podstatně. |
| Nízká |Nízký dopad doporučení by měl poskytovat lepší výkon než bez, ale nemusí být výrazné vylepšení. |


> [!NOTE]
> Databáze SQL Azure je potřeba sledovat aktivity alespoň za den za účelem zjištění několik doporučení. Databáze SQL Azure můžete snadněji optimalizovat konzistentní dotazu v případě vzorů, než je to možné na náhodných problematického shluky aktivity. Pokud nejsou momentálně k dispozici, doporučení **výkonu doporučení** stránka obsahuje zprávu s vysvětlením, proč.
> 

Můžete také zobrazit stav přehled činností. Vyberte doporučení nebo stav zobrazíte další informace.

Tady je příklad "Vytvořit index" doporučení na portálu Azure.

![Vytvořit index](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Uplatňovat doporučení
Databáze SQL Azure vám dává plnou kontrolu nad jak doporučení jsou povolit pomocí některé z následujících tří možností: 

* Používat jednotlivé doporučení jeden v daném okamžiku.
* Povolte automatické ladění pro automatické použití doporučení.
* Pokud chcete doporučení implementovat ručně, spusťte doporučené skript T-SQL databáze.

Vyberte všechny doporučení zobrazit jeho podrobnosti a potom klikněte na **zobrazit skript** ke kontrole přesné údaje o tom, jak vytvořit doporučení.

Databáze zůstane online, zatímco doporučení platí – pomocí výkonu doporučení nebo automatické ladění nikdy převede do režimu offline databáze.

### <a name="apply-an-individual-recommendation"></a>Jednotlivá doporučení použít
Můžete zkontrolovat a přijmout doporučení jeden najednou.

1. Na **doporučení** vyberte doporučení.
2. Na **podrobnosti** klikněte na tlačítko **použít** tlačítko.
   
    ![použít doporučení](./media/sql-database-advisor-portal/apply.png)

Vybrané doporučení se používají v databázi.

### <a name="removing-recommendations-from-the-list"></a>Odebrání doporučení ze seznamu
Pokud seznam doporučení obsahuje položky, které chcete odebrat ze seznamu, můžete zrušit doporučení:

1. V seznamu vyberte doporučení **doporučení** otevřete podrobnosti.
2. Klikněte na tlačítko **zahodit** na **podrobnosti** stránky.

V případě potřeby můžete přidat vyřazené položky zpět do **doporučení** seznamu:

1. Na **doporučení** klikněte na tlačítko **zobrazení zahozena**.
2. Vyberte položku zrušených ze seznamu zobrazíte její podrobnosti.
3. Volitelně klikněte na **vrátit zpět zahodit** přidat index zpět do hlavní seznam **doporučení**.

> [!NOTE]
> Upozorňujeme, že pokud databáze SQL [automatické ladění](sql-database-automatic-tuning.md) je povolená, a pokud jste ručně zrušené doporučení ze seznamu, tato doporučení se nikdy použijí automaticky. Zahození doporučení je užitečný způsob, jak uživatelé měli automatické ladění povoleno v případech, vyžadují, že by neměl být použitá konkrétní doporučení.
> Toto chování můžete obnovit tak, že přidáte zrušených doporučení zpět do seznamu doporučení pomocí možnosti Zahodit vrátit zpět.
> 

### <a name="enable-automatic-tuning"></a>Povolení automatického ladění
Můžete nastavit Azure SQL Database k implementaci doporučení automaticky. Doporučení jsou k dispozici, budou automaticky použita. Stejně jako u všech doporučení, spravováno službou, pokud je negativní dopad na výkon, je vrácen doporučení.

1. Na **doporučení** klikněte na tlačítko **automatické**:
   
    ![Nastavení služby Advisor](./media/sql-database-advisor-portal/settings.png)
2. Vyberte akce automatizovat:
   
    ![Doporučené indexy](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Pamatujte, že **DROP_INDEX** možnost v tuto chvíli není kompatibilní s aplikací pomocí pomocné parametry přepínání a index oddílu a nesmí být zapnut v těchto případech.
>

### <a name="manually-run-the-recommended-t-sql-script"></a>Ručně spustit doporučené skriptu T-SQL
Vyberte všechny doporučení a pak klikněte na tlačítko **zobrazit skript**. Spusťte tento skript databázi doporučení použít ručně.

*Indexy, které jsou spouštěny ručně nejsou sledovat a ověřit pro vlivu na výkon službou* , doporučujeme sledovat tyto indexy po vytvoření ověřte se poskytují zvýšení výkonu a upravit nebo odstranit v případě potřeby. Podrobnosti o vytváření indexů najdete v tématu [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).

### <a name="canceling-recommendations"></a>Zrušení doporučení
Doporučení, které jsou v **čekající**, **ověření**, nebo **úspěch** stav se dají zrušit. Doporučení se stavem **zpracování** nelze zrušit.

1. Vyberte doporučení v **ladění historie** oblasti otevřete **podrobnosti doporučení** stránky.
2. Klikněte na tlačítko **zrušit** k přerušení proces použití doporučení.

## <a name="monitoring-operations"></a>Monitorování operací
Použití doporučeným nemusí dojít okamžitě. Portál obsahuje podrobné informace týkající se stavu doporučení. Toto jsou možné stavy, které může být index:

| Status | Popis |
|:--- |:--- |
| Čekající na vyřízení |Platí doporučení, příkaz byl přijat a je naplánováno spuštění. |
| Provádění |Doporučuje se právě používá. |
| Ověřování |Bylo úspěšně aplikováno doporučení a službu je měření výhody. |
| Úspěch |Bylo úspěšně aplikováno doporučení a měří výhody. |
| Chyba |Při zavádění doporučení se stala chyba. Může to být dočasný problém, nebo které by mohly mít schéma změnit do tabulky a skript již není platný. |
| Probíhá vrácení |Doporučení bylo použito, ale je považována za nenáročných a automaticky zrušeny. |
| Vráceno |Doporučuje se vrátila. |

Kliknutím na doporučení v procesu v seznamu zobrazíte další informace:

![Doporučené indexy](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Při vrácení doporučení
Pokud jste použili výkonu doporučení k doporučení použít (což znamená, že nebyl spuštěn ručně skriptu T-SQL), se automaticky vrátí změnu Pokud najde negativní dopad na výkon. Pokud z nějakého důvodu chcete jednoduše obnovit doporučení, můžete provést následující:

1. Vyberte úspěšně použité doporučení v **ladění historie** oblasti.
2. Klikněte na tlačítko **vrátit** na **podrobnosti o doporučení** stránky.

![Doporučené indexy](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorování vlivu na výkon doporučení indexu
Po doporučení jsou úspěšně implementováno (v současné době operace indexu a Parametrizace pouze dotazy doporučení), můžete kliknout na **přehled dotazu** na doporučení podrobnosti stránky otevřete [dotazu Přehled o výkonu](sql-database-query-performance.md) a zobrazit dopad na výkon nejčastějších dotazů.

![Dopad na výkon monitorování](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Souhrn
Databáze SQL Azure poskytuje doporučení pro zlepšení výkonu databáze SQL. Tím, že poskytuje skriptů T-SQL, získáte pomoc při optimalizaci vaši databázi a nakonec zlepšení výkonu dotazů.

## <a name="next-steps"></a>Další postup
Sledovat vaše doporučení a pokračuje v používání jejich Upřesnit výkonu. Databázové úlohy jsou dynamické a průběžně změnu. Azure SQL Database i nadále monitorovat a poskytovat doporučení, které může zlepšit výkon vaší databáze. 

* V tématu [automatické ladění](sql-database-automatic-tuning.md) Další informace o automatické ladění ve službě Azure SQL Database.
* V tématu [výkonu doporučení](sql-database-advisor.md) přehled o výkonu doporučení Azure SQL Database.
* V tématu [Query Performance Insight](sql-database-query-performance.md) Další informace o zobrazení dopad na výkon nejčastějších dotazů.

## <a name="additional-resources"></a>Další zdroje informací:
* [Úložiště dotazů](https://msdn.microsoft.com/library/dn817826.aspx)
* [VYTVOŘENÍ INDEXU](https://msdn.microsoft.com/library/ms188783.aspx)
* [Řízení přístupu na základě rolí](../role-based-access-control/overview.md)

