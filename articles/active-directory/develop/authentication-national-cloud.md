---
title: Ověřování Azure AD & národní cloudy | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o koncových bodech registrace a ověřování aplikací pro národní cloudy.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262994"
---
# <a name="national-clouds"></a>Národní mraky

Národní cloudy jsou fyzicky izolované instance Azure. Tyto oblasti Azure jsou navržené tak, aby zajistily, že požadavky na rezidenci dat, suverenitu a dodržování předpisů jsou dodržovány v rámci geografických hranic.

Včetně globálního cloudu se Azure Active Directory (Azure AD) nasazuje v následujících národních cloudech:  

- Azure Government
- Azure Germany
- Azure China 21Vianet

Národní cloudy jsou jedinečné a samostatné prostředí od Azure global. Je důležité být si vědom klíčových rozdílů při vývoji aplikace pro tato prostředí. Rozdíly zahrnují registraci aplikací, získávání tokenů a konfiguraci koncových bodů.

## <a name="app-registration-endpoints"></a>Koncové body registrace aplikací

Pro každý národní cloud je samostatný portál Azure. Chcete-li integrovat aplikace s platformou identit Microsoftu v národním cloudu, musíte zaregistrovat aplikaci samostatně na každém portálu Azure, který je specifický pro prostředí.

V následující tabulce jsou uvedeny základní adresy URL pro koncové body Azure AD používané k registraci aplikace pro každý národní cloud.

| Národní cloud | Koncový bod portálu Azure AD |
|----------------|--------------------------|
| Azure AD pro vládu USA | `https://portal.azure.us` |
| Azure AD Německo | `https://portal.microsoftazure.de` |
| Azure AD Čína provozovaná společností 21Vianet | `https://portal.azure.cn` |
| Azure AD (globální služba) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Koncové body ověřování Azure AD

Všechny národní cloudy ověřují uživatele samostatně v každém prostředí a mají samostatné koncové body ověřování.

V následující tabulce jsou uvedeny základní adresy URL pro koncové body Azure AD používané k získání tokenů pro každý národní cloud.

| Národní cloud | Koncový bod ověřování Azure AD |
|----------------|-------------------------|
| Azure AD pro vládu USA | `https://login.microsoftonline.us` |
| Azure AD Německo| `https://login.microsoftonline.de` |
| Azure AD Čína provozovaná společností 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (globální služba)| `https://login.microsoftonline.com` |

Můžete vytvořit požadavky na autorizaci Azure AD nebo koncové body tokenu pomocí příslušné základní adresy URL specifické pro oblast. Například pro Azure Germany:

  - Registrace je běžným koncovým bodem `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Token společný koncový `https://login.microsoftonline.de/common/oauth2/token`bod je .

U aplikací s jedním tenantem nahraďte "běžné" v předchozích adresách URL id nebo názvem klienta. Příklad: `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Informace o tom, jak volat rozhraní API Microsoft Graphu v národním cloudovém prostředí, přejděte na [Microsoft Graph v nasazení národního cloudu](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Některé služby a funkce, které jsou v konkrétních oblastech globální služby nemusí být k dispozici ve všech národních cloudech. Chcete-li zjistit, jaké služby jsou k dispozici, přejděte na [produkty dostupné podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Chcete-li se dozvědět, jak vytvořit aplikaci pomocí platformy identit společnosti Microsoft, postupujte podle [kurzu Microsoft Authentication Library (MSAL).](msal-national-cloud.md) Konkrétně tato aplikace se přihlásí uživatele a získat přístupový token pro volání rozhraní Microsoft Graph API.

## <a name="next-steps"></a>Další kroky

Další informace:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure (Německo)](https://docs.microsoft.com/azure/germany/)
- [Základy ověřování Azure AD](authentication-scenarios.md)
