---
title: zahrnout soubor
description: zahrnout soubor
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 314f29a1135e355597e890b72ef3c0b7372194e6
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227203"
---
### <a name="sql-databases"></a>Databáze SQL

|  |  |
|---------|---------|
| [Povolené skladové položky pro databáze SQL](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Vyžaduje, aby databáze SQL používaly schválenou skladovou položku. Zadáte pole ID povolených skladových položek nebo pole názvů povolených skladových položek. |
| [Auditování nastavení detekce hrozeb na úrovni databáze](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | Provede audit zásad výstrah zabezpečení databáze SQL, pokud tyto zásady nejsou nastavené na zadaný stav. Zadejte hodnotu, která určí, jestli je detekce hrozeb povolená nebo ne.  |
| [Auditování šifrování databáze SQL](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Provede audit, jestli databáze SQL nemá povolené transparentní šifrování dat. |
| [Audit nastavení auditování na úrovni databáze SQL](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Provede audit nastavení auditování databáze SQL v případě, že tato nastavení nevyhovují zadaným kritériím. Zadejte hodnotu, která určí, jestli má být nastavení auditování povoleno nebo ne.  |