---
title: zahrnout soubor
description: zahrnout soubor
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 298b92205e73b3623db02fb1dd803edac8379700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664488"
---
### <a name="sql-servers"></a>SQL Servery

|  |  |
|---------|---------|
| [Provedení auditu, když není žádný správce Azure Active Directory](../articles/azure-policy/scripts/audit-no-aad-admin.md) | Provedete audit v situaci, kdy k SQL serveru není přiřazený žádný správce Azure Active Directory. |
| [Audit nastavení detekce hrozeb na úrovni serveru](../articles/azure-policy/scripts/audit-sql-ser-threat-det-setting.md) | Provede audit zásad výstrah zabezpečení databáze SQL, pokud tyto zásady nejsou nastavené na zadaný stav. Zadejte hodnotu, která určí, jestli je detekce hrozeb povolená nebo ne.  |
| [Auditování nastavení auditování serveru SQL](../articles/azure-policy/scripts/sql-server-audit.md) | Provede audit serveru SQL podle toho, jestli jsou povolená nastavení auditování. |
| [Audit nastavení auditování na úrovni SQL Serveru](../articles/azure-policy/scripts/audit-sql-ser-leve-audit-setting.md) | Provede audit nastavení auditování SQL Serveru v případě, že tato nastavení nevyhovují zadaným kritériím. Zadejte hodnotu, která určí, jestli má být nastavení auditování povoleno nebo ne. |
| [Vyžadovat SQL Server verze 12.0](../articles/azure-policy/scripts/req-sql-12.md) | Vyžaduje, aby SQL Servery používaly verzi 12.0.  |