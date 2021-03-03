---
title: Použití spravovaných identit ve komunikačních službách
titleSuffix: An Azure Communication Services quickstart
description: Spravované identity umožňují autorizovat přístup ke komunikačním službám Azure z aplikací běžících na virtuálních počítačích Azure, aplikacích Function App a dalších prostředcích.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 2/24/2021
ms.author: peiliu
ms.reviewer: mikben
ms.openlocfilehash: 0d25e5dc97c700daf6655ecd270bfda469a9d353
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657612"
---
# <a name="use-managed-identities"></a>Použití spravovaných identit
Začněte s komunikačními službami Azure pomocí spravovaných identit. Identita komunikačních služeb a klientské knihovny SMS podporují ověřování Azure Active Directory (Azure AD) se [spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

V tomto rychlém startu se dozvíte, jak autorizovat přístup k klientským knihovnám identit a SMS z prostředí Azure, které podporuje spravované identity. Také popisuje, jak testovat kód ve vývojovém prostředí.

## <a name="prerequisites"></a>Požadavky

 - Účet Azure s aktivním předplatným. [Vytvořit účet zdarma](https://azure.microsoft.com/free)
 - Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](./create-communication-resource.md?pivots=platform-azp&tabs=windows).

## <a name="setting-up"></a>Nastavení

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Povolení spravovaných identit na virtuálním počítači nebo ve službě App Service

U prostředků Azure, které autorizujete, by měly být povolené spravované identity. Informace o tom, jak povolit spravované identity pro prostředky Azure, najdete v jednom z těchto článků:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Šablona Azure Resource Manageru](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Klientské knihovny Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Aplikační služby](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Přiřazení rolí Azure k Azure Portal

1. Přejděte na Azure Portal.
1. Přejděte do prostředku služby Azure Communication Service.
1. Nabídka přejít na Access Control (IAM) – > + Add-> přidat přiřazení role.
1. Vyberte roli Přispěvatel (Jedná se o jedinou podporovanou roli).
1. Zvolte položku spravovaná identita přiřazená uživatelem (nebo "spravovaná identita přiřazená systémem") a pak vyberte požadovanou identitu. Uložte výběr.

![Spravovaná role identity](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Přiřazení rolí Azure k PowerShellu

Pokud chcete přiřadit role a oprávnění pomocí PowerShellu, přečtěte si téma [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end