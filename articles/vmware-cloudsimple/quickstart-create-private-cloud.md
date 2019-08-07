---
title: Rychlé zprovoznění řešení Azure VMware podle CloudSimple – vytvoření privátního cloudu
description: Naučte se vytvářet a konfigurovat privátní cloud pomocí řešení Azure VMware od CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b68dcd47377ee56c4ebedc94905e1f0a8b70b38
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812336"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Rychlý Start – konfigurace prostředí privátního cloudu

V tomto článku se dozvíte, jak vytvořit privátní cloud CloudSimple a nastavit své prostředí privátního cloudu.

## <a name="before-you-begin"></a>Před zahájením

Přidělte rozsah CIDR pro podsítě vSphere/síti vSAN pro privátní cloud. Privátní cloud se vytvoří jako izolovaný zásobník VMware (ESXi hosts, vCenter, síti vSAN a NSX), který spravuje Server vCenter. Součásti pro správu se nasazují v síti vybrané pro směrování vSphere/síti vSAN pro sítě. Rozsah směrování sítě je v průběhu nasazení rozdělen do různých podsítí.  Adresní prostor podsítě vSphere/síti vSAN musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple.  Sítě, které komunikují s CloudSimple, zahrnují místní sítě a virtuální sítě Azure.  Další informace o podsítích vSphere/síti vSAN najdete v tématu [Přehled sítí VLAN a podsítí](cloudsimple-vlans-subnets.md).

* Minimální předpona rozsahu CIDR podsítě vSphere/síti vSAN:/24 
* Maximální předpona rozsahu CIDR podsítě vSphere/síti vSAN:/21

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Vytvoření privátního cloudu

1. Vyberte **Všechny služby**.
2. Vyhledejte **služby CloudSimple Services**.
3. Vyberte službu CloudSimple, na které chcete vytvořit privátní cloud.
4. V přehledu klikněte na **vytvořit privátní cloud** . otevře se nová karta prohlížeče pro CloudSimple portál.  Pokud se zobrazí výzva, přihlaste se pomocí přihlašovacích údajů pro přihlášení k Azure.  

    ![Vytvoření privátního cloudu z Azure](media/create-private-cloud-from-azure.png)

5. V portálu CloudSimple zadejte název vašeho privátního cloudu.
6. Vyberte **umístění** vašeho privátního cloudu.
7. Vyberte **typ uzlu** , který jste zřídili v Azure.  Můžete zvolit [možnost CS28 nebo CS36](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). Tato možnost zahrnuje maximální kapacitu výpočetní kapacity a paměti.
8. Zadejte **počet uzlů**.  Pro vytvoření privátního cloudu se vyžadují minimální tři uzly.

    ![Vytvořit privátní cloud – základní informace](media/create-private-cloud-basic-info.png)

9. Klikněte **na další: Rozšířené možnosti**.
10. Zadejte rozsah CIDR pro podsítě vSphere/síti vSAN. Ujistěte se, že se rozsah CIDR nepřekrývá s žádnou z vašich místních nebo jiných podsítí Azure.

    ![Vytvoření privátního cloudu – rozšířené možnosti](media/create-private-cloud-advanced-options.png)

11. Vyberte **další: Zkontrolujte a vytvořte**.
12. Zkontrolujte nastavení. Pokud potřebujete změnit nějaké nastavení, klikněte na tlačítko **Předchozí**.
13. Klikněte na možnost **Vytvořit**.

Spustí se proces zřízení privátního cloudu.  Zřízení privátního cloudu může trvat až dvě hodiny.

## <a name="launch-cloudsimple-portal"></a>Spustit portál CloudSimple

Portál CloudSimple můžete zpřístupnit z Azure Portal.  Portál CloudSimple se spustí s přihlašovacími údaji pro přihlášení k Azure pomocí jednotného přihlašování (SSO).  Přístup k portálu CloudSimple vyžaduje autorizaci **autorizační aplikace služby CloudSimple** .  Další informace o udělení oprávnění najdete v tématu [souhlasu s aplikací autorizace služby CloudSimple](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application) .

