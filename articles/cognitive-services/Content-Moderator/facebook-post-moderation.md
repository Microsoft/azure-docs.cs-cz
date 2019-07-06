---
title: 'Kurz: Moderovat obsah Facebooku – Content Moderator'
titlesuffix: Azure Cognitive Services
description: V tomto kurzu se dozvíte, jak používat na základě machine learningu Content Moderator moderování příspěvků na Facebooku a komentáře.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: dd06330e82850cc44bc0f4d36ba7caf596ace939
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603502"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Kurz: Moderování příspěvků na Facebooku a příkazy s Azure Content Moderator

V tomto kurzu se dozvíte, jak pomocí Azure Content Moderator moderování příspěvků a komentáře na stránku Facebooku. Facebook odešle obsah publikovaný ze strany návštěvníků ke službě Content Moderator. Pak vašich pracovních postupů Content Moderator bude buď publikovat obsah nebo vytvářet revize v rámci nástroje pro recenze, v závislosti na obsahu skóre a prahové hodnoty. Zobrazit [Build 2017 – ukázkové video](https://channel9.msdn.com/Events/Build/2017/T6033) pro funkční příklad tohoto scénáře.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření týmu Content Moderatoru.
> * Vytvoření funkcí Azure Functions, které naslouchají událostem protokolu HTTP od Content Moderatoru a Facebooku.
> * Propojte Facebookové stránce Content Moderator pomocí aplikace pro Facebook.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Tento diagram znázorňuje jednotlivé komponenty v tomto scénáři:

![Diagram Content Moderator přijímat informace ze sítě Facebook prostřednictvím "FBListener" a odesílání informací prostřednictvím "CMListener"](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> V roce 2018 Facebooku implementované přísnější prověřování aplikace pro Facebook. Nebudete mít k dokončení kroků v tomto kurzu, pokud vaše aplikace nebyla byly zkontrolován a schválen revize týmem Facebooku.

## <a name="prerequisites"></a>Požadavky

- Klíč předplatného Content Moderatoru. Postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k odběru služby Content Moderator a získejte klíč.
- A [Facebookový účet](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Vytvořit tým kontroly

Odkazovat [zkuste Content Moderator na webu](quick-start.md) rychlý start pro pokyny, jak zaregistrovat [Content Moderator Zkontrolujte nástroj](https://contentmoderator.cognitive.microsoft.com/) a vytvořte tým kontroly. Poznamenejte si **ID týmu** hodnoty na **pověření** stránky.

## <a name="configure-image-moderation-workflow"></a>Konfigurace pracovního postupu pro moderování obrázků

Odkazovat [definovat, testování a pracovních postupů pomocí](review-tool-user-guide/workflows.md) průvodce k vytvoření vlastní image pracovního postupu. Content Moderator použije tento pracovní postup automaticky kontrolu imagí na Facebooku a některé pro nástroj pro recenze odeslat. Poznamenejte si pracovní postup **název**.

## <a name="configure-text-moderation-workflow"></a>Konfigurace pracovního postupu pro moderování textu

Znovu, odkazovat [definovat, testování a pracovních postupů pomocí](review-tool-user-guide/workflows.md) Průvodce; to čas, vytvoření pracovního postupu vlastní text. Content Moderator použije tento pracovní postup k automatické kontrole textový obsah. Poznamenejte si pracovní postup **název**.

![Konfigurace pracovního postupu textu](images/text-workflow-configure.PNG)

Testování pomocí vašeho pracovního postupu **spuštění pracovního postupu** tlačítko.

![Testování pracovního postupu textu](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Vytvoření funkcí Azure Functions

Přihlaste se k [webu Azure portal](https://portal.azure.com/) a postupujte podle těchto kroků:

1. Vytvořte aplikaci funkcí Azure Function App, jak je to zobrazeno na stránce [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
1. Přejděte do nově vytvořené aplikace Function App.
1. V aplikaci, přejděte **funkce platformy** kartě a vyberte **konfigurace**. V **nastavení aplikace** na další stránce vyberte **nové nastavení aplikace** přidáte následující dvojice klíč/hodnota:
    
    | Název nastavení aplikace | value   | 
    | -------------------- |-------------|
    | cm:TeamId   | TeamId Content Moderatoru.  | 
    | cm:SubscriptionKey | Klíč předplatného Content Moderatoru – viz [přihlašovací údaje](review-tool-user-guide/credentials.md). |
    | cm:Region | Název oblasti Content Moderatoru bez mezer. |
    | cm:ImageWorkflow | Název pracovního postupu ke spuštění na obrázcích. |
    | cm:TextWorkflow | Název pracovního postupu pro spuštění na textu. |
    | cm:CallbackEndpoint | Adresa URL pro aplikaci funkcí CMListener, kterou vytvoříte později v tomto průvodci |
    | fb:VerificationToken | Tajný token, který vytvoříte, používá k registraci na Facebook informační kanál událostí |
    | fb:PageAccessToken | Přístupovému tokenu Facebook Graph API nekončí platnost a povoluje se pomocí něho používání funkcí skrytí a odstranění příspěvků vaším jménem. Zobrazí se tento token v pozdějším kroku. |

    Klikněte na tlačítko **Uložit** tlačítko v horní části stránky.

1. Přejděte zpět **funkce platformy** kartu. Použití **+** tlačítko v levém podokně zobrazí se **novou funkci** podokně. Funkce, do které se chystáte vytvořit zobrazí události ze sítě Facebook.

    ![Podokno s Azure Functions se zvýrazněným tlačítkem Přidat funkci.](images/new-function.png)

    1. Kliknutím na dlaždici, která uvádí, že **triggeru Http**.
    1. Zadejte název **FBListener**. Pole **Úroveň autorizace** by mělo být nastavené na možnost **Funkce**.
    1. Klikněte na možnost **Vytvořit**.
    1. Nahraďte obsah **run.csx** s obsahem z **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Vytvořte nový **triggeru Http** funkce s názvem **CMListener**. Tato funkce přijímá události od Content Moderatoru. Nahraďte obsah **run.csx** s obsahem z **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Konfigurace Facebookové stránky a aplikace pro Facebook

1. Vytvořte aplikaci pro Facebook.

    ![stránka na facebooku pro vývojáře](images/facebook-developer-app.png)

    1. Přejděte na [web pro vývojáře Facebooku](https://developers.facebook.com/).
    1. Klikněte na **My Apps** (Moje aplikace).
    1. Přidejte novou aplikaci.
    1. Název
    1. Vyberte **Webhooky -> sady nahoru**
    1. Vyberte **stránky** v rozevírací nabídce a vyberte **přihlášení odběru tohoto objektu**
    1. Zadejte **FBListener URL** jako adresu URL pro zpětné volání a **Token ověření**, který jste nakonfigurovali v části **nastavení aplikace Function App**.
    1. Jakmile se přihlásíte k odběru, posuňte se dolů na informační kanál a vyberte **Přihlásit k odběru**.
    1. Klikněte na **testování** tlačítko **kanálu** řádek odeslat zkušební zprávu FBListener funkce Azure, stačí stisknout **odesílání na tento Server** tlačítko. Měli byste vidět požadavku na vaše FBListener.

1. Vytvořte Facebookovou stránku.

    > [!IMPORTANT]
    > V roce 2018 Facebooku implementované přísnější prověřování aplikace pro Facebook. Nebudete moci být prováděny části 2, 3 a 4, pokud vaše aplikace nebyla byly zkontrolován a schválen revize týmem Facebooku.

    1. Přejděte na [Facebook](https://www.facebook.com/bookmarks/pages) a vytvořte **novou Facebookovou stránku**.
    1. Povolte aplikaci pro Facebook přístup k této stránce pomocí následujících kroků:
        1. Přejděte do [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        1. Vyberte možnost **Application** (Aplikace).
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

Toto řešení odešle všechny obrázky a texty zveřejněné na vaší Facebookové stránce do Content Moderatoru. Poté jsou spuštěny pracovní postupy, které jste nakonfigurovali v předchozích krocích. Obsah, který nepředá vaše kritéria definovaná v pracovních postupech bude předána do revize v rámci nástroje pro recenze. Získá zbývající obsah automaticky publikován.

## <a name="next-steps"></a>Další postup

V tomto kurzu nastavíte programu k analýze obrázků produktů pro účely označování podle typu produktu a povolení tým kontroly se informovaně rozhodnout o moderování obsahu. Další informace o podrobnosti o moderování obrázků v dalším kroku.

> [!div class="nextstepaction"]
> [Moderování obrázků](./image-moderation-api.md)
