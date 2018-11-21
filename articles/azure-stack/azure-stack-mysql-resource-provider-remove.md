---
title: Odebrání poskytovatele prostředků MySQL ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak můžete odebrat poskytovatele prostředků MySQL z nasazení služby Azure Stack.
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
ms.openlocfilehash: c3dbecfcaf40a85c57b9f795d7f2d9b76d27c195
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274065"
---
# <a name="remove-the-mysql-resource-provider"></a>Odebrat poskytovatele prostředků MySQL

Před odebráním poskytovatele prostředků MySQL, musíte odebrat všechny závislosti zprostředkovatele. Budete také potřebovat kopii balíčku pro nasazení, který byl použit k instalaci zprostředkovatele prostředků.

> [!NOTE]
> Můžete najít odkazy ke stažení pro prostředek zprostředkovatele instalační programy v [nasazení požadavky na poskytovatele prostředků](.\azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Odebrání poskytovatele prostředků MySQL nedojde k odstranění databáze tenantů z hostitelské servery.

## <a name="dependency-cleanup"></a>Vyčištění závislostí

Následuje několik úloh vyčištění před spuštěním skriptu DeployMySqlProvider.ps1 odebrat poskytovatele prostředků.

Operátor Azure stacku zodpovídá za tyto úlohy čištění:

* Odstraňte všechny plány, které odkazují na adaptér MySQL.
* Odstraňte všechny kvóty, které jsou spojené s adaptérem MySQL.

## <a name="to-remove-the-mysql-resource-provider"></a>Chcete-li odebrat poskytovatele prostředků MySQL

1. Ověřte, že jste odebrali všechny existující MySQL zprostředkovatele závislosti prostředků.

   > [!NOTE]
   > Odinstalace poskytovatele prostředků MySQL bude pokračovat i v případě poskytovatele prostředků aktuálně používají závislé prostředky.
  
2. Získat kopii balíčku instalace poskytovatele prostředků MySQL a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře.
3. Otevřete okno konzole Powershellu s nová se zvýšenými oprávněními a přejděte do adresáře, které jste extrahovali instalační soubory poskytovatele prostředků MySQL.
4. Spusťte skript DeployMySqlProvider.ps1 s následujícími parametry:
    - **Odinstalujte**. Odebere poskytovatele prostředků a všechny související prostředky.
    - **PrivilegedEndpoint**. IP adresa nebo název DNS privileged koncového bodu.
    - **AzureEnvironment**. Prostředí Azure používá pro nasazení Azure Stack. Vyžaduje se jenom pro nasazení služby Azure AD.
    - **CloudAdminCredential**. Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegovaným koncový bod.
    - **DirectoryTenantID**
    - **AzCredential**. Přihlašovací údaje pro účet správce služby Azure Stack. Použijte stejné přihlašovací údaje, které jste použili k nasazení Azure Stack.

## <a name="next-steps"></a>Další postup

[Nabídka App Service jako PaaS](azure-stack-app-service-overview.md)
