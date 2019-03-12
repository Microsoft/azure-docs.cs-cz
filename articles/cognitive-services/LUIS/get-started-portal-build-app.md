---
title: 'Rychlý start: Vytvoření nové aplikace pomocí portálu služby LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: V tomto rychlém startu vytvoříte novou aplikaci na portálu služby LUIS. Vytvoření základní součásti aplikace, záměry a entity a potom otestovat tím, že poskytuje utterance uživatele ukázkové panelu interaktivní testu pro získání záměru předpokládané. Vytvoření aplikace je zdarma; nevyžaduje předplatné Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: e97be28261d28c2a72e507adcdac0248691745c7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57786867"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Rychlý start: Vytvoření nové aplikace na portálu služby LUIS

V tomto rychlém startu vytvoříte novou aplikaci v [LUIS portál](https://www.luis.ai). Vytvoření základní součásti aplikace, **záměry** a **entity**, vyzkoušejte tím, že poskytuje utterance uživatele ukázkové panelu interaktivní testu pro získání záměru předpokládané.

Vytvoření aplikace je zdarma; nevyžaduje předplatné Azure. Jakmile budete připraveni k nasazení své aplikace a vytvořte prostředek Azure Cognitive Service a přiřaďte ho do aplikace. Tento proces nasazení se [dalšímu rychlému startu](get-started-portal-deploy-app.md).

## <a name="create-app"></a>Vytvořit aplikaci 

1. Otevřít [LUIS portál](https://www.luis.ai) v prohlížeči a přihlaste se. Pokud je toto vaše první přihlášení, musíte vytvořit bezplatný účet uživatele portálu služby LUIS.

1. Vyberte **vytvořit novou aplikaci** z panelu nástrojů kontextu.

    [![Vytvoření nové aplikace na portálu služby LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. V místním okně Konfigurace aplikace s následujícím nastavením a poté vyberte příkaz **provádí**.

    |Název nastavení| Hodnota | Účel|
    |--|--|--|
    |Název|`myEnglishApp`|Jedinečný název aplikace LUIS<br>povinné|
    |Jazyková verze|**Angličtina**|Jazyk projevy od uživatelů, **en-us**<br>povinné|
    |Popis|`App made with LUIS Portal`|Popis aplikace<br>nepovinné|

    ![Zadejte nové nastavení aplikace](./media/get-started-portal-build-app/create-new-app-settings.png)


## <a name="create-intent"></a>Vytvořit záměr 

Po vytvoření této aplikace je dalším krokem je vytvoření záměry. Záměry představují způsob, jak zařadit text od uživatelů. Pokud máte aplikaci lidských zdrojů, který má dvě funkce: nejprve který pomůže uživatelům najít a použijte pro úlohy a druhý k nalezení formuláře žádosti o úlohy, tyto dva různé _záměry_ zarovnat následující příkazy:

|Záměr|Příklad od uživatele<br>označuje jako _utterance_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

1. Po vytvoření aplikace je na **záměry** stránku **sestavení** oddílu. Vyberte **Create new intent** (Vytvořit nový záměr). 

    [![Vyberte tlačítko Vytvořit nový záměru](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Zadejte název záměru `FindForm` a vyberte **provádí**.

    ![Zadejte název FindForm záměru](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-example-utterance"></a>Přidejte příklad utterance 

Po vytvoření záměr, dalším krokem je přidání projevů příklad. Toto je text zadaný uživatelem v chatovací robot nebo v jiné klientské aplikace, která se mapuje na záměr LUIS záměru uživatele text. 

Pro tuto aplikaci příklad `FindForm` záměru, příklad projevy bude obsahovat číslo formuláře, což je důležité informace v rámci utterance, že klientská aplikace potřebuje v pořadí fullfil žádost uživatele. 

Přidejte následující projevy patnáct příklad do `FindForm` záměr. 

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

Tyto projevy příkladu lišit, následujícími způsoby:

* Délka utterance
* Interpunkce
* Volba aplikace Word
* příkaz čas (je, byla, bude)
* pořadí slov

[![Zadejte projevy příklad pro FindForm záměr](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-regular-expression-entity"></a>Vytvoření entity regulárního výrazu 

Pokud chcete získat formuláře číslo vrácen jako část předpovědi odpověď modulu runtime, formulář nemá být označený jako entity. Protože text čísla formuláře je vysoce strukturovaná, může být označený, pomocí regulárních výrazů entity. Vytvoření entity pomocí následujících kroků. 

1. Vyberte **entity** z levé navigační nabídky. 

1. Vyberte **vytvořit novou entitu** na stránce entity.

1. Zadejte název `Human Resources Form Number`, vyberte **regulární výraz** entity typu a zadejte regulární výraz `hrf-[0-9]{6}`. To odpovídá literálních znaků, `hrf-`a umožňuje přesně 6 číslic. 

    ![Zadejte informace o entitách entity regulárního výrazu](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Vyberte **Done** (Hotovo). 

## <a name="add-example-utterances-to-none-intent"></a>Přidání projevů příklad na hodnotu None záměru

**Žádný** záměr je záložní záměr a by nemělo být ponecháno prázdné. Cílem tohoto je, musí mít 1 utterance pro každých 10 ve zbývající části záměry aplikace. 

**Žádný** na záměr projevů příklad by měla být mimo doménu aplikace vašeho klienta. 

1. Vyberte **záměry** v levé nabídce vyberte **žádný** ze seznamu záměry.

1. Následující příklad projevy přidejte k příslušnému záměru:

    |Jeden příklad záměr projevů|
    |--|
    |Barking dogs are annoying (Štěkající psi jsou otravní)|
    |Order a pizza for me (Objednej mi pizzu)|
    |Penguins in the ocean (Tučňáci v oceánech)|

    Pro tuto aplikaci lidských zdrojů těchto projevů příkladu jsou mimo doménu. Pokud vaši doménu lidských zdrojů, včetně zvířata, potravin nebo oceánských pak tyto projevy příklad by neměl nejde použít **žádný** záměr. 

## <a name="train-the-app"></a>Trénování aplikace

V pravém horním navigačním panelu vyberte **trénování** použít záměr a entity model změní na aktuální verzi aplikace. 

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Podívejte se na entitu regulárních výrazů v příkladu projevy

1. Zkontrolujte entity je ve **FindForm** záměru tak, že vyberete **záměry** v levé nabídce vyberte **FindForm** záměr. 

    Entita je označeno, ve kterém se zobrazuje v projevy příklad. Pokud chcete zobrazit původní text, ne v názvu entity přepněte **zobrazení entity** z panelu nástrojů.

    [![Všechny projevy příklad označena s entitami](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Otestovat novou aplikaci s interaktivní testovací podokno

Pomocí interaktivního **Test** podokně portálu LUIS k ověření entity se extrahují z nové projevy aplikace nebylo dosud nikdy.

1. Vyberte **Test** v nabídce v pravé horní části.

1. Přidat nový utterance stiskněte klávesu Enter:

    ```Is there a form named hrf-234098```

    ![Nové utterance testů v testovací podokno](./media/get-started-portal-build-app/test-new-utterance.png)

    Horní předpovědět záměrem je správně **FindForm** s více než 90 % spolehlivosti (0.977) a **číslo formuláře lidských zdrojů** entity se extrahuje hodnotu hrf 234098. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Až budete hotovi, v tomto rychlém startu a nejste dalšímu rychlému startu pokračovat, vyberte **Moje aplikace** v horní navigační nabídce. Poté vyberte zaškrtávací políčko vlevo aplikace ze seznamu a vyberte **odstranit** z kontextu nástrojů nad seznamem. 

[![Odstranit aplikaci ze seznamu aplikací](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [2. Nasazení aplikace](get-started-portal-deploy-app.md)