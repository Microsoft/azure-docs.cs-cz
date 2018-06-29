---
title: Odebrání poskytovatele prostředků MySQL v Azure zásobníku | Microsoft Docs
description: Další informace, odebrání zprostředkovatele prostředků MySQL od nasazení Azure zásobníku.
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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: d3a615e3b92a62709a787d0463dfa3148f14d07e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085779"
---
# <a name="remove-the-mysql-resource-provider"></a>Odebrat zprostředkovatele prostředků MySQL

Před odebráním poskytovatele prostředků MySQL, musíte odebrat všechny závislosti zprostředkovatele. Budete také potřebovat kopii balíček nasazení, který byl použitý k instalaci zprostředkovatele prostředků.

## <a name="dependency-cleanup"></a>Vyčištění závislostí

Existuje několik úloh čištění udělat předtím, než spustíte DeployMySqlProvider.ps1 skript, který chcete odebrat poskytovatele prostředků.

Klienti jsou zodpovědní za následující úlohy čištění:

* Odstraňte všechny své databáze od zprostředkovatele prostředků. (Odstranit databáze klienta neodstraní data.)
* Zrušení registrace obor názvů zprostředkovatele.

Správce zodpovídá za tyto úlohy čištění:

* Odstraní z databáze MySQL adaptéru hostitelskými servery.
* Odstraní všechny plány, které odkazují na adaptér MySQL.
* Odstraní všechny kvóty, které jsou spojeny s adaptérem MySQL.

## <a name="to-remove-the-mysql-resource-provider"></a>Chcete-li odebrat poskytovatele prostředků MySQL

1. Ověřte, že jste odebrali všechny existující MySQL zprostředkovatele závislosti prostředků.

   >[!NOTE]
   >Odinstalace zprostředkovatele prostředků MySQL bude pokračovat i v případě, že zprostředkovatel prostředků aktuálně používáte závislé prostředky.
  
2. Získat kopii poskytovatele prostředků MySQL binární a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře.
3. Získat binární kopie poskytovatele prostředků SQL a poté spusťte Self-Extractor extrahujte obsah do dočasného adresáře.
4. Otevřete okno konzoly prostředí PowerShell nové zvýšenými oprávněními a přejděte do adresáře, které jste extrahovali binární soubory zprostředkovatele prostředků MySQL.
5. Spusťte skript DeployMySqlProvider.ps1 s následujícími parametry:
    - **Odinstalace**. Odebere poskytovatele prostředků a všechny přidružené prostředky.
    - **PrivilegedEndpoint**. IP adresa nebo název DNS privilegované koncového bodu.
    - **CloudAdminCredential**. Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod.
    - **DirectoryTenantID**
    - **AzCredential**. Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů, které jste použili pro nasazení Azure zásobníku.

## <a name="next-steps"></a>Další postup

[Nabídka aplikační služby jako PaaS](azure-stack-app-service-overview.md)
