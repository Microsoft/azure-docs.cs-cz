---
title: 'Azure AD Connect: Synchronizovat instance služby | Dokumentace Microsoftu'
description: Tato stránka dokumenty speciální aspekty pro instance služby Azure AD.
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
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298817"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Speciální aspekty pro instance
Azure AD Connect je nejčastěji používaná celosvětové instance služby Azure AD a Office 365. Ale existují také jiné instance a ty mají různé požadavky na adresy URL a další důležité.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
[Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) je suverénních cloudů, kterou provozuje společnost dat pro Německo.

| Adresy URL pro otevření v proxy serveru |
| --- |
| \*.microsoftonline.de |
| \*. windows.net |
| + Seznamy odvolaných certifikátů |

Při přihlášení k tenantovi Azure AD, musíte použít účet v doméně onmicrosoft.de.

Funkce aktuálně není k dispozici v Microsoft cloudu Německo:

* **Zpětný zápis hesla** dostupná ve verzi preview služby Azure AD Connect verze 1.1.570.0 a po.
* Další služby Azure AD Premium nejsou k dispozici.

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
[Cloudu Microsoft Azure Government](https://azure.microsoft.com/features/gov/) je cloud určený pro státní správu USA.

Tento cloud má byla podporuje starší verze nástroje DirSync. Z buildu 1.1.180 služby Azure AD Connect se podporuje další generaci cloudu. Tato generace používá pouze pro USA založené na koncových bodech a mít různé seznamy adres URL pro otevření v proxy serveru.

| Adresy URL pro otevření v proxy serveru |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (vyžadováno pro automatické zjišťování tenanta Azure Government) |
| \*.gov.us.microsoftonline.com |
| + Seznamy odvolaných certifikátů |

> [!NOTE]
> Od Azure AD Connect verze 1.1.647.0, nastavení AzureInstance hodnoty v registru se už nevyžaduje za předpokladu, že *. windows.net je otevřený na vašich serverech proxy serveru. Však pro zákazníky, které neumožňují připojení k Internetu z jejich serverech Azure AD Connect, následující ruční konfigurace můžete použít.

### <a name="manual-configuration"></a>Ruční konfigurace

Následující postup ruční konfigurace se používají k zajištění, že použije koncových bodů Azure Government synchronizace Azure AD Connect.

1. Spusťte instalaci Azure AD Connect.
2. Když se zobrazí první stránka, kde máte smlouvy EULA, nepokračujte dál, ale ponechte spuštění Průvodce instalací.
3. Spustit program regedit a změňte klíč registru `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` hodnotě `4`.
4. Přejděte zpět do Průvodce Azure AD Connect, přijměte smlouvu EULA a pokračovat. Během instalace, je nutné použít **vlastní konfigurace** instalační cesty (a není Expresní instalace), pak pokračovat v instalaci jako obvykle.

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
