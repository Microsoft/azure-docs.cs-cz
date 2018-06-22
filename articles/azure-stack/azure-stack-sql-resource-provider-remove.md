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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 150d1c40463aa04527bdd6e356a4c24ef68b02ef
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301894"
---
# <a name="remove-the-sql-resource-provider"></a>Odebrat zprostředkovatele prostředků SQL

Před odebráním poskytovatele prostředků SQL, musíte odebrat všechny závislosti zprostředkovatele. Budete také potřebovat kopii balíček nasazení, který byl použitý k instalaci zprostředkovatele prostředků.

## <a name="to-remove-the-sql-resource-provider"></a>Chcete-li odebrat poskytovatele prostředků SQL

1. Ověřte, že jste odebrali všechny existující SQL zprostředkovatele závislosti prostředků.

   > [!NOTE]
   > Odinstalace zprostředkovatele prostředků SQL bude pokračovat i v případě, že zprostředkovatel prostředků aktuálně používáte závislé prostředky.
  
2. Získat binární kopie poskytovatele prostředků SQL a poté spusťte Self-Extractor extrahujte obsah do dočasného adresáře.

3. Otevřete okno konzoly prostředí PowerShell nové zvýšenými oprávněními a přejděte do adresáře, které jste extrahovali binární soubory zprostředkovatele prostředků SQL.

4. Spusťte skript DeploySqlProvider.ps1 s následujícími parametry:

    - **Odinstalace**. Odebere poskytovatele prostředků a všechny přidružené prostředky.
    - **PrivilegedEndpoint**. IP adresa nebo název DNS privilegované koncového bodu.
    - **CloudAdminCredential**. Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod.
    - **AzCredential**. Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů, které jste použili pro nasazení Azure zásobníku.

## <a name="next-steps"></a>Další postup

[Nabídka aplikační služby jako PaaS](azure-stack-app-service-overview.md)
