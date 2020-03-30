---
title: Připojení k místním virtuálním montovnám Azure pomocí Azure Site Recovery
description: Popisuje, jak se připojit k virtuálním ms Azure po převzetí služeb při selhání z místního do Azure pomocí Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f222cdd315b79503b1bdea032f495c71df4682b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281987"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Připojení k virtuálním počítačům Azure po převzetí služeb při selhání z místního prostředí 


Tento článek popisuje, jak nastavit připojení, abyste se po převzetí služeb při selhání mohli úspěšně připojit k virtuálním počítačům Azure.

Když nastavíte zotavení po havárii místních virtuálních počítačů (VM) a fyzických serverů do Azure, [Azure Site Recovery](site-recovery-overview.md) spustí replikaci počítačů do Azure. Když pak dojde k výpadkům, můžete přejít do Azure z místního webu. Když dojde k převzetí služeb při selhání, Site Recovery vytvoří virtuální počítače Azure pomocí replikovaných místních dat. V rámci plánování zotavení po havárii musíte zjistit, jak se připojit k aplikacím spuštěným na těchto virtuálních počítačích Azure po převzetí služeb při selhání.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Před převzetím služeb při selhání připravte místní počítače.
> * Připravte virtuální počítače Azure po převzetí služeb při selhání. 
> * Po převzetí služeb při selhání uchovávejte IP adresy ve virtuálních počítačích Azure.
> * Přiřaďte nové IP adresy virtuálním počítačům Azure po převzetí služeb při selhání.

## <a name="prepare-on-premises-machines"></a>Příprava místních strojů

Chcete-li zajistit připojení k virtuálním počítačům Azure, připravte místní počítače před převzetím služeb při selhání.

### <a name="prepare-windows-machines"></a>Příprava počítačů se systémem Windows

V místních počítačích s Windows postupujte takto:

1. Konfigurace nastavení systému Windows. Mezi ně patří odebrání všech statických trvalých tras nebo serveru proxy protokolu WinHTTP a nastavení zásady sítě SAN disku na **onlinevše**. [Postupujte](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) podle těchto pokynů.

2. Ujistěte se, že tyto služby jsou [spuštěny.](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)

3. Povolte vzdálenou plochu (RDP) a povolte vzdálená připojení k místnímu počítači. [Přečtěte si, jak](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) povolit přístup k přístupu k serveru RDP pomocí prostředí PowerShell.

4. Chcete-li získat přístup k virtuálnímu počítači Azure přes internet po převzetí služeb při selhání, v bráně Windows Firewall v místním počítači povolte protokolt tcp a UDP ve veřejném profilu a nastavte protokol RDP jako povolenou aplikaci pro všechny profily.

5. Pokud chcete získat přístup k virtuálnímu počítači Azure přes síť VPN mezi lokalitami po převzetí služeb při selhání, v bráně Windows Firewall v místním počítači povolte protokol RDP pro profily domény a soukromé. [Přečtěte si,](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) jak povolit provoz v RDP.
6. Ujistěte se, že neexistují žádné aktualizace systému čekající na místní virtuální počítač při aktivaci převzetí služeb při selhání. Pokud existují, aktualizace může začít instalovat na virtuálním počítači Azure po převzetí služeb při selhání a nebudete moci přihlásit do virtuálního počítače, dokud aktualizace dokončit.

### <a name="prepare-linux-machines"></a>Příprava linuxových strojů

V místních počítačích s Linuxem postupujte takto:

1. Zkontrolujte, zda je služba Zabezpečené prostředí nastavena na automatické spuštění při spuštění systému.
2. Zkontrolujte, že pravidla brány firewall umožňují připojení SSH.


## <a name="configure-azure-vms-after-failover"></a>Konfigurace virtuálních počítačů Azure po převzetí služeb při selhání

Po převzetí služeb při selhání postupujte takto na virtuálních počítačích Azure, které jsou vytvořeny.

