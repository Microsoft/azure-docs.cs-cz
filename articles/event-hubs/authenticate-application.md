---
title: Ověření aplikace pro přístup k prostředkům Azure Event Hubs
description: Tento článek poskytuje informace o ověřování aplikace s Azure Active Directory pro přístup k prostředkům Azure Event Hubs.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 25ec5f11ca7b5e801e18155f1a3da6474c8e66e2
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913309"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Ověření aplikace s Azure Active Directory pro přístup k prostředkům Event Hubs
Microsoft Azure poskytuje integrované řízení přístupu pro prostředky a aplikace založené na Azure Active Directory (Azure AD). Klíčovou výhodou použití Azure AD s Azure Event Hubs je, že už nemusíte ukládat přihlašovací údaje do kódu. Místo toho můžete požádat o přístupový token OAuth 2,0 z platformy Microsoft Identity Platform. Název prostředku pro vyžádání tokenu je `https://eventhubs.azure.net/` a je stejný pro všechny cloudy/klienty (pro klienty Kafka, prostředek pro vyžádání tokenu `https://<namespace>.servicebus.windows.net` ). Azure AD ověřuje objekt zabezpečení (uživatel, skupina nebo instanční objekt), který spouští aplikaci. Pokud je ověření úspěšné, služba Azure AD vrátí přístupový token do aplikace a aplikace pak může pomocí přístupového tokenu autorizovat požadavky na prostředky Azure Event Hubs.

