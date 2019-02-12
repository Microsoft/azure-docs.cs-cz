---
title: Automatizace procesů Azure Monitor protokolu pomocí Microsoft Flow
description: Zjistěte, jak můžete pomocí Microsoft Flow k automatizaci rychle opakovatelnou procesů pomocí konektoru služby Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
ms.service: log-analytics
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: f38d9b40143391be34ce5f72627720e2f5119dc6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993771"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Automatizace procesů Azure Monitor protokolu pomocí konektoru pro Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) můžete vytvářet automatizované pracovní postupy pomocí stovek akce pro širokou škálu služeb. Výstup z jedné akce můžete použít jako vstup do druhé umožňuje vytvářet integrace mezi různými službami.  Azure Log Analytics connector pro Microsoft Flow umožňují vytvářet pracovní postupy, které obsahují data načíst pomocí dotazů na protokoly z pracovního prostoru Log Analytics ve službě Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Například můžete použít Microsoft Flow používat LAzure monitorování protokolu data v e-mailové oznámení z Office 365, vytvořit chybu ve službě Azure DevOps nebo publikovat zprávu Slack.  Pracovní postup můžete aktivovat podle jednoduchého plánu nebo z některé akce propojené služby, například při přijetí e-mailu nebo tweet.  

Kurz v tomto článku se dozvíte, jak vytvořit tok, který automaticky odesílá výsledky dotazu protokolu Azure Monitor e-mailem, pouze jeden příklad použití konektoru Log Analytics v Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Krok 1: Vytvořit tok
1. Přihlaste se k [Microsoft Flow](https://flow.microsoft.com)a vyberte **Moje toky**.
2. Klikněte na tlačítko **+ vytvořit z prázdné**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Krok 2: Vytvoření aktivační události pro váš tok
1. Klikněte na tlačítko **prohledejte stovky konektorů a aktivačních událostí**.
2. Typ **plán** do vyhledávacího pole.
3. Vyberte **plán**a pak vyberte **plán – opakování**.
4. V **frekvence** pole vyberte **den** a **Interval** zadejte **1**.<br><br>![Dialogové okno aktivační události Microsoft Flow](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Krok 3: Přidejte akci Log Analytics
1. Klikněte na tlačítko **+ nový krok**a potom klikněte na tlačítko **přidat akci**.
2. Vyhledejte **Log Analytics**.
3. Klikněte na tlačítko **Azure Log Analytics – spustit dotaz a vizualizaci výsledků**.<br><br>![Spusťte okno dotazu log Analytics](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Krok 4: Nakonfigurujte akci, která Log Analytics

1. Zadejte podrobnosti pro váš pracovní prostor, včetně ID předplatného, skupinu prostředků a název pracovního prostoru.
2. Přidejte následující dotaz protokolu, který **dotazu** okna.  Toto je ukázkový dotaz a můžete nahradit všechny jiné, které vrací data.
```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
```

2. Vyberte **tabulku HTML** pro **typ grafu**.<br><br>![Log Analytics akce](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Krok 5: Nakonfigurujte tok poslání e-mailu

1. Klikněte na tlačítko **nový krok**a potom klikněte na tlačítko **+ přidat akci**.
2. Vyhledejte **Office 365 Outlook**.
3. Klikněte na tlačítko **Office 365 Outlook – odeslat e-mail**.<br><br>![Okno pro výběr Office 365 Outlooku](media/flow-tutorial/flow04.png)

4. Zadejte e-mailová adresa příjemce v **k** okno a předmět e-mailu v **subjektu**.
5. Klikněte kamkoli do **tělo** pole.  A **dynamický obsah** otevře se okno s hodnotami z předchozích akcí.  
6. Vyberte **tělo**.  Toto je výsledky dotazu v akci Log Analytics.
6. Klikněte na tlačítko **zobrazit pokročilé možnosti**.
7. V **je HTML** vyberte **Ano**.<br><br>![Okno Konfigurace e-mailu Office 365](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Krok 6: Uložit a testování vašeho toku
1. V **název toku** pole, přidat pro nový tok název a potom klikněte na tlačítko **vytvořit tok**.<br><br>![Uložit tok](media/flow-tutorial/flow06.png)
2. Tok se teď vytvoří a spustí za den, což je plán, který jste zadali. 
3. Pokud chcete okamžitě testování tohoto toku, klikněte na tlačítko **Run Now** a potom **spustit tok**.<br><br>![Spustit tok](media/flow-tutorial/flow07.png)
3. Po dokončení toku zaškrtněte příjemce, který jste zadali e-mailu.  Byste měli obdržet e-mail s tělem podobný následujícímu:<br><br>![Ukázka e-mailu](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>Další postup

- Další informace o [protokolu dotazů ve službě Azure Monitor](../log-query/log-query-overview.md).
- Další informace o [Microsoft Flow](https://ms.flow.microsoft.com).



