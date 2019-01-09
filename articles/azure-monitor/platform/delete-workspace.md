---
title: Odstranění pracovního prostoru Azure Log Analytics | Dokumentace Microsoftu
description: Zjistěte, jak odstranění pracovního prostoru Log Analytics, pokud jste vytvořili v rámci služby předplacené osobní nebo restrukturalizaci pracovního prostoru modelu.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
ms.openlocfilehash: 9c62f4c58742c2c3247ff19b76575d1ca11499cb
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101629"
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Odstranění pracovního prostoru Azure Log Analytics pomocí webu Azure portal
Tento článek ukazuje, jak pomocí webu Azure portal k odstranění pracovního prostoru Log Analytics, která může už nepotřebujete. 

## <a name="to-delete-a-workspace"></a>Odstranění pracovního prostoru 
Když odstraníte pracovní prostor Log Analytics, všechna data související s do vašeho pracovního prostoru odstraní ze služby do 30 dní.  Chcete při odstranění pracovního prostoru, protože může být důležitá data a konfiguraci, která může mít negativní vliv na servisní operace. postupujte opatrně. Vezměte v úvahu dalšími službami Azure a zdroje, které ukládají data v Log Analytics, jako například:

* Application Insights
* Azure Security Center
* Azure Automation
* Agenti na virtuálních počítačích Windows a Linux
* Agenty používané ve Windows a Linuxu počítačů ve vašem prostředí
* System Center Operations Manager
* Řešení pro správu 

Všichni agenti a skupiny pro správu System Center Operations Manager nakonfigurovat tak, aby sestavy do pracovního prostoru nadále ve stavu osamocený.  Jaké agenty řešení, zásob a další služby Azure jsou integrované s pracovním prostorem předtím, než budete pokračovat.   
 
Pokud jste správce a k pracovnímu prostoru bylo přidruženo víc uživatelů, přidružení těchto uživatelů s pracovním prostorem se přeruší. Pokud byli tito uživatelé přidruženi s jinými pracovními prostory, můžou pokračovat v používání služby Log Analytics s těmito prostory. Ale pokud nejsou přidruženi s jinými pracovními prostory pak potřebují vytvořit pracovní prostor používat Log Analytics. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
3. V podokně předplatná Log Analytics vyberte pracovní prostor a pak klikněte na tlačítko **odstranit** z horní části podokna uprostřed.<br><br> ![V podokně vlastností pracovního prostoru s možností odstranit](media/delete-workspace/log-analytics-delete-workspace.png)<br>  
4. Jakmile se zobrazí v okně zprávy potvrzení, s výzvou k potvrzení odstranění pracovního prostoru, klikněte na tlačítko **Ano**.<br><br> ![Potvrzení odstranění pracovního prostoru](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

