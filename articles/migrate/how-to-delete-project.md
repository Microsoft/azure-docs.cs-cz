---
title: Odstranění projektu Azure Migrate
description: V tomto článku se dozvíte, jak odstranit Azure Migrate projekt pomocí Azure Portal.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: 8c94bb23f5d514fef5cdacb855657efdf5219631
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714735"
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

### <a name="projects-with-public-endpoint-connectivity"></a>Projekty s připojením k veřejnému koncovému bodu

#### <a name="vmwarephysical-server"></a>VMware / fyzický server

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
migrateapplisbns* | Service Bus Namespace

#### <a name="hyper-v-vm"></a>Virtuální počítač s technologií Hyper-V

**Prostředek** | **Typ**
--- | ---
Názevprojektu | Microsoft. migruje/migrateprojects
Projekt ProjectName | Microsoft. migruje/assessmentProjects
HyperV * KV | Trezor klíčů
Hyper-v Web | Microsoft. OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Trezor služby Recovery Services

<br/>
Následující tabulka shrnuje prostředky vytvořené nástrojem Azure Migrate k zjišťování, vyhodnocení a migraci serverů přes privátní síť pomocí [privátního propojení Azure](./how-to-use-azure-migrate-with-private-endpoints.md).

### <a name="projects-with-private-endpoint-connectivity"></a>Projekty s připojením privátního koncového bodu

#### <a name="vmware-vms---agentless-migrations"></a>Virtuální počítače VMware – migrace bez agentů

**Typ** | **Prostředek** | **Soukromý koncový bod <br/>** |
--- | --- | ---
Microsoft. migruje/migrateprojects | Názevprojektu | "ProjectName" \* PE 
Lokalita zjišťování (hlavní lokalita) | "ProjectName" * mastersite | "ProjectName" \* mastersite \* PE 
Microsoft. migruje/assessmentProjects | "Zařízení" * projekt | "Zařízení" \* projektu \* PE 
Trezor klíčů | "ProjectName" * KV | "ProjectName" \* KV \* PE
Microsoft. OffAzure/VMwareSites | "Zařízení" * Web | NA
Trezor služby Recovery Services | "Zařízení" * trezor | NA
Účet úložiště | "Zařízení" * USA | "Zařízení" – \* USA – \* PE
Trezor služby Recovery Services | "ProjectName"-MigrateVault-* | NA
Účet úložiště | migrateappligwsa* | NA
Účet úložiště | migrateapplilsa* | NA
Trezor klíčů | migrateapplikv* | NA
Service Bus Namespace | migrateapplisbns* | NA

#### <a name="hyper-v-vms"></a>Virtuální počítače Hyper-V 

**Typ** | **Prostředek** | **Soukromý koncový bod <br/>** |
--- | --- | ---
Microsoft. migruje/migrateprojects | Názevprojektu | "ProjectName" \* PE 
Lokalita zjišťování (hlavní lokalita) | "ProjectName" * mastersite | "ProjectName" \* mastersite \* PE 
Microsoft. migruje/assessmentProjects | "Zařízení" * projekt | "Zařízení" \* projektu \* PE 
Trezor klíčů | "ProjectName" * KV | "ProjectName" \* KV \* PE
Microsoft. OffAzure/HyperVSites | "Zařízení" * Web | NA
Trezor služby Recovery Services | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-* PE

#### <a name="physical-servers--aws-vms--gcp-vms"></a>Fyzické servery/virtuální počítače s AWS/virtuální počítače s GCP 

**Typ** | **Prostředek** | **Soukromý koncový bod <br/>** |
--- | --- | ---
Microsoft. migruje/migrateprojects | Názevprojektu | "ProjectName" \* PE 
Lokalita zjišťování (hlavní lokalita) | "ProjectName" * mastersite | "ProjectName" \* mastersite \* PE 
Microsoft. migruje/assessmentProjects | "Zařízení" * projekt | "Zařízení" \* projektu \* PE 
Trezor klíčů | "ProjectName" * KV | "ProjectName" \* KV \* PE
Microsoft. OffAzure/serversites | "Zařízení" * Web | NA
Trezor služby Recovery Services | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-* PE


## <a name="next-steps"></a>Další kroky

Přečtěte si, jak přidat další nástroje pro [posouzení](how-to-assess.md) a [migraci](how-to-migrate.md) . 
