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
ms.openlocfilehash: 76e161be1adca4aa2ec7b682a13b0a42e4b79412
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003634"
---
### <a name="sql-servers"></a>SQL Servery

|  |  |
|---------|---------|
| [Provedení auditu, když není žádný správce Azure Active Directory](../articles/governance/policy/samples/audit-no-aad-admin.md) | Provedete audit v situaci, kdy k SQL serveru není přiřazený žádný správce Azure Active Directory. |
| [Audit nastavení detekce hrozeb na úrovni serveru](../articles/governance/policy/samples/audit-sql-ser-threat-det-setting.md) | Provede audit zásad výstrah zabezpečení databáze SQL, pokud tyto zásady nejsou nastavené na zadaný stav. Zadejte hodnotu, která určí, jestli je detekce hrozeb povolená nebo ne.  |
| [Auditování nastavení auditování serveru SQL](../articles/governance/policy/samples/sql-server-audit.md) | Provede audit serveru SQL podle toho, jestli jsou povolená nastavení auditování. |
| [Audit nastavení auditování na úrovni SQL Serveru](../articles/governance/policy/samples/audit-sql-ser-leve-audit-setting.md) | Provede audit nastavení auditování SQL Serveru v případě, že tato nastavení nevyhovují zadaným kritériím. Zadejte hodnotu, která určí, jestli má být nastavení auditování povoleno nebo ne. |
| [Vyžadovat SQL Server verze 12.0](../articles/governance/policy/samples/req-sql-12.md) | Vyžaduje, aby SQL Servery používaly verzi 12.0.  |