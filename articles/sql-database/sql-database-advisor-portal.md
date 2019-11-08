---
title: Použití doporučení k výkonu
description: Pomocí Azure Portal můžete najít doporučení pro výkon, která mohou optimalizovat výkon Azure SQL Database.
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
ms.openlocfilehash: 5462a03accb3420b3f0fcec4624734c8f6d68859
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811597"
---
# <a name="find-and-apply-performance-recommendations"></a>Najít a použít doporučení pro výkon

Pomocí Azure Portal můžete najít doporučení k výkonu, která mohou optimalizovat výkon vašeho Azure SQL Database nebo opravit nějaký problém identifikovaný v rámci vašeho zatížení. Stránka s **doporučením pro výkon** v Azure Portal vám umožní najít nejdůležitější doporučení na základě jejich potenciálního dopadu. 

## <a name="viewing-recommendations"></a>Zobrazení doporučení

Chcete-li zobrazit a použít doporučení týkající se výkonu, budete potřebovat správné oprávnění [řízení přístupu na základě role](../role-based-access-control/overview.md) v Azure. **Čtenář**, oprávnění **Přispěvatel SQL DB** jsou nutná k zobrazení doporučení a **vlastníka**, oprávnění **přispěvatele databáze SQL** jsou vyžadována ke spuštění všech akcí; vytvořit nebo vyřadit indexy a zrušit vytváření indexů.

K nalezení doporučení výkonu Azure Portal použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Pro **všechny služby** > **databáze SQL**a vyberte svou databázi.
3. Přejděte do **doporučení ke zvýšení výkonu** a zobrazte dostupná doporučení pro vybranou databázi.

Doporučení týkající se výkonu se zobrazují v tabulce podobné tomu na následujícím obrázku:

![Doporučení](./media/sql-database-advisor-portal/recommendations.png)

Doporučení jsou seřazená podle jejich potenciálního dopadu na výkon do následujících kategorií:

| Dopad | Popis |
|:--- |:--- |
| Vysoký |Doporučení s vysokým dopadem by měla poskytovat nejvýznamnější dopad na výkon. |
| Střednědobé používání |Doporučení středně velkých dopadů by měla zlepšit výkon, ale ne podstatně. |
| Nízká |Doporučení pro nízký dopad by měla poskytovat lepší výkon než bez, ale vylepšení nemusí být významná. |


> [!NOTE]
> Azure SQL Database musí sledovat aktivity alespoň za den, aby bylo možné určit některá doporučení. Azure SQL Database lze snadněji optimalizovat pro konzistentní vzory dotazů, než umožňuje náhodné rozspottych nárůstů aktivity. Pokud doporučení nejsou aktuálně k dispozici, zobrazí stránka s **doporučením pro výkon** zprávu s vysvětlením, proč.
> 

Můžete si také prohlédnout stav historických operací. Pokud chcete zobrazit další informace, vyberte doporučení nebo stav.

Tady je příklad doporučení "vytvořit index" v Azure Portal.

