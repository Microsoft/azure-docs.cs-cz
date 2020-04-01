---
title: 'Kurz: Integrace s power virtual agentem - QnA Maker'
description: V tomto kurzu zlepšete kvalitu znalostní báze s aktivním učením. Zkontrolujte, přijměte nebo zamítněte, přidejte bez odebrání nebo změny stávajících otázek.
ms.topic: tutorial
ms.date: 03/11/2020
ms.openlocfilehash: 283667c587e395a1d712f82f3385582b4c5c3227
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398874"
---
# <a name="tutorial-add-knowledge-base-to-power-virtual-agent"></a>Kurz: Přidání znalostní báze do virtuálního agenta power
Vytvořte a rozšiřte robota [Power Virtual Agent,](https://powervirtualagents.microsoft.com/) abyste poskytli odpovědi z vaší znalostní báze.

**V tomto kurzu se dozvíte, jak:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření virtuálního agenta napájení
> * Vytvořit záložní téma systému
> * Přidání QnA Makeru jako akce k tématu jako tok Power Automate
> * Vytvoření řešení Power Automate
> * Přidání toku power automatu do řešení
> * Publikování virtuálního agenta napájení
> * Test Power Virtual Agent, obdržíodpověď od znalostní báze QnA Maker

## <a name="integrate-a-power-virtual-agent-with-a-knowledge-base"></a>Integrace power virtual agenta se znalostní bází

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) umožňuje týmům snadno vytvářet výkonné roboty pomocí grafického rozhraní s průvodcem bez kódu bez potřeby datových vědců nebo vývojářů.

Virtuální agent napájení je vytvořen s řadou témat (oblastí předmětu), aby bylo možné odpovědět na otázky uživatelů prováděním akcí. Pokud nelze najít odpověď, záložní systém může vrátit odpověď.

Nakonfigurujte agenta tak, aby odeslal otázku do znalostní báze jako součást akce tématu nebo jako součást cesty k tématu **System Fallback.** Oba používají stejný mechanismus akce pro připojení k vaší znalostní bázi a vrátit odpověď.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate se připojuje k akci GenerateAnswer

Chcete-li připojit agenta ke znalostní bázi, vytvořte akci pomocí power automatu. Power Automate poskytuje **tok**procesů , který se připojuje k rozhraní GenerateAnswer API qnA makeru.

Jakmile je **tok** navržen a uložen, je k dispozici v řešení Power Automate **.**  Po přidání toku GenerateAnswer do řešení použijte toto řešení jako akci ve vašem agentovi.

## <a name="process-steps-to-connect-an-agent-to-your-knowledge-base"></a>Zpracovat kroky pro připojení agenta ke znalostní bázi

Následující kroky jsou uvedeny jako přehled, který vám pomůže pochopit, jak tyto kroky souvisejí s cílem připojení virtuálního agenta power-agenta ke znalostní bázi QnA Maker.

