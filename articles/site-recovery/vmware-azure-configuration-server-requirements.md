---
title: Požadavky na server pro konfiguraci zotavení po havárii společnosti VMware v nástroji Azure Site Recovery
description: Tento článek popisuje podporu a požadavky při nasazování konfiguračního serveru pro zotavení po havárii společnosti VMware do Azure pomocí azure site recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257404"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Požadavky na konfigurační server pro zotavení po havárii společnosti VMware do Azure

Místní konfigurační server nasadíte, když do Azure použijete [Azure Site Recovery](site-recovery-overview.md) pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů.

- Konfigurační server koordinuje komunikaci mezi místním vmware a Azure. Spravuje také replikaci dat.
- [Přečtěte si další informace](vmware-azure-architecture.md) o součástech a procesech konfiguračního serveru.

## <a name="configuration-server-deployment"></a>Nasazení konfiguračního serveru

Pro zotavení po havárii virtuálních počítačů VMware do Azure nasadíte konfigurační server jako virtuální počítač VMware.

- Site Recovery poskytuje šablonu OVA, kterou stáhnete z portálu Azure a importujete do serveru vCenter a nastavíte virtuální počítač konfiguračního serveru.
- Při nasazení konfiguračního serveru pomocí šablony OVA virtuální ho dohledu virtuální ho dohledu automaticky splňuje požadavky uvedené v tomto článku.
- Důrazně doporučujeme nastavit konfigurační server pomocí šablony OVA. Pokud však nastavujete zotavení po havárii pro virtuální počítače VMware a nemůžete použít šablonu OVA, můžete nasadit konfigurační server podle [těchto pokynů .](physical-azure-set-up-source.md)
- Pokud nasazujete konfigurační server pro zotavení po havárii místních fyzických počítačů do Azure, postupujte podle pokynů v [tomto článku](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Další kroky
Nastavte zotavení virtuálních [počítačů VMware](vmware-azure-tutorial.md) do Azure.
