---
title: Migrace virtuálních počítačů AWS do Azure pomocí služby Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje migraci virtuálních počítačů s Windows na platformě Amazon Web Services (AWS) do Azure pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dd91e99b45405cca10b9ddc2982674e72ad6bf86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281289"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrace virtuálních počítačů služby Amazon Web Services (AWS) do Azure

Tento článek popisuje možnosti migrace instancí Amazon Web Services (AWS) do Azure.

## <a name="migrate-with-azure-migrate"></a>Migrace pomocí Azure Migrate

K migraci instancí AWS do Azure doporučujeme použít službu [Azure Migrate](../migrate/migrate-services-overview.md) . Azure Migrate poskytuje centralizované centrum pro posuzování a migraci místních počítačů do Azure, použití Azure Migrate, dalších služeb Azure a nástrojů třetích stran.

[Naučte](../migrate/tutorial-migrate-aws-virtual-machines.md) se MIGROVAT instance AWS pomocí Azure Migrate. 


## <a name="migrate-with-site-recovery"></a>Migrace pomocí Site Recovery

Site Recovery by se měly používat jenom pro zotavení po havárii, a ne na migraci.

Pokud již používáte Azure Site Recovery a chcete ho nadále používat pro migraci AWS, postupujte podle kroků, které použijete k nastavení [zotavení po havárii fyzických počítačů](physical-azure-disaster-recovery.md).


> [!NOTE]
> Když spustíte převzetí služeb při selhání pro zotavení po havárii, jako poslední krok potvrdíte převzetí služeb při selhání. Při migraci instancí AWS není možnost **potvrzení** relevantní. Místo toho vyberte možnost **kompletní migrace** . 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si běžné otázky](../migrate/resources-faq.md) týkající se Azure Migrate.
