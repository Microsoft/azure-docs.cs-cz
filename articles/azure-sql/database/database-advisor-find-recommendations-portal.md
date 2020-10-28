---
title: Použití doporučení k výkonu
description: Pomocí Azure Portal můžete najít doporučení pro výkon, která můžou optimalizovat výkon vaší databáze.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 12/19/2018
ms.openlocfilehash: 6ad8f3e146c13e7b88752b8ef6d514346542ce26
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92672271"
---
# <a name="find-and-apply-performance-recommendations"></a>Najít a použít doporučení pro výkon
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Pomocí Azure Portal můžete najít doporučení k výkonu, která můžou optimalizovat výkon databáze v Azure SQL Database nebo opravit nějaký problém identifikovaný v rámci vašeho zatížení. Stránka s **doporučením pro výkon** v Azure Portal vám umožní najít nejdůležitější doporučení na základě jejich potenciálního dopadu.

## <a name="viewing-recommendations"></a>Zobrazení doporučení

Pokud chcete zobrazit a použít doporučení pro výkon, budete potřebovat správné oprávnění [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) v Azure. **Čtenář** , oprávnění **Přispěvatel SQL DB** jsou nutná k zobrazení doporučení a **vlastníka** , oprávnění **přispěvatele databáze SQL** jsou vyžadována ke spuštění všech akcí; vytvořit nebo vyřadit indexy a zrušit vytváření indexů.

Pomocí následujících kroků můžete najít doporučení týkající se výkonu Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejít na **všechny služby**  >  **databáze SQL** a vyberte svou databázi.
3. Přejděte do **doporučení ke zvýšení výkonu** a zobrazte dostupná doporučení pro vybranou databázi.

Doporučení týkající se výkonu se zobrazují v tabulce podobné tomu na následujícím obrázku:

![Snímek obrazovky ukazuje doporučení týkající se výkonu v tabulce s popisem akce a doporučení.](./media/database-advisor-find-recommendations-portal/recommendations.png)

Doporučení jsou seřazená podle jejich potenciálního dopadu na výkon do následujících kategorií:

| Dopad | Popis |
|:--- |:--- |
| Vysoké |Doporučení s vysokým dopadem by měla poskytovat nejvýznamnější dopad na výkon. |
| Střední |Doporučení středně velkých dopadů by měla zlepšit výkon, ale ne podstatně. |
| Nízká |Doporučení pro nízký dopad by měla poskytovat lepší výkon než bez, ale vylepšení nemusí být významná. |

> [!NOTE]
> Azure SQL Database musí sledovat aktivity alespoň za den, aby bylo možné určit některá doporučení. Azure SQL Database lze snadněji optimalizovat pro konzistentní vzory dotazů, než umožňuje náhodné rozspottych nárůstů aktivity. Pokud doporučení nejsou aktuálně k dispozici, zobrazí stránka s **doporučením pro výkon** zprávu s vysvětlením, proč.

Můžete si také prohlédnout stav historických operací. Pokud chcete zobrazit další informace, vyberte doporučení nebo stav.

Tady je příklad doporučení "vytvořit index" v Azure Portal.

