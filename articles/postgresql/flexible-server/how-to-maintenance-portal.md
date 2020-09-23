---
title: Azure Database for PostgreSQL – flexibilní Server (Preview) – Plánovaná údržba – Azure Portal
description: Naučte se konfigurovat nastavení plánované údržby pro Azure Database for PostgreSQL flexibilní Server z Azure Portal.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 5b06120437555ec71bc431cb1429120cb5e1987f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935925"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--flexible-server"></a>Spravovat nastavení naplánované údržby pro Azure Database for PostgreSQL – flexibilní Server
 
Můžete zadat možnosti údržby pro každý flexibilní Server v rámci vašeho předplatného Azure. Možnosti zahrnují plán údržby a nastavení oznámení pro nadcházející a dokončené události údržby.

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto průvodce budete potřebovat:
- [Azure Database for PostgreSQL – flexibilní Server](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>Zadat možnosti plánu údržby
 
1. Na stránce serveru PostgreSQL v části **Nastavení** vyberte **Údržba** a otevřete možnosti naplánované údržby.
2. Výchozím plánem (spravovaným systémem) je náhodný den v týdnu a 60 minutové okno pro údržbu, které začíná mezi 23:00 a 7am místního serveru. Pokud chcete tento plán přizpůsobit, vyberte možnost **vlastní plán**. Pak můžete vybrat upřednostňovaný den v týdnu a okno 60 minut pro čas spuštění údržby.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Oznámení o plánovaných událostech údržby
 
Pomocí Azure Service Health můžete [Zobrazit oznámení](/azure/service-health/service-notifications.md) o nadcházející a prováděné plánované údržbě na flexibilním serveru. Můžete také [nastavit](/azure/service-health/resource-health-alert-monitor-guide.md) výstrahy v Azure Service Health, abyste získali oznámení o událostech údržby.
 
## <a name="next-steps"></a>Další kroky  
 
* Další informace o [plánované údržbě v Azure Database for PostgreSQL – flexibilní Server](concepts-maintenance.md)
* Lean o [Azure Service Health](/azure/service-health/overview.md)
