---
title: Ověření aplikace pro přístup k Azure Service Bus entit
description: Tento článek poskytuje informace o ověřování aplikace s Azure Active Directory pro přístup k Azure Service Bus entit (fronty, témata atd.).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: c4e19c0ab26d491ba0b95159e274383431aefaee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518224"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Ověřování a autorizace aplikace s Azure Active Directory pro přístup k Azure Service Busm entitám
Azure Service Bus podporuje použití Azure Active Directory (Azure AD) k autorizaci požadavků na Service Bus entit (fronty, témata, odběry nebo filtry). Pomocí Azure AD můžete použít řízení přístupu na základě role Azure (Azure RBAC) k udělení oprávnění objektu zabezpečení, který může být uživatel, skupina nebo instanční objekt aplikace. Další informace o rolích a přiřazení rolí najdete v tématu [Principy různých rolí](../role-based-access-control/overview.md).

## <a name="overview"></a>Přehled
Když se objekt zabezpečení (uživatel, skupina nebo aplikace) pokusí o přístup k Service Bus entitě, musí být žádost autorizována. S Azure AD je přístup k prostředku v procesu se dvěma kroky. 

 1. Nejprve je ověřená identita objektu zabezpečení a je vrácen token OAuth 2,0. Název prostředku pro vyžádání tokenu `https://servicebus.azure.net` .
 1. V dalším kroku se token předává jako součást požadavku služby Service Bus k autorizaci přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby žádost o aplikaci obsahovala přístupový token OAuth 2,0 za běhu. Pokud je aplikace spuštěná v rámci entity Azure, jako je třeba virtuální počítač Azure, sada škálování virtuálního počítače nebo aplikace funkce Azure, může pro přístup k prostředkům použít spravovanou identitu. Informace o tom, jak ověřit požadavky prováděné spravovanou identitou pro Service Bus službu, najdete v tématu [ověření přístupu k prostředkům Azure Service Bus pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](service-bus-managed-service-identity.md). 

Autorizační krok vyžaduje, aby se k objektu zabezpečení přiřadila jedna nebo více rolí Azure. Azure Service Bus poskytuje role Azure, které zahrnují sady oprávnění pro prostředky Service Bus. Role, které jsou přiřazeny objektu zabezpečení, určují oprávnění, která bude mít objekt zabezpečení. Další informace o přiřazování rolí Azure Azure Service Bus najdete v tématu [předdefinované role Azure pro Azure Service Bus](#azure-built-in-roles-for-azure-service-bus). 

Nativní aplikace a webové aplikace, které vytvářejí požadavky na Service Bus, mohou být také autorizovány pomocí Azure AD. V tomto článku se dozvíte, jak požádat o přístupový token a použít ho k autorizaci žádostí o Service Bus prostředky. 


## <a name="assigning-azure-roles-for-access-rights"></a>Přiřazení rolí Azure pro přístupová práva
Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [Azure RBAC](../role-based-access-control/overview.md). Azure Service Bus definuje sadu předdefinovaných rolí Azure, které zahrnují společné sady oprávnění používané pro přístup k Service Bus entitám, a můžete také definovat vlastní role pro přístup k datům.

Když je role Azure přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup může být vymezen na úrovni předplatného, skupiny prostředků nebo oboru názvů Service Bus. Objekt zabezpečení Azure AD může být uživatelem, skupinou, instančním objektem služby nebo [spravovanou identitou pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Předdefinované role Azure pro Azure Service Bus
Pro Azure Service Bus už je Správa oborů názvů a všech souvisejících prostředků prostřednictvím Azure Portal a rozhraní API pro správu prostředků Azure chráněná pomocí modelu Azure RBAC. Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu k oboru názvů Service Bus:

- [Azure Service Bus vlastník dat](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): povoluje přístup k datům Service Bus oboru názvů a jeho entit (fronty, témata, odběry a filtry).
- [Azure Service Bus odesílatel dat](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Tato role slouží k poskytnutí přístupu pro přístup k oboru názvů Service Bus a jeho entitám.
- [Azure Service Bus příjemce dat](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Tato role slouží k poskytnutí přístupu k oboru názvů Service Bus a jeho entitám. 

## <a name="resource-scope"></a>Obor prostředku 
Než do objektu zabezpečení přiřadíte roli Azure, určete rozsah přístupu, který má objekt zabezpečení mít. Osvědčené postupy určují, že vždy nejlépe přidělíte jenom nejužšímu možnému rozsahu.

Následující seznam popisuje úrovně, na jejichž základě můžete nastavit rozsah přístupu k prostředkům Service Bus, počínaje nejužším rozsahem:

- **Zařazení do fronty**, **tématu** nebo **předplatného**: přiřazení role se vztahuje na konkrétní entitu Service Bus. V současné době Azure Portal nepodporuje přiřazování uživatelů/skupin/spravovaných identit do Service Bus rolí Azure na úrovni předplatného. 
- **Service Bus obor názvů**: přiřazování rolí zahrnuje celou topologii Service Bus pod oborem názvů a do skupiny uživatelů, které jsou k ní přidružené.
- **Skupina prostředků**: přiřazení role se vztahuje na všechny prostředky Service Bus v rámci skupiny prostředků.
- **Předplatné**: přiřazení role se vztahuje na všechny prostředky Service Bus ve všech skupinách prostředků v rámci předplatného.

> [!NOTE]
> Mějte na paměti, že rozšíření přiřazení rolí Azure může trvat až pět minut. 

Další informace o tom, jak jsou předdefinované role definované, najdete v tématu [vysvětlení definic rolí](../role-based-access-control/role-definitions.md#management-and-data-operations). Informace o vytváření vlastních rolí Azure najdete v tématu [vlastní role Azure](../role-based-access-control/custom-roles.md).


## <a name="assign-azure-roles-using-the-azure-portal"></a>Přiřazení rolí Azure pomocí Azure Portal  
Další informace o správě přístupu k prostředkům Azure pomocí Azure RBAC a Azure Portal najdete v [tomto článku](..//role-based-access-control/role-assignments-portal.md). 

Po určení vhodného oboru pro přiřazení role přejděte k tomuto prostředku v Azure Portal. Zobrazit nastavení řízení přístupu (IAM) pro prostředek a podle těchto pokynů můžete spravovat přiřazení rolí:

> [!NOTE]
> Kroky popsané níže přiřadí roli vašemu oboru názvů Service Bus. Stejný postup můžete použít k přiřazení role k ostatním podporovaným oborům (skupina prostředků, předplatné atd.).

1. V [Azure Portal](https://portal.azure.com/)přejděte do svého oboru názvů Service Bus. V nabídce vlevo vyberte **Access Control (IAM)** a zobrazte nastavení řízení přístupu pro obor názvů. Pokud potřebujete vytvořit obor názvů Service Bus, postupujte podle pokynů v tomto článku: [Vytvoření oboru názvů pro zasílání zpráv Service Bus](service-bus-create-namespace-portal.md).

    ![V nabídce vlevo vyberte Access Control](./media/authenticate-application/select-access-control-menu.png)
1. Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí. Na panelu nástrojů vyberte tlačítko **Přidat** a pak vyberte **Přidat přiřazení role**. 

    ![Přidat tlačítko na panelu nástrojů](./media/authenticate-application/role-assignments-add-button.png)
1. Na stránce **Přidat přiřazení role** proveďte následující kroky:
    1. Vyberte **roli Service Bus** , kterou chcete přiřadit. 
    1. Vyhledejte **objekt zabezpečení** (uživatel, skupina, instanční objekt), ke kterému chcete přiřadit roli.
    1. Vyberte **Uložit** a uložte přiřazení role. 

        ![Přiřazení role uživateli](./media/authenticate-application/assign-role-to-user.png)
    4. Identita, ke které jste přiřadili roli, se zobrazí v seznamu v rámci této role. Například na následujícím obrázku vidíte, že Azure – uživatelé jsou v roli Azure Service Bus data Owner. 
        
        ![Uživatel v seznamu](./media/authenticate-application/user-in-list.png)

Podle podobných kroků můžete přiřadit obor role ke skupině prostředků nebo předplatnému. Po definování role a jejího oboru můžete toto chování otestovat pomocí [ukázek na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Ověření z aplikace
Klíčovou výhodou použití Azure AD s Service Bus je to, že vaše přihlašovací údaje už nemusíte ukládat do svého kódu. Místo toho můžete požádat o přístupový token OAuth 2,0 z platformy Microsoft Identity Platform. Azure AD ověřuje objekt zabezpečení (uživatel, skupina nebo instanční objekt), který spouští aplikaci. Pokud je ověření úspěšné, služba Azure AD vrátí přístupový token do aplikace a aplikace pak může pomocí přístupového tokenu autorizovat požadavky na Azure Service Bus.

V následujících částech se dozvíte, jak nakonfigurovat nativní aplikaci nebo webovou aplikaci pro ověřování pomocí platformy Microsoft Identity Platform 2,0. Další informace o Microsoft Identity Platform 2,0 najdete v tématu [Přehled Microsoft Identity Platform (v 2.0)](../active-directory/develop/v2-overview.md).

Přehled toku udělení kódu OAuth 2,0 najdete v tématu [autorizace přístupu k Azure Active Directory webových aplikací pomocí toku udělení kódu oauth 2,0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrace aplikace pomocí tenanta Azure AD
Prvním krokem při použití Azure AD k autorizaci Service Bus entit je registrace klientské aplikace pomocí klienta služby Azure AD z [Azure Portal](https://portal.azure.com/). Při registraci klientské aplikace zadáváte informace o aplikaci službě AD. Azure AD pak poskytuje ID klienta (označované také jako ID aplikace), které můžete použít k přidružení aplikace k modulu runtime služby Azure AD. Další informace o ID klienta najdete [v tématu aplikace a objekty zabezpečení služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Následující obrázky ukazují kroky pro registraci webové aplikace:

![Registrace aplikace](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Pokud aplikaci zaregistrujete jako nativní aplikaci, můžete pro identifikátor URI přesměrování zadat libovolný platný identifikátor URI. Pro nativní aplikace nemusí být tato hodnota skutečnou adresou URL. Pro webové aplikace musí být identifikátor URI pro přesměrování platným identifikátorem URI, protože určuje adresu URL, na které jsou tokeny poskytovány.

Po zaregistrování aplikace se v části **Nastavení** zobrazí **ID aplikace (klienta)** :

![ID aplikace registrované aplikace](./media/authenticate-application/application-id.png)

Další informace o registraci aplikace v Azure AD najdete v tématu [integrování aplikací pomocí Azure Active Directory](../active-directory/develop/quickstart-register-app.md).

> [!IMPORTANT]
> Poznamenejte si **TenantId** a **ApplicationId**. Tyto hodnoty budete potřebovat ke spuštění aplikace.

### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta   
Aplikace potřebuje při žádosti o tokenu klíč klienta k prokázání jeho identity. Chcete-li přidat tajný klíč klienta, postupujte podle těchto kroků.

1. Pokud na stránce ještě nemáte, přejděte k registraci vaší aplikace v Azure Portal.
1. V nabídce vlevo vyberte **certifikáty & tajné klíče** .
1. V části **tajné klíče klienta** vyberte **nový tajný klíč klienta** a vytvořte nový tajný klíč.

    ![Nový tajný klíč klienta – tlačítko](./media/authenticate-application/new-client-secret-button.png)
1. Zadejte popis tajného kódu a vyberte požadovaný interval vypršení platnosti a pak vyberte **Přidat**.

    ![Přidat stránku tajného klíče klienta](./media/authenticate-application/add-client-secret-page.png)
1. Hodnotu nového tajného klíče hned zkopírujte do zabezpečeného umístění. Hodnota Fill se zobrazí pouze jednou.

    ![Tajný klíč klienta](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Oprávnění pro rozhraní Service Bus API
Pokud je vaše aplikace Konzolová aplikace, musíte zaregistrovat nativní aplikaci a přidat oprávnění rozhraní API pro **Microsoft. ServiceBus** do požadované sady **oprávnění** . Nativní aplikace také potřebují identifikátor **URI přesměrování** ve službě Azure AD, který slouží jako identifikátor; identifikátor URI nemusí být cílovým umístěním v síti. Použijte v `https://servicebus.microsoft.com` tomto příkladu, protože vzorový kód již používá tento identifikátor URI.

### <a name="client-libraries-for-token-acquisition"></a>Klientské knihovny pro získání tokenu  
Po zaregistrování aplikace a udělení oprávnění k odesílání a přijímání dat v Azure Service Bus můžete do své aplikace přidat kód pro ověření objektu zabezpečení a získání tokenu OAuth 2,0. K ověření a získání tokenu můžete použít buď jednu z [knihoven ověřování Microsoft Identity Platform](../active-directory/develop/reference-v2-libraries.md) , nebo jinou Open Source knihovnu, která podporuje OpenID nebo Connect 1,0. Aplikace pak může použít přístupový token k autorizaci žádosti o Azure Service Bus.

Seznam scénářů, pro které se podporují tokeny, najdete v části [scénáře](https://aka.ms/msal-net-scenarios) v [knihovně Microsoft Authentication Library (MSAL) pro](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) úložiště GitHub .NET.

## <a name="sample-on-github"></a>Ukázka na GitHubu
Viz následující ukázka na GitHubu: [řízení přístupu na základě role Azure pro Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

Použijte možnost **přihlášení pomocí tajného klíče klienta** , nikoli možnost **interaktivního přihlášení uživatele** . Když použijete možnost tajný klíč klienta, nezobrazí se místní okno. Aplikace využívá ID tenanta a ID aplikace pro ověřování. 

### <a name="run-the-sample"></a>Spuštění ukázky

Než budete moct ukázku spustit, upravte soubor **app.config** a v závislosti na vašem scénáři nastavte následující hodnoty:

- `tenantId`: Nastavte na hodnotu **TenantId** .
- `clientId`: Nastavte na hodnotu **ApplicationId** .
- `clientSecret`: Pokud se chcete přihlásit pomocí tajného kódu klienta, vytvořte ho v Azure AD. Místo nativní aplikace taky použijte webovou aplikaci nebo rozhraní API. Do oboru názvů, který jste dříve vytvořili, taky přidejte aplikaci do **Access Control (IAM)** .
- `serviceBusNamespaceFQDN`: Nastavte na úplný název DNS nově vytvořeného oboru názvů Service Bus; například `example.servicebus.windows.net` .
- `queueName`: Nastavte název fronty, kterou jste vytvořili.
- Identifikátor URI pro přesměrování, který jste zadali v aplikaci v předchozích krocích.

Když spustíte konzolovou aplikaci, zobrazí se výzva k výběru scénáře. Vyberte **interaktivní uživatelské přihlášení** zadáním jeho čísla a stisknutím klávesy ENTER. Aplikace zobrazí okno přihlášení, požádá o souhlas s přístupem k Service Bus a potom službu použije ke spuštění prostřednictvím scénáře odeslání a přijetí pomocí identity přihlášení.


## <a name="next-steps"></a>Další kroky
- Další informace o službě Azure RBAC najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md)?
- Další informace o tom, jak přiřadit a spravovat přiřazení rolí Azure pomocí Azure PowerShell, Azure CLI nebo REST API, najdete v těchto článcích:
    - [Přidání nebo odebrání přiřazení rolí v Azure pomocí Azure PowerShellu](../role-based-access-control/role-assignments-powershell.md)  
    - [Přidání nebo odebrání přiřazení rolí v Azure pomocí Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Přidání nebo odebrání přiřazení rolí v Azure pomocí REST API](../role-based-access-control/role-assignments-rest.md)
    - [Přidání nebo odebrání přiřazení rolí Azure pomocí šablon Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Pokud se o přenosu zpráv přes Service Bus chcete dozvědět víc, pročtěte si následující témata.

- [Ukázky Service Bus Azure RBAC](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
- [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
- [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)