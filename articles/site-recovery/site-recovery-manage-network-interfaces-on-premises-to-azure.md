---
title: Správa síťových adaptérů pro obnovení místních havárií pomocí Azure Site Recovery
description: Popisuje, jak spravovat síťová rozhraní pro místní zotavení po havárii do Azure pomocí Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: harshacs
ms.openlocfilehash: 4dad7f76edf34782131c7c844978763cda53acc7
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068111"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Správa síťových rozhraní virtuálních počítačů pro zotavení po havárii místního prostředí do Azure

Virtuální počítač (VM) v Azure musí mít připojené aspoň jedno síťové rozhraní. Může mít k němu připojené tolik síťových rozhraní, jako podporuje velikost virtuálního počítače.

Ve výchozím nastavení je první síťové rozhraní připojené k virtuálnímu počítači Azure definované jako primární síťové rozhraní. Všechna ostatní síťová rozhraní ve virtuálním počítači jsou sekundární síťová rozhraní. Ve výchozím nastavení se veškerý odchozí provoz z virtuálního počítače odesílá taky IP adresa, která je přiřazená k primární konfiguraci protokolu IP primárního síťového rozhraní.

V místním prostředí můžou mít virtuální počítače nebo servery více síťových rozhraní pro různé sítě v rámci prostředí. Různé sítě se obvykle používají k provádění konkrétních operací, jako jsou například upgrady, údržba a přístup k Internetu. Při migraci nebo převzetí služeb při selhání v Azure z místního prostředí Pamatujte na to, že síťová rozhraní ve stejném virtuálním počítači musí být připojená ke stejné virtuální síti.

Ve výchozím nastavení Azure Site Recovery vytvoří tolik síťových rozhraní na virtuálním počítači Azure, jako jsou připojené k místnímu serveru. Při migraci nebo převzetí služeb při selhání se můžete vyhnout vytváření redundantních síťových rozhraní úpravou nastavení síťového rozhraní v nastavení pro replikovaný virtuální počítač.

## <a name="select-the-target-network"></a>Vyberte cílovou síť.

Pro VMware a fyzické počítače a pro virtuální počítače Hyper-V (bez System Center Virtual Machine Manager) můžete zadat cílovou virtuální síť pro jednotlivé virtuální počítače. U virtuálních počítačů Hyper-V spravovaných pomocí Virtual Machine Manager použijte [mapování sítě](./hyper-v-vmm-network-mapping.md) pro mapování sítí virtuálních počítačů na zdrojovém serveru Virtual Machine Manager a cílové sítě Azure.

1. V části **replikované položky** v Recovery Servicesovém trezoru vyberte jakoukoli replikovanou položku pro přístup k nastavení této replikované položky.

2. Vyberte kartu **výpočty a síť** pro přístup k nastavení sítě pro replikovanou položku.

3. V části **Vlastnosti sítě**vyberte virtuální síť ze seznamu dostupných síťových rozhraní.

    ![Nastavení sítě](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Změna cílové sítě má vliv na všechna síťová rozhraní pro konkrétní virtuální počítač.

U Virtual Machine Managerch cloudů má změna mapování sítě vliv na všechny virtuální počítače a jejich síťová rozhraní.

## <a name="select-the-target-interface-type"></a>Vyberte typ cílového rozhraní.

V části **Síťová rozhraní** v podokně **výpočty a síť** můžete zobrazit a upravit nastavení síťového rozhraní. Můžete také zadat cílový typ síťového rozhraní.

- Pro převzetí služeb při selhání se vyžaduje **primární** síťové rozhraní.
- Všechna ostatní vybraná síťová rozhraní jsou v případě **sekundárních** síťových rozhraní.
- Vyberte **Nepoužívat** k vyloučení síťového rozhraní před vytvořením při převzetí služeb při selhání.

Ve výchozím nastavení, když povolujete replikaci, Site Recovery vybere všechna zjištěná síťová rozhraní na místním serveru. Označuje jednu jako **primární** a všechny ostatní jako **sekundární**. Všechna další rozhraní přidaná na místním serveru jsou označena jako **nepoužívá** se ve výchozím nastavení. Když přidáváte více síťových rozhraní, ujistěte se, že je vybraná správná velikost cílového virtuálního počítače Azure, aby se vešla všechna požadovaná síťová rozhraní.

## <a name="modify-network-interface-settings"></a>Upravit nastavení síťového rozhraní

Můžete upravit podsíť a IP adresu pro síťová rozhraní replikované položky. Pokud není zadaná IP adresa, Site Recovery přiřadí další dostupnou IP adresu z podsítě k síťovému rozhraní při převzetí služeb při selhání.

1. Výběrem libovolného dostupného síťového rozhraní otevřete nastavení síťového rozhraní.

2. Vyberte požadovanou podsíť ze seznamu dostupných podsítí.

3. Zadejte požadovanou IP adresu (podle potřeby).

    ![Nastavení síťového rozhraní](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Výběrem **OK** dokončete úpravy a vraťte se do podokna **výpočty a síť** .

5. Opakujte kroky 1-4 pro další síťová rozhraní.

6. Vyberte **Uložit** a uložte všechny změny.

## <a name="next-steps"></a>Další kroky
  [Přečtěte si další informace](../virtual-network/virtual-network-network-interface-vm.md) o síťových rozhraních pro virtuální počítače Azure.
