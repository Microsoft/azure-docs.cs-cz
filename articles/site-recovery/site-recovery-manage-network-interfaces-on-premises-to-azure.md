---
title: Správa síťových rozhraní ve službě Azure Site Recovery pro místní zotavení po havárii do Azure | Dokumentace Microsoftu
description: Popisuje, jak Správa síťových rozhraní pro místní zotavení po havárii do Azure pomocí Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: e5757cee2238f44706194da5247da7d4721762f3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837566"
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Správa síťových rozhraní virtuálního počítače, místní zotavení po havárii do Azure
Virtuální počítač (VM) v Azure musí mít aspoň jedno síťové rozhraní k němu připojená. Může mít jak velký počet síťových rozhraní připojených k němu jako podporuje velikost virtuálního počítače.

Ve výchozím nastavení je první síťové rozhraní připojené k virtuálnímu počítači Azure definováno jako primární síťové rozhraní. Všechna další síťová rozhraní virtuálního počítače se sekundárními síťovými rozhraními. Také ve výchozím nastavení, veškerý odchozí provoz z virtuálního počítače bude odeslaná IP adresu, která je přiřazena primární konfigurace IP primárního síťového rozhraní.

V místním prostředí může mít několik síťových rozhraní pro různé sítě v prostředí virtuálních počítačů nebo serverů. Různé sítě se obvykle používají pro provádění určitých operací, jako je například upgrady, Údržba a přístup k Internetu. Při migraci nebo převzetí služeb při selhání do Azure z místního prostředí, mějte na paměti, aby síťová rozhraní ve stejném virtuálním počítači musí všechny připojené ke stejné virtuální síti.

Ve výchozím nastavení Azure Site Recovery vytvoří velký počet síťových rozhraní na virtuálním počítači Azure, jako jsou připojené k místnímu serveru. Vytváření redundantních síťových rozhraní během migrace nebo převzetí služeb při selhání tak, že upravíte nastavení síťového rozhraní v části Nastavení replikovaný virtuální počítač se můžete vyhnout.

## <a name="select-the-target-network"></a>Vyberte cílovou síť

Pro VMware a fyzických počítačů a virtuálních počítačů Hyper-V (bez System Center Virtual Machine Manager) můžete určit cílovou virtuální sítí pro jednotlivé virtuální počítače. Pro virtuální počítače Hyper-V spravované pomocí Virtual Machine Manager, použijte [mapování sítě](site-recovery-network-mapping.md) mapování sítě virtuálních počítačů na zdrojovém serveru Virtual Machine Manager a cílovými sítěmi Azure.

1. V části **replikované položky** v trezoru služby Recovery Services, vyberte všechny replikované položky pro přístup k nastavení pro tuto replikovanou položku.

2. Vyberte **výpočty a síť** kartu pro přístup k nastavení sítě pro replikovanou položku.

3. V části **vlastnosti sítě**, zvolte virtuální síť v seznamu dostupných síťových rozhraní.

    ![Nastavení sítě](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Změna cílové síti má vliv na všech síťových rozhraní pro tento konkrétní virtuální počítač.

Upravuje se mapování sítě pro cloudy Virtual Machine Manager, ovlivňuje všechny virtuální počítače a jejich síťová rozhraní.

## <a name="select-the-target-interface-type"></a>Vyberte cílový typ rozhraní

V části **síťová rozhraní** část **výpočty a síť** podokně můžete zobrazit a upravit nastavení síťového rozhraní. Můžete také zadat typ cílového síťového rozhraní.

- A **primární** síťové rozhraní se ale vyžaduje pro převzetí služeb při selhání.
- Všechny vybrané síťové rozhraní, pokud jsou **sekundární** síťová rozhraní.
- Vyberte **nepoužívejte** chcete vyloučit z vytvoření při převzetí služeb při selhání síťového rozhraní.

Ve výchozím nastavení Pokud se povolení replikace, Site Recovery vybere všech zjištěných síťových rozhraní na místním serveru. Označí jako jeden **primární** a všechny ostatní jako **sekundární**. Všechny následující rozhraní přidat na místním serveru, jsou označeny **nepoužívejte** ve výchozím nastavení. Když přidáváte více síťových rozhraní, ujistěte se, že je vybraná velikost cíle správné virtuálních počítačů Azure tak, aby vyhovovaly všechna požadovaná síťová rozhraní.

## <a name="modify-network-interface-settings"></a>Upravit nastavení síťového rozhraní

Můžete upravit podsíť a IP adresy pro síťová rozhraní replikované položky. Pokud IP adresa není zadaná, Site Recovery přiřadí další dostupnou IP adresu z podsítě k síťovému rozhraní při převzetí služeb při selhání.

1. Vyberte jakékoli dostupných síťových rozhraní, otevřete nastavení síťového rozhraní.

2. Vyberte požadované podsítě ze seznamu dostupných podsítí.

3. Zadejte požadovanou IP adresu (podle potřeby).

    ![Nastavení síťového rozhraní](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Vyberte **OK** s úpravami hotovi a vraťte **výpočty a síť** podokně.

5. Opakujte kroky 1 až 4 pro jiné síťová rozhraní.

6. Vyberte **Uložit** uložte všechny změny.

## <a name="next-steps"></a>Další postup
  [Další informace](../virtual-network/virtual-network-network-interface-vm.md) o síťových rozhraních pro virtuální počítače Azure.
