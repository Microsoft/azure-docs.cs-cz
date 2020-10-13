---
title: Použití spravovaných identit na virtuálním počítači Azure se sadami SDK Azure – Azure AD
description: Ukázky kódu pro použití sad Azure SDK s virtuálním počítačem Azure, který má spravované identity pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecfb2fa5f45a23d387741d4865aa9707df960e86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018412"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Použití spravovaných identit pro prostředky Azure na virtuálním počítači Azure se sadami SDK Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Tento článek poskytuje seznam ukázek sady SDK, které ukazují použití příslušné podpory sady Azure SDK pro spravované identity prostředků Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Veškerý ukázkový kód/skript v tomto článku předpokládá, že klient běží na virtuálním počítači se spravovanými identitami pro prostředky Azure. Pomocí funkce připojit k VIRTUÁLNÍmu počítači ve Azure Portal se můžete vzdáleně připojit k vašemu VIRTUÁLNÍmu počítači. Podrobnosti o povolení spravovaných identit pro prostředky Azure na virtuálním počítači najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](qs-configure-portal-windows-vm.md)nebo některého z článků variant (pomocí PowerShellu, rozhraní příkazového řádku, šablony nebo sady Azure SDK). 

## <a name="sdk-code-samples"></a>Ukázky kódu SDK

| Sada SDK             | Ukázka kódu |
| --------------- | ----------- |
| .NET            | [Nasazení šablony Azure Resource Manager z virtuálního počítače s Windows pomocí spravovaných identit pro prostředky Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Volání služeb Azure z virtuálního počítače se systémem Linux pomocí spravovaných identit pro prostředky Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Správa prostředků pomocí spravovaných identit pro prostředky Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Použití spravovaných identit pro prostředky Azure k ověřování jednoduše zevnitř virtuálního počítače](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Správa prostředků z virtuálního počítače se spravovanými identitami pro prostředky Azure povolené](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Další kroky

- Úplný seznam prostředků sady Azure SDK, včetně stažení knihovny, dokumentace a dalších, najdete v tématu [Azure SDK](https://azure.microsoft.com/downloads/) .
- Pokud chcete povolit spravované identity pro prostředky Azure na virtuálním počítači Azure, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](qs-configure-portal-windows-vm.md).








