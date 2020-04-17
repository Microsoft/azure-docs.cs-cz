---
title: Spravované identity pro prostředky Azure pomocí Service Bus
description: Tento článek popisuje, jak používat spravované identity pro přístup pomocí entit Azure Service Bus (fronty, témata a předplatná).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 46a1db94d576174b837a40c646fcf9e082e339c8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461612"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Ověření spravované identity pomocí Služby Azure Active Directory pro přístup k prostředkům Služby Azure Service Bus
[Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) je funkce pro více azure, která umožňuje vytvořit zabezpečenou identitu přidruženou k nasazení, pod kterým běží kód aplikace. Tuto identitu pak můžete přidružit k rolím řízení přístupu, které udělují vlastní oprávnění pro přístup ke konkrétním prostředkům Azure, které vaše aplikace potřebuje.

Se spravovanými identitami spravuje platforma Azure tuto identitu za běhu. Není nutné ukládat a chránit přístupové klíče v kódu aplikace nebo konfiguraci, a to buď pro samotnou identitu, nebo pro prostředky, které potřebujete k přístupu. Klientská aplikace Service Bus spuštěná v aplikaci Služby Azure App Service nebo ve virtuálním počítači s povolenými spravovanými entitami pro podporu prostředků Azure nemusí zpracovávat pravidla a klíče SAS ani žádné jiné přístupové tokeny. Klientská aplikace potřebuje jenom adresu koncového bodu oboru názvů Zasílání zpráv služby Service Bus. Když se aplikace připojí, Service Bus váže kontext spravované entity na klienta v operaci, která je uvedena v příkladu dále v tomto článku. Jakmile je přidružen a spravované identity, váš klient service bus můžete provést všechny autorizované operace. Autorizace je udělena připojováním spravované entity k rolím service bus. 

## <a name="overview"></a>Přehled
Když se objekt zabezpečení (uživatel, skupina nebo aplikace) pokusí o přístup k entitě service bus, musí být požadavek autorizován. S Azure AD, přístup k prostředku je dvoustupňový proces. 

 1. Nejprve je ověřena identita objektu zabezpečení a je vrácen token OAuth 2.0. Název prostředku pro vyžádání `https://servicebus.azure.net`tokenu je .
 1. Dále je token předán jako součást požadavku na službu Service Bus k autorizaci přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby požadavek aplikace obsahoval přístupový token OAuth 2.0 za běhu. Pokud aplikace běží v rámci entity Azure, jako je například virtuální počítač Azure, škálovací sada virtuálního počítače nebo aplikace Azure Function, můžete použít spravovanou identitu pro přístup k prostředkům. 

Krok autorizace vyžaduje, aby byla k objektu zabezpečení přiřazena jedna nebo více rolí RBAC. Azure Service Bus poskytuje role RBAC, které zahrnují sady oprávnění pro prostředky service bus. Role, které jsou přiřazeny k objektu zabezpečení určit oprávnění, která bude mít objekt zabezpečení. Další informace o přiřazování rolí RBAC službě Azure Service Bus najdete [v tématu integrované role RBAC pro Azure Service Bus](#built-in-rbac-roles-for-azure-service-bus). 

Nativní aplikace a webové aplikace, které pořizují požadavky na Service Bus můžete také autorizovat s Azure AD. Tento článek ukazuje, jak požádat o přístupový token a použít jej k autorizaci požadavků na prostředky service bus. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Přiřazení rolí RBAC pro přístupová práva
Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě rolí (RBAC).](../role-based-access-control/overview.md) Azure Service Bus definuje sadu předdefinovaných rolí RBAC, které zahrnují společné sady oprávnění používaných pro přístup k entitám Service Bus a můžete také definovat vlastní role pro přístup k datům.

Když je role RBAC přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup může být vymezen na úroveň předplatného, skupiny prostředků nebo oboru názvů Service Bus. Objekt zabezpečení Azure AD může být uživatel, skupina, instanční objekt aplikační služby nebo spravovaná identita pro prostředky Azure.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Integrované role RBAC pro Azure Service Bus
Pro Azure Service Bus je správa oborů názvů a všech souvisejících prostředků prostřednictvím portálu Azure portal a rozhraní API pro správu prostředků Azure již chráněna pomocí modelu řízení přístupu (RBAC) *na základě rolí.* Azure poskytuje níže integrované role RBAC pro autorizaci přístupu k oboru názvů Service Bus:

