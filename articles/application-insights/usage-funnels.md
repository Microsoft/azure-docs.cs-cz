---
title: Azure Application Insights trychtýře
description: Zjistěte, jak můžete pomocí trychtýře ke zjištění, jak se zákazníky interagují s vaší aplikací.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: mbullwin
ms.openlocfilehash: 8478106fd68f6fcc65dff832b5cb27ca8db5f5bd
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093717"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Zjistit, jak zákazníci používají vaši aplikaci s Application Insights trychtýře

Principy prostředí pro zákazníky je naprosto pro vaši firmu. Pokud aplikace vyžaduje více fázích, je potřeba vědět, pokud postupují většina zákazníků provede celým procesem nebo jejich jsou ukončení procesu v určitém okamžiku. Jeho průběh prostřednictvím řady kroků ve webové aplikaci se označuje jako *trychtýřového grafu*. Můžete získat přehled o vašich uživatelů pomocí Azure Application Insights trychtýře a sledovat míry úspěšnosti krok za krokem. 

## <a name="create-your-funnel"></a>Vytvoření vašeho trychtýřového grafu
Před vytvořením svém trychtýři rozhodněte na otázku, kterou chcete odpovědět. Například můžete chtít vědět, kolik uživatelů se zobrazuje na domovské stránce Profil zákazníků zobrazení a vytváření lístku. V tomto příkladu vlastníci společnosti Fabrikam Fiber rádi procento zákazníky, kteří úspěšně vytvořit lístek zákazníka.

Tady jsou kroky, které přebírají vytvořit jejich trychtýřového grafu.

1. V nástroji Application Insights trychtýře vyberte **nový**.
1. Z **časový rozsah** rozevírací nabídky vyberte **posledních 90 dnů**. Vyberte buď **Moje trychtýře** nebo **sdílené trychtýře**.
1. Z **kroku 1** rozevíracího seznamu vyberte **Index**. 
1. Z **kroku 2** seznamu vyberte **zákazníka**.
1. Z **kroku 3** seznamu vyberte **vytvořit**.
1. Přidání názvu trychtýřového grafu a výběr **Uložit**.

Následující snímek obrazovky ukazuje že příklad, jaká data nástroj trychtýře generuje. Vlastníci společnosti Fabrikam vidíme, že během posledních 90 dnů, 54.3 procent společností z žebříčku svým zákazníkům, kteří navštívili na domovské stránce vytvořit lístek zákazníka. Můžete také zjistit, že 2,700 svým zákazníkům přišel do indexu z domovské stránky. To může znamenat problém s aktualizací zobrazení.


![Nástroje trychtýře – snímek obrazovky s daty](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnels-features"></a>Funkce trychtýře
Na předchozím snímku obrazovky obsahuje pět zvýrazněné oblasti. Toto jsou funkce trychtýře. Následující seznam obsahuje další informace o každé odpovídající oblasti na snímku obrazovky:
1. Pokud vaše aplikace vede, zobrazí se banner vzorkování. Vyberte informační zprávu se otevře podokno kontextu, s vysvětlením, jak vypnout vzorkování. 
2. Můžete exportovat na svém trychtýři [Power BI](app-insights-export-power-bi.md).
3. Vyberte krok, abyste zobrazili další podrobnosti na pravé straně. 
4. Historická konverze graf ukazuje míry úspěšnosti za posledních 90 dní. 
5. Uživatelům lepší pochopení díky přístupu do nástroj Uživatelé. Pomocí filtrů v každém kroku. 

## <a name="next-steps"></a>Další postup
  * [Přehled využití](app-insights-usage-overview.md)
  * [Uživatelé, relace a události](app-insights-usage-segmentation.md)
  * [Uchování](app-insights-usage-retention.md)
  * [Workbooks](app-insights-usage-workbooks.md)
  * [Přidat kontext uživatele](app-insights-usage-send-user-context.md)
  * [Export do Power BI](app-insights-export-power-bi.md)

