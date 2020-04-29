---
title: 'Rychlý Start: vytvoření nové aplikace na portálu LUIS'
description: V tomto rychlém startu vytvoříte základní části aplikace, záměry a entity a otestujete je pomocí Sample utterance na portálu LUIS.
ms.topic: quickstart
ms.date: 04/14/2020
ms.openlocfilehash: 2d601646c43c0f0d99dc6934cf1f1c960e0b0f79
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382571"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Rychlý Start: vytvoření nové aplikace na portálu LUIS

V tomto rychlém startu vytvoříte novou aplikaci na portálu LUIS. Nejdřív vytvořte základní části aplikace, **záměrů**a **entit**. Pak otestujte aplikaci zadáním ukázkového uživatele utterance na interaktivním panelu testů, který získá předpokládaný záměr.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Vytvoření aplikace

1. Z panelu nástrojů kontext vyberte **+ Nová aplikace pro konverzaci** a pak vyberte **Nová aplikace pro konverzaci**.

    > [!div class="mx-imgBorder"]
    > [![Vytvoření nové aplikace na portálu LUIS](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. V automaticky otevíraném okně nakonfigurujte aplikaci s následujícím nastavením a potom vyberte **Hotovo**.

   |Název nastavení| Hodnota | Účel|
   |--|--|--|
   |Název|`myEnglishApp`|Jedinečný název aplikace LUIS<br>Požadovaný|
   |Kultura|**Angličtina**|Jazyk projevy od uživatelů, **en-US**<br>Požadovaný|
   |Popis (volitelné)|`App made with LUIS Portal`|Popis aplikace<br>optional|
   |Prostředek předpovědi (volitelné) |-  |Nevybírejte. LUIS vám nabízí počáteční klíč, který můžete zdarma použít pro vytváření a 1 000 požadavků na koncový bod předpovědi. |

   ![Zadat nové nastavení aplikace](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Vytvořit záměry

Po vytvoření aplikace LUIS je potřeba vytvořit záměry. Záměry je způsob kategorizace textu od uživatelů. Například aplikace pro lidské zdroje může mít dvě funkce. Pomáhat lidem:

 1. Najít a použít pro úlohy
 1. Najít formuláře, které se mají použít pro úlohy

Dvě různé _záměry_ aplikace jsou v souladu s následujícími záměry:

|Záměr|Příklad textu od uživatele<br>známé jako _utterance_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Chcete-li vytvořit záměry, proveďte následující kroky:

1. Po vytvoření aplikace se nacházíte na stránce **záměry** v části **sestavení** . Vyberte **Vytvořit**.

   [![Vyberte vytvořit a vytvořte nový záměr.](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Zadejte název `FindForm`záměru a potom vyberte **Hotovo**.

## <a name="add-an-example-utterance"></a>Přidat příklad utterance

Můžete přidat příklad projevy po vytvoření záměrů. Příkladem projevy je text, který uživatel zadá do robota chatu nebo jiné klientské aplikace. Namapují záměr textu uživatele na LUIS záměr.

Pro tento příklad `FindForm` záměru aplikace bude vzorový projevy obsahovat číslo formuláře. Klientská aplikace potřebuje ke splnění požadavku uživatele číslo formuláře, takže je důležité ho zahrnout do utterance.

> [!div class="mx-imgBorder"]
> [![Zadejte příklad projevy pro záměr FindForm.](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Přidejte následující 15 příkladů projevy k `FindForm` záměru.

|#|Ukázkové promluvy|
|--|--|
|1|Hledání HRF-123456|
|2|Kde se nachází formulář lidské zdroje HRF-234591?|
|3|HRF-345623, kde je|
|4|Je možné poslat mi HRF-345794|
|5|Potřebuji pro interní úlohu použít HRF-234695?|
|6|Potřebuje mi správce vědět, že ho mám použít pro úlohu s HRF-234091|
|7|Kam můžu odeslat HRF-234918? Zobrazuje se mi e-mailová odpověď, která byla přijata?|
|8|HRF-234555|
|9|Kdy byla aktualizace HRF-234987?|
|10|Použít formu HRF-876345 k použití pro technické pozice|
|11|Byla pro moji Open REQ odeslána nová verze HRF-765234?|
|12|Používám HRF-234234 pro mezinárodní úlohy?|
|13|HRF-234598 pravopisná chyba|
|14|bude HRF-234567 upravovat nové požadavky|
|15|HRF-123456, HRF-123123, HRF-234567|

Podle návrhu se tyto příklady projevy liší následujícími způsoby:

* Délka utterance
* [oddělovač](luis-reference-application-settings.md#punctuation-normalization)
* Volba Wordu
* příkaz vhodné (is, was, bude)
* pořadí slov


## <a name="create-a-regular-expression-entity"></a>Vytvořit entitu regulárního výrazu

Chcete-li vrátit číslo formuláře v odpovědi předpovědi modulu runtime, musí být formulář označen jako entita. Vzhledem k tomu, že je text s číslem formuláře vysoce strukturovaný, můžete ho označit pomocí entity regulárního výrazu. Vytvořte entitu pomocí následujících kroků:

1. V nabídce vlevo vyberte **entity** .

1. Na stránce **entity** vyberte **vytvořit** .

1. Zadejte název `Human Resources Form Number`, vyberte typ entity **Regex** a pak vyberte **Další**.

   ![Vytvořit entitu regulárního výrazu](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Zadejte výraz regulárního výrazu (**Regex**), `hrf-[0-9]{6}`. Tato položka odpovídá znakům literálu `hrf-`, a umožňuje zadat přesně 6 číslic a pak vybrat **vytvořit**.

   ![Zadejte regulární výraz pro entitu](./media/get-started-portal-build-app/create-regular-expression-entity-with-expression.png)


## <a name="add-example-utterances-to-the-none-intent"></a>Přidat příklad projevy k záměru None

Záměr **none** není záložním záměrem a neměl by být ponechán prázdný. Tento záměr by měl obsahovat jeden utterance pro každých 10 příkladů projevy, které jste přidali pro ostatní záměry aplikace.

Příklad projevy záměru **none** by měl být mimo vaši doménu klientské aplikace.

1. V nabídce vlevo vyberte **záměry** a pak v seznamu záměry vyberte **žádné** .

1. Do záměru přidejte následující příklad projevy:

   |Projevy příklad záměru None|
   |--|
   |Barking dogs are annoying (Štěkající psi jsou otravní)|
   |Order a pizza for me (Objednej mi pizzu)|
   |Penguins in the ocean (Tučňáci v oceánech)|

   V případě této aplikace jsou tyto příklady projevy mimo doménu. Pokud vaše doména zahrnuje zvířata, jídlo nebo oceánu, měli byste pro záměr **none** použít jiný příklad projevy.

## <a name="train-the-app"></a>Trénování aplikace

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Podívejte se na entitu regulární výraz v příkladu projevy

1. Ověřte, že se entita nachází v **FindForm** záměru tím, že v nabídce vlevo vyberete **záměry** . Pak vyberte **FindForm** záměr.

   Entita je označena tak, jak se zobrazuje v příkladu projevy. Chcete-li zobrazit původní text místo názvu entity, přepínejte **zobrazení entit** z panelu nástrojů.

   > [!div class="mx-imgBorder"]
   > [![Všechny příklady projevy označené entitami](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Otestujte novou aplikaci pomocí interaktivního testovacího podokna.

Pomocí interaktivního **testovacího** podokna na portálu Luis ověřte, že se entita extrahuje z nové projevy aplikace ještě nevidí.

1. V pravé horní nabídce vyberte **test** .

1. Přidejte nový utterance a stiskněte klávesu ENTER:

   ```Is there a form named hrf-234098```

    Vyberte **zkontrolovat** a podívejte se na předpovědi entit.

   > [!div class="mx-imgBorder"]
   > ![Test New utterance v testovacím podokně](./media/get-started-portal-build-app/test-new-utterance.png)

   Nejvyšší předpokládaný záměr je správně **FindForm** s více než 90% spolehlivostí (0,977). Entita **Číslo formuláře pro lidské zdroje** je extrahována hodnotou hrf-234098.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi a nepřesouváte se k dalšímu rychlému startu, vyberte **Moje aplikace** z horní navigační nabídky. Pak ze seznamu vyberte levé políčko aplikace a na panelu nástrojů kontext vyberte tlačítko **Odstranit** nad seznamem.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [2. nasazení aplikace](get-started-portal-deploy-app.md)
