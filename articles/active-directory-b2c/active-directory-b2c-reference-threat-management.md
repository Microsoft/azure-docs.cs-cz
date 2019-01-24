---
title: Správa hrozeb k prostředkům a datům v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o detekci a zmírnění distribuovaných útoků techniky pro útoky s cílem odepření služeb a útoky hesel v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 66932aa4ea070c5f8ca83524a424e3b73b724c73
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845718"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Správa hrozeb k prostředkům a datům v Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C má integrované funkce, které vám umožní chránit proti hrozbám k prostředkům a datům. Tyto hrozby zahrnout heslo útokům a útoky s cílem odepření služeb. Útoky s cílem odepření služeb může vytvářet prostředky k dispozici pro odpovídající uživatelé. Heslo útoky vést k neoprávněnému přístupu k prostředkům. 

## <a name="denial-of-service-attacks"></a>Útoky s cílem odepření služeb

Azure AD B2C systémů proti útokům zahlcení SYN pomocí souboru cookie SYN. Azure AD B2C taky chrání před útoky DOS pomocí omezení pro míry a připojení.

## <a name="password-attacks"></a>Heslo útoky

Hesla, které jsou nastaveny uživatelé musejí být poměrně složité. Azure AD B2C zavedl techniky omezení rizik útoků heslo. Zmírnění dopadů zahrnuje útoky na hesla hrubou silou a slovníkové útoky heslo. Azure AD B2C s využitím různých signály, analyzuje integritu požadavků. Azure AD B2C je navržená k inteligentně rozlišení odpovídající uživatelé před hackery a botnetů. 

Azure AD B2C používá sofistikované strategii pro uzamčení účtů. Účty jsou uzamčené na základě na adrese IP požadavku a zadaných heslech. Doba trvání uzamčení se taky zvyšuje podle pravděpodobnosti, že se jedná o útoku. Po heslo se neúspěšně pokusila 10krát, dojde k uzamčení jedné minuty. Při příštím přihlášení neproběhne úspěšně po účet odemknut, další minutu trvající uzamčení dochází a pokračuje pro každé neúspěšné přihlášení. Opakovaně zadávat stejné heslo nebude počítat jako několik neúspěšných přihlášení. 

První období 10 uzamčení jsou dlouhé jednu minutu. Období dalších 10 uzamčení se trochu delší a zvýšení doby trvání za každých 10 uzamčení období. Resetování čítače uzamčení hodnotě nula. Po úspěšném přihlášení, když není účet uzamčen. Uzamčení období může trvat až pět hodin. 

V současné době není možné:

- Aktivovalo uzamčení s méně než 10 neúspěšných přihlášení
- Načíst seznam neuzamčení účty
- Konfigurace uzamčení zásad

Další informace najdete [Microsoft Trust Center](https://www.microsoft.com/trustcenter/default.aspx).
