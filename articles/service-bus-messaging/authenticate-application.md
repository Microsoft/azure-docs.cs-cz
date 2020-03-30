---
title: Ověření aplikace pro přístup k entitám Azure Service Bus
description: Tento článek obsahuje informace o ověřování aplikace pomocí služby Azure Active Directory pro přístup k entitám Azure Service Bus (fronty, témata atd.).
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259289"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Ověření a autorizace aplikace pomocí Služby Azure Active Directory pro přístup k entitám Azure Service Bus
Azure Service Bus podporuje použití Azure Active Directory (Azure AD) autorizovat požadavky na entity Service Bus (fronty, témata, předplatná nebo filtry). Pomocí služby Azure AD můžete pomocí řízení přístupu na základě rolí (RBAC) udělit oprávnění k objektu zabezpečení, který může být uživatel, skupina nebo instanční objekt služby aplikace. Další informace o rolích a přiřazeních rolí naleznete v [tématu Principy různých rolí](../role-based-access-control/overview.md).

## <a name="overview"></a>Přehled
Když se objekt zabezpečení (uživatel, skupina nebo aplikace) pokusí o přístup k entitě service bus, musí být požadavek autorizován. S Azure AD, přístup k prostředku je dvoustupňový proces. 

 1. Nejprve je ověřena identita objektu zabezpečení a je vrácen token OAuth 2.0. Název prostředku pro vyžádání `https://servicebus.azure.net`tokenu je .
 1. Dále je token předán jako součást požadavku na službu Service Bus k autorizaci přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby požadavek aplikace obsahoval přístupový token OAuth 2.0 za běhu. Pokud aplikace běží v rámci entity Azure, jako je například virtuální počítač Azure, škálovací sada virtuálního počítače nebo aplikace Azure Function, můžete použít spravovanou identitu pro přístup k prostředkům. Informace o tom, jak ověřovat požadavky spravované identity na službu Service Bus, najdete [v tématu Ověřování přístupu k prostředkům Azure Service Bus pomocí Služby Azure Active Directory a spravovaných identit pro prostředky Azure](service-bus-managed-service-identity.md). 

Krok autorizace vyžaduje, aby byla k objektu zabezpečení přiřazena jedna nebo více rolí RBAC. Azure Service Bus poskytuje role RBAC, které zahrnují sady oprávnění pro prostředky service bus. Role, které jsou přiřazeny k objektu zabezpečení určit oprávnění, která bude mít objekt zabezpečení. Další informace o přiřazování rolí RBAC službě Azure Service Bus najdete [v tématu integrované role RBAC pro Azure Service Bus](#built-in-rbac-roles-for-azure-service-bus). 

Nativní aplikace a webové aplikace, které pořizují požadavky na Service Bus můžete také autorizovat s Azure AD. Tento článek ukazuje, jak požádat o přístupový token a použít jej k autorizaci požadavků na prostředky service bus. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Přiřazení rolí RBAC pro přístupová práva
Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě rolí (RBAC).](../role-based-access-control/overview.md) Azure Service Bus definuje sadu předdefinovaných rolí RBAC, které zahrnují společné sady oprávnění používaných pro přístup k entitám Service Bus a můžete také definovat vlastní role pro přístup k datům.

