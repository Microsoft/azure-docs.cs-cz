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
ms.openlocfilehash: 70128735aef64d273c63236a13b0ae28edb6077d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003646"
---
### <a name="sql-databases"></a>Databáze SQL

|  |  |
|---------|---------|
| [Povolené skladové položky pro databáze SQL](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Vyžaduje, aby databáze SQL používaly schválenou skladovou položku. Zadáte pole ID povolených skladových položek nebo pole názvů povolených skladových položek. |
| [Auditování nastavení detekce hrozeb na úrovni databáze](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | Provede audit zásad výstrah zabezpečení databáze SQL, pokud tyto zásady nejsou nastavené na zadaný stav. Zadejte hodnotu, která určí, jestli je detekce hrozeb povolená nebo ne.  |
| [Auditování šifrování databáze SQL](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Provede audit, jestli databáze SQL nemá povolené transparentní šifrování dat. |
| [Audit nastavení auditování na úrovni databáze SQL](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Provede audit nastavení auditování databáze SQL v případě, že tato nastavení nevyhovují zadaným kritériím. Zadejte hodnotu, která určí, jestli má být nastavení auditování povoleno nebo ne.  |
| [Auditování stavu transparentního šifrování dat](../articles/governance/policy/samples/audit-trans-data-enc-status.md) | Provede audit transparentního šifrování dat databáze SQL, jestliže není povoleno.  |