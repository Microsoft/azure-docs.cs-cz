---
title: Ověření aplikace pro přístup k prostředkům Centra událostí Azure
description: Tento článek obsahuje informace o ověřování aplikace pomocí Služby Azure Active Directory pro přístup k prostředkům centra Událostí Azure
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 4cef49f138b96848b8e59cb5b2d0b185d4568aa9
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520999"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Ověření aplikace pomocí Služby Azure Active Directory pro přístup k prostředkům centra událostí
Microsoft Azure poskytuje integrovanou správu řízení přístupu pro prostředky a aplikace založené na Azure Active Directory (Azure AD). Klíčovou výhodou používání Azure AD s Azure Event Hubs je, že už nemusíte ukládat přihlašovací údaje do kódu. Místo toho můžete požádat o přístupový token OAuth 2.0 z platformy Microsoft Identity. Název prostředku pro vyžádání `https://eventhubs.azure.net/` tokenu je (Pro klienty Kafka `https://<namespace>.servicebus.windows.net`je prostředek pro vyžádání tokenu ). Azure AD ověřuje intezuru zabezpečení (uživatele, skupiny nebo instančního objektu) spuštěné aplikace. Pokud je ověření úspěšné, Azure AD vrátí přístupový token do aplikace a aplikace pak můžete použít přístupový token k autorizaci požadavku na prostředky Azure Event Hubs.

