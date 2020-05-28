---
title: Synchronizace konfigurace sítě pro Azure App Service
titleSuffix: Azure SQL Managed Instance
description: Tento článek popisuje, jak synchronizovat konfiguraci sítě pro Azure App Service plán hostování s vaší spravovanou instancí Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: 6e4f6789be2faa8d1af0fd75830541f8a9e27a7e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044567"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Synchronizace konfigurace sítě pro Azure App Service plán hostování s použitím spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Může dojít k tomu, že i když jste [aplikaci integrovanou do Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md), nemůžete navázat připojení k spravované instanci SQL. Při aktualizaci nebo synchronizaci sítě může tento problém vyřešit konfigurace sítě pro váš plán služby. 

## <a name="sync-network-configuration"></a>Synchronizace konfigurace sítě 

Provedete to podle těchto kroků:  

1. Přejít do svého plánu App Service Web Apps.

   ![plán služby App Service](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Vyberte **sítě** a pak **pro správu vyberte kliknutím sem**.

   ![Správa plánu služeb](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Vyberte **virtuální síť** a klikněte na **synchronizovat síť**.

   ![synchronizovat síť](./media/azure-app-sync-network-configuration/sync.png)

4. Počkejte na dokončení synchronizace.
  
   ![synchronizace dokončena](./media/azure-app-sync-network-configuration/sync-done.png)

Teď jste připraveni zkusit znovu navázat připojení ke spravované instanci SQL.

## <a name="next-steps"></a>Další kroky

- Informace o konfiguraci virtuální sítě pro spravovanou instanci SQL najdete v tématu [Architektura virtuální sítě spravované instance SQL](connectivity-architecture-overview.md) a [jak nakonfigurovat stávající virtuální síť](vnet-existing-add-subnet.md).
