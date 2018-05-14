---
title: Mapování virtuální sítě mezi dvěma oblastmi Azure ve službě Azure Site Recovery | Microsoft Docs
description: Azure Site Recovery koordinuje replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů a fyzických serverů. Další informace o převzetí služeb při selhání do Azure nebo do sekundárního datacentra.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 9294940785deb0834a419de8320286783635d68e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Mapování virtuální sítě v různých oblastech Azure


Tento článek popisuje způsob namapování dvě instance Azure Virtual Network umístěné v různých oblastech Azure mezi sebou. Mapování sítě zajistí, aby při replikovaného virtuálního počítače se vytvoří v cílové oblasti Azure, virtuální počítač je taky vytvořit ve virtuální síti, který je namapovaný na virtuální síť zdrojového virtuálního počítače.  

## <a name="prerequisites"></a>Požadavky
Předtím, než je mapovat sítě, ujistěte se, že jste vytvořili [virtuální síť Azure](../virtual-network/virtual-networks-overview.md) v oblasti zdrojové i cílové oblasti Azure.

## <a name="map-virtual-networks"></a>Mapování virtuální sítě

Mapovat Azure virtuální sítě, který je umístěný v jedné oblasti Azure (Zdrojová síť) k virtuální síti, která se nachází v jiné oblasti (Cílová síť) pro virtuální počítače Azure, přejděte na **infrastruktura Site Recovery**  >  **Sítě mapování**. Vytvořte mapování sítě.

![Okno mapování sítě – vytvoření mapování sítě](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


V následujícím příkladu je virtuální počítač spuštěn v oblasti, východní Asie. Virtuální počítač je právě replikován pro jihovýchodní Asie.

Vytvořit mapování sítě z oblasti východní Asie oblast, jihovýchodní Asie, vyberte umístění zdroje sítě a umístění cílové síti. Pak vyberte **OK**.

![Přidejte okno mapování sítě - vyberte zdrojové a cílové umístění pro zdrojové síti](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Opakujte předchozí postup k vytvoření mapování sítě z jihovýchodní Asie pro oblast východní Asie.

![Přidání podokna mapování sítě - vyberte zdrojové a cílové umístění pro cílové síti](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>Mapování sítě, když aktivujete replikaci

Při replikaci virtuálního počítače z jedné oblasti Azure do jiné oblasti poprvé, pokud neexistuje žádné mapování sítě, můžete nastavit cílovou síť při replikaci. Podle tohoto nastavení, Azure Site Recovery vytvoří mapování sítě z oblasti zdrojové do cílové oblasti a z oblasti cílové oblasti zdroje.   

![Konfigurace nastavení podokně – zvolte cílové umístění](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Ve výchozím nastavení Site Recovery vytvoří síť v cílová oblast, která je stejná jako zdrojovou síť. Site Recovery vytvoří síť přidáním **- automatické obnovení systému** jako příponu na název zdrojové síti. Vyberte síť, která již byla vytvořena, vyberte **přizpůsobit**.

![Přizpůsobení podokno nastavení cílové - název skupiny prostředků cílové sady a název cílové virtuální sítě](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

Pokud už došlo k mapování sítě, nelze změnit cílové virtuální síti, když aktivujete replikaci. V takovém případě změnit cíl virtuální síť, upravte existující mapování sítě.  

![Přizpůsobení cíl podokno nastavení – nastavit název cílové skupiny prostředků](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Upravit podokně mapování sítě – upravit stávající název cílové virtuální sítě](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Pokud změníte mapování sítě z oblasti A do oblasti B, ujistěte se také upravit mapování sítě z oblasti B oblasti A.
>
>


## <a name="subnet-selection"></a>Výběr podsítě
Podsíť cílového virtuálního počítače je vybrána na základě názvu podsíti zdrojového virtuálního počítače. Pokud podsíť, která má stejný název jako zdrojový virtuální počítač je k dispozici v cílové síti, je pro cílový virtuální počítač nastaven této podsíti. Pokud podsíť s tímto názvem neexistuje v cílové síti, abecedně první podsíť je nastaven jako cílové podsíti.

Chcete-li změnit podsíť, přejděte na **výpočty a síť** nastavení pro virtuální počítač.

![Výpočtů a sítě výpočetní vlastnosti – okno](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP adresa

Jak je popsáno v následujících částech, je sada adres IP pro každé síťové rozhraní cílového virtuálního počítače.

### <a name="dhcp"></a>DHCP
Pokud síťové rozhraní zdrojový virtuální počítač používá protokol DHCP, síťové rozhraní cílového virtuálního počítače je také nastavena na používání protokolu DHCP.

### <a name="static-ip-address"></a>Statická IP adresa
Pokud síťové rozhraní zdrojový virtuální počítač používá statickou IP adresu, síťové rozhraní cílového virtuálního počítače je také nastavena na použijte statickou IP adresu. Následující části popisují, jak nastavit statickou IP adresu.

#### <a name="same-address-space"></a>Stejné adresní prostor

Podsíť zdrojové a cílové podsíti stejnému adresnímu prostoru adres, IP adresu síťového rozhraní zdrojového virtuálního počítače a nastaví jako cílová IP adresa. Pokud není k dispozici stejnou IP adresu, další dostupnou IP adresu nastavena jako cílová IP adresa.

#### <a name="different-address-spaces"></a>Různé adresních prostorů

Pokud podsíť zdrojové a cílové podsíti různých adresní prostory, další dostupnou IP adresu v cílové podsíti nastavena jako cílová IP adresa.

Chcete-li upravit cílová IP adresa na každé rozhraní sítě, přejděte na **výpočty a síť** nastavení pro virtuální počítač.

## <a name="next-steps"></a>Další postup

* Zkontrolujte [sítě pokyny pro replikaci virtuálních počítačů Azure](site-recovery-azure-to-azure-networking-guidance.md).
