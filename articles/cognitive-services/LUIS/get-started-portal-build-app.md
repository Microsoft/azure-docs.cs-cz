---
title: 'Úvodní příručka: Vytvoření nové aplikace na portálu LUIS'
description: V tomto rychlém startu vytvoříte základní části aplikace, záměry a entity, stejně jako test s ukázkovým utterance na portálu LUIS.
ms.topic: quickstart
ms.date: 04/14/2020
ms.openlocfilehash: 2d601646c43c0f0d99dc6934cf1f1c960e0b0f79
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382571"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Úvodní příručka: Vytvoření nové aplikace na portálu LUIS

V tomto rychlém startu vytvoříte novou aplikaci na portálu LUIS. Nejprve vytvořte základní části aplikace, **záměry**a **entity**. Pak otestujte aplikaci poskytnutím ukázkové projevy uživatele v interaktivním testovacím panelu, abyste získali předpokládaný záměr.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Vytvoření aplikace

1. Vyberte **+ Nová aplikace pro konverzaci** z panelu nástrojů kontextu a pak vyberte Nová aplikace pro **konverzaci**.

    > [!div class="mx-imgBorder"]
    > [![Vytvoření nové aplikace na portálu LUIS](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. V rozbalovacím okně nakonfigurujte aplikaci s následujícím nastavením a pak vyberte **Hotovo**.

   |Název nastavení| Hodnota | Účel|
   |--|--|--|
   |Name (Název)|`myEnglishApp`|Jedinečný název aplikace LUIS<br>Požadovaný|
   |Culture|**Angličtina**|Jazyk promluv od uživatelů, **en-us**<br>Požadovaný|
   |Popis (nepovinné)|`App made with LUIS Portal`|Popis aplikace<br>optional|
   |Zdroj predikce (nepovinné) |-  |Nevybírejte. Služba LUIS vám poskytuje klávesu Starter, která se používá zdarma pro vytváření a požadavky na koncový bod předvídání 1 000. |

   ![Zadání nového nastavení aplikace](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Vytvořit záměry

Po vytvoření aplikace LUIS je nutné vytvořit záměry. Záměry jsou způsob, jak kategorizovat text od uživatelů. Například aplikace pro lidské zdroje může mít dvě funkce. Pomoc lidem:

 1. Hledání a podání žádosti o zaměstnání
 1. Najít formuláře pro použití pro úlohy

Dvě různé _záměry_ aplikace odpovídají následujícím záměrům:

|Záměr|Příklad textu od uživatele<br>známý jako _utterance_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Chcete-li vytvořit záměry, proveďte následující kroky:

1. Po vytvoření aplikace se nacházíte na stránce **Záměry** v části **Sestavení.** Vyberte **Vytvořit**.

   [![Vyberte Vytvořit, chcete-li vytvořit nový záměr.](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Zadejte název `FindForm`záměru a pak vyberte **Hotovo**.

## <a name="add-an-example-utterance"></a>Přidání příkladu utterance

Přidáte příklad projevy po vytvoření záměry. Příklad y utterances jsou text, který uživatel zadá v chatu robota nebo jiné klientské aplikace. Mapují záměr textu uživatele na záměr LUIS.

Pro záměr aplikace `FindForm` v tomto příkladu ukázkové projevy budou obsahovat číslo formuláře. Klientská aplikace potřebuje číslo formuláře ke splnění požadavku uživatele, takže je důležité zahrnout do utterance.

> [!div class="mx-imgBorder"]
> [![Zadejte ukázkové projevy pro záměr FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Přidejte následující 15 příklad projevy záměru. `FindForm`

|#|Ukázkové promluvy|
|--|--|
|1|Hledáte hrf-123456|
|2|Kde je forma lidských zdrojů hrf-234591?|
|3|hrf-345623, kde je|
|4|Je možné mi poslat hrf-345794|
|5|Musím hrf-234695 požádat o interní práci?|
|6|Má můj manažer potřebuje vědět, že jsem se ucházet o práci s hrf-234091|
|7|Kam mám poslat hrf-234918? Dostanu e-mailovou odpověď, že byla přijata?|
|8|hrf-234555|
|9|Kdy byl hrf-234987 aktualizován?|
|10|Mám použít formulář hrf-876345 požádat o inženýrské pozice|
|11|Byla nová verze hrf-765234 předložena pro můj otevřený req?|
|12|Mám hrf-234234 používat pro mezinárodní pracovní místa?|
|13|hrf-234598 pravopisná chyba|
|14|bude hrf-234567 být upraveny pro nové požadavky|
|15|hrf-123456, hrf-123123, hrf-234567|

Podle návrhu tyto příkladprojevy se liší v následujících způsobech:

* délka utterance
* [Interpunkce](luis-reference-application-settings.md#punctuation-normalization)
* volba slova
* sloveso čas (je, byl, bude)
* pořadí slov


## <a name="create-a-regular-expression-entity"></a>Vytvoření entity regulárního výrazu

Chcete-li vrátit číslo formuláře v odpovědi prognózy za běhu, musí být formulář označen jako entita. Vzhledem k tomu, že text čísla formuláře je vysoce strukturovaný, můžete jej označit pomocí entity regulárního výrazu. Vytvořte entitu pomocí následujících kroků:

1. V nabídce vlevo vyberte **Entity.**

1. Na stránce **Entity vyberte** **Vytvořit.**

1. Zadejte `Human Resources Form Number`název , vyberte typ entity **Regex** a pak vyberte **Další**.

   ![Vytvořit entitu regulárního výrazu](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Zadejte regulární výraz ( `hrf-[0-9]{6}`**RegEx)** výraz, . Tato položka odpovídá literálovým znakům `hrf-`a umožňuje přesně 6 číslic a pak vyberte **Vytvořit**.

   ![Zadat regulární výraz pro entitu](./media/get-started-portal-build-app/create-regular-expression-entity-with-expression.png)


## <a name="add-example-utterances-to-the-none-intent"></a>Přidání ukázkových promluv do záměru None

**Žádný** záměr je záložní záměr a by neměly být ponechány prázdné. Tento záměr by měl obsahovat jednu utterance pro každých 10 příklad projevy, které jste přidali pro ostatní záměry aplikace.

**Příklad y žádný** záměr projevy by měl y být mimo doménu klientské aplikace.

1. V levé nabídce vyberte **Záměry** a pak ze seznamu záměrů vyberte **Žádné.**

1. Přidejte následující příklad projevy záměru:

   |Žádný záměr příklad projevy|
   |--|
   |Barking dogs are annoying (Štěkající psi jsou otravní)|
   |Order a pizza for me (Objednej mi pizzu)|
   |Penguins in the ocean (Tučňáci v oceánech)|

   Pro tuto aplikaci tyto příklady projevy jsou mimo doménu. Pokud vaše doména obsahuje zvířata, jídlo nebo oceán, měli byste použít různé příklady projevy pro **žádný** záměr.

## <a name="train-the-app"></a>Trénování aplikace

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Podívejte se na entitu regulárního výrazu v příkladových projevech

1. Ověřte, zda se entita nachází v záměru **FindForm** výběrem **záměrů** z levé nabídky. Pak vyberte **FindForm** záměr.

   Entita je označena tam, kde se zobrazí v příkladu projevy. Pokud chcete zobrazit původní text namísto názvu entity, přepněte **zobrazení entit** z panelu nástrojů.

   > [!div class="mx-imgBorder"]
   > [![Všechny ukázkové projevy označené entitami](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testování nové aplikace pomocí interaktivního testovacího podokna

Pomocí interaktivního **podokna testování** na portálu LUIS ověřte, že entita je extrahována z nových promluv, které aplikace ještě neviděla.

1. Z pravé horní nabídky vyberte **Testovat.**

1. Přidejte novou promluvu a stiskněte Enter:

   ```Is there a form named hrf-234098```

    Chcete-li zobrazit předpovědi entit, vyberte **možnost Zkontrolovat.**

   > [!div class="mx-imgBorder"]
   > ![Testování nové holání v testovacím podokně](./media/get-started-portal-build-app/test-new-utterance.png)

   Nejvyšší předpovídaný záměr je správně **FindForm** s více než 90% spolehlivost (0,977). Entita **Číslo formuláře lidských zdrojů** je extrahována s hodnotou hrf-234098.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až s tímto rychlým startem skončíte a nepřejdete na další rychlý start, vyberte v horní navigační nabídce moje **aplikace.** Pak ze seznamu zaškrtněte levé políčko aplikace a vpruhu nástrojů kontextu nad seznamem vyberte **Odstranit.**

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [2. Nasazení aplikace](get-started-portal-deploy-app.md)
