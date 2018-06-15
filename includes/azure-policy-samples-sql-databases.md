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
ms.openlocfilehash: 50a6388a95bce14bc9af7e0c9a5387e148f6fa73
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664494"
---
### <a name="sql-databases"></a>Databáze SQL

|  |  |
|---------|---------|
| [Povolené skladové položky pro databáze SQL](../articles/azure-policy/scripts/allowed-sql-db-skus.md) | Vyžaduje, aby databáze SQL používaly schválenou skladovou položku. Zadáte pole ID povolených skladových položek nebo pole názvů povolených skladových položek. |
| [Auditování nastavení detekce hrozeb na úrovni databáze](../articles/azure-policy/scripts/audit-db-threat-det-setting.md) | Provede audit zásad výstrah zabezpečení databáze SQL, pokud tyto zásady nejsou nastavené na zadaný stav. Zadejte hodnotu, která určí, jestli je detekce hrozeb povolená nebo ne.  |
| [Auditování šifrování databáze SQL](../articles/azure-policy/scripts/sql-database-encryption-audit.md) | Provede audit, jestli databáze SQL nemá povolené transparentní šifrování dat. |
| [Audit nastavení auditování na úrovni databáze SQL](../articles/azure-policy/scripts/audit-sql-db-audit-setting.md) | Provede audit nastavení auditování databáze SQL v případě, že tato nastavení nevyhovují zadaným kritériím. Zadejte hodnotu, která určí, jestli má být nastavení auditování povoleno nebo ne.  |
| [Auditování stavu transparentního šifrování dat](../articles/azure-policy/scripts/audit-trans-data-enc-status.md) | Provede audit transparentního šifrování dat databáze SQL, jestliže není povoleno.  |