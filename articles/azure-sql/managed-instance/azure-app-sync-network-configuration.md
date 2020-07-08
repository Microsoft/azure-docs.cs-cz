---
title: Synchronizace konfigurace sítě pro Azure App Service
titleSuffix: Azure SQL Managed Instance
description: Tento článek popisuje, jak synchronizovat konfiguraci sítě pro Azure App Service plán hostování s vaší spravovanou instancí Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: a0192f204fdb0797d98947a174bf6d669034b666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695449"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Synchronizace konfigurace sítě pro Azure App Service plán hostování s použitím spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Může dojít k tomu, že i když jste [aplikaci integrovanou do Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md), nemůžete navázat připojení k spravované instanci SQL. Při aktualizaci nebo synchronizaci sítě může tento problém vyřešit konfigurace sítě pro váš plán služby. 

## <a name="sync-network-configuration"></a>Synchronizace konfigurace sítě 

Provedete to podle těchto kroků:  

1. Přejít do svého plánu App Service Web Apps.

   ![Snímek obrazovky s plánem App Service](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Vyberte **sítě** a pak **pro správu vyberte kliknutím sem**.

   ![Snímek obrazovky s plánem správy služeb](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Vyberte **virtuální síť** a klikněte na **synchronizovat síť**.

   ![Snímek obrazovky se synchronizací sítě](./media/azure-app-sync-network-configuration/sync.png)

4. Počkejte na dokončení synchronizace.
  
   ![Snímek obrazovky s dokončenou synchronizací](./media/azure-app-sync-network-configuration/sync-done.png)

Teď jste připraveni zkusit znovu navázat připojení ke spravované instanci SQL.

## <a name="next-steps"></a>Další kroky

- Informace o konfiguraci virtuální sítě pro spravovanou instanci SQL najdete v tématu [Architektura virtuální sítě spravované instance SQL](connectivity-architecture-overview.md) a [jak nakonfigurovat stávající virtuální síť](vnet-existing-add-subnet.md).
