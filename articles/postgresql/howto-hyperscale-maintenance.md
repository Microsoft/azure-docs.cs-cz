---
title: Azure Database for PostgreSQL – Citus) – Plánovaná údržba – Azure Portal
description: Naučte se konfigurovat nastavení plánované údržby pro Azure Database for PostgreSQL-Citus () z Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 04dd37e3e9abbfbec7badb036802e645cc7732b0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108251"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--hyperscale-citus"></a>Správa nastavení naplánované údržby pro Azure Database for PostgreSQL – Citus (škálování)

V rámci vašeho předplatného Azure můžete zadat možnosti údržby pro každou skupinu serverů Citus (vlastní měřítko). Možnosti zahrnují plán údržby a nastavení oznámení pro nadcházející a dokončené události údržby.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

- [Skupina serverů Azure Database for PostgreSQL-Citus (škálování)](quickstart-create-hyperscale-portal.md)

## <a name="specify-maintenance-schedule-options"></a>Zadat možnosti plánu údržby

1. Na stránce skupiny serverů Citus () klikněte v části **Nastavení** na položku **Údržba** a otevřete možnosti naplánované údržby.
2. Výchozím plánem (spravovaným systémem) je náhodný den v týdnu a 30 minut pro údržbu, který začíná mezi 23:00 a 7am skupinou serverů v [oblasti Azure](https://go.microsoft.com/fwlink/?linkid=2143646). Pokud chcete tento plán přizpůsobit, vyberte možnost **vlastní plán**. Pak můžete vybrat upřednostňovaný den v týdnu a 30 minut pro čas spuštění údržby.

## <a name="notifications-about-scheduled-maintenance-events"></a>Oznámení o plánovaných událostech údržby

Azure Service Health můžete použít k [zobrazení oznámení](../service-health/service-notifications.md) o nadcházející a minulé plánované údržbě na skupině serverů Citus (škálovatelná údržba). Můžete také [nastavit](../service-health/resource-health-alert-monitor-guide.md) výstrahy v Azure Service Health, abyste získali oznámení o událostech údržby.

## <a name="next-steps"></a>Další kroky

* Informace o [plánované údržbě v Azure Database for PostgreSQL – Citus (škálování)](concepts-hyperscale-maintenance.md)
* Lean o [Azure Service Health](../service-health/overview.md)