1. Vyberte **Všechny služby**.
2. Vyhledejte **služby CloudSimple Services**.
3. Vyberte službu CloudSimple, na které chcete vytvořit privátní cloud.
4. Z přehledu klikněte na **Přejít na portál CloudSimple** a otevřete novou kartu prohlížeče pro portál CloudSimple.  Pokud se zobrazí výzva, přihlaste se pomocí přihlašovacích údajů pro přihlášení k Azure.  

    ![Spustit portál CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Vytvoření sítě VPN typu Point-to-site

Připojení VPN typu Point-to-site je nejjednodušší způsob, jak se připojit k privátnímu cloudu z vašeho počítače. Pokud se připojujete k privátnímu cloudu vzdáleně, použijte připojení VPN typu Point-to-site.  Pro rychlý přístup k privátnímu cloudu použijte následující postup.  Přístup k oblasti CloudSimple z vaší místní sítě se dá udělat pomocí [sítě VPN typu Site-to-site](https://docs.azure.cloudsimple.com/vpn-gateway/) nebo [Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/).

### <a name="create-gateway"></a>Vytvořit bránu

1. Spusťte portál CloudSimple a vyberte **síť**.
2. Vyberte **VPN Gateway**.
3. Klikněte na **nový VPN Gateway**.

    ![Vytvoření brány VPN](media/create-vpn-gateway.png)

4. V části **Konfigurace brány**zadejte následující nastavení a klikněte na **Další**.

    * Jako typ brány vyberte **síť VPN typu Point-to-site** .
    * Zadejte název pro identifikaci brány.
    * Vyberte umístění Azure, kde je vaše služba CloudSimple nasazená.
    * Zadejte podsíť klienta pro bránu Point-to-site.  Adresy DHCP budou předány z této podsítě, když se připojíte.

5. V poli **připojení/uživatel**zadejte následující nastavení a klikněte na tlačítko **Další**.

    * Pokud chcete všem současným a budoucím uživatelům automaticky dovolit přístup k privátnímu cloudu prostřednictvím této brány Point-to-site, vyberte **automaticky přidat všechny uživatele**. Když vyberete tuto možnost, automaticky se vyberou všichni uživatelé v seznamu uživatelů. Automatickou možnost můžete přepsat tak, že zrušíte výběr jednotlivých uživatelů v seznamu.
    * Chcete-li vybrat pouze jednotlivé uživatele, klikněte na zaškrtávací políčka v seznamu uživatelů.

6. V části sítě VLAN a podsítě můžete zadat správu a sítě VLAN a uživatele pro bránu a připojení.

    * **Automatické přidávání** možností nastaví globální zásady pro tuto bránu. Nastavení platí pro aktuální bránu. Nastavení lze přepsat v oblasti **výběru** .
    * Vyberte **Přidat sítě VLAN pro správu/podsítě privátních cloudů**. 
    * Pokud chcete přidat všechny sítě VLAN a podsítě definované uživatelem, klikněte na **Přidat uživatelsky definované sítě VLAN/podsítě**. 
    * Nastavení **Vybrat** přepíše globální nastavení v části **automaticky přidat**. 

7. Kliknutím na **Další** zkontrolujte nastavení. Kliknutím na ikony úprav proveďte požadované změny.
8. Kliknutím na **vytvořit** vytvořte bránu VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Připojení k CloudSimple pomocí sítě VPN typu Point-to-site

Klient VPN je potřebný pro připojení k CloudSimple z počítače.  Stáhněte si [klienta OpenVPN](https://openvpn.net/community-downloads/) pro Windows nebo [viskozitu](https://www.sparklabs.com/viscosity/download/) pro MacOS a OS X.

1. Spusťte portál CloudSimple a vyberte **síť**.
2. Vyberte **VPN Gateway**.
3. V seznamu bran VPN klikněte na bránu VPN typu Point-to-site.
4. Vyberte možnost **Uživatelé**.
5. Klikněte na **Stáhnout konfiguraci sítě VPN** .

    ![Stáhnout konfiguraci sítě VPN](media/download-p2s-vpn-configuration.png)

6. Import konfigurace na klienta VPN

    * Pokyny pro [Import konfigurace na klienta Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Pokyny pro [Import konfigurace v MacOS nebo OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Připojení k CloudSimple

## <a name="create-a-vlan-for-your-workload-vms"></a>Vytvoření sítě VLAN pro virtuální počítače s úlohami

Po vytvoření privátního cloudu Vytvořte síť VLAN, do které budete nasazovat své pracovní a aplikační virtuální počítače.

1. Na portálu CloudSimple vyberte **síť**.
2. Klikněte na **síť VLAN/podsítě**.
3. Klikněte na **vytvořit síť VLAN nebo podsíť** .

    ![Vytvořit síť VLAN nebo podsíť](media/create-new-vlan-subnet.png)

4. Vyberte **privátní cloud** pro novou síť VLAN nebo podsíť.
5. Ze seznamu vyberte ID sítě VLAN.  
6. Zadejte název podsítě pro identifikaci podsítě.
7. Zadejte rozsah a masku směrování mezi podsítěmi.  Tento rozsah se nesmí překrývat s žádnými stávajícími podsítěmi.
8. Klikněte na **Submit** (Odeslat).

    ![Vytvořit podrobnosti o síti VLAN nebo podsíti](media/create-new-vlan-subnet-details.png)

Vytvoří se síť VLAN nebo podsíť.  Teď můžete pomocí tohoto ID sítě VLAN Vytvořit distribuovanou skupinu portů v rámci vašeho privátního cloudu vCenter. 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Připojení prostředí k virtuální síti Azure

CloudSimple poskytuje okruh ExpressRoute pro váš privátní cloud. Virtuální síť v Azure můžete připojit k okruhu ExpressRoute. Úplné podrobnosti o nastavení připojení najdete v postupu v části [připojení Azure Virtual Network pomocí ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/) .

## <a name="sign-in-to-vcenter"></a>Přihlásit se k vCenter

Nyní se můžete přihlásit k vCenter a nastavit virtuální počítače a zásady.

1. Pokud chcete získat přístup k serveru vCenter, začněte na portálu CloudSimple. Na domovské stránce v části **běžné úlohy**klikněte na **Spustit klienta vSphere**.  Vyberte privátní cloud a pak klikněte na **Spustit klienta vSphere** v privátním cloudu.

    ![Spustit klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Vyberte preferovaného klienta vSphere pro přístup k serveru vCenter a přihlaste se pomocí uživatelského jména a hesla.  Výchozí hodnoty jsou:
    * Uživatelské jméno: **CloudOwner@cloudsimple.local**
    * Heslo: **CloudSimple123!**  

Obrazovky vCenter v následujících postupech jsou od klienta vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Změna hesla vCenter

CloudSimple doporučuje změnit heslo při prvním přihlášení do vCenter.  
Heslo, které jste nastavili, musí splňovat následující požadavky:

* Maximální doba života: Heslo je třeba změnit každých 365 dní.
* Omezit opakované použití: Uživatelé nemůžou znovu použít žádné z předchozích pěti hesel.
* Délka: 8-20 znaků
* Speciální znak: Aspoň jeden speciální znak
* Abecední znaky: Aspoň jedno velké písmeno, A-Z a aspoň jedno malé písmeno, a-z
* Hodnoty Aspoň jeden numerický znak, 0-9
* Maximální počet identických sousedících znaků: Tři

    Příklad: KOPIE nebo CCC jsou přijatelné jako součást hesla, ale CCCC ne.

Pokud nastavíte heslo, které nesplňuje požadavky:

* Pokud používáte klienta vSphere Flash, nahlásí chybu.
* Pokud používáte klienta HTML5, nehlásí chybu. Klient nepřijme změnu a původní heslo bude nadále fungovat.

## <a name="change-nsx-administrator-password"></a>Změnit heslo správce NSX

NSX Manager je nasazen s výchozím heslem.  Po vytvoření privátního cloudu doporučujeme změnit heslo.

   * Uživatelské jméno: **správce**
   * Heslo: **CloudSimple123!**

Plně kvalifikovaný název domény (FQDN) a IP adresa NSX Manageru najdete na portálu CloudSimple.

1. Spusťte portál CloudSimple a vyberte **prostředky**.
2. Klikněte na privátní cloud, který chcete použít.
3. Vybrat **síť pro správu vSphere**
4. Použijte plně kvalifikovaný název domény nebo IP adresu **NSX Manageru** a připojte se pomocí webového prohlížeče. 

    ![Najít plně kvalifikovaný název domény NSX Manageru](media/private-cloud-nsx-manager-fqdn.png)

Chcete-li změnit heslo, postupujte podle pokynů v části [Správa hesla uživatele](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.4/administration/GUID-DB31B304-66A5-4516-9E55-2712D12B4F27.html).

> [!WARNING]
> Ve výchozím nastavení vyprší platnost hesla správce NSX po 90 dnech.

## <a name="create-a-port-group"></a>Vytvoření skupiny portů

Postup vytvoření distribuované skupiny portů v vSphere:

1. Postupujte podle pokynů v části Přidání distribuované skupiny portů v [příručce sítě vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Při nastavování distribuované skupiny portů zadejte ID sítě VLAN vytvořené v části [vytvoření sítě VLAN pro vaše virtuální počítače](#create-a-vlan-for-your-workload-vms)s úlohami.

## <a name="next-steps"></a>Další postup

* [Využití virtuálních počítačů VMware v Azure](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [Využití virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* [Připojení k místní síti pomocí Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Nastavení VPN typu Site-to-Site z místního prostředí](https://docs.azure.cloudsimple.com/vpn-gateway/)
