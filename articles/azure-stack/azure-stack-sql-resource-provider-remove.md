---
title: Odebrání poskytovatele prostředků SQL v Azure zásobníku | Microsoft Docs
description: Zjistěte, jak odebrat poskytovatele prostředků SQL ze zásobníku Azure nasazení.
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
ms.openlocfilehash: b73deebb10d0c81a06df9cd192eaa2ef28de744d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083035"
---
# <a name="remove-the-sql-resource-provider"></a>Odebrat zprostředkovatele prostředků SQL

Před odebráním poskytovatele prostředků SQL, musíte odebrat všechny závislosti zprostředkovatele. Budete také potřebovat kopii balíček nasazení, který byl použitý k instalaci zprostředkovatele prostředků.

Různé úlohy čištění provést před spuštěním _DeploySqlProvider.ps1_ skript pro odebrání zprostředkovatele prostředků.
Klienti jsou zodpovědní za následující úlohy čištění:

* Odstraňte všechny své databáze od zprostředkovatele prostředků. (Odstranit databáze klienta neodstraní data.)
* Zrušení registrace obor názvů zprostředkovatele prostředků.

Správce zodpovídá za tyto úlohy čištění:

* Odstraní hostitelskými servery od zprostředkovatele prostředků SQL.
* Odstraní všechny plány, které odkazují poskytovatele prostředků SQL.
* Odstraní všechny kvóty, které jsou spojeny s poskytovatelem prostředků SQL.

## <a name="to-remove-the-sql-resource-provider"></a>Chcete-li odebrat poskytovatele prostředků SQL

1. Ověřte, že jste odebrali všechny existující SQL zprostředkovatele závislosti prostředků.

   > [!NOTE]
   > Odinstalace zprostředkovatele prostředků SQL bude pokračovat i v případě, že zprostředkovatel prostředků aktuálně používáte závislé prostředky.
  
2. Získat binární kopie poskytovatele prostředků SQL a poté spusťte Self-Extractor extrahujte obsah do dočasného adresáře.

3. Otevřete okno konzoly prostředí PowerShell nové zvýšenými oprávněními a přejděte do adresáře, které jste extrahovali binární soubory zprostředkovatele prostředků SQL.

4. Spusťte skript DeploySqlProvider.ps1 s následujícími parametry:

    * **Odinstalace**. Odebere poskytovatele prostředků a všechny přidružené prostředky.
    * **PrivilegedEndpoint**. IP adresa nebo název DNS privilegované koncového bodu.
    * **CloudAdminCredential**. Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod.
    * **AzCredential**. Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů, které jste použili pro nasazení Azure zásobníku.

## <a name="next-steps"></a>Další postup

[Nabídka aplikační služby jako PaaS](azure-stack-app-service-overview.md)
