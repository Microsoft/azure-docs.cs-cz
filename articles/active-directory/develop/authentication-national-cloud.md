---
title: Ověřování Azure AD & národní cloudy | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o registraci aplikací a koncových bodech ověřování pro národní cloudy.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev
ms.openlocfilehash: 20a053369149dc29d6485c49bb091a75bb9fb591
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377197"
---
# <a name="national-clouds"></a>Národní cloudy

Národní cloudy jsou fyzicky izolované instancemi Azure. Tyto oblasti Azure jsou navržené tak, aby se zajistilo, že se nároky na data, svrchovanost a dodržování předpisů uplatňují v rámci zeměpisných hranic.

Zahrnutí globálního cloudu Azure Active Directory (Azure AD) je nasazeno v následujících národních cloudech:  

- Azure Government
- Azure Germany
- Azure China 21Vianet

Národní cloudy jsou jedinečné a oddělené prostředí z globálního úložiště Azure. Je důležité si uvědomit klíčové rozdíly při vývoji aplikace pro tato prostředí. Mezi rozdíly patří registrace aplikací, získávání tokenů a konfigurace koncových bodů.

## <a name="app-registration-endpoints"></a>Koncové body registrace aplikace

Pro každý z národních cloudů je k dispozici samostatný Azure Portal. Chcete-li integrovat aplikace s platformou Microsoft identity v národním cloudu, je nutné zaregistrovat svou aplikaci samostatně v každém Azure Portal, který je specifický pro dané prostředí.

V následující tabulce jsou uvedené základní adresy URL koncových bodů Azure AD, které se používají k registraci aplikace pro každý národní Cloud.

| Národní Cloud | Koncový bod portálu Azure AD |
|----------------|--------------------------|
| Azure AD pro státní správu USA | `https://portal.azure.us` |
| Azure AD Německo | `https://portal.microsoftazure.de` |
| Azure AD Čína provozovaný společností 21Vianet | `https://portal.azure.cn` |
| Azure AD (globální služba) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Koncové body ověřování Azure AD

Všechny národní cloudy ověřují uživatele samostatně v každém prostředí a mají samostatné koncové body ověřování.

V následující tabulce jsou uvedené základní adresy URL pro koncové body služby Azure AD, které se používají k získání tokenů pro jednotlivé národní cloudy.

| Národní Cloud | Koncový bod ověřování Azure AD |
|----------------|-------------------------|
| Azure AD pro státní správu USA | `https://login.microsoftonline.us` |
| Azure AD Německo| `https://login.microsoftonline.de` |
| Azure AD Čína provozovaný společností 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (globální služba)| `https://login.microsoftonline.com` |

Pomocí základní adresy URL specifické pro danou oblast můžete vymezit požadavky na ověřování Azure AD nebo koncové body tokenu. Například pro Azure Německo:

  - Je `https://login.microsoftonline.de/common/oauth2/authorize`běžný koncový bod autorizace.
  - Společný koncový bod tokenu je `https://login.microsoftonline.de/common/oauth2/token`.

U aplikací pro jednoho tenanta nahraďte "Common" v předchozích adresách URL ID tenanta nebo názvem. Příklad: `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>Rozhraní API pro Microsoft Graph

Informace o tom, jak volat rozhraní API Microsoft Graph v národním cloudovém prostředí, najdete v tématu [Microsoft Graph v národních cloudových nasazeních](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Některé služby a funkce, které jsou v konkrétních oblastech globální služby, nemusí být k dispozici ve všech národních cloudech. Pokud chcete zjistit, jaké služby jsou k dispozici, přečtěte si [produkty dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Pokud se chcete dozvědět, jak vytvořit aplikaci pomocí platformy Microsoft identity, postupujte podle [kurzu MSAL (Microsoft Authentication Library)](msal-national-cloud.md). Konkrétně se tato aplikace bude přihlašovat uživateli a získat přístupový token pro volání rozhraní Microsoft Graph API.

## <a name="next-steps"></a>Další kroky

Další informace pro:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure Čína 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure (Německo)](https://docs.microsoft.com/azure/germany/)
- [Základy ověřování Azure AD](authentication-scenarios.md)
