---
title: Autorizace vývojářských účtů pomocí Azure Active Directory – Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak autorizovat uživatele pomocí služby Azure Active Directory ve službě API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: 6c288e4492ac56436d40d1e3db98af8eb7b173c8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436314"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizace vývojářských účtů pomocí Azure Active Directory ve službě Azure API Management

V tomto článku se dozvíte, jak povolit přístup k portálu pro vývojáře pro uživatele ze služby Azure Active Directory (Azure AD). Tento průvodce také ukazuje, jak spravovat skupiny uživatelů Azure AD tak, že přidáte externí skupiny, které obsahují uživatele.

> [!NOTE]
> Integrace se službou Azure AD je k dispozici v [Developer, Standard a Premium](https://azure.microsoft.com/pricing/details/api-management/) pouze úrovní.

## <a name="prerequisites"></a>Požadavky

- Projděte si následující rychlý start: [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md).
- Import a publikování instance Azure API Management. Další informace najdete v tématu [Import a publikování](import-and-publish.md).

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizace vývojářských účtů pomocí Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Vyberte ![šipka](./media/api-management-howto-aad/arrow.png).
1. Typ **api** do vyhledávacího pole.
1. Vyberte **služby API Management**.
1. Vyberte instanci služby API Management.
1. V části **zabezpečení**vyberte **identit**.

1. Vyberte **+ přidat** shora.

    **Přidat zprostředkovatele identity** otevře se podokno na pravé straně.
1. V části **typ zprostředkovatele**vyberte **Azure Active Directory**.

    V podokně se zobrazí ovládací prvky, které vám umožní zadat další potřebné informace. Ovládací prvky zahrnují **ID klienta** a **tajný kód klienta**. (Dále v tomto článku získáte informace o těchto ovládacích prvků.)
1. Poznamenejte si obsah **adresy URL pro přesměrování**.
    
   ![Kroky pro přidání poskytovatele identit na portálu Azure portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
1. V prohlížeči se otevře na jinou kartu. 
1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Vyberte ![šipka](./media/api-management-howto-aad/arrow.png).
1. Typ **aktivní**. **Azure Active Directory** otevře se podokno.
1. Vyberte **Azure Active Directory**.
1. V části **SPRAVOVAT**vyberte **registrace aplikací**.
1. Vyberte **Registrace nové aplikace**.

    ![Vybrané možnosti pro vytvoření registrace nové aplikace](./media/api-management-howto-aad/api-management-with-aad002.png)

    **Vytvořit** otevře se podokno na pravé straně. Je to, kde můžete zadat informace související s aplikací Azure AD.
1. Zadejte název aplikace.
1. Typ aplikace vyberte **webové aplikace nebo rozhraní API**.
1. Přihlašovací adresa URL zadejte přihlašovací adresu URL portálu pro vývojáře. V tomto příkladu je adresa URL přihlášení https://apimwithaad.portal.azure-api.net/signin.
1. Vyberte **vytvořit** k vytvoření aplikace.
1. Pokud chcete najít aplikace, vyberte **registrace aplikací** a hledat podle názvu.

    ![Pole, ve kterém můžete aplikaci vyhledat](./media/api-management-howto-aad/find-your-app.png)
1. Po registraci aplikace, přejděte na **adresy URL odpovědi** a ujistěte se, že **adresy URL pro přesměrování** je nastavena na hodnotu, která jste získali v kroku 9. 
1. Pokud chcete nakonfigurovat svoji aplikaci (například změnit **adresa URL aplikace App ID**) vyberte **vlastnosti**.

    ![Otevření podokna "Properties"](./media/api-management-howto-aad/api-management-with-aad004.png)

    Pokud více instancí služby Azure AD se použije pro tuto aplikaci, vyberte **Ano** pro **víceklientských**. Výchozí hodnota je **ne**.
1. Nastavení oprávnění aplikace tak, že vyberete **požadovaná oprávnění**.
1. Vyberte svou aplikaci a pak vyberte **čtení dat adresáře** a **přihlášení a čtení profilu uživatele** zaškrtávací políčka.

    ![Zaškrtnutí políček u oprávnění](./media/api-management-howto-aad/api-management-with-aad005.png)

    Další informace o oprávněních aplikace a delegovaná oprávnění najdete v tématu [přístup k rozhraní Graph API][Accessing the Graph API].
1. V levém podokně zkopírujte **ID aplikace** hodnotu.

    ![Hodnota "ID aplikace"](./media/api-management-howto-aad/application-id.png)
1. Přepněte zpět do aplikace API Management. 

    V **přidat zprostředkovatele identity** okně Vložit **ID aplikace** hodnotu **ID klienta** pole.
1. Přepněte zpět do konfigurace služby Azure AD a vyberte **klíče**.
1. Vytvořte nový klíč tak, že zadáte název a doby trvání. 
1. Vyberte **Uložit**. Klíč se generuje.

    Klíč zkopírujte do schránky.

    ![Výběry pro vytváření klíčů](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Tento klíč si poznamenejte. Po zavření okna konfigurace služby Azure AD, nelze zobrazit klíč znovu.
    > 
    > 
1. Přepněte zpět do aplikace API Management. 

    V **přidat zprostředkovatele identity** okně vložte klíč, který v **tajný kód klienta** textového pole.

    > [!IMPORTANT]
    > Zkontrolujte prosím, že aktualizace **tajný kód klienta** vypršení platnosti klíče. 
    >  
    >
1. **Přidat zprostředkovatele identity** okno obsahuje také **povolené Tenantů** textového pole. Existuje zadejte domény instancí Azure AD, ke kterým chcete udělit přístup k rozhraním API instance služby API Management. Více domén můžete oddělit vložení znaků newline, mezerami nebo čárkami.

    Můžete zadat více doménách **povolené Tenantů** části. Než každý uživatel může přihlásit z jiné domény než původní doménu, ve kterém byl zaregistrován aplikace, globálním správcem jiné doméně, musí udělit oprávnění pro aplikaci pro přístup k datům adresáře. Udělení oprávnění, by měl globálního správce:
    
    a. Přejděte na `https://<URL of your developer portal>/aadadminconsent` (například https://contoso.portal.azure-api.net/aadadminconsent).
    
    b. Zadejte název domény, které chtějí poskytnout přístup k tenantovi Azure AD.
    
    c. Vyberte **odeslat**. 
    
    V následujícím příkladu se pokouší globálního správce ze miaoaad.onmicrosoft.com udělit oprávnění k tomuto portálu zejména pro vývojáře. 

1. Po zadání požadované konfigurace, vyberte **přidat**.

    !["Přidání" tlačítko v podokně "Přidat zprostředkovatele identity"](./media/api-management-howto-aad/api-management-with-aad007.png)

Po uložení změn, uživatelů ve službě Azure AD zadané instance můžete přihlásit k portálu pro vývojáře pomocí následujících kroků v [přihlásit k portálu pro vývojáře pomocí účtu služby Azure AD](#log_in_to_dev_portal).

![Zadejte název tenanta služby Azure AD](./media/api-management-howto-aad/api-management-aad-consent.png)

Na další obrazovce je globální správce výzva k potvrzení udělení oprávnění. 

![Potvrzení přiřazení oprávnění](./media/api-management-howto-aad/api-management-permissions-form.png)

Pokud není globální správce se pokusí přihlásit před globální správce udělí oprávnění, pokus o přihlášení selže a chybová obrazovka se zobrazí.

## <a name="add-an-external-azure-ad-group"></a>Přidat vnější skupinu Azure AD

Když povolíte přístup pro uživatele v instanci služby Azure AD, můžete přidat skupiny Azure AD ve službě API Management. Potom můžete snadněji spravovat přidružení požadované produkty vývojářů ve skupině.

Konfigurace externího skupiny Azure AD, je nutné nejprve nakonfigurovat instanci služby Azure AD na **identit** kartu podle postupu v předchozí části. 

Přidat externí Azure AD skupiny z **skupiny** kartu vaší instance služby API Management.

1. Vyberte kartu **Skupiny**.
1. Vyberte **skupiny AAD přidat** tlačítko.
   ![Tlačítko "Přidat skupinu AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
1. Vyberte skupinu, kterou chcete přidat.
1. Stisknutím klávesy **vyberte** tlačítko.

Po přidání externí Azure AD seskupit, můžete zkontrolovat a nakonfigurujte její vlastnosti. Vyberte název skupiny z **skupiny** kartu. Tady můžete upravit **název** a **popis** informace o skupině.
 
Uživatelé z nakonfigurované instance služby Azure AD můžete přihlásit k portálu pro vývojáře. Se může zobrazovat a odebírat žádné skupiny, ke kterým mají viditelnost.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Přihlaste se k portálu pro vývojáře pomocí účtu služby Azure AD

Pro přihlášení k portálu pro vývojáře pomocí účtu služby Azure AD, kterou jste nakonfigurovali v předchozích částech:

1. Otevřete nové okno prohlížeče pomocí adresu URL přihlášení z konfigurace aplikace služby Active Directory a vyberte **Azure Active Directory**.

   ![Přihlašovací stránka][api-management-dev-portal-signin]

1. Zadejte přihlašovací údaje z jednoho z uživatelů ve službě Azure AD a vyberte **přihlášení**.

   ![Přihlášení pomocí uživatelského jména a hesla][api-management-aad-signin]

1. Pokud je potřeba žádné další informace o může výzva s registrační formulář. Vyplňte registrační formulář a vyberte **zaregistrovat**.

   !["Zaregistrovat" tlačítko na registrační formulář][api-management-complete-registration]

Uživatel je nyní přihlášení k portálu pro vývojáře pro instanci služby API Management.

![Portál pro vývojáře po dokončení registrace][api-management-registration-complete]

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

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
