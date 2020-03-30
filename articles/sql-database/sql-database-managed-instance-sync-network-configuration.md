---
title: Služba Azure App Service – konfigurace synchronizační sítě
description: Tento článek popisuje, jak synchronizovat konfiguraci sítě pro plán hostování služby Azure App Service.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73687899"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synchronizace konfigurace sítě pro plán hostování služby Azure App Service

Může se stát, že i když jste [aplikaci integrovali do virtuální sítě Azure](../app-service/web-sites-integrate-with-vnet.md), nemůžete navázat připojení ke spravované instanci. Jedna věc, kterou můžete zkusit, je aktualizovat konfiguraci sítě pro váš plán služeb.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synchronizace konfigurace sítě pro plán hostování služby App Service

Provedete to podle těchto kroků:  

1. Přejděte do plánu služby App Service pro webové aplikace.

   ![plán služeb aplikace](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Klepněte na **položku Síť** a potom klepnutím **na tlačítko Klikněte sem pro příkaz Manage**.

   ![správa servisního plánu](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Vyberte **virtuální síť** a klikněte na **Synchronizovat síť**.

   ![synchronizační síť](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Počkejte, až bude synchronizace hotová.
  
   ![synchronizace byla provedena](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Nyní jste připraveni pokusit se obnovit připojení ke spravované instanci.

## <a name="next-steps"></a>Další kroky

- Informace o konfiguraci virtuální sítě pro spravovanou instanci najdete v tématu [Architektura virtuální sítě spravované instance](sql-database-managed-instance-connectivity-architecture.md) a Jak [nakonfigurovat existující virtuální síť](sql-database-managed-instance-configure-vnet-subnet.md).
