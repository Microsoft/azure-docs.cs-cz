---
title: 'Rychlý Start: vytvoření nové aplikace na portálu LUIS'
description: V tomto rychlém startu vytvoříte základní části aplikace, záměry a entity a otestujete je pomocí Sample utterance na portálu LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/30/2020
ms.openlocfilehash: a5443dce9fc8bc028232f66f45b537a46858f35a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436670"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Rychlý Start: vytvoření nové aplikace na portálu LUIS

V tomto rychlém startu vytvoříte novou aplikaci na portálu LUIS. Nejdřív vytvořte základní části aplikace, **záměrů** a **entit**. Pak otestujte aplikaci zadáním ukázkového uživatele utterance na interaktivním panelu testů, který získá předpokládaný záměr.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Vytvoření aplikace

Pokud chcete vytvořit aplikaci, vyberte **+ Nová aplikace**. 

V okně, které se zobrazí, zadejte následující informace:

|Název  |Popis  |
|---------|---------|
|Název     | Název vaší aplikace. Například "domácí automatizace". Povinná hodnota.        |
|Kultura     | Jazyk, který vaše aplikace chápe a mluví. Povinná hodnota.   |
|Popis | Popis vaší aplikace Nepovinný parametr.
|Prostředek předpovědi | Předpověď prostředku, který bude přijímat dotazy. Nepovinný parametr. |

Vyberte **Hotovo**.

>[!NOTE]
>Jakmile se aplikace vytvoří, nemůžete změnit její jazykovou verzi.


## <a name="create-intents"></a>Vytvořit záměry

Po vytvoření aplikace LUIS je potřeba vytvořit záměry. Záměry je způsob, jak klasifikovat text od uživatelů. Například aplikace pro lidské zdroje může mít dvě funkce. Pomáhat lidem:

 1. Najít a použít pro úlohy
 1. Najít formuláře, které se mají použít pro úlohy

Dvě různé _záměry_ aplikace jsou v souladu s následujícími záměry:

|Záměr|Příklad textu od uživatele<br>známé jako _utterance_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Chcete-li vytvořit záměry, proveďte následující kroky:

1. Po vytvoření aplikace se ujistěte, že jste na stránce **záměry** v části **sestavení** . Vyberte **Vytvořit**.

   [![Snímek obrazovky s výběrem možnosti ' vytvořit ' pro vytvoření nového záměru](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Zadejte název záměru `FindForm` a potom vyberte **Hotovo**.

## <a name="add-an-example-utterance"></a>Přidat příklad utterance

Můžete přidat příklad projevy po vytvoření záměrů. Příkladem projevy je text, který uživatel zadá do robota chatu nebo jiné klientské aplikace. Namapují záměr textu uživatele na LUIS záměr. Pro tento příklad `FindForm` záměru aplikace bude vzorový projevy obsahovat číslo formuláře. Klientská aplikace potřebuje ke splnění požadavku uživatele číslo formuláře, takže je důležité ho zahrnout do utterance.

Na stránce **záměry** pro `FindForm` přidejte následující příklad projevy v části **příklad utterance**, 

|#|Ukázkové promluvy|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

Podle návrhu se tyto příklady projevy liší následujícími způsoby:

* Délka utterance
* [oddělovač](luis-reference-application-settings.md#punctuation-normalization)
* Volba Wordu
* příkaz vhodné (is, was, bude)
* pořadí slov


## <a name="create-a-regular-expression-entity"></a>Vytvořit entitu regulárního výrazu

Chcete-li vrátit číslo formuláře v odpovědi předpovědi modulu runtime, číslo formuláře musí být extrahováno jako entita. Vzhledem k tomu, že je text s číslem formuláře vysoce strukturovaný, můžete použít entitu regulárního výrazu. Vytvořte entitu regulárního výrazu pomocí následujících kroků:

1. V nabídce vlevo vyberte **entity** .

1. Na stránce **entity** vyberte **vytvořit** .

1. Zadejte název `FormNumber` , vyberte typ entity **Regex** .

1. `hrf-[0-9]{6}`Do pole **Regex** zadejte regulární výraz. Tato položka odpovídá znakům literálu, `hrf-` a umožňuje zadat přesně šest číslic a pak vybrat **vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s vytvořením entity regulárního výrazu](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    Tato entita extrahuje libovolný text, který odpovídá regulárnímu výrazu v jakémkoli z záměrů.

## <a name="add-example-utterances-to-the-none-intent"></a>Přidat příklad projevy k záměru None

Záměr **none** není záložním záměrem a neměl by být ponechán prázdný. Tento záměr by měl obsahovat jeden utterance pro každých 10 příkladů projevy, které jste přidali pro ostatní záměry aplikace.

Příklad projevy záměru **none** by měl být mimo vaši doménu klientské aplikace.

1. V nabídce vlevo vyberte **záměry** a pak v seznamu záměry vyberte **žádné** .

1. Do záměru přidejte následující příklad projevy:

   |Projevy příklad záměru None|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   V případě této aplikace jsou tyto příklady projevy mimo doménu. Pokud vaše doména obsahuje zvířata nebo oceánu, pak byste měli použít jiný příklad projevy pro záměr **none** .

## <a name="train-the-app"></a>Trénování aplikace

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Podívejte se na entitu regulární výraz v příkladu projevy

1. Ověřte, že se entita nachází v **FindForm**  záměru tím, že v nabídce vlevo vyberete **záměry** . Pak vyberte **FindForm** záměr.

   Entita je označena tak, jak se zobrazuje v příkladu projevy.

   > [!div class="mx-imgBorder"]
   > [![Snímek obrazovky všech příkladů projevy označených entitami](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Otestujte novou aplikaci pomocí interaktivního testovacího podokna.

Pomocí interaktivního **testovacího** podokna na portálu Luis ověřte, že se entita extrahuje z nové projevy aplikace ještě nevidí.

1. V pravé horní nabídce vyberte **test** .

1. Přidejte nový utterance a stiskněte klávesu ENTER:

   ```Is there a form named hrf-234098```

    Vyberte **zkontrolovat** a podívejte se na předpovědi entit.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s testováním nových utterance v testovacím podokně](./media/get-started-portal-build-app/test-new-utterance.png)

   Nejvyšší předpokládaný záměr je správně **FindForm** s více než 90% spolehlivostí (0,977). Entita **FormNumber** se extrahuje s hodnotou hrf-234098.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi a nepřesouváte se k dalšímu rychlému startu, vyberte **Moje aplikace** z horní navigační nabídky. Pak ze seznamu vyberte levé políčko aplikace a na panelu nástrojů kontext vyberte tlačítko  **Odstranit** nad seznamem.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [2. nasazení aplikace](get-started-portal-deploy-app.md)
