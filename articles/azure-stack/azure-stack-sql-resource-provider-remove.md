---
title: Používání databáze SQL v Azure zásobníku | Microsoft Docs
description: Zjistěte, jak můžete nasadit databází SQL jako služba na Azure zásobníku a rychlé kroky k nasazení adaptér zprostředkovatele prostředků systému SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: c2686a2d5241af46e70263d1827028aa7e9b2138
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="remove-the-sql-resource-provider"></a>Odebrat zprostředkovatele prostředků SQL

Odebrat zprostředkovatele prostředků SQL, je nutné nejprve odebrat všechny závislosti:

1. Ujistěte se, že máte původní balíček nasazení, který jste stáhli pro tuto verzi adaptér zprostředkovatele prostředků SQL.

2. Od zprostředkovatele prostředků je třeba odstranit všechny uživatelské databáze. (Odstranění uživatelských databází neodstraní data.) Tato úloha by měla provést sami uživatelé.

3. Správce musí odstranit hostitelskými servery z adaptéru zprostředkovatele prostředků SQL.

4. Správce musí odstranit všechny plány, které odkazují na adaptéru zprostředkovatele prostředků SQL.

5. Správce musí odstranit všechny identifikátory SKU a kvóty, které jsou spojeny s adaptérem zprostředkovatele prostředků SQL.

6. Spusťte znovu skript nasazení s následující prvky:
    - -Odinstalovat parametr
    - Koncové body Azure Resource Manager
    - DirectoryTenantID
    - Přihlašovací údaje k účtu správce služby

