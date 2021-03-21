---
title: Mapování virtuálních sítí mezi dvěma oblastmi v Azure Site Recovery
description: Přečtěte si o mapování virtuálních sítí mezi dvěma oblastmi Azure pro zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: harshacs
ms.openlocfilehash: ff1f80641dc3db1f6b69fc0223c60022f8cf8435
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95811629"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Nastavení mapování sítě a přidělování IP adres pro virtuální sítě

Tento článek popisuje, jak namapovat dvě instance virtuálních sítí Azure (virtuální sítě) nacházejících se v různých oblastech Azure a jak nastavit IP adresy mezi sítěmi. Mapování sítě poskytuje výchozí chování pro výběr cílové sítě na základě zdrojové sítě v době povolení replikace.

## <a name="prerequisites"></a>Předpoklady

Předtím, než budete mapovat sítě, byste měli mít [Azure virtuální sítě](../virtual-network/virtual-networks-overview.md) ve zdrojové a cílové oblasti Azure. 

## <a name="set-up-network-mapping-manually-optional"></a>Ruční nastavení mapování sítě (volitelné)

Mapujte sítě následujícím způsobem:

1. V **Site Recovery infrastruktuře** klikněte na **+ mapování sítě**.

    ![ Vytvoření mapování sítě](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. V části **Přidat mapování sítě** vyberte zdrojové a cílové umístění. V našem příkladu je zdrojový virtuální počítač spuštěný v oblasti Východní Asie a replikuje se do oblasti jihovýchodní Asie.

    ![Vybrat zdroj a cíl](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Nyní vytvořte mapování sítě v opačném směru. V našem příkladu bude zdroj teď jihovýchodní Asie a cíl bude Východní Asie.

    ![Přidat podokno mapování sítě – vyberte zdrojové a cílové umístění pro cílovou síť.](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mapování sítí při povolení replikace

Pokud jste nepřipravili mapování sítě před konfigurací zotavení po havárii pro virtuální počítače Azure, můžete při [nastavování a povolování replikace](azure-to-azure-how-to-enable-replication.md)zadat cílovou síť. Když to uděláte, dojde k následujícímu:

- V závislosti na zvoleném cíli Site Recovery automaticky vytvořit mapování sítě ze zdroje do cílové oblasti a z cíle do zdrojové oblasti.
- Ve výchozím nastavení Site Recovery vytvoří síť v cílové oblasti, která je shodná se zdrojovou sítí. Site Recovery přidá nástroj **-ASR** jako příponu na název zdrojové sítě. Můžete přizpůsobit cílovou síť.
- Pokud pro zdrojovou síť již proběhlo mapování sítě, namapovaná Cílová síť bude vždy výchozí v době povolování replikace pro více virtuálních počítačů. Cílovou virtuální síť můžete změnit výběrem jiných dostupných možností z rozevíracího seznamu. 
- Chcete-li změnit výchozí cílovou virtuální síť pro nové replikace, je nutné upravit existující mapování sítě.
- Pokud chcete změnit mapování sítě z oblasti A na oblast B, ujistěte se, že jste nejdřív odstranili mapování sítě z oblasti B na oblast A. Po odstranění mapování zpět změňte mapování sítě z oblasti A na oblast B a pak vytvořte příslušné zpětné mapování.

>[!NOTE]
>* Změna mapování sítě mění pouze výchozí hodnoty pro nové replikace virtuálních počítačů. Nemá vliv na vybrané cílové virtuální sítě pro existující replikace. 
>* Pokud chcete změnit cílovou síť pro existující replikaci, použijte nastavení výpočty a síť replikované položky.

## <a name="specify-a-subnet"></a>Zadejte podsíť.

Podsíť cílového virtuálního počítače se vybere na základě názvu podsítě zdrojového virtuálního počítače.

- Pokud je v cílové síti dostupná podsíť se stejným názvem, jako je zdrojová podsíť virtuálních počítačů, je tato podsíť nastavená pro cílový virtuální počítač.
- Pokud v cílové síti neexistuje podsíť se stejným názvem, první podsíť v abecedním pořadí se nastaví jako cílová podsíť.
- Cílovou podsíť můžete upravit v nastavení **výpočty a síť** pro virtuální počítač.

    ![Okno výpočetních a síťových výpočetních vlastností](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Nastavení IP adres pro cílové virtuální počítače

IP adresa každé síťové karty na cílovém virtuálním počítači je nakonfigurovaná takto:

- **DHCP**: Pokud síťová karta zdrojového virtuálního počítače používá službu DHCP, síťové rozhraní cílového virtuálního počítače je také nastaveno na používání protokolu DHCP.
- **Statická IP adresa**: Pokud síťová karta zdrojového virtuálního počítače používá přidělování STATICKÝch IP adres, použije cílový síťový adaptér virtuálního počítače taky statickou IP adresu.


## <a name="ip-address-assignment-during-failover"></a>Přiřazení IP adresy během převzetí služeb při selhání

**Zdrojové a cílové podsítě** | **Podrobnosti**
--- | ---
Stejný adresní prostor | IP adresa zdrojového adaptéru virtuálního počítače je nastavená jako IP adresa cílové síťové karty virtuálního počítače.<br/><br/> Pokud adresa není k dispozici, je jako cíl nastavena Další dostupná IP adresa.
Jiný adresní prostor | Následující dostupná IP adresa v cílové podsíti je nastavená jako adresa cílové síťové karty virtuálního počítače.



## <a name="ip-address-assignment-during-test-failover"></a>Přiřazení IP adresy během testovacího převzetí služeb při selhání

**Cílová síť** | **Podrobnosti**
--- | ---
Cílová síť je virtuální síť převzetí služeb při selhání. | -Cílová IP adresa bude statická se stejnou IP adresou. <br/><br/>  – Pokud je stejná IP adresa už přiřazená, pak je tato IP adresa další dostupnou na konci rozsahu podsítě. Příklad: Pokud je zdrojová IP adresa 10.0.0.19 a převzetí služeb při selhání používá rozsah 10.0.0.0/24, pak je 10.0.0.254 další IP adresa přiřazená k cílovému virtuálnímu počítači.
Cílová síť není virtuální síť převzetí služeb při selhání. | -Cílová IP adresa bude statická se stejnou IP adresou.<br/><br/>  – Pokud je stejná IP adresa už přiřazená, pak je tato IP adresa další dostupnou na konci rozsahu podsítě.<br/><br/> Příklad: Pokud je zdrojová statická IP adresa 10.0.0.19 a převzetí služeb při selhání je v síti, která není síť s podporou převzetí služeb při selhání, s rozsahem 10.0.0.0/24, pak bude cílová statická IP adresa 10.0.0.19, pokud bude k dispozici, a jinak bude 10.0.0.254.

- Virtuální síť převzetí služeb při selhání je cílová síť, kterou vyberete při nastavování zotavení po havárii.
- Pro testovací převzetí služeb při selhání doporučujeme vždycky používat neprodukční síť.
- Cílovou IP adresu můžete upravit v nastavení **výpočty a síť** virtuálního počítače.


## <a name="next-steps"></a>Další kroky

- Přečtěte si [pokyny k síti](./azure-to-azure-about-networking.md) pro zotavení po havárii virtuálního počítače Azure.
- [Přečtěte si další informace](site-recovery-retain-ip-azure-vm-failover.md) o zachování IP adres po převzetí služeb při selhání.
