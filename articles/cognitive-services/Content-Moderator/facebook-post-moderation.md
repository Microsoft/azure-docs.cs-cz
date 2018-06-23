---
title: Facebook obsahu přerušování s Azure obsah moderátora | Microsoft Docs
description: Střední Facebook stránek s strojové učení na základě obsahu moderátora
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 316c7212c30e9fe1151cdf5ceabf875439ad4c65
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342526"
---
# <a name="facebook-content-moderation-with-content-moderator"></a>Facebook obsahu přerušování s moderátora obsahu

V tomto kurzu jsme Naučte se používat na základě machine learning obsahu moderátora pomohou střední hraniční sítě Facebook a komentáře.

Tento kurz vás provede tyto kroky:

1. Vytvoření obsahu moderátora týmu.
2. Vytvoření funkce Azure, který naslouchat události protokolu HTTP z obsahu moderátora a Facebook.
3. Vytvoření sítě Facebook stránky a aplikace a připojte ho k obsahu moderátora.

Poté, co jsme se dokončí, odešle Facebook obsah vystavil návštěvníky moderátorovi obsah. Podle prahovou hodnotu shody, vaše pracovní postupy moderátora obsah buď publikovat obsah nebo vytvořte recenze v rámci nástroje revize. 

Následující obrázek znázorňuje stavební bloky řešení.

