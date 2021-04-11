---
title: Autorizace vývojářských účtů pomocí Azure Active Directory
titleSuffix: Azure API Management
description: Naučte se, jak autorizovat uživatele pomocí Azure Active Directory v API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2021
ms.author: apimpm
ms.openlocfilehash: 743a7e7d34457405aa4be42b196dc994506c6587
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105035794"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizace vývojářských účtů pomocí Azure Active Directory ve službě Azure API Management

V tomto článku se dozvíte, jak povolit přístup k portálu pro vývojáře pro uživatele z Azure Active Directory (Azure AD). Tato příručka také ukazuje, jak spravovat skupiny uživatelů Azure AD přidáním externích skupin, které obsahují uživatele.

## <a name="prerequisites"></a>Požadavky

- Dokončete následující rychlý Start: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
- Import a publikování instance služby Azure API Management. Další informace najdete v tématu [Import a publikování](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizace vývojářských účtů pomocí Azure AD

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
2. Vyberte ![Ikona šipky](./media/api-management-howto-aad/arrow.png).
3. Do vyhledávacího pole zadejte **API** .
4. Vyberte **API Management Services**.
5. Vyberte instanci služby API Management.
6. V části **portál pro vývojáře** vyberte **identity**.
7. V horní části vyberte **+ Přidat** .

    Na pravé straně se zobrazí podokno **Přidat poskytovatele identity** .
8. V části **Typ poskytovatele** vyberte **Azure Active Directory**.

    Ovládací prvky, které umožňují zadat další potřebné informace, se zobrazí v podokně. Ovládací prvky zahrnují **ID klienta** a **tajný klíč klienta**. (Informace o těchto ovládacích prvcích získáte později v článku.)
9. Poznamenejte si obsah **adresy URL pro přesměrování**.
    

    :::image type="content" source="media/api-management-howto-aad/api-management-with-aad001.png" alt-text="Přidat zprostředkovatele identity v Azure Portal":::
    > [!NOTE]
    > Existují dvě adresy URL pro přesměrování:<br/>
    > **Adresa URL pro přesměrování** odkazuje na nejnovější portál pro vývojáře API Management.<br/>
    > **Adresa URL pro přesměrování (nepoužívané portál)** – odkazuje na zastaralý portál pro vývojáře API Management.
    >
    > Doporučuje se použít adresu URL pro přesměrování nejnovějšího portálu pro vývojáře.
   
10. V prohlížeči otevřete jinou kartu. 
11. Pokud chcete aplikaci zaregistrovat ve službě Active Directory, přejděte k [Azure Portal registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) .
12. V části **Spravovat** vyberte **Registrace aplikací**.
13. Vyberte **Nová registrace**. Na stránce **zaregistrovat aplikaci** nastavte následující hodnoty:
    
    * Nastavte **název** na smysluplný název. např. *vývojář – portál*
    * Nastavte **podporované typy účtů** **jenom na účty v tomto organizačním adresáři**. 
    * Nastavte **identifikátor URI přesměrování** na hodnotu, kterou jste získali z kroku 9. 
    * Vyberte možnost **Registrovat**. 

14.  Po zaregistrování aplikace zkopírujte na stránce **Přehled** **ID aplikace (klienta)** . 
15. Vraťte se k instanci API Management. V okně **Přidat zprostředkovatele identity** vložte hodnotu **ID aplikace (klienta)** do pole **ID klienta** .
16. Přepněte zpátky do konfigurace Azure AD, v části **Spravovat** vyberte **certifikáty & tajných** kódů. Vyberte tlačítko **nový tajný klíč klienta** . Zadejte hodnotu v **popisu**, vyberte možnost pro **vypršení platnosti** a zvolte **Přidat**. Před zavřením stránky zkopírujte hodnotu tajného klíče klienta. Budete je potřebovat v dalším kroku. 
17. V části **Spravovat** vyberte **ověřování** a v části **implicitní udělení** vyberte **tokeny ID** .
18. Vraťte se do své instance API Management a vložte tajný klíč do pole **tajný kód klienta** .

    > [!IMPORTANT]
    > Před vypršením platnosti klíče prosím nezapomeňte aktualizovat **tajný klíč klienta** . 
    >  
    >

19. Okno **Přidat zprostředkovatele identity** obsahuje taky textové pole **povolené klienty** . Zde zadejte domény instancí služby Azure AD, kterým chcete udělit přístup k rozhraním API instance služby API Management. Více domén můžete oddělit newlines, mezerami nebo čárkami.

    > [!NOTE]
    > V části **Povolení klienti** můžete zadat více domén. Předtím, než se uživatel může přihlásit z jiné domény, než je původní doména, ve které byla aplikace zaregistrována, musí globální správce jiné domény udělit oprávnění aplikaci pro přístup k datům adresáře. Aby bylo možné udělit oprávnění, globální správce by měl: a. Přejít na `https://<URL of your developer portal>/aadadminconsent` (například https://contoso.portal.azure-api.net/aadadminconsent) .
    > b. Zadejte název domény tenanta Azure AD, kterému chcete udělit přístup.
    > c. Vyberte **Odeslat**. 

20.  Po zadání požadované konfigurace vyberte **Přidat**.

Po uložení změn se uživatelé ze zadané instance služby Azure AD mohou přihlásit k portálu pro vývojáře pomocí postupu v části [přihlášení k portálu pro vývojáře pomocí účtu Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Přidat externí skupinu Azure AD

Po povolení přístupu pro uživatele v tenantovi Azure AD můžete přidat skupiny Azure AD do API Management. V důsledku toho můžete ovládat viditelnost produktu pomocí skupin Azure AD.

Pokud chcete do APIM přidat externí skupinu Azure AD, musíte nejdřív dokončit předchozí oddíl. Kromě toho musí být zaregistrovaná aplikace udělena přístup k rozhraní Microsoft Graph API s `Directory.Read.All` oprávněním pomocí následujících kroků: 

1. Vraťte se k registraci vaší aplikace, která byla vytvořena v předchozí části.
2. Vyberte **oprávnění rozhraní API** a pak klikněte na **+ Přidat oprávnění**. 
3. V podokně **oprávnění API pro vyžádání** vyberte kartu **rozhraní Microsoft API** , přejděte dolů a vyberte dlaždici **Azure Active Directory Graph** . Vyberte **oprávnění aplikace**, vyhledejte **adresář** a potom vyberte **adresář. číst. všechna** oprávnění. 
4. V dolní části podokna klikněte na **Přidat oprávnění** a pak klikněte na **udělit souhlas správce pro {tenant}** , abyste všem uživatelům v tomto adresáři udělili přístup. 

Nyní můžete přidat externí skupiny služby Azure AD z karty **skupiny** vaší instance API Management.

1. Vyberte kartu **Skupiny**.
2. Vyberte tlačítko **Přidat skupinu AAD** .
    ![Tlačítko Přidat skupinu AAD](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Vyberte skupinu, kterou chcete přidat.
4. Stiskněte tlačítko **Vybrat** .

Po přidání externí skupiny služby Azure AD můžete zkontrolovat a nakonfigurovat její vlastnosti. Na kartě **skupiny** vyberte název skupiny. Tady můžete upravit **název** a **Popis** informace pro skupinu.
 
Uživatelé z nakonfigurované instance služby Azure AD se teď můžou přihlásit k portálu pro vývojáře. Můžou zobrazit a přihlásit se k odběru všech skupin, pro které mají viditelnost.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> Portál pro vývojáře – přidání ověřování účtu Azure AD

Na portálu pro vývojáře se může přihlásit pomocí AAD pomocí **tlačítka pro přihlášení: widget OAuth** . Pomůcka už je součástí přihlašovací stránky výchozího obsahu portálu pro vývojáře.

I když se nový účet automaticky vytvoří pokaždé, když se přihlásí nový uživatel pomocí AAD, můžete zvážit přidání stejného widgetu do registrační stránky.

**Formulář pro registraci: widget OAuth** představuje formulář používaný k registraci pomocí protokolu OAuth.

> [!IMPORTANT]
> Aby se změny AAD projevily, je potřeba [znovu publikovat portál](api-management-howto-developer-portal-customize.md#publish) .

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Starší portál pro vývojáře – jak se přihlásit pomocí Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Pokud se chcete přihlásit k portálu pro vývojáře pomocí účtu Azure AD, který jste nakonfigurovali v předchozích částech:

1. Otevřete nové okno prohlížeče pomocí přihlašovací adresy URL z konfigurace aplikace Active Directory a vyberte **Azure Active Directory**.

   ![Přihlašovací stránka][api-management-dev-portal-signin]

1. Zadejte přihlašovací údaje jednoho z uživatelů v Azure AD a vyberte **Přihlásit** se.

   ![Přihlášení pomocí uživatelského jména a hesla][api-management-aad-signin]

1. Pokud potřebujete nějaké další informace, může se zobrazit výzva s registračním formulářem. Dokončete registrační formulář a vyberte možnost **zaregistrovat** se.

   ![Tlačítko zaregistrovat u registračního formuláře][api-management-complete-registration]

Uživatel je teď přihlášený k portálu pro vývojáře pro vaši instanci služby API Management.

![Portál pro vývojáře po dokončení registrace][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
