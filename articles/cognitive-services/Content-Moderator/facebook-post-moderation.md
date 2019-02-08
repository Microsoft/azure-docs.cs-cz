---
title: 'Kurz: Moderování obsahu Facebooku - Azure Content Moderator'
titlesuffix: Azure Cognitive Services
description: V tomto kurzu se dozvíte, jak používat na základě machine learningu Content Moderator moderování příspěvků na Facebooku a komentáře.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 86c89164e3ccd5bf5df303b98cf6d336f3916e2b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878048"
---
# <a name="tutorial-facebook-content-moderation-with-content-moderator"></a>Kurz: Moderování obsahu Facebooku s Content Moderator

V tomto kurzu se dozvíte, jak používat na základě machine learningu Content Moderator moderování příspěvků na Facebooku a komentáře.

Tento kurz vás provede následujícími kroky:

1. Vytvoření týmu Content Moderatoru.
2. Vytvoření funkcí Azure Functions, které naslouchají událostem protokolu HTTP od Content Moderatoru a Facebooku.
3. Vytvoření Facebookové stránky a aplikace pro Facebook a jejich propojení pomocí Content Moderatoru.

Po dokončení bude Facebook posílat obsah zveřejněný návštěvníky do Content Moderatoru. Pracovní postupy Content Moderatoru v závislosti na prahových hodnotách buď publikují obsah, nebo vytvoří kontrolu v rámci nástroje pro kontrolu. 

Následující obrázek znázorňuje stavební kameny řešení.

