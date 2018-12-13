---
title: zahrnout soubor
description: zahrnout soubor
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: c8453a2ec00a2fca107f85a23a8af1e6313a70b6
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318156"
---
### <a name="sql-servers"></a>SQL Servery

|  |  |
|---------|---------|
| [Provedení auditu, když není žádný správce Azure Active Directory](../articles/governance/policy/samples/audit-no-aad-admin.md) | Provedete audit v situaci, kdy k SQL serveru není přiřazený žádný správce Azure Active Directory. |
| [Audit nastavení detekce hrozeb na úrovni serveru](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | Provede audit zásad výstrah zabezpečení databáze SQL, pokud tyto zásady nejsou nastavené na zadaný stav. Zadejte hodnotu, která určí, jestli je detekce hrozeb povolená nebo ne.  |
| [Auditování nastavení auditování serveru SQL](../articles/governance/policy/samples/sql-server-audit.md) | Provede audit serveru SQL podle toho, jestli jsou povolená nastavení auditování. |
| [Audit nastavení auditování na úrovni SQL Serveru](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | Provede audit nastavení auditování SQL Serveru v případě, že tato nastavení nevyhovují zadaným kritériím. Zadejte hodnotu, která určí, jestli má být nastavení auditování povoleno nebo ne. |
| [Vyžadovat SQL Server verze 12.0](../articles/governance/policy/samples/require-sql-12.md) | Vyžaduje, aby SQL Servery používaly verzi 12.0.  |