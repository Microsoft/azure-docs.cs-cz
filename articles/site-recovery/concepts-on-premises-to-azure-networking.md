---
title: Připojení k místnímu virtuálnímu počítači Azure s převzetím služeb při selhání pomocí Azure Site Recovery
description: Popisuje, jak se připojit k virtuálním počítačům Azure po převzetí služeb při selhání z místního prostředí do Azure pomocí Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: harshacs
ms.openlocfilehash: 4b88ed44632aa255837d8fb499782e11c716d443
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048183"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Připojení k virtuálním počítačům Azure po převzetí služeb při selhání z místního prostředí 


Tento článek popisuje, jak nastavit připojení, abyste se mohli úspěšně připojit k virtuálním počítačům Azure po převzetí služeb při selhání.

Když nastavíte zotavení po havárii místních virtuálních počítačů a fyzických serverů do Azure, [Azure Site Recovery](site-recovery-overview.md) spustí replikování počítačů do Azure. Pokud dojde k výpadku, můžete převzít služby při selhání do Azure z místní lokality. Když dojde k převzetí služeb při selhání, Site Recovery vytvoří virtuální počítače Azure pomocí replikovaných místních dat. V rámci plánování zotavení po havárii potřebujete zjistit, jak se připojit k aplikacím běžícím na těchto virtuálních počítačích Azure po převzetí služeb při selhání.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Příprava místních počítačů před převzetím služeb při selhání.
> * Příprava virtuálních počítačů Azure po převzetí služeb při selhání. 
> * Po převzetí služeb při selhání uchovávat IP adresy na virtuálních počítačích Azure.
> * Po převzetí služeb při selhání přiřaďte virtuálním počítačům Azure nové IP adresy.

## <a name="prepare-on-premises-machines"></a>Příprava místních počítačů

Aby se zajistilo připojení k virtuálním počítačům Azure, připravte své místní počítače před převzetím služeb při selhání.

### <a name="prepare-windows-machines"></a>Příprava počítačů s Windows

Na místních počítačích s Windows udělejte toto:

1. Nakonfigurujte nastavení systému Windows. Patří mezi ně odebrání statických trvalých tras nebo proxy WinHTTP a nastavení zásad pro diskovou síť SAN na **OnlineAll**. [Postupujte podle](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) těchto pokynů.

2. Ujistěte se, že [tyto služby](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) běží.

3. Pokud chcete povolit vzdálená připojení k místnímu počítači, povolte vzdálenou plochu (RDP). [Naučte](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) se, jak povolit protokol RDP pomocí PowerShellu.

4. Pokud chcete po převzetí služeb při selhání získat přístup k virtuálnímu počítači Azure, v bráně Windows Firewall na místním počítači Povolte protokol TCP a UDP ve veřejném profilu a nastavte RDP jako povolenou aplikaci pro všechny profily.

5. Pokud chcete po převzetí služeb při selhání přistupovat k virtuálnímu počítači Azure pomocí sítě VPN typu Site-to-site, povolte v bráně Windows Firewall na místním počítači protokol RDP pro doménu a privátní profily. [Naučte](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) se, jak povolíte provoz protokolu RDP.
6. Ujistěte se, že při aktivaci převzetí služeb při selhání nečekají na místním VIRTUÁLNÍm počítači žádné aktualizace Windows. V takovém případě můžou aktualizace po převzetí služeb při selhání zahájit instalaci na virtuálním počítači Azure a až do dokončení aktualizace se nebudete moct přihlásit k virtuálnímu počítači.

### <a name="prepare-linux-machines"></a>Příprava počítačů se systémem Linux

Na místních počítačích se systémem Linux proveďte tyto kroky:

1. Ověřte, že je služba Secure Shell nastavená tak, aby se spouštěla automaticky při spuštění systému.
2. Zkontrolujte, že pravidla brány firewall umožňují připojení SSH.


## <a name="configure-azure-vms-after-failover"></a>Konfigurace virtuálních počítačů Azure po převzetí služeb při selhání

Po převzetí služeb při selhání proveďte následující na virtuálních počítačích Azure, které se vytvořily.

