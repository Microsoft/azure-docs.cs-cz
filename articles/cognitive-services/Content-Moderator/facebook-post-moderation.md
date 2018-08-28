---
title: Moderování obsahu Facebooku s Azure Content Moderator | Dokumentace Microsoftu
description: Střední Facebook stránek pomocí služby machine learning na základě Content Moderatoru
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 66caea65c21bb1f8bb6efa9b50c917599bb71e2f
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43093973"
---
# <a name="facebook-content-moderation-with-content-moderator"></a>Moderování obsahu Facebooku s Content Moderator

V tomto kurzu jsme zjistěte, jak pomocí na základě machine learningu Content Moderator moderování příspěvků na Facebooku a komentáře.

Tento kurz vás provede tyto kroky:

1. Vytvořte tým Content Moderatoru.
2. Vytvoření funkce Azure, které naslouchat událostem protokolu HTTP z Content Moderator a Facebook.
3. Vytvoření Facebookové stránky a aplikace a propojte jej s Content Moderatorem.

Poté, co jsme se vším hotovi, Facebooku odešle obsah zveřejnil(a) návštěvníkům Content Moderator. Založené na porovnání prahové hodnoty, pracovní postupy Content Moderator buď publikovat obsah nebo vytvářet revize v rámci nástroje pro recenze. 

Následující obrázek znázorňuje stavební kameny nástroje řešení.

