---
title: 'Azure AD Connect: Instance služby synchronizace | Dokumentace Microsoftu'
description: Tato stránka dokumenty speciální aspekty pro instance služby Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: d086b958ddca6caded19cc02a790f8091aba993e
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425164"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Speciální aspekty pro instance
Azure AD Connect je nejčastěji používaná celosvětové instance služby Azure AD a Office 365. Ale existují také jiné instance a ty mají různé požadavky na adresy URL a další důležité.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
[Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) je suverénních cloudů, kterou provozuje společnost dat pro Německo.

| Adresy URL pro otevření v proxy serveru |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + Seznamy odvolaných certifikátů |

Při přihlášení k tenantovi Azure AD, musíte použít účet v doméně onmicrosoft.de.

Funkce aktuálně není k dispozici v Microsoft cloudu Německo:

* **Zpětný zápis hesla** dostupná ve verzi preview služby Azure AD Connect verze 1.1.570.0 a po.
* Další služby Azure AD Premium nejsou k dispozici.

## <a name="microsoft-azure-government-cloud"></a>Cloud Microsoft Azure Government
[Cloudu Microsoft Azure Government](https://azure.microsoft.com/features/gov/) je cloud určený pro státní správu USA.

Tento cloud má byla podporuje starší verze nástroje DirSync. Z buildu 1.1.180 služby Azure AD Connect se podporuje další generaci cloudu. Tato generace používá pouze pro USA založené na koncových bodech a mít různé seznamy adres URL pro otevření v proxy serveru.

| Adresy URL pro otevření v proxy serveru |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (vyžadováno pro automatické zjišťování tenanta Azure AD státní správu) |
| \*.gov.us.microsoftonline.com |
| + Seznamy odvolaných certifikátů |

> [!NOTE]
> Od AAD Connect verze 1.1.647.0 nastavení AzureInstance hodnoty v registru se už nevyžaduje za předpokladu, že *. windows.net je otevřený na vašich serverech proxy serveru.

Funkce aktuálně není k dispozici v cloudu Microsoft Azure Government:

* **Zpětný zápis hesla** dostupná ve verzi preview služby Azure AD Connect verze 1.1.570.0 a po.
* Další služby Azure AD Premium nejsou k dispozici.

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
