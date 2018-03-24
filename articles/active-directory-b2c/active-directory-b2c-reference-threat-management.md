---
title: 'Azure Active Directory B2C: Hrozby správy | Microsoft Docs'
description: Další informace o zjišťování a ke zmírnění techniky pro útoky DOS a útoky heslo v Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/27/2016
ms.author: davidmu
ms.openlocfilehash: 5ab699b0dccd772ec905699d94dedaca0eefcdad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C: Hrozby správy

Správa hrozeb zahrnuje plánování pro ochranu před útoky na systém a sítě. Útoky DoS provést prostředky k dispozici příslušným uživatelům. Heslo útoky vést k neoprávněnému přístupu k prostředkům. Azure Active Directory B2C (Azure AD B2C) obsahuje integrované funkce, které vám umožní chránit vaše data před tyto hrozby několika způsoby.

## <a name="denial-of-service-attacks"></a>Útoky DoS

Azure AD B2C používá detekce a zmírnění techniky, jako třeba SYN soubory cookie a omezení rychlost a připojení k ochraně před útoky DoS příslušných prostředků.

## <a name="password-attacks"></a>Útoky heslo

Zmírnění techniky Azure AD B2C má také nastavené pro útoky heslo. Zmírnění dopadů zahrnuje útoky hrubou silou heslo a slovníkovým útokům heslo. Hesla, které jsou nastavené uživatelé musí být dostatečně komplexní. Pomocí různých signály analyzuje Azure AD B2C integritu požadavků. Azure AD B2C je určena pro inteligentně před hackery a botnetů rozlišit příslušným uživatelům. Azure AD B2C poskytuje sofistikované strategie pro lock účty podle zadaná v pravděpodobnost útoku hesla.

Další informace najdete v článku [Microsoft Trust Center](https://www.microsoft.com/trustcenter/security/threatmanagement).