Když je role přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup může být vymezen na úroveň předplatného, skupiny prostředků, obor názvů Event Hubs nebo jakýkoli prostředek pod ním. Zabezpečení Azure AD můžete přiřadit role pro uživatele, skupinu, instanční objekt služby aplikace nebo [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Definice role je kolekce oprávnění. Řízení přístupu na základě rolí (RBAC) určuje, jak jsou tato oprávnění vynucena prostřednictvím přiřazení role. Přiřazení role se skládá ze tří prvků: objekt zabezpečení, definice role a obor. Další informace naleznete [v tématu Principy různých rolí](../role-based-access-control/overview.md).

## <a name="built-in-roles-for-azure-event-hubs"></a>Předdefinované role pro Centra událostí Azure
Azure poskytuje následující integrované role RBAC pro autorizaci přístupu k datům centra událostí pomocí Azure AD a OAuth:

- [Vlastník dat centra událostí Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Pomocí této role můžete poskytnout úplný přístup k prostředkům centra událostí.
- [Odesílatel dat centra událostí Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Pomocí této role můžete udělit přístup k prostředkům centra událostí.
- [Přijímač dat centra událostí Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Tato role slouží k poskytnutí přístupu k prostředkům centra událostí.   

> [!IMPORTANT]
> Naše verze preview podporuje přidávání oprávnění pro přístup k datům centra událostí do role Vlastník nebo Přispěvatel. Oprávnění pro přístup k datům pro roli vlastníka a přispěvatele však již nejsou respektována. Pokud používáte roli Vlastník nebo Přispěvatel, přepněte na použití role Vlastník dat centra událostí Azure.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Přiřazení rolí RBAC pomocí portálu Azure  
Další informace o správě přístupu k prostředkům Azure pomocí RBAC a portálu Azure najdete v [tomto článku](..//role-based-access-control/role-assignments-portal.md). 

Po určení vhodného oboru pro přiřazení role přejděte na tento prostředek na webu Azure Portal. Zobrazte nastavení řízení přístupu (IAM) pro prostředek a podle následujících pokynů spravujte přiřazení rolí:

> [!NOTE]
> Níže popsané kroky přiřazují roli centru událostí v oborech názvů Event Hubs, ale stejným způsobem můžete přiřadit roli vymezenou jakémukoli prostředku Centra událostí.

1. Na [webu Azure Portal](https://portal.azure.com/)přejděte do oboru názvů Centra událostí.
2. Na stránce **Přehled** vyberte centrum událostí, ke kterému chcete přiřadit roli.

    ![Výběr centra událostí](./media/authenticate-application/select-event-hub.png)
1. Vyberte **Řízení přístupu (IAM),** chcete-li zobrazit nastavení řízení přístupu pro centrum událostí. 
1. Výběrem karty **Přiřazení rolí** zobrazíte seznam přiřazení rolí. Na panelu nástrojů vyberte tlačítko **Přidat** a pak vyberte **Přidat přiřazení role**. 

    ![Tlačítko Přidat na panelu nástrojů](./media/authenticate-application/role-assignments-add-button.png)
1. Na stránce **Přidat přiřazení rolí** postupujte takto:
    1. Vyberte **roli Centra událostí,** kterou chcete přiřadit. 
    1. Vyhledejte **zaregistrovaný objekt zabezpečení** (uživatel, skupina, instanční objekt), ke kterému chcete roli přiřadit.
    1. Chcete-li uložit přiřazení role, vyberte **Uložit.** 

        ![Přiřazení role uživateli](./media/authenticate-application/assign-role-to-user.png)
    4. Identita, které jste roli přiřadili, se zobrazí v seznamu v této roli. Například následující obrázek ukazuje, že uživatelé Azure je v roli vlastníka dat Centra událostí Azure. 
        
        ![Uživatel v seznamu](./media/authenticate-application/user-in-list.png)

Podobnými kroky můžete přiřadit roli vymezenou oboruem oboru názvů Centra událostí, skupiny prostředků nebo předplatného. Jakmile definujete roli a její obor, můžete toto chování otestovat pomocí ukázek [v tomto umístění GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).


## <a name="authenticate-from-an-application"></a>Ověření z aplikace
Klíčovou výhodou používání Azure AD s Event Hubs je, že vaše přihlašovací údaje už nemusí být uloženy ve vašem kódu. Místo toho můžete požádat o přístupový token OAuth 2.0 z platformy identit Microsoftu. Azure AD ověřuje objekt zabezpečení (uživatel, skupina nebo instanční objekt) spuštěné aplikace. Pokud ověřování proběhne úspěšně, Azure AD vrátí přístupový token do aplikace a aplikace pak můžete použít přístupový token k autorizaci požadavků na Azure Event Hubs.

Následující části ukazují, jak nakonfigurovat nativní aplikaci nebo webovou aplikaci pro ověřování pomocí platformy Microsoft Identity Platform 2.0. Další informace o platformě Microsoft Identity Platform 2.0 naleznete [v přehledu platformy microsoftidentit (v2.0).](../active-directory/develop/v2-overview.md)

Přehled toku udělení kódu OAuth 2.0 najdete v tématu [Autorizace přístupu k webovým aplikacím Služby Azure Active Directory pomocí toku udělení kódu OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrace aplikace u klienta Azure AD
Prvním krokem při používání Azure AD k autorizaci prostředků Event Hubs je registrace klientské aplikace s tenantem Azure AD z [webu Azure Portal](https://portal.azure.com/). Při registraci klientské aplikace zadáte informace o aplikaci do služby AD. Azure AD pak poskytuje ID klienta (označované také jako ID aplikace), které můžete použít k přidružení aplikace k runtime Azure AD. Další informace o ID klienta najdete v [tématu Objekty za registrovaných aplikací a služeb ve službě Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Následující obrázky ukazují kroky pro registraci webové aplikace:

![Registrace aplikace](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Pokud zaregistrujete aplikaci jako nativní aplikaci, můžete zadat libovolný platný identifikátor URI pro identifikátor URI přesměrování. Pro nativní aplikace tato hodnota nemusí být skutečná adresa URL. U webových aplikací musí být identifikátor URI přesměrování platný identifikátor URI, protože určuje adresu URL, na kterou jsou k dispozici tokeny.

Po registraci aplikace se v části Nastavení zobrazí **ID aplikace (klienta):** **Settings**

![ID přihlášky registrované přihlášky](./media/authenticate-application/application-id.png)

Další informace o registraci aplikace ve službě Azure AD najdete v [tématu Integrace aplikací s Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).


### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta   
Aplikace potřebuje tajný klíč klienta k prokázání své identity při požadování tokenu. Chcete-li přidat tajný klíč klienta, postupujte takto.

1. Přejděte na registraci aplikace na webu Azure Portal.
1. Vyberte nastavení **Certifikáty & tajných kódů.**
1. V části **Tajné klíče klienta**vyberte **Nový tajný klíč klienta** a vytvořte nový tajný klíč.
1. Zadejte popis tajného klíče a zvolte žádaný interval vypršení platnosti.
1. Okamžitě zkopírujte hodnotu nového tajného klíče do zabezpečeného umístění. Hodnota výplně se zobrazí pouze jednou.

    ![Tajný klíč klienta](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>Klientské knihovny pro získávání tokenů  
Jakmile zaregistrujete aplikaci a udělíte jí oprávnění k odesílání a přijímání dat v Azure Event Hubs, můžete do aplikace přidat kód k ověření objektu zabezpečení a získat token OAuth 2.0. Chcete-li ověřit a získat token, můžete použít jednu z [knihoven ověřování platformy identit Microsoftu](../active-directory/develop/reference-v2-libraries.md) nebo jinou otevřenou knihovnu, která podporuje OpenID nebo Connect 1.0. Vaše aplikace pak můžete použít přístupový token k autorizaci požadavku proti Azure Event Hubs.

Seznam scénářů, pro které je podporováno získávání tokenů, naleznete v části [Scénáře](https://aka.ms/msal-net-scenarios) [v knihovně ověřování Microsoft (MSAL) pro](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) úložiště .NET GitHub.

## <a name="samples"></a>ukázky
- [Ukázky Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Tyto ukázky používají starou knihovnu **Microsoft.Azure.EventHubs,** ale můžete ji snadno aktualizovat na použití nejnovější **knihovny Azure.Messaging.EventHubs.** Pokud chcete vzorek přesunout z používání staré knihovny do nové, přečtěte si [příručku pro migraci z Microsoft.Azure.EventHubs na Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Ukázky Azure.Messaging.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Tato ukázka byla aktualizována tak, aby používala nejnovější knihovnu **Azure.Messaging.EventHubs.**

## <a name="next-steps"></a>Další kroky
- Další informace o RBAC, naleznete v [tématu Co je řízení přístupu na základě rolí (RBAC)](../role-based-access-control/overview.md)?
- Informace o tom, jak přiřazovat a spravovat přiřazení rolí RBAC pomocí Azure PowerShellu, rozhraní API Azure nebo rozhraní REST API, najdete v těchto článcích:
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí Azure PowerShellu](../role-based-access-control/role-assignments-powershell.md)  
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí azure cli](../role-based-access-control/role-assignments-cli.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí rozhraní REST API](../role-based-access-control/role-assignments-rest.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí šablon Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Podívejte se na následující související články:
- [Ověření spravované identity pomocí Služby Azure Active Directory pro přístup k prostředkům centra událostí](authenticate-managed-identity.md)
- [Ověřování požadavků na Centra událostí Azure pomocí sdílených přístupových podpisů](authenticate-shared-access-signature.md)
- [Autorizace přístupu k prostředkům Centra událostí pomocí Služby Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorizace přístupu k prostředkům Centra událostí pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md)

