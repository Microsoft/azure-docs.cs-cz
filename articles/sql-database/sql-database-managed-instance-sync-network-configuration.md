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
ms.openlocfilehash: e488fc7e568fc94f9a0744c12122b605f37f8152
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339667"
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

- Informace o konfiguraci virtuální sítě pro Managed Instance najdete v tématu [Managed Instance VNet architektura](sql-database-managed-instance-connectivity-architecture.md) a [konfigurace stávající virtuální síť](sql-database-managed-instance-configure-vnet-subnet.md).
