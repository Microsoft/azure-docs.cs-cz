---
title: Použití spravovaných identit na virtuálním počítači Azure s azure sdk – Azure AD
description: Ukázky kódu pro použití sad Azure SDK s virtuálním počítačem Azure, který spravuje identity pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 422b4c63a86904721fc6910193ffa63a8fe46f0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184156"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Jak používat spravované identity pro prostředky Azure na virtuálním počítači Azure s Azure SDKs 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Tento článek obsahuje seznam ukázek sady SDK, které demonstrují použití jejich příslušné podpory sady Azure SDK pro spravované identity pro prostředky Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Veškerý ukázkový kód nebo skript v tomto článku předpokládá, že klient běží na virtuálním počítači se spravovanými identitami pro prostředky Azure. Pomocí funkce Připojení virtuálního počítače na webu Azure Portal můžete vzdáleně připojit k virtuálnímu počítači. Podrobnosti o povolení spravovaných identit pro prostředky Azure na virtuálním počítači najdete [v tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí portálu Azure Portal](qs-configure-portal-windows-vm.md)nebo jednoho z článků variant (pomocí PowerShellu, CLI, šablony nebo Sady Azure SDK). 

## <a name="sdk-code-samples"></a>Ukázky kódu sady SDK

| Sada SDK             | Ukázka kódu |
| --------------- | ----------- |
| .NET            | [Nasazení šablony Azure Resource Manageru z virtuálního počítače s Windows pomocí spravovaných identit pro prostředky Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Volání služeb Azure z virtuálního počítače s Linuxem pomocí spravovaných identit pro prostředky Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Správa prostředků pomocí spravovaných identit pro prostředky Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Použití spravovaných identit pro prostředky Azure k ověření jednoduše zevnitř virtuálního počítače](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Správa prostředků z virtuálního počítače s povolenými spravovanými identitami pro prostředky Azure](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Další kroky

- Úplný seznam prostředků sady Azure SDK, včetně stahování knihoven, dokumentace a dalších témat, najdete v článku [sady Azure SDK.](https://azure.microsoft.com/downloads/)
- Pokud chcete povolit spravované identity pro prostředky Azure na virtuálním počítači Azure, přečtěte si témat [u tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí portálu Azure](qs-configure-portal-windows-vm.md).