- [Vlastník dat služby Azure Service Bus:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)Povolí přístup k oboru názvů Service Bus a jeho entit (fronty, témata, předplatná a filtry).
- [Odesílatel dat služby Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Tato role slouží k poskytnutí přístupu k přístupu k oboru názvů Service Bus a jeho entitám.
- [Příjemce dat služby Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Tato role slouží k poskytnutí přístupu k oboru názvů Service Bus a jeho entitám. 

## <a name="resource-scope"></a>Obor prostředků 
Před přiřazením role RBAC k objektu zabezpečení určete rozsah přístupu, který by měl mít zaregistrovaný objekt zabezpečení. Osvědčené postupy určují, že je vždy nejlepší udělit pouze nejužší možný rozsah.

Následující seznam popisuje úrovně, na kterých můžete obor přístup k prostředkům service bus, počínaje nejužší maže:

- **Fronta**, **téma**nebo **předplatné**: Přiřazení role se vztahuje na konkrétní entitu služby Service Bus. V současné době portál Azure nepodporuje přiřazování uživatelů/skupin/spravovaných identit k rolím RBAC služby Service Bus na úrovni předplatného. Tady je příklad použití příkazu Azure CLI: [az-role-assignment-create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) pro přiřazení identity k roli RBAC služby: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Obor názvů service bus**: Přiřazení role zahrnuje celou topologii služby Service Bus pod oborem názvů a do skupiny spotřebitelů, která je k ní přidružena.
- **Skupina zdrojů**: Přiřazení role se vztahuje na všechny prostředky služby Service Bus v rámci skupiny prostředků.
- **Předplatné**: Přiřazení role se vztahuje na všechny prostředky service bus ve všech skupinách prostředků v předplatném.

> [!NOTE]
> Mějte na paměti, že přiřazení rolí RBAC může trvat až pět minut k šíření. 

Další informace o tom, jak jsou definovány předdefinované role, naleznete [v tématu Principy definic rolí](../role-based-access-control/role-definitions.md#management-and-data-operations). Informace o vytváření vlastních rolí RBAC najdete v [tématu Vytvoření vlastních rolí pro řízení přístupu na základě rolí Azure](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Povolení spravovaných identit na virtuálním počítači
Než budete moct použít spravované identity pro prostředky Azure k autorizaci prostředků Service Bus z vašeho virtuálního počítače, musíte nejdřív povolit spravované identity pro prostředky Azure na virtuálním počítači. Informace o povolení spravovaných identit pro prostředky Azure najdete v jednom z těchto článků:

- [portál Azure](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manageru](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Udělení oprávnění spravované identitě ve službě Azure AD
Chcete-li autorizovat požadavek na službu Service Bus ze spravované identity ve vaší aplikaci, nejprve nakonfigurujte nastavení řízení přístupu na základě rolí (RBAC) pro tuto spravovanou identitu. Azure Service Bus definuje role RBAC, které zahrnují oprávnění pro odesílání a čtení ze služby Service Bus. Když je role RBAC přiřazena spravované identitě, spravované identitě je udělen přístup k entitám service bus v příslušném oboru.

Další informace o přiřazování rolí RBAC najdete [v tématu Ověření a autorizace pomocí služby Azure Active Directory pro přístup k prostředkům služby Service Bus](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Použití service busu se spravovanými identitami pro prostředky Azure
Chcete-li použít service bus se spravovanými identitami, musíte přiřadit identitu roli a příslušný obor. Postup v této části používá jednoduchou aplikaci, která běží pod spravovanou identitou a přistupuje k prostředkům služby Service Bus.

Tady používáme ukázkovou webovou aplikaci hostovohouji ve [službě Azure App Service](https://azure.microsoft.com/services/app-service/). Podrobné pokyny k vytvoření webové aplikace najdete v [tématu Vytvoření webové aplikace ASP.NET Core v Azure.](../app-service/app-service-web-get-started-dotnet.md)

Po vytvoření aplikace postupujte takto: 

1. Přejděte do **nastavení** a vyberte **Identita**. 
1. Vyberte **stav,** na který chcete být **zapnuti**. 
1. Vyberte **Uložit** a nastavení se uloží. 

    ![Spravovaná identita webové aplikace](./media/service-bus-managed-service-identity/identity-web-app.png)

Po povolení tohoto nastavení se ve vaší službě Azure Active Directory (Azure AD) vytvoří nová identita služby a nakonfiguruje se do hostitele služby App Service.

Nyní přiřaďte tuto identitu služby roli v požadovaném oboru ve vašich prostředcích služby Service Bus.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Přiřazení rolí RBAC pomocí portálu Azure
Pokud chcete přiřadit roli oboru názvů Service Bus, přejděte do oboru názvů na webu Azure Portal. Zobrazte nastavení řízení přístupu (IAM) pro prostředek a podle následujících pokynů spravujte přiřazení rolí:

> [!NOTE]
> Následující kroky přiřadí roli identity služby do oborů názvů service bus. Stejným postupem můžete přiřadit roli v jiných podporovaných oborech (skupina prostředků a předplatné). 
> 
> Pokud nemáte [obor názvů zasílání zpráv služby Service Bus,](service-bus-create-namespace-portal.md) vytvořte jej. 

1. Na webu Azure Portal přejděte do oboru názvů Service Bus a zobrazte **přehled** oboru názvů. 
1. V levé nabídce vyberte **Řízení přístupu (IAM),** chcete-li zobrazit nastavení řízení přístupu pro obor názvů služby Service Bus.
1.  Výběrem karty **Přiřazení rolí** zobrazíte seznam přiřazení rolí.
3.  Výběrem **možnosti Přidat** přidáte novou roli.
4.  Na stránce **Přidat přiřazení role** vyberte role Azure Service Bus, které chcete přiřadit. Potom vyhledejte identitu služby, kterou jste zaregistrovali, abyste roli přiřadili.
    
    ![Stránka přiřazení role Přidat](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Vyberte **Uložit**. Identita, které jste roli přiřadili, se zobrazí v seznamu v této roli. Například následující obrázek ukazuje, že identita služby má vlastníka dat služby Azure Service Bus.
    
    ![Identita přiřazená k roli](./media/service-bus-managed-service-identity/role-assigned.png)

Po přiřazení role bude mít webová aplikace přístup k entitám service bus v rámci definovaného oboru. 

### <a name="run-the-app"></a>Spuštění aplikace

Nyní upravte výchozí stránku ASP.NET aplikace, kterou jste vytvořili. Kód webové aplikace můžete použít z [tohoto úložiště GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Vstupní stránka Default.aspx je vstupní stránka. Kód naleznete v Default.aspx.cs souboru. Výsledkem je minimální webová aplikace s několika vstupními poli a s tlačítky **pro odesílání** a **přijímání,** které se připojují ke službě Service Bus k odesílání nebo přijímání zpráv.

Všimněte si, jak je inicializován objekt [MessagingFactory.](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) Místo použití zprostředkovatele tokenu tokenu sdíleného přístupového tokenu (SAS) `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` kód vytvoří zprostředkovatele tokenu pro spravovanou identitu s voláním. Jako takové neexistují žádné tajemství zachovat a používat. Tok kontextu spravované identity do service bus a autorizace handshake jsou automaticky zpracovány zprostředkovatelem tokenu. Je to jednodušší model než pomocí SAS.

Po provedení těchto změn publikujte a spusťte aplikaci. Správná data publikování můžete snadno získat stažením a importem profilu publikování v sadě Visual Studio:

![Získat profil publikování](./media/service-bus-managed-service-identity/msi3.png)
 
Chcete-li odesílat nebo přijímat zprávy, zadejte název oboru názvů a název entity, kterou jste vytvořili. Potom klepněte na tlačítko **Odeslat** nebo **přijmout**.


> [!NOTE]
> - Spravovaná identita funguje jenom v prostředí Azure, ve službách App Services, virtuálních počítačích Azure a škálovacích sadách. Pro aplikace .NET knihovna Microsoft.Azure.Services.AppAuthentication, která se používá balíček Service Bus NuGet, poskytuje abstrakci nad tímto protokolem a podporuje prostředí místního vývoje. Tato knihovna také umožňuje testovat kód místně ve vývojovém počítači pomocí uživatelského účtu z Visual Studia, Azure CLI 2.0 nebo Integrované ověřování služby Active Directory. Další informace o možnostech místního vývoje v této knihovně najdete v [tématu Ověřování služeb azure key vault pomocí .NET](../key-vault/general/service-to-service-authentication.md).  
> 
> - V současné době spravované identity nefungují s sloty pro nasazení služby App Service.

## <a name="next-steps"></a>Další kroky

Další informace o zasílání zpráv služby Service Bus najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
