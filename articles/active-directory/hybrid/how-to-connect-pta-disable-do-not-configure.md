---
title: Zakázání pta při použití služby Azure AD Connect "Nekonfigurovat" | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak zakázat PTA pomocí funkce Azure AD Connect "nekonfigurovat".
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726797"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>Zakázání pta při použití služby Azure AD Connect "Nekonfigurovat"

Pokud používáte předávací ověřování s Azure AD Connect a máte nastavena na "Nekonfigurovat", můžete zakázat. Zakázání PTA lze provést pomocí následujících rutin. 

## <a name="prerequisites"></a>Požadavky
Jsou vyžadovány následující předpoklady:
- Jakýkoli počítač se systémem Windows, který má nainstalován agenta PTA. 
- Agent musí být ve verzi 1.5.1742.0 nebo novější. 
- Účet globálního správce Azure za účelem spuštění rutin prostředí PowerShell zakázat PTA.

>[!NOTE]
> Pokud je váš agent starší, nemusí mít rutiny potřebné k dokončení této operace. Můžete získat nového agenta z Azure Portal a nainstalovat ho do libovolného počítače windows a poskytnout přihlašovací údaje správce. (Instalace agenta nemá vliv na stav PTA v cloudu)

> [!IMPORTANT]
> Pokud používáte cloud Azure Government, budete muset předat parametr ENVIRONMENTNAME s následující hodnotou. 
>
>| Název prostředí | Cloud |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>Zakázání pta
V rámci relace prostředí PowerShell zakažte pta pomocí následujícího:
1. PS C:\Program Files\Microsoft Azure AD Connect Authentication Agent>`Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` nebo `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` nebo `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Pokud nemáte přístup k agentovi

Pokud nemáte počítač s agentem, můžete k instalaci agenta použít následující příkaz.

1. Stáhněte si nejnovější Auth Agent z portal.azure.com.
2. Nainstalujte tuto `.\AADConnectAuthAgentSetup.exe` funkci: nebo`.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Další kroky

- [Přihlašování uživatelů s využitím předávacího ověřování služby Azure Active Directory](how-to-connect-pta.md)