---
title: Mapování virtuálních sítí mezi dvěma oblastmi Azure ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Azure Site Recovery koordinuje replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů a fyzických serverů. Další informace o převzetí služeb při selhání do Azure nebo do sekundárního datacentra.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: b25806044dd74092a5404ad7ef24ddd386dffbc3
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521756"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Nastavte mapování sítě a IP adresy pro virtuální sítě

Tento článek popisuje, jak namapovat dvě instance s virtuálními sítěmi Azure (Vnet) nachází v různých oblastech Azure a jak nastavit IP adresování mezi sítěmi. Mapování sítě poskytuje výchozí chování pro výběr cílové sítě založené na zdrojové síti při povolení replikace.

## <a name="prerequisites"></a>Požadavky

Než budete mapovat sítě, měli byste mít [virtuálním sítím Azure](../virtual-network/virtual-networks-overview.md) ve zdrojové a cílové oblasti Azure. 

## <a name="set-up-network-mapping-manually-optional"></a>Nastavit ruční mapování sítě (volitelné)

Mapovat sítě následujícím způsobem:

1. V **infrastruktura Site Recovery**, klikněte na tlačítko **+ mapování sítě**.

    ![ Vytvořit mapování sítě](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. V **přidání mapování sítě**vyberte zdrojové a cílové umístění. V našem příkladu zdroj virtuální počítač běží v oblasti východní Asie a replikuje do oblasti jihovýchodní Asie.

    ![Vyberte zdroj a cíl](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. V adresáři opačné nyní vytvořte mapování sítě. V našem příkladu zdrojem bude nyní jihovýchodní Asii a cíl bude východní Asie.

    ![Přidání podokna mapování sítě – vyberte zdrojové a cílové umístění pro cílovou síť](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mapovat sítě při povolení replikace

Pokud jste dosud připraveni mapování sítě, než začnete konfigurovat zotavení po havárii pro virtuální počítače Azure, můžete zadat cílovou síť, jakmile je [nastavení a povolení replikace](azure-to-azure-how-to-enable-replication.md). Když nastavíte takto to se stane toto:

- Založený na cíli, který vyberete, Site Recovery automaticky vytvoří mapování sítě ze zdrojové do cílové oblasti a z cíle do zdrojové oblasti.
- Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti, která je stejný jako zdrojovou síť k síti. Site Recovery přidává **Azure Site Recovery -** jako příponu k názvu zdrojové síti. Můžete přizpůsobit cílová síť.
- Pokud mapování sítě pro zdrojové síti již došlo, namapované Cílová síť bude vždy výchozí při povolení replikace pro více virtuálních počítačů. Můžete změnit cílovou virtuální sítí výběrem dalších dostupných možností z rozevíracího seznamu. 
- Chcete-li změnit výchozí cílová virtuální síť pro nové replikace, budete muset upravit existující mapování sítě.
- Pokud chcete upravit mapování sítě z oblasti A oblasti B, ujistěte se, že je nejprve odstranit mapování sítě z oblasti B do oblasti A. Po odstranění reverzní mapování upravit mapování sítě z oblasti A oblasti B a pak vytvořte relevantní reverzní mapování.

>[!NOTE]
>* Upravuje se mapování sítě se změní pouze výchozí hodnoty pro nový virtuální počítač replikace. Výběr cílové virtuální sítě pro existující replikace nemá vliv. 
>* Pokud chcete změnit Cílová síť pro stávající replikaci, přejděte na výpočty a síť nastavení replikované položky.

## <a name="specify-a-subnet"></a>Zadejte podsíť

Podsítě, kterou vyberete virtuální počítač s cílem vycházet z názvu podsítě zdrojový virtuální počítač.

- Pokud podsíť se stejným názvem jako zdrojová podsíť virtuálních počítačů je k dispozici v cílové síti, nastaví se pro cílový virtuální počítač této podsíti.
- Pokud podsíť s tímto názvem neexistuje v cílové síti, první podsíti v abecedním pořadí je nastavena jako cílovou podsíť.
- Můžete změnit cílovou podsíť v **výpočty a síť** nastavení pro virtuální počítač.

    ![Výpočty a síť výpočetní vlastnosti okna](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Nastavení přidělování IP adres pro cílový virtuální počítače

IP adresa pro jednotlivé síťové karty na cílový virtuální počítač nakonfigurován takto:

- **DHCP**: Pokud síťového rozhraní zdrojového virtuálního počítače používá DHCP, síťového rozhraní cílového virtuálního počítače je také nastavena na používání protokolu DHCP.
- **Statická IP adresa**: Pokud síťového rozhraní zdrojového virtuálního počítače používá statické IP adresy, cílové síťové karty virtuálního počítače také použít statické IP adresy.


## <a name="ip-address-assignment-during-failover"></a>Přiřazení IP adresy během převzetí služeb při selhání

**Zdrojové a cílové podsítě** | **Podrobnosti**
--- | ---
Stejného adresního prostoru | IP adresa zdroje síťové karty virtuálního počítače je nastavena jako cílové síťové karty IP adresu virtuálního počítače.<br/><br/> Pokud tato adresa není k dispozici, nastavit je jako cíl další dostupnou IP adresu.

Jiným adresním prostorem<br/><br/> Další dostupnou IP adresu v cílové podsíti je nastavena jako cíl adresa síťové karty virtuálního počítače.



## <a name="ip-address-assignment-during-test-failover"></a>Přiřazení IP adresy během testovacího převzetí služeb při selhání

**Cílová síť** | **Podrobnosti**
--- | ---
Cílová síť je převzetí služeb při selhání virtuální sítě | -Cílová IP adresa je statická, ale ne stejnou IP adresu jako vyhrazené pro převzetí služeb při selhání.<br/><br/>  -Přiřazenou adresu je další dostupnou adresu od konce rozsahu podsítě.<br/><br/> Příklad: Pokud zdrojová IP adresa je 10.0.0.19 a převzetí služeb při selhání síť používá rozsah 10.0.0.0/24, IP adresa dalšího přiřazené k cílovému virtuálnímu počítači je 10.0.0.254.
Cílová síť není převzetí služeb při selhání virtuální sítě | -Cílová IP adresa bude statické pomocí stejné IP adresy rezervované pro převzetí služeb při selhání.<br/><br/>  – Pokud je již přiřazen stejnou IP adresu, IP adresa je dalším objektem, který je k dispozici na konci rozsahu podsítě.<br/><br/> Příklad: Pokud statickou IP adresu zdrojového 10.0.0.19 a převzetí služeb při selhání je připojen k síti, která není převzetí služeb při selhání sítě, s rozsah 10.0.0.0/24, pak statickou IP adresu cílového bude 10.0.0.0.19, pokud je k dispozici, a v opačném případě bude 10.0.0.254.

- Převzetí služeb virtuální sítě je cílové síti, který vyberete při nastavování zotavení po havárii.
- Doporučujeme vždy používat mimo produkční sítě pro testovací převzetí služeb při selhání.
- Můžete upravit IP adresu cílového v **výpočty a síť** nastavení virtuálního počítače.


## <a name="next-steps"></a>Další postup

- Kontrola [sítě pokyny](site-recovery-azure-to-azure-networking-guidance.md) pro zotavení po havárii virtuálního počítače Azure.
- [Další informace](site-recovery-retain-ip-azure-vm-failover.md) o zachování IP adresy po převzetí služeb při selhání.

Je-li vybrána Cílová síť vnet převzetí služeb při selhání"a 2. přejděte na příkaz Řekněme, že"Pokud cílová síť zvolili se liší od virtuální síť převzetí služeb při selhání, ale má stejného rozsahu podsítě jako virtuální síť převzetí služeb při selhání"
