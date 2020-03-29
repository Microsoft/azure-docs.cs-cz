---
title: 'Azure AD Connect: Synchronizace instancí služeb | Dokumenty společnosti Microsoft'
description: Na této stránce jsou popsány zvláštní aspekty pro instance Azure AD.
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
ms.openlocfilehash: c342eac5460d8d52422b0497b1283f367660eb3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66298817"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Zvláštní důležité informace pro instance
Azure AD Connect se nejčastěji používá s celorozšířenou instancí Azure AD a Office 365. Existují však i další instance a ty mají různé požadavky na adresy URL a další zvláštní aspekty.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Německo
[Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) je suverénní cloud provozovaný německým datovým správcem.

| Adresy URL, které se mají otevřít na proxy serveru |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +Seznamy odvolaných certifikátů |

Když se přihlásíte ke svému tenantovi Azure AD, musíte použít účet v doméně onmicrosoft.de.

Funkce, které se v současné době nevyskytují v microsoft cloudu v Německu:

* **Zpětný zápis hesla** je k dispozici pro náhled s Azure AD Connect verze 1.1.570.0 a po.
* Ostatní služby Azure AD Premium nejsou k dispozici.

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
[Cloud Microsoft Azure Government](https://azure.microsoft.com/features/gov/) je cloud pro vládu USA.

Tento cloud byl podporován dřívějšími verzemi DirSync. Z sestavení 1.1.180 Azure AD Connect je podporovaná další generace cloudu. Tato generace používá koncové body založené pouze na USA a mají jiný seznam adres URL otevřít na serveru proxy.

| Adresy URL, které se mají otevřít na proxy serveru |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net (Povinné pro automatickou detekci tenanta Azure Government) |
| \*.gov.us.microsoftonline.com |
| +Seznamy odvolaných certifikátů |

> [!NOTE]
> Od Azure AD Connect verze 1.1.647.0 nastavení azureinstance hodnotu v registru již není nutné za předpokladu, že *.windows.net je otevřena na proxy serverech. Pro zákazníky, kteří nepovolují připojení k Internetu ze svých serverů Azure AD Connect, však lze použít následující ruční konfiguraci.

### <a name="manual-configuration"></a>Ruční konfigurace

Následující kroky ruční konfigurace se používají k zajištění Azure AD Connect používá koncové body synchronizace Azure Government.

1. Spusťte instalaci Služby Azure AD Connect.
2. Když se zobrazí první stránka, na které máte přijmout eula, nepokračujte, ale nechte průvodce instalací spuštěn.
3. Spusťte regedit a `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` změňte `4`klíč registru na hodnotu .
4. Vraťte se k průvodci instalací služby Azure AD Connect, přijměte eula a pokračujte. Během instalace se ujistěte, že používáte vlastní instalační cestu **konfigurace** (a ne expresní instalaci), a pak pokračujte v instalaci obvyklým způsobem.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
