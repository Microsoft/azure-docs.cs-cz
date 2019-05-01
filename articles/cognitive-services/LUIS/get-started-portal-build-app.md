---
title: 'Rychlý start: Vytvoření nové aplikace na portálu služby LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: V tomto rychlém startu vytvoříte novou aplikaci na portálu služby LUIS. Vytvoření základní součásti aplikace, záměry a entity. Otestujte aplikaci zadáním utterance uživatele ukázkové panelu interaktivní testu pro získání záměru předpokládané. Vytvoření aplikace je zdarma; nevyžaduje předplatné Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 5ec38c510fedee7f489b3b100285eeb7c75f4561
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64690973"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Rychlý start: Vytvoření nové aplikace na portálu služby LUIS

V tomto rychlém startu vytvoříte novou aplikaci v [LUIS portál](https://www.luis.ai). Nejprve vytvoříte základní části aplikace, **záměry**, a **entity**. Potom otestujte aplikaci zadáním utterance uživatele ukázkové panelu interaktivní testu pro získání záměru předpokládané.

Vytvoření aplikace je bezplatná a nevyžaduje předplatné Azure. Jakmile budete připraveni k nasazení své aplikace, najdete v článku [rychlý start k nasazení aplikace](get-started-portal-deploy-app.md). To ukazuje, jak vytvořit prostředek Azure Cognitive Service a přiřadit aplikace.

## <a name="create-an-app"></a>Vytvoření aplikace

1. Otevřít [LUIS portál](https://www.luis.ai) v prohlížeči a přihlaste se. Pokud je poprvé přihlášení, musíte vytvořit bezplatný účet uživatele portálu služby LUIS.

1. Vyberte **vytvořit novou aplikaci** z panelu nástrojů kontextu.

   [![Vytvoření nové aplikace na portálu služby LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. V místním okně Konfigurace aplikace s následujícím nastavením a pak vyberte **provádí**.

   |Název nastavení| Hodnota | Účel|
   |--|--|--|
   |Název|`myEnglishApp`|Jedinečný název aplikace LUIS<br>povinné|
   |Jazyková verze|**Angličtina**|Jazyk projevy od uživatelů, **en-us**<br>povinné|
   |Popis|`App made with LUIS Portal`|Popis aplikace<br>nepovinné|
   | | | |

   ![Zadejte nové nastavení aplikace](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Vytvořit záměry

Po vytvoření aplikace LUIS, je potřeba vytvořit záměry. Záměry představují způsob, jak zařadit text od uživatelů. Například aplikaci lidských zdrojů může mít dvě funkce. Abyste pomohli uživatelům:

 1. Vyhledejte a použijte pro úlohy
 1. Najít tvary, které chcete použít pro úlohy

Aplikaci prvku dva různé _záměry_ zarovnat následující příkazy:

|Záměr|Příklad od uživatele<br>označuje jako _utterance_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Pokud chcete vytvořit záměrů, proveďte následující kroky:

1. Po vytvoření aplikace je na **záměry** stránku **sestavení** oddílu. Vyberte **Create new intent** (Vytvořit nový záměr).

   [![Vyberte tlačítko Vytvořit nový záměru](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Zadejte název záměru `FindForm`a pak vyberte **provádí**.

   ![Zadejte název FindForm záměru](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-an-example-utterance"></a>Přidat utterance příklad

Příklad projevy přidáte po vytvoření záměry. Příklad projevy jsou text, který uživatel zadá v chatovací robot nebo v jiné klientské aplikace. Jsou záměr LUIS mapovány záměru uživatele text.

Pro tuto aplikaci příklad `FindForm` záměru, příklad projevy bude obsahovat číslo formuláře. Klientská aplikace potřebuje ke splnění žádost uživatele, takže je důležité zahrnout utterance číslo formuláře.

Přidejte následující projevy 15 příklad do `FindForm` záměr.

|#|Ukázkové promluvy|
|--|--|
|1|Hledáte hrf 123456|
|2|Kde je hrf-234591 formuláře lidských zdrojů?|
|3|hrf 345623, kde je to|
|4|Je možné poslat mi hrf 345794|
|5|Potřebuji hrf 234695 chtějí zažádat o interní úlohy?|
|6|Potřebuje vědět, že žádající úlohy pomocí hrf 234091 vedoucí|
|7|Kam můžu poslat hrf 234918? Získat e-mailové odpovědi, který byl přijat?|
|8|hrf-234555|
|9|Pokud byla aktualizována hrf 234987?|
|10|Pomocí formuláře hrf-876345 platí pro technický pozice|
|11|Bylo na novou verzi hrf-765234 odeslané pro moje open No?|
|12|Pro mezinárodní úlohy používají hrf 234234?|
|13|hrf 234598 překlepy|
|14|bude hrf 234567 upravovat pro nové požadavky|
|15|hrf-123456, hrf-123123, hrf-234567|

Návrh těchto projevů příklad liší následujícími způsoby:

* Délka utterance
* Interpunkce
* Volba aplikace Word
* příkaz čas (je, byla, bude)
* pořadí slov

[![Zadejte projevy příklad pro FindForm záměr](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-a-regular-expression-entity"></a>Vytvoření entity regulárního výrazu

Chcete-li získat formuláře číslo vrácen jako část předpovědi odpověď modulu runtime, formulář musí být označen jako entity. Protože text čísla formuláře je vysoce strukturovaná, může být označený, pomocí regulárních výrazů entity. Vytvoření entity pomocí následujících kroků:

1. Vyberte **entity** z nabídky na levé straně.

1. Vyberte **vytvořit novou entitu** na **entity** stránky.

1. Zadejte název `Human Resources Form Number`, vyberte **regulární výraz** entity typu a zadejte regulární výraz `hrf-[0-9]{6}`. Tato položka shoduje s literálními znaky `hrf-`a umožňuje přesně 6 číslic.

   ![Zadejte informace o entitách entity regulárního výrazu](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Vyberte **Done** (Hotovo).

## <a name="add-example-utterances-to-the-none-intent"></a>Přidání projevů příklad na hodnotu None záměru

**Žádný** záměr je záložní záměr a by nemělo být ponecháno prázdné. Cílem tohoto je by měl obsahovat jeden utterance pro každých 10 příklad projevy, které jste přidali pro ostatní záměry aplikace.

**Žádný** na záměr projevů příklad by měla být mimo doménu aplikace vašeho klienta.

1. Vyberte **záměry** v levé nabídce a pak vyberte **žádný** ze seznamu záměry.

1. Následující příklad projevy přidejte k příslušnému záměru:

   |Jeden příklad záměr projevů|
   |--|
   |Barking dogs are annoying (Štěkající psi jsou otravní)|
   |Order a pizza for me (Objednej mi pizzu)|
   |Penguins in the ocean (Tučňáci v oceánech)|

   Pro tuto aplikaci lidských zdrojů těchto projevů příkladu jsou mimo doménu. Pokud vaši doménu lidských zdrojů patří zvířata, potravin nebo oceánských, pak byste měli používat jiný příklad projevy pro **žádný** záměr.

## <a name="train-the-app"></a>Trénování aplikace

V nabídce v pravé horní části vyberte **trénování** použít záměr a entity model změní na aktuální verzi aplikace.

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Podívejte se na entitu regulárních výrazů v příkladu projevy

1. Zkontrolujte entity je ve **FindForm** záměru tak, že vyberete **záměry** v levé nabídce. Potom vyberte **FindForm** záměr.

   Entita je označeno, ve kterém se zobrazuje v projevy příklad. Pokud chcete zobrazit původního textu místo názvu entity, přepněte **zobrazení entity** z panelu nástrojů.

   [![Všechny projevy příklad označena s entitami](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Otestovat novou aplikaci s interaktivní testovací podokno

Pomocí interaktivního **Test** podokně portálu LUIS k ověření, že entita se extrahují z nové projevy aplikace nebylo dosud nikdy.

1. Vyberte **Test** v pravé horní nabídce.

1. Přidat nový utterance a potom stiskněte klávesu Enter:

   ```Is there a form named hrf-234098```

   ![Nové utterance testů v testovací podokno](./media/get-started-portal-build-app/test-new-utterance.png)

   Horní předpovědět záměrem je správně **FindForm** s více než 90 % spolehlivosti (0.977). **Číslo formuláře lidských zdrojů** entity se extrahuje hodnotu hrf 234098.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto rychlého startu a nejsou dalšímu rychlému startu pokračovat, vyberte **Moje aplikace** v horní navigační nabídce. Vyberte zaškrtávací políčko vlevo aplikace ze seznamu a vyberte **odstranit** z kontextu nástrojů nad seznamem.

[![Odstranit aplikaci ze seznamu aplikací](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [2. Nasazení aplikace](get-started-portal-deploy-app.md)