![Moderování příspěvků na Facebooku](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Vytvořit tým Content Moderatoru

Odkazovat [rychlý Start](quick-start.md) stránka pro registraci pro Content Moderator a vytvoření týmu.

## <a name="configure-image-moderation-workflow-threshold"></a>Konfigurace pracovního postupu pro moderování obrázků (threshold)

Odkazovat [pracovních postupů](review-tool-user-guide/workflows.md) stránku konfigurace pracovního postupu vlastní image (threshold). Všimněte si, pracovní postup **název**.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Konfigurace pracovního postupu pro moderování textu (threshold)

Použijte postup podobný [pracovních postupů](review-tool-user-guide/workflows.md) stránku konfigurace prahovou hodnotu vlastní text a pracovního postupu. Všimněte si, pracovní postup **název**.

![Konfigurace pracovního postupu Text](images/text-workflow-configure.PNG)

Testování pracovního postupu pomocí tlačítka "Spuštění pracovního postupu".

![Pracovní postup testování Text](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Vytvoření funkce Azure

Přihlaste se k [portálu pro správu Azure](https://portal.azure.com/) k vytváření Azure Functions. Postupujte následovně:

1. Vytvořte aplikaci Azure Function App, jak je znázorněno na [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) stránky.
2. Otevřete nově vytvořené aplikace Function App.
3. V aplikaci, přejděte na **platformy funkce -> Nastavení aplikace**
4. Definujte následující [nastavení aplikace](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> **Cm: oblast** by měl být název oblasti (bez mezer).
> Například **westeurope**, ne západní Evropa, **westcentralus**, ne střed USA – západ a tak dále.
>

| Nastavení aplikace | Popis   | 
| -------------------- |-------------|
| cm:TeamId   | Vaše TeamId Content Moderatoru  | 
| cm:SubscriptionKey | Klíč předplatného Content Moderator – viz [přihlašovacích údajů](review-tool-user-guide/credentials.md) | 
| cm:region | Content Moderator oblasti název, bez mezer. Viz předchozí poznámka. |
| cm:ImageWorkflow | Název pracovního postupu ke spuštění na Imagích |
| cm:TextWorkflow | Název pracovního postupu pro spuštění na Text |
| cm:CallbackEndpoint | Adresa URL pro aplikaci funkcí CMListener, kterou vytvoříte později v tomto průvodci |
| FB:VerificationToken | Token tajného kódu používá také k přihlášení k síti Facebook odběru informačního kanálu události |
| FB:PageAccessToken | Přístupový token sítě Facebook graph api, nemá prošlou platnost a povolí používání funkce příspěvky skrýt, odstranění vaším jménem. |

5. Vytvořte nový **HttpTrigger-CSharp** funkce s názvem **FBListener**. Tato funkce přijímá události ze sítě Facebook. Vytvořte tuto funkci pomocí následujících kroků:

    1. Zachovat [vytvoření funkce Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) otevřete pro odkaz na stránku.
    2. Klikněte na tlačítko **+** přidat a vytvořte si nové funkce.
    3. Namísto integrovaných šablon zvolte **začít používat vlastní/vlastní funkce** možnost.
    4. Kliknutím na dlaždici, která uvádí, že **HttpTrigger-CSharp**.
    5. Zadejte název **FBListener**. **Úroveň autorizace** pole by mělo být nastavené **funkce**.
    6. Klikněte na možnost **Vytvořit**.
    7. Nahraďte obsah **run.csx** s obsahem z [ **FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Vytvořte nový **HttpTrigger-CSharp** funkce s názvem **CMListener**. Tato funkce přijímá události z Content Moderatoru. Postupujte podle těchto kroků můžete vytvořit tuto funkci.

    1. Zachovat [vytvoření funkce Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) otevřete pro odkaz na stránku.
    2. Klikněte na tlačítko **+** přidat a vytvořte si nové funkce.
    3. Namísto integrovaných šablon zvolte **začít používat vlastní/vlastní funkce** možnost.
    4. Kliknutím na dlaždici, která uvádí, že **HttpTrigger-CSharp**
    5. Zadejte název **CMListener**. **Úroveň autorizace** pole by mělo být nastavené **funkce**.
    6. Klikněte na možnost **Vytvořit**.
    7. Nahraďte obsah **run.csx** s obsahem z [ **CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Konfigurace Facebookové stránky a aplikace
1. Vytvoření aplikace pro Facebook.

    1. Přejděte [webu vývojáře služby Facebook](https://developers.facebook.com/)
    2. Klikněte na **Moje aplikace**.
    3. Přidejte novou aplikaci.
    4. Vyberte **Webhooky -> Get spuštění**
    5. Vyberte **stránky -> přihlásit k odběru tohoto tématu**
    6. Zadejte **FBListener Url** jako adresu URL zpětného volání a **ověřit Token** jste nakonfigurovali v části **nastavení aplikace Function App**
    7. Jakmile odběru, posuňte se dolů informační kanál a vyberte **odběru**.

2. Vytvořte stránku Facebooku.

    1. Přejděte do [Facebook](https://www.facebook.com/bookmarks/pages) a vytvořit **novou stránku Facebooku**.
    2. Povolte aplikaci Facebook pro přístup k této stránce pomocí následujících kroků:
        1. Přejděte [rozhraní Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        2. Vyberte **aplikace**.
        3. Vyberte **stránky přístupový Token**, odesílání **získat** požadavku.
        4. Klikněte na tlačítko **ID stránky** v odpovědi.
        5. Nyní přidat **/subscribed_apps** na adresu URL a odeslat **získat** požadavku (prázdnou odpověď).
        6. Odeslat **příspěvek** požadavku. Získejte odpovědi jako **Úspěch: true**.

3. Vytvoření bez vypršení platnosti tokenu přístupu rozhraní Graph API.

    1. Přejděte [rozhraní Graph API Explorer](https://developers.facebook.com/tools/explorer/).
    2. Vyberte **aplikace** možnost.
    3. Vyberte **získat přístup k tokenu uživatele** možnost.
    4. V části **oprávnění Select**vyberte **manage_pages** a **publish_pages** možnosti.
    5. Budeme používat **přístupový token** (krátkou životnost tokenu) v dalším kroku.

4. Používáme Postman pro několik dalších kroků.

    1. Otevřít **Postman** (nebo ho získat [tady](https://www.getpostman.com/)).
    2. Naimportujte tyto dva soubory:
        1. [Kolekce postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman prostředí](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Aktualizujte tyto proměnné prostředí:
    
    | Klíč | Hodnota   | 
    | -------------------- |-------------|
    | ID aplikace   | Vložit vaše Facebooku identifikátor aplikace zde  | 
    | appSecret | Vložit sem tajný klíč pro aplikaci Facebook | 
    | short_lived_token | Vložit přístupový token krátkodobý uživatele, který jste vygenerovali v předchozím kroku |
    4. Nyní spusťte 3 API uvedené v kolekci: 
        1. Vyberte **generovat Long-Lived přístup na Token** a klikněte na tlačítko **odeslat**.
        2. Vyberte **získat ID uživatele** a klikněte na tlačítko **odeslat**.
        3. Vyberte **získat trvalé stránky přístupový Token** a klikněte na tlačítko **odeslat**.
    5. Kopírovat **access_token** hodnotu z odpovědi a přiřadit ji k nastavení aplikace **fb:PageAccessToken**.

A to je vše!

Řešení odešle všechny obrázky a text pro Content Moderator zveřejněný na Facebookovou stránku. Pracovní postupy, které jste nakonfigurovali v předchozích krocích jsou vyvolány. Obsah, který nepředá vaše kritéria definovaná v výsledky kontroly v rámci nástroje pro recenze do pracovních postupů. Zbývající část obsahu publikuje.

## <a name="license"></a>Licence

Všechny sady Microsoft Cognitive Services SDK a ukázky jsou licencované s licencí MIT. Další podrobnosti najdete v tématu [licence](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Pravidla chování vývojáře

Vývojáři, kteří používají služeb Cognitive Services, včetně této klientské knihovny a ukázky, očekává se, postupujte "pro vývojáře kód z chování pro Microsoft Cognitive Services", které se nacházejí v http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Další postup

1. [Podívejte se na ukázku (video)](https://channel9.msdn.com/Events/Build/2017/T6033) tohoto řešení od společnosti Microsoft Build 2017.
1. [Facebook ukázka na Githubu](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
