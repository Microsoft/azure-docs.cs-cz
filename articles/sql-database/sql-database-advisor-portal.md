---
title: Použití doporučení k výkonu – Azure SQL Database | Dokumentace Microsoftu
description: Najít doporučení k výkonu, které můžete optimalizovat výkon Azure SQL Database pomocí webu Azure portal.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: d06d29fb43ea8d8971c3f013c1e66406a30798d3
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064250"
---
# <a name="find-and-apply-performance-recommendations"></a>Vyhledání a použití doporučení k výkonu

Na webu Azure portal můžete použít k vyhledání doporučení k výkonu, které můžete optimalizovat výkon vaší databáze SQL Azure nebo chcete-li některý problém identifikovat ve vašich úloh. **Doporučení k výkonu** stránky na webu Azure portal umožňuje najít nejčastějších doporučení podle jejich potenciální dopad. 

## <a name="viewing-recommendations"></a>Zobrazení doporučení

K zobrazení a použití doporučení k výkonu, je třeba správné [řízení přístupu na základě rolí](../role-based-access-control/overview.md) oprávnění v Azure. **Čtečka**, **Přispěvatel databází SQL** oprávnění nejsou vyžadována k zobrazení doporučení, a **vlastníka**, **Přispěvatel databází SQL** oprávnění jsou vyžadována pro provádět žádné akce; Vytvořit nebo vyřadit indexy a zrušit vytváření indexů.

Následujícím postupem zobrazíte doporučení k výkonu na portálu Azure portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte na **všechny služby** > **databází SQL**a vyberte svou databázi.
3. Přejděte do **výkonu doporučení** zobrazíte doporučení k dispozici pro vybranou databázi.

Doporučení k výkonu jsou uvedeny v tabulce podobný tomu na následujícím obrázku:

![Doporučení](./media/sql-database-advisor-portal/recommendations.png)

Doporučení jsou seřazeny podle jejich potenciální dopad na výkon do následujících kategorií:

| Dopad | Popis |
|:--- |:--- |
| Vysoký |Vysoký dopad doporučení by měla poskytnout nejvýraznější dopad na výkon. |
| Střednědobé používání |Střední dopad doporučení by měl zvýšit výkon, ale není výrazně. |
| Nízká |Nízký dopad doporučení by měl poskytovat lepší výkon než bez, ale nemusí být výrazné vylepšení. |


> [!NOTE]
> Azure SQL Database je potřeba sledovat aktivity alespoň jeden den za účelem zjištění několik doporučení. Azure SQL Database můžete snadněji optimalizovat pro vzory dotazů konzistentní než dokáže pro náhodné problematického nárůstům aktivity. Pokud nejsou aktuálně k dispozici, doporučení **výkonu doporučení** stránka obsahuje zprávu s vysvětlením, proč.
> 

Můžete také zobrazit stav přehled činností. Vyberte stav nebo doporučení zobrazíte další informace.

Tady je příklad z "Vytvořit index" doporučení na webu Azure Portal.

