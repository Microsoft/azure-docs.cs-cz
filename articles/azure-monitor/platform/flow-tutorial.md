---
title: Automatizace Azure Monitorch procesů protokolu pomocí Microsoft Flow
description: Naučte se, jak můžete použít Microsoft Flow k rychlé automatizaci opakujících se procesů pomocí konektoru Azure Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/29/2017
ms.openlocfilehash: a6097d38d3335be356ca75f5a9d0eadeed414b03
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166948"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Automatizace Azure Monitorch procesů protokolu pomocí konektoru pro Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) umožňuje vytvářet automatizované pracovní postupy s využitím stovek akcí pro nejrůznější služby. Výstup z jedné akce lze použít jako vstup do jiného, což vám umožní vytvořit integraci mezi různými službami.  Konektor služby Azure Log Analytics pro Microsoft Flow umožňuje sestavovat pracovní postupy, které zahrnují data načtená pomocí dotazů protokolu z pracovního prostoru Log Analytics v Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Pomocí Microsoft Flow můžete například použít data protokolu Azure Monitor v e-mailovém oznámení ze sady Office 365, vytvořit chybu v Azure DevOps nebo odeslat zprávu o časové rezervě.  Pracovní postup můžete aktivovat jednoduchým plánem nebo z nějaké akce v připojené službě, jako je například doručení e-mailu nebo.  

V tomto článku se dozvíte, jak vytvořit tok, který automaticky pošle výsledky Azure Monitorového dotazu protokolu e-mailem, jenom jeden příklad, jak můžete použít konektor Log Analytics v Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Krok 1: vytvoření toku
1. Přihlaste se k [Microsoft Flow](https://flow.microsoft.com)a vyberte **Moje toky**.
2. Klikněte na **+ vytvořit z prázdné**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Krok 2: vytvoření triggeru pro váš tok
1. Klikněte na **Prohledat stovky konektorů a triggerů**.
2. Do vyhledávacího pole zadejte **Schedule** .
3. Vyberte **plán**a pak vyberte **plán – opakování**.
4. V poli **frekvence** vyberte **den** a v poli **interval** zadejte **1**.<br><br>Dialogové okno aktivační události Microsoft Flow ![](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Krok 3: Přidání akce Log Analytics
1. Klikněte na **+ Nový krok**a pak klikněte na **přidat akci**.
2. Vyhledejte **Log Analytics**.
3. Klikněte na **Azure Log Analytics – spusťte dotaz a vizualizujte výsledky**.<br><br>![Log Analytics spuštění okna dotazu](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Krok 4: Konfigurace akce Log Analytics

1. Zadejte podrobnosti pro váš pracovní prostor včetně ID předplatného, skupiny prostředků a názvu pracovního prostoru.
2. Do okna **dotazu** přidejte následující dotaz protokolu.  Toto je pouze ukázkový dotaz a lze jej nahradit jakýmkoli jiným, který vrací data.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. Vyberte **tabulku HTML** pro **typ grafu**.<br><br>akce Log Analytics ![](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Krok 5: Konfigurace toku pro odesílání e-mailů

1. Klikněte na **Nový krok**a potom klikněte na **+ přidat akci**.
2. Vyhledejte **Office 365 Outlook**.
3. Klikněte na **Office 365 Outlook – poslat e-mail**.<br><br>![okno pro výběr](media/flow-tutorial/flow04.png) Office 365 Outlook

4. Zadejte e-mailovou adresu příjemce v okně **Komu** a předmět e-mailu v **předmětu**.
5. Klikněte kamkoli do pole **text** .  Otevře se okno **dynamického obsahu** s hodnotami z předchozích akcí.  
6. Vyberte **text**.  Toto je výsledky dotazu v akci Log Analytics.
6. Klikněte na **Zobrazit upřesňující možnosti**.
7. V poli **je HTML** vyberte **Ano**.<br><br>![okno Konfigurace e-mailu Office 365](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Krok 6: uložení a testování toku
1. Do pole **název toku** přidejte název vašeho toku a potom klikněte na **vytvořit tok**.<br><br>![Uložit tok](media/flow-tutorial/flow06.png)
2. Tok se teď vytvoří a spustí se po dni, který jste zadali. 
3. Pokud chcete tok hned otestovat, klikněte na **Spustit** a pak na **Spustit tok**.<br><br>![spuštění](media/flow-tutorial/flow07.png) toku
3. Po dokončení toku Ověřte e-mailovou adresu příjemce, kterého jste zadali.  Měli byste obdržet e-mail s textem podobným následujícímu:<br><br>![Ukázkový e-mail](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [dotazech protokolu v Azure monitor](../log-query/log-query-overview.md).
- Přečtěte si další informace o [Microsoft Flow](https://ms.flow.microsoft.com).


