---
title: Nastavení IP adresy pro připojení po převzetí služeb při selhání do Azure s Azure Site Recovery | Microsoft Docs
description: Popisuje, jak nastavit IP adres k připojení k virtuálním počítačům Azure po převzetí služeb při selhání z místního se službou Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/24/2018
ms.author: manayar
ms.openlocfilehash: e74ad30ea8c4be994c1c67735ee91ef8c033b9b3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="set-up-ip-addressing-to-connect-after-failover-to-azure"></a>Nastavení IP adres k připojení po převzetí služeb při selhání do Azure

Tento článek vysvětluje síťové požadavky pro připojení k virtuálním počítačům Azure, po použití [Azure Site Recovery](site-recovery-overview.md) služby pro replikaci a převzetí služeb při selhání do Azure.

V tomto článku se dozvíte o:

> [!div class="checklist"]
> * Metody připojení, které můžete použít
> * Jak používat jinou IP adresu pro replikované virtuální počítače Azure
> * Postup zachovat IP adresy pro virtuální počítače Azure po převzetí služeb při selhání

## <a name="connecting-to-replica-vms"></a>Připojení k virtuální počítače replik

Při plánování replikace a převzetí služeb při selhání, mezi klíčové otázky je způsob připojení k virtuálnímu počítači Azure po převzetí služeb při selhání. Při navrhování strategie sítě pro virtuální počítače Azure repliky existuje několik možností:

- **Použít jinou IP adresu**: můžete vybrat použít jiný rozsah IP adres pro replikované síť virtuálního počítače Azure. V tomto scénáři virtuální počítač získá nové adresy IP po převzetí služeb při selhání, a je nutná aktualizace DNS.
- **Zachovat stejnou IP adresu**: můžete chtít použít stejného rozsahu IP adres, který ve vaší lokalitě primární místní síť Azure po převzetí služeb při selhání. Zachovat stejné IP adresy zjednodušuje obnovení snížením problémy související se sítí po převzetí služeb při selhání. Ale pokud replikujete do Azure, musíte po převzetí služeb při selhání aktualizace tras se nové umístění na IP adresy.

## <a name="retaining-ip-addresses"></a>Zachování IP adresy

Site Recovery poskytuje schopnost zachovat pevné IP adresy při přebírání služeb při selhání do Azure, s převzetím služeb podsítě.

- S převzetí služeb při selhání podsíť určité podsíti se nachází v lokalitě 1 nebo 2 lokality, ale nikdy v obou lokalitách současně.
- Aby byla zachována adresní prostor IP adres v případě selhání, můžete prostřednictvím kódu programu uspořádejte infrastruktury směrovač přesunout podsítě z jedné lokality do jiného.
- Během převzetí služeb při selhání přesuňte podsítí s přidružené chráněných virtuálních počítačů. Hlavní nevýhodou je, že v případě selhání, je nutné přesunout celý podsítě.


### <a name="failover-example"></a>Příklad převzetí služeb při selhání

Podívejme se na příklad pro převzetí služeb při selhání do Azure pomocí fiktivní společnosti Woodgrove Bank.

- Společnost Woodgrove Bank hostitelem jejich obchodních aplikací v místním serverem. Hostují své mobilní aplikace v Azure.
- Neexistuje připojení site-to-site VPN mezi jejich místní hraniční sítě a virtuální síť Azure. Kvůli připojení k síti VPN se zobrazí virtuální sítě v Azure jako rozšíření místní sítě.
- Woodgrove chce replikovat místní úlohy do Azure pomocí Site Recovery.
 - Woodgrove má aplikace, které závisí na pevně IP adresy, takže je třeba zachovat IP adresy pro aplikace, po převzetí služeb při selhání do Azure.
 - Prostředky, které běží v Azure používají 172.16.1.0/24 rozsah IP adres, 172.16.2.0/24.

![Před převzetí služeb při selhání podsíť](./media/site-recovery-network-design/network-design7.png)

**Infrastruktury před převzetí služeb při selhání**


Woodgrove, abyste mohli replikovat jejích virtuálních počítačů do Azure a zachovat stávající IP adresy, zde je, co společnosti musí udělat:


1. Vytvořte virtuální síť Azure ve kterém se vytvoří virtuální počítače Azure po převzetí služeb při selhání místního počítače. Rozšíření místní sítě, třeba tak, že aplikace bezproblémově přebírány.
2. Před převzetí služeb při selhání ve službě Site Recovery se přiřadit stejnou IP adresu ve vlastnostech počítače. Site Recovery po převzetí služeb při selhání, přiřadí tato adresa virtuálního počítače Azure.
3. Po spuštění převzetí služeb při selhání a virtuálních počítačích Azure jsou vytvořeny pomocí stejné IP adresy, připojení k síti pomocí [virtuální síť připojení mezi virtuálními](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Tato akce může provádět skriptování.
4. Je třeba upravit trasy, aby odpovídaly že této 192.168.1.0/24 je nyní přesunuta do Azure.


**Infrastruktury po převzetí služeb při selhání**

![Po převzetí služeb při selhání podsíť](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Připojení Site-to-site

Kromě připojení vnet-to-vnet, po převzetí služeb při selhání Woodgrove můžete nastavit připojení site-to-site VPN:
- Při nastavování připojení site-to-site v síť Azure, které je možné pouze směrovat provoz na místní umístění (místní ntwork) Pokud rozsahu IP adres se liší od místní rozsah IP adres. Je to proto, že Azure nepodporuje roztažené podsítě. Ano Pokud máte podsíť 192.168.1.0/24 místně, nemůžete přidat 192.168.1.0/24 místní sítě v síti Azure. Toto je očekávané, protože Azure není známo, že neexistují žádné aktivní virtuální počítače v podsíti, a že podsíť se vytváří jenom zotavení po havárii.
- Abyste mohli správně směrovat síťový provoz mimo síť Azure, nesmí být v konfliktu podsítě v síti a v místní síti.




## <a name="assigning-new-ip-addresses"></a>Přiřazení nové IP adresy

To [příspěvku na blogu](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) vysvětluje, jak nastavit Azure síťová infrastruktura, pokud nepotřebujete zachovat IP adres po převzetí služeb při selhání. Popis aplikace spustí, vypadá v tom, jak nastavit místní sítí a Azure a s informacemi o spuštění převzetí služeb při selhání se ukončí.

## <a name="next-steps"></a>Další postup
[Spuštění převzetí služeb při selhání](site-recovery-failover.md)
