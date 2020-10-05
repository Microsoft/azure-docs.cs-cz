---
title: Použití sady SDK ke konfiguraci spravovaných identit na virtuálním počítači – Azure AD
description: Podrobné pokyny pro konfiguraci a používání spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí sady Azure SDK.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b4b55fa5a21ce56d27505a724bcf08f1a893c71
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89003452"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Konfigurace virtuálního počítače se spravovanými identitami pro prostředky Azure pomocí sady Azure SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory (AD). Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a odebrat spravované identity pro prostředky Azure pro virtuální počítač Azure pomocí sady Azure SDK.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure SDK se spravovanými identitami pro podporu prostředků Azure 

Azure podporuje několik programovacích platforem prostřednictvím řady [sad Azure SDK](https://azure.microsoft.com/downloads). Několik z nich bylo aktualizováno na podporu spravovaných identit pro prostředky Azure a poskytuje odpovídající ukázky k předvedení používání. Tento seznam je aktualizovaný, protože je přidána další podpora:

| Sada SDK | Ukázka |
| --- | ------ | 
| .NET   | [Správa prostředků z virtuálního počítače s povoleným spravovanými identitami pro prostředky Azure](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Správa úložiště z virtuálního počítače povoleného se spravovanými identitami pro prostředky Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Vytvoření virtuálního počítače s povolenou spravovanou identitou přiřazenou systémem](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Vytvoření virtuálního počítače s povolenou spravovanou identitou přiřazenou systémem](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Vytvoření virtuálního počítače Azure s povolenou identitou přiřazenou systémem](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak můžete používat Azure Portal, PowerShell, CLI a šablony prostředků, najdete v části související články v části **Konfigurace identity pro virtuální počítač Azure**.
