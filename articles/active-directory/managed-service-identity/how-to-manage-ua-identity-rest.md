---
title: Jak spravovat Azure uživatele přiřazeny spravovaných identit pomocí rozhraní REST
description: Podrobné pokyny o tom, jak vytvořit, vypsat a odstranit uživatele přiřadit spravovanou identitu pro volání rozhraní REST API.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: daveba
ms.openlocfilehash: afeac0cdb24593f5b7614a145021eefd7b376be9
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904021"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-rest-api-calls"></a>Vytváření, seznamu a odstraňování identity přiřazené uživateli, volání rozhraní REST API

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravovaná identita dává Azure services kvůli ověření do služeb, že ověřování podpory služby Azure AD bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak vytvářet, seznam, a odstranit pomocí příkazu CURL k volání rozhraní REST API pro spravované identity přiřazené uživatele.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.
- Pokud používáte Windows, nainstalujte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo použijte [Azure Cloud Shell](../../cloud-shell/overview.md) na webu Azure Portal.
- Pokud používáte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo [operačním systémem distribuce Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [nainstalovat místní konzoly Azure CLI](/azure/install-azure-cli).
- Pokud používáte místní konzoly příkazového řádku Azure, přihlaste se do Azure s využitím `az login` pomocí účtu, který je přidružený k předplatnému Azure chcete nasadit nebo načíst uživatel přiřazenou informace spravovaná identita.
- Získat nosný přístup k tokenu pomocí `az account get-access-token` provádět následující uživatel přiřazené operace spravovaná identita.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživateli 

K vytvoření spravované identity přiřazené uživateli, použijte následující požadavek CURL k rozhraní API Azure Resource Manageru. Nahradit `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>`,`<LOCATION>`, a `<ACCESS TOKEN>` hodnoty vlastními hodnotami:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>Seznam uživatel přiřazenou spravovaných identit

Seznam spravovaných identit přiřazených uživateli, použijte následující požadavek CURL k rozhraní API Azure Resource Manageru.  Nahradit `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, a `<ACCESS TOKEN>` hodnoty vlastními hodnotami:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživateli

Pokud chcete odstranit spravované identity přiřazené uživateli, použijte následující požadavek CURL k rozhraní API Azure Resource Manageru. Nahradit `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, a `<ACCESS TOKEN>` parametry hodnoty vlastními hodnotami:

> [!NOTE]
> Odstraňování identity přiřazené uživateli nedojde k odebrání odkazu ze všech prostředků, který byl přiřazen. Odebrání uživatele přiřazeny spravované z virtuálního počítače pomocí příkazu CURL naleznete v tématu [identity přiřazené uživateli odebrání virtuálního počítače Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Další postup

Informace o tom, jak přiřadit do Azure VM/VMSS identity přiřazené uživateli pomocí příkazu CURL naleznete v tématu, [nakonfigurovat spravované Identity na Virtuálním počítači Azure pomocí příkazu CURL](qs-configure-rest-vm.md#user-assigned-identity) a [nakonfigurovat spravované Identity ve virtuálním počítači škálovací sadě pomocí příkazu CURL ](qs-configure-rest-vmss.md#user-assigned-identity).


