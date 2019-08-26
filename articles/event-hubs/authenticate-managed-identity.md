---
title: Ověřování spravované identity pomocí Azure Active Directory
description: Tento článek poskytuje informace o ověřování spravované identity pomocí Azure Active Directory pro přístup k prostředkům Azure Event Hubs.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: cbd7de7d526e1954aaad60f7d71e5cdf202f6a29
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992832"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Event Hubs
Azure Event Hubs podporuje ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md). Spravované identity pro prostředky Azure můžou autorizovat přístup k Event Hubs prostředkům pomocí přihlašovacích údajů Azure AD z aplikací běžících ve službě Azure Virtual Machines (VM), aplikací Function App, Virtual Machine Scale Sets a dalších služeb. Pomocí spravovaných identit pro prostředky Azure spolu s ověřováním Azure AD se můžete vyhnout ukládání přihlašovacích údajů k vašim aplikacím, které běží v cloudu.

Tento článek popisuje, jak autorizovat přístup k centru událostí pomocí spravované identity z virtuálního počítače Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Povolení spravovaných identit na virtuálním počítači
Než budete moct použít spravované identity pro prostředky Azure k autorizaci Event Hubs prostředků z virtuálního počítače, musíte nejdřív na VIRTUÁLNÍm počítači povolit spravované identity pro prostředky Azure. Informace o tom, jak povolit spravované identity pro prostředky Azure, najdete v jednom z těchto článků:

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Udělení oprávnění spravované identitě v Azure AD
Pro autorizaci žádosti o Event Hubs služby ze spravované identity ve vaší aplikaci nejdřív nakonfigurujte nastavení řízení přístupu na základě role (RBAC) pro tuto spravovanou identitu. Azure Event Hubs definuje role RBAC, které zahrnují oprávnění k posílání a čtení z Event Hubs. Když je role RBAC přiřazená ke spravované identitě, má spravovaná identita udělený přístup k Event Hubs datům v příslušném oboru.

Další informace o přiřazování rolí RBAC najdete v tématu [ověřování pomocí Azure Active Directory pro přístup k prostředkům Event Hubs](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Použití Event Hubs se spravovanými identitami
Chcete-li použít Event Hubs se spravovanými identitami, je nutné přiřadit identitu roli a příslušnému oboru. Postup v této části používá jednoduchou aplikaci, která běží pod spravovanou identitou a přistupuje k prostředkům Event Hubs.

Tady používáme ukázkovou webovou aplikaci hostovanou v [Azure App Service](https://azure.microsoft.com/services/app-service/). Podrobné pokyny pro vytvoření webové aplikace najdete v tématu [Vytvoření webové aplikace v ASP.NET Core v Azure](../app-service/app-service-web-get-started-dotnet.md) .

Po vytvoření aplikace proveďte tyto kroky: 

1. Přejít na **Nastavení** a vybrat **identitu**. 
1. Vyberte **stav** , který má být **zapnut**. 
1. Vyberte **Uložit** a nastavení se uloží. 

    ![Spravovaná identita pro webovou aplikaci](./media/authenticate-managed-identity/identity-web-app.png)

Po povolení tohoto nastavení se v Azure Active Directory (Azure AD) vytvoří nová identita služby a nakonfiguruje se na hostitele App Service.

Teď tuto identitu služby přiřaďte roli v požadovaném oboru ve vašich Event Hubsch prostředcích.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Přiřazení rolí RBAC pomocí Azure Portal
Chcete-li přiřadit roli k Event Hubs prostředkům, přejděte k tomuto prostředku v Azure Portal. Zobrazit nastavení Access Control (IAM) prostředku a podle těchto pokynů můžete spravovat přiřazení rolí:

> [!NOTE]
> Následující kroky přiřadí roli identity služby vašim oborům názvů Event Hubs. Stejný postup můžete provést, chcete-li přiřadit role s rozsahem Event Hubs prostředku. 

1. V Azure Portal přejděte na svůj obor názvů Event Hubs a zobrazte **Přehled** oboru názvů. 
1. V nabídce vlevo vyberte **Access Control (IAM)** a zobrazte nastavení řízení přístupu pro centrum událostí.
1.  Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí.
3.  Pokud chcete přidat novou roli, vyberte **Přidat** .
4.  Na stránce **Přidat přiřazení role** vyberte role Event Hubs, které chcete přiřadit. Pak vyhledejte identitu služby, kterou jste zaregistrovali pro přiřazení role.
    
    ![Přidat stránku přiřazení role](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Vyberte **Uložit**. Identita, ke které jste přiřadili roli, se zobrazí v seznamu v rámci této role. Například následující obrázek ukazuje, že identita služby má Event Hubs vlastníka dat.
    
    ![Identita přiřazená k roli](./media/authenticate-managed-identity/role-assigned.png)

Po přiřazení role bude mít webová aplikace přístup k prostředkům Event Hubs v rámci definovaného oboru. 

### <a name="test-the-web-application"></a>Testování webové aplikace
Nyní můžete spustit webovou aplikaci a nasměrovat prohlížeč na vzorovou stránku ASPX. Můžete najít ukázkovou webovou aplikaci, která odesílá a přijímá data z Event Hubsch prostředků v [úložišti GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Nainstalujte si nejnovější balíček z [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)a začněte odesílat a přijímat data z Center událostí pomocí EventHubClient, jak je znázorněno v následujícím kódu: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```

## <a name="next-steps"></a>Další postup
- Stáhněte si [ukázku](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp) z GitHubu.
- V následujícím článku se dozvíte o spravovaných identitách prostředků Azure: [Co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Podívejte se na následující související články:
    - [Ověřování požadavků do Azure Event Hubs z aplikace pomocí Azure Active Directory](authenticate-application.md)
    - [Ověřování požadavků na Azure Event Hubs pomocí sdílených přístupových podpisů](authenticate-shared-access-signature.md)
    - [Autorizace přístupu k prostředkům Event Hubs pomocí Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizace přístupu k prostředkům Event Hubs pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md)