Když je role přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup může být vymezen na úrovni předplatného, skupiny prostředků, oboru názvů Event Hubs nebo jakéhokoli prostředku. Zabezpečení Azure AD může přiřadit role uživateli, skupině, instančnímu objektu služby nebo [spravované identitě pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Definice role je kolekce oprávnění. Řízení přístupu na základě role v Azure (Azure RBAC) řídí způsob, jakým se tato oprávnění uplatňují prostřednictvím přiřazení role. Přiřazení role se skládá ze tří prvků: objekt zabezpečení, definice role a obor. Další informace najdete v tématu [porozumění různým rolím](../role-based-access-control/overview.md).

## <a name="built-in-roles-for-azure-event-hubs"></a>Předdefinované role pro Azure Event Hubs
Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu k Event Hubs datům pomocí Azure AD a OAuth:

- [Vlastník dat Event Hubs Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): pomocí této role získáte úplný přístup k prostředkům Event Hubs.
- [Datový odesílatel Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): tuto roli použijte, pokud chcete udělit přístup k prostředkům Event Hubs.
- [Příjemce dat Event Hubs Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): pomocí této role získáte příjem přístupu k prostředkům Event Hubs.   

Předdefinované role registru schématu najdete v tématu [role registru schématu](schema-registry-overview.md#azure-role-based-access-control).

> [!IMPORTANT]
> Naše verze Preview podporuje přidávání oprávnění k přístupu k datům Event Hubs k roli vlastníka nebo přispěvatele. Oprávnění pro přístup k datům pro vlastníka a roli přispěvatele se ale už neuplatňují. Pokud používáte roli vlastníka nebo přispěvatel, přepněte se na použití role vlastníka dat Event Hubs Azure.


## <a name="authenticate-from-an-application"></a>Ověření z aplikace
Klíčovou výhodou použití Azure AD s Event Hubs je to, že vaše přihlašovací údaje už nemusíte ukládat do svého kódu. Místo toho můžete požádat o přístupový token OAuth 2,0 z platformy Microsoft Identity Platform. Azure AD ověřuje objekt zabezpečení (uživatel, skupina nebo instanční objekt), který spouští aplikaci. Pokud je ověření úspěšné, Azure AD vrátí přístupový token do aplikace a aplikace pak může použít přístupový token k autorizaci požadavků do Azure Event Hubs.

V následujících částech se dozvíte, jak nakonfigurovat nativní aplikaci nebo webovou aplikaci pro ověřování pomocí platformy Microsoft Identity Platform 2,0. Další informace o Microsoft Identity Platform 2,0 najdete v tématu [Přehled Microsoft Identity Platform (v 2.0)](../active-directory/develop/v2-overview.md).

Přehled toku udělení kódu OAuth 2,0 najdete v tématu [autorizace přístupu k Azure Active Directory webových aplikací pomocí toku udělení kódu oauth 2,0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrace aplikace pomocí tenanta Azure AD
Prvním krokem při použití Azure AD k autorizaci Event Hubs prostředků je registrace klientské aplikace pomocí klienta služby Azure AD z [Azure Portal](https://portal.azure.com/). Při registraci klientské aplikace zadáváte informace o aplikaci službě AD. Azure AD pak poskytuje ID klienta (označované také jako ID aplikace), které můžete použít k přidružení aplikace k modulu runtime služby Azure AD. Další informace o ID klienta najdete [v tématu aplikace a objekty zabezpečení služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Následující obrázky ukazují kroky pro registraci webové aplikace:

![Registrace aplikace](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Pokud aplikaci zaregistrujete jako nativní aplikaci, můžete pro identifikátor URI přesměrování zadat libovolný platný identifikátor URI. Pro nativní aplikace nemusí být tato hodnota skutečnou adresou URL. Pro webové aplikace musí být identifikátor URI pro přesměrování platným identifikátorem URI, protože určuje adresu URL, na které jsou tokeny poskytovány.

Po zaregistrování aplikace se v části **Nastavení** zobrazí **ID aplikace (klienta)** :

![ID aplikace registrované aplikace](./media/authenticate-application/application-id.png)

Další informace o registraci aplikace v Azure AD najdete v tématu [integrování aplikací pomocí Azure Active Directory](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta   
Aplikace potřebuje při žádosti o tokenu klíč klienta k prokázání jeho identity. Chcete-li přidat tajný klíč klienta, postupujte podle těchto kroků.

1. Přejděte k registraci vaší aplikace v Azure Portal.
1. Vyberte nastavení **certifikáty & tajných** kódů.
1. V části **tajné klíče klienta** vyberte **nový tajný klíč klienta** a vytvořte nový tajný klíč.
1. Zadejte popis tajného kódu a vyberte požadovaný interval vypršení platnosti.
1. Hodnotu nového tajného klíče hned zkopírujte do zabezpečeného umístění. Hodnota Fill se zobrazí pouze jednou.

    ![Tajný klíč klienta](./media/authenticate-application/client-secret.png)


## <a name="assign-azure-roles-using-the-azure-portal"></a>Přiřazení rolí Azure pomocí Azure Portal  
Po registraci aplikace přiřadíte instanční objekt aplikace k Event Hubs role služby Azure AD popsané v části [role sestavení pro azure Event Hubs](#built-in-roles-for-azure-event-hubs) . 

1. V [Azure Portal](https://portal.azure.com/)přejděte do svého oboru názvů Event Hubs.
2. Na stránce **Přehled** vyberte centrum událostí, pro které chcete přiřadit roli.

    ![Výběr centra událostí](./media/authenticate-application/select-event-hub.png)
1. Vyberte **Access Control (IAM)** a zobrazte nastavení řízení přístupu pro centrum událostí. 
1. Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí. Na panelu nástrojů vyberte tlačítko **Přidat** a pak vyberte **Přidat přiřazení role** . 

    ![Přidat tlačítko na panelu nástrojů](./media/authenticate-application/role-assignments-add-button.png)
1. Na stránce **Přidat přiřazení role** proveďte následující kroky:
    1. Vyberte **roli Event Hubs** , kterou chcete přiřadit. 
    1. Vyhledejte **objekt zabezpečení** (uživatel, skupina, instanční objekt), ke kterému chcete přiřadit roli. Vyberte ze seznamu **registrovanou aplikaci** . 
    1. Vyberte **Uložit** a uložte přiřazení role. 

        ![Přiřazení role uživateli](./media/authenticate-application/assign-role-to-user.png)
    4. Přepněte na kartu **přiřazení rolí** a potvrďte přiřazení role. Například následující obrázek ukazuje, že **MyWebApp** je v roli **odesilatele dat Azure Event Hubs** . 
        
        ![Uživatel v seznamu](./media/authenticate-application/user-in-list.png)

Můžete postupovat podle podobných kroků a přiřadit obor role Event Hubs oboru názvů, skupiny prostředků nebo předplatného. Po definování role a jejího oboru můžete toto chování otestovat pomocí ukázek [v tomto umístění GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). Další informace o správě přístupu k prostředkům Azure pomocí Azure RBAC a Azure Portal najdete v [tomto článku](..//role-based-access-control/role-assignments-portal.md). 


### <a name="client-libraries-for-token-acquisition"></a>Klientské knihovny pro získání tokenu  
Po zaregistrování aplikace a udělení oprávnění IT k posílání a přijímání dat v Azure Event Hubs můžete do své aplikace přidat kód pro ověření objektu zabezpečení a získání tokenu OAuth 2,0. K ověření a získání tokenu můžete použít buď jednu z [knihoven ověřování Microsoft Identity Platform](../active-directory/develop/reference-v2-libraries.md) , nebo jinou Open Source knihovnu, která podporuje OpenID nebo Connect 1,0. Aplikace pak může pomocí přístupového tokenu autorizovat požadavek oproti službě Azure Event Hubs.

Seznam scénářů, pro které se podporují tokeny, najdete v části [scénáře](https://aka.ms/msal-net-scenarios) v [knihovně Microsoft Authentication Library (MSAL) pro](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) úložiště GitHub .NET.

## <a name="samples"></a>ukázky
- [Ukázky Microsoft. Azure. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 
    
    Tyto ukázky používají starou knihovnu **Microsoft. Azure. EventHubs** , ale můžete ji snadno aktualizovat tak, aby používala nejnovější knihovnu **Azure. Messaging. EventHubs** . Postup přesunutí ukázky z použití staré knihovny do nové verze najdete v [Průvodci migrací z Microsoft. Azure. EventHubs do Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Ukázky pro Azure. Messaging. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Tato ukázka se aktualizovala tak, aby používala nejnovější knihovnu **Azure. Messaging. EventHubs** .

## <a name="next-steps"></a>Další kroky
- Další informace o službě Azure RBAC najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md)?
- Další informace o tom, jak přiřadit a spravovat přiřazení rolí Azure pomocí Azure PowerShell, Azure CLI nebo REST API, najdete v těchto článcích:
    - [Přidání nebo odebrání přiřazení rolí v Azure pomocí Azure PowerShellu](../role-based-access-control/role-assignments-powershell.md)  
    - [Přidání nebo odebrání přiřazení rolí v Azure pomocí Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Přidání nebo odebrání přiřazení rolí v Azure pomocí REST API](../role-based-access-control/role-assignments-rest.md)
    - [Přidání přiřazení rolí Azure pomocí šablon Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Podívejte se na následující související články:
- [Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Event Hubs](authenticate-managed-identity.md)
- [Ověřování požadavků na Azure Event Hubs pomocí sdílených přístupových podpisů](authenticate-shared-access-signature.md)
- [Autorizace přístupu k prostředkům Event Hubs pomocí Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorizace přístupu k prostředkům Event Hubs pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md)
