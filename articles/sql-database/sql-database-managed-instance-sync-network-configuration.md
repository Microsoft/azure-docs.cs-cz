---
title: Azure App Service – synchronizace konfigurace sítě
description: Tento článek popisuje, jak synchronizovat konfiguraci sítě pro Azure App Service plán hostování.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: e7263d6a7716caf9f53e8496c6fb02b6d17b5509
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687899"
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
