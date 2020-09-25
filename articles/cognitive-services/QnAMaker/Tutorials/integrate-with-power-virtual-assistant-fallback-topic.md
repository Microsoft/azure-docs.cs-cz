---
title: 'Kurz: integrace s virtuálními agenty pro napájení – QnA Maker'
description: V tomto kurzu Vylepšete kvalitu znalostní báze s aktivním učením. Přečtěte, přijměte nebo odmítněte nebo přidejte bez odebrání nebo změny existujících otázek.
ms.topic: tutorial
ms.date: 06/08/2020
ms.openlocfilehash: 6d00833a1331ebca9b070d90f32ee30c56bd5f96
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319210"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Kurz: Přidání znalostní báze do virtuálních agentů pro napájení
Vytvořte a rozšíříte robota [Power Virtual Agents](https://powervirtualagents.microsoft.com/) , abyste získali odpovědi z vaší znalostní báze.

V tomto kurzu se naučíte:

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření robota virtuálních agentů
> * Vytvoření záložního tématu systému
> * Přidání QnA Maker jako akce do tématu jako interaktivní postup automatizace
> * Vytvoření řešení Power automatizuje
> * Přidejte do řešení tok automatizovaného napájení.
> * Publikování virtuálních agentů Power
> * Testovací virtuální agenti a příjem odpovědi z vaší QnA Maker znalostní báze Knowledge Base

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Integrace agenta se znalostní bázi

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) umožňuje týmům vytvářet výkonné roboty pomocí grafického rozhraní s asistencí bez kódu. Nepotřebujete odborníci na data ani vývojáře.

V části Power Virtual agenti vytvoříte agenta s řadou témat (oblastí předmětu), aby bylo možné zodpovědět otázky uživatelů pomocí akcí. Pokud odpověď nejde najít, může záložní systém vrátit odpověď.

Nakonfigurujte agenta tak, aby poslal otázku do vaší znalostní báze jako součást akce tématu, nebo jako součást cesty k části *záložního tématu systému* . Používají akci pro připojení ke znalostní bázi a vrácení odpovědi.

## <a name="power-automate-connects-to-generateanswer-action"></a>Zapnout automatické připojení k `GenerateAnswer` akci

Pokud chcete připojit svého agenta k znalostní bázi, použijte k vytvoření akce Power automat. Power automatizuje poskytuje tok procesu, který se připojuje k `GenerateAnswer` rozhraní QnA maker API.

Po návrh a uložení toku je tento tok dostupný z řešení Power Automate. Použijte toto řešení jako akci v agentovi.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Připojení agenta k znalostní bázi

Tady je přehled kroků připojení agenta v rámci virtuálních agentů k znalostní bázi ve službě QnA Maker.

