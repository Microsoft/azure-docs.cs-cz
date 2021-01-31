---
title: 'Kurz: moderovaná Facebooková obsahu – Content Moderator'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se naučíte, jak pomocí Content Moderator založeného na strojovém učení pomáhat moderovaným příspěvkům a komentářům na Facebooku.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: pafarley
ms.openlocfilehash: f3b43ed6a86276b308599f9091d581423b0f363c
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220986"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Kurz: střední příspěvky a příkazy Facebooku pomocí Azure Content Moderator

V tomto kurzu se naučíte, jak používat Azure Content Moderator k tomu, abyste se seznámili s příspěvky a komentáři na stránce Facebook. Facebook pošle obsah publikovaný návštěvníkům do služby Content Moderator. V závislosti na hodnocení obsahu a prahových hodnotch pak vaše Content Moderator pracovní postupy publikují obsah nebo vytvoří recenze v rámci nástroje pro kontrolu. Pracovní příklad tohoto scénáře najdete v [ukázkovém videu Build 2017](https://channel9.msdn.com/Events/Build/2017/T6033) .

> [!IMPORTANT]
> V 2018 byla Facebook implementovala přísnější zásady dozvíte ČSFD pro aplikace Facebook. Kroky tohoto kurzu nebudete moci dokončit, pokud vaše aplikace nebyla přezkoumána a schválena v rámci týmu pro kontrolu Facebooku.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření týmu Content Moderatoru.
> * Vytvoření funkcí Azure Functions, které naslouchají událostem protokolu HTTP od Content Moderatoru a Facebooku.
> * Propojte stránku Facebooku s Content Moderator pomocí aplikace Facebook.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

Tento diagram znázorňuje jednotlivé komponenty tohoto scénáře:

![Diagram Content Moderator přijímání informací z Facebooku prostřednictvím "FBListener" a odesílání informací prostřednictvím "CMListener"](images/tutorial-facebook-moderation.png)

## <a name="prerequisites"></a>Požadavky

- Klíč předplatného Content Moderatoru. Podle pokynů v části [Vytvoření účtu Cognitive Services](../cognitive-services-apis-create-account.md) se přihlaste k odběru služby Content moderator a Získejte svůj klíč.
- [Účet Facebook](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Vytvořit tým pro revize

Pokyny, jak se zaregistrovat do [Nástroje pro kontrolu Content moderator](https://contentmoderator.cognitive.microsoft.com/) a vytvořit tým pro kontrolu, najdete v [Content Moderatorm](quick-start.md) rychlém startu na webu. Poznamenejte si hodnotu **ID týmu** na stránce **přihlašovací údaje** .

## <a name="configure-image-moderation-workflow"></a>Pracovní postup konfigurace pro moderování obrázků

Postup vytvoření vlastní image pracovního postupu najdete v průvodci [definováním, otestováním a používáním pracovních postupů](review-tool-user-guide/workflows.md) . Content Moderator použije tento pracovní postup k automatické kontrole imagí na Facebooku a odeslání některých do nástroje pro kontrolu. Poznamenejte si **název** pracovního postupu.

## <a name="configure-text-moderation-workflow"></a>Pracovní postup konfigurace moderování textu

Znovu si přečtěte téma průvodce [definováním, otestováním a používáním pracovních postupů](review-tool-user-guide/workflows.md) . Tentokrát vytvořte vlastní textový pracovní postup. Content Moderator použije tento pracovní postup k automatické kontrole obsahu textu. Poznamenejte si **název** pracovního postupu.

![Konfigurace pracovního postupu textu](images/text-workflow-configure.PNG)

Otestujte pracovní postup pomocí tlačítka **Spustit pracovní postup** .

![Testování pracovního postupu textu](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Vytvoření funkcí Azure Functions

Přihlaste se k [Azure Portal](https://portal.azure.com/) a proveďte následující kroky:

1. Vytvořte aplikaci funkcí Azure Function App, jak je to zobrazeno na stránce [Azure Functions](../../azure-functions/functions-create-function-app-portal.md).
1. Přejít na nově vytvořenou Function App.
1. V aplikaci otevřete kartu **funkce platformy** a vyberte **Konfigurace**. V části **nastavení aplikace** na další stránce vyberte možnost **nové nastavení aplikace** a přidejte následující páry klíč/hodnota:
    
    | Název nastavení aplikace | hodnota   | 
    | -------------------- |-------------|
    | `cm:TeamId`   | TeamId Content Moderatoru.  | 
    | `cm:SubscriptionKey` | Klíč předplatného Content Moderatoru – viz [přihlašovací údaje](./review-tool-user-guide/configure.md#credentials). |
    | `cm:Region` | Název oblasti Content Moderatoru bez mezer. Tento název najdete v poli **umístění** na kartě **Přehled** prostředku Azure.|
    | `cm:ImageWorkflow` | Název pracovního postupu ke spuštění na obrázcích. |
    | `cm:TextWorkflow` | Název pracovního postupu pro spuštění na textu. |
    | `cm:CallbackEndpoint` | Adresa URL Function App CMListener, kterou vytvoříte později v této příručce |
    | `fb:VerificationToken` | Tajný token, který vytvoříte, který se používá k přihlášení k odběru událostí kanálu Facebooku |
    | `fb:PageAccessToken` | Přístupovému tokenu Facebook Graph API nekončí platnost a povoluje se pomocí něho používání funkcí skrytí a odstranění příspěvků vaším jménem. Tento token se zobrazí v pozdějším kroku. |

    Klikněte na tlačítko **Uložit** v horní části stránky.

1. Vraťte se na kartu **funkce platformy** . Pomocí **+** tlačítka v levém podokně zobrazte **nové podokno funkce** . Funkce, kterou se chystáte vytvořit, bude přijímat události z Facebooku.

    ![Podokno Azure Functions s zvýrazněným tlačítkem přidat funkci](images/new-function.png)

    1. Klikněte na dlaždici, která říká **Trigger http**.
    1. Zadejte název **FBListener**. Pole **Úroveň autorizace** by mělo být nastavené na možnost **Funkce**.
    1. Klikněte na **Vytvořit**.
    1. Nahraďte obsah rutiny **Run. csx** obsahem z **FbListener/run. csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Vytvořte novou funkci **triggeru http** s názvem **CMListener**. Tato funkce přijímá události od Content Moderatoru. Nahraďte obsah rutiny **Run. csx** obsahem z **CMListener/run. csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Konfigurace Facebookové stránky a aplikace pro Facebook

1. Vytvořte aplikaci pro Facebook.

    ![Stránka pro vývojáře na Facebooku](images/facebook-developer-app.png)

    1. Přejděte na [web pro vývojáře Facebooku](https://developers.facebook.com/).
    1. Přejít na **Moje aplikace**
    1. Přidejte novou aplikaci.
    1. Zadat název
    1. Vyberte **Webhooky – nastavení >**
    1. V rozevírací nabídce vyberte **stránku** a vyberte **přihlášení k odběru tohoto objektu** .
    1. Zadejte **FBListener URL** jako adresu URL pro zpětné volání a **Token ověření**, který jste nakonfigurovali v části **nastavení aplikace Function App**.
    1. Jakmile se přihlásíte k odběru, posuňte se dolů na informační kanál a vyberte **Přihlásit k odběru**.
    1. Výběrem tlačítka **test** na řádku **informačního kanálu** odešlete zkušební zprávu do FBListener funkce Azure a potom stiskněte tlačítko **Odeslat na server** . Měla by se zobrazit žádost o přijetí v FBListener.

1. Vytvořte Facebookovou stránku.

    > [!IMPORTANT]
    > V 2018 byla Facebook implementovala přísnější dozvíte ČSFD aplikací z Facebooku. Pokud vaše aplikace nebyla přezkoumána a schválena v rámci týmu pro kontrolu Facebooku, nebudete moct spouštět oddíly 2, 3 a 4.

    1. Přejděte na [Facebook](https://www.facebook.com/bookmarks/pages) a vytvořte **novou Facebookovou stránku**.
    1. Povolte aplikaci pro Facebook přístup k této stránce pomocí následujících kroků:
        1. Přejděte do [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        1. Vyberte možnost **aplikace**.
        1. Vyberte **Page Access Token** (přístupový token stránky) a odešlete požadavek **GET**.
        1. V odpovědi vyberte **ID stránky** .
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

Toto řešení odešle všechny obrázky a texty zveřejněné na vaší Facebookové stránce do Content Moderatoru. Pak jsou vyvolány pracovní postupy, které jste nakonfigurovali dříve. Obsah, který nepředává kritéria definovaná v pracovních postupech, se předává kontrolám v rámci nástroje pro kontrolu. Zbytek obsahu se bude publikovat automaticky.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nastavili program pro analýzu imagí produktů, označíte je podle typu produktu a umožníte týmu kontroly, aby se o moderování obsahu rozhodli. V dalším kroku se dozvíte více o podrobnostech o moderování imagí.

> [!div class="nextstepaction"]
> [Moderování obrázků](./image-moderation-api.md)