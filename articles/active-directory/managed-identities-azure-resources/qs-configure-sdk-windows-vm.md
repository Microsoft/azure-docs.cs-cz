---
title: Konfigurace spravovaných identit na virtuálním počítači – Azure AD pomocí sady SDK
description: Krok za krokem pokyny pro konfiguraci a používání spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí Sady Azure SDK.
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
ms.date: 09/28/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11cd51984f82bc20c02669e796d9ba21b9ed5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74183474"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Konfigurace virtuálního počítače se spravovanými identitami pro prostředky Azure pomocí sady Azure SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory (AD). Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, bez pověření ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a odebrat spravované identity pro prostředky Azure pro virtuální počítač Azure pomocí sady Azure SDK.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Sady Azure SDK se spravovanými identitami pro podporu prostředků Azure 

Azure podporuje několik programovacích platforem prostřednictvím řady [sad Azure SDK](https://azure.microsoft.com/downloads). Několik z nich byly aktualizovány na podporu spravovaných identit pro prostředky Azure a poskytují odpovídající ukázky k předvedení využití. Tento seznam je aktualizován jako další podpora je přidána:

| Sada SDK | Ukázka |
| --- | ------ | 
| .NET   | [Správa prostředků z virtuálního počítače povoleného se spravovanými identitami pro prostředky Azure povolené](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Správa úložiště z virtuálního počítače povoleného se spravovanými identitami pro prostředky Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Vytvoření virtuálního virtuálního zařízení s povolenou spravovanou identitou přiřazenou systémem](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Vytvoření virtuálního virtuálního zařízení s povolenou spravovanou identitou přiřazenou systémem](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Vytvoření virtuálního počítače Azure s povolenou identitou přiřazenou systémem](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Další kroky

- V článku **Konfigurace identity pro virtuální počítač Azure**najdete informace o tom, jak můžete taky použít azure portal, PowerShell, CLI a šablony prostředků.
