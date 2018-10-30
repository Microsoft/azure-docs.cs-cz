---
title: Mapování virtuálních sítí mezi dvěma oblastmi Azure ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Azure Site Recovery koordinuje replikaci, převzetí služeb při selhání a obnovení virtuálních počítačů a fyzických serverů. Další informace o převzetí služeb při selhání do Azure nebo do sekundárního datacentra.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: 1e8bad9a7a194c96c39be0ab4f1c2f40d79031ea
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209584"
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Mapování virtuálních sítí v různých oblastech Azure


Tento článek popisuje, jak namapovat dvě instance služby Azure Virtual Network umístěné v různých oblastech Azure mezi sebou. Mapování sítě zajišťuje, že při replikovaného virtuálního počítače se vytvoří v cílové oblasti Azure, virtuálního počítače se také vytvoří ve virtuální síti, která je namapovaná na virtuální síť zdrojového virtuálního počítače.  

## <a name="prerequisites"></a>Požadavky
Než budete mapovat sítě, ujistěte se, že jste vytvořili [virtuální síť Azure](../virtual-network/virtual-networks-overview.md) ve zdrojové oblasti a cílovou oblastí Azure.

## <a name="map-virtual-networks"></a>Mapování virtuálních sítí

Pokud chcete namapovat virtuální síť Azure, který se nachází v jedné oblasti Azure (Zdrojová síť) k virtuální síti, který se nachází v jiné oblasti (Cílová síť), pro virtuální počítače Azure, přejděte na **infrastruktura Site Recovery**  >  **Síťové mapování**. Vytvořte mapování sítě.

