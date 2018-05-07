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
ms.openlocfilehash: b1cc1fad6b0831bcf0bab5ba4f37b753c3cf33ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Databáze SQL pro použití v zásobníku Microsoft Azure
Používat adaptér zprostředkovatele prostředků systému SQL Server jako službu vystavit databází SQL [zásobník Azure](azure-stack-poc.md). Po instalaci poskytovatele prostředků a připojte ho k jedné nebo více instancí systému SQL Server, můžete vytvořit vás a uživatele:
- Databáze pro nativní cloudové aplikace.
- Weby, které jsou založené na SQL.
- Úlohy, které jsou založené na SQL.
Nemáte pokaždé, když zřízení virtuálního počítače (VM) který je hostitelem systému SQL Server.

Zprostředkovatel prostředků nepodporuje všechny možnosti správy databáze z [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Například fondů elastické databáze a schopnost vytočit výkonu databáze nahoru a dolů automaticky nejsou k dispozici. Však nepodporuje Podpora zprostředkovatele prostředků podobné vytvářet, číst, aktualizovat a odstranit operace. Rozhraní API není kompatibilní s databází SQL.

## <a name="sql-resource-provider-adapter-architecture"></a>Architektura adaptér zprostředkovatele prostředků SQL
Zprostředkovatel prostředků se skládá ze tří součástí:

- **Adaptér pro poskytovatele prostředků SQL virtuálních počítačů**, což je virtuální počítač Windows, který spouští služby poskytovatele.
- **Zprostředkovatel prostředků samotné**, která zpracovává požadavky pro zřizování a zpřístupňuje databáze prostředků.
- **Servery, které jsou hostiteli systému SQL Server**, které poskytují kapacitu pro databáze názvem hostitelskými servery.

Musíte vytvořit jednu (nebo více) instance systému SQL Server nebo poskytovat přístup k externí instance systému SQL Server.

> [!NOTE]
> Hostitelské servery, které jsou nainstalovány v Azure zásobníku integrované systémy musí být vytvořeny z předplatného klienta. Nemohou být vytvářeny z předplatného výchozího zprostředkovatele. Musí být vytvořený z portálu klienta nebo z relace prostředí PowerShell příslušné přihlášení. Všechny hostitelské servery jsou fakturovatelné virtuální počítače a musí mít příslušnou licenci. Správce služby můžete být vlastníkem předplatného klienta.


## <a name="next-steps"></a>Další postup

[Nasazení poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md)
