---
title: Migrace virtuálních počítačů s AWS do Azure pomocí Azure Migrate
description: Tento článek popisuje možnosti migrace instancí AWS do Azure a doporučuje Azure Migrate.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 83d8106b9f206da9095c37b6179bdda8001b439c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009054"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrace virtuálních počítačů služby Amazon Web Services (AWS) do Azure

Tento článek popisuje možnosti migrace instancí Amazon Web Services (AWS) do Azure.

## <a name="migrate-with-azure-migrate"></a>Migrace pomocí Azure Migrate

Doporučujeme migrovat instance EC2 AWS do Azure pomocí služby [Azure Migrate](../migrate/migrate-services-overview.md) . Azure Migrate je účelově sestavený pro migraci serveru. Azure Migrate poskytuje centralizované centrum pro zjišťování, posuzování a migraci místních počítačů do Azure.

[Naučte](../migrate/tutorial-migrate-aws-virtual-machines.md) se MIGROVAT instance AWS pomocí Azure Migrate. 


## <a name="migrate-with-site-recovery"></a>Migrace pomocí Site Recovery

Site Recovery by se měly používat jenom pro zotavení po havárii, a ne na migraci.

Pokud již používáte Azure Site Recovery a chcete ho nadále používat pro migraci AWS, postupujte podle kroků, které použijete k nastavení [zotavení po havárii fyzických počítačů](physical-azure-disaster-recovery.md).


> [!NOTE]
> Když spustíte převzetí služeb při selhání pro zotavení po havárii, jako poslední krok potvrdíte převzetí služeb při selhání. Při migraci instancí AWS není možnost **potvrzení** relevantní. Místo toho vyberte možnost **kompletní migrace** . 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si běžné otázky](../migrate/resources-faq.md) týkající se Azure Migrate.