![Moderování příspěvků na Facebooku](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Vytvoření týmu Content Moderatoru

Odkazovat [zkuste Content Moderator na webu](quick-start.md) rychlého startu pro registraci pro Content Moderator a vytvoření týmu.

## <a name="configure-image-moderation-workflow-threshold"></a>Konfigurace pracovního postupu pro moderování obrázků (prahová hodnota)

Na stránce [pracovního postupu](review-tool-user-guide/workflows.md) je možné nakonfigurovat vlastní pracovní postup obrázku (threshold). Všimněte si **názvu** pracovního postupu.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Konfigurace pracovního postupu pro moderování textu (prahová hodnota)

Abyste nakonfigurovali vlastní prahovou hodnotu textu a pracovního postupu, použijte postup podobný tomu na stránce [pracovních postupů](review-tool-user-guide/workflows.md). Všimněte si **názvu** pracovního postupu.

![Konfigurace pracovního postupu textu](images/text-workflow-configure.PNG)

Otestujte pracovní postup pomocí tlačítka Execute Workflow (Spustit pracovní postup).

![Testování pracovního postupu textu](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Vytvoření funkcí Azure Functions

Azure Functions je možné vytvářet po přihlášení k [portálu pro správu Azure](https://portal.azure.com/). Postupujte následovně:

1. Vytvořte aplikaci funkcí Azure Function App, jak je to zobrazeno na stránce [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
2. Otevřete nově vytvořenou aplikaci funkcí Function App.
3. V aplikaci přejděte na **Funkce platformy -> Nastavení aplikace**
4. Definujte následující [nastavení aplikace](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> **Cm: Oblast** by měl být název oblasti (bez mezer).
> Například **západníevropa**, ne Západní Evropa, **usastředozápad**, ne USA – středozápad, a tak dále.
>

| Nastavení aplikace | Popis   | 
| -------------------- |-------------|
| cm:TeamId   | TeamId Content Moderatoru.  | 
| cm:SubscriptionKey | Klíč předplatného Content Moderatoru – viz [přihlašovací údaje](review-tool-user-guide/credentials.md). | 
| cm:Region | Název oblasti Content Moderatoru bez mezer. Viz předchozí poznámka. |
| cm:ImageWorkflow | Název pracovního postupu ke spuštění na obrázcích. |
| cm:TextWorkflow | Název pracovního postupu pro spuštění na textu. |
| cm:CallbackEndpoint | Adresa URL pro aplikaci funkcí Function App CMListener, kterou vytvoříte později v tomto průvodci. |
| fb:VerificationToken | Token tajného kódu, který se používá také k přihlášení k odběru událostí informačního kanálu Facebooku. |
| fb:PageAccessToken | Přístupovému tokenu Facebook Graph API nekončí platnost a povoluje se pomocí něho používání funkcí skrytí a odstranění příspěvků vaším jménem. |

5. Vytvořte novou funkci **HttpTrigger-CSharp** s názvem **FBListener**. Tato funkce přijímá události z Facebooku. Vytvořte tuto funkci pomocí následujících kroků:

    1. Nechte stránku [Vytvoření funkcí Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) otevřenou pro referenci.
    2. Novou funkci vytvoříte kliknutím na tlačítko přidání **+**.
    3. Namísto integrovaných šablon zvolte možnost **Začít na vlastní funkci**.
    4. Klikněte na dlaždici **HttpTrigger-CSharp**.
    5. Zadejte název **FBListener**. Pole **Úroveň autorizace** by mělo být nastavené na možnost **Funkce**.
    6. Klikněte na možnost **Vytvořit**.
    7. Nahraďte obsah souboru **run.csx** obsahem ze souboru [ **FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Vytvořte novou funkci **HttpTrigger-CSharp** s názvem **CMListener**. Tato funkce přijímá události od Content Moderatoru. Tuto funkci vytvořte podle následujících pokynů:

    1. Nechte stránku [Vytvoření funkcí Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) otevřenou pro referenci.
    2. Novou funkci vytvoříte kliknutím na tlačítko přidání **+**.
    3. Namísto integrovaných šablon zvolte možnost **Začít na vlastní funkci**.
    4. Klikněte na dlaždici **HttpTrigger-CSharp**
    5. Zadejte název **CMListener**. Pole **Úroveň autorizace** by mělo být nastavené na možnost **Funkce**.
    6. Klikněte na možnost **Vytvořit**.
    7. Nahraďte obsah souboru **run.csx** obsahem ze souboru [ **CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Konfigurace Facebookové stránky a aplikace pro Facebook
1. Vytvořte aplikaci pro Facebook.

    1. Přejděte na [web pro vývojáře Facebooku](https://developers.facebook.com/).
    2. Klikněte na **My Apps** (Moje aplikace).
    3. Přidejte novou aplikaci.
    4. Vyberte **Webhooks -> Get Started** (Webhooky -> Začínáme).
    5. Vyberte **Page -> Subscribe to this topic** (Stránka -> Přihlásit k odběru tohoto tématu).
    6. Zadejte **FBListener URL** jako adresu URL pro zpětné volání a **Token ověření**, který jste nakonfigurovali v části **nastavení aplikace Function App**.
    7. Jakmile se přihlásíte k odběru, posuňte se dolů na informační kanál a vyberte **Přihlásit k odběru**.

2. Vytvořte Facebookovou stránku.

    1. Přejděte na [Facebook](https://www.facebook.com/bookmarks/pages) a vytvořte **novou Facebookovou stránku**.
    2. Povolte aplikaci pro Facebook přístup k této stránce pomocí následujících kroků:
        1. Přejděte do [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        2. Vyberte možnost **Application** (Aplikace).
        3. Vyberte **Page Access Token** (přístupový token stránky) a odešlete požadavek **GET**.
        4. V odpovědi klikněte na tlačítko **Page ID** (ID stránky).
        5. Nyní přidejte **/subscribed_apps** do adresy URL a odešlete požadavek **GET** (prázdnou odpověď).
        6. Odešlete požadavek **POST**. Obdržíte odpověď jako **success: true** (úspěšné).

3. Vytvořte přístupový token Graph API bez vypršení platnosti.

    1. Přejděte do [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
    2. Vyberte možnost **Application** (Aplikace).
    3. Vyberte možnost **Get User Access Token** (získat token uživatelského přístupu).
    4. V části **Select Permissions** (Výběr oprávnění) vyberte možnosti **manage_pages** a **publish_pages**.
    5. **Přístupový token** (Short Lived Token) využijeme v následujícím kroku.

4. V několika dalších krocích používáme klienta Postman.

    1. Otevřete **Postmana** (nebo ho získejte [zde](https://www.getpostman.com/)).
    2. Naimportujte tyto dva soubory:
        1. [Postman Collection](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman Environment](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Aktualizujte tyto proměnné prostředí:
    
    | Klíč | Hodnota   | 
    | -------------------- |-------------|
    | appId   | Sem vložte identifikátor vaší aplikace pro Facebooku  | 
    | appSecret | Sem vložte tajný klíč vaší aplikace pro Facebooku | 
    | short_lived_token | Sem vložte krátkodobý přístupový token uživatele, který jste vygenerovali v předchozím kroku |
    4. Nyní spusťte 3 rozhraní API uvedené v kolekci: 
        1. Vyberte **Generate Long-Lived Access Token** (generovat dlouhotrvající přístupový token) a klikněte na tlačítko **Send** (Odeslat).
        2. Vyberte **Get User ID** (získat ID uživatele) a klikněte na tlačítko **Send** (Odeslat).
        3. Vyberte **Get Permanent Page Access Token** (získat dlouhotrvající přístupový token stránky) a klikněte na tlačítko **Send** (Odeslat).
    5. Zkopírujte hodnotu **access_tokenu** z odpovědi a přiřaďte ji k nastavení aplikace **fb:PageAccessToken**.

A to je vše!

Toto řešení odešle všechny obrázky a texty zveřejněné na vaší Facebookové stránce do Content Moderatoru. Vyvolají se pracovní postupy, které jste nakonfigurovali v předchozích krocích. Obsah, který nesplní vaše kritéria definovaná ve výsledcích pracovních postupů, bude kontrolován v rámci nástroje pro kontrolu. Zbývající obsah bude publikován.

## <a name="license"></a>Licence

Všechny sady SDK služeb Microsoft Cognitive Services a ukázky jsou licencovány licencí MIT. Další podrobnosti najdete v [LICENCI](https://microsoft.mit-license.org/).

## <a name="next-steps"></a>Další postup

1. [Podívejte se na ukázku (video)](https://channel9.msdn.com/Events/Build/2017/T6033) tohoto řešení z Microsoft Build 2017.
1. [Facebook ukázka na Githubu](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
