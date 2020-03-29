---
title: Správa síťových adaptérů pro místní zotavení po havárii pomocí Azure Site Recovery
description: Popisuje, jak spravovat síťová rozhraní pro místní zotavení po havárii do Azure pomocí Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 2a4752b501e40f9e8a4f3bc82cb2533c11f9e526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954589"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Správa síťových rozhraní virtuálních montovny pro místní zotavení po havárii do Azure

Virtuální počítač (VM) v Azure musí mít k němu připojeno alespoň jedno síťové rozhraní. Může mít tolik síťových rozhraní, jako podporuje velikost virtuálního počítače.

Ve výchozím nastavení je první síťové rozhraní připojené k virtuálnímu počítači Azure definováno jako primární síťové rozhraní. Všechna ostatní síťová rozhraní ve virtuálním počítači jsou sekundární síťová rozhraní. Ve výchozím nastavení je také veškerý odchozí provoz z virtuálního počítače odeslán ip adresu, která je přiřazena k primární konfiguraci IP primárního síťového rozhraní.

V místním prostředí mohou mít virtuální počítače nebo servery více síťových rozhraní pro různé sítě v rámci prostředí. Různé sítě se obvykle používají k provádění konkrétních operací, jako jsou inovace, údržba a přístup k Internetu. Když migrujete nebo přejdete do Azure z místního prostředí, mějte na paměti, že všechna síťová rozhraní ve stejném virtuálním počítači musí být připojena ke stejné virtuální síti.

Ve výchozím nastavení Azure Site Recovery vytvoří tolik síťových rozhraní na virtuálním počítači Azure, kolik je připojených k místnímu serveru. Během migrace nebo převzetí služeb při selhání se můžete vyhnout vytváření redundantních síťových rozhraní úpravou nastavení síťového rozhraní v rámci nastavení replikovaného virtuálního počítače.

## <a name="select-the-target-network"></a>Výběr cílové sítě

Pro virtuální počítače VMware a fyzické počítače a pro virtuální počítače Hyper-V (bez správce virtuálních počítačů System Center) můžete určit cílovou virtuální síť pro jednotlivé virtuální počítače. U virtuálních počítačů Hyper-V spravovaných pomocí Správce virtuálních počítačů můžete pomocí [mapování sítě](site-recovery-network-mapping.md) mapovat sítě virtuálních počítačů na zdrojovém serveru Virtual Machine Manager a cílit na sítě Azure.

1. V části **Replikované položky** v trezoru služby Recovery Services vyberte libovolnou replikovanou položku pro přístup k nastavení pro tuto replikovanou položku.

2. Vyberte kartu **Výpočetní výkon a síť** pro přístup k nastavení sítě pro replikovanou položku.

3. V části **Vlastnosti sítě**zvolte virtuální síť ze seznamu dostupných síťových rozhraní.

    ![Nastavení sítě](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Změna cílové sítě ovlivní všechna síťová rozhraní pro daný virtuální počítač.

V cloudech správce virtuálních počítačů ovlivní úprava mapování sítě všechny virtuální počítače a jejich síťová rozhraní.

## <a name="select-the-target-interface-type"></a>Výběr typu cílového rozhraní

V části **Síťová rozhraní** v podokně **Výpočetní výkon a síť** můžete zobrazit a upravit nastavení síťového rozhraní. Můžete také určit typ cílového síťového rozhraní.

- Pro převzetí služeb při selhání je vyžadováno **primární** síťové rozhraní.
- Všechna ostatní vybraná síťová rozhraní, pokud existují, jsou **sekundární** síťová rozhraní.
- Vyberte **Nepoužívat** k vyloučení síťového rozhraní z vytváření při převzetí služeb při selhání.

Ve výchozím nastavení při povolení replikace vybere site recovery všechna zjištěná síťová rozhraní na místním serveru. To označuje jeden jako **primární** a všechny ostatní jako **sekundární**. Všechna následující rozhraní přidaná na místní server jsou označena **jako nepoužívejte** ve výchozím nastavení. Když přidáváte další síťová rozhraní, ujistěte se, že je vybrána správná velikost cíle virtuálního počítače Azure tak, aby vyhovovala všem požadovaným síťovým rozhraním.

## <a name="modify-network-interface-settings"></a>Změna nastavení síťového rozhraní

Můžete upravit podsíť a adresu IP pro síťová rozhraní replikované položky. Pokud není zadána adresa IP, site recovery přiřadí další dostupnou adresu IP z podsítě síťovému rozhraní při převzetí služeb při selhání.

1. Vyberte libovolné dostupné síťové rozhraní a otevřete nastavení síťového rozhraní.

2. Ze seznamu dostupných podsítí zvolte požadovanou podsíť.

3. Zadejte požadovanou IP adresu (podle potřeby).

    ![Nastavení síťového rozhraní](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Výběrem **možnosti OK** dokončíte úpravy a vrátíte se do podokna **Výpočetní výkon a síť.**

5. Opakujte kroky 1-4 pro ostatní síťová rozhraní.

6. Vyberte **Uložit,** chcete-li uložit všechny změny.

## <a name="next-steps"></a>Další kroky
  [Další informace](../virtual-network/virtual-network-network-interface-vm.md) o síťových rozhraních pro virtuální počítače Azure.
