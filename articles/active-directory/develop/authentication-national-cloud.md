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
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: 41bb7287aaa045e191d61abb00f470018fda9cbe
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105031592"
---
# <a name="national-clouds"></a>Národní cloudy

Národní cloudy jsou fyzicky izolované instancemi Azure. Tyto oblasti Azure jsou navržené tak, aby se zajistilo, že se nároky na data, svrchovanost a dodržování předpisů uplatňují v rámci zeměpisných hranic.

Zahrnutí globálního cloudu Azure Active Directory (Azure AD) je nasazeno v následujících národních cloudech:  

- Azure Government
- Azure Germany
- Azure (Čína) 21Vianet

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
| Azure AD Čína provozovaný společností 21Vianet | `https://login.partner.microsoftonline.cn` |
| Azure AD (globální služba)| `https://login.microsoftonline.com` |

Pomocí základní adresy URL specifické pro danou oblast můžete vymezit požadavky na ověřování Azure AD nebo koncové body tokenu. Například pro Azure Německo:

- Běžný koncový bod autorizace je `https://login.microsoftonline.de/common/oauth2/v2.0/authorize` .
- Společný koncový bod tokenu je `https://login.microsoftonline.de/common/oauth2/v2.0/token` .

U aplikací pro jednoho tenanta nahraďte "Common" v předchozích adresách URL ID tenanta nebo názvem. Příklad: `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Informace o tom, jak volat rozhraní API Microsoft Graph v národním cloudovém prostředí, najdete v tématu [Microsoft Graph v národních cloudových nasazeních](/graph/deployments).

> [!IMPORTANT]
> Některé služby a funkce, které jsou v konkrétních oblastech globální služby, nemusí být k dispozici ve všech národních cloudech. Pokud chcete zjistit, jaké služby jsou k dispozici, přečtěte si [produkty dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Pokud se chcete dozvědět, jak vytvořit aplikaci pomocí platformy Microsoft identity, postupujte podle [kurzu MSAL (Microsoft Authentication Library)](msal-national-cloud.md). Konkrétně se tato aplikace bude přihlašovat uživateli a získat přístupový token pro volání rozhraní Microsoft Graph API.

## <a name="next-steps"></a>Další kroky

Naučte se používat [Microsoft Authentication Library (MSAL) v národním cloudovém prostředí](msal-national-cloud.md).

Dokumentace k národnímu cloudu:

- [Azure Government](../../azure-government/index.yml)
- [Azure (Čína) 21Vianet](/azure/china/)
- [Azure (Německo)](../../germany/index.yml)
