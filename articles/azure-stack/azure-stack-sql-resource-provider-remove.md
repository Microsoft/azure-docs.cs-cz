---
title: Odebrání poskytovatele prostředků SQL ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak odebrat poskytovatele prostředků SQL z nasazení služby Azure Stack.
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
ms.date: 11/20/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: ac0b4ddf876fb8108e20eeaf48c6fb0ab797af0d
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246327"
---
# <a name="remove-the-sql-resource-provider"></a>Odebrat poskytovatele prostředků SQL

Před odebráním poskytovatele prostředků SQL, musíte odebrat všechny závislosti zprostředkovatele. Budete také potřebovat kopii balíčku pro nasazení, který byl použit k instalaci zprostředkovatele prostředků.

> [!NOTE]
> Můžete najít odkazy ke stažení pro prostředek zprostředkovatele instalační programy v [nasazení požadavky na poskytovatele prostředků](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Odebrání poskytovatele prostředků SQL nedojde k odstranění databáze tenantů z hostitelské servery.

## <a name="dependency-cleanup"></a>Vyčištění závislostí

Následuje několik úloh vyčištění před spuštěním skriptu DeploySqlProvider.ps1 odebrat poskytovatele prostředků.

Operátor Azure stacku zodpovídá za tyto úlohy čištění:

* Odstraňte všechny plány, které odkazují na adaptéru pro SQL.
* Odstraňte všechny kvóty, které jsou spojené s adaptérem SQL.

## <a name="to-remove-the-sql-resource-provider"></a>Chcete-li odebrat poskytovatele prostředků SQL

1. Ověřte, že jste odebrali všechny existující SQL poskytovatele závislosti prostředků.

   > [!NOTE]
   > Odinstalace poskytovatele prostředků SQL bude pokračovat i v případě poskytovatele prostředků aktuálně používají závislé prostředky.
  
2. Získat kopii balíčku instalace poskytovatele prostředků SQL a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře.

3. Otevřete okno konzole Powershellu s nová se zvýšenými oprávněními a přejděte do adresáře, které jste extrahovali soubory instalace poskytovatele prostředků SQL.

4. Spusťte skript DeploySqlProvider.ps1 s následujícími parametry:

    * **Odinstalujte**. Odebere poskytovatele prostředků a všechny související prostředky.
    * **PrivilegedEndpoint**. IP adresa nebo název DNS privileged koncového bodu.
    * **AzureEnvironment**. Prostředí Azure používá pro nasazení Azure Stack. Vyžaduje se jenom pro nasazení služby Azure AD.
    * **CloudAdminCredential**. Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegovaným koncový bod.
    * **AzCredential**. Přihlašovací údaje pro účet správce služby Azure Stack. Použijte stejné přihlašovací údaje, které jste použili k nasazení Azure Stack.

## <a name="next-steps"></a>Další postup

[Nabídka App Service jako PaaS](azure-stack-app-service-overview.md)
