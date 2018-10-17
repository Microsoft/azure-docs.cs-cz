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
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: f5aa67ad0588e3f42e68056c8ffca97767975e8b
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49361477"
---
# <a name="remove-the-sql-resource-provider"></a>Odebrat poskytovatele prostředků SQL

Před odebráním poskytovatele prostředků SQL, musíte odebrat všechny závislosti zprostředkovatele. Budete také potřebovat kopii balíčku pro nasazení, který byl použit k instalaci zprostředkovatele prostředků.

Několik úkolů čištění udělat předtím, než spustíte _DeploySqlProvider.ps1_ skript pro odebrání poskytovatele prostředků.
Klienti jsou zodpovědné za tyto úlohy čištění:

* Odstraňte všechny své databáze od zprostředkovatele prostředků. (Při odstranění databáze tenantů neodstraní data.)
* Zrušit registraci obor názvů zprostředkovatele prostředků.

Správce zodpovídá za tyto úlohy čištění:

* Odstraní hostitelské servery z poskytovatele prostředků SQL.
* Odstraní všechny plány, které odkazují na poskytovatele prostředků SQL.
* Odstraní všechny kvóty, které jsou spojeny s poskytovateli prostředků SQL.

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
