---
title: Jak nakonfigurovat povolené MSI virtuálního počítače Azure pomocí sady Azure SDK
description: Krok pokyny pro konfiguraci a použití Identity spravované služby (MSI) na virtuálním počítači Azure pomocí sady Azure SDK.
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
ms.openlocfilehash: dee4a3e27623150ce3fa648d73542db0cbb23e93
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901438"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>Konfigurace VM-Managed identita služby (MSI) pomocí sady Azure SDK

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory (AD). Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a odebrat MSI pro virtuální počítač Azure, pomocí sady Azure SDK.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>Sady Azure SDK s podporou MSI 

Azure podporuje různé programovací platformy prostřednictvím řady [sady Azure SDK](https://azure.microsoft.com/downloads). Některé z nich byla aktualizována, aby podporovala MSI a aby poskytovala odpovídající ukázky, které ukazují využití. Tento seznam se aktualizuje, jak přidat další podporu:

| Sada SDK | Ukázka |
| --- | ------ | 
| .NET   | [Správa prostředků z virtuálního počítače s podporou MSI](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Správa služby storage z virtuálního počítače s podporou MSI](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Vytvoření virtuálního počítače s povolenou službou MSI](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Vytvoření virtuálního počítače s povolenou službou MSI](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Vytvoření virtuálního počítače Azure pomocí MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Další postup

- Zobrazit související články v části "Konfigurace MSI pro Virtuálním počítači Azure", se dozvíte, jak můžete také použít šablony Azure portal, Powershellu, rozhraní příkazového řádku a prostředků.

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.
