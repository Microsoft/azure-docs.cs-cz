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
ms.date: 11/14/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: b7af23ccdd379aac9959bb9993fc1781a44e705e
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684022"
---
# <a name="remove-the-sql-resource-provider"></a>Odebrat poskytovatele prostředků SQL

Před odebráním poskytovatele prostředků SQL, musíte odebrat všechny závislosti zprostředkovatele. Budete také potřebovat kopii balíčku pro nasazení, který byl použit k instalaci zprostředkovatele prostředků.

  |Minimální verze služby Azure Stack|Verze poskytovatele prostředků SQL|
  |-----|-----|
  |Verze. 1808 (1.1808.0.97)|[SQL RP verze 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
  |Verzi 1804 (1.0.180513.1)|[SQL RP verze 1.1.24.0](https://aka.ms/azurestacksqlrp11240)
  |     |     |

## <a name="dependency-cleanup"></a>Vyčištění závislostí

Následuje několik úloh vyčištění před spuštěním skriptu DeploySqlProvider.ps1 odebrat poskytovatele prostředků.

Azure Stack tenanta uživatelé nesou zodpovědnost za tyto úlohy čištění:

* Odstraňte všechny své databáze od zprostředkovatele prostředků. (Při odstranění databáze tenantů neodstraní data.)
* Zrušit registraci obor názvů poskytovatele.

Operátor Azure stacku zodpovídá za tyto úlohy čištění:

* Odstraní hostitelské servery z adaptéru MySQL.
* Odstraní všechny plány, které odkazují na adaptér MySQL.
* Odstraní všechny kvóty, které jsou spojené s adaptérem MySQL.

## <a name="to-remove-the-sql-resource-provider"></a>Chcete-li odebrat poskytovatele prostředků SQL

1. Ověřte, že jste odebrali všechny existující SQL poskytovatele závislosti prostředků.

   > [!NOTE]
   > Odinstalace poskytovatele prostředků SQL bude pokračovat i v případě poskytovatele prostředků aktuálně používají závislé prostředky.
  
2. Získat binární kopii tohoto poskytovatele prostředků SQL a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře.

3. Otevřete okno konzole Powershellu s nová se zvýšenými oprávněními a přejděte do adresáře, které jste extrahovali binární soubory poskytovatele prostředků SQL.

4. Spusťte skript DeploySqlProvider.ps1 s následujícími parametry:

    * **Odinstalujte**. Odebere poskytovatele prostředků a všechny související prostředky.
    * **PrivilegedEndpoint**. IP adresa nebo název DNS privileged koncového bodu.
    * **AzureEnvironment**. Prostředí Azure používá pro nasazení Azure Stack. Vyžaduje se jenom pro nasazení služby Azure AD.
    * **CloudAdminCredential**. Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegovaným koncový bod.
    * **AzCredential**. Přihlašovací údaje pro účet správce služby Azure Stack. Použijte stejné přihlašovací údaje, které jste použili k nasazení Azure Stack.

## <a name="next-steps"></a>Další postup

[Nabídka App Service jako PaaS](azure-stack-app-service-overview.md)
