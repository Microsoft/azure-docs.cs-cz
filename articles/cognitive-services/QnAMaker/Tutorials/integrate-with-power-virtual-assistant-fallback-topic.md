---
title: 'Kurz: Integrace s virtuálními agenty napájení - QnA Maker'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu zlepšete kvalitu znalostní báze s aktivním učením. Zkontrolujte, přijměte nebo zamítněte nebo přidejte bez odebrání nebo změny stávajících otázek.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402814"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Kurz: Přidání znalostní báze do virtuálních agentů napájení
Vytvořte a rozšiřte robota [Power Virtual Agents,](https://powervirtualagents.microsoft.com/) abyste poskytli odpovědi z vaší znalostní báze.

V tomto kurzu se naučíte:

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření robota power virtual agents
> * Vytvoření záložního tématu systému
> * Přidání QnA Makeru jako akce k tématu jako toku power automatu
> * Vytvoření řešení Power Automate
> * Přidání toku power automatu do vašeho řešení
> * Publikování virtuálních agentů napájení
> * Otestujte virtuální agenty napájení a získejte odpověď od znalostní báze QnA Maker

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Integrace agenta se znalostní báze

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) umožňuje týmům vytvářet výkonné roboty pomocí grafického rozhraní s průvodcem bez kódu. Nepotřebujete datové vědce ani vývojáře.

Ve virtuálních agentech napájení vytvoříte agenta s řadou témat (oblastí předmětu), abyste odpověděli na otázky uživatelů prováděním akcí. Pokud nelze najít odpověď, záložní systém může vrátit odpověď.

Nakonfigurujte agenta tak, aby odeslal otázku do znalostní báze jako součást akce tématu nebo jako součást cesty k tématu *System Fallback.* Oba používají akci pro připojení k vaší znalostní bázi a vrátit odpověď.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate se `GenerateAnswer` připojuje k akci

Chcete-li připojit agenta ke znalostní bázi, vytvořte akci pomocí power automatu. Power Automate poskytuje procesní tok, který se připojuje `GenerateAnswer` k rozhraní API QnA Maker.

Po návrhu a uložení toku je k dispozici v řešení Power Automate. Použijte toto řešení jako akci ve vašem agentovi.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Připojení agenta ke znalostní bázi

Tady je přehled kroků pro připojení agenta ve virtuálních agentech power k znalostní bázi v QnA Makeru.

