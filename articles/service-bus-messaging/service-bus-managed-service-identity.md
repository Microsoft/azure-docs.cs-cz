---
title: Spravované identity pro prostředky Azure s Service Bus
description: Tento článek popisuje, jak používat spravované identity pro přístup k Azure Service Bus entit (fronty, témata a předplatná).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1deb3bdf823f1554e302bb35baabe444223f9008
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88079854"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Azure Service Bus
[Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) je funkce mezi Azure, která umožňuje vytvořit zabezpečenou identitu přidruženou k nasazení, ve kterém se spouští kód aplikace. Tuto identitu pak můžete přidružit k rolím řízení přístupu, které udělují vlastní oprávnění pro přístup ke konkrétním prostředkům Azure, které vaše aplikace potřebuje.

Se spravovanými identitami spravuje platforma Azure tuto identitu modulu runtime. Nemusíte ukládat a chránit přístupové klíče v kódu nebo konfiguraci aplikace, a to buď pro samotnou identitu, nebo pro prostředky, ke kterým potřebujete přístup. Klientská aplikace Service Bus spuštěná v aplikaci Azure App Service nebo ve virtuálním počítači s povolenými spravovanými entitami pro podporu prostředků Azure nemusí zpracovávat pravidla a klíče SAS ani žádné jiné přístupové tokeny. Klientská aplikace potřebuje jenom adresu koncového bodu oboru názvů pro zasílání zpráv Service Bus. Když se aplikace připojí, Service Bus sváže kontext spravované entity s klientem v operaci, která je uvedená v příkladu dále v tomto článku. Po přidružení ke spravované identitě může klient Service Bus provádět všechny autorizované operace. Autorizace se uděluje přidružením spravované entity k rolím Service Bus. 

## <a name="overview"></a>Přehled
Když se objekt zabezpečení (uživatel, skupina nebo aplikace) pokusí o přístup k Service Bus entitě, musí být žádost autorizována. S Azure AD je přístup k prostředku v procesu se dvěma kroky. 

 1. Nejprve je ověřená identita objektu zabezpečení a je vrácen token OAuth 2,0. Název prostředku pro vyžádání tokenu `https://servicebus.azure.net` .
 1. V dalším kroku se token předává jako součást požadavku služby Service Bus k autorizaci přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby žádost o aplikaci obsahovala přístupový token OAuth 2,0 za běhu. Pokud je aplikace spuštěná v rámci entity Azure, jako je třeba virtuální počítač Azure, sada škálování virtuálního počítače nebo aplikace funkce Azure, může pro přístup k prostředkům použít spravovanou identitu. 

Autorizační krok vyžaduje, aby se k objektu zabezpečení přiřadila jedna nebo více rolí Azure. Azure Service Bus poskytuje role Azure, které zahrnují sady oprávnění pro prostředky Service Bus. Role, které jsou přiřazeny objektu zabezpečení, určují oprávnění, která bude mít objekt zabezpečení. Další informace o přiřazování rolí Azure Azure Service Bus najdete v tématu [předdefinované role Azure pro Azure Service Bus](#azure-built-in-roles-for-azure-service-bus). 

Nativní aplikace a webové aplikace, které vytvářejí požadavky na Service Bus, mohou být také autorizovány pomocí Azure AD. V tomto článku se dozvíte, jak požádat o přístupový token a použít ho k autorizaci žádostí o Service Bus prostředky. 


## <a name="assigning-azure-roles-for-access-rights"></a>Přiřazení rolí Azure pro přístupová práva
Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md). Azure Service Bus definuje sadu předdefinovaných rolí Azure, které zahrnují společné sady oprávnění používané pro přístup k Service Bus entitám, a můžete také definovat vlastní role pro přístup k datům.

Když je role Azure přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup může být vymezen na úrovni předplatného, skupiny prostředků nebo oboru názvů Service Bus. Objekt zabezpečení Azure AD může být uživatelem, skupinou, instančním objektem služby nebo spravovanou identitou pro prostředky Azure.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Předdefinované role Azure pro Azure Service Bus
Pro Azure Service Bus už je Správa oborů názvů a všech souvisejících prostředků prostřednictvím Azure Portal a rozhraní API pro správu prostředků Azure chráněná pomocí modelu Azure RBAC. Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu k oboru názvů Service Bus:

