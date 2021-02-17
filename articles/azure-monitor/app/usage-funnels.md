---
title: Trychtýře Azure Application Insights
description: Naučte se, jak můžete použít trychtýře k zjištění, jak zákazníci pracují s vaší aplikací.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: d0e5cb5097a2f8833b10fec16a99b67c5c109fc8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583463"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Zjistěte, jak zákazníci používají vaši aplikaci s Application Insights trychtýři.

Porozumění prostředí pro zákazníky má nejvyšší význam pro vaši firmu. Pokud vaše aplikace zahrnuje několik fází, je třeba zjistit, zda většina zákazníků projde celým procesem, nebo pokud se proces ukončuje v určitém bodě. Průběh v rámci řady kroků ve webové aplikaci se označuje jako *trychtýřový*. Pomocí trychtýřů Azure Application Insights můžete získat přehled o vašich uživatelích a monitorovat podrobné přepočítací převody. 

## <a name="create-your-funnel"></a>Vytvoření trychtýře
Před vytvořením trychtýře se rozhodněte na otázku, na kterou chcete odpovědět. Například můžete chtít zjistit, kolik uživatelů zobrazuje domovskou stránku, Zobrazit profil zákazníka a vytvořit lístek. V tomto příkladu si vlastníci společnosti Fabrikam Fiber Company chtějí zjistit procento zákazníků, kteří úspěšně vytvořili lístek zákazníka.

Tady jsou kroky, které jsou potřeba k vytvoření trychtýře.

1. V nástroji Application Insights trychtýře vyberte **Nový**.
1. V rozevírací nabídce **časový rozsah** vyberte **posledních 90 dní**. Vyberte buď **Moje trychtýře** nebo **sdílené trychtýře**.
1. V rozevíracím seznamu **Krok 1** vyberte možnost **index**. 
1. V seznamu **2. krok** vyberte možnost **Zákazník**.
1. V seznamu **3. krok** vyberte **vytvořit**.
1. Přidejte do trychtýře název a vyberte **Uložit**.

Následující snímek obrazovky ukazuje příklad druhu dat, které nástroj trychtýře vygeneruje. Vlastníci společnosti Fabrikam uvidí, že během posledních 90 dnů 54,3 procento svých zákazníků, kteří navštívili domovskou stránku, vytvořili lístek zákazníka. Můžou také vidět, že 2 700 jejich zákazníků z domovské stránky pocházejí do indexu. To může znamenat problém s aktualizací.


![Snímek obrazovky s nástrojem trychtýře s daty](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Funkce trychtýře
Předchozí snímek obrazovky obsahuje pět zvýrazněných oblastí. Jedná se o funkce trychtýřů. Následující seznam vysvětluje více o všech odpovídajících oblastech na snímku obrazovky:
1. Pokud je vaše aplikace Navzorkovaná, zobrazí se nápis vzorkování. Výběrem nápisu se otevře podokno kontextu, které vysvětluje, jak zapnout vzorkování. 
2. Filtr můžete vyexportovat do [Power BI](./export-power-bi.md).
3. Kliknutím na krok zobrazíte další podrobnosti na pravé straně. 
4. Graf s historickým převodem zobrazuje míry převodu za posledních 90 dní. 
5. Přístup k nástroji pro uživatele vám umožní lépe pochopit uživatele. V každém kroku můžete použít filtry. 

## <a name="next-steps"></a>Další kroky
  * [Přehled využití](usage-overview.md)
  * [Uživatelé, relace a události](usage-segmentation.md)
  * [Toalet](usage-retention.md)
  * [Workbooks](../visualize/workbooks-overview.md)
  * [Přidat kontext uživatele](usage-send-user-context.md)
  * [Export do Power BI](./export-power-bi.md)