* Na portálu [QnA maker](https://www.qnamaker.ai/) :
    * Sestavte a publikujte znalostní bázi.
    * Zkopírujte podrobnosti znalostní báze, včetně ID, klíče koncového bodu modulu runtime a hostitele koncového bodu modulu runtime.
* Na portálu [Power Virtual Agents](https://powerva.microsoft.com/) :
    * Sestavení tématu agenta.
    * Volání akce (pro automatizaci toku).
* Na portálu pro [automatizaci napájení](https://us.flow.microsoft.com/) :
    * Vyhledat _odpověď pro generování pomocí šablony QnA maker_
    * Pomocí šablony můžete nakonfigurovat tok pro použití [GenerateAnswer QnA maker](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * Informace o QnA Maker publikované znalostní báze Knowledge Base:
            * ID znalostní báze
            * Hostitel koncového bodu QnA Maker prostředku
            * Klíč koncového bodu prostředku QnA Maker
        * Dotaz Input-User
        * Výstup – odpověď základu znalostní báze
    * Vytvořte řešení a přidejte tok nebo přidejte tok do existujícího řešení.
* Vrátit se k virtuálním agentům pro napájení:
    * Vyberte výstup řešení jako zprávu pro téma.

## <a name="create-and-publish-a-knowledge-base"></a>Vytvoření a publikování znalostní báze

1. Postupujte podle pokynů v [rychlém](../Quickstarts/create-publish-knowledge-base.md) startu a vytvořte znalostní bázi. Neprovádějte poslední část s informacemi o vytváření robotů. Místo toho můžete pomocí tohoto kurzu vytvořit robota s virtuálními agenty.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s publikovaným nastavením znalostní báze](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Do svého publikovaného nastavení znalostní báze zadejte na stránce **Nastavení** na portálu [QnA maker](https://www.qnamaker.ai/) . Tyto informace budete potřebovat pro krok Automate k [Automatické](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) konfiguraci QnA Makerho `GenerateAnswer` připojení.

1. Na portálu QnA Maker na stránce **Nastavení** najděte klíč koncového bodu, hostitele koncového bodu a ID znalostní báze.

## <a name="create-an-agent-in-power-virtual-agents"></a>Vytvoření agenta ve virtuálních agentech Power

1. [Přihlaste se k Power Virtual agentům](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Použijte svůj školní nebo pracovní e-mailový účet.

1. Pokud je to vaše první robot, budete na **domovské** stránce agenta. Pokud se nejedná o první robot, vyberte robota v pravé horní části stránky a vyberte **+ nový robot**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky domovské stránky Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="topics-provided-in-the-bot"></a>Témata, která jsou součástí robota

Agent používá kolekci témat k zodpovězení otázek v oblasti vašeho předmětu. V tomto kurzu má agent pro vás mnoho témat, která jsou rozdělená na témata uživatelů a systémová témata.

V levém navigačním panelu můžete vybrat **témata** , která vám poskytnou témata od robota.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s tématy obsaženými v agentovi](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Vytvoření záložního tématu systému

I když se agent může připojit k vaší znalostní bázi z libovolného tématu, v tomto kurzu se používá *systémové záložní* téma. Náhradní téma se používá, když Agent nemůže najít odpověď. Agent předá text uživatele QnA Maker `GenerateAnswer` rozhraní API, obdrží odpověď ze znalostní báze a zobrazí ji uživateli jako zprávu.

1. Na portálu [Power Virtual Agents](https://powerva.microsoft.com/#/) v pravém horním rohu vyberte **Nastavení** (ikona ozubeného kolečka). Pak vyberte možnost **záložní systém**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s položkou nabídky virtuálních agentů napájení pro systém Fallback](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Vyberte **+ Přidat** a přidejte tak systémové záložní téma.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s přidáním záložního tématu](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Po přidání tématu vyberte **Přejít na záložní téma** a vytvořte záložní téma na plátně pro vytváření obsahu.

    > [!TIP]
    > Pokud se potřebujete vrátit k záložnímu tématu, je k dispozici v části **témata** v rámci **systémových** témat.

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Přidání akce pomocí plátna pro tvorbu

K připojení záložního tématu k vaší znalostní bázi použijte plátno pro vytváření virtuálních agentů Power Virtual. Téma začíná nerozpoznaným uživatelským textem. Přidejte akci, která předá tento text QnA Maker a pak zobrazí odpověď jako zprávu. Poslední krok zobrazení odpovědi se zpracovává jako [samostatný krok](#add-your-solutions-flow-to-power-virtual-agents)dále v tomto kurzu.

V této části se vytvoří postup při konverzaci v náhradním tématu.

1. Nová záložní akce možná již obsahuje prvky toku konverzace. Odstraňte položku **Eskalace** výběrem nabídky **Možnosti** .

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/delete-escalate-action-using-option-menu.png" alt-text="Částečný snímek obrazovky toku konverzace s zvýrazněnou možností odstranit":::

1. Vyberte **+** spojnici, která se má přesměrovat do pole se **zprávou** , a pak vyberte **zavolat akci**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png" alt-text="Částečný snímek obrazovky se voláním akce":::

1. Vyberte **Vytvořit tok**. Proces vás přesměruje na portál Power automatizováno.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s vytvořením toku](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)


    Power automatizuje se otevře v nové šabloně. Tuto novou šablonu nebudete používat.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-flow-initial-template.png" alt-text="Částečný snímek obrazovky automatizace s novou šablonou toku":::

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Vytvoření toku automatizovaného procesu pro připojení k znalostní bázi

Následující postup vytvoří tok automatizovaného automatizace, který:
* Převezme text příchozího uživatele a pošle ho do QnA Maker.
* Vrátí nejvyšší odpověď zpět k agentovi.

1. V **Power**automatu vyberte **šablony** z levé navigační části. Pokud se zobrazí dotaz, zda chcete opustit stránku prohlížeče, přijměte položku ponechat.

1. Na stránce šablony vyhledejte šablonu **generovat odpověď pomocí QnA maker** pak vyberte šablonu. Tato šablona obsahuje všechny kroky pro volání QnA Maker s nastavením znalostní báze a vrácení hlavní odpovědi.

1. Na nové obrazovce QnA Makerho toku vyberte **pokračovat**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-qna-flow-template-continue.png" alt-text="Částečný snímek obrazovky QnA Makerho toku šablony s zvýrazněným tlačítkem pokračovat":::

1. Zaškrtněte políčko **generovat odpověď** a zadejte nastavení QnA maker z předchozí části s názvem [Vytvoření a publikování znalostní báze](#create-and-publish-a-knowledge-base). **Hostitel služby** v následujícím obrázku odkazuje **na hostitele hostitele znalostní báze a má** formát `https://YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker` .


    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fill-in-generate-answer-settings.png" alt-text="Částečný snímek obrazovky QnA Makerho toku šablon se zvýrazněnou možností generovat odpověď (Preview).":::

1. Uložte tok výběrem možnosti **Uložit**.

## <a name="create-a-solution-and-add-the-flow"></a>Vytvoření řešení a přidání toku

Aby mohl agent vyhledat a připojit se ke toku, musí být tok součástí řešení Power automat.

1. Pořád na portálu Power Automata vyberte **řešení** z navigace na levé straně.

1. Vyberte **+ Nové řešení**.

1. Zadejte zobrazovaný název. Seznam řešení zahrnuje každé řešení ve vaší organizaci nebo ve škole. Vyberte zásady vytváření názvů, které vám pomohou filtrovat pouze vaše řešení. Můžete například do svého názvu řešení přidat svůj e-mail jako předponu: `jondoe-power-virtual-agent-qnamaker-fallback` .

1. Vyberte vydavatele ze seznamu možností.

1. Přijměte výchozí hodnoty pro název a verzi.

1. Pro dokončení procesu vyberte **vytvořit** .

## <a name="add-your-flow-to-the-solution"></a>Přidání toku do řešení

1. V seznamu řešení vyberte řešení, které jste právě vytvořili. Měl by být v horní části seznamu. Pokud ne, hledejte podle svého názvu e-mailu, který je součástí názvu řešení.

1. V řešení vyberte **+ Přidat existující**a v seznamu vyberte flow ( **tok** ).

1. Najděte svůj tok ze seznamu **vnější řešení** a pak vyberte **Přidat** a proces dokončete. Pokud existuje mnoho toků, podívejte se do **upraveného** sloupce a najděte nejnovější tok.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Přidání toku řešení do virtuálních agentů pro napájení

1. Vraťte se na kartu prohlížeče s vaším agentem v části Power Virtual Agents. Plátno pro tvorbu obsahu by se pořád otevřelo.

1. Pokud chcete vložit nový krok do toku, v poli Akce **zprávy** vyberte **+** konektor. Pak vyberte **zavolat akci**.

1. V automaticky otevíraném okně **Flow** vyberte nový tok s názvem **generovat odpovědi pomocí QnA maker znalostní báze...**. V toku se zobrazí nová akce.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-flow-after-adding-action.png" alt-text="Částečný snímek obrazovky s tématem konverzace o konverzaci s tématem virtuálního agenta po přidání QnA Makerho toku":::

1. Chcete-li správně nastavit vstupní proměnnou na akci QnA Maker, vyberte **možnost vybrat proměnnou**a pak vyberte **robot. UnrecognizedTriggerPhrase**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-selection-action-input.png" alt-text="Částečný snímek obrazovky témata konverzace na plátně pro virtuálním virtuálním agentem výběr vstupní proměnné":::


1. Chcete-li správně nastavit výstupní proměnnou na akci QnA Maker, v akci **zprávy** vyberte možnost **UnrecognizedTriggerPhrase**, vyberte ikonu pro vložení proměnné, `{x}` a pak vyberte možnost **FinalAnswer**.

1. Na panelu nástrojů kontextu vyberte **Uložit**a uložte podrobnosti plátna pro vytváření obsahu pro téma.

Vypadá to, že na plátně finálního agenta se líbí.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s koncovým plátnem pro agenty zobrazuje části s triggery, akce a zprávy.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Test agenta

1. V podokně test přepínejte **sledovat mezi tématy**. To vám umožňuje sledovat průběh mezi tématy a také v rámci jednoho tématu.

1. Otestujte agenta zadáním textu uživatele v následujícím pořadí. Plátno pro tvorbu sestav sestaví úspěšné kroky se zeleným znakem zaškrtnutí.

    |Pořadí otázek|Testovací otázky|Účel|
    |--|--|--|
    |1|Hello (Nazdar)|Spustit konverzaci|
    |2|Hodiny úložiště|Ukázkové téma. To je nakonfigurované pro vás bez jakékoli další práce na vaší straně.|
    |3|Yes|V reakci na `Did that answer your question?`|
    |4|Vynikající|V reakci na `Please rate your experience.`|
    |5|Yes|V reakci na `Can I help with anything else?`|
    |6|Jak můžu zlepšit výkon propustnosti pro dotaz předpovědi?|Tato otázka spustí záložní akci, která odešle text do vaší znalostní báze na odpověď. Pak se zobrazí odpověď. zelené značky zaškrtnutí u jednotlivých akcí označují úspěch pro každou akci.|

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png" alt-text="Snímek obrazovky s chatem s plátnem, který indikuje zelenou značku zaškrtnutí u úspěšných akcí":::

## <a name="publish-your-bot"></a>Publikování robota

Pokud chcete, aby byl agent dostupný pro všechny členy školy nebo organizace, musíte ho publikovat.

1. Z navigace na levé straně vyberte **publikovat**. Pak na stránce vyberte **publikovat** .

1. Vyzkoušejte si bot na webu demo (podívejte se na odkaz v části **publikovat**).

    Otevře se nová webová stránka s vaším robotem. Zeptejte se na robota stejnou zkušební otázku: `How can I improve the throughput performance for query predictions?`

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s finálním plátnem agenta](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Sdílejte robota

Pokud chcete ukázkový web sdílet, nakonfigurujte ho jako kanál.

1. Z navigace na levé straně vyberte **Spravovat**  >  **kanály**.

1. V seznamu kanály vyberte **ukázkový web** .

1. Zkopírujte odkaz a vyberte **Uložit**. Vložte odkaz na ukázkový web do e-mailu pro členy školy nebo organizace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až se znalostní báze dokončí, odeberte prostředky QnA Maker v Azure Portal.

## <a name="next-step"></a>Další krok

[Získání analýz pro znalostní bázi](../How-To/get-analytics-knowledge-base.md)

Přečtěte si další informace:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [Konektor QnA maker](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) a [Nastavení konektoru](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)