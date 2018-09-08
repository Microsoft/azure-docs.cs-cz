---
title: Použití spravované identity pro prostředky Azure na Virtuálním počítači Azure pomocí sady Azure SDK
description: Ukázky kódu pro virtuální počítač Azure, který se má spravovat identity pro prostředky Azure pomocí sady Azure SDK.
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
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 6827b01e72cd72d3f017df36205ccb985b4f242e
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157646"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Použití spravované identity pro prostředky Azure na Virtuálním počítači Azure pomocí sady Azure SDK 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Tento článek obsahuje seznam vzorků, sady SDK, které ukazují použití podporu jejich odpovídajících Azure SDK pro spravované identity pro prostředky Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Všechny ukázky kódu či skript v tomto článku se předpokládá, klient je spuštěný na virtuálním počítači pomocí spravované identity pro prostředky Azure, které jsou povolené. Pomocí funkce "Připojení" virtuálního počítače na webu Azure Portal, se vzdáleně připojit k virtuálnímu počítači. Podrobnosti o povolení spravovaných identit pro prostředky Azure na virtuálním počítači, naleznete v tématu [konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí webu Azure portal](qs-configure-portal-windows-vm.md), nebo jeden z variant článků (pomocí Powershellu, rozhraní příkazového řádku, šablonu nebo Azure SADA SDK). 

## <a name="sdk-code-samples"></a>Ukázky kódu SDK

| Sada SDK             | Ukázka kódu |
| --------------- | ----------- |
| .NET            | [Nasazení šablony Azure Resource Manageru z virtuálního počítače s Windows pomocí spravované identity pro prostředky Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Volání služby Azure z virtuálního počítače s Linuxem pomocí spravované identity pro prostředky Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Správa prostředků pro prostředky Azure pomocí spravované identity](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Ověření jednoduše z uvnitř virtuálního počítače pomocí spravované identity pro prostředky Azure](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Správa prostředků z virtuálního počítače pomocí spravované identity pro prostředky Azure, které jsou povolené](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Další postup

- Zobrazit [sady Azure SDK](https://azure.microsoft.com/downloads/) úplný seznam prostředků sady Azure SDK, včetně souborů ke stažení knihovny, dokumentaci a další.
- Povolit spravovaným identitám pro prostředky Azure na Virtuálním počítači Azure, najdete v článku [konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí webu Azure portal](qs-configure-portal-windows-vm.md).








