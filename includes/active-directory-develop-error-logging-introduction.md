---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98763396"
---
Aplikace Microsoft Authentication Library (MSAL) generují zprávy protokolu, které mohou pomáhat diagnostikovat problémy. Aplikace může nakonfigurovat protokolování s několika řádky kódu a mít vlastní kontrolu nad úrovní podrobností a bez ohledu na to, jestli se protokolují osobní údaje a data organizace. Doporučujeme vytvořit MSAL zpětného volání protokolování a poskytnout uživatelům možnost Odeslat protokoly, když budou mít problémy s ověřením.

## <a name="logging-levels"></a>Úrovně protokolování

MSAL poskytuje několik úrovní podrobností protokolování:

- Chyba: indikuje, že došlo k chybě, a vygenerovala se chyba. Používá se pro ladění a identifikaci problémů.
- Upozornění: došlo k chybě nebo selhání nutně, ale jsou určené pro diagnostiku a problémy.
- Informace: MSAL bude protokolovat události určené pro informativní účely, které nejsou nutně určené pro ladění.
- Verbose: výchozí. MSAL zaznamená všechny podrobnosti o chování knihovny.

## <a name="personal-and-organizational-data"></a>Osobní a organizační data

Protokolovací nástroj MSAL ve výchozím nastavení nezachytí žádná citlivá osobní nebo organizační data. Knihovna nabízí možnost povolit protokolování osobních a organizačních dat, pokud se rozhodnete tak učinit.

Následující části obsahují další podrobnosti o protokolování chyb MSAL pro vaši aplikaci.