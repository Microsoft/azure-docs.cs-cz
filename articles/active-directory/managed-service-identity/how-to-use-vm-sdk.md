---
title: Použití Azure VM identita spravované služby pomocí sady Azure SDK
description: Ukázky kódu pro soubor MSI virtuálních počítačů Azure pomocí sady Azure SDK.
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
ms.openlocfilehash: 1c7a0d10f8ff005d90bb77f33bf40a00f97e078f
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901748"
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Použití Azure VM Identity spravované služby (MSI) pomocí sady Azure SDK 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Tento článek obsahuje seznam vzorků, sady SDK, které ukazují použití podporu jejich odpovídajících Azure SDK pro Instalační služby MSI.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Všechny ukázky kódu či skript v tomto článku předpokládá, klient je spuštěný na virtuálním počítači povolená MSI. Pomocí funkce "Připojení" virtuálního počítače na webu Azure Portal, se vzdáleně připojit k virtuálnímu počítači. Podrobnosti o povolení MSI virtuálního počítače najdete v tématu [nakonfigurovat virtuálním počítači Identity spravované služby (MSI) pomocí webu Azure portal](qs-configure-portal-windows-vm.md), nebo jeden z variant článků (pomocí Powershellu, rozhraní příkazového řádku, šablonu nebo pomocí sady Azure SDK). 

## <a name="sdk-code-samples"></a>Ukázky kódu SDK

| Sada SDK             | Ukázka kódu |
| --------------- | ----------- |
| .NET            | [Nasazení šablony Azure Resource Manageru z virtuálního počítače s Windows pomocí Identity spravované služby](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Volání služby Azure z virtuálního počítače s Linuxem pomocí Identity spravované služby](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Správa prostředků pomocí Identity spravované služby](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Použití MSI pro ověření jednoduše z uvnitř virtuálního počítače](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Správa prostředků z virtuálního počítače s podporou MSI](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>Související obsah

- Zobrazit [sady Azure SDK](https://azure.microsoft.com/downloads/) úplný seznam prostředků sady Azure SDK, včetně souborů ke stažení knihovny, dokumentaci a další.
- Povolení MSI ve Virtuálním počítači Azure, najdete v článku [nakonfigurovat virtuálním počítači Identity spravované služby (MSI) pomocí webu Azure portal](qs-configure-portal-windows-vm.md).

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.