1. Pokud se chcete připojit k virtuálnímu počítači přes Internet, přiřaďte virtuálnímu počítači veřejnou IP adresu. Nemůžete použít stejnou veřejnou IP adresu pro virtuální počítač Azure, který jste použili pro místní počítač. [Další informace](../virtual-network/virtual-network-public-ip-address.md)
2. Ověřte, že pravidla skupiny zabezpečení sítě (NSG) na virtuálním počítači povolují příchozí připojení k portu RDP nebo SSH.
3. Zkontrolujte [diagnostiku spouštění](/troubleshoot/azure/virtual-machines/boot-diagnostics#enable-boot-diagnostics-on-existing-virtual-machine) a zobrazte si virtuální počítač.


> [!NOTE]
> Služba Azure bastionu nabízí privátní připojení RDP a SSH k virtuálním počítačům Azure. [Přečtěte si další informace](../bastion/bastion-overview.md) o této službě.

## <a name="set-a-public-ip-address"></a>Nastavení veřejné IP adresy

Jako alternativu k ručnímu přiřazení veřejné IP adresy virtuálnímu počítači Azure můžete tuto adresu přiřadit během převzetí služeb při selhání pomocí skriptu nebo Runbooku služby Azure Automation v Site Recovery [plánu obnovení](site-recovery-create-recovery-plans.md), nebo můžete nastavit směrování na úrovni DNS pomocí Azure Traffic Manager. [Přečtěte si další informace](concepts-public-ip-address-with-site-recovery.md) o nastavení veřejné adresy.


## <a name="assign-an-internal-address"></a>Přiřadit interní adresu

Pokud chcete nastavit interní IP adresu virtuálního počítače Azure po převzetí služeb při selhání, máte několik možností:

- **Zachovat stejnou IP adresu**: můžete použít stejnou IP adresu na virtuálním počítači Azure jako počítač přidělený místnímu počítači.
- **Použijte jinou IP adresu**: pro virtuální počítač Azure můžete použít jinou IP adresu.


## <a name="retain-ip-addresses"></a>Uchování IP adres

Site Recovery vám umožní při převzetí služeb při selhání do Azure zachovat stejné IP adresy. Zachování stejné IP adresy zabrání potenciálním problémům se sítí po převzetí služeb při selhání, ale zavádí určitou složitost.

- Pokud cílový virtuální počítač Azure používá stejnou IP adresu nebo podsíť jako vaše místní lokalita, nemůžete se mezi nimi připojit pomocí připojení VPN typu Site-to-site nebo ExpressRoute, protože se překrývá adresa. Podsítě musí být jedinečné.
- Po převzetí služeb při selhání budete potřebovat připojení z místního prostředí do Azure, aby byly aplikace dostupné na virtuálních počítačích Azure. Azure nepodporuje roztažené sítě VLAN, takže pokud chcete zachovat IP adresy, které potřebujete, abyste si v Azure převzali služby při selhání přes celou podsíť (kromě místního počítače).
- Převzetí služeb při selhání v podsíti zajišťuje, že konkrétní podsíť není dostupná současně místně a v Azure.

Zachování IP adres vyžaduje následující kroky:

- Ve vlastnostech služby COMPUTE & sítě replikované položky nastavte síťové a IP adresy cílového virtuálního počítače Azure tak, aby odpovídaly místnímu nastavení.
- Podsítě se musí spravovat jako součást procesu zotavení po havárii. Potřebujete virtuální síť Azure, která bude odpovídat místní síti, a po změně síťových tras převzetí služeb při selhání je potřeba, aby odrážely, že se podsíť přesunula do Azure a přidala se nová umístění IP adres.  

### <a name="failover-example"></a>Příklad převzetí služeb při selhání

Podívejme se na příklad.

- Fiktivní společnost Woodgrove Bank hostuje své obchodní aplikace v místním prostředí, které hostují své mobilní aplikace v Azure.
- Připojují se z místního prostředí k Azure prostřednictvím sítě Site-to-Site VPN. 
- Woodgrove používá Site Recovery k replikaci místních počítačů do Azure.
- Jejich místní aplikace používají pevně zakódované IP adresy, takže chtějí uchovávat stejné IP adresy v Azure.
- Místní počítače, na kterých běží aplikace, běží ve třech podsítích:
    - 192.168.1.0/24
    - 192.168.2.0/24
    - 192.168.3.0/24
- Aplikace běžící v Azure se nacházejí ve dvou podsítích ve virtuální **síti** Azure:
    - 172.16.1.0/24
    - 172.16.2.0/24

Aby bylo možné tyto adresy zachovat, je zde postup.

1. Pokud povolíte replikaci, určují, že počítače by se měly replikovat do **sítě Azure**.
2. Vytvářejí **síť pro obnovení** v Azure. Tato virtuální síť odráží podsíť 192.168.1.0/24 ve své místní síti.
3. Woodgrove nastaví připojení typu [VNet-to-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) mezi dvěma sítěmi. 

    > [!NOTE]
    > V závislosti na požadavcích aplikace by bylo možné nastavit připojení typu VNet-to-VNet před převzetím služeb při selhání, jako krok ruční krok/skriptování nebo sada Runbook služby Azure Automation v rámci [plánu obnovení](site-recovery-create-recovery-plans.md)Site Recovery nebo po dokončení převzetí služeb při selhání.

4. Před převzetím služeb při selhání ve vlastnostech počítače v Site Recovery nastaví cílovou IP adresu na adresu místního počítače, jak je popsáno v dalším postupu.
5. Po převzetí služeb při selhání se vytvoří virtuální počítače Azure se stejnou IP adresou. Woodgrove se připojuje ze **sítě Azure** k virtuální **síti pro obnovení** pomocí partnerského vztahu virtuálních sítí (s povoleným přenosovým připojením).
6. V místním prostředí Woodgrove potřebuje provést změny v síti, včetně změny tras, aby odrážely, že 192.168.1.0/24 přesunul do Azure.  

**Infrastruktura před převzetím služeb při selhání**

![Před převzetím služeb při selhání podsítí](./media/site-recovery-network-design/network-design7.png)


**Infrastruktura po převzetí služeb při selhání**

![Po převzetí služeb při selhání podsítě](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Nastavení cílové sítě

Před převzetím služeb při selhání zadejte nastavení sítě a IP adresu pro cílový virtuální počítač Azure.

1.  V poli Recovery Services trezor – > **replikované položky** vyberte místní počítač.
2. Na stránce **výpočty a síť** počítače klikněte na **Upravit** a nakonfigurujte nastavení sítě a adaptéru pro cílový virtuální počítač Azure.
3. V **okně Vlastnosti sítě** vyberte cílovou síť, ve které se virtuální počítač Azure při vytvoření po převzetí služeb při selhání bude nacházet.
4. V části **Síťová rozhraní** nakonfigurujte síťové adaptéry v cílové síti. Ve výchozím nastavení Site Recovery zobrazuje všechny zjištěné síťové karty na místním počítači.
    - V poli **typ cílového síťového rozhraní** můžete nastavit každou síťovou kartu jako **primární**, **sekundární** nebo **nevytvořenou** , pokud nepotřebujete konkrétní síťovou kartu v cílové síti. Jeden síťový adaptér musí být nastaven jako primární pro převzetí služeb při selhání. Všimněte si, že změna cílové sítě má vliv na všechny síťové karty virtuálního počítače Azure.
    - Kliknutím na název síťové karty určete podsíť, do které se virtuální počítač Azure nasadí.
    - Proveďte **dynamické** přepsání pomocí privátní IP adresy, kterou chcete přiřadit k CÍLOVÉmu virtuálnímu počítači Azure. Pokud IP adresa není zadaná Site Recovery přiřadí k síťovému rozhraní při převzetí služeb při selhání další dostupnou IP adresu v podsíti.
    - [Přečtěte si další informace](site-recovery-manage-network-interfaces-on-premises-to-azure.md) o správě síťových adaptérů pro místní převzetí služeb při selhání do Azure.


## <a name="get-new-ip-addresses"></a>Získat nové IP adresy

V tomto scénáři po převzetí služeb při selhání Získá virtuální počítač Azure novou IP adresu. Pokud chcete nastavit novou IP adresu pro virtuální počítač vytvořený po převzetí služeb při selhání, můžete postupovat podle následujících kroků:

1. Přejít na **replikované položky**.
2. Vyberte požadovaný virtuální počítač Azure.
3. Vyberte **výpočty a síť** a vyberte **Upravit**.

     ![Přizpůsobení síťových konfigurací převzetí služeb při selhání](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Pokud chcete aktualizovat nastavení sítě převzetí služeb při selhání, vyberte **Upravit** pro síťovou kartu, kterou chcete nakonfigurovat. Na další stránce, která se otevře, zadejte odpovídající předem vytvořenou IP adresu v části testovací převzetí služeb při selhání a umístění pro převzetí služeb při selhání.

    ![Úprava konfigurace síťové karty](media/azure-to-azure-customize-networking/nic-drilldown.png)

5. Vyberte **OK**.

Site Recovery teď budou dodržovat tato nastavení a zajistěte, aby se virtuální počítač při převzetí služeb při selhání připojil k vybranému prostředku přes odpovídající IP adresa, pokud je k dispozici v rozsahu cílových IP adres. V tomto scénáři není nutné převzetí služeb při selhání celé podsíti. K aktualizaci záznamů pro převzetí služeb při selhání počítače, aby odkazovala na novou IP adresu virtuálního počítače, bude nutné aktualizovat DNS.

## <a name="next-steps"></a>Další kroky
[Přečtěte si o](site-recovery-active-directory.md) replikaci místní služby Active Directory a DNS do Azure.