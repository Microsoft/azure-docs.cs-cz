---
title: 'Azure AD Connect: synchronizační instance služby | Microsoft Docs'
description: Tato stránka popisuje zvláštní předpoklady pro instance služby Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c4d8b0a33763a967550453d8a205258f7583084
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90015258"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: zvláštní předpoklady pro instance
Azure AD Connect se nejčastěji používá v rámci světové instance Azure AD a Microsoft 365. Existují však i další instance a mají různé požadavky na adresy URL a další zvláštní důležité požadavky.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Německo
[Microsoft Cloud Německo](https://www.microsoft.de/cloud-deutschland) je svrchovaný Cloud provozovaný německým důvěryhodným datovým správcem.

| Adresy URL, které se mají otevřít v proxy server |
| --- |
| \*. microsoftonline.de |
| \*.windows.net |
| + Seznamy odvolaných certifikátů |

Když se přihlásíte ke klientovi služby Azure AD, musíte použít účet v doméně onmicrosoft.de.

Funkce, které nejsou v Microsoft Cloud Německu v současnosti k dispozici:

* **Zpětný zápis hesla** je k dispozici pro verzi preview s Azure AD Connect verzí 1.1.570.0 a After.
* Další Azure AD Premium služby nejsou k dispozici.

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
[Cloud Microsoft Azure Government](https://azure.microsoft.com/features/gov/) je Cloud pro státní správu USA.

Tento Cloud byl podporován staršími verzemi DirSync. V rámci sestavení 1.1.180 Azure AD Connect je podporována další generace cloudu. Tato generace používá koncové body založené jenom na US a má jiný seznam adres URL, které se otevřou v proxy server.

| Adresy URL, které se mají otevřít v proxy server |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (vyžadováno pro Azure Government zjišťování klientů) |
| \*. gov.us.microsoftonline.com |
| + Seznamy odvolaných certifikátů |

> [!NOTE]
> Od Azure AD Connect verze 1.1.647.0 se už nepožaduje nastavení hodnoty AzureInstance v registru, pokud je na vašich proxy serverch otevřený tento znak: *. windows.net. Pro zákazníky, kteří neumožňují připojení k Internetu ze svých Azure AD Connect serverů, je však možné použít následující ruční konfiguraci.

### <a name="manual-configuration"></a>Ruční konfigurace

Následující kroky ruční konfigurace se používají k zajištění, aby Azure AD Connect používaly koncové body synchronizace Azure Government.

1. Spusťte instalaci Azure AD Connect.
2. Po zobrazení první stránky, na kterou chcete přijmout smlouvu EULA, nepokračujte, ale ponechejte Průvodce instalací spuštěný.
3. Spusťte příkaz regedit a změňte klíč registru `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` na hodnotu `4` .
4. Vraťte se zpět do Průvodce instalací Azure AD Connect, přijměte smlouvu EULA a pokračujte. Během instalace se ujistěte, že používáte vlastní cestu k instalaci **Konfigurace** (a ne expresní instalaci), a potom pokračujte v instalaci obvyklým způsobem.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