![Moderování příspěvků na Facebooku](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Vytvoření týmu obsahu moderátora

Odkazovat [rychlý Start](quick-start.md) stránky zaregistrovat obsahu moderátora a vytvoření týmu.

## <a name="configure-image-moderation-workflow-threshold"></a>Konfigurace pracovního postupu přerušování bitové kopie (prahová hodnota)

Odkazovat [pracovních](review-tool-user-guide/workflows.md) můžete nakonfigurovat vlastní image pracovního postupu (prahovou hodnotu). Všimněte si, pracovní postup **název**.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Konfigurace pracovního postupu přerušování text (prahová hodnota)

Použijte postup podobně jako [pracovních](review-tool-user-guide/workflows.md) můžete nakonfigurovat prahové hodnoty vlastní text a pracovní postup. Všimněte si, pracovní postup **název**.

![Konfigurace pracovního postupu textu](images/text-workflow-configure.PNG)

Pracovní postup otestujte pomocí tlačítko "Workflow spustit".

![Pracovní postup testovacího textu](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Vytvoření Azure Functions

Přihlaste se k [portálu pro správu Azure](https://portal.azure.com/) k vytvoření Azure Functions. Postupujte následovně:

1. Vytvoření aplikace funkce Azure, jak je znázorněno na [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) stránky.
2. Otevřete nově vytvořený aplikaci funkce.
3. V aplikaci, přejděte na **platformy funkce -> Nastavení aplikace**
4. Definujte následující [nastavení aplikace](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> **Cm: oblast** by měl být název oblasti (bez žádné mezery).
> Například **westeurope**, není západní Evropa, **westcentralus**, není – Západ střední USA a tak dále.
>

| Nastavení aplikace | Popis   | 
| -------------------- |-------------|
| cm:TeamId   | Vaše TeamId obsahu moderátora  | 
| cm:SubscriptionKey | Svůj klíč obsahu moderátora předplatného – viz [přihlašovací údaje](/review-tool-user-guide/credentials.md) | 
| cm:region | Vaše obsahu moderátora název oblasti, bez mezer. Další informace v předchozí poznámce. |
| cm:ImageWorkflow | Název pracovního postupu ke spuštění na bitové kopie |
| cm:TextWorkflow | Název pracovního postupu ke spuštění na Text |
| cm:CallbackEndpoint | Adresa URL pro aplikaci CMListener funkce, které vytvoříte v této příručce |
| FB:VerificationToken | Tajný token, také použít k odběru k síti Facebook kanálu události |
| FB:PageAccessToken | Přístupový token služby Facebook grafu rozhraní api, nemá prošlou platnost a umožňuje funkce skrýt nebo odstranění příspěvcích vaším jménem. |

5. Vytvořte novou **HttpTrigger CSharp** funkce s názvem **FBListener**. Tato funkce přijímá události ze sítě Facebook. Tato funkce vytvořte pomocí následujících kroků:

    1. Zachovat [vytvoření funkce Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) stránky otevřete pro referenci.
    2. Klikněte **+** přidat k vytvoření nové funkce.
    3. Namísto integrované šablony, vyberte **začít pracovat na svoji vlastní nebo vlastní funkci** možnost.
    4. Klikněte na dlaždici, která uvádí, že **HttpTrigger CSharp**.
    5. Zadejte název **FBListener**. **Oprávnění na úrovni** pole musí být nastavena na **funkce**.
    6. Klikněte na možnost **Vytvořit**.
    7. Nahraďte obsah **run.csx** obsahem z [ **FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Vytvořte novou **HttpTrigger CSharp** funkce s názvem **CMListener**. Tato funkce přijímá události ze sítě Facebook. Postupujte podle těchto kroků můžete vytvořit tuto funkci.

    1. Zachovat [vytvoření funkce Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) stránky otevřete pro referenci.
    2. Klikněte **+** přidat k vytvoření nové funkce.
    3. Namísto integrované šablony, vyberte **začít pracovat na svoji vlastní nebo vlastní funkci** možnost.
    4. Klikněte na dlaždici, která uvádí, že **HttpTrigger CSharp**
    5. Zadejte název **CMListener**. **Oprávnění na úrovni** pole musí být nastavena na **funkce**.
    6. Klikněte na možnost **Vytvořit**.
    7. Nahraďte obsah **run.csx** obsahem z [ **CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Konfigurace sítě Facebook stránky a aplikace
1. Vytvoření aplikace pro Facebook.

    1. Přejděte do [lokality vývojáře Facebook](https://developers.facebook.com/)
    2. Klikněte na **Moje aplikace**.
    3. Přidejte novou aplikaci.
    4. Vyberte **Webhooky -> Get spuštění**
    5. Vyberte **stránky -> přihlásit k odběru do tohoto tématu**
    6. Zadejte **FBListener Url** jako adresu URL zpětného volání a **ověření tokenu** jste nakonfigurovali v části **nastavení aplikace – funkce**
    7. Jakmile se předplatné, posuňte se dolů a informační kanál a vyberte **přihlášení k odběru**.

2. Vytvořte stránku služby Facebook.

    1. Přejděte na [Facebook](https://www.facebook.com/bookmarks/pages) a vytvořte **nová stránka Facebook**.
    2. Povolit aplikaci Facebook pro přístup k této stránce pomocí následujících kroků:
        1. Přejděte na [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        2. Vyberte **aplikace**.
        3. Vyberte **tokenu přístupu stránky**, odeslání **získat** požadavku.
        4. Klikněte **ID stránky** v odpovědi.
        5. Teď připojit **/subscribed_apps** na adresu URL a odesílání **získat** požadavku (prázdnou odpověď).
        6. Odeslání **Post** požadavku. Získat odpověď jako **Úspěch: true**.

3. Vytvoření-vypršení platnosti tokenu přístup k rozhraní Graph API.

    1. Přejděte na [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
    2. Vyberte **aplikace** možnost.
    3. Vyberte **získat přístup k tokenu uživatele** možnost.
    4. V části **vyberte oprávnění**, vyberte **manage_pages** a **publish_pages** možnosti.
    5. Budeme používat **přístupový token** (krátkou životnost tokenu) v dalším kroku.

4. Používáme Postman pro několika dalších krocích.

    1. Otevřete **Postman** (nebo ji získat [sem](https://www.getpostman.com/)).
    2. Importujte tyto dva soubory:
        1. [Kolekce postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Prostředí postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Aktualizace těchto proměnných prostředí:
    
    | Klíč | Hodnota   | 
    | -------------------- |-------------|
    | appId   | Vložit vaší sítě Facebook identifikátor aplikace zde  | 
    | appSecret | Vložit zde tajný klíč pro aplikaci Facebook | 
    | short_lived_token | Vložit zkrátí uživatele přístupový token, který jste vygenerovali v předchozím kroku |
    4. Teď spustit 3 rozhraní API uvedené v kolekci: 
        1. Vyberte **vygenerovat Long-Lived přístup na Token** a klikněte na tlačítko **odeslat**.
        2. Vyberte **získat ID uživatele** a klikněte na tlačítko **odeslat**.
        3. Vyberte **získat Token trvalé stránky na přístup** a klikněte na tlačítko **odeslat**.
    5. Kopírování **access_token** hodnotu z odpovědi a přiřadit ji k nastavení aplikace **fb:PageAccessToken**.

A to je vše!

Řešení odešle všechny obrázky a text obsahu moderátorovi odeslány na stránku služby Facebook. Pracovní postupy, které jste dříve nakonfigurovali jsou vyvolány. Obsah, který nesplňuje kritériím definované ve výsledcích pracovních postupů v recenze v rámci nástroje Kontrola. Zbývající obsah zveřejnění.

## <a name="license"></a>Licence

Všechny sady Microsoft kognitivní služby SDK a ukázky licenci s licencí MIT. Další podrobnosti najdete v tématu [licence](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Pravidla chování vývojáře

Vývojáře, kteří používají kognitivní služeb, včetně Tato klientská knihovna pro & ukázce se očekává, postupujte podle "Vývojáře kód z chování pro Microsoft kognitivní služby", který naleznete na http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Další postup

1. [Podívejte se na ukázku (video)](https://channel9.msdn.com/Events/Build/2017/T6033) tohoto řešení od společnosti Microsoft sestavení 2017.
1. [Facebook ukázce na Githubu](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
