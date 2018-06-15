---
title: Autorizovat vývojářským účtům pomocí OAuth 2.0 ve službě Azure API Management | Microsoft Docs
description: Zjistěte, jak se mají autorizovat uživatele, kteří používají OAuth 2.0 ve službě API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: f3611fa4da571dd74d844c7fad45788ece372be4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937228"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Jak se mají autorizovat vývojářským účtům pomocí OAuth 2.0 ve službě Azure API Management
Mnoho rozhraní API podporují [OAuth 2.0](http://oauth.net/2/) zabezpečení rozhraní API a zkontrolujte, zda mají přístup jenom uživatelé platný a jejich pouze přístup k prostředkům, ke kterým máte nárok. Aby bylo možné používat interaktivní vývojářské konzole Azure API Management s takové rozhraní API, služba umožňuje nakonfigurovat instanci služby pro práci s vaší OAuth 2.0 povoleno rozhraní API.

## <a name="prerequisites"> </a>Požadavky
Tato příručka ukazuje, jak nakonfigurovat instanci služby API Management používat autorizace OAuth 2.0 pro vývojáře účty, ale nezobrazuje postup konfigurace poskytovatele OAuth 2.0. Konfigurace pro každého zprostředkovatele OAuth 2.0 je jiné, i když kroky se podobají a vyžaduje řadu informací použít při konfiguraci OAuth 2.0 ve vaší instance služby API Management jsou stejné. Toto téma ukazuje příklady pomocí služby Azure Active Directory jako zprostředkovatel OAuth 2.0.

> [!NOTE]
> Další informace o konfiguraci OAuth 2.0 pomocí služby Azure Active Directory najdete v tématu [WebApp. GraphAPI DotNet] [ WebApp-GraphAPI-DotNet] ukázka.
> 
> 

## <a name="step1"> </a>Konfigurace serveru autorizace OAuth 2.0 ve službě API Management
Začněte tak, že na webu Azure Portal dané služby API Management kliknete na **Portál vydavatele**.

![Portál vydavatele][api-management-management-console]

> [!NOTE]
> Pokud jste instanci služby API Management ještě nevytvořili, přečtěte si téma [vytvoření instance API Management][Create an API Management service instance].
> 
> 

Klikněte na tlačítko **zabezpečení** z **API Management** nabídky na levé straně, klikněte na tlačítko **OAuth 2.0**a potom klikněte na **serveru ověřování přidat**.

![OAuth 2.0][api-management-oauth2]

Po kliknutí na **serveru ověřování přidat**, se zobrazí nový formulář serveru autorizace.

![Nový server][api-management-oauth2-server-1]

Zadejte název a volitelný popis v **název** a **popis** pole. 

> [!NOTE]
> Tato pole se používají k identifikaci serveru ověřování OAuth 2.0 v rámci aktuální instance služby API Management a jejich hodnoty nepocházejí ze serveru OAuth 2.0.
> 
> 

Zadejte **adresa URL stránky registrace klienta**. Tato stránka je, kde uživatelé mohou vytvářet a spravovat své účty a se liší v závislosti na zprostředkovateli OAuth 2.0 používat. **Adresa URL stránky registrace klienta** odkazuje na stránku, uživatelé můžete vytvořit a nakonfigurovat svoje vlastní účty zprostředkovatelů OAuth 2.0, které podporují správu uživatelské účty. Některé organizace nenakonfigurujete nebo tuto funkci použít, i v případě, že ji podporuje zprostředkovateli OAuth 2.0. Pokud váš poskytovatel OAuth 2.0 správu uživatelů pro účty konfigurované nemá, zadejte URL zástupný symbol zde například adresu URL vaší společnosti, nebo adresu URL, jako `https://placeholder.contoso.com`.

Obsahuje další části formuláře **typy udělení autorizačního kódu**, **adresu URL koncového bodu autorizace**, a **metoda autorizace požadavku** nastavení.

![Nový server][api-management-oauth2-server-2]

Zadejte **typy udělení autorizačního kódu** kontrolou požadované typy. **Autorizační kód** je zadána ve výchozím nastavení.

Zadejte **adresu URL koncového bodu autorizace**. Pro Azure Active Directory, bude tato adresa URL vypadat podobně jako následující adresu URL, kde `<client_id>` je nahrazena id klienta, který identifikuje aplikaci na server OAuth 2.0.

`https://login.microsoftonline.com/<client_id>/oauth2/authorize`

**Metoda autorizace požadavku** Určuje, jak bude odeslána žádost o ověření se serverem OAuth 2.0. Ve výchozím nastavení **získat** je vybrána.

V další části je tam, kde **adresu URL koncového bodu Token**, **metody ověřování klienta**, **přístupový token odesílání metoda**, a **výchozí obor** nejsou zadány.

![Nový server][api-management-oauth2-server-3]

Pro server Azure Active Directory OAuth 2.0 **adresu URL koncového bodu Token** bude mít následující formát, kde `<APPID>` má formát `yourapp.onmicrosoft.com`.

`https://login.microsoftonline.com/<APPID>/oauth2/token`

Výchozí nastavení pro **metody ověřování klienta** je **základní**, a **přístupový token odesílání metoda** je **autorizační hlavičky**. Tyto hodnoty jsou nakonfigurované v této části formuláře, spolu s **výchozí obor**.

**Pověření klienta** část obsahuje **ID klienta** a **tajný klíč klienta**, které se získají během procesu vytváření a konfigurace vašeho serveru OAuth 2.0. Jednou **ID klienta** a **tajný klíč klienta** jsou určené **redirect_uri** pro **autorizační kód** se vygeneruje. Tento identifikátor URI, slouží ke konfiguraci adresa URL odpovědi v konfiguraci serveru OAuth 2.0.

![Nový server][api-management-oauth2-server-4]

Pokud **typy udělení autorizačního kódu** je nastaven na **heslo vlastníka prostředku**, **oprávnění hesla vlastníka prostředku** části slouží k určení tyto přihlašovací údaje; v opačném případě můžete toto pole ponecháte prázdné.

![Nový server][api-management-oauth2-server-5]

Po dokončení formuláře klikněte na tlačítko **Uložit** uložit konfiguraci serveru autorizace API Management OAuth 2.0. Po konfiguraci serveru je uložen, můžete nakonfigurovat rozhraní API pro použití této konfigurace, jak je znázorněno v následujícím oddílu.

## <a name="step2"> </a>Konfigurace rozhraní API používat autorizace uživatelů OAuth 2.0
Klikněte na tlačítko **rozhraní API** z **API Management** nabídky na levé straně, klikněte na název požadované rozhraní API, klikněte na tlačítko **zabezpečení**a potom zaškrtněte políčko pro **OAuth 2.0**.

![Autorizace uživatelů][api-management-user-authorization]

Vyberte požadovanou **serveru ověřování** z rozevíracího seznamu a klikněte na tlačítko **Uložit**.

![Autorizace uživatelů][api-management-user-authorization-save]

## <a name="step3"> </a>Testování autorizace OAuth 2.0 uživatelů v portálu pro vývojáře
Jakmile jste nakonfigurovali vašeho serveru ověřování OAuth 2.0 a nakonfigurované vaše rozhraní API pro tento server se používá, můžete ji otestovat tak, že přejdete na portál pro vývojáře a volání rozhraní API.  Klikněte na **Portál pro vývojáře** v pravé horní nabídce.

![Portál pro vývojáře][api-management-developer-portal-menu]

Klikněte na tlačítko **rozhraní API** v horní nabídce a vyberte **Echo API**.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Pokud máte nakonfigurované jenom jedno rozhraní API nebo váš účet vidí jenom jedno, můžete kliknutím na rozhraní API přejít přímo na operace tohoto rozhraní.
> 
> 

Vyberte **GET Resource** operace, klikněte na tlačítko **otevřít konzolu**a potom vyberte **autorizační kód** z rozevíracího seznamu.

![Otevření konzoly][api-management-open-console]

Když **autorizační kód** je vybrána, automaticky otevírané okno se zobrazí s formulářem přihlašovací zprostředkovatelů OAuth 2.0. V tomto příkladu je přihlašovací formulář poskytovaný Azure Active Directory.

> [!NOTE]
> Pokud máte zakázána automaticky otevíraná okna zobrazí se výzva k povolení v prohlížeči. Když je povolit, vyberte **autorizační kód** znovu a zobrazí se přihlašovací formulář.
> 
> 

![Přihlášení][api-management-oauth2-signin]

Až se zaregistrujete, **hlavičky požadavku** se naplní `Authorization : Bearer` hlavičky, která ověří požadavek.

![Token hlavičky požadavku][api-management-request-header-token]

V tomto okamžiku můžete nakonfigurovat požadované hodnoty pro zbývající parametry a odešlete žádost. 

## <a name="next-steps"></a>Další postup
Další informace o používání OAuth 2.0 a API Management najdete v tématu následující videa a doprovodné [článku](api-management-howto-protect-backend-with-aad.md).


[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