1. Chcete-li se připojit k virtuálnímu virtuálnímu počítačům přes internet, přiřaďte virtuálnímu hoprotokolu veřejnou IP adresu. Pro virtuální počítač Azure, který jste použili pro místní počítač, nemůžete použít stejnou veřejnou IP adresu. [Další informace](../virtual-network/virtual-network-public-ip-address.md)
2. Zkontrolujte, zda pravidla skupiny zabezpečení sítě (NSG) na virtuálním počítači umožňují příchozí připojení k portu RDP nebo SSH.
3. Zkontrolujte [diagnostiku spuštění](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) a zobrazte virtuální ho.


> [!NOTE]
> Služba Azure Bastion nabízí privátní přístup RDP a SSH k virtuálním počítačům Azure. [Další informace](../bastion/bastion-overview.md) o této službě.

## <a name="set-a-public-ip-address"></a>Nastavení veřejné IP adresy

Jako alternativu k ručnímu přiřazení veřejné IP adresy k virtuálnímu počítači Azure můžete adresu přiřadit během převzetí služeb při selhání pomocí skriptu nebo runbooku automatizace Azure v [plánu obnovení](site-recovery-create-recovery-plans.md)webu nebo můžete nastavit směrování na úrovni DNS pomocí Azure Traffic Manageru. [Přečtěte si další informace](concepts-public-ip-address-with-site-recovery.md) o nastavení veřejné adresy.


## <a name="assign-an-internal-address"></a>Přiřazení interní adresy

Pokud chcete nastavit interní IP adresu virtuálního počítače Azure po převzetí služeb při selhání, máte několik možností:

- **Zachovat stejnou IP adresu**: Můžete použít stejnou IP adresu na virtuálním počítači Azure jako ten, který je přidělen místnímu počítači.
- **Použití jiné IP adresy**: Pro virtuální počítač Azure můžete použít jinou IP adresu.


## <a name="retain-ip-addresses"></a>Zachovat IP adresy

Site Recovery umožňuje zachovat stejné IP adresy při převzetí softwaru do Azure. Zachování stejné adresy IP zabrání potenciálním problémům se sítí po převzetí služeb při selhání, ale zavádí určitou složitost.

- Pokud cílový virtuální počítač Azure používá stejnou IP adresu nebo podsíť jako místní web, nemůžete se mezi nimi připojit pomocí připojení VPN mezi lokalitami nebo ExpressRoute, protože se adresa překrývá. Podsítě musí být jedinečné.
- Po převzetí služeb při selhání potřebujete připojení z místního do Azure, aby aplikace byly dostupné na virtuálních počítačích Azure. Azure nepodporuje roztažené sítě VLAN, takže pokud chcete zachovat IP adresy, musíte převzít IP prostor do Azure tím, že převezmete celou podsíť, kromě místního počítače.
- Převzetí služeb při selhání podsítě zajišťuje, že konkrétní podsíť není k dispozici současně místně a v Azure.

Zachování adres IP vyžaduje následující kroky:

- V vlastnostech výpočetní & síť replikované položky nastavte síťové a IP adresování pro cílový virtuální počítač Azure tak, aby odrážely místní nastavení.
- Podsítě musí být spravovány jako součást procesu zotavení po havárii. Potřebujete virtuální síť Azure tak, aby odpovídala místní síti, a po obnovení sítě musí být upravena tak, aby odrážela, že podsíť byla přesunuta do Azure a nová umístění IP adres.  

### <a name="failover-example"></a>Příklad převzetí služeb při selhání

Pojďme se podívat na příklad.

- Fiktivní společnost Woodgrove Bank hostuje své obchodní aplikace v místním prostředí, které hostují své mobilní aplikace v Azure.
- Připojují se z místního do Azure přes VPN site-to-site. 
- Woodgrove používá site recovery k replikaci místních počítačů do Azure.
- Jejich místní aplikace používají pevně zakódované IP adresy, takže chtějí zachovat stejné IP adresy v Azure.
- Místní počítače se spuštěny aplikace jsou spuštěny ve třech podsítích:
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- Jejich aplikace spuštěné v Azure jsou umístěné v síti Azure **Azure network** ve dvou podsítích:
- 172.16.1.0/24
- 172.16.2.0/24.

