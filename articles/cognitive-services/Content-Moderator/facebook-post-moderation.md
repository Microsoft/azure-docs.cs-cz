---
title: 'Kurz: Moderování obsahu na Facebooku – Moderátor obsahu'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se dozvíte, jak používat moderátorobsahu založeného na strojovém učení, který pomáhá moderovat příspěvky a komentáře na Facebooku.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 9f25c845302d62e3bc9e230b4a6f8f2669f4ac35
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774270"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Kurz: Moderování příspěvků a příkazů na Facebooku pomocí Moderátora obsahu Azure

V tomto kurzu se dozvíte, jak pomocí Moderátora obsahu Azure moderovat příspěvky a komentáře na stránce Facebooku. Facebook odešle obsah zveřejněný návštěvníky služby Content Moderator. Pracovní postupy Moderátor obsahu pak buď publikují obsah, nebo vytvoří recenze v nástroji Revize v závislosti na skóre obsahu a prahových hodnotách. Podívejte se na [ukázkové video sestavení 2017,](https://channel9.msdn.com/Events/Build/2017/T6033) kde najdete pracovní příklad tohoto scénáře.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření týmu Content Moderatoru.
> * Vytvoření funkcí Azure Functions, které naslouchají událostem protokolu HTTP od Content Moderatoru a Facebooku.
> * Propojte stránku na Facebooku s moderátorem obsahu pomocí aplikace Facebook.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

Tento diagram znázorňuje každou součást tohoto scénáře:

![Diagram obsahu Moderátor přijímání informací z Facebooku přes "FBListener" a odesílání informací prostřednictvím "CMListener"](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> V roce 2018 Facebook zavedl přísnější zásady prověřování facebookových aplikací. Pokud vaše aplikace nebyla zkontrolována a schválena týmem pro kontrolu Facebooku, nebudete moci dokončit kroky tohoto kurzu.

## <a name="prerequisites"></a>Požadavky

- Klíč předplatného Content Moderatoru. Podle pokynů v [tématu Vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) se přihlaste ke službě Content Moderator a získejte klíč.
- [Facebook účet](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Vytvoření revizního týmu

Pokyny k registraci [nástroje Revize moderátora obsahu](https://contentmoderator.cognitive.microsoft.com/) na webu naleznete v rychlém spuštění nástroje [Vyzkoušet obsah](quick-start.md) a vytvořit tým pro recenze. Poznamenejte si hodnotu **ID týmu** na stránce **Přihlašovací údaje.**

## <a name="configure-image-moderation-workflow"></a>Konfigurace pracovního postupu moderování obrázků

K vytvoření vlastního pracovního postupu image naleznete průvodce [Definování, testování a použití pracovních postupů.](review-tool-user-guide/workflows.md) Moderátor obsahu použije tento pracovní postup k automatické kontrole obrázků na Facebooku a odeslání některých do nástroje Revize. Poznamenejte si **název**pracovního postupu .

## <a name="configure-text-moderation-workflow"></a>Konfigurace pracovního postupu moderování textu

Znovu naleznete v příručce [Definovat, testovat a používat pracovní postupy;](review-tool-user-guide/workflows.md) tentokrát vytvořte vlastní textový pracovní postup. Moderátor obsahu použije tento pracovní postup k automatické kontrole textového obsahu. Poznamenejte si **název**pracovního postupu .

![Konfigurace pracovního postupu textu](images/text-workflow-configure.PNG)

Otestujte svůj pracovní postup pomocí tlačítka **Spustit pracovní postup.**

![Testování pracovního postupu textu](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Vytvoření funkcí Azure Functions

Přihlaste se k [portálu Azure a](https://portal.azure.com/) postupujte takto:

1. Vytvořte aplikaci funkcí Azure Function App, jak je to zobrazeno na stránce [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
1. Přejděte do nově vytvořené aplikace funkce.
1. V aplikaci přejděte na kartu **Funkce platformy** a vyberte **Konfigurace**. V části **Nastavení aplikace** na další stránce vyberte Nastavení **nové aplikace** a přidejte následující páry klíč/hodnota:
    
    | Název nastavení aplikace | value   | 
    | -------------------- |-------------|
    | cm:TeamId   | TeamId Content Moderatoru.  | 
    | cm:SubscriptionKey | Klíč předplatného Content Moderatoru – viz [přihlašovací údaje](review-tool-user-guide/credentials.md). |
    | cm:Region | Název oblasti Content Moderatoru bez mezer. Najdete to v poli **Umístění** na kartě **Přehled** vašeho prostředku Azure.|
    | cm:ImageWorkflow | Název pracovního postupu ke spuštění na obrázcích. |
    | cm:TextWorkflow | Název pracovního postupu pro spuštění na textu. |
    | cm:CallbackEndpoint | Adresa URL aplikace CMListener Function App, kterou vytvoříte později v této příručce |
    | fb:VerificationToken | Tajný token, který vytvoříte a který se používá k odběru událostí kanálu na Facebooku. |
    | fb:PageAccessToken | Přístupovému tokenu Facebook Graph API nekončí platnost a povoluje se pomocí něho používání funkcí skrytí a odstranění příspěvků vaším jménem. Tento žeton získáte později. |

    V horní části stránky klikněte na tlačítko **Uložit.**

1. Vraťte se na kartu Funkce **+** **platformy.** **New function** Funkce, kterou se chystáte vytvořit, obdrží události z Facebooku.

    ![Podokno Funkce Azure se zvýrazněným tlačítkem Přidat funkci](images/new-function.png)

    1. Klikněte na dlaždici, která **říká, http aktivační událost**.
    1. Zadejte název **FBListener**. Pole **Úroveň autorizace** by mělo být nastavené na možnost **Funkce**.
    1. Klikněte na **Vytvořit**.
    1. Nahraďte obsah **souboru run.csx** obsahem z **fblisteneru/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Vytvořte novou aktivační funkci **http** s názvem **CMListener**. Tato funkce přijímá události od Content Moderatoru. Nahraďte obsah **souboru run.csx** obsahem **cmlistener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Konfigurace Facebookové stránky a aplikace pro Facebook

1. Vytvořte aplikaci pro Facebook.

    ![facebook developer stránka](images/facebook-developer-app.png)

    1. Přejděte na [web pro vývojáře Facebooku](https://developers.facebook.com/).
    1. Klikněte na **My Apps** (Moje aplikace).
    1. Přidejte novou aplikaci.
    1. pojmenujte to něco
    1. Vybrat **webhooky -> Nastavit**
    1. V rozevírací nabídce vyberte **Stránka** a vyberte **Přihlásit se k odběru tohoto objektu.**
    1. Zadejte **FBListener URL** jako adresu URL pro zpětné volání a **Token ověření**, který jste nakonfigurovali v části **nastavení aplikace Function App**.
    1. Jakmile se přihlásíte k odběru, posuňte se dolů na informační kanál a vyberte **Přihlásit k odběru**.
    1. Kliknutím na tlačítko **Test** na řádku **informačního kanálu** odešlete testovací zprávu do funkce FBListener Azure a stiskněte tlačítko **Odeslat na můj server.** Měli byste vidět požadavek přijatý na fblistener.

1. Vytvořte Facebookovou stránku.

    > [!IMPORTANT]
    > V roce 2018 Facebook zavedl přísnější prověřování aplikací Facebooku. Oddíly 2, 3 a 4 nebudete moci spustit, pokud vaše aplikace nebyla zkontrolována a schválena týmem pro kontrolu na Facebooku.

    1. Přejděte na [Facebook](https://www.facebook.com/bookmarks/pages) a vytvořte **novou Facebookovou stránku**.
    1. Povolte aplikaci pro Facebook přístup k této stránce pomocí následujících kroků:
        1. Přejděte do [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        1. Vyberte **možnost Aplikace**.
        1. Vyberte **Page Access Token** (přístupový token stránky) a odešlete požadavek **GET**.
        1. V odpovědi klikněte na tlačítko **Page ID** (ID stránky).
        1. Nyní přidejte **/subscribed_apps** do adresy URL a odešlete požadavek **GET** (prázdnou odpověď).
        1. Odešlete požadavek **POST**. Obdržíte odpověď jako **success: true** (úspěšné).

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

Toto řešení odešle všechny obrázky a texty zveřejněné na vaší Facebookové stránce do Content Moderatoru. Potom jsou vyvolány pracovní postupy, které jste nakonfigurovali dříve. Obsah, který nesplňuje kritéria definovaná v pracovních postupech, bude předán do recenzí v rámci nástroje pro kontrolu. Zbytek obsahu se publikuje automaticky.

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte program pro analýzu bitových kopií produktů za účelem jejich označení podle typu produktu a povolení týmu pro kontrolu činit informovaná rozhodnutí o moderování obsahu. Dále se dozvíte více o podrobnostech moderování obrazu.

> [!div class="nextstepaction"]
> [Moderování obrázků](./image-moderation-api.md)
