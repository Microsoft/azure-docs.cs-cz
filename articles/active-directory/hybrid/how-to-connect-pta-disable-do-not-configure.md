---
title: Zakázat PTA při použití Azure AD Connect nekonfigurovat | Microsoft Docs
description: Tento článek popisuje, jak zakázat PTA pomocí funkce Azure AD Connect nekonfigurovat.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5db99f5e8ed2ea5844acba5500bc94d8fb0db2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85358340"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>Zakázat PTA při použití Azure AD Connect nekonfigurovat

Pokud používáte předávací ověřování s Azure AD Connect a máte nastavenou možnost nekonfigurovat, můžete ho zakázat. Zakázání PTA můžete provést pomocí následujících rutin. 

## <a name="prerequisites"></a>Požadavky
Vyžadují se tyto požadavky:
- Libovolný počítač s Windows, na kterém je nainstalovaný agent PTA. 
- Agent musí být ve verzi 1.5.1742.0 nebo novější. 
- Účet globálního správce Azure, aby bylo možné spustit rutiny prostředí PowerShell pro zakázání PTA.

>[!NOTE]
> Pokud je váš agent starší, nemusí mít rutiny potřebné k dokončení této operace. Nového agenta můžete získat z webu Azure Portal a nainstalovat ho do libovolného počítače s Windows a zadat přihlašovací údaje správce. (Instalace agenta nemá vliv na stav PTA v cloudu.)

> [!IMPORTANT]
> Pokud používáte Azure Government Cloud, budete muset předat parametr ENVIRONMENT s následující hodnotou. 
>
>| Název prostředí | Cloud |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>Zakázání PTA
V rámci relace PowerShellu použijte následující příkaz, kterým zakážete PTA:
1. PS C:\Program Files\Microsoft Azure AD Connect Authentication agent> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` nebo `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` nebo `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Pokud nemáte přístup k agentovi

Pokud nemáte počítač agenta, můžete k instalaci agenta použít následující příkaz.

1. Stáhněte si nejnovějšího agenta ověření z portal.azure.com.
2. Nainstalujte funkci: `.\AADConnectAuthAgentSetup.exe` nebo `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Další kroky

- [Přihlašování uživatelů s využitím předávacího ověřování služby Azure Active Directory](how-to-connect-pta.md)