![Vytvořit index](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

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
   
    ![Použít doporučení](./media/sql-database-advisor-portal/apply.png)

Vybrané doporučení se aplikuje na databázi.

### <a name="removing-recommendations-from-the-list"></a>Odebrání doporučení ze seznamu

Pokud seznam doporučení obsahuje položky, které chcete ze seznamu odebrat, můžete toto doporučení zrušit:

1. Kliknutím na doporučení v seznamu **doporučení** otevřete podrobnosti.
2. Na stránce **Podrobnosti** klikněte na **Zahodit** .

V případě potřeby můžete zahozené položky přidat zpátky do seznamu **doporučení** :

1. Na stránce **doporučení** klikněte na **Zobrazit zahozené**.
2. Výběrem zahozené položky ze seznamu zobrazíte její podrobnosti.
3. Volitelně můžete kliknutím na **Zrušit zrušení** přidat index zpátky do hlavního seznamu **doporučení**.

> [!NOTE]
> Upozorňujeme, že pokud je povolené SQL Database [Automatické ladění](sql-database-automatic-tuning.md) a pokud jste ze seznamu ručně zrušili doporučení, toto doporučení se nikdy nepoužije automaticky. Zrušení doporučení je praktický způsob, jak uživatelům povolit automatické ladění v případech, kdy se vyžaduje, aby konkrétní doporučení nebylo použito.
> Toto chování můžete vrátit zpět tak, že do seznamu doporučení přidáte zrušená doporučení tak, že vyberete možnost zrušit zahození.
> 

### <a name="enable-automatic-tuning"></a>Povolení automatického ladění
Můžete nastavit Azure SQL Database pro automatické implementace doporučení. Jakmile budou doporučení k dispozici, automaticky se aplikují. Stejně jako u všech doporučení, která spravuje služba, platí, že pokud je dopad na výkon záporný, doporučení se vrátí.

1. Na stránce **doporučení** klikněte na **automatizovat**:
   
    ![Nastavení Poradce](./media/sql-database-advisor-portal/settings.png)
2. Vyberte akce, které chcete automatizovat:
   
    ![Doporučené indexy](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Upozorňujeme, že možnost **DROP_INDEX** aktuálně není kompatibilní s aplikacemi, které používají přepínání oddílů a parametry indexu. 
>

Po výběru požadované konfigurace klikněte na použít.

### <a name="manually-apply-recommendations-through-t-sql"></a>Ruční použití doporučení prostřednictvím T-SQL

Vyberte libovolné doporučení a pak klikněte na **Zobrazit skript**. Spusťte tento skript pro vaši databázi, abyste mohli doporučení použít ručně.

*Ručně prováděné indexy nejsou monitorovány a ověřovány pro dopad na výkon služby* , takže je navrženo, že tyto indexy po vytvoření sledujete a v případě potřeby je upravíte nebo odstraníte. Podrobnosti o vytváření indexů najdete v tématu [Create index (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx). Ručně použitá doporučení budou navíc aktivní a budou se zobrazovat v seznamu doporučení 24-48 hodin. předtím, než je systém automaticky stáhne. Pokud byste chtěli doporučení odebrat dřív, můžete ho ručně zahodit.

### <a name="canceling-recommendations"></a>Rušení doporučení

Doporučení, která jsou ve stavu **čekání**, **ověřování**nebo **úspěch** , lze zrušit. Doporučení se stavem **provádění** nelze zrušit.

1. Vyberte doporučení v oblasti **Historie ladění** a otevřete stránku s **podrobnostmi o doporučení** .
2. Kliknutím na tlačítko **Storno** přerušte proces použití doporučení.

## <a name="monitoring-operations"></a>Operace sledování

Použití doporučení nemusí okamžitě probíhat. Portál poskytuje podrobné informace o stavu doporučení. Níže jsou možné stavy, ve kterých může být index:

| Status | Popis |
|:--- |:--- |
| Čekající na vyřízení |Příkaz Apply doporučení byl přijat a je naplánován ke spuštění. |
| Zpracovávan |Doporučení se používá. |
| Opětovné |Doporučení se úspěšně nastavilo a služba měří výhody. |
| Úspěch |Doporučení se úspěšně použilo a byly měřeny výhody. |
| Chyba |Během procesu použití doporučení došlo k chybě. Může to být přechodný problém nebo pravděpodobně Změna schématu tabulky a skript již není platný. |
| Vrací |Doporučení bylo použito, ale bylo považováno za nevýkonné a automaticky se vrátí. |
| Vrátit |Doporučení bylo vráceno. |

Kliknutím na místní doporučení ze seznamu zobrazíte další informace:

![Doporučené indexy](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Vrácení doporučení zpět
Pokud jste použili doporučení k výkonu k použití doporučení (což znamená, že jste ručně nespouštěli skript T-SQL), automaticky vrátí změnu, pokud najde dopad na výkon, který je záporný. Pokud z nějakého důvodu jednoduše chcete jenom vrátit doporučení, můžete postupovat takto:

1. V oblasti **Historie ladění** vyberte úspěšně použité doporučení.
2. Na stránce s **podrobnostmi o doporučení** klikněte na **obnovit** .

![Doporučené indexy](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorování dopadu na výkon doporučení indexu
Po úspěšné implementaci doporučení (v současné době platí pouze pro operace s indexem a parametrizovat dotazy), můžete kliknout na **dotaz na přehledy** na stránce s podrobnostmi o doporučeních a otevřít [dotaz – Přehled výkonu](sql-database-query-performance.md) a zobrazit dopad nejdůležitějších dotazů na výkon.

![Sledovat dopad na výkon](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Souhrn
Azure SQL Database poskytuje doporučení pro zlepšení výkonu SQL Database. Díky skriptům T-SQL získáte pomoc při optimalizaci databáze a nakonec se zlepší výkon dotazů.

## <a name="next-steps"></a>Další kroky
Sledujte vaše doporučení a pokračujte v jejich použití pro zlepšení výkonu. Databázová zatížení jsou dynamická a neustále se mění. Azure SQL Database nadále sleduje a poskytuje doporučení, která mohou potenciálně zlepšit výkon vaší databáze. 

* Další informace o automatickém ladění v Azure SQL Database najdete v tématu věnovaném [automatickému ladění](sql-database-automatic-tuning.md) .
* Přehled Azure SQL Databasech doporučení týkajících se výkonu najdete v tématu [doporučení pro výkon](sql-database-advisor.md) .
* V tématu [Přehled výkonu dotazů](sql-database-query-performance.md) najdete informace o tom, jak zobrazit dopad vašich dotazů na výkon.

## <a name="additional-resources"></a>Další zdroje
* [Úložiště dotazů](https://msdn.microsoft.com/library/dn817826.aspx)
* [VYTVOŘIT INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Řízení přístupu na základě rolí](../role-based-access-control/overview.md)

