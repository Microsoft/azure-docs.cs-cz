---
title: Chování výstrah SMS ve skupinách akcí
description: Formát zprávy SMS a odpověď na zprávy SMS, které mají zrušit odběr, znovu přihlásit odběr nebo požádat o podporu.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.subservice: alerts
ms.openlocfilehash: 98558c2b1525094277dd6c165349f25ce5cd3b23
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717872"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Chování výstrah SMS ve skupinách akcí

## <a name="overview"></a>Přehled 
Skupiny akcí umožňují nakonfigurovat seznam akcí. Tyto skupiny se používají při definování výstrah. zajištění, že se při aktivaci výstrahy upozorní konkrétní skupina akcí Jedna z podporovaných akcí je SMS; Oznámení SMS podporují obousměrnou komunikaci. Uživatel může reagovat na server SMS na:

- **Odhlásit odběr výstrah:** Uživatel může zrušit odběr všech upozornění serveru SMS pro všechny skupiny akcí nebo jednu skupinu akcí.
- Znovu **přihlásit k odběru výstrah:** Uživatel může znovu přihlásit odběr všech výstrah serveru SMS pro všechny skupiny akcí nebo jednu skupinu akcí.  
- **Žádost o podporu:** Uživatel může požádat o další informace na serveru SMS. Budou přesměrovány na tento článek.

Tento článek se zabývá chováním výstrah serveru SMS a akcemi odpovědí, které může uživatel provést na základě národního prostředí uživatele:

## <a name="receiving-an-sms-alert"></a>Příjem výstrahy serveru SMS
Přijímač SMS nakonfigurovaný jako součást skupiny akcí obdrží zprávu SMS, když se aktivuje výstraha. Zpráva SMS obsahuje následující informace:
* Krátký název skupiny akcí, na kterou se tato výstraha odeslala
* Název výstrahy

| REPLY | Popis |
| ----- | ----------- |
| DEZAKTIVOVAT `<Action Group Short name>` | Zakáže další zprávu SMS ze skupiny akcí. |
| ABY `<Action Group Short name>` | Opětovné povolení serveru SMS ze skupiny akcí |
| Stop | Zakáže další SMS ze všech skupin akcí. |
| Čína | Opětovné povolení služby SMS ze všech skupin akcí |
| HELP | Uživateli se pošle odpověď s odkazem na tento článek. |

>[!NOTE]
>Pokud se uživatel odhlásí z upozornění serveru SMS, ale přidá se do nové skupiny akcí; BUDOU dostávat výstrahy SMS pro tuto novou skupinu akcí, ale zůstanou odhlášené ze všech předchozích skupin akcí.

## <a name="next-steps"></a>Další kroky
Získejte [Přehled výstrah protokolu aktivit](./alerts-overview.md) a Naučte se, jak získat výstrahy.  
Další informace o [omezování míry SMS](alerts-rate-limiting.md)  
Další informace o [skupinách akcí](./action-groups.md)