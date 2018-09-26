---
title: Azure App Service – synchronizace konfigurace sítě | Dokumentace Microsoftu
description: Tento článek popisuje, jak synchronizovat konfiguraci sítě pro plán hostování služby Azure App Service.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 03/07/2018
ms.openlocfilehash: d5de908166e8de1d45a36f97aee8934653e59623
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163154"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synchronizace konfigurace sítí pro plán hostování služby Azure App Service

To může stát, že i když jste [vaší aplikace integrované s Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), nelze navázat připojení k Managed Instance. Jednou z věcí, které můžete zkusit se aktualizovat konfiguraci sítě pro váš plán služby. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synchronizace konfigurace sítě pro plán hostování služby App Service

Provedete to podle těchto kroků:  

1. Přejděte na web apps plán služby App Service.
 
   ![plán služby App Service](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Klikněte na tlačítko **sítě** a potom klikněte na tlačítko **kliknutím sem můžete spravovat**.
 
   ![Správa plánu služby](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Vyberte vaše **VNet** a klikněte na tlačítko **synchronizovat síť**. 
 
   ![Synchronizovat síť](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Počkejte na dokončení synchronizace.
  
   ![synchronizace provádí](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Nyní jste připraveni pokusí znovu navázat připojení k Managed Instance.

## <a name="next-steps"></a>Další postup

- Informace o konfiguraci virtuální sítě pro Managed Instance najdete v tématu [konfigurace připojení typu VNet Managed Instance](sql-database-managed-instance-vnet-configuration.md).
