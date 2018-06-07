---
title: Azure App Service – konfigurace sítě synchronizace | Microsoft Docs
description: Tento článek popisuje postup synchronizace konfiguraci sítě pro plán hostování služby Azure App Service.
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: ea8f4aae0324e201def6b9b6cd33b0e79042ebbe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647965"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synchronizace konfigurace sítí pro plán hostování služby Azure App Service

Se může stát, že i když jste [integrované aplikace s virtuální síť Azure](../app-service/web-sites-integrate-with-vnet.md), nelze navázat připojení k spravované Instance. Je jednou z věcí, které můžete použít k aktualizaci konfigurace sítí pro plán služby. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Konfigurace sítě synchronizace pro plán hostování služby App Service

Provedete to podle těchto kroků:  

1. Přejděte na webové aplikace plán služby App Service.
 
   ![plán služby App Service](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Klikněte na tlačítko **sítě** a pak klikněte na **chcete spravovat, klikněte sem**.
 
   ![Spravovat plán služby](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Vyberte vaše **VNet** a klikněte na tlačítko **synchronizace sítě**. 
 
   ![synchronizace sítě](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Počkejte, dokud probíhá synchronizace.
  
   ![provádí synchronizaci](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Nyní jste připraveni pokusí znovu navázat připojení k vaší spravované Instance.

## <a name="next-steps"></a>Další postup

- Informace o konfiguraci virtuální sítě pro spravované Instance najdete v tématu [konfiguraci spravovat virtuální sítě Instance](sql-database-managed-instance-vnet-configuration.md).
