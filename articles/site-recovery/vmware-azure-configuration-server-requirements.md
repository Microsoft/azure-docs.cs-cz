---
title: Zotavení po havárii VMware – požadavky na konfigurační server v Azure Site Recovery
description: Tento článek popisuje podporu a požadavky při nasazování konfiguračního serveru pro zotavení po havárii VMware do Azure pomocí Azure Site Recovery
ms.service: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: aec08aa4f18c69195be91b7544b74fee100f6f6b
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580180"
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
