---
title: Ověřování spravované identity pomocí Azure Active Directory
description: Tento článek poskytuje informace o ověřování spravované identity pomocí Azure Active Directory pro přístup ke službě Azure Signal.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 46d66451bb8f2cd6c5d4448131b5f4842a728fd0
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797496"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům služby Azure Signal
Služba signalizace Azure podporuje ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md). Spravované identity pro prostředky Azure můžou autorizovat přístup k prostředkům služby signalizace Azure pomocí přihlašovacích údajů Azure AD z aplikací běžících ve službě Azure Virtual Machines (virtuální počítače), aplikací Function App, Virtual Machine Scale Sets a dalších služeb. Pomocí spravovaných identit pro prostředky Azure spolu s ověřováním Azure AD se můžete vyhnout ukládání přihlašovacích údajů k vašim aplikacím, které běží v cloudu.

V tomto článku se dozvíte, jak autorizovat přístup ke službě Azure Signal pomocí spravované identity z virtuálního počítače Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Povolení spravovaných identit na virtuálním počítači
Než budete moct použít spravované identity pro prostředky Azure k autorizaci prostředků služby signalizace Azure z virtuálního počítače, musíte nejdřív povolit spravované identity pro prostředky Azure na VIRTUÁLNÍm počítači. Informace o tom, jak povolit spravované identity pro prostředky Azure, najdete v jednom z těchto článků:

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Udělení oprávnění spravované identitě v Azure AD
Pokud chcete autorizovat požadavek na službu Azure Signal Service ze spravované identity ve vaší aplikaci, nejdřív nakonfigurujte nastavení řízení přístupu na základě role (RBAC) pro tuto spravovanou identitu. Služba signalizace Azure definuje role RBAC, které zahrnují oprávnění k získání `AccessKey` nebo `ClientToken` . Když se role RBAC přiřadí spravované identitě, udělí se spravované identitě přístup ke službě Azure Signal Service v příslušném oboru.

Další informace o přiřazování rolí RBAC najdete v tématu [ověřování pomocí Azure Active Directory pro přístup k prostředkům služby Azure Signal Service](authorize-access-azure-active-directory.md).

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>Připojení ke službě signalizace Azure pomocí spravovaných identit
Pokud se chcete připojit ke službě Azure Signal Service se spravovanými identitami, je potřeba přiřadit identitu roli a příslušnému oboru. Postup v této části používá jednoduchou aplikaci, která běží ve spravované identitě a přistupuje k prostředkům služby Azure Signal Service.

Tady používáme ukázkový prostředek virtuálního počítače Azure.

1. Přejít na **Nastavení** a vybrat **identitu**. 
1. Vyberte **stav** , který má být **zapnut**. 
1. Vyberte **Uložit** a nastavení se uloží. 

    ![Spravovaná identita pro virtuální počítač](./media/authenticate/identity-virtual-machine.png)

Po povolení tohoto nastavení se v Azure Active Directory (Azure AD) vytvoří nová identita služby a nakonfiguruje se na hostitele App Service.

Teď tuto identitu služby přiřaďte roli v požadovaném oboru prostředků služby Azure Signal Service.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Přiřazení rolí RBAC pomocí Azure Portal  
Další informace o správě přístupu k prostředkům Azure pomocí RBAC a Azure Portal najdete v [tomto článku](..//role-based-access-control/role-assignments-portal.md). 

Po určení vhodného oboru pro přiřazení role přejděte k tomuto prostředku v Azure Portal. Zobrazit nastavení řízení přístupu (IAM) pro prostředek a podle těchto pokynů můžete spravovat přiřazení rolí:

1. V [Azure Portal](https://portal.azure.com/)přejděte na prostředek signalizace.
1. Vyberte **Access Control (IAM)** a zobrazte nastavení řízení přístupu pro službu Azure Signal. 
1. Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí. Na panelu nástrojů vyberte tlačítko **Přidat** a pak vyberte **Přidat přiřazení role**. 

    ![Přidat tlačítko na panelu nástrojů](./media/authenticate/role-assignments-add-button.png)

1. Na stránce **Přidat přiřazení role** proveďte následující kroky:
    1. Vyberte **roli nástroje Azure Signal** , kterou chcete přiřadit. 
    1. Vyhledejte **objekt zabezpečení** (uživatel, skupina, instanční objekt), ke kterému chcete přiřadit roli.
    1. Vyberte **Uložit** a uložte přiřazení role. 

        ![Přiřazení role k aplikaci](./media/authenticate/assign-role-to-application.png)

    1. Identita, ke které jste přiřadili roli, se zobrazí v seznamu v rámci této role. Například na následujícím obrázku vidíte, že aplikace `signalr-dev` a `signalr-service` jsou v roli serveru aplikace signaler. 
        
        ![Seznam přiřazení rolí](./media/authenticate/role-assignment-list.png)

Podle podobných kroků můžete přiřadit obor role ke skupině prostředků nebo předplatnému. Po definování role a jejího oboru můžete toto chování otestovat pomocí ukázek [v tomto umístění GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).

## <a name="samples-code-while-configuring-your-app-server"></a>Ukázky kódu při konfiguraci serveru aplikace

Přidejte následující možnosti, když `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>Další kroky
- Další informace o RBAC najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md)?
- Další informace o tom, jak přiřadit a spravovat přiřazení rolí Azure pomocí Azure PowerShell, Azure CLI nebo REST API, najdete v těchto článcích:
    - [Správa řízení přístupu na základě role (RBAC) pomocí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Správa řízení přístupu na základě role (RBAC) pomocí Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Správa řízení přístupu na základě role (RBAC) pomocí REST API](../role-based-access-control/role-assignments-rest.md)
    - [Správa řízení přístupu na základě role (RBAC) pomocí šablon Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Podívejte se na následující související články:
- [Ověření aplikace s Azure Active Directory pro přístup ke službě Azure Signal](authenticate-application.md)
- [Autorizace přístupu ke službě Azure Signaler pomocí Azure Active Directory](authorize-access-azure-active-directory.md)