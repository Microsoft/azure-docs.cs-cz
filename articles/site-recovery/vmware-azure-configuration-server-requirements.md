---
title: Zotavení po havárii VMware – požadavky na konfigurační server v Azure Site Recovery
description: Tento článek popisuje podporu a požadavky při nasazování konfiguračního serveru pro zotavení po havárii VMware do Azure pomocí Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997800"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Požadavky na konfigurační server pro zotavení po havárii VMware do Azure

Místní konfigurační server nasadíte při použití [Azure Site Recovery](site-recovery-overview.md) k zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure.

- Konfigurační server koordinuje komunikaci mezi místními VMware a Azure. Také spravuje replikaci dat.
- [Přečtěte si další informace](vmware-azure-architecture.md) o součástech a procesech konfiguračního serveru.

## <a name="configuration-server-deployment"></a>Nasazení konfiguračního serveru

Pro zotavení po havárii virtuálních počítačů VMware do Azure nasadíte konfigurační server jako virtuální počítač VMware.

- Site Recovery poskytuje šablonu vajíček, kterou stáhnete z Azure Portal a naimportujte do vCenter Server k nastavení virtuálního počítače konfiguračního serveru.
- Když nasadíte konfigurační server pomocí šablony vajíček, virtuální počítač automaticky splňuje požadavky uvedené v tomto článku.
- Důrazně doporučujeme, abyste nastavili konfigurační server pomocí šablony vajíček. Pokud však nastavujete zotavení po havárii pro virtuální počítače VMware a nemůžete použít šablonu vajíček, můžete konfigurační server nasadit pomocí uvedených [pokynů](physical-azure-set-up-source.md).
- Pokud nasazujete konfigurační server pro zotavení po havárii místních fyzických počítačů do Azure, postupujte podle pokynů v [tomto článku](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Další kroky
Nastavte zotavení po havárii [virtuálních počítačů VMware](vmware-azure-tutorial.md) do Azure.
