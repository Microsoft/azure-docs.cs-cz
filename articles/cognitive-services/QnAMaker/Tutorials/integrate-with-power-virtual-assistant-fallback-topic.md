---
title: 'Kurz: integrace s virtuálními agenty pro napájení – QnA Maker'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu Vylepšete kvalitu znalostní báze s aktivním učením. Přečtěte, přijměte nebo odmítněte nebo přidejte bez odebrání nebo změny existujících otázek.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81402814"
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
    * Vytvoření toku s konektorem pro [QnA maker GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * Informace o QnA Maker publikované znalostní báze Knowledge Base:
            * ID znalostní báze
            * Hostitel koncového bodu QnA Maker prostředku
            * Klíč koncového bodu prostředku QnA Maker
        * Dotaz Input-User
        * Výstup – odpověď základu znalostní báze
    * Vytvořte řešení a přidejte tok.
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

1. Zadejte publikovaná nastavení znalostní báze, která se nachází na stránce **Nastavení** na portálu [QnA maker](https://www.qnamaker.ai/) .

## <a name="topics-provided-in-the-bot"></a>Témata, která jsou součástí robota

Agent používá kolekci témat k zodpovězení otázek v oblasti vašeho předmětu. V tomto kurzu má agent pro vás mnoho témat, která jsou rozdělená na témata uživatelů a systémová témata.

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

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky záložního tématu Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. V okně **+** se **zprávou** vyberte tok konektoru a pak vyberte **zavolat akci**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s voláním akce](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Vyberte **Vytvořit tok**. Proces vás přesměruje na portál Power automatizováno.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s vytvořením toku](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Vytvoření toku automatizovaného procesu pro připojení k znalostní bázi

Následující postup vytvoří tok automatizovaného automatizace, který:
* Převezme text příchozího uživatele a pošle ho do QnA Maker.
* Přiřadí QnA Maker hlavní odpověď na proměnnou a pošle proměnnou (hlavní odpověď) jako odpověď zpět do vašeho agenta.

1. V **Power**automatu se pro vás spustí **Šablona Flow** . V položce toku **virtuálních agentů energie** vyberte **Upravit** . tím se nakonfiguruje vstupní proměnná přicházející od agenta k vaší znalostní bázi. Textová vstupní proměnná je textovou otázkou odeslanou uživatelem od vašeho agenta.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s možností automatizace pro konfiguraci vstupní proměnné jako textový řetězec](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Přidejte textové zadání a pojmenujte proměnnou `InputText`s popisem. `IncomingUserQuestion` Toto pojmenování pomáhá odlišit vstupní text z výstupního textu, který vytvoříte později.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s možností automatizace pro konfiguraci názvu a popisu vstupní proměnné](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Pokud chcete **+** vložit nový krok do toku (předtím, než se **vrátí hodnoty do virtuálního agenta napájení**), vyberte konektorový tok z pole **virtuální agenti napájení** . Pak vyberte **přidat akci**.

1. `Qna` Vyhledejte **QnA maker** akce a potom vyberte **generovat odpověď**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s vygenerováním odpovědi](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    Požadované nastavení připojení pro QnA Maker se zobrazí v akci a nastavení otázky z agenta.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s požadovaným nastavením připojení](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Nakonfigurujte akci pomocí ID vašeho znalostní báze, hostitele koncového bodu a klíče koncového bodu. Ty najdete na stránce **Nastavení** ve znalostní bázi na portálu QnA maker.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s publikovaným nastavením znalostní báze](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Chcete-li nakonfigurovat **otázku**, vyberte textové pole a potom vyberte `InputText` ze seznamu.

1. Pokud chcete do toku vložit nový krok, vyberte v poli **+** **generovat odpověď** postup spojnice. Pak vyberte **přidat akci**.

1. Chcete-li přidat proměnnou pro zachycení textu odpovědi vráceného `GenerateAnswer`z, vyhledejte a vyberte `Initialize variable` akci.

    Nastavte název proměnné na `OutgoingQnAAnswer`a vyberte typ jako **řetězec**. Nenastavte **hodnotu**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s inicializací výstupní proměnné](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Chcete-li vložit nový krok do toku, vyberte v **+** poli **inicializovat proměnnou** akci tok konektoru. Pak vyberte **přidat akci**.

1. Pokud chcete nastavit celou odpověď JSON znalostní báze na proměnnou, vyhledejte a vyberte`Apply to each` akci. `GenerateAnswer` `answers`Vyberte.

1. Chcete-li vrátit pouze hlavní odpověď, vyberte ve stejném poli **použít u každého** pole možnost **přidat akci**. Vyhledejte a vyberte možnost **nastavit proměnnou**.

    V poli **nastavit proměnnou** vyberte textové pole pro **název**a pak v seznamu vyberte **OutgoingQnAAnswer** .

    Vyberte textové pole pro **hodnotu**a potom v seznamu vyberte **odpovědi na odpověď** .

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s nastavením názvu a hodnoty pro proměnnou](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Chcete-li vrátit proměnnou (a její hodnotu), vyberte **návratové hodnoty do položky toku virtuálního agenta napájení** . Pak vyberte **Upravit** > **Přidat výstup**. Vyberte typ **textového** výstupu a potom zadejte **název** `FinalAnswer`. Vyberte textové pole pro **hodnotu**a pak vyberte `OutgoingQnAAnswer` proměnnou.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s nastavením návratové hodnoty](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Uložte tok výběrem možnosti **Uložit**.

## <a name="create-a-solution-and-add-the-flow"></a>Vytvoření řešení a přidání toku

Aby mohl agent vyhledat a připojit se ke toku, musí být tok součástí řešení Power automat.

1. Pořád na portálu Power Automata vyberte **řešení** z navigace na levé straně.

1. Vyberte **+ Nové řešení**.

1. Zadejte zobrazovaný název. Seznam řešení zahrnuje každé řešení ve vaší organizaci nebo ve škole. Vyberte zásady vytváření názvů, které vám pomohou filtrovat pouze vaše řešení. Můžete například do svého názvu řešení přidat svůj e-mail jako předponu: `jondoe-power-virtual-agent-qnamaker-fallback`.

1. Vyberte vydavatele ze seznamu možností.

1. Přijměte výchozí hodnoty pro název a verzi.

1. Pro dokončení procesu vyberte **vytvořit** .

## <a name="add-your-flow-to-the-solution"></a>Přidání toku do řešení

1. V seznamu řešení vyberte řešení, které jste právě vytvořili. Měl by být v horní části seznamu. Pokud ne, hledejte podle svého názvu e-mailu, který je součástí názvu řešení.

1. V řešení vyberte **+ Přidat existující**a v seznamu vyberte flow ( **tok** ).

1. Najděte svůj tok a potom ho vyberte **Přidat** a proces dokončete. Pokud existuje mnoho toků, podívejte se do **upraveného** sloupce a najděte nejnovější tok.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Přidání toku řešení do virtuálních agentů pro napájení

1. Vraťte se na kartu prohlížeče s vaším agentem v části Power Virtual Agents. Plátno pro tvorbu obsahu by se pořád otevřelo.

1. Pokud chcete vložit nový krok do toku, v poli Akce **zprávy** vyberte **+** konektor. Pak vyberte **zavolat akci**.

1. V nové akci vyberte vstupní hodnotu **UnrecognizedTriggerPhrase**. Tím se text z agenta předává do toku.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s možností virtuálních agentů Power Virtual pro výběr nerozpoznané fráze triggeru](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Pokud chcete vložit nový krok do toku, v poli **Akce** vyberte **+** konektor. Pak vyberte **Zobrazit zprávu**.

1. Zadejte text zprávy, `Your answer is:`. Vyberte `FinalAnswer` jako kontextovou proměnnou pomocí funkce místního panelu nástrojů.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s možností Power Virtual Agents k zadání textu zprávy](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Na panelu nástrojů kontextu vyberte **Uložit**a uložte podrobnosti plátna pro vytváření obsahu pro téma.

Vypadá to, že na plátně finálního agenta se líbí.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s finálním plátnem agenta](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Test agenta

1. V podokně test přepínejte **sledovat mezi tématy**. To vám umožňuje sledovat průběh mezi tématy a také v rámci jednoho tématu.

1. Otestujte agenta zadáním textu uživatele v následujícím pořadí. Plátno pro tvorbu sestav sestaví úspěšné kroky se zeleným znakem zaškrtnutí.

    |Pořadí otázek|Testovací otázky|Účel|
    |--|--|--|
    |1|Dobrý den|Spustit konverzaci|
    |2|Hodiny úložiště|Ukázkové téma. To je nakonfigurované pro vás bez jakékoli další práce na vaší straně.|
    |3|Ano|V reakci na`Did that answer your question?`|
    |4|Vynikající|V reakci na`Please rate your experience.`|
    |5|Ano|V reakci na`Can I help with anything else?`|
    |6|Co je znalostní báze?|Tato otázka spustí záložní akci, která odešle text do vaší znalostní báze na odpověď. Pak se zobrazí odpověď. |

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s finálním plátnem agenta](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Publikování robota

Pokud chcete, aby byl agent dostupný pro všechny členy školy nebo organizace, musíte ho publikovat.

1. Z navigace na levé straně vyberte **publikovat**. Pak na stránce vyberte **publikovat** .

1. Vyzkoušejte si bot na webu demo (podívejte se na odkaz v části **publikovat**).

    Otevře se nová webová stránka s vaším robotem. Zeptejte se na robota stejnou zkušební otázku:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s finálním plátnem agenta](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Sdílejte robota

Pokud chcete ukázkový web sdílet, nakonfigurujte ho jako kanál.

1. Z navigace na levé straně vyberte **Spravovat** > **kanály**.

1. V seznamu kanály vyberte **ukázkový web** .

1. Zkopírujte odkaz a vyberte **Uložit**. Vložte odkaz na ukázkový web do e-mailu pro členy školy nebo organizace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až se znalostní báze dokončí, odeberte prostředky QnA Maker v Azure Portal.

## <a name="next-step"></a>Další krok

[Získání analýz pro znalostní bázi](../How-To/get-analytics-knowledge-base.md)

Další informace:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [Konektor QnA maker](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) a [Nastavení konektoru](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)