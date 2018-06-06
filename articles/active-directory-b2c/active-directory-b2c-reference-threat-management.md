---
title: Hrozby správy v Azure Active Directory B2C | Microsoft Docs
description: Další informace o zjišťování a ke zmírnění techniky pro útoky DOS a útoky heslo v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/27/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b2686b4bb2b98d3f79d8087f6857c149cfdeb553
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711259"
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C: Hrozby správy

Správa hrozeb zahrnuje plánování pro ochranu před útoky na systém a sítě. Útoky DoS provést prostředky k dispozici příslušným uživatelům. Heslo útoky vést k neoprávněnému přístupu k prostředkům. Azure Active Directory B2C (Azure AD B2C) obsahuje integrované funkce, které vám umožní chránit vaše data před tyto hrozby několika způsoby.

## <a name="denial-of-service-attacks"></a>Útoky DoS

Azure AD B2C používá detekce a zmírnění techniky, jako třeba SYN soubory cookie a omezení rychlost a připojení k ochraně před útoky DoS příslušných prostředků.

## <a name="password-attacks"></a>Útoky heslo

Zmírnění techniky Azure AD B2C má také nastavené pro útoky heslo. Zmírnění dopadů zahrnuje útoky hrubou silou heslo a slovníkovým útokům heslo. Hesla, které jsou nastavené uživatelé musí být dostatečně komplexní. Pomocí různých signály analyzuje Azure AD B2C integritu požadavků. Azure AD B2C je určena pro inteligentně před hackery a botnetů rozlišit příslušným uživatelům. Azure AD B2C poskytuje sofistikované strategie pro lock účty podle zadaná v pravděpodobnost útoku hesla.

Další informace najdete v článku [Microsoft Trust Center](https://www.microsoft.com/trustcenter/security/threatmanagement).
