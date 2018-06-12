---
title: Odebrání poskytovatele prostředků SQL v Azure zásobníku | Microsoft Docs
description: Zjistěte, jak můžete odebrat poskytovatele prostředků SQL z vašeho nasazení zásobník Azure.
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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 9f90201cad0f74923460c2f25eff4de98dc6690a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294776"
---
# <a name="removing-the-mysql-resource-provider"></a>Odebrání poskytovatele prostředků MySQL  
Před odebráním poskytovatele prostředků SQL, je nutné nejprve odebrat všechny závislosti.

## <a name="remove-the-mysql-resource-provider"></a>Odebrat zprostředkovatele prostředků MySQL 

1. Ověřte, že jste odstranili všechny stávající závislosti zprostředkovatele prostředků SQL.

  > [!NOTE]
  > Odinstalace zprostředkovatele prostředků SQL bude pokračovat i v případě, že zprostředkovatel prostředků aktuálně používáte závislé prostředky. 
  
2. Ujistěte se, že máte původní balíček nasazení, který jste stáhli pro tuto verzi adaptér zprostředkovatele prostředků SQL.
3. Spusťte znovu skript nasazení s následujícími parametry:
    - Použití-odinstalovat parametr
    - IP adresa nebo název DNS privilegované koncového bodu.
    - Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod.
    - Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů jako používat pro nasazování Azure zásobníku.

## <a name="next-steps"></a>Další postup
[Nabídka aplikační služby jako PaaS](azure-stack-app-service-overview.md)