- [Azure Service Bus vlastník dat](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): povoluje přístup k datům Service Bus oboru názvů a jeho entit (fronty, témata, odběry a filtry).
- [Azure Service Bus odesílatel dat](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Tato role slouží k poskytnutí přístupu pro přístup k oboru názvů Service Bus a jeho entitám.
- [Azure Service Bus příjemce dat](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Tato role slouží k poskytnutí přístupu k oboru názvů Service Bus a jeho entitám. 

## <a name="resource-scope"></a>Obor prostředku 
Než do objektu zabezpečení přiřadíte roli Azure, určete rozsah přístupu, který má objekt zabezpečení mít. Osvědčené postupy určují, že vždy nejlépe přidělíte jenom nejužšímu možnému rozsahu.

Následující seznam popisuje úrovně, na jejichž základě můžete nastavit rozsah přístupu k prostředkům Service Bus, počínaje nejužším rozsahem:

- **Zařazení do fronty**, **tématu**nebo **předplatného**: přiřazení role se vztahuje na konkrétní entitu Service Bus. V současné době Azure Portal nepodporuje přiřazování uživatelů/skupin/spravovaných identit do Service Bus rolí Azure na úrovni předplatného. Tady je příklad použití příkazu Azure CLI: [AZ-role-Assignment-Create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) k přiřazení identity k Service Bus role Azure: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus obor názvů**: přiřazování rolí zahrnuje celou topologii Service Bus pod oborem názvů a do skupiny uživatelů, které jsou k ní přidružené.
- **Skupina prostředků**: přiřazení role se vztahuje na všechny prostředky Service Bus v rámci skupiny prostředků.
- **Předplatné**: přiřazení role se vztahuje na všechny prostředky Service Bus ve všech skupinách prostředků v rámci předplatného.

> [!NOTE]
> Mějte na paměti, že rozšíření přiřazení rolí Azure může trvat až pět minut. 

Další informace o tom, jak jsou předdefinované role definované, najdete v tématu [vysvětlení definic rolí](../role-based-access-control/role-definitions.md#management-and-data-operations). Informace o vytváření vlastních rolí Azure najdete v tématu [vlastní role Azure](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Povolení spravovaných identit na virtuálním počítači
Než budete moct použít spravované identity pro prostředky Azure k autorizaci Service Bus prostředků z virtuálního počítače, musíte nejdřív na VIRTUÁLNÍm počítači povolit spravované identity pro prostředky Azure. Informace o tom, jak povolit spravované identity pro prostředky Azure, najdete v jednom z těchto článků:

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Udělení oprávnění spravované identitě v Azure AD
Pokud chcete autorizovat požadavek na službu Service Bus ze spravované identity ve vaší aplikaci, nejdřív nakonfigurujte nastavení řízení přístupu na základě role v Azure (Azure RBAC) pro tuto spravovanou identitu. Azure Service Bus definuje role Azure, které zahrnují oprávnění k posílání a čtení z Service Bus. Když se role Azure přiřadí spravované identitě, udělí se spravované identitě přístup k entitám Service Bus v příslušném oboru.

Další informace o přiřazování rolí Azure najdete v tématu [ověřování a autorizace pomocí Azure Active Directory pro přístup k prostředkům Service Bus](authenticate-application.md#azure-built-in-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Použití Service Bus se spravovanými identitami pro prostředky Azure
Chcete-li použít Service Bus se spravovanými identitami, je nutné přiřadit identitu roli a příslušnému oboru. Postup v této části používá jednoduchou aplikaci, která běží pod spravovanou identitou a přistupuje k prostředkům Service Bus.

Tady používáme ukázkovou webovou aplikaci hostovanou v [Azure App Service](https://azure.microsoft.com/services/app-service/). Podrobné pokyny pro vytvoření webové aplikace najdete v tématu [Vytvoření webové aplikace v ASP.NET Core v Azure](../app-service/quickstart-dotnetcore.md) .

Po vytvoření aplikace proveďte tyto kroky: 

1. Přejít na **Nastavení** a vybrat **identitu**. 
1. Vyberte **stav** , který má být **zapnut**. 
1. Vyberte **Uložit** a nastavení se uloží. 

    ![Spravovaná identita pro webovou aplikaci](./media/service-bus-managed-service-identity/identity-web-app.png)

Po povolení tohoto nastavení se v Azure Active Directory (Azure AD) vytvoří nová identita služby a nakonfiguruje se na hostitele App Service.

Teď tuto identitu služby přiřaďte roli v požadovaném oboru ve vašich Service Busch prostředcích.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Přiřazení rolí Azure pomocí Azure Portal
Chcete-li přiřadit roli k oboru názvů Service Bus, přejděte na obor názvů v Azure Portal. Zobrazit nastavení Access Control (IAM) prostředku a podle těchto pokynů můžete spravovat přiřazení rolí:

> [!NOTE]
> Následující kroky přiřadí roli identity služby vašim oborům názvů Service Bus. Stejný postup můžete použít k přiřazení role v jiných podporovaných oborech (skupina prostředků a předplatné). 
> 
> Pokud nemáte [obor názvů pro zasílání zpráv Service Bus, vytvořte](service-bus-create-namespace-portal.md) ho. 

1. V Azure Portal přejděte na svůj obor názvů Service Bus a zobrazte **Přehled** oboru názvů. 
1. V nabídce vlevo vyberte **Access Control (IAM)** a zobrazte nastavení řízení přístupu pro obor názvů Service Bus.
1.  Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí.
3.  Pokud chcete přidat novou roli, vyberte **Přidat** .
4.  Na stránce **Přidat přiřazení role** vyberte role Azure Service Bus, které chcete přiřadit. Pak vyhledejte identitu služby, kterou jste zaregistrovali pro přiřazení role.
    
    ![Přidat stránku přiřazení role](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Vyberte **Uložit**. Identita, ke které jste přiřadili roli, se zobrazí v seznamu v rámci této role. Například následující obrázek ukazuje, že identita služby má Azure Service Bus vlastníka dat.
    
    ![Identita přiřazená k roli](./media/service-bus-managed-service-identity/role-assigned.png)

Po přiřazení role bude webová aplikace mít přístup k entitám Service Bus v rámci definovaného oboru. 

### <a name="run-the-app"></a>Spuštění aplikace

Nyní upravte výchozí stránku aplikace ASP.NET, kterou jste vytvořili. Můžete použít kód webové aplikace z [tohoto úložiště GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Stránka default. aspx je cílovou stránkou. Kód najdete v souboru Default.aspx.cs. Výsledkem je minimální webová aplikace s několika vstupními poli a tlačítky **Odeslat** a **přijmout** , která se připojují k Service Bus k odeslání nebo přijetí zpráv.

Všimněte si, jak se inicializuje objekt [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) . Namísto použití poskytovatele tokenu sdíleného přístupového tokenu (SAS) vytvoří kód pro spravovanou identitu poskytovatele tokenu s `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` voláním. V takovém případě nejsou k dispozici žádné tajné kódy k uchování a používání. Tok spravované identity, který se má Service Bus, a ověřovací metodou handshake automaticky zpracovává Poskytovatel tokenů. Je to jednodušší model než použití SAS.

Po provedení těchto změn publikování a spuštění aplikace. Správná data publikování můžete snadno získat tak, že si stáhnete a pak importujete profil publikování v aplikaci Visual Studio:

![Získat profil publikování](./media/service-bus-managed-service-identity/msi3.png)
 
Chcete-li odesílat nebo přijímat zprávy, zadejte název oboru názvů a název vytvořené entity. Pak klikněte na **Odeslat** nebo **přijmout**.


> [!NOTE]
> - Spravovaná identita funguje jenom v prostředí Azure, ve službě App Services, na virtuálních počítačích Azure a v sadách škálování. V případě aplikací .NET poskytuje knihovna Microsoft. Azure. Services. AppAuthentication, která je používána balíčkem Service Bus NuGet, abstrakci prostřednictvím tohoto protokolu a podporuje místní vývojové prostředí. Tato knihovna také umožňuje testovat kód místně na vašem vývojovém počítači pomocí uživatelského účtu ze sady Visual Studio, Azure CLI 2,0 nebo integrovaného ověřování služby Active Directory. Další informace o možnostech místního vývoje s touto knihovnou najdete v tématu [ověřování služba-služba pro Azure Key Vault pomocí rozhraní .NET](../key-vault/general/service-to-service-authentication.md).  
> 
> - Spravované identity v současné době nefungují s App Service sloty nasazení.

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)