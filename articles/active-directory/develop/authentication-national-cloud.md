---
title: Ověřování pomocí Azure AD v národních cloudech
description: Další informace o registraci a ověřování koncových bodů aplikace pro národní cloudy.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/20/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a487c233e7bb71f05c6f2181d9c822ca508a583c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192944"
---
# <a name="national-clouds"></a>Národní cloudy

Národních cloudech (neboli suverénních cloudech) jsou fyzicky izolované instance Azure. Tyto oblasti Azure jsou navržené tak, aby jistotu, že požadavky na rezidenci a suverenitu, dodržování předpisů dat jsou zachované v zeměpisné oblasti.

Včetně globální cloudové služby Azure Active Directory nasazené v následujících národních cloudů:  

- Azure US Government
- Azure Germany
- Azure China 21Vianet

Národní cloudy jsou jedinečné a jiné prostředí, než Azure globální. Proto je potřeba mít na paměti několik klíčových rozdílů při vývoji aplikace pro tato prostředí, jako je registrace aplikací, získávání tokenů a konfigurace koncových bodů.

## <a name="app-registration-endpoints"></a>Koncové body registrace aplikace

Neexistuje samostatné webu Azure portal pro každé z nich národních cloudů. Můžete integrovat aplikace s platformou Identity Microsoft v národních cloudů, je nutné zaregistrovat aplikaci zvlášť v každém z webu Azure portal specifická pro prostředí.

V následující tabulce jsou uvedeny základní adresy URL pro koncové body služby Azure Active Directory (Azure AD), použije k registraci aplikace pro jednotlivé národní cloudy.

| Národních cloudů | Azure AD koncového bodu portálu
| --- | --- |
| Azure AD pro státní správu USA |https://portal.azure.us
|Služba Azure AD Germany |https://portal.microsoftazure.de
|Azure AD Číně, provozovaný společností 21Vianet |https://portal.azure.cn
|Azure AD (globální službu)|https://portal.azure.com 

## <a name="azure-ad-authentication-endpoints"></a>Koncové body pro ověřování Azure AD

Národní cloudy ověřovat uživatele samostatně v každém prostředí a mít samostatného ověření koncových bodů.

V následující tabulce jsou uvedeny základní adresy URL pro koncové body služby Azure Active Directory (Azure AD), které slouží k získání tokenů pro jednotlivé národní cloudy.

| Národních cloudů | Koncový bod ověřování Azure AD
| --- | --- |
| Azure AD pro státní správu USA |`https://login.microsoftonline.us`
|Služba Azure AD Germany| `https://login.microsoftonline.de`
|Azure AD Číně, provozovaný společností 21Vianet | `https://login.chinacloudapi.cn`
|Azure AD (globální službu)|`https://login.microsoftonline.com`

- Požadavky na koncové body pro povolení nebo token Azure AD můžete tvar pomocí odpovídající základní adresu URL oblast. Například pro Azure Germany:

  - Je běžné koncový bod autorizace `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Je běžné koncový bod tokenu `https://login.microsoftonline.de/common/oauth2/token`.

- Pro aplikace s jedním tenantem, nahraďte běžné v předchozí adresy URL s ID tenanta nebo název, například `https://login.microsoftonline.de/contoso.com`.

>[!NOTE]
> [Autorizace Azure AD v2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) a token koncové body jsou dostupné jenom pro globální služby. Není dosud podporována pro nasazení národních cloudů.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Další informace o volání rozhraní API Microsoft Graphu v prostředí národního cloudu, přejděte na [Microsoft Graph v národních cloudů](https://developer.microsoft.com/graph/docs/concepts/deployments).



>[!IMPORTANT]
Některé služby a funkce, které jsou v různých oblastech z globální služby nemusí být k dispozici ve všech národních cloudech. Chcete-li zjistit, jaké služby jsou k dispozici přejděte na [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

## <a name="next-steps"></a>Další postup

- Další informace o [Azure Government](https://docs.microsoft.com/azure/azure-government/).
- Další informace o [Azure China 21Vianet](https://docs.microsoft.com/azure/china/).
- Další informace o [Azure Germany](https://docs.microsoft.com/azure/germany/).
- Další informace o [základy ověřování Azure AD](authentication-scenarios.md).
