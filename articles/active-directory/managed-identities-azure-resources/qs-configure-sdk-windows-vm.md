---
title: Konfigurace virtuálního počítače pomocí spravované identity pro prostředky Azure pomocí sady Azure SDK
description: Podrobné pokyny pro konfiguraci a použití spravované identity pro prostředky Azure na virtuálním počítači Azure pomocí sady Azure SDK.
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
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 3b485fd0f655588ec5ae7941bcb6a43ca7728134
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47106824"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Konfigurace virtuálního počítače pomocí spravované identity pro prostředky Azure pomocí sady Azure SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory (AD). Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a odebrat spravovaných identit pro prostředky Azure pro virtuální počítač Azure, pomocí sady Azure SDK.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Podpora sady Azure SDK pomocí spravované identity pro prostředky Azure 

Azure podporuje různé programovací platformy prostřednictvím řady [sady Azure SDK](https://azure.microsoft.com/downloads). Některé z nich byly aktualizovány pro podporu spravovaných identit pro prostředky Azure a zadejte odpovídající ukázky, které ukazují využití. Tento seznam se aktualizuje, jak přidat další podporu:

| Sada SDK | Ukázka |
| --- | ------ | 
| .NET   | [Správa prostředků z virtuálního počítače povolená pomocí spravované identity pro prostředky Azure, které jsou povolené](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Správa služby storage z virtuálního počítače povolená pomocí spravované identity pro prostředky Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Vytvoření Virtuálního počítače s systém přiřadil povolené spravovaná identita.](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Vytvoření Virtuálního počítače s systém přiřadil povolené spravovaná identita.](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Vytvoření virtuálního počítače Azure s identitou systém přiřadil povoleno](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Další postup

- Zobrazit související články v části **konfigurace Identity pro virtuální počítač Azure**, se dozvíte, jak můžete také použít šablony Azure portal, Powershellu, rozhraní příkazového řádku a prostředků.
