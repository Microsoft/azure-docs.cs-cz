---
title: Automatizovat procesy analýzy protokolů Azure s Flow Microsoft
description: Zjistěte, jak Microsoft Flow můžete rychle automatizovat opakované procesy pomocí konektoru Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
ms.service: log-analytics
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: bwren
ms.component: na
ms.openlocfilehash: 21cf7cf3d12902b02fcbf650a1623e78004d28b4
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131696"
---
# <a name="automate-log-analytics-processes-with-the-connector-for-microsoft-flow"></a>Automatizovat procesy analýzy protokolů pomocí konektoru pro Flow Microsoft
[Microsoft Flow](https://ms.flow.microsoft.com) vám umožní vytvořit automatizované pracovní postupy pomocí stovky akce pro celou řadu služeb. Výstup z jednu akci je možné použít jako vstup pro jiné umožňuje vytvářet integrace mezi různými službami.  Konektor analýzy protokolů Azure pro Microsoft Flow umožňují vytvářet pracovní postupy, které zahrnují data načtená při prohledávání protokolu v analýzy protokolů.

Například můžete použít Microsoft Flow používat data analýzy protokolů e-mailem oznámení z Office 365, vytvoření chyby ve Visual Studio Team Services, nebo příspěvek ve Slack.  Při jednoduchého plánu, nebo některá z akcí v připojené služby, jako je při doručení e-mailu nebo tweet, můžete aktivovat pracovní postup.  

Tento kurz v tomto článku se dozvíte, jak vytvořit toku, který automaticky odesílá výsledky vyhledávání protokolu analýzy protokolů e-mailem, pouze příklad, jak můžete použít analýzy protokolů v Flow Microsoft. 


## <a name="step-1-create-a-flow"></a>Krok 1: Vytvoření toku
1. Přihlaste se k [Microsoft Flow](http://flow.microsoft.com)a vyberte **Moje toků**.
2. Klikněte na tlačítko **+ vytvořit z prázdné**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Krok 2: Vytvoření aktivační událost pro vaše tok
1. Klikněte na tlačítko **vyhledávání stovky konektory a aktivační události**.
2. Typ **plán** do vyhledávacího pole.
3. Vyberte **plán**a potom vyberte **plán - opakování**.
4. V **frekvence** zaškrtněte **den** a v **Interval** zadejte **1**.<br><br>![Dialogové okno Microsoft Flow aktivační události](media/log-analytics-flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Krok 3: Přidání akce analýzy protokolů
1. Klikněte na tlačítko **+ nový krok**a potom klikněte na **přidat akci**.
2. Vyhledejte **protokolu analýzy**.
3. Klikněte na tlačítko **Azure Log Analytics – spuštění dotazu a vizualizace výsledků**.<br><br>![Spusťte okno dotazu analýzy protokolů](media/log-analytics-flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Krok 4: Konfigurace analýzy protokolů akce

1. Zadejte podrobnosti pro pracovní prostor, včetně ID předplatné, skupinu prostředků a název pracovního prostoru.
2. Přidejte následující dotaz analýzy protokolů, který **dotazu** okno.  Toto je ukázkový dotaz a můžete nahradit všechny jiné, vrací data.
```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
```

2. Vyberte **tabulky HTML** pro **typ grafu**.<br><br>![Akce analýzy protokolů](media/log-analytics-flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Krok 5: Konfigurace toku k odeslání e-mailu

1. Klikněte na tlačítko **nový krok**a potom klikněte na **+ přidat akci**.
2. Vyhledejte **Office 365 Outlook**.
3. Klikněte na tlačítko **Office 365 Outlook – e-mailu**.<br><br>![Okno Výběr Outlook Office 365](media/log-analytics-flow-tutorial/flow04.png)

4. Zadejte e-mailová adresa příjemce v **k** okno a předmět e-mailu v **subjektu**.
5. Klikněte kamkoli do **textu** pole.  A **dynamický obsah** otevře se okno s hodnotami z předchozí akce.  
6. Vyberte **textu**.  Toto je výsledky dotazu v akci pro analýzy protokolů.
6. Klikněte na tlačítko **zobrazit rozšířené možnosti**.
7. V **je HTML** vyberte **Ano**.<br><br>![Okno Konfigurace e-mailu Office 365](media/log-analytics-flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Krok 6: Uložit a testování vaší toku
1. V **toku název** pole, přidejte název vaší toku a pak klikněte na tlačítko **vytvořit toku**.<br><br>![Uložit toku](media/log-analytics-flow-tutorial/flow06.png)
2. Tok teď se vytvoří a spustí za den, což je plán, který jste zadali. 
3. Chcete-li hned otestovat toku, klikněte na tlačítko **spustit nyní** a potom **spustit toku**.<br><br>![Spustit toku](media/log-analytics-flow-tutorial/flow07.png)
3. Po dokončení toku zkontrolujte e-mailu příjemce, který jste zadali.  Byste měli obdržet e-mail se text podobný následujícímu:<br><br>![Ukázkového e-mailu](media/log-analytics-flow-tutorial/flow08.png)


## <a name="next-steps"></a>Další postup

- Další informace o [přihlásit analýzy protokolů hledání](log-analytics-log-search-new.md).
- Další informace o [Microsoft Flow](https://ms.flow.microsoft.com).



