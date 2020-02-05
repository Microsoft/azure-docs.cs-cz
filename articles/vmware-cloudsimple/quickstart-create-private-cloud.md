---
title: Rychlé zprovoznění řešení Azure VMware (AVS) – vytvoření privátního cloudu služby AVS
description: Naučte se vytvářet a konfigurovat privátní cloud služby AVS pomocí řešení Azure VMware (AVS).
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cafcf04dac0542f1506980d8b9484b82b558e100
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018563"
---
# <a name="quickstart---configure-an-avs-private-cloud-environment"></a>Rychlý Start – konfigurace prostředí privátního cloudu pro funkci AVS

V tomto článku se dozvíte, jak vytvořit privátní cloud služby AVS a jak nastavit vaše prostředí privátního cloudu služby AVS.

## <a name="before-you-begin"></a>Než začnete

Kontrola [požadavků na síť](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-avs-private-cloud"></a>Vytvoření privátního cloudu služby AVS

Privátní cloud služby AVS je izolovaný zásobník VMware, který podporuje hostitele ESXi, vCenter, síti vSAN a NSX.

Privátní cloudy pro funkci AVS jsou spravovány prostřednictvím portálu AVS. Mají svůj vlastní Server vCenter ve své vlastní doméně pro správu. Zásobník běží na vyhrazených uzlech a v izolovaných hardwarových uzlech.

1. Vyberte **Všechny služby**.
2. Vyhledejte **služby AVS**.
3. Vyberte službu AVS, na které chcete vytvořit privátní cloud služby AVS.
4. V **přehledu**klikněte na **vytvořit privátní cloud** a otevřete novou kartu prohlížeče pro portál AVS. Pokud se zobrazí výzva, přihlaste se pomocí přihlašovacích údajů pro přihlášení do Azure. 

    ![Vytvoření privátního cloudu služby AVS z Azure](media/create-private-cloud-from-azure.png)

5. Na portálu služby AVS zadejte název vašeho privátního cloudu služby AVS.
6. Vyberte **umístění** vašeho privátního cloudu služby AVS.
7. Vyberte **typ uzlu**konzistentní s tím, co jste zřídili v Azure.
8. Zadejte **počet uzlů**. K vytvoření privátního cloudu služby AVS se vyžadují aspoň tři uzly.

    ![Vytvoření privátního cloudu pro funkci AVS – základní informace](media/create-private-cloud-basic-info.png)

9. Klikněte na **Další: Upřesnit možnosti**.
10. Zadejte rozsah CIDR pro podsítě vSphere/síti vSAN. Ujistěte se, že se rozsah CIDR nepřekrývá s žádnou místní nebo jinou podsítí Azure (virtuální sítě) nebo s podsítí brány.

    **Možnosti rozsahu CIDR:** /24,/23,/22 nebo/21. Rozsah CIDR a/24 podporuje až 26 uzlů, a/23 rozsah CIDR podporuje až 58 uzlů, a/22 a/21 rozsah CIDR podporuje 64 uzlů (maximální počet uzlů v privátním cloudu služby AVS). Další informace a sítě VLAN a podsítě najdete v tématu [Přehled sítí VLAN a podsítí](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > IP adresy v rozsahu vSphere/síti vSAN CIDR jsou vyhrazené pro použití v infrastruktuře privátního cloudu služby AVS. Nepoužívejte IP adresu v tomto rozsahu na žádném virtuálním počítači.

11. Klikněte na **Další: zkontrolovat a vytvořit**.
12. Zkontrolujte nastavení. Pokud potřebujete změnit nějaké nastavení, klikněte na tlačítko **Předchozí**.
13. Klikněte na **Vytvořit**.

Spustí se proces zřízení privátního cloudu AVS. Zřízení privátního cloudu služby AVS může trvat až dvě hodiny.

## <a name="launch-avs-portal"></a>Spustit portál AVS

Přístup k portálu služby AVS můžete získat z Azure Portal. Portál služby AVS se spustí s přihlašovacími údaji pro přihlášení k Azure pomocí jednotného přihlašování (SSO). Přístup k portálu služby AVS vyžaduje autorizaci aplikace pro **autorizaci služby AVS** . Další informace o udělení oprávnění najdete v tématu [vyjádření souhlasu s aplikací pro autorizaci služby AVS](access-cloudsimple-portal.md#consent-to-avs-service-authorization-application).

1. Vyberte **Všechny služby**.
2. Vyhledejte **služby AVS**.
3. Vyberte službu AVS, na které chcete vytvořit privátní cloud služby AVS.
4. V části Přehled klikněte na **Přejít na portál služby AVS** a otevřete novou kartu prohlížeče pro portál AVS. Pokud se zobrazí výzva, přihlaste se pomocí přihlašovacích údajů pro přihlášení do Azure. 

    ![Spustit portál AVS](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Vytvoření sítě VPN typu Point-to-site

Připojení VPN typu Point-to-site je nejjednodušší způsob, jak se připojit k privátnímu cloudu služby AVS z počítače. Připojení VPN typu Point-to-site použijte v případě, že se připojujete k privátnímu cloudu AVS vzdáleně. Pro rychlý přístup k privátnímu cloudu služby AVS použijte následující postup. Přístup k oblasti služby AVS z vaší místní sítě se dá provést pomocí [sítě VPN typu Site-to-site](vpn-gateway.md) nebo [Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Vytvořit bránu

1. Spusťte portál AVS a vyberte **síť**.
2. Vyberte **VPN Gateway**.
3. Klikněte na **nový VPN Gateway**.

    ![Vytvoření brány VPN](media/create-vpn-gateway.png)

4. V části **Konfigurace brány**zadejte následující nastavení a klikněte na **Další**.

    * Jako typ brány vyberte **síť VPN typu Point-to-site** .
    * Zadejte název pro identifikaci brány.
    * Vyberte umístění Azure, kde je vaše služba AVS nasazená.
    * Zadejte podsíť klienta pro bránu Point-to-site. Adresy DHCP budou předány z této podsítě, když se připojíte.

5. V poli **připojení/uživatel**zadejte následující nastavení a klikněte na tlačítko **Další**.

    * Pokud chcete automaticky všem současným a budoucím uživatelům udělit přístup k privátnímu cloudu služby AVS prostřednictvím této brány Point-to-site, vyberte možnost **automaticky přidat všechny uživatele**. Když vyberete tuto možnost, automaticky se vyberou všichni uživatelé v seznamu uživatelů. Automatickou možnost můžete přepsat tak, že zrušíte výběr jednotlivých uživatelů v seznamu.
    * Chcete-li vybrat pouze jednotlivé uživatele, klikněte na zaškrtávací políčka v seznamu uživatelů.

6. V části sítě VLAN a podsítě můžete zadat správu a sítě VLAN a uživatele pro bránu a připojení.

    * **Automatické přidávání** možností nastaví globální zásady pro tuto bránu. Nastavení platí pro aktuální bránu. Nastavení lze přepsat v oblasti **výběru** .
    * Vyberte **Přidat sítě VLAN pro správu/podsítě privátních cloudů**pro funkci AVS.
    * Pokud chcete přidat všechny sítě VLAN a podsítě definované uživatelem, klikněte na **Přidat uživatelsky definované sítě VLAN/podsítě**.
    * Nastavení **Vybrat** přepíše globální nastavení v části **automaticky přidat**.

7. Kliknutím na **Další** zkontrolujte nastavení. Kliknutím na ikony úprav proveďte požadované změny.
8. Kliknutím na **vytvořit** vytvořte bránu VPN.

### <a name="connect-to-avs-using-point-to-site-vpn"></a>Připojení k funkci AVS pomocí sítě VPN typu Point-to-site

Klient VPN je potřebný pro připojení k funkci AVS z počítače. Stáhněte si [klienta OpenVPN](https://openvpn.net/community-downloads/) pro Windows nebo [viskozitu](https://www.sparklabs.com/viscosity/download/) pro MacOS a OS X.

1. Spusťte portál AVS a vyberte **síť**.
2. Vyberte **VPN Gateway**.
3. V seznamu bran sítě VPN klikněte na bránu VPN typu Point-to-site.
4. Vyberte možnost **Uživatelé**.
5. Klikněte na **Stáhnout konfiguraci sítě VPN**.

    ![Stažení konfigurace zařízení VPN](media/download-p2s-vpn-configuration.png)

6. Naimportujte konfiguraci do svého klienta VPN.

    * Pokyny pro [Import konfigurace na klienta Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Pokyny pro [Import konfigurace v MacOS nebo OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Připojte se k funkci AVS.

## <a name="create-a-vlan-for-your-workload-vms"></a>Vytvoření sítě VLAN pro virtuální počítače s úlohami

Po vytvoření privátního cloudu služby AVS Vytvořte síť VLAN, do které budete nasazovat své pracovní a aplikační virtuální počítače.

1. Na portálu AVS vyberte **síť**.
2. Klikněte na **síť VLAN/podsítě**.
3. Klikněte na **vytvořit síť VLAN nebo podsíť**.

    ![Vytvořit síť VLAN nebo podsíť](media/create-new-vlan-subnet.png)

4. Vyberte **privátní cloud služby AVS** pro novou síť VLAN nebo podsíť.
5. Ze seznamu vyberte ID sítě VLAN. 
6. Zadejte název podsítě pro identifikaci podsítě.
7. Zadejte rozsah a masku směrování mezi podsítěmi. Tento rozsah se nesmí překrývat s žádnými stávajícími podsítěmi.
8. Klikněte na **Submit** (Odeslat).

    ![Vytvořit podrobnosti o síti VLAN nebo podsíti](media/create-new-vlan-subnet-details.png)

Vytvoří se síť VLAN nebo podsíť. Teď můžete pomocí tohoto ID sítě VLAN Vytvořit distribuovanou skupinu portů na serveru vCenter privátního cloudu služby AVS.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Připojení prostředí k virtuální síti Azure

K dispozici je okruh ExpressRoute pro váš privátní cloud služby AVS. Virtuální síť v Azure můžete připojit k okruhu ExpressRoute. Úplné informace o nastavení připojení najdete v postupu v části [připojení k Azure Virtual Network pomocí ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/).

## <a name="sign-in-to-vcenter"></a>Přihlásit se k vCenter

Nyní se můžete přihlásit k vCenter a nastavit virtuální počítače a zásady.

1. Pro přístup k serveru vCenter spusťte z portálu AVS. Na domovské stránce v části **běžné úlohy**klikněte na **Spustit klienta vSphere**. Vyberte privátní cloud AVS a pak klikněte na **Spustit klienta vSphere** v privátním cloudu AVS.

    ![Spustit klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Vyberte preferovaného klienta vSphere pro přístup k serveru vCenter a přihlaste se pomocí uživatelského jména a hesla. Výchozí hodnoty jsou:
    * Uživatelské jméno: **CloudOwner@AVS.local**
    * Heslo: **AVS123!**  

Obrazovky vCenter v následujících postupech jsou od klienta vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Změna hesla vCenter

Po prvním přihlášení k serveru vCenter doporučuje aplikace AVS změnit heslo. Heslo, které jste nastavili, musí splňovat následující požadavky:

* Maximální doba života: heslo se musí změnit každých 365 dní.
* Omezit opakované použití: uživatelé nemůžou znovu použít žádné z předchozích pěti hesel.
* Délka: 8-20 znaků
* Speciální znak: aspoň jeden speciální znak
* Abecední znaky: aspoň jedno velké písmeno, A-Z a aspoň jedno malé písmeno, a-z
* Numbers: alespoň jeden numerický znak, 0-9
* Maximální počet identických sousedících znaků: 3

    Příklad: CC nebo CCC je přijatelné jako součást hesla, ale CCCC ne.

Pokud nastavíte heslo, které nesplňuje požadavky:

* Pokud používáte klienta vSphere Flash, nahlásí chybu.
* Pokud používáte klienta HTML5, nehlásí chybu. Klient nepřijme změnu a původní heslo bude nadále fungovat.

## <a name="access-nsx-manager"></a>Přístup k NSX Manageru

NSX Manager je nasazen s výchozím heslem. 

* Uživatelské jméno: **správce**
* Heslo: **AVS123!**

Plně kvalifikovaný název domény (FQDN) a IP adresa NSX Manageru najdete na portálu pro funkci AVS.

1. Spusťte portál AVS a vyberte **prostředky**.
2. Klikněte na privátní cloud AVS, který chcete použít.
3. Vybrat **síť pro správu vSphere**
4. Použijte plně kvalifikovaný název domény nebo IP adresu **NSX Manageru** a připojte se pomocí webového prohlížeče.

    ![Najít plně kvalifikovaný název domény NSX Manageru](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Vytvoření skupiny portů

Postup vytvoření distribuované skupiny portů v vSphere:

1. Postupujte podle pokynů v části "Přidání distribuované skupiny portů" v tématu [Průvodce sítí vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Při nastavování distribuované skupiny portů zadejte ID sítě VLAN vytvořené v části [vytvoření sítě VLAN pro vaše virtuální počítače s úlohami](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Další kroky

* [Využití virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* [Připojení k místní síti pomocí Azure ExpressRoute](on-premises-connection.md)
* [Nastavení VPN typu Site-to-Site z místního prostředí](vpn-gateway.md)
