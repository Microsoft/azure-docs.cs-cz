---
title: Autorizace vývojářských účtů pomocí služby Azure Active Directory
titleSuffix: Azure API Management
description: Zjistěte, jak autorizovat uživatele pomocí Služby Azure Active Directory ve správě rozhraní API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 6102b1e1d6ddbac01033b9cecfeba96a7eb33777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473536"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizace vývojářských účtů pomocí Azure Active Directory ve službě Azure API Management

Tento článek ukazuje, jak povolit přístup k portálu pro vývojáře pro uživatele z Azure Active Directory (Azure AD). Tato příručka také ukazuje, jak spravovat skupiny uživatelů Azure AD přidáním externích skupin, které obsahují uživatele.

## <a name="prerequisites"></a>Požadavky

- Dokončete následující rychlý start: [Vytvořte instanci Azure API Management](get-started-create-service-instance.md).
- Importujte a publikujte instanci Azure API Management. Další informace naleznete v [tématu Import a publikování](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizace vývojářských účtů pomocí Azure AD

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
2. Vyberte ![šipka](./media/api-management-howto-aad/arrow.png).
3. Do vyhledávacího pole zadejte **rozhraní API.**
4. Vyberte **služby správy rozhraní API**.
5. Vyberte instanci služby API Management.
6. V části **Zabezpečení**vyberte **položku Identity**.
7. Vyberte **+Přidat** shora.

    Vpravo se zobrazí podokno **Přidat zprostředkovatele identity.**
8. V části **Typ zprostředkovatele**vyberte **Azure Active Directory**.

    V podokně se zobrazí ovládací prvky, které umožňují zadat další potřebné informace. Ovládací prvky zahrnují **ID klienta** a **tajný klíč klienta**. (Informace o těchto ovládacích prvcích získáte dále v článku.)
9. Poznamenejte si obsah **adresy URL přesměrování**.
    
   ![Postup přidání poskytovatele identity na webu Azure Portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. V prohlížeči otevřete jinou kartu. 
11. Přejděte na [portál Azure – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) a zaregistrujte aplikaci ve službě Active Directory.
12. V části **Spravovat** vyberte **Registrace aplikací**.
13. Vyberte **možnost Nová registrace**. Na stránce **Registrovat aplikaci** nastavte hodnoty takto:
    
    * Nastavte **název** na smysluplný název. například *developer-portál*
    * Nastavte **podporované typy účtů** na účty pouze v tomto **organizačním adresáři**. 
    * Nastavte **identifikátor URI přesměrování** na hodnotu, kterou jste získali z kroku 9. 
    * Zvolte **Registrovat**. 

14.  Po registraci aplikace zkopírujte **ID Aplikace (klienta)** ze stránky **Přehled.** 
15. Vraťte se k instanci správy rozhraní API. V okně **Přidat zprostředkovatele identity** vložte hodnotu **ID aplikace (klienta)** do pole **ID klienta.**
16. Přepněte zpět do konfigurace Azure AD, **vyberte certifikáty & tajných kódů** v části **Správa**. Vyberte tlačítko **Nový tajný klíč klienta.** Zadejte hodnotu v **popisu**, vyberte libovolnou možnost **pro Vypršení platnosti** a zvolte **Přidat**. Před opuštěním stránky zkopírujte tajnou hodnotu klienta. Budete je potřebovat v dalším kroku. 
17. V části **Manage**vyberte **Authentication** a pak vyberte **ID tokeny** v části **Implicitní udělení.**
18. Vraťte se do instance správy rozhraní API a vložte tajný klíč do tajného pole **Klienta.**

    > [!IMPORTANT]
    > Před vypršením platnosti klíče nezapomeňte aktualizovat **tajný klíč klienta.** 
    >  
    >

19. Okno **Přidat zprostředkovatele identity** obsahuje také textové pole **Povoleno klienti.** Zde zadejte domény instancí Azure AD, kterým chcete udělit přístup k rozhraním API instance služby API Management. Můžete oddělit více domén pomocí nových řádků, mezer nebo čárek.

    > [!NOTE]
    > V části **Povolené klienty** můžete určit více domén. Aby se kterýkoli uživatel mohl přihlásit z jiné domény, než je původní doména, ve které byla aplikace zaregistrována, musí globální správce jiné domény udělit aplikaci oprávnění k přístupu k datům adresáře. Chcete-li udělit oprávnění, globální správce by měl: a. Přejděte `https://<URL of your developer portal>/aadadminconsent` na (například https://contoso.portal.azure-api.net/aadadminconsent).
    > b. Zadejte název domény klienta Azure AD, ke kterému chtějí udělit přístup.
    > c. Vyberte **Odeslat**. 

20.  Po zadání požadované konfigurace vyberte **Přidat**.

Po uložení změn se uživatelé v zadané instanci Azure AD můžou přihlásit k portálu pro vývojáře podle kroků v [přihlaste se k portálu pro vývojáře pomocí účtu Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Přidání externí skupiny Azure AD

Po povolení přístupu pro uživatele v tenantovi Azure AD, můžete přidat skupiny Azure AD do správy rozhraní API. V důsledku toho můžete řídit viditelnost produktu pomocí skupin Azure AD.

Chcete-li přidat externí skupinu Azure AD do APIM, musíte nejprve dokončit předchozí část. Aplikace, kterou jste zaregistrovali, musí být navíc `Directory.Read.All` s oprávněním udělen přístup k rozhraní Microsoft Graph API následujícím postupem: 

1. Vraťte se k registraci aplikace, která byla vytvořena v předchozí části.
2. Vyberte **oprávnění rozhraní API**a klepněte na tlačítko **+Přidat oprávnění**. 
3. V podokně **Požádat o oprávnění rozhraní API** vyberte kartu Microsoft **API** a pak vyberte dlaždici **Microsoft Graph.** Vyberte **oprávnění aplikace**, vyhledejte **adresář**a pak vyberte oprávnění **Directory.Read.All.** 
4. V dolní části podokna klikněte na **Přidat oprávnění** a potom klikněte na **Udělit souhlas správce pro {název tenanta},** abyste udělili přístup všem uživatelům v tomto adresáři. 

Teď můžete přidat externí skupiny Azure AD na kartě **Skupiny** instance správy rozhraní API.

1. Vyberte kartu **Skupiny**.
2. Vyberte tlačítko **Přidat skupinu AAD.**
    ![Tlačítko "Přidat skupinu AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Vyberte skupinu, kterou chcete přidat.
4. Stiskněte tlačítko **Vybrat.**

Po přidání externí skupiny Azure AD můžete zkontrolovat a nakonfigurovat její vlastnosti. Na kartě Skupiny vyberte název **skupiny.** Zde můžete upravit **informace o názvu** a **popisu** skupiny.
 
Uživatelé z nakonfigurované instance Azure AD se teď můžou přihlásit k portálu pro vývojáře. Mohou zobrazit a přihlásit se k odběru všech skupin, pro které mají viditelnost.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/>Portál pro vývojáře – přidání ověřování účtu Azure AD

Na portálu pro vývojáře je možné se přihlásit pomocí ads možné pomocí **tlačítka Přihlášení: OAuth** widget. Widget je již součástí přihlašovací stránky výchozího obsahu portálu pro vývojáře.

Přestože nový účet bude automaticky vytvořen při každém přihlášení nového uživatele pomocí ad, můžete zvážit přidání stejného widgetu na registrační stránku.

Widget **Registrace: OAuth** představuje formulář používaný pro registraci pomocí oauth.

> [!IMPORTANT]
> Změny ad se projeví [znovu publikovat na portálu.](api-management-howto-developer-portal-customize.md#publish)

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Starší portál pro vývojáře – jak se přihlásit pomocí Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Přihlášení k portálu pro vývojáře pomocí účtu Azure AD, který jste nakonfigurovali v předchozích částech:

1. Otevřete nové okno prohlížeče pomocí přihlašovací adresy URL z konfigurace aplikace služby Active Directory a vyberte **službu Azure Active Directory**.

   ![Přihlašovací stránka][api-management-dev-portal-signin]

1. Zadejte přihlašovací údaje jednoho z uživatelů ve službě Azure AD a **vyberte Přihlásit se**.

   ![Přihlášení pomocí uživatelského jména a hesla][api-management-aad-signin]

1. Pokud jsou požadovány další informace, můžete se zobrazit s registračním formulářem. Vyplňte registrační formulář a vyberte **Zaregistrovat .**

   ![Tlačítko "Přihlásit se" v registračním formuláři][api-management-complete-registration]

Váš uživatel je nyní přihlášen k portálu pro vývojáře pro instanci služby Api Management.

![Vývojářský portál po dokončení registrace][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
