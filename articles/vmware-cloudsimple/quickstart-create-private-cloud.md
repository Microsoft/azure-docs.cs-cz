---
title: Řešení Azure VMware podle CloudSimple rychlý start – vytvoření privátního cloudu
description: Zjistěte, jak vytvořit a nakonfigurovat řešení VMware Azure podle CloudSimple privátního cloudu
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efeadd2e1600e5ee572c4a69dde0ff4c53a13cd7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577657"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Rychlý start – konfigurace prostředí privátního cloudu

V tomto článku zjistěte, jak vytvořit privátní cloud CloudSimple a nastavení prostředí privátního cloudu.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Vytvoření privátního cloudu

1. Vyberte **Všechny služby**.
2. Vyhledejte **CloudSimple služby**.
3. Vyberte službu CloudSimple, na kterém chcete vytvořit privátní Cloud.
4. Přehled, klikněte na tlačítko **vytvoření privátního cloudu** otevřít na nové kartě prohlížeče CloudSimple portálu.  Pokud se zobrazí výzva, přihlaste se pomocí Azure přihlášení pověření.  

    ![Vytvoření privátního cloudu z Azure](media/create-private-cloud-from-azure.png)

5. Na portálu CloudSimple zadejte název privátního cloudu
6. Vyberte **umístění** z privátního cloudu
7. Vyberte **typ uzlu** jste zakoupili v Azure.  Můžete použít [CS28 nebo CS36 možnost](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). Druhou možnost zahrnuje maximální kapacitu výpočetní a paměťové prostředky.
8. Zadejte **počet uzlů**.  Minimálně tři uzly jsou nutné k vytvoření privátního cloudu

    ![Vytvoření privátního cloudu – základní informace](media/create-private-cloud-basic-info.png)

9. Klikněte na tlačítko **Další: Rozšířené možnosti**.
10. Zadejte rozsah CIDR. pro podsítě vSphere/síti vSAN. Ujistěte se, že rozsah CIDR se nepřekrývá s žádným z místní nebo jiných podsítích Azure.

    ![Vytvoření privátního cloudu – Pokročilá nastavení](media/create-private-cloud-advanced-options.png)

11. Vyberte **Další: Zkontrolovat a vytvořit**.
12. Zkontrolujte nastavení. Pokud potřebujete změnit libovolné nastavení, klikněte na tlačítko **předchozí**.
13. Klikněte na možnost **Vytvořit**.

Spustí se proces zajišťování privátního cloudu.  Může trvat až dvě hodiny pro privátní Cloud, které se mají zřídit.

## <a name="launch-cloudsimple-portal"></a>Spusťte portál CloudSimple

CloudSimple portálu můžete přistupovat z webu Azure portal.  Spustí se portál CloudSimple s Azure přihlášení přihlašovací údaje pomocí jednotného přihlašování (SSO).  Přístup k portálu CloudSimple vyžaduje autorizaci **autorizace služby CloudSimple** aplikace.  Další informace o udělení oprávnění najdete v tématu [souhlas s aplikací CloudSimple autorizace služby](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application)

