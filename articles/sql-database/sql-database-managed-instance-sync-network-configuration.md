---
title: Azure App Service – synchronizace konfigurace sítě
description: Tento článek popisuje, jak synchronizovat konfiguraci sítě pro Azure App Service plán hostování.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: 0065b5c7ddf8e19897f76aeb49300ec7a6809b30
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772365"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synchronizace konfigurace sítě pro plán hostování Azure App Service

Může dojít k tomu, že i když jste [aplikaci integrovanou do Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), nemůžete navázat připojení ke spravované instanci. Jednou z věcí, které můžete vyzkoušet, je aktualizovat konfiguraci sítě pro svůj plán služby.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synchronizace konfigurace sítě pro plán hostování App Service

Provedete to podle těchto kroků:  

1. Přejít do svého plánu App Service Web Apps.

   ![plán služby App Service](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Klikněte na **sítě** a potom klikněte na **tlačítko pro správu**.

   ![Správa plánu služeb](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Vyberte **virtuální síť** a klikněte na **synchronizovat síť**.

   ![synchronizovat síť](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Počkejte na dokončení synchronizace.
  
   ![synchronizace dokončena](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Teď jste připraveni zkusit znovu navázat připojení ke svojí spravované instanci.

## <a name="next-steps"></a>Další kroky

- Informace o konfiguraci virtuální sítě pro spravovanou instanci najdete v tématu [Architektura VNet spravované instance](sql-database-managed-instance-connectivity-architecture.md) a [Jak konfigurovat stávající virtuální síť](sql-database-managed-instance-configure-vnet-subnet.md).