![Vytvořit index](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Použití doporučení
Azure SQL Database poskytuje plnou kontrolu nad jak doporučení jsou povolené pomocí kteréhokoli z těchto tří možností: 

* Použijte individuální doporučení jeden po druhém.
* Povolte automatické ladění pro automatické použití doporučení.
* Implementace doporučení ručně, spusťte doporučené skriptu T-SQL na vaší databázi.

Vyberte jakékoli doporučení a zobrazit jeho podrobnosti a potom klikněte na tlačítko **zobrazit skript** zobrazíte podrobnosti o tom, jak se vytváří doporučení.

Databáze zůstane online, zatímco doporučení platí – pomocí doporučení výkonu nebo automatické ladění nikdy převede do režimu offline databáze.

### <a name="apply-an-individual-recommendation"></a>Použít individuální doporučení
Můžete zkontrolovat a přijmout doporučení jeden po druhém.

1. Na **doporučení** vyberte doporučení.
2. Na **podrobnosti** klikněte na **použít** tlačítko.
   
    ![Použití doporučení](./media/sql-database-advisor-portal/apply.png)

Vybrané doporučení se používají v databázi.

### <a name="removing-recommendations-from-the-list"></a>Doporučení se odebírá ze seznamu
Pokud váš seznam doporučení, která obsahuje položky, které chcete odebrat ze seznamu, můžete zrušit doporučení:

1. V seznamu vyberte doporučení **doporučení** zobrazíte podrobnosti.
2. Klikněte na tlačítko **zahodit** na **podrobnosti** stránky.

V případě potřeby můžete přidat vyřazené položky zpět **doporučení** seznamu:

1. Na **doporučení** klikněte na **zobrazení nepřijatých**.
2. Vyberte zrušených položku ze seznamu zobrazíte její podrobnosti.
3. Případně můžete kliknout **vrátit zpět zahození** přidat index zpět do hlavní seznam **doporučení**.

> [!NOTE]
> Všimněte si, že pokud databáze SQL [automatické ladění](sql-database-automatic-tuning.md) je povolená, a pokud jste ručně zrušených doporučení v seznamu, tato doporučení se nikdy použije automaticky. Zahazuje se doporučení je praktický způsob pro uživatelům mají povoleno v případech, při vyžadování, aby neměla používat konkrétní doporučení automatického ladění.
> Toto chování můžete vrátit tak, že přidáte tak, že vyberete možnost vrátit zpět zahození nepřijatá doporučení zpět na seznam doporučení.
> 

### <a name="enable-automatic-tuning"></a>Povolení automatického ladění
Můžete nastavit Azure SQL Database automaticky implementace doporučení. Doporučení jsou k dispozici, se automaticky použijí. Stejně jako u všech doporučení, spravováno službou, pokud je negativní dopad na výkon, doporučuje se vrátí zpět.

1. Na **doporučení** klikněte na **automatizace**:
   
    ![Nastavení služby Advisor](./media/sql-database-advisor-portal/settings.png)
2. Výběr akce k automatizaci:
   
    ![Doporučené indexy](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Pamatujte, že **DROP_INDEX** v tuto chvíli není kompatibilní s aplikací s použitím pomocné parametry přepnutí a index oddílu a v těchto případech by se neměla povolovat.
>

Jakmile vyberete požadovanou konfiguraci, klikněte na tlačítko použít.

### <a name="manually-run-the-recommended-t-sql-script"></a>Ruční spuštění doporučené skriptu T-SQL
Vyberte jakékoli doporučení a pak klikněte na tlačítko **zobrazit skript**. Tento skript spusťte proti databázi pro ruční použití doporučení.

*Indexy, které jsou spouštěny ručně nejsou monitorované a ověřené pro dopad na výkon pomocí služby* proto vám doporučujeme monitorovat tyto indexy po vytvoření ověřit jejich poskytnout zvýšení výkonu a upravit nebo odstranit v případě potřeby. Podrobnosti o vytváření indexů najdete v tématu [vytvoření indexu (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).

### <a name="canceling-recommendations"></a>Ruší se doporučení
Doporučení, která jsou v **čekající**, **ověřování**, nebo **úspěch** stav může být zrušen. Doporučení se stavem **zpracování** nelze zrušit.

1. Výběru doporučení v **ladění historie** oblasti, otevřete **podrobnosti** stránky.
2. Klikněte na tlačítko **zrušit** přerušit proces zavádění doporučení.

## <a name="monitoring-operations"></a>Operace sledování
Použití doporučení nemusí dojít okamžitě. Portál poskytuje podrobné informace o stavu doporučení. Toto jsou možné stavy, které v můžou být indexu:

| Status | Popis |
|:--- |:--- |
| Čekající na vyřízení |Použití doporučení příkaz byla přijata a je naplánováno na vykonání. |
| Provádění |Doporučení se zavádí. |
| Ověřování platnosti |Doporučení se úspěšně zavedlo a služba je měření výhody. |
| Úspěch |Doporučení se úspěšně zavedlo a měří výhody. |
| Chyba |Při zavádění doporučení se stala chyba. Může jít o přechodný problém, případně schéma změňte na tabulky a skript už není platný. |
| Probíhá vrácení |Doporučení se použil, ale bylo považováno za nenáročných a je automaticky vrací k uloženému. |
| Vráceno |Doporučení bylo vráceno zpět. |

Kliknutím na doporučení v procesu ze seznamu zobrazíte další informace:

![Doporučené indexy](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Vrácení zpět doporučení
Pokud jste použili doporučení ohledně výkonu pro jednotlivá doporučení použít (to znamená, že nebyl spuštěn ručně skriptu T-SQL), automaticky rozpojeného změnu Pokud najde negativní dopad na výkon. Pokud z nějakého důvodu chcete jednoduše vrátit zpět doporučení, máte následující:

1. Vyberte úspěšně zavedla doporučení v **historie optimalizace** oblasti.
2. Klikněte na tlačítko **vrácení** na **podrobnosti o doporučení** stránky.

![Doporučené indexy](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorování výkonu dopad doporučení indexu
Po dokončení doporučení se úspěšně implementace (v současné době operace indexování a parametrizovat dotazy doporučení pouze), můžete kliknout na **přehledy dotazů** na doporučení podrobností stránky otevřete [dotazu Informace o výkonu](sql-database-query-performance.md) a podívejte se, dopad na výkon vaše hlavní dotazy.

![Dopad na výkon monitorování](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Souhrn
Azure SQL Database poskytuje doporučení pro vylepšení výkonu SQL database. Tím, že poskytuje skripty T-SQL, získáte pomoc při optimalizaci databáze a nakonec zlepšení výkonu dotazů.

## <a name="next-steps"></a>Další postup
Monitorujte svá doporučení a pokračuje v používání jejich vylepšit výkon. Databázové úlohy jsou dynamická a neustále. Azure SQL Database dál sledovat a poskytovat doporučení, které mohou potenciálně tak vylepšit výkon vaší databáze. 

* Zobrazit [automatické ladění](sql-database-automatic-tuning.md) Další informace o automatickém ladění ve službě Azure SQL Database.
* Zobrazit [doporučení k výkonu](sql-database-advisor.md) přehled doporučení ohledně výkonu pro Azure SQL Database.
* Zobrazit [informace o výkonu dotazů](sql-database-query-performance.md) Další informace o zobrazení dopad na výkon vaše hlavní dotazy.

## <a name="additional-resources"></a>Další zdroje informací:
* [Query Store](https://msdn.microsoft.com/library/dn817826.aspx)
* [VYTVOŘENÍ INDEXU](https://msdn.microsoft.com/library/ms188783.aspx)
* [Řízení přístupu na základě rolí](../role-based-access-control/overview.md)

