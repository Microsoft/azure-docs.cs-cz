---
title: Odstranění projektu Azure Migrate
description: V tomto článku se dozvíte, jak odstranit Azure Migrate projekt pomocí Azure Portal.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: bfb4db5d3ebf69f9c7f552c175d33a8b817d1562
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595134"
---
# <a name="delete-an-azure-migrate-project"></a>Odstranění projektu Azure Migrate

Tento článek popisuje, jak odstranit [Azure Migrate](./migrate-services-overview.md) projekt.


## <a name="before-you-start"></a>Než začnete

Před odstraněním projektu:

- Po odstranění projektu se odstraní projekt a zjištěná metadata počítače.
- Pokud jste připojili Log Analytics pracovní prostor k nástroji pro vyhodnocení závislostí, rozhodněte se, zda chcete odstranit pracovní prostor. 
    - Pracovní prostor se automaticky neodstraní. Odstraňte ji ručně.
    - Ověřte, že se pracovní prostor používá pro předtím, než ho odstraníte. Stejný pracovní prostor Log Analytics lze použít pro více scénářů.
    - Před odstraněním projektu můžete v části pracovní prostor OMS najít odkaz na pracovní prostor v **Azure Migrate-servery**  >  **Azure Migrate – posouzení serveru**. 
    - Pokud chcete odstranit pracovní prostor po odstranění projektu, najděte pracovní prostor v příslušné skupině prostředků a postupujte podle [těchto pokynů](../azure-monitor/logs/delete-workspace.md).


## <a name="delete-a-project"></a>Odstranit projekt


1. V Azure Portal otevřete skupinu prostředků, ve které byl projekt vytvořen.
2. Na stránce skupina prostředků vyberte **Zobrazit skryté typy**.
3. Vyberte projekt a související prostředky, které chcete odstranit.
    - Typ prostředku pro Azure Migrate projekty je **Microsoft. migruje/migrateprojects**.
    - V další části si projděte informace o prostředcích vytvořených pro zjišťování, posouzení a migraci v projektu Azure Migrate.
    - Pokud skupina prostředků obsahuje pouze projekt Azure Migrate, můžete odstranit celou skupinu prostředků.
    - Pokud chcete odstranit projekt z předchozí verze Azure Migrate, jsou tyto kroky stejné. Typ prostředku pro tyto projekty je **projekt migrace**.


## <a name="created-resources"></a>Vytvořené prostředky

Tyto tabulky shrnují prostředky vytvořené pro zjišťování, posuzování a migraci v projektu Azure Migrate.

> [!NOTE]
> Odstraňte Trezor klíčů opatrně, protože může obsahovat bezpečnostní klíče.

### <a name="vmwarephysical-server"></a>VMware / fyzický server

**Prostředek** | **Typ**
--- | ---
"Zařízení" KV | Trezor klíčů
Web "zařízení" | Microsoft. OffAzure/VMwareSites
Názevprojektu | Microsoft. migruje/migrateprojects
Projekt ProjectName | Microsoft. migruje/assessmentProjects
"ProjectName" rsvault | Trezor služby Recovery Services
"ProjectName"-MigrateVault-* | Trezor služby Recovery Services
migrateappligwsa* | Účet úložiště
migrateapplilsa* | Účet úložiště
migrateapplicsa* | Účet úložiště
migrateapplikv* | Trezor klíčů
migrateapplisbns16041 | Service Bus Namespace

### <a name="hyper-v-vm"></a>Virtuální počítač s technologií Hyper-V 

**Prostředek** | **Typ**
--- | ---
Názevprojektu | Microsoft. migruje/migrateprojects
Projekt ProjectName | Microsoft. migruje/assessmentProjects
HyperV * KV | Trezor klíčů
Hyper-v Web | Microsoft. OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Trezor služby Recovery Services


## <a name="next-steps"></a>Další kroky

Přečtěte si, jak přidat další nástroje pro [posouzení](how-to-assess.md) a [migraci](how-to-migrate.md) . 
