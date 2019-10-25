---
title: Použití spravovaných identit pro prostředky Azure na virtuálním počítači Azure se sadami SDK Azure
description: Ukázky kódu pro použití sad Azure SDK s virtuálním počítačem Azure, který má spravované identity pro prostředky Azure.
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
ms.openlocfilehash: 5c66bb2b4e9af6f4b79b0ead98b8d18fc56cb467
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809181"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Použití spravovaných identit pro prostředky Azure na virtuálním počítači Azure se sadami SDK Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Tento článek poskytuje seznam ukázek sady SDK, které ukazují použití příslušné podpory sady Azure SDK pro spravované identity prostředků Azure.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Veškerý ukázkový kód/skript v tomto článku předpokládá, že klient běží na virtuálním počítači se spravovanými identitami pro prostředky Azure. Pomocí funkce připojit k VIRTUÁLNÍmu počítači ve Azure Portal se můžete vzdáleně připojit k vašemu VIRTUÁLNÍmu počítači. Podrobnosti o povolení spravovaných identit pro prostředky Azure na virtuálním počítači najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](qs-configure-portal-windows-vm.md)nebo některého z článků variant (pomocí PowerShellu, rozhraní příkazového řádku, šablony nebo sady Azure SDK). 

## <a name="sdk-code-samples"></a>Ukázky kódu SDK

| SDK             | Ukázka kódu |
| --------------- | ----------- |
| .NET            | [Nasazení šablony Azure Resource Manager z virtuálního počítače s Windows pomocí spravovaných identit pro prostředky Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Volání služeb Azure z virtuálního počítače se systémem Linux pomocí spravovaných identit pro prostředky Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Správa prostředků pomocí spravovaných identit pro prostředky Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Použití spravovaných identit pro prostředky Azure k ověřování jednoduše zevnitř virtuálního počítače](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Správa prostředků z virtuálního počítače se spravovanými identitami pro prostředky Azure povolené](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Další kroky

- Úplný seznam prostředků sady Azure SDK, včetně stažení knihovny, dokumentace a dalších, najdete v tématu [Azure SDK](https://azure.microsoft.com/downloads/) .
- Pokud chcete povolit spravované identity pro prostředky Azure na virtuálním počítači Azure, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](qs-configure-portal-windows-vm.md).








