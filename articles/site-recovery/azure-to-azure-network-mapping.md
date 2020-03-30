---
title: Mapování virtuálních sítí mezi dvěma oblastmi v azure site recovery
description: Přečtěte si o mapování virtuálních sítí mezi dvěma oblastmi Azure pro zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mayg
ms.openlocfilehash: b8f0512f978f25ca196ad6e9a7a03243c47f0662
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258080"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Nastavení mapování sítě a adresování IP adres pro virtuální sítě

Tento článek popisuje, jak mapovat dvě instance virtuálních sítí Azure (Virtuální sítě) umístěných v různých oblastech Azure a jak nastavit adresování IP mezi sítěmi. Mapování sítě poskytuje výchozí chování pro výběr cílové sítě na základě zdrojové sítě v době povolení replikace.

## <a name="prerequisites"></a>Požadavky

Než namapujete sítě, měli byste mít virtuální sítě Azure ve [zdrojových](../virtual-network/virtual-networks-overview.md) a cílových oblastech Azure. 

## <a name="set-up-network-mapping-manually-optional"></a>Ruční nastavení mapování sítě (volitelné)

Mapové sítě takto:

1. V **oblasti Infrastruktury pro obnovení lokality**klepněte na **položku +Mapování sítě**.

    ![ Vytvoření síťového mapování](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. V **části Přidat mapování sítě**vyberte zdrojová a cílová umístění. V našem příkladu zdrojový virtuální virtuální byl spuštěn v oblasti východní Asie a replikuje se do oblasti jihovýchodní Asie.

    ![Vybrat zdroj a cíl](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Nyní vytvořte síťové mapování v opačném směru. V našem příkladu bude nyní zdrojem jihovýchodní Asie a cílem bude východní Asie.

    ![Podokno mapování sítě – výběr zdrojových a cílových umístění pro cílovou síť](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mapovat sítě při povolení replikace

Pokud jste nepřipravili mapování sítě před konfigurací zotavení po havárii pro virtuální počítače Azure, můžete určit cílovou síť při [nastavování a povolení replikace](azure-to-azure-how-to-enable-replication.md). Když touděláte, stane se následující:

- Na základě vybraného cíle site recovery automaticky vytvoří mapování sítě ze zdroje do cílové oblasti a z cílové oblasti do zdrojové oblasti.
- Ve výchozím nastavení site recovery vytvoří síť v cílové oblasti, která je shodná se zdrojovou sítí. Site Recovery přidá **-asr** jako příponu k názvu zdrojové sítě. Cílovou síť můžete přizpůsobit.
- Pokud již došlo k mapování sítě pro zdrojovou síť, namapovaná cílová síť bude vždy výchozí v době povolení replikace pro více virtuálních připojení. Cílovou virtuální síť můžete změnit výběrem dalších dostupných možností v rozevíracím seznamu. 
- Chcete-li změnit výchozí cílovou virtuální síť pro nové replikace, je třeba upravit existující mapování sítě.
- Pokud chcete upravit mapování sítě z oblasti A do oblasti B, ujistěte se, že nejprve odstraníte mapování sítě z oblasti B do oblasti A. Po odstranění zpětného mapování upravte mapování sítě z oblasti A do oblasti B a vytvořte příslušné zpětné mapování.

>[!NOTE]
>* Úprava mapování sítě změní pouze výchozí hodnoty pro nové replikace virtuálních aplikací. Nemá vliv na výběr cílové virtuální sítě pro existující replikace. 
>* Pokud chcete upravit cílovou síť pro existující replikaci, přejděte na výpočetní a síťové nastavení replikované položky.

## <a name="specify-a-subnet"></a>Určení podsítě

Podsíť cílového virtuálního počítače je vybraná na základě názvu podsítě zdrojového virtuálního počítači.

- Pokud je v cílové síti dostupná podsíť se stejným názvem jako zdrojová podsíť virtuálních byl, je tato podsíť nastavená pro cílový virtuální virtuální soud.
- Pokud podsíť se stejným názvem v cílové síti neexistuje, první podsíť v abecedním pořadí je nastavena jako cílová podsíť.
- Cílovou podsíť můžete upravit v nastavení **výpočetních prostředků a sítě** pro virtuální počítač.

    ![Okno výpočetních a síťových výpočetních vlastností](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Nastavení adresování IP pro cílové virtuální jevy

IP adresa pro každou nic na cílovém virtuálním počítači je nakonfigurována takto:

- **DHCP**: Pokud nic zdrojového virtuálního soudu používá DHCP, nic cílového virtuálního soudu je také nastavena na použití DHCP.
- **Statická IP adresa**: Pokud nic zdrojového virtuálního počítačů používá statické adresování IP, cílová větev virtuálního virtuálního počítačů bude také používat statickou IP adresu.


## <a name="ip-address-assignment-during-failover"></a>Přiřazení IP adresy během převzetí služeb při selhání

**Zdrojové a cílové podsítě** | **Podrobnosti**
--- | ---
Stejný adresní prostor | IP adresa zdrojové vnonice virtuálních počítačů je nastavena jako cílová IP adresa nic virtuálního měniče virtuálního počítačů.<br/><br/> Pokud adresa není k dispozici, další dostupná IP adresa je nastavena jako cíl.
Jiný adresní prostor | Další dostupná ADRESA IP v cílové podsíti je nastavena jako cílová adresa síťové ho spoje virtuálního počítačů.



## <a name="ip-address-assignment-during-test-failover"></a>Přiřazení IP adresy během převzetí služeb při selhání testu

**Cílová síť** | **Podrobnosti**
--- | ---
Cílová síť je virtuální síť s podporou převzetí služeb při selhání. | - Cílová IP adresa bude statická se stejnou IP adresou. <br/><br/>  - Pokud je již přiřazena stejná ADRESA IP, pak je ip adresa další dostupná na konci rozsahu podsítě. Příklad: Pokud je zdrojová IP adresa 10.0.0.19 a síť s podporou převzetí služeb při selhání používá rozsah 10.0.0.0/24, pak je další ADRESA IP přiřazená cílovému virtuálnímu virtuálnímu počítačůmu 10.0.0.254.
Cílová síť není virtuální síť s podporou převzetí služeb při selhání | - Cílová IP adresa bude statická se stejnou IP adresou.<br/><br/>  - Pokud je již přiřazena stejná ADRESA IP, pak je ip adresa další dostupná na konci rozsahu podsítě.<br/><br/> Například: Pokud je zdrojová statická ADRESA IP 10.0.0.19 a převzetí služeb při selhání je v síti, která není sítí s podporou převzetí služeb při selhání, s rozsahem 10.0.0.0/24, bude cílová statická adresa IP 10.0.0.0.19, pokud je k dispozici, a jinak bude 10.0.0.254.

- Virtuální síť s podporou převzetí služeb při selhání je cílová síť, kterou vyberete při nastavování zotavení po havárii.
- Doporučujeme vždy používat neprodukční síť pro převzetí služeb při selhání testu.
- Cílovou IP adresu můžete upravit v nastavení **výpočetních prostředků a sítě** virtuálního počítače.


## <a name="next-steps"></a>Další kroky

- Projděte si [pokyny k vytváření sítí](site-recovery-azure-to-azure-networking-guidance.md) pro zotavení po havárii virtuálního počítače Azure.
- [Přečtěte si další informace](site-recovery-retain-ip-azure-vm-failover.md) o zachování IP adres po převzetí služeb při selhání.
