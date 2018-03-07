---
title: "Postup konfigurace povoleno MSI virtuálního počítače Azure pomocí sady Azure SDK"
description: "Krok podle podrobné pokyny pro konfiguraci a použití ve virtuálním počítači Azure, pomocí sady Azure SDK a spravovaná služba Identity (MSI)."
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
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: dee89e4cd6501bb56015a2e4cfc045cc6308400e
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>Konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí sady Azure SDK

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory (AD). Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku zjistěte, jak povolit a odebrat MSI pro virtuální počítač Azure, pomocí sady SDK pro Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDK s podporou MSI 

Azure podporuje více platforem programovací prostřednictvím řady [sady Azure SDK](https://azure.microsoft.com/downloads). Několik z nich bylo aktualizováno pro podporu MSI a zadejte odpovídající ukázky k předvedení využití. Tento seznam je aktualizovat, protože je přidat další podporu:

| Sada SDK | Ukázka |
| --- | ------ | 
| .NET   | [Správa prostředků z virtuálního počítače povoleným MSI](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Spravovat úložiště z virtuálního počítače povoleným MSI](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Vytvoření virtuálního počítače s MSI povoleno](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Vytvoření virtuálního počítače s MSI povoleno](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Vytvoření virtuálního počítače Azure pomocí souboru MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Další postup

- Najdete v části související články v části "Konfigurace MSI pro virtuálního počítače Azure", se dozvíte, jak můžete také použít šablony Azure portal, prostředí PowerShell, rozhraní příkazového řádku a prostředků.

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.
