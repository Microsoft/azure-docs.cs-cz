---
title: Plány údržby Azure (preview) | Dokumentace Microsoftu
description: Plánování údržby umožňuje zákazníkům naplánovat nezbytné plánované údržby události služby Azure SQL Data warehouse používá k zavedení nových funkcí, upgrady a opravy.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/07/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a6eedc0bac7aab69a9138f4f63d0d9d802e74dfc
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228304"
---
# <a name="change-a-maintenance-schedule"></a>Změnit plán údržby 

## <a name="portal"></a>Portál
Plán údržby můžete aktualizovat nebo kdykoli změnit. Ale pokud vybraná instance je aktuálně probíhá cyklus údržby aktivní nastavení bude uložen a pouze aktivuje během dalšího období údržby identifikovaný. [Další informace](https://docs.microsoft.com/azure/service-health/resource-health-overview) o monitorování služby data warehouse během události údržby aktivní. 

Ve verzi preview jsme se se žádostí o vyberte dva časová období údržby v období 7 dní. Každé časové období údržby může být v rozmezí 3 až 8 hodin, 3 hodin aktuálně prováděné nejkratší k dispozici možnost. Údržba může probíhat kdykoli v rámci zjištěné údržbu, ale nedojde ven z nich označen předchozí oznámení časových oken, můžete se také prostředí být stručný ke ztrátě připojení, jak služba nasadí nový kód k vašim datům sklad. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Určení primárního a sekundárního systému windows

Primární a sekundární windows musí být určen v rámci samostatné rozsahů (to znamená, že primární okno (úterý – Thursday), sekundární okno (sobota – neděle)

Proveďte následující kroky, chcete-li změnit plán údržby, která byla použita k vašemu datovému skladu portálu.
1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2.  Vyberte datový sklad, který chcete aktualizovat. Na stránce se otevře v okně přehledu. 
3.  Na stránce nastavení plánu údržby je možný buď kliknutím na údržbu plán (preview) souhrn odkaz v okně Přehled, nebo prostřednictvím možnost plánu údržby v levé nabídce prostředků.  

    ![Přehled okno Možnosti](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Rozmezí dnů upřednostňované můžete identifikovat pro vaše primární údržbu pomocí přepínačů v horní části stránky. Tento výběr určuje, pokud primární okno dojde na jeden den v týdnu nebo přes víkend pokazil. Váš výběr aktualizuje níže uvedené hodnoty rozevíracího seznamu. Během období preview nemusí podporovat některé oblasti ještě úplnou sadu dostupných možností den. Tyto hodnoty se aktualizace v nadcházejících měsících.

   ![Okno nastavení údržby](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Vyberte upřednostňovaný primární a sekundární údržby windows pomocí den, počáteční čas a umístění okna čas níže. Plán shrnutí v dolní části okna se aktualizace na základě rozevírací seznam hodnot vybrané.

#### <a name="dropdown-options"></a>Rozevírací seznam možností
- Den: Upřednostňovaný den provádění údržby během vybraného časového období.
- Čas zahájení: upřednostňované počáteční časového období údržby.
- Časový interval: upřednostňované dobu trvání časového intervalu.

  Po výběru Upřednostňované časové intervaly, klikněte na Uložit. Potvrzovací zpráva se zobrazí potvrzení, že se že nový plán není aktivní. Pokud ukládáte plánu v oblasti, která zatím nepodporuje plánování údržby, se zobrazí následující zpráva. Nastavení se uloží a aktivuje, jakmile tato funkce je k dispozici ve vybrané oblasti.    

    ![Neaktivní v oblasti informačních zpráv](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Další postup
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) akce Webhooku pro pravidla upozornění protokolů.
- [Další informace](https://docs.microsoft.com/azure/service-health/service-health-overview) o Azure Service Health


