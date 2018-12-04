---
title: Nastavení IP adres připojit po zotavení po havárii a převzetí služeb při selhání do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Popisuje, jak nastavit IP adres pro připojení k virtuálním počítačům Azure po zotavení po havárii a převzetí služeb při selhání z místního pomocí Azure Site Recovery
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 7e682850d331770766a9b97c2b4b9102af143f2e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836266"
---
# <a name="set-up-ip-addressing-to-connect-to-azure-vms-after-failover"></a>Nastavení IP adres pro připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Tento článek vysvětluje požadavky na síť pro připojení k virtuálním počítačům Azure po použití [Azure Site Recovery](site-recovery-overview.md) service pro replikaci a převzetí služeb při selhání do Azure.

V tomto článku se dozvíte o:

> [!div class="checklist"]
> * Připojení metod, které můžete použít
> * Jak použít jinou IP adresu pro replikované virtuální počítače Azure
> * Jak zachovat IP adresy pro virtuální počítače Azure po převzetí služeb při selhání

## <a name="connecting-to-replica-vms"></a>Připojení k virtuální počítače replik

Při plánování replikace a převzetí služeb při selhání, jeden z klíčových otázek je, jak se připojit k virtuálnímu počítači Azure po převzetí služeb při selhání. Při navrhování strategie sítě pro virtuální počítače Azure repliky, máte několik možností:

- **Použít jinou IP adresu**: můžete vybrat použít jiný rozsah IP adres pro replikované síť virtuálních počítačů Azure. V tomto scénáři virtuální počítač dostane novou IP adresu po převzetí služeb při selhání, a vyžaduje se aktualizace DNS.
- **Zachovat stejnou IP adresu**: můžete chtít použít stejného rozsahu IP adres, který ve vaší místní lokalitě pro síť Azure po převzetí služeb při selhání. Zachování stejné IP adresy zjednodušuje obnovení snížením problémy související se sítí po převzetí služeb při selhání. Ale při replikaci do Azure, je potřeba aktualizovat trasy s novým umístěním IP adresy po převzetí služeb při selhání.

## <a name="retaining-ip-addresses"></a>Zachování IP adresy

Site Recovery poskytuje schopnost zachovat pevnou IP adres po převzetí služeb při selhání do Azure, s převzetím služeb podsítě.

- S podsítí převzetí služeb při selhání v konkrétní podsíti se nachází v lokalitě 1 nebo 2 lokality, ale nikdy na obou místech současně.
- Aby zůstalo zachované v případě selhání adresní prostor IP adres, je prostřednictvím kódu programu uspořádat infrastruktury směrovače přesunout podsítě z jedné lokality do jiného.
- Během převzetí služeb při selhání podsítí přejít bez přidružených chráněných virtuálních počítačích. Hlavní nevýhodou je, že v případě selhání, je nutné přesunout celé podsítě.


### <a name="failover-example"></a>Příklad převzetí služeb při selhání

Pojďme se podívat na příklad pro převzetí služeb při selhání do Azure s využitím fiktivní společnosti Woodgrove Bank.

- Společnosti Woodgrove Bank hostitelem jejich obchodních aplikací v místní lokalitě. Hostují své mobilní aplikace na Azure.
- Existuje připojení site-to-site VPN mezi jejich místní hraniční sítí a virtuální síť Azure. Z důvodu připojení VPN se zobrazí virtuální síť v Azure jako rozšíření místní sítě.
- Woodgrove chce, aby se pro replikaci místních úloh do Azure pomocí Site Recovery.
 - Woodgrove má aplikace, které závisí na pevně zakódované IP adresy, takže je třeba zachovat IP adresy pro aplikace, po převzetí služeb při selhání do Azure.
 - Prostředky spuštěné v Azure použít 172.16.1.0/24 rozsah IP adres, 172.16.2.0/24.

![Před převzetí služeb při selhání podsíť](./media/site-recovery-network-design/network-design7.png)

**Infrastruktura před převzetí služeb při selhání**


Pro organizaci Woodgrove mohli své virtuální počítače replikovat do Azure při zachování IP adresy, tady na co společnost potřebuje provést:


1. Vytvořte Azure virtual network, ve kterém se vytvoří virtuální počítače Azure po převzetí služeb při selhání místních počítačů. Rozšíření místní sítě, třeba tak, že aplikace můžete převzetí služeb při selhání bez problémů.
2. Před převzetí služeb při selhání v Site Recovery přiřadí stejnou IP adresu ve vlastnostech počítače. Site Recovery po převzetí služeb při selhání, přiřadí tuto adresu k virtuálnímu počítači Azure.
3. Po spuštění převzetí služeb při selhání a virtuální počítače Azure jsou vytvářeny pomocí stejné IP adresy, připojení k síti pomocí [připojení mezi virtuálními sítěmi](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Tato akce možnost využívat skripty.
4. Je třeba upravit trasy, aby odpovídaly že tohoto 192.168.1.0/24 je nyní přesunuta do Azure.


**Infrastruktury po převzetí služeb při selhání**

![Po převzetí služeb při selhání podsíť](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Připojení Site-to-site

Kromě připojení vnet-to-vnet, po převzetí služeb při selhání Woodgrove můžete nastavit připojení VPN typu site-to-site:
- Při nastavování připojení site-to-site v síti Azure, které můžete pouze směrovat provoz do místního umístění (místní síť) Pokud rozsah adres IP se liší od místní rozsah IP adres. Je to proto, že Azure nepodporuje roztažené podsítě. Takže pokud máte podsítě 192.168.1.0/24 místní, nelze přidat 192.168.1.0/24 místní sítě v síti Azure. To je očekáváno, protože Azure nebude vědět, že neexistují žádné aktivní virtuální počítače v podsíti, a že podsíť se vytváří jenom zotavení po havárii.
- Aby bylo možné správně směrování síťového provozu mimo síť Azure, nesmí být v konfliktu podsítě v síti a v místní síti.




## <a name="assigning-new-ip-addresses"></a>Přiřazení nové IP adresy

To [blogový příspěvek](https://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) vysvětluje, jak nastavit síťové infrastruktury Azure, když není nutné zachovat IP adresy po převzetí služeb při selhání. Začne popis aplikace, bude vypadat na tom, jak nastavit sítě v místním úložišti a Azure a končí informace o spuštění převzetí služeb při selhání.

## <a name="next-steps"></a>Další postup
[Převzetí služeb při selhání](site-recovery-failover.md)
