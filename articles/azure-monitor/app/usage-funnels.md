---
title: Cesty azure application insights
description: Zjistěte, jak můžete pomocí trychtýřů zjistit, jak zákazníci s vaší aplikací komunikují.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 89440a6385bab5b917a866b686e8d2ba828c92e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671048"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Zjistěte, jak zákazníci používají vaši aplikaci pomocí trychtýřů Application Insights

Pochopení zkušeností zákazníků je pro vaše podnikání velmi důležité. Pokud vaše aplikace zahrnuje více fází, musíte vědět, zda většina zákazníků postupuje celým procesem nebo pokud proces v určitém okamžiku ukončují. Postup prostřednictvím řady kroků ve webové aplikaci se označuje jako *trychtýř*. Pomocí cest Azure Application Insights můžete získat přehled o uživatelích a sledovat míru konverze krok za krokem. 

## <a name="create-your-funnel"></a>Vytvoření cesty
Před vytvořením cesty se rozhodněte pro otázku, na kterou chcete odpovědět. Můžete například chtít vědět, kolik uživatelů si prohlíží domovskou stránku, prohlíží profil zákazníka a vytváří lístek. V tomto příkladu chtějí vlastníci společnosti Fabrikam Fiber znát procento zákazníků, kteří úspěšně vytvořili lístek zákazníka.

Zde jsou kroky, které podniknou k vytvoření cesty.

1. V nástroji Cesty Přehledy aplikací vyberte **Nový**.
1. V rozevírací nabídce **Časový rozsah** vyberte **Posledních 90 dní**. Vyberte **Možnost Moje cesty** nebo Sdílené **cesty**.
1. V rozevíracím seznamu **Krok 1** vyberte **Index**. 
1. V seznamu **Krok 2** vyberte **zákazníka**.
1. V seznamu **Krok 3** vyberte **Vytvořit**.
1. Přidejte název do trychtýře a vyberte **Uložit**.

Následující snímek obrazovky ukazuje příklad druhu dat, která nástroj Cesty generuje. Majitelé společnosti Fabrikam mohou vidět, že během posledních 90 dnů 54,3 procenta jejich zákazníků, kteří navštívili domovskou stránku, vytvořilo lístek pro zákazníky. Mohou také vidět, že 2700 svých zákazníků přišlo do indexu z domovské stránky. To může znamenat problém s aktualizací.


![Snímek obrazovky s nástrojem Cesty s daty](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Funkce trychtýřů
Předchozí snímek obrazovky obsahuje pět zvýrazněných oblastí. To jsou vlastnosti trychtýřů. Následující seznam vysvětluje více o každé odpovídající oblasti na snímku obrazovky:
1. Pokud je vaše aplikace ukázková, zobrazí se ukázkový nápis. Výběrem nápisu se otevře podokno kontextu s vysvětlením, jak vzorkování vypnout. 
2. Cestu můžete exportovat do [Power BI](../../azure-monitor/app/export-power-bi.md ).
3. Výběrem kroku zobrazíte další podrobnosti vpravo. 
4. Historický graf konverze zobrazuje míry konverze za posledních 90 dní. 
5. Pochopte své uživatele lépe tím, že přístup k nástroji pro uživatele. Filtry můžete použít v každém kroku. 

## <a name="next-steps"></a>Další kroky
  * [Přehled použití](usage-overview.md)
  * [Uživatelé, relace a události](usage-segmentation.md)
  * [Uchovávání](usage-retention.md)
  * [Workbooks](../../azure-monitor/app/usage-workbooks.md)
  * [Přidání kontextu uživatele](usage-send-user-context.md)
  * [Export do Power BI](../../azure-monitor/app/export-power-bi.md )

