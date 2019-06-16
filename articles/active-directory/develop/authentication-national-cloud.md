---
title: Ověřování pomocí Azure Active Directory v národních cloudech
description: Další informace o registraci a ověřování koncových bodů aplikace pro národní cloudy.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd37366697a9c1f5019d2864e6d81a4dcd02e3a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235493"
---
# <a name="national-clouds"></a>Národní cloudy

Národní cloudy jsou fyzicky izolované instance Azure. Tyto oblasti Azure jsou navržené tak, aby jistotu, že požadavky na rezidenci a suverenitu, dodržování předpisů dat jsou zachované v zeměpisné oblasti.

Včetně globální cloudové služby Azure Active Directory (Azure AD) nasazené v následujících národních cloudů:  

- Azure Government
- Azure Germany
- Azure China 21Vianet

Národní cloudy jsou jedinečné a samostatné prostředí z Azure global. Je důležité mít na paměti klíčové rozdíly při vývoji aplikace pro tato prostředí. Rozdíly zahrnují registrace aplikací, získávání tokenů a konfigurace koncových bodů.

## <a name="app-registration-endpoints"></a>Koncové body registrace aplikace

Neexistuje samostatné webu Azure portal pro každé z nich národních cloudů. Můžete integrovat aplikace s platformou identity Microsoft v národních cloudů, je nutné zaregistrovat aplikaci zvlášť každého webu Azure Portal, která je specifická pro prostředí.

V následující tabulce jsou uvedeny základní adresy URL pro koncové body služby Azure AD použije k registraci aplikace pro jednotlivé národní cloudy.

| Národních cloudů | Azure AD koncového bodu portálu |
|----------------|--------------------------|
| Azure AD pro státní správu USA | `https://portal.azure.us` |
| Služba Azure AD Germany | `https://portal.microsoftazure.de` |
| Azure AD Číně, provozovaný společností 21Vianet | `https://portal.azure.cn` |
| Azure AD (globální službu) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Koncové body pro ověřování Azure AD

Národní cloudy ověřovat uživatele samostatně v každém prostředí a mít samostatného ověření koncových bodů.

V následující tabulce jsou uvedeny základní adresy URL pro koncové body služby Azure AD slouží k získání tokenů pro jednotlivé národní cloudy.

| Národních cloudů | Koncový bod ověřování Azure AD |
|----------------|-------------------------|
| Azure AD pro státní správu USA | `https://login.microsoftonline.us` |
| Služba Azure AD Germany| `https://login.microsoftonline.de` |
| Azure AD Číně, provozovaný společností 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (globální službu)| `https://login.microsoftonline.com` |

Požadavky na povolení služby Azure AD nebo token koncových bodů můžete formulář pomocí odpovídající základní adresu URL oblast. Například pro Azure Germany:

  - Je běžné koncový bod autorizace `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Je běžné koncový bod tokenu `https://login.microsoftonline.de/common/oauth2/token`.

Pro aplikace s jedním tenantem nahraďte "common" v předchozí adresy URL s ID tenanta nebo název. Příklad: `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> [Autorizace Azure AD v2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) a token koncové body jsou k dispozici pouze pro globální službu. Nejsou podporovány pro nasazení národních cloudů.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Chcete-li získat informace o volání rozhraní API Microsoft Graphu v prostředí národního cloudu, přejděte na [Microsoft Graph v nasazeních národních cloudů](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Některé služby a funkce, které jsou v různých oblastech z globální služby nemusí být k dispozici ve všech národních cloudech. Pokud chcete zjistit, jaké služby jsou k dispozici, přejděte na [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Zjistěte, jak sestavit aplikaci pomocí Microsoft identity platform, postupujte [kurzu Microsoft Authentication Library (MSAL)](msal-national-cloud.md). Konkrétně tato aplikace se přihlásit uživatele a získání přístupového tokenu pro volání rozhraní Microsoft Graph API.

## <a name="next-steps"></a>Další postup

Další informace:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
- [Základy ověřování Azure AD](authentication-scenarios.md)
