---
title: "Jak používat Azure virtuálního počítače spravované služby Identity se sadami SDK služby Azure"
description: "Ukázky kódu pro MSI virtuálních počítačů Azure pomocí sady Azure SDK."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: bd2f03f47cebec52aecb84ef2e97a745ede670ff
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Jak používat Azure virtuálního počítače spravované služby Identity (MSI) s SDK služby Azure 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
Tento článek obsahuje seznam ukázky sady SDK, které ukazují použití podpory jejich odpovídajících Azure SDK pro MSI.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Všechny ukázkový kód nebo skript v tomto článku předpokládá klienta běží na virtuálním počítači povolená MSI. Použijte funkci "Připojit" virtuální počítač na portálu Azure se vzdáleně připojit k virtuálnímu počítači. Informace o povolení MSI na virtuálním počítači, v [konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vm.md), nebo jeden z typu variant článků (pomocí prostředí PowerShell, rozhraní příkazového řádku, šablonu nebo Azure SDK). 

## <a name="sdk-code-samples"></a>Ukázky kódu SDK

| Sada SDK             | Ukázka kódu |
| --------------- | ----------- |
| .NET            | [Nasadit šablonu Azure Resource Manager ze systému Windows virtuálního počítače pomocí identita spravované služby](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Volání služby Azure z virtuálního počítače s Linuxem pomocí identita spravované služby](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Správa prostředků pomocí identita spravované služby](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [K ověření jednoduše z uvnitř virtuálního počítače použít MSI](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Spravovat prostředky z virtuálního počítače povoleným MSI](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>Související obsah

- V tématu [sady Azure SDK](https://azure.microsoft.com/downloads/) úplný seznam prostředků sady SDK Azure, včetně knihovny stahování, dokumentace a další.
- Pokud chcete povolit MSI ve virtuálním počítači Azure, najdete v části [konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vm.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.








