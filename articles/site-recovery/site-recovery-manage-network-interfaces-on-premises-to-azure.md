---
title: Správa síťových rozhraní v Azure Site Recovery pro místním nasazením a Azure replikace | Microsoft Docs
description: Popisuje, jak spravovat síťová rozhraní pro místním nasazením a Azure replikace s Azure Site Recovery
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: cf16a0e67aaf2d2c73649adccabf3e5eb0540bd3
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-to-azure-replication"></a>Spravovat síťová rozhraní virtuálních počítačů pro místním nasazením a Azure replikace

Virtuální počítač (VM) v Azure, musí mít aspoň jedno síťové rozhraní, které jsou k němu připojen. Může mít jako mnoho síťových rozhraní, které jsou k němu připojen jako podporuje velikost virtuálního počítače.

Ve výchozím nastavení je první síťové rozhraní připojené k virtuální počítač Azure definován jako primární síťové rozhraní. Všechny ostatní síťová rozhraní virtuálního počítače se sekundárními síťovými rozhraními. Také ve výchozím nastavení, všechny odchozí přenosy z virtuálního počítače je odeslaná IP adresu, která je přiřazena k primární konfiguraci IP primární síťové rozhraní.

V prostředí místní virtuální počítače nebo servery můžou mít více síťových rozhraní pro různé sítě v prostředí. Různé sítě jsou obvykle používány k provedení určité operace, například upgrady, údržbu a přístup k Internetu. Při migraci nebo přebírání služeb při selhání do Azure z místního prostředí, mějte na paměti, že síťová rozhraní stejného virtuálního počítače všechny připojen ke stejné virtuální síti.

Ve výchozím nastavení vytvoří Azure Site Recovery jako mnoho síťová rozhraní na virtuální počítač Azure, jako jsou připojené k místnímu serveru. Vytváření redundantních síťových rozhraní během migrace nebo převzetí služeb při selhání úpravou nastavení síťového rozhraní v části nastavení pro replikované virtuální počítač se můžete vyhnout.

## <a name="select-the-target-network"></a>Vyberte cílovou síť

VMware a fyzické počítače a pro virtuální počítače Hyper-V (bez System Center Virtual Machine Manager) můžete zadat cílový virtuální sítě pro jednotlivé virtuální počítače. U virtuálních počítačů technologie Hyper-V spravované pomocí nástroje Virtual Machine Manager používat [mapování sítě](site-recovery-network-mapping.md) mapování sítě virtuálních počítačů na serveru nástroje Virtual Machine Manager zdrojové a cílové sítě Azure.

1. V části **replikované položky** v trezoru služeb zotavení, vyberte všechny replikované položky pro přístup k nastavení tohoto replikované položky.

2. Vyberte **výpočty a síť** karty pro přístup k nastavení sítě pro replikované položky.

3. V části **vlastnosti síťového**, vyberte virtuální síť ze seznamu dostupných síťových rozhraní.

    ![Nastavení sítě](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Úprava cílové síti ovlivňuje všech síťových rozhraní pro tento konkrétní virtuální počítač.

Úprava mapování sítě pro cloudy Virtual Machine Manager, ovlivňuje všechny virtuální počítače a jejich síťových rozhraní.

## <a name="select-the-target-interface-type"></a>Vyberte cílový typ rozhraní

V části **síťových rozhraní** části **výpočty a síť** podokně můžete zobrazit a upravit nastavení síťového rozhraní. Můžete také zadat typ cílového síťového rozhraní.

- A **primární** síťové rozhraní je vyžadována pro převzetí služeb při selhání.
- Všechny ostatní vybraná síťová rozhraní, pokud jsou některé, **sekundární** síťových rozhraní.
- Vyberte **nepoužívejte** vyloučit síťové rozhraní z vytvoření na převzetí služeb při selhání.

Ve výchozím nastavení když jste povolení replikace, Site Recovery vybere všech zjištěných síťových rozhraní na místním serveru. Označuje jeden jako **primární** a všechny ostatní jako **sekundární**. Všechny následné rozhraní přidat na místním serveru jsou označeny **nepoužívejte** ve výchozím nastavení. Když přidáváte více síťových rozhraní, zkontrolujte, že je vybraný správný virtuální počítač Azure cílovou velikost pro uložení všech požadovaných síťových rozhraní.

## <a name="modify-network-interface-settings"></a>Upravit nastavení síťového rozhraní

Můžete upravit podsíť a IP adresy pro síťová rozhraní replikované položky. Pokud není zadána IP adresa, Site Recovery přiřadit další dostupnou IP adresu z podsítě pro síťové rozhraní na převzetí služeb při selhání.

1. Vyberte všechny dostupné síťové rozhraní otevřete nastavení síťového rozhraní.

2. Požadovaná podsíť zvolte ze seznamu dostupných podsítí.

3. Zadejte požadovanou IP adresu (podle potřeby).

    ![Nastavení síťového rozhraní](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Vyberte **OK** ukončení úprav a vrátíte se na **výpočty a síť** podokně.

5. Opakujte kroky 1 – 4 pro jiné síťová rozhraní.

6. Vyberte **Uložit** uložit všechny změny.

## <a name="next-steps"></a>Další postup
  [Další informace](../virtual-network/virtual-network-network-interface-vm.md) o síťová rozhraní pro virtuální počítače Azure.
