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
ms.openlocfilehash: 7bf885f2ab81e5d86878d2b0b53f4e98b8aedd9a
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318244"
---
### <a name="sql-databases"></a>Databáze SQL

|  |  |
|---------|---------|
| [Povolené skladové položky pro databáze SQL](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Vyžaduje, aby databáze SQL používaly schválenou skladovou položku. Zadáte pole ID povolených skladových položek nebo pole názvů povolených skladových položek. |
| [Auditování nastavení detekce hrozeb na úrovni databáze](../articles/governance/policy/samples/audit-db-threat-detection-setting.md) | Provede audit zásad výstrah zabezpečení databáze SQL, pokud tyto zásady nejsou nastavené na zadaný stav. Zadejte hodnotu, která určí, jestli je detekce hrozeb povolená nebo ne.  |
| [Auditování šifrování databáze SQL](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Provede audit, jestli databáze SQL nemá povolené transparentní šifrování dat. |
| [Audit nastavení auditování na úrovni databáze SQL](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Provede audit nastavení auditování databáze SQL v případě, že tato nastavení nevyhovují zadaným kritériím. Zadejte hodnotu, která určí, jestli má být nastavení auditování povoleno nebo ne.  |