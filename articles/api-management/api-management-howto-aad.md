---
title: Autorizovat vývojářským účtům pomocí Azure Active Directory – Azure API Management | Microsoft Docs
description: Naučte se autorizace uživatelů pomocí služby Azure Active Directory ve službě API Management.
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
ms.openlocfilehash: b0476865b19cd078b05e5def4a51c2df17315daa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizovat vývojářským účtům pomocí služby Azure Active Directory v Azure API Management

Tento článek ukazuje, jak povolit přístup k portálu pro vývojáře pro uživatele ze služby Azure Active Directory (Azure AD). Tento průvodce také ukazuje, jak spravovat přidáním externí skupiny, které obsahují uživatele, skupiny uživatelů Azure AD.

> [!NOTE]
> Integrace se službou Azure AD je k dispozici v [Developer, Standard a Premium](https://azure.microsoft.com/pricing/details/api-management/) pouze úrovně.

## <a name="prerequisites"></a>Požadavky

- Projděte si následující rychlý start: [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md).
- Import a publikujte instance služby Azure API Management. Další informace najdete v tématu [importu a publikování](import-and-publish.md).

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizovat vývojářským účtům pomocí Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Vyberte ![šipka](./media/api-management-howto-aad/arrow.png).
3. Typ **rozhraní api** do vyhledávacího pole.
4. Vyberte **služby API Management**.
5. Vyberte instanci služby API Management.
6. V části **zabezpečení**, vyberte **identity**.

7. Vyberte **+ přidat** shora.

    **Zprostředkovatele identity přidat** podokně se zobrazí na pravé straně.
8. V části **typ zprostředkovatele**, vyberte **Azure Active Directory**.

    V podokně se zobrazí ovládací prvky, které vám umožní zadat další potřebné informace. Ovládací prvky zahrnují **ID klienta** a **tajný klíč klienta**. (Dále v článku získat informace o těchto ovládacích prvků.)
9. Poznamenejte si obsah **adresy URL pro přesměrování**.
    
   ![Kroky pro přidání poskytovatele identit na portálu Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. V prohlížeči otevřete na jiné kartě. 
11. Přejděte na [portál Azure](https://portal.azure.com).
12. Vyberte ![šipka](./media/api-management-howto-aad/arrow.png).
13. Typ **active**. **Azure Active Directory** podokně se zobrazí.
14. Vyberte **Azure Active Directory**.
15. V části **SPRAVOVAT**, vyberte **registrace aplikace**.
16. Vyberte **Registrace nové aplikace**.

    ![Možnosti pro vytvoření nové aplikace registrace](./media/api-management-howto-aad/api-management-with-aad002.png)

    **Vytvořit** podokně se zobrazí na pravé straně. To je, kde zadáte informace relevantní aplikaci Azure AD.
17. Zadejte název aplikace.
18. Typ aplikace, vyberte **webové aplikace nebo rozhraní API**.
19. Přihlašovací adresa URL zadejte přihlašovací adresa URL portálu pro vývojáře. V tomto příkladu přihlašovací adresa URL je https://apimwithaad.portal.azure-api.net/signin.
20. Vyberte **vytvořit** k vytvoření aplikace.
21. Chcete-li najít aplikaci, vyberte **registrace aplikace** a hledat podle názvu.

    ![Pole, kde hledat pro aplikaci](./media/api-management-howto-aad/find-your-app.png)
22. Po registraci aplikace, přejděte na **adresa URL odpovědi** a zajistěte, aby **adresy URL pro přesměrování** je nastaven na hodnotu, která jste získali v kroku 9. 
23. Pokud chcete nakonfigurovat aplikace (například změnit **URL ID aplikace**), vyberte **vlastnosti**.

    ![Otevření podokna "Vlastnosti"](./media/api-management-howto-aad/api-management-with-aad004.png)

    Pokud se více instancí služby Azure AD bude používat pro tuto aplikaci, vyberte **Ano** pro **nevyužívá dělené tabulky více**. Výchozí hodnota je **ne**.
24. Nastavte oprávnění aplikací tak, že vyberete **požadovaná oprávnění**.
25. Vyberte aplikaci a pak vyberte **čtení dat adresáře** a **přihlášení a čtení profilu uživatele** zaškrtávací políčka.

    ![Políčka u oprávnění](./media/api-management-howto-aad/api-management-with-aad005.png)

    Další informace o aplikaci oprávnění a přidělená oprávnění najdete v tématu [přístup k rozhraní Graph API][Accessing the Graph API].
26. V levém podokně, zkopírujte **ID aplikace** hodnotu.

    ![Hodnota "ID aplikace"](./media/api-management-howto-aad/application-id.png)
27. Přepnout zpět do aplikace API Management. 

    V **zprostředkovatele identity přidat** okně vložení **ID aplikace** hodnotu **ID klienta** pole.
28. Přepněte zpět do konfigurace Azure AD a vyberte **klíče**.
29. Vytvořte nový klíč zadáním názvu a doba trvání. 
30. Vyberte **Uložit**. Klíč se generuje.

    Klíč zkopírujte do schránky.

    ![Možnosti pro vytvoření klíče](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Tento klíč si poznamenejte. Po zavření v podokně Konfigurace Azure AD, klíč nelze zobrazit znovu.
    > 
    > 
31. Přepnout zpět do aplikace API Management. 

    V **zprostředkovatele identity přidat** okno, vložte klíč v **tajný klíč klienta** textové pole.

    > [!IMPORTANT]
    > Zkontrolujte, zda aktualizace **tajný klíč klienta** před vypršením platnosti klíče. 
    >  
    >
32. **Zprostředkovatele identity přidat** okno obsahuje také **klientům povoleno** textové pole. Zde zadejte domény instancí Azure AD, u kterých chcete udělit přístup k rozhraním API v instanci služby API Management. Více domén můžete oddělit vložení znaků newline, mezery nebo čárkami.

    Můžete zadat více doménách **klientům povoleno** části. Než každý uživatel může přihlásit z jiné doméně než původní domény, kde byla aplikace registrovaná, globální správce jiné domény, musí udělit oprávnění pro aplikaci pro přístup k datům adresáře. Udělit oprávnění, má globální správce:
    
    a. Přejděte na `https://<URL of your developer portal>/aadadminconsent` (například https://contoso.portal.azure-api.net/aadadminconsent).
    
    b. Zadejte název domény klienta Azure AD, kterou chce umožnit přístup k.
    
    c. Vyberte **odeslání**. 
    
    V následujícím příkladu se pokouší globální správce miaoaad.onmicrosoft.com udělit oprávnění k tomuto portálu konkrétní developer. 

33. Po zadání požadované konfigurace, vyberte **přidat**.

    !["Přidat" tlačítko v podokně "Přidat zprostředkovatele identity"](./media/api-management-howto-aad/api-management-with-aad007.png)

Po uložení změny uživatelů v zadané Azure AD instance můžete přihlásit k portálu pro vývojáře podle kroků v [Přihlaste se k portálu pro vývojáře pomocí účtu Azure AD](#log_in_to_dev_portal).

![Zadávání názvů klient služby Azure AD](./media/api-management-howto-aad/api-management-aad-consent.png)

Na další obrazovce je globálním správcem výzva k potvrzení, udělíte oprávnění. 

![Potvrzení přiřazení oprávnění](./media/api-management-howto-aad/api-management-permissions-form.png)

Pokud není globální správce se pokusí přihlásit před globální správce udělí oprávnění, pokus o přihlášení selže a se zobrazí chybové obrazovce.

## <a name="add-an-external-azure-ad-group"></a>Přidat externí skupiny Azure AD

Když povolíte přístup pro uživatele v instanci služby Azure AD, můžete přidat skupiny Azure AD ve službě API Management. Potom můžete snadněji spravovat přidružení požadované produkty vývojářů ve skupině.

Ke konfiguraci externí skupiny Azure AD, musíte napřed nakonfigurovat instanci Azure AD na **identity** karta podle postupu v předchozím oddílu. 

Přidat externí Azure AD skupin z **skupiny** kartě instanci služby API Management.

1. Vyberte kartu **Skupiny**.
2. Vyberte **skupiny přidat AAD** tlačítko.
   ![Tlačítko "Přidat skupinu AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Vyberte skupinu, kterou chcete přidat.
4. Stiskněte **vyberte** tlačítko.

Po přidání externí Azure AD skupiny, můžete zkontrolovat a nakonfigurujte její vlastnosti. Vyberte název skupiny z **skupiny** kartě. Zde můžete upravit **název** a **popis** informace o skupině.
 
Uživatelé z konfigurovaného instancí Azure AD teď může přihlásit k portálu pro vývojáře. Se můžou zobrazovat a odebírat žádných skupin, ke kterým mají viditelnost.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Přihlaste se k portálu pro vývojáře pomocí účtu Azure AD

Pro přihlášení k portálu pro vývojáře pomocí účtu Azure AD, který jste nakonfigurovali v předchozích částech:

1. Otevřete nové okno prohlížeče pomocí adresu URL přihlášení z konfigurace aplikace služby Active Directory a vyberte **Azure Active Directory**.

   ![Přihlašovací stránka][api-management-dev-portal-signin]

2. Zadejte přihlašovací údaje pro jeden z uživatelů ve službě Azure AD a vyberte **přihlášení**.

   ![Přihlášení pomocí uživatelského jména a hesla][api-management-aad-signin]

3. Vám může být vyzvání s registračním formuláři, pokud je vyžadováno žádné další údaje. Dokončení registrace a vyberte **zaregistrovat**.

   !["Registrace" tlačítko ve formuláři registrace][api-management-complete-registration]

Uživatel je teď přihlášení na portál pro vývojáře pro instanci služby API Management.

![Po dokončení registrace portál pro vývojáře][api-management-registration-complete]

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