* Na portálu [QnA Maker:](https://www.qnamaker.ai/)
    * Sestavte a publikujte znalostní bázi.
    * Zkopírujte podrobnosti znalostní báze, včetně ID, klíče koncového bodu runtime a hostitele koncového bodu runtime.
* Na portálu [Power Virtual Agents:](https://powerva.microsoft.com/)
    * Vytvořte téma agenta.
    * Volání akce (pro tok power automatu).
* Na portálu [Power Automate:](https://us.flow.microsoft.com/)
    * Vytvořte tok s konektorem na [GenerateAnswer QnA Maker](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * QnA Maker publikoval informace o znalostní bázi:
            * ID znalostní báze
            * Hostitel koncového bodu prostředku QnA Maker
            * Klíč koncového bodu prostředku QnA Maker
        * Vstup - uživatelský dotaz
        * Výstup - odpověď znalostní báze
    * Vytvořte řešení a přidejte tok.
* Návrat k virtuálním agentům:
    * Vyberte výstup řešení jako zprávu pro téma.

## <a name="create-and-publish-a-knowledge-base"></a>Vytvoření a publikování znalostní báze

1. Chcete-li vytvořit znalostní bázi, postupujte podle [rychlého startu.](../Quickstarts/create-publish-knowledge-base.md) Nedokončujte poslední část, o vytvoření robota. Místo toho použijte tento kurz k vytvoření robota s virtuálními agenty napájení.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s publikovaným nastavením znalostní báze](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Zadejte publikovaná nastavení znalostní báze, která najdete na stránce **Nastavení** na portálu [QnA Maker.](https://www.qnamaker.ai/) Tyto informace budete potřebovat pro [krok Power Automate](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) ke `GenerateAnswer` konfiguraci připojení QnA Maker.

1. Na portálu QnA Maker na stránce **Nastavení** najděte klíč koncového bodu, hostitele koncového bodu a ID znalostní báze.

## <a name="create-an-agent-in-power-virtual-agents"></a>Vytvoření agenta ve virtuálních agentech napájení

1. [Přihlaste se k virtuálním agentům napájení](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Použijte svůj školní nebo pracovní e-mailový účet.
1. Pokud je toto váš první bot, budete na **domovské** stránce agenta. Pokud to není váš první bot, vyberte bot z pravé horní části stránky, a vyberte **+ New Bot**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky domovské stránky Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Zadejte publikovaná nastavení znalostní báze, která najdete na stránce **Nastavení** na portálu [QnA Maker.](https://www.qnamaker.ai/)

## <a name="topics-provided-in-the-bot"></a>Témata uvedená v bot

Agent používá kolekci témat k zodpovězení otázek v oblasti předmětu. V tomto kurzu má agent mnoho témat, která jsou pro vás k dispozici, rozdělená do uživatelských témat a systémových témat.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s tématy poskytnutými v agentovi](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Vytvoření záložního tématu systému

Přestože se agent může připojit k vaší znalostní bázi z libovolného tématu, tento kurz používá záložní téma *systému.* Záložní téma se používá, když agent nemůže najít odpověď. Agent předá text uživatele `GenerateAnswer` rozhraní API QnA Maker, obdrží odpověď z vaší znalostní báze a zobrazí ji uživateli jako zprávu.

1. Na portálu [Power Virtual Agents](https://powerva.microsoft.com/#/) v pravém horním rohu vyberte **Nastavení** (ikona ozubeného kola). Pak vyberte **možnost Záložní systém**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s položkou nabídky Power Virtual Agents pro záložní systém](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Výběrem **možnosti + přidat** přidáte záložní téma systému.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s přidáním záložního tématu](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Po přidání tématu vyberte **Přejít na téma Záložní** a vytvořte záložní téma na vývojovém plátně.

    > [!TIP]
    > Pokud se potřebujete vrátit k tématu záložní, je k dispozici v části **Témata** jako součást témat **systému.**

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Přidání akce pomocí vývojového plátna

Pomocí základní stránky pro vytváření virtuálních agentů power propojte záložní téma se znalostní bází. Téma začíná nerozpoznaným textem uživatele. Přidejte akci, která předá tento text qnA makeru a pak zobrazí odpověď jako zprávu. Poslední krok zobrazení odpovědi je zpracován jako [samostatný krok](#add-your-solutions-flow-to-power-virtual-agents), dále v tomto kurzu.

Tato část vytvoří tok konverzace záložní téma.

1. Nová záložní akce již může mít prvky toku konverzace. Odstraňte položku **Eskalovat** výběrem nabídky **Možnosti.**

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s tématem záložního zdroje Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Vyberte **+** konektor tekoucí z pole **Zpráva** a pak vyberte **Volat akci**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s akcí Volání](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Vyberte **Vytvořit tok**. Tento proces vás přenese na portál Power Automate.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s vytvořením toku](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Vytvoření toku power automatu pro připojení k vaší znalostní bázi

Následující postup vytvoří tok power automatu, který:
* Pořídí text příchozího uživatele a odešle jej do QnA Makeru.
* Přiřadí nejvyšší odpověď qnA makeru proměnné a odešle proměnnou (horní odpověď) jako odpověď zpět vašemu agentovi.

1. V **power automatu**je **šablona toku** spuštěna za vás. V položce toku **virtuálních agentů napájení** vyberte **Upravit** a nakonfigurujte vstupní proměnnou přicházející od agenta do znalostní báze. Vstupní proměnná založená na textu je textová otázka odeslaná uživatelem od vašeho agenta.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s možností Power Automate pro konfiguraci vstupní proměnné jako textového řetězce](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Přidejte textový vstup a `InputText`pojmenujte proměnnou s popisem . `IncomingUserQuestion` Toto pojmenování pomáhá odlišit vstupní text od výstupního textu, který vytvoříte později.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s možností Power Automate pro konfiguraci názvu a popisu vstupní proměnné](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Vyberte **+** konektor tekoucí z pole **Virtuální agenti napájení,** chcete-li vložit nový krok do toku (před **návratovou hodnotu (hodnoty) do virtuálního agenta napájení).** Pak vyberte **Přidat akci**.

1. Vyhledejte `Qna` akce **QnA Maker** a pak vyberte **Generovat odpověď**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky generovat odpověď](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    Požadované nastavení připojení pro QnA Maker se zobrazí v akci a nastavení otázky od agenta.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s požadovaným nastavením připojení](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Nakonfigurujte akci pomocí ID znalostní báze, hostitele koncového bodu a klíče koncového bodu. Ty najdete na stránce **Nastavení** vaší znalostní báze na portálu QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s publikovaným nastavením znalostní báze](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Chcete-li **nakonfigurovat otázku**, vyberte `InputText` textové pole a vyberte ze seznamu.

1. Chcete-li do toku vložit **+** nový krok, vyberte spojnici toku z pole Generovat akci **odpovědi.** Pak vyberte **Přidat akci**.

1. Chcete-li přidat proměnnou pro `GenerateAnswer`zachycení textu odpovědi `Initialize variable` vráceného z aplikace , vyhledejte a vyberte akci.

    Nastavte název proměnné na `OutgoingQnAAnswer`a vyberte typ jako **String**. Nenastavovat **hodnotu**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s inicializací výstupní proměnné](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Chcete-li do toku vložit **+** nový krok, vyberte spojnici tekoucí z pole **Akce proměnné Inicializovat.** Pak vyberte **Přidat akci**.

1. Chcete-li nastavit celou odpověď JSON znalostní báze na proměnnou, vyhledejte a vyberte`Apply to each` akci. Vyberte `GenerateAnswer` `answers`možnost .

1. Chcete-li vrátit pouze horní odpověď, v jednom **poli Použít pro každé** pole vyberte Přidat **akci**. Vyhledejte a vyberte **nastavit proměnnou**.

    V poli **Nastavit proměnnou** vyberte textové pole **Název**a ze seznamu vyberte **OutgoingQnAAnswer.**

    Vyberte textové pole pro **Hodnotu**a ze seznamu vyberte Odpovědi na **odpověď.**

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s nastavením názvu a hodnoty proměnné](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Chcete-li proměnnou (a její hodnotu) vrátit, vyberte položku toku **virtuálního agenta.** Pak vyberte **Upravit** > **přidat výstup**. Vyberte typ **textového** výstupu **Title** a `FinalAnswer`zadejte název souboru . Vyberte textové pole pro **hodnotu** `OutgoingQnAAnswer` a pak vyberte proměnnou.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s nastavením vrácené hodnoty](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Uložte tok výběrem možnosti **Uložit**.

## <a name="create-a-solution-and-add-the-flow"></a>Vytvoření řešení a přidání toku

Aby agent mohl najít tok a připojit se k němu, musí být tok součástí řešení Power Automate.

1. Ještě na portálu Power Automate vyberte **řešení** z navigace na levé straně.

1. Vyberte **+ Nové řešení**.

1. Zadejte zobrazovaný název. Seznam řešení zahrnuje všechna řešení ve vaší organizaci nebo škole. Zvolte konvenci pojmenování, která vám pomůže filtrovat pouze vaše řešení. Můžete například předponu e-mailu na `jondoe-power-virtual-agent-qnamaker-fallback`název řešení: .

1. Vyberte vydavatele ze seznamu voleb.

1. Přijměte výchozí hodnoty pro název a verzi.

1. Chcete-li proces dokončit, vyberte **vytvořit.**

## <a name="add-your-flow-to-the-solution"></a>Přidejte svůj tok do řešení

1. V seznamu řešení vyberte řešení, které jste právě vytvořili. Mělo by to být na prvním místě seznamu. Pokud tomu tak není, vyhledejte název e-mailu, který je součástí názvu řešení.

1. V řešení vyberte **+ Přidat existující**a pak ze seznamu vyberte **Tok.**

1. Najděte svůj tok a pak vyberte **Přidat,** abyste dokončili proces. Pokud existuje mnoho toků, podívejte se na **změněný** sloupec najít nejnovější tok.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Přidejte tok svého řešení do virtuálních agentů napájení

1. Vraťte se na kartu prohlížeče se svým agentem v Power Virtual Agents. Authoring plátno by mělo být stále otevřené.

1. Chcete-li do toku vložit nový krok, vyberte v poli **Akce zpráva** spojnici. **+** Pak vyberte **Volat na akci**.

1. V nové akci vyberte vstupní hodnotu **UnrecognizedTriggerPhrase**. To předá text z agenta toku.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s možností Power Virtual Agents pro výběr nerozpoznané spouštěcí fráze](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Chcete-li do toku vložit **Action** nový krok, **+** vyberte pod polem Akce spojnici. Pak vyberte **Zobrazit zprávu**.

1. Zadejte text `Your answer is:`zprávy, . Vyberte `FinalAnswer` jako kontextovou proměnnou pomocí funkce panelu nástrojů na místě.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s možností Power Virtual Agents pro zadání textu zprávy](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Na panelu nástrojů kontextu vyberte **Uložit**, chcete-li uložit detaily vývojové hoplátna pro téma.

Takhle vypadá plátno posledního agenta.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s plátnem konečného agenta](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Otestujte agenta

1. V testovacím podokně přepněte **sledovat mezi tématy**. To vám umožní sledovat průběh mezi tématy, stejně jako v rámci jednoho tématu.

1. Otestujte agenta zadáním textu uživatele v následujícím pořadí. Authoring canvas hlásí úspěšné kroky se zeleným zaškrtnutím.

    |Pořadí otázek|Zkušební otázky|Účel|
    |--|--|--|
    |1|Dobrý den|Zahájit konverzaci|
    |2|Hodiny obchodu|Ukázkové téma. To je konfigurováno pro vás bez jakékoli další práce z vaší strany.|
    |3|Ano|V odpovědi na`Did that answer your question?`|
    |4|Vynikající|V odpovědi na`Please rate your experience.`|
    |5|Ano|V odpovědi na`Can I help with anything else?`|
    |6|Co je znalostní báze?|Tato otázka spustí záložní akci, která odešle text do znalostní báze odpovědět. Pak se zobrazí odpověď. |

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s plátnem konečného agenta](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Publikování svého robota

Chcete-li agenta zpřístupnit všem členům vaší školy nebo organizace, musíte jej publikovat.

1. V navigaci vlevo vyberte **Publikovat**. Pak na stránce vyberte **Publikovat.**

1. Zkuste svého robota na demo webových stránkách (podívejte se na odkaz v části **Publikovat**).

    Otevře se nová webová stránka s vaším robotem. Zeptejte se bot stejnou zkušební otázku:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s plátnem konečného agenta](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Podělte se o svého robota

Chcete-li sdílet ukázkový web, nakonfigurujte jej jako kanál.

1. V navigaci vlevo vyberte **Spravovat** > **kanály**.

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