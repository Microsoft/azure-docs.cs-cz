---
title: Použití databází SQL v Azure stacku | Dokumentace Microsoftu
description: Zjistěte, jak nasadit SQL databáze jako služba v Azure stacku a rychlé kroky pro nasazení adaptéru poskytovatele prostředků SQL serveru.
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
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 7183cae491287042c778c2e56be8a1451c8c71a2
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247761"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Použití databází SQL v Microsoft Azure Stack

Nabídka databází SQL jako služba pomocí adaptéru poskytovatele prostředků SQL serveru [Azure Stack](azure-stack-poc.md). Po instalaci poskytovatele prostředků a jeho připojení k jedné nebo několika instancím SQL Serveru můžete vy i vaši uživatelé vytvářet:

- Databáze pro aplikace nativní pro cloud.
- Weby, které používají SQL.
- Úlohy, které používají SQL.

Poskytovatel prostředků neobsahuje všechny databáze schopnosti správy [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Například elastických fondů, které automaticky přidělit prostředky nejsou podporovány. Ale podporuje zprostředkovatel prostředků, podobně jako vytvoření, čtení, aktualizace a odstranění (CRUD) operací v databázi serveru SQL Server. 

## <a name="sql-resource-provider-adapter-architecture"></a>Architektura adaptéru poskytovatele prostředků SQL

Poskytovatel prostředků se skládá z následujících součástí:

- **SQL prostředků poskytovatele adaptér virtuálního počítače (VM)**, což je virtuální počítač Windows Server, na kterém běží služby poskytovatele.
- **Poskytovatel prostředků**, která zpracovává požadavky a prostředky databáze přístupy.
- **Servery, které hostují SQL Server**, které poskytují kapacity pro databáze názvem hostitelskými servery.

Musíte vytvořit alespoň jednu instanci systému SQL Server nebo poskytovat přístup k externí instance systému SQL Server.

> [!NOTE]
> Hostitelské servery, které jsou nainstalované ve službě Azure Stack integrované systémy musí být vytvořené z tenanta předplatného. Nelze vytvořit z výchozí předplatné poskytovatele. Musí se vytvořit portál pro klienty nebo pomocí prostředí PowerShell s odpovídající přihlášení. Všechny hostitelské servery jsou fakturovatelná virtuální počítače a musí mít licence. Správce služby může být vlastníkem předplatného tenanta.

## <a name="next-steps"></a>Další postup

[Nasazení poskytovatele prostředků SQL Serveru](azure-stack-sql-resource-provider-deploy.md)