Postup použití power virtual agenta s QnA Makerem:
* Na portálu [QnA Maker](https://www.qnamaker.ai/)
    * Vytváření a publikování znalostní báze
    * Zkopírujte podrobnosti znalostní báze knowledge base včetně ID znalostní báze, klíče koncového bodu runtime a hostitele koncového bodu runtime.
* Na portálu [Virtuální agent napájení](https://powerva.microsoft.com/)
    * Téma sestavení agenta
    * Volání akce (pro power automate flow)
* Na portálu [Power Automate](https://us.flow.microsoft.com/)
    * Vytvoření toku s konektorem na [generateanswer qnA makeru](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)
        * QnA Maker publikoval informace o znalostní bázi
            * ID znalostní báze
            * Hostitel koncového bodu prostředku QnA Maker
            * Klíč koncového bodu prostředku QnA Maker
        * Vstup - uživatelský dotaz
        * Výstup - odpověď znalostní báze
    * Vytvoření řešení a přidání toku
* Návrat k virtuálnímu agentovi napájení
    * Vybrat výstup řešení jako zprávu pro téma

## <a name="create-and-publish-a-knowledge-base"></a>Vytvoření a publikování znalostní báze

1. Chcete-li vytvořit znalostní bázi, postupujte podle [rychlého startu.](../Quickstarts/create-publish-knowledge-base.md) Nevyplňuj poslední část a nevytvořte bota. Tento kurz je náhradou za poslední část rychlého startu, protože tento kurz používá virtuálního agenta napájení k vytvoření robota namísto robota Framework v rychlém startu.

    > [!div class="mx-imgBorder"]
    > ![Zadejte publikovaná nastavení znalostní báze, která najdete na stráncehttps://www.qnamaker.ai/) **Settings** na portálu [QnA Maker].(](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Tyto informace budete potřebovat pro [krok Power Automate](#create-power-automate-flow-to-connect-to-your-knowledge-base) ke konfiguraci připojení QnA Maker GenerateAnswer.

1. Na stránce **Nastavení** na portálu QnA Maker najdete klíč koncového bodu, hostitele koncového bodu a ID znalostní báze.

## <a name="create-power-virtual-agent"></a>Vytvoření virtuálního agenta napájení

1. [Přihlaste se ke](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409) virtuálnímu agentovi Power Virtual Agent pomocí školního nebo pracovního e-mailového účtu.
1. Pokud je toto váš první bot, měli byste být na **domovské** stránce agenta. Pokud se nejedná o váš první Power Virtual Agent, vyberte bot z pravé ho nahoře navigace a vyberte **+ New Bot**.

    > [!div class="mx-imgBorder"]
    > ![Zadejte publikovaná nastavení znalostní báze, která najdete na stráncehttps://www.qnamaker.ai/) **Settings** na portálu [QnA Maker].(](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="several-topics-are-provided-in-the-bot"></a>Několik témat jsou uvedeny v bot

Agent používá kolekci témat k zodpovězení otázek v oblasti předmětu. V tomto kurzu má agent mnoho témat, která jsou pro vás k dispozici, rozdělená do **témat uživatelských a** **systémových témat**.

> [!div class="mx-imgBorder"]
> ![Agent používá kolekci témat k zodpovězení otázek v oblasti předmětu. V tomto kurzu má agent mnoho témat rozdělených do témat **User Topics** a **System témata**.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-power-virtual-agents-system-fallback-topic"></a>Vytvořit záložní téma systému systému power virtuálního agenta

Zatímco agent se může připojit k vaší znalostní bázi z libovolného tématu, tento kurz používá téma **Záložní** systém. Záložní téma se používá, když agent nemůže najít odpověď. Agent předá text uživatele rozhraní API GenerateAnswer společnosti QnA Maker, obdrží odpověď z vaší znalostní báze a zobrazí ji zpět uživateli jako zprávu.

1. Na portálu [Power Virtual Agents](https://powerva.microsoft.com/#/) vyberte v pravém horním rohu navigace stránku **Nastavení.** Ikona pro tuto stránku je ozubené kolo. Vyberte **možnost Záložní systém**.

    > [!div class="mx-imgBorder"]
    > ![Power Virtual agent položka nabídky pro záložní systém](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. V rozbalovacím okně **Nastavení** vyberte **+ Přidat** a přidejte téma Záložní systém.

    > [!div class="mx-imgBorder"]
    > ![V okně Nastavení přidejte záložní téma.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Po přidání tématu vyberte **Přejít na téma Záložní** a vytvořte téma Fallback na vývojovém plátně.

    > [!TIP]
    > Pokud se potřebujete vrátit k tématu Fallback, je k dispozici v části **Témata** jako součást témat **systému.**

## <a name="use-authoring-canvas-to-add-an-action"></a>Přidání akce pomocí vytváření plátna

Pomocí základní stránky pro vytváření virtuálních agentů power propojte téma Záložní k vaší znalostní bázi. Téma začíná **nerozpoznaným textem uživatele**. Přidejte akci, která předá tento text QnA Makeru, a pak zobrazí odpověď jako zprávu. Poslední krok zobrazení odpovědi je zpracována jako [samostatný krok](#add-solutions-flow-to-power-virtual-agent) dále v tomto kurzu.

Tato část vytvoří tok konverzace záložní téma.

1. Nová akce Záložní již může mít prvky toku konverzace. Odstraňte položku **Eskalovat** výběrem nabídky Možnosti.

    > [!div class="mx-imgBorder"]
    > ![Zahájit záložní akci pomocí spouštěcích frází](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Vyberte **+** konektor tekoucí z pole **Zpráva** a pak vyberte **Volat akci**.

    > [!div class="mx-imgBorder"]
    > ![Volání akce](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Vyberte **Vytvořit tok**. Tento proces vás přenese na **power automate**, jiný portál založený na prohlížeči.

    > [!div class="mx-imgBorder"]
    > ![Volání akce](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-power-automate-flow-to-connect-to-your-knowledge-base"></a>Vytvoření power automatu Flow pro připojení ke své znalostní bázi

Následující postup vytvoří tok **power automatu,** který:
* přebírá text příchozího uživatele
* odešle jej qna makeru
* přiřadí nejvyšší odpověď qnA makeru proměnné
* odešle proměnnou (horní odpověď) jako odpověď zpět vašemu agentovi

1. V **power automatu**je **šablona toku** spuštěna za vás. V položce toku **virtuálních agentů napájení** vyberte **Upravit** a nakonfigurujte vstupní proměnnou přicházející od agenta do znalostní báze. Vstupní proměnná založená na textu je textová otázka odeslaná uživatelem od vašeho agenta.

    > [!div class="mx-imgBorder"]
    > ![Konfigurace vstupní proměnné jako textového řetězce](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Přidejte textový vstup a `InputText` pojmenujte `IncomingUserQuestion`proměnnou s popisem . Toto pojmenování pomáhá odlišit vstupní text od výstupního textu, který vytvoříte později.

    > [!div class="mx-imgBorder"]
    > ![Přidejte textový vstup a pojmenujte proměnnou InputText s popisem "UserQuestion"](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Vyberte **+** konektor tekoucí z pole **Virtuální agenti napájení,** chcete-li vložit nový krok do toku (před **návratovou hodnotu (hodnoty) do virtuálního agenta napájení**), pak vyberte Přidat **akci**.

1. Vyhledejte `Qna` akce **QnA Maker** a pak vyberte **Generovat odpověď**.

    > [!div class="mx-imgBorder"]
    > ![Vyhledejte akce "Qna", chcete-li najít akce **QnA Maker**, a pak vyberte možnost **Generovat odpověď**](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    Tři (3) požadovaná nastavení připojení pro QnA Maker se zobrazí v akci a nastavení otázky z virtuálního agenta napájení.

    > [!div class="mx-imgBorder"]
    > ![Nastavení připojení pro QnA Maker se zobrazí v akci.](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Nakonfigurujte akci pomocí ID znalostní báze, hostitele koncového bodu a klíče koncového bodu. Ty najdete na stránce **Nastavení** vaší znalostní báze na portálu QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Zadejte publikovaná nastavení znalostní báze, která najdete na stráncehttps://www.qnamaker.ai/) **Settings** na portálu [QnA Maker].(](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Chcete-li **nakonfigurovat otázku**, vyberte textové pole a vyberte `InputText` jej ze seznamu.

1. Chcete-li do toku vložit **+** nový krok, vyberte spojnici toku z pole Generovat akci **odpovědi** a pak vyberte **Přidat akci**.

1. Chcete-li přidat proměnnou pro zachycení textu odpovědi vráceného `Initialize variable` z funkce GenerateAnswer, vyhledejte a vyberte akci.

    Nastavte název proměnné na `OutgoingQnAAnswer`a vyberte typ jako **String**. Nenastavovat **hodnotu**.

    > [!div class="mx-imgBorder"]
    > ![Nastavte název proměnné na "QnAAnswer" a vyberte typ jako **String**](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Chcete-li do toku vložit **+** nový krok, vyberte spojnici tekoucí z pole **Akce inicializovat a** pak vyberte **Přidat akci**.

1. Chcete-li nastavit celou odpověď JSON znalostní báze na proměnnou, vyhledejte a vyberte`Apply to each` akci. Vyberte generateanswer `answers`.

1. Chcete-li vrátit pouze horní odpověď, v jednom **poli Použít pro každé** pole vyberte Přidat **akci**. Vyhledejte a vyberte **nastavit proměnnou**.

    V poli **Nastavit proměnnou** vyberte textové pole **pro Název**a ze seznamu vyberte **OutgoingQnAAnswer.**

    Vyberte textové pole pro **Hodnotu**a ze seznamu vyberte Odpovědět na **odpověď.**

    > [!div class="mx-imgBorder"]
    > ![Nastavení názvu a hodnoty proměnné](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Chcete-li proměnnou (a její hodnotu) vrátit, vyberte hodnotu Návrat na položku toku **virtuálního agenta napájení** a pak vyberte **Upravit**a **pak Přidat výstup**. Vyberte **textový** typ výstupu `FinalAnswer`a zadejte **název** souboru . Vyberte textové pole pro **hodnotu a**pak vyberte proměnnou. `OutgoingQnAAnswer`

    > [!div class="mx-imgBorder"]
    > ![Nastavení vrácené hodnoty](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Chcete-li tok uložit, vyberte **Uložit.**

## <a name="create-solution-and-add-flow"></a>Vytvoření řešení a přidání toku

Aby virtuální agent napájení mohl najít tok a připojit se k němu, musí být tok součástí řešení power automate.

1. Ještě na portálu Power Automate vyberte **řešení** z navigace na levé straně.

1. Vyberte **+ Nové řešení**.

1. Zadejte zobrazovaný název. Seznam řešení zahrnuje všechna řešení ve vaší organizaci nebo škole. Zvolte konvenci pojmenování, která vám pomůže filtrovat pouze `jondoe-power-virtual-agent-qnamaker-fallback`řešení, jako je předpona e-mailu na název řešení: .

1. Vyberte vydavatele ze seznamu voleb.

1. Přijměte výchozí hodnoty pro název a verzi.

1. Chcete-li proces dokončit, vyberte **vytvořit.**

## <a name="add-flow-to-solution"></a>Přidání toku do řešení

1. V seznamu řešení vyberte řešení, které jste právě vytvořili. Mělo by to být na prvním místě seznamu. Pokud tomu tak není, vyhledejte název e-mailu, který je součástí názvu řešení.

1. V řešení vyberte **+ Přidat existující**, pak vyberte **Tok** ze seznamu.

1. Najděte svůj tok a pak vyberte **Přidat** a dokončete proces. Pokud existuje mnoho toků, podívejte se na **změněný** sloupec najít nejnovější tok.

## <a name="add-solutions-flow-to-power-virtual-agent"></a>Přidání toku řešení do virtuálního agenta napájení

1. Vraťte se na kartu prohlížeče s vaším power virtual agentem. Authoring plátno by mělo být stále otevřené.

1. Vyberte **+** spojnici pod polem **Akce Zpráva,** chcete-li do toku vložit nový krok, a pak vyberte **Volat akci**.

1. V nové akci vyberte vstupní hodnotu **UnrecognizedTriggerPhrase**. To předá text z agenta toku.

    > [!div class="mx-imgBorder"]
    > ![V nové akci vyberte vstupní hodnotu **UnrecognizedTriggerPhrase**.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Vyberte **+** spojnici pod polem **Akce,** chcete-li do toku vložit nový krok, a pak vyberte **Zobrazit zprávu**.

1. Zadejte text `Your answer is:`zprávy a `FinalAnswer` vyberte jako kontextovou proměnnou pomocí funkce panelu nástrojů na místě.

    > [!div class="mx-imgBorder"]
    > ![Zadejte text zprávy a "FinalAnswer" z toku power automatu.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Vyberte **Uložit** z panelu nástrojů kontextu, chcete-li uložit detaily vývojového plátna pro téma.

Konečné plátno je uvedeno níže.

> [!div class="mx-imgBorder"]
> ![Plátno konečného agenta](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-power-virtual-agent"></a>Virtuální agent testovacího výkonu

1. V testovacím podokně přepněte **sledovat mezi tématy**. To vám umožní sledovat průběh mezi tématy, stejně jako v jednom tématu.

1. Otestujte agenta zadáním textu uživatele v pořadí uvedeném níže. Authoring canvas hlásí úspěšné kroky se zeleným zaškrtnutím.

|Pořadí otázek|Zkušební otázky|Účel|
|--|--|--|
|1|Dobrý den|Zahájit konverzaci|
|2|Hodiny obchodu|Ukázkové téma - nakonfigurováno pro vás bez jakékoli další práce z vaší strany.|
|3|Ano|V odpovědi na`Did that answer your question?`|
|4|Vynikající|V odpovědi na`Please rate your experience.`|
|5|Ano|V odpovědi na`Can I help with anything else?`|
|6|Co je znalostní báze?|Tato otázka spustí záložní akci, která odešle text do znalostní báze odpovědět, pak se zobrazí odpověď. |

> [!div class="mx-imgBorder"]
> ![Plátno konečného agenta](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Publikování svého robota

Chcete-li agenta zpřístupnit všem členům vaší školy nebo organizace, musíte jej publikovat.

1. Z navigace vlevo vyberte **Publikovat** a pak na stránce vyberte **Publikovat.**

1. Zkuste svého robota na demo webové stránky, za předpokladu, jako odkaz pod **tlačítkem Publikovat** .

    Otevře se nová webová stránka s vaším robotem. Zeptejte se bot stejnou zkušební otázku:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Plátno konečného agenta](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Podělte se o svého robota

Chcete-li sdílet demo webové stránky, nakonfigurujte jej jako kanál.

1. Z levé navigace vyberte **Spravovat** **kanály.**

1. Ze seznamu kanálů vyberte **Ukázkový web.**

1. Zkopírujte odkaz a vyberte **Uložit**. Vložte odkaz na demo web do e-mailu členům vaší školy nebo organizace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi s znalostní báze, odeberte prostředky QnA Maker na webu Azure Portal.

## <a name="next-step"></a>Další krok

[Získání analýz pro znalostní bázi](../How-To/get-analytics-knowledge-base.md)

Další informace:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) a [nastavení konektoru](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)