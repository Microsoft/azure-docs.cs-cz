---
title: Ověřování pomocí Azure AD v národních cloudech
description: Další informace o registraci a ověřování koncových bodů aplikace pro národní cloudy.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda
ms.custom: aaddev
ms.openlocfilehash: 866a86178d66b7b4af069d684e4eb56c12db47ca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981997"
---
# <a name="national-clouds"></a>Národní Cloudy

Národních cloudech (neboli suverénních cloudech) jsou fyzicky izolované instance Azure. Tyto oblasti Azure jsou navržené tak, aby jistotu, že požadavky na rezidenci a suverenitu, dodržování předpisů dat jsou zachované v zeměpisné oblasti.

Včetně globální cloudové služby Azure Active Directory nasazené v následujících národních cloudů:  

- Azure US Government
- Azure Germany
- Azure China 21Vianet

## <a name="app-registration-endpoints"></a>Koncové body registrace aplikace

V následující tabulce jsou uvedeny základní adresy URL pro koncové body služby Azure Active Directory (Azure AD), použije k registraci aplikace pro jednotlivé národní cloudy.

| Národních cloudů | Azure AD koncového bodu portálu
| --- | --- |
| Azure AD pro státní správu USA |https://portal.azure.us
|Služba Azure AD Germany |https://portal.microsoftazure.de
|Azure AD Číně, provozovaný společností 21Vianet |https://portal.azure.cn
|Azure AD (globální službu)|https://portal.azure.com

## <a name="azure-ad-authentication-endpoints"></a>Koncové body pro ověřování Azure AD

V následující tabulce jsou uvedeny základní adresy URL pro koncové body služby Azure Active Directory (Azure AD) umožňují získat tokeny pro volání Microsoft Graphu pro jednotlivé národní cloudy.

| Národních cloudů | Koncový bod ověřování Azure AD
| --- | --- |
| Azure AD pro státní správu USA |`https://login.microsoftonline.us`
|Služba Azure AD Germany| `https://login.microsoftonline.de`
|Azure AD Číně, provozovaný společností 21Vianet | `https://login.chinacloudapi.cn`
|Azure AD (globální službu)|`https://login.microsoftonline.com`

Požadavky na koncové body pro povolení nebo token Azure AD můžete tvar pomocí odpovídající základní adresu URL oblast. Například v případě Německo:

- Je běžné koncový bod autorizace `https://login.microsoftonline.de/common/oauth2/authorize`
- Je běžné koncový bod tokenu `https://login.microsoftonline.de/common/oauth2/token` 

Pro aplikace s jedním tenantem nahraďte běžné ve výše uvedených adres URL s id tenanta nebo název, například `https://login.microsoftonline.de/contoso.com`

>[!NOTE]
> [Autorizace Azure AD v2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) a token koncové body jsou dostupné jenom pro globální služby. Není dosud podporována pro nasazení národních cloudů.

## <a name="next-steps"></a>Další postup

- Další informace o [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- Další informace o [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- Další informace o [Azure Germany](https://docs.microsoft.com/azure/germany/)
- Další informace o [základy ověřování Azure AD](authentication-scenarios.md)
- Další informace o [Microsoft Graphu nasazení do národních cloudů](https://developer.microsoft.com/graph/docs/concepts/deployments)