![Vytvořit index](./media/database-advisor-find-recommendations-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Použití doporučení

Azure SQL Database vám poskytuje plnou kontrolu nad tím, jak jsou doporučení povolená, a to pomocí kterékoli z následujících tří možností:

* Jednotlivé doporučení použijte po jednom.
* Povolení automatického ladění pro automatické použití doporučení.
* Pokud chcete doporučení implementovat ručně, spusťte doporučený skript T-SQL pro vaši databázi.

Pokud chcete zobrazit podrobnosti, vyberte libovolné doporučení a pak klikněte na **Zobrazit skript** . Projděte si přesné podrobnosti o tom, jak se doporučení vytvořilo.

Databáze zůstane při použití doporučení online – pomocí doporučení výkonu nebo automatického ladění nikdy nepřevezme databázi offline.

### <a name="apply-an-individual-recommendation"></a>Použít individuální doporučení

Doporučení si můžete prohlédnout a přijmout v jednom okamžiku.

1. Na stránce **doporučení** vyberte doporučení.
2. Na stránce **Podrobnosti** klikněte na tlačítko **použít** .

   ![Použít doporučení](./media/database-advisor-find-recommendations-portal/apply.png)

Vybraná doporučení se aplikují na databázi.

### <a name="removing-recommendations-from-the-list"></a>Odebrání doporučení ze seznamu

Pokud seznam doporučení obsahuje položky, které chcete ze seznamu odebrat, můžete toto doporučení zrušit:

1. Kliknutím na doporučení v seznamu **doporučení** otevřete podrobnosti.
2. Na stránce **Podrobnosti** klikněte na **Zahodit** .

V případě potřeby můžete zahozené položky přidat zpátky do seznamu **doporučení** :

1. Na stránce **doporučení** klikněte na **Zobrazit zahozené** .
2. Výběrem zahozené položky ze seznamu zobrazíte její podrobnosti.
3. Volitelně můžete kliknutím na **Zrušit zrušení** přidat index zpátky do hlavního seznamu **doporučení** .

> [!NOTE]
> Upozorňujeme, že pokud je povolené SQL Database [Automatické ladění](automatic-tuning-overview.md) a pokud jste ze seznamu ručně zrušili doporučení, toto doporučení se nikdy nepoužije automaticky. Zrušení doporučení je praktický způsob, jak uživatelům povolit automatické ladění v případech, kdy se vyžaduje, aby konkrétní doporučení nebylo použito.
> Toto chování můžete vrátit zpět tak, že do seznamu doporučení přidáte zrušená doporučení tak, že vyberete možnost zrušit zahození.

### <a name="enable-automatic-tuning"></a>Povolení automatického ladění

Databázi můžete nastavit tak, aby automaticky implementovala doporučení. Jakmile budou doporučení k dispozici, automaticky se aplikují. Stejně jako u všech doporučení, která spravuje služba, platí, že pokud je dopad na výkon záporný, doporučení se vrátí.

1. Na stránce **doporučení** klikněte na **automatizovat** :

   ![Nastavení Poradce](./media/database-advisor-find-recommendations-portal/settings.png)
2. Vyberte akce, které chcete automatizovat:

   ![Snímek obrazovky, který ukazuje, kde vybrat akce k automatizaci](./media/database-advisor-find-recommendations-portal/server.png)

> [!NOTE]
> Upozorňujeme, že možnost **DROP_INDEX** aktuálně není kompatibilní s aplikacemi, které používají přepínání oddílů a parametry indexu.

Po výběru požadované konfigurace klikněte na použít.

### <a name="manually-apply-recommendations-through-t-sql"></a>Ruční použití doporučení prostřednictvím T-SQL

Vyberte libovolné doporučení a pak klikněte na **Zobrazit skript** . Spusťte tento skript pro vaši databázi, abyste mohli doporučení použít ručně.

*Ručně prováděné indexy nejsou monitorovány a ověřovány pro dopad na výkon služby* , takže je navrženo, že tyto indexy po vytvoření sledujete a v případě potřeby je upravíte nebo odstraníte. Podrobnosti o vytváření indexů najdete v tématu [Create index (Transact-SQL)](/sql/t-sql/statements/create-index-transact-sql). Ručně použitá doporučení budou navíc aktivní a budou se zobrazovat v seznamu doporučení 24-48 hodin. předtím, než je systém automaticky stáhne. Pokud byste chtěli doporučení odebrat dřív, můžete ho ručně zahodit.

### <a name="canceling-recommendations"></a>Rušení doporučení

Doporučení, která jsou ve stavu **čekání** , **ověřování** nebo **úspěch** , lze zrušit. Doporučení se stavem **provádění** nelze zrušit.

1. Vyberte doporučení v oblasti **Historie ladění** a otevřete stránku s **podrobnostmi o doporučení** .
2. Kliknutím na tlačítko **Storno** přerušte proces použití doporučení.

## <a name="monitoring-operations"></a>Monitorování operací

Použití doporučení nemusí okamžitě probíhat. Portál poskytuje podrobné informace o stavu doporučení. Níže jsou možné stavy, ve kterých může být index:

| Status | Popis |
|:--- |:--- |
| Čekající |Příkaz Apply doporučení byl přijat a je naplánován ke spuštění. |
| Zpracovávan |Doporučení se používá. |
| Opětovné |Doporučení se úspěšně nastavilo a služba měří výhody. |
| Success |Doporučení se úspěšně použilo a byly měřeny výhody. |
| Chybová |Během procesu použití doporučení došlo k chybě. Může to být přechodný problém nebo pravděpodobně Změna schématu tabulky a skript již není platný. |
| Vrací |Doporučení bylo použito, ale bylo považováno za nevýkonné a automaticky se vrátí. |
| Vrátit |Doporučení bylo vráceno. |

Kliknutím na místní doporučení ze seznamu zobrazíte další informace:

![Snímek obrazovky, který zobrazuje seznam doporučených postupů.](./media/database-advisor-find-recommendations-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Vrácení doporučení zpět

Pokud jste použili doporučení k výkonu k použití doporučení (což znamená, že jste ručně nespouštěli skript T-SQL), automaticky vrátí změnu, pokud najde dopad na výkon, který je záporný. Pokud z nějakého důvodu jednoduše chcete jenom vrátit doporučení, můžete postupovat takto:

1. V oblasti **Historie ladění** vyberte úspěšně použité doporučení.
2. Na stránce s **podrobnostmi o doporučení** klikněte na **obnovit** .

![Doporučené indexy](./media/database-advisor-find-recommendations-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorování dopadu na výkon doporučení indexu

Po úspěšném dokončení implementace doporučení (v současné době platí pouze pro operace s indexem a parametrizovat dotaz), můžete kliknout na **dotaz na přehledy** na stránce s podrobnostmi o doporučeních, otevřít [dotaz na výkon](query-performance-insight-use.md) a zobrazit dopad na výkon vašich dotazů na nejvyšší úrovni.

![Sledovat dopad na výkon](./media/database-advisor-find-recommendations-portal/query-insights.png)

## <a name="summary"></a>Souhrn

Azure SQL Database poskytuje doporučení pro zlepšení výkonu databáze. Díky skriptům T-SQL získáte pomoc při optimalizaci databáze a nakonec se zlepší výkon dotazů.

## <a name="next-steps"></a>Další kroky

Sledujte vaše doporučení a pokračujte v jejich použití pro zlepšení výkonu. Databázová zatížení jsou dynamická a neustále se mění. Azure SQL Database nadále sleduje a poskytuje doporučení, která mohou potenciálně zlepšit výkon vaší databáze.

* Další informace o automatickém ladění v Azure SQL Database najdete v tématu věnovaném [automatickému ladění](automatic-tuning-overview.md) .
* Přehled Azure SQL Databasech doporučení týkajících se výkonu najdete v tématu [doporučení pro výkon](database-advisor-implement-performance-recommendations.md) .
* V tématu [Přehled výkonu dotazů](query-performance-insight-use.md) najdete informace o tom, jak zobrazit dopad vašich dotazů na výkon.

## <a name="additional-resources"></a>Další zdroje informací

* [Úložiště dotazů](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [CREATE INDEX](/sql/t-sql/statements/create-index-transact-sql)
* [Řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md)