Když je role RBAC přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup může být vymezen na úroveň předplatného, skupiny prostředků nebo oboru názvů Service Bus. Zaregistrovaný objekt zabezpečení Azure AD může být uživatel, skupina, instanční objekt aplikační služby nebo [spravovaná identita pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Integrované role RBAC pro Azure Service Bus
Pro Azure Service Bus je správa oborů názvů a všech souvisejících prostředků prostřednictvím portálu Azure portal a rozhraní API pro správu prostředků Azure již chráněna pomocí modelu řízení přístupu (RBAC) *na základě rolí.* Azure poskytuje níže integrované role RBAC pro autorizaci přístupu k oboru názvů Service Bus:

- [Vlastník dat služby Azure Service Bus:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)Povolí přístup k oboru názvů Service Bus a jeho entit (fronty, témata, předplatná a filtry).
- [Odesílatel dat služby Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Tato role slouží k poskytnutí přístupu k přístupu k oboru názvů Service Bus a jeho entitám.
- [Příjemce dat služby Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Tato role slouží k poskytnutí přístupu k oboru názvů Service Bus a jeho entitám. 

## <a name="resource-scope"></a>Obor prostředků 
Před přiřazením role RBAC k objektu zabezpečení určete rozsah přístupu, který by měl mít zaregistrovaný objekt zabezpečení. Osvědčené postupy určují, že je vždy nejlepší udělit pouze nejužší možný rozsah.

Následující seznam popisuje úrovně, na kterých můžete obor přístup k prostředkům service bus, počínaje nejužší maže:

- **Fronta**, **téma**nebo **předplatné**: Přiřazení role se vztahuje na konkrétní entitu služby Service Bus. V současné době portál Azure nepodporuje přiřazování uživatelů/skupin/spravovaných identit k rolím RBAC služby Service Bus na úrovni předplatného. 
- **Obor názvů service bus**: Přiřazení role zahrnuje celou topologii služby Service Bus pod oborem názvů a do skupiny spotřebitelů, která je k ní přidružena.
- **Skupina zdrojů**: Přiřazení role se vztahuje na všechny prostředky služby Service Bus v rámci skupiny prostředků.
- **Předplatné**: Přiřazení role se vztahuje na všechny prostředky service bus ve všech skupinách prostředků v předplatném.

> [!NOTE]
> Mějte na paměti, že přiřazení rolí RBAC může trvat až pět minut k šíření. 

Další informace o tom, jak jsou definovány předdefinované role, naleznete [v tématu Principy definic rolí](../role-based-access-control/role-definitions.md#management-and-data-operations). Informace o vytváření vlastních rolí RBAC najdete v [tématu Vytvoření vlastních rolí pro řízení přístupu na základě rolí Azure](../role-based-access-control/custom-roles.md).


## <a name="assign-rbac-roles-using-the-azure-portal"></a>Přiřazení rolí RBAC pomocí portálu Azure  
Další informace o správě přístupu k prostředkům Azure pomocí RBAC a portálu Azure najdete v [tomto článku](..//role-based-access-control/role-assignments-portal.md). 

Po určení vhodného oboru pro přiřazení role přejděte na tento prostředek na webu Azure Portal. Zobrazte nastavení řízení přístupu (IAM) pro prostředek a podle následujících pokynů spravujte přiřazení rolí:

> [!NOTE]
> Níže popsané kroky přiřadí roli oboru názvů Service Bus. Stejným postupem můžete přiřadit roli dalším podporovaným oborům (skupina prostředků, předplatné atd.).

1. Na [webu Azure Portal](https://portal.azure.com/)přejděte do oboru názvů Service Bus. V yberte **Řízení přístupu (IAM)** v levé nabídce, chcete-li zobrazit nastavení řízení přístupu pro obor názvů. Pokud potřebujete vytvořit obor názvů Service Bus, postupujte podle pokynů z tohoto článku: [Vytvoření oboru názvů zasílání zpráv služby Service Bus](service-bus-create-namespace-portal.md).

    ![V levé nabídce vyberte řízení přístupu.](./media/authenticate-application/select-access-control-menu.png)
1. Výběrem karty **Přiřazení rolí** zobrazíte seznam přiřazení rolí. Na panelu nástrojů vyberte tlačítko **Přidat** a pak vyberte **Přidat přiřazení role**. 

    ![Tlačítko Přidat na panelu nástrojů](./media/authenticate-application/role-assignments-add-button.png)
1. Na stránce **Přidat přiřazení rolí** postupujte takto:
    1. Vyberte **roli Service Bus,** kterou chcete přiřadit. 
    1. Vyhledejte **zaregistrovaný objekt zabezpečení** (uživatel, skupina, instanční objekt), ke kterému chcete roli přiřadit.
    1. Chcete-li uložit přiřazení role, vyberte **Uložit.** 

        ![Přiřazení role uživateli](./media/authenticate-application/assign-role-to-user.png)
    4. Identita, které jste roli přiřadili, se zobrazí v seznamu v této roli. Například následující obrázek ukazuje, že uživatelé Azure je v roli vlastníka dat služby Azure Service Bus. 
        
        ![Uživatel v seznamu](./media/authenticate-application/user-in-list.png)

Můžete postupovat podle podobných kroků a přiřadit roli vymezenou skupině prostředků nebo předplatnému. Jakmile definujete roli a její obor, můžete toto chování otestovat pomocí [ukázek na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Ověření z aplikace
Klíčovou výhodou použití Azure AD s Service Bus je, že vaše přihlašovací údaje už nemusí být uloženy ve vašem kódu. Místo toho můžete požádat o přístupový token OAuth 2.0 z platformy identit Microsoftu. Azure AD ověřuje objekt zabezpečení (uživatel, skupina nebo instanční objekt) spuštěné aplikace. Pokud ověřování proběhne úspěšně, Azure AD vrátí přístupový token do aplikace a aplikace pak můžete použít přístupový token k autorizaci požadavků na Azure Service Bus.

Následující části ukazují, jak nakonfigurovat nativní aplikaci nebo webovou aplikaci pro ověřování pomocí platformy Microsoft Identity Platform 2.0. Další informace o platformě Microsoft Identity Platform 2.0 naleznete [v přehledu platformy microsoftidentit (v2.0).](../active-directory/develop/v2-overview.md)

Přehled toku udělení kódu OAuth 2.0 najdete v tématu [Autorizace přístupu k webovým aplikacím Služby Azure Active Directory pomocí toku udělení kódu OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrace aplikace u klienta Azure AD
Prvním krokem při používání Azure AD k autorizaci entit Service Bus je registrace klientské aplikace s tenantem Azure AD z [portálu Azure](https://portal.azure.com/). Při registraci klientské aplikace zadáte informace o aplikaci do služby AD. Azure AD pak poskytuje ID klienta (označované také jako ID aplikace), které můžete použít k přidružení aplikace k runtime Azure AD. Další informace o ID klienta najdete v [tématu Objekty za registrovaných aplikací a služeb ve službě Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Následující obrázky ukazují kroky pro registraci webové aplikace:

![Registrace aplikace](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Pokud zaregistrujete aplikaci jako nativní aplikaci, můžete zadat libovolný platný identifikátor URI pro identifikátor URI přesměrování. Pro nativní aplikace tato hodnota nemusí být skutečná adresa URL. U webových aplikací musí být identifikátor URI přesměrování platný identifikátor URI, protože určuje adresu URL, na kterou jsou k dispozici tokeny.

Po registraci aplikace se v části Nastavení zobrazí **ID aplikace (klienta):** **Settings**

![ID přihlášky registrované přihlášky](./media/authenticate-application/application-id.png)

Další informace o registraci aplikace ve službě Azure AD najdete v [tématu Integrace aplikací s Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).

> [!IMPORTANT]
> Poznamenejte si **TenantId** a **ApplicationId**. Tyto hodnoty budete potřebovat ke spuštění aplikace.

### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta   
Aplikace potřebuje tajný klíč klienta k prokázání své identity při požadování tokenu. Chcete-li přidat tajný klíč klienta, postupujte takto.

1. Pokud ještě na stránce nejste, přejděte na registraci aplikace na webu Azure Portal.
1. V levé nabídce vyberte **& tajné kódy.**
1. V části **Tajné klíče klienta**vyberte **Nový tajný klíč klienta** a vytvořte nový tajný klíč.

    ![Nový tajný klíč klienta - tlačítko](./media/authenticate-application/new-client-secret-button.png)
1. Zadejte popis tajného klíče a zvolte žádaný interval vypršení platnosti a pak vyberte **Přidat**.

    ![Přidat stránku tajného klíče klienta](./media/authenticate-application/add-client-secret-page.png)
1. Okamžitě zkopírujte hodnotu nového tajného klíče do zabezpečeného umístění. Hodnota výplně se zobrazí pouze jednou.

    ![Tajný klíč klienta](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Oprávnění pro rozhraní API služby Service Bus
Pokud je vaše aplikace konzolová aplikace, musíte zaregistrovat nativní aplikaci a přidat oprávnění rozhraní API pro **Microsoft.ServiceBus** do **sady požadovaných oprávnění.** Nativní aplikace také potřebují **přesměrování URI** ve službě Azure AD, který slouží jako identifikátor; identifikátor URI nemusí být cílem sítě. Použijte `https://servicebus.microsoft.com` pro tento příklad, protože ukázkový kód již používá tento identifikátor URI.

### <a name="client-libraries-for-token-acquisition"></a>Klientské knihovny pro získávání tokenů  
Jakmile zaregistrujete aplikaci a udělíte jí oprávnění k odesílání a přijímání dat v Azure Service Bus, můžete do aplikace přidat kód k ověření objektu zabezpečení a získat token OAuth 2.0. Chcete-li ověřit a získat token, můžete použít jednu z [knihoven ověřování platformy identit Microsoftu](../active-directory/develop/reference-v2-libraries.md) nebo jinou otevřenou knihovnu, která podporuje OpenID nebo Connect 1.0. Vaše aplikace pak můžete použít přístupový token k autorizaci požadavku proti Azure Service Bus.

Seznam scénářů, pro které je podporováno získávání tokenů, naleznete v části [Scénáře](https://aka.ms/msal-net-scenarios) [v knihovně ověřování Microsoft (MSAL) pro](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) úložiště .NET GitHub.

## <a name="sample-on-github"></a>Ukázka na GitHubu
Podívejte se na následující ukázku na [GitHubu: Řízení přístupu na základě rolí pro service bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

Použijte možnost **Klientské tajné přihlášení,** ne **možnost Interaktivní přihlášení uživatele.** Při použití možnosti tajného klíče klienta se nezobrazí automaticky otevírané okno. Aplikace využívá ID klienta a ID aplikace pro ověřování. 

### <a name="run-the-sample"></a>Spuštění ukázky

Před spuštěním ukázky upravte soubor **app.config** a v závislosti na scénáři nastavte následující hodnoty:

- `tenantId`: Nastavit hodnotu **TenantId.**
- `clientId`: Nastavit hodnotu **ApplicationId.**
- `clientSecret`: Pokud se chcete přihlásit pomocí tajného klíče klienta, vytvořte ho ve službě Azure AD. Místo nativní aplikace používejte také webovou aplikaci nebo rozhraní API. Také přidejte aplikaci v části **Řízení přístupu (IAM)** v oboru názvů, který jste dříve vytvořili.
- `serviceBusNamespaceFQDN`: Nastavte úplný název DNS nově vytvořeného oboru názvů služby Service Bus. například `example.servicebus.windows.net`.
- `queueName`: Nastavte název fronty, kterou jste vytvořili.
- Identifikátor URI přesměrování, který jste zadali v aplikaci v předchozích krocích.

Při spuštění aplikace konzoly budete vyzváni k výběru scénáře. Vyberte **Interaktivní přihlášení uživatele** zadáním jeho čísla a stisknutím klávesy ENTER. Aplikace zobrazí přihlašovací okno, požádá o váš souhlas s přístupem k service bus a pak pomocí služby spustí scénář odesílání a přijímání pomocí přihlašovací identity.


## <a name="next-steps"></a>Další kroky
- Další informace o RBAC, naleznete v [tématu Co je řízení přístupu na základě rolí (RBAC)](../role-based-access-control/overview.md)?
- Informace o tom, jak přiřazovat a spravovat přiřazení rolí RBAC pomocí Azure PowerShellu, rozhraní API Azure nebo rozhraní REST API, najdete v těchto článcích:
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí Azure PowerShellu](../role-based-access-control/role-assignments-powershell.md)  
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí azure cli](../role-based-access-control/role-assignments-cli.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí rozhraní REST API](../role-based-access-control/role-assignments-rest.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí šablon Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Pokud se o přenosu zpráv přes Service Bus chcete dozvědět víc, pročtěte si následující témata.

- [Ukázky RBAC sběrnice](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
- [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
- [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