![Okno mapování sítě – vytvořit mapování sítě](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


V následujícím příkladu je virtuální počítač spuštěn v oblasti východní Asie. Virtuální počítač je právě replikován do oblasti jihovýchodní Asie.

Pokud chcete vytvořit mapování sítě z oblasti jihovýchodní Asie do oblasti jihovýchodní Asie, vyberte umístění zdrojové síti a umístění cílové síti. Pak vyberte **OK**.

![Přidání okna mapování sítě – vyberte zdrojové a cílové umístění pro zdrojovou síť](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Opakujte předchozí postup vytvoření mapování sítě z oblasti jihovýchodní Asie na oblast východní Asie.

![Přidání podokna mapování sítě – vyberte zdrojové a cílové umístění pro cílovou síť](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>Při povolení replikace mapování sítě

Při replikaci virtuálních počítačů z jedné oblasti Azure do jiné oblasti poprvé, pokud neexistuje žádné mapování sítě, můžete nastavit cílové síti, při nastavení replikace. Na základě tohoto nastavení, Azure Site Recovery vytvoří mapování sítí z oblasti zdrojové do cílové oblasti a z cílové oblasti zdrojové oblasti.   

![Konfigurace nastavení podokně – zvolte cílové umístění](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti, která je stejný jako zdrojovou síť k síti. Site Recovery vytvoří síť tak, že přidáte **Azure Site Recovery -** jako příponu k názvu zdrojové síti. Chcete-li zvolit sítě, které již byly vytvořeny, vyberte **vlastní**.

![Přizpůsobení podokno nastavení cílové – sada název cílové skupiny prostředků a název cílové virtuální sítě](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

Pokud mapování sítě se už k problému došlo, nelze změnit cílovou virtuální sítí, když povolíte replikaci. V tomto případě Pokud chcete změnit cílovou virtuální sítí, upravte existující mapování sítě.  

![Přizpůsobit cílový podokno nastavení – nastavte název cílové skupiny prostředků](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Upravit podokna mapování sítě – upravit existující název cílové virtuální sítě](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Pokud upravíte mapování sítě z oblasti A oblasti B, ujistěte se, že upravíte mapování sítě z oblasti B do oblasti A.
>
>


## <a name="subnet-selection"></a>Výběr podsítě
Podsíť cílový virtuální počítač je vybrána na základě názvu podsítě zdrojový virtuální počítač. Pokud podsíť, která má stejný název jako zdrojový virtuální počítač je k dispozici v cílové síti, nastaví se pro cílový virtuální počítač této podsíti. Pokud podsíť s tímto názvem neexistuje v cílové síti, podle abecedy první podsíť je nastavená jako cílovou podsíť.

Chcete-li změnit podsíť, přejděte **výpočty a síť** nastavení pro virtuální počítač.

![Výpočty a síť výpočetní vlastnosti okna](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP adresa

IP adresa pro každé síťové rozhraní cílového virtuálního počítače je nastavená, jak je popsáno v následujících částech.

### <a name="dhcp"></a>DHCP
Pokud síťového rozhraní zdrojového virtuálního počítače používá DHCP, síťového rozhraní cílového virtuálního počítače je také nastavena na používání protokolu DHCP.

### <a name="static-ip-address"></a>Statická IP adresa
Pokud síťového rozhraní zdrojového virtuálního počítače používá statickou IP adresu, síťového rozhraní cílového virtuálního počítače je také nastavena na použijte statickou IP adresu. Následující části popisují, jak nastavit statickou IP adresu.

### <a name="ip-assignment-behavior-during-failover"></a>Chování přiřazení IP během převzetí služeb při selhání
#### <a name="1-same-address-space"></a>1. Stejného adresního prostoru

Pokud podsíť zdrojové a cílové podsíti máte stejném adresním prostoru, IP adresa síťového rozhraní zdrojového virtuálního počítače je nastavena jako cílová IP adresa. Pokud stejnou IP adresu není k dispozici, nastavit je jako cílová IP adresa další dostupnou IP adresu.

#### <a name="2-different-address-spaces"></a>2. Různé adresních prostorů

Pokud podsíť zdrojové a cílové podsíti máte různé adresních prostorů, další dostupnou IP adresu v cílové podsíti je nastavena jako cílová IP adresa.


### <a name="ip-assignment-behavior-during-test-failover"></a>Chování přiřazení IP během testovacího převzetí služeb při selhání
#### <a name="1-if-the-target-network-chosen-is-the-production-vnet"></a>1. Pokud je cílové síti, zvolit virtuální síť výroby
- Obnovení IP (cílová IP adresa) bude statická IP adresa, ale jeho **nesmí být stejné IP adresy** jako vyhrazené pro převzetí služeb při selhání.
- Přiřazená IP adresa bude další dostupné IP adresy z konce rozsahu adres podsítě.
- Pro například, pokud statickou IP adresu zdrojového virtuálního počítače je nakonfigurovaná na stav: 10.0.0.19 a testovací převzetí služeb při selhání došlo k pokusu o s nakonfigurovanou produkční sítě: ***zotavení po havárii PROD-severozápadní***, s rozsahu podsítě jako 10.0.0.0/24. </br>
Převzetím služeb při selhání virtuálního počítače by byli přiřazeni s - další dostupné IP adresy, od konce rozsahu adres podsítě, která je: 10.0.0.254 </br>

**Poznámka:** terminologii **virtuální síť výroby** jsou označovány "Cílová síť" namapované během konfigurace zotavení po havárii.
#### <a name="2-if-the-target-network-chosen-is-not-the-production-vnet-but-has-the-same-subnet-range-as-production-network"></a>2. Pokud je vybrána Cílová síť není virtuální síť výroby, ale má stejného rozsahu podsítě jako produkční sítě

- Obnovení IP (cílová IP adresa) bude statické IP adresy s **stejnou IP adresu** (tj, nakonfigurovat statickou IP adresu) jako vyhrazené pro převzetí služeb při selhání. Za předpokladu, že je k dispozici stejné IP adresy.
- Pokud je nakonfigurovaná statická IP adresa je už přiřazený k jiné virtuální počítač nebo zařízení, budou IP pro zotavení další dostupné IP adresy z konce rozsahu adres podsítě.
- Pro například, pokud statickou IP adresu zdrojového virtuálního počítače je nakonfigurovaná na stav: 10.0.0.19 a testovací převzetí služeb při selhání došlo k pokusu o k testovací síti: ***zotavení po havárii bez-PROD-severozápadní***, pomocí stejného rozsahu podsítě jako produkční sítě - 10.0.0.0/24. </br>
  S následující statická IP adresa se přiřadí převzetím služeb při selhání virtuálního počítače </br>
    - nakonfigurovat statickou IP adresu: 10.0.0.19, pokud je IP adresa dostupná.
    - Další dostupné IP adresy: 10.0.0.254, pokud je IP adresa 10.0.0.19 již v použít.


Pokud chcete upravit IP adresu cílového v každé síťové rozhraní, přejděte na **výpočty a síť** nastavení pro virtuální počítač.</br>
Doporučujeme vždy doporučujeme, abyste zvolte testovací síti provést testovací převzetí služeb při selhání.
## <a name="next-steps"></a>Další postup

* Kontrola [sítě pokyny pro replikaci virtuálních počítačů Azure](site-recovery-azure-to-azure-networking-guidance.md).