Aby si mohli uchovat adresy, dělají toto.

1. Když povolí replikaci, určují, že počítače by se měly replikovat do **sítě Azure**.
2. Vytvářejí **síť obnovení** v Azure. Tato virtuální síť zrcadlí podsíť 192.168.1.0/24 v jejich místní síti.
3. Woodgrove nastaví [připojení virtuální sítě k virtuální síti](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) mezi těmito dvěma sítěmi. 

    > [!NOTE]
    > V závislosti na požadavcích aplikace může být připojení virtuální sítě k virtuální síti nastaveno před převzetím služeb při selhání, jako ruční krok/skriptovaný krok/sada runbook automatizace Azure v [plánu obnovení](site-recovery-create-recovery-plans.md)webu nebo po dokončení převzetí služeb při selhání.

4. Před převzetím služeb při selhání na vlastnosti počítače v site recovery, nastaví cílovou IP adresu na adresu místního počítače, jak je popsáno v dalším postupu.
5. Po převzetí služeb při selhání se virtuální počítače Azure vytvoří se stejnou IP adresou. Woodgrove se připojuje ze **sítě Azure** k virtuální síti **pro obnovení** pomocí partnerského vztahu virtuální sítě (s povoleným připojením k tranzitu).
6. Místní Woodgrove potřebuje provést změny v síti, včetně úpravy tras tak, aby odrážely, že 192.168.1.0/24 se přesunuldo Azure.  

**Infrastruktura před převzetím služeb při selhání**

![Před převzetím služeb při selhání podsítě](./media/site-recovery-network-design/network-design7.png)


**Infrastruktura po převzetí služeb při selhání**

![Po převzetí služeb při selhání podsítě](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Nastavení cílové sítě

Před převzetím služeb při selhání zadejte nastavení sítě a IP adresu pro cílový virtuální počítač Azure.

1.  V trezoru služby Recovery Services -> **replikované položky**vyberte místní počítač.
2. Na stránce **Výpočetní prostředky a síť** pro počítač klikněte na **Upravit**a nakonfigurujte nastavení sítě a adaptéru pro cílový virtuální počítač Azure.
3. Ve **vlastnostech sítě**vyberte cílovou síť, ve které bude virtuální počítač Azure umístěn po jeho vytvoření po převzetí služeb při selhání.
4. V **síťových rozhraních**nakonfigurujte síťové adaptéry v cílové síti. Ve výchozím nastavení site recovery zobrazí všechny zjištěné síťové karty v místním počítači.
    - V **typu cílového síťového rozhraní** můžete nastavit každou síťovou síť jako **primární**, **sekundární**nebo **nevytvářet,** pokud nepotřebujete tuto konkrétní síťovou síť v cílové síti. Jeden síťový adaptér musí být nastaven jako primární pro převzetí služeb při selhání. Všimněte si, že úprava cílové sítě ovlivní všechny síťové karty pro virtuální počítač Azure.
    - Kliknutím na název síťové kouta určete podsíť, ve které se bude virtuální počítač Azure nasazovat.
    - Přepište **Dynamic** privátní IP adresou, kterou chcete přiřadit k cílovému virtuálnímu počítači Azure. Pokud ip adresa není zadána Obnovení webu přiřadí další dostupnou ADRESU IP v podsíti síťové křenici při převzetí služeb při selhání.
    - [Přečtěte si další informace](site-recovery-manage-network-interfaces-on-premises-to-azure.md) o správě nic pro místní převzetí služeb při selhání do Azure.


## <a name="get-new-ip-addresses"></a>Získat nové IP adresy

V tomto scénáři virtuální počítač Azure získá novou IP adresu po převzetí služeb při selhání. Aktualizace DNS aktualizovat záznamy pro počítače s popřevzetím služeb při selhání přejděte na IP adresu virtuálního počítače Azure.



## <a name="next-steps"></a>Další kroky
[Přečtěte si o](site-recovery-active-directory.md) replikaci místní služby Active Directory a SLUŽBY DNS do Azure.


