---
title: Hrozeb správy v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o detekci a zmírnění distribuovaných útoků techniky pro útoky s cílem odepření služeb a útoky hesel v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1801fe9695aa15850d600300b957df2c7d7cd9ef
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055657"
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C: Řízení rizik

Řízení rizik zahrnuje plánování pro ochranu před útoky na systém a sítě. Útoky s cílem odepření služeb může vytvářet prostředky k dispozici pro odpovídající uživatelé. Heslo útoky vést k neoprávněnému přístupu k prostředkům. Azure Active Directory B2C (Azure AD B2C) má integrované funkce, které vám pomůže ochránit vaše data před těmito hrozbami několika různými způsoby.

## <a name="denial-of-service-attacks"></a>Útoky s cílem odepření služeb

Azure AD B2C používá detekci a zmírnění distribuovaných útoků postupů, jako jsou soubory cookie SYN a omezení frekvence a připojení k ochraně před útoky DoS příslušných prostředků.

## <a name="password-attacks"></a>Heslo útoky

Omezení rizik techniky Azure AD B2C má také místo hesla útoků. Zmírnění dopadů zahrnuje útoky na hesla hrubou silou a slovníkové útoky heslo. Hesla, které jsou nastaveny uživatelé musejí být poměrně složité. Azure AD B2C s využitím různých signály, analyzuje integritu požadavků. Azure AD B2C je navržená k inteligentně rozlišení odpovídající uživatelé před hackery a botnetů. Azure AD B2C poskytuje sofistikované strategii pro uzamčení účtů podle zadaná v pravděpodobnost útoku hesla.

Další informace najdete [Microsoft Trust Center](https://www.microsoft.com/trustcenter/default.aspx).