1. Vyberte **Všechny služby**.
2. Vyhledejte **CloudSimple služby**.
3. Vyberte službu CloudSimple, na kterém chcete vytvořit privátní Cloud.
4. Přehled, klikněte na tlačítko **přejít na portál CloudSimple** otevřít na nové kartě prohlížeče CloudSimple portálu.  Pokud se zobrazí výzva, přihlaste se pomocí Azure přihlášení pověření.  

    ![Spusťte portál CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Vytvoření sítě VPN point-to-site

Připojení VPN typu point-to-site je nejjednodušší způsob, jak připojit z počítače do privátního cloudu. Pomocí připojení VPN typu point-to-site, pokud se připojujete k privátní Cloud vzdáleně.  Rychlý přístup k vaší privátní Cloud postupujte podle následujících kroků.  Přístup k oblasti CloudSimple z vaší místní sítě lze provést pomocí [Site-to-Site VPN](https://docs.azure.cloudsimple.com/vpn-gateway/) nebo [Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/).

### <a name="create-gateway"></a>Vytvoření brány

1. Spuštění CloudSimple portal a vyberte **sítě**.
2. Vyberte **VPN Gateway**.
3. Klikněte na tlačítko **novou bránu VPN**.

    ![Vytvoření brány VPN](media/create-vpn-gateway.png)

4. Pro **konfigurace brány**, nakonfigurujte následující nastavení a klikněte na tlačítko **Další**.

    * Vyberte **Point-to-Site VPN** jako typ brány.
    * Zadejte název pro identifikaci brány.
    * Vyberte umístění Azure, ve kterém je nasazená CloudSimple služby.
    * Zadejte podsíť klienta pro danou bránu point-to-site.  Když se připojíte, se z této podsítě dostanou adres DHCP.

5. Pro **připojení/uživatele**, nakonfigurujte následující nastavení a klikněte na tlačítko **Další**.

    * Pokud chcete, aby automaticky aktuální a budoucí uživatelé pro přístup k privátním cloudu přes tuto bránu point-to-site, vyberte **automaticky přidat všechny uživatele**. Když vyberete tuto možnost, jsou automaticky vybrané všechny uživatele v seznamu uživatelů. Možnost automatického můžete přepsat tak, že zrušením výběru jednotlivých uživatelů v seznamu.
    * Pokud chcete vybrat pouze jednotlivé uživatele, klikněte na zaškrtávací políčka v seznamu uživatelů.

6. V části sítě VLAN a podsítě umožňuje určit správy a uživatele sítě VLAN a podsítě brány a připojení.

    * **Automaticky přidat** možnosti nastavení globálních zásad pro tuto bránu. Nastavení platí pro aktuální brány. Nastavení mohou být přepsána nastaveními v **vyberte** oblasti.
    * Vyberte **přidat Správa sítí VLAN a podsítě privátních cloudů**. 
    * Chcete-li přidat všechny uživatelem definované sítě VLAN a podsítě, klikněte na tlačítko **přidání uživatelem definované sítě VLAN a podsítě**. 
    * **Vyberte** nastavení přepsat globální nastavení v části **automaticky přidat**. 

7. Klikněte na tlačítko **Další** a zkontrolujte nastavení. Klikněte na tlačítko Upravit ikony žádné změny.
8. Klikněte na tlačítko **vytvořit** a vytvořte bránu VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Připojte se k CloudSimple pomocí sítě VPN typu point-to-site

Klient VPN je potřeba pro připojení k CloudSimple z vašeho počítače.  Stáhněte si [OpenVPN klienta](https://openvpn.net/community-downloads/) pro Windows nebo [viskozita](https://www.sparklabs.com/viscosity/download/) pro macOS a OS X.

1. Spuštění CloudSimple portal a vyberte **sítě**.
2. Vyberte **VPN Gateway**.
3. V seznamu bran VPN klikněte na bráně VPN typu point-to-site.
4. Vyberte **uživatelé**.
5. Klikněte na **stáhnout konfiguraci sítě VPN**

    ![Stažení konfigurace zařízení VPN](media/download-p2s-vpn-configuration.png)

6. Importujte konfiguraci na klienta VPN

    * Pokyny, jak [importu konfigurace v klientovi Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Pokyny, jak [importu konfigurace v systému macOS nebo OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Připojte se k CloudSimple

## <a name="create-a-vlan-for-your-workload-vms"></a>Vytvoření sítě VLAN pro úlohy virtuálních počítačů

Po vytvoření privátního cloudu, vytvořte síť VLAN, kde nasadíte úloh/aplikace virtuálních počítačů.

1. Na portálu CloudSimple vyberte **sítě**.
2. Klikněte na tlačítko **sítě VLAN a podsítě**.
3. Klikněte na tlačítko **vytvoření sítě VLAN a podsítě**

    ![Vytvoření sítě VLAN a podsítě](media/create-new-vlan-subnet.png)

4. Vyberte **privátního cloudu** pro nové podsítě/sítě VLAN.
5. Vyberte ze seznamu ID sítě VLAN.  
6. Zadejte název podsítě určující podsíť.
7. Zadejte rozsah CIDR podsítě a masku.  Tato oblast se nesmí překrývat s existující podsítí.
8. Klikněte na **Submit** (Odeslat).

    ![Vytvoření podrobnosti o síti VLAN a podsítě](media/create-new-vlan-subnet-details.png)

Vytvoří se sítě VLAN a podsítě.  Toto ID VLAN nyní slouží k vytvoření skupiny distribuované port na vCenter privátního cloudu. 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Svoje prostředí připojit ke službě Azure virtual network

CloudSimple vám poskytne okruh ExpressRoute pro privátní cloud. Službě virtual network v Azure můžete připojit k okruhu ExpressRoute. Úplné podrobnosti o nastavení připojení postupujte podle kroků v [Azure virtuální síťové připojení pomocí ExpressRoute](https://docs.azure.cloudsimple.com/azure-er-connection)

## <a name="sign-in-to-vcenter"></a>Přihlaste se k serveru vCenter

Teď se můžete přihlásit k vCenter pro nastavení virtuálních počítačů a zásad.

1. Pro přístup k serveru vCenter, spusťte z portálu CloudSimple. Na domovské stránce v části **běžné úkoly**, klikněte na tlačítko **spuštění vSphere klienta**.  Vyberte privátní Cloud a poté klikněte na tlačítko **spuštění vSphere klienta** na privátní Cloud.

    ![Spuštění vSphere klienta](media/launch-vcenter-from-cloudsimple-portal.png)

2. Vyberte váš klient upřednostňované vSphere pro přístup k serveru vCenter a přihlaste se pomocí uživatelského jména a hesla.  Výchozí hodnoty jsou:
    * Uživatelské jméno: **CloudOwner@cloudsimple.local**
    * Heslo: **CloudSimple123!**  

VCenter obrazovky v dalším postupu jsou z klienta vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Změna hesla vCenter

CloudSimple doporučuje, abyste si změnili heslo při prvním přihlášení k serveru vCenter.  
Heslo, které nastavíte, musí splňovat následující požadavky:

* Maximální doba života: Každý 365 dnů musí změnit heslo
* Zakázat opakované použití: Uživatele nelze znovu použít některé z předchozích pět hesel
* Délka: 8 – 20 znaků
* Speciální znak. Aspoň jeden speciální znak
* Abecední znaky: Minimálně jedno velké písmeno, A-Z a aspoň jedno malé písmeno, a – z
* Čísla: Alespoň jeden číselný znak, 0-9
* Maximální počet znaků sousední shodné: Tři

    Příklad: Kopie nebo kopie je přijatelné jako součást heslo, ale není CCCC.

Pokud jste nastavili heslo nesplňuje požadavky:

* Pokud používáte Flash klienta vSphere, hlásí chybu
* Pokud používáte klienta HTML5, není hlášena chyba. Klient nepodporuje změnu přijmout, a nadále fungovat původní heslo.

## <a name="change-nsx-administrator-password"></a>Změna hesla správce NSX

Správce NSX se nasazuje s výchozí heslo.  Doporučujeme, abyste že po vytvoření privátního cloudu se změnit heslo.

   * Uživatelské jméno: **správce**
   * Heslo: **CloudSimple123!**

Plně kvalifikovaný název domény (FQDN) a IP adresa správce NSX najdete na portálu CloudSimple.

1. Spuštění CloudSimple portal a vyberte **prostředky**.
2. Klikněte na privátní Cloud, který chcete použít.
3. Vyberte **vSphere síť pro správu**
4. Použijte plně kvalifikovaný název domény nebo IP adresu **NSX správce** a připojte se pomocí webového prohlížeče. 

    ![Najít NSX správce plně kvalifikovaný název domény](media/private-cloud-nsx-manager-fqdn.png)

Chcete-li změnit heslo, postupujte podle pokynů v [NSX Správce instalace](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html).

## <a name="create-a-port-group"></a>Vytvořte skupinu portů

K vytvoření skupiny distribuované portu v vSphere:

1. Postupujte podle pokynů v "Přidat skupinu distribuované portu" v [vSphere sítě Průvodce](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Při nastavování skupiny distribuované port, zadejte ID sítě VLAN, které jsou vytvořené v [vytvoření sítě VLAN pro úlohy virtuálních počítačů](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Další postup

* [Využívání virtuálních počítačů VMware v Azure](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* [Připojit k místní síti pomocí Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Nastavení sítě VPN Site-to-Site z místní](https://docs.azure.cloudsimple.com/vpn-gateway/)
