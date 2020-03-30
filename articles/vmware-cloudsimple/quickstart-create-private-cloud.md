---
title: 'Úvodní příručka: Vytvoření privátního cloudu'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Zjistěte, jak vytvořit a nakonfigurovat privátní cloud pomocí řešení Azure VMware od CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7460490dbd45862f4269d25e3910373700ec9a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564716"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Úvodní příručka – konfigurace prostředí privátního cloudu

V tomto článku se dozvíte, jak vytvořit cloudový cloudový cloud a nastavit prostředí privátního cloudu.

## <a name="before-you-begin"></a>Než začnete

Zkontrolujte [požadavky sítě](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="create-a-private-cloud"></a>Vytvoření privátního cloudu

Privátní cloud je izolovaný zásobník VMware, který podporuje hostitele ESXi, vCenter, vSAN a NSX.

Privátní cloudy jsou spravovány prostřednictvím portálu CloudSimple. Mají svůj vlastní server vCenter ve své vlastní doméně pro správu. Zásobník běží na vyhrazených uzlech a izolovaných holé kovové hardwarové uzly.

1. Vyberte **Všechny služby**.
2. Vyhledejte **cloudové jednoduché služby**.
3. Vyberte službu CloudSimple, na které chcete vytvořit privátní cloud.
4. V **části Přehled**klikněte na Vytvořit **privátní cloud** a otevřete novou kartu prohlížeče pro portál CloudSimple.  Pokud se zobrazí výzva, přihlaste se pomocí přihlašovacích údajů k Azure.  

    ![Vytvoření privátního cloudu z Azure](media/create-private-cloud-from-azure.png)

5. Na portálu CloudSimple zadejte název privátního cloudu.
6. Vyberte **umístění** svého privátního cloudu.
7. Vyberte **typ uzlu**, konzistentní s tím, co jste zřídit v Azure.
8. Zadejte **počet uzlů**.  K vytvoření privátního cloudu jsou potřeba alespoň tři uzly.

    ![Vytvořit privátní cloud – základní informace](media/create-private-cloud-basic-info.png)

9. Klikněte na **Další: Upřesnit možnosti**.
10. Zadejte rozsah CIDR pro podsítě vSphere/vSAN. Ujistěte se, že rozsah CIDR se nepřekrývá s žádnou z vašich místních nebo jiných podsítí Azure (virtuálních sítí) nebo s podsítí brány.

    **Možnosti rozsahu CIDR:** /24, /23, /22 nebo /21. Rozsah CIDR /24 podporuje až 26 uzlů, rozsah CIDR /23 podporuje až 58 uzlů a rozsah CIDR /22 a /21 podporuje 64 uzlů (maximální počet uzlů v privátním cloudu).  Další informace a sítě VLAN a podsítě naleznete [v tématu Přehled sítí VLAN a podsítí](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > IP adresy v rozsahu CIDR vSphere/vSAN jsou vyhrazeny pro použití infrastrukturou Privátního cloudu.  Nepoužívejte IP adresu v tomto rozsahu na žádném virtuálním počítači.

11. Klikněte na **Další: Zkontrolujte a vytvořte**.
12. Zkontrolujte nastavení. Pokud potřebujete změnit nastavení, klepněte na tlačítko **Předchozí**.
13. Klikněte na **Vytvořit**.

Proces zřizování privátního cloudu se spustí.  Zřízení privátního cloudu může trvat až dvě hodiny.

## <a name="launch-cloudsimple-portal"></a>Spuštění portálu CloudSimple

Přístup k portálu CloudSimple můžete přistupovat z portálu Azure.  Portál CloudSimple se spustí s přihlašovacími přihlašovacími údaji pro přihlášení do Azure pomocí jednotného přihlašování (SSO).  Přístup k portálu CloudSimple vyžaduje autorizaci aplikace **CloudSimple Service Authorization.**  Další informace o udělení oprávnění naleznete v [tématu Consent to CloudSimple Service Authorization application](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application).

1. Vyberte **Všechny služby**.
2. Vyhledejte **cloudové jednoduché služby**.
3. Vyberte službu CloudSimple, na které chcete vytvořit privátní cloud.
4. V přehledu klikněte na **Přejít na portál CloudSimple** a otevřete novou kartu prohlížeče pro portál CloudSimple.  Pokud se zobrazí výzva, přihlaste se pomocí přihlašovacích údajů k Azure.  

    ![Spuštění portálu CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Vytvoření sítě VPN z bodu na web

Připojení VPN z bodu na místo je nejjednodušší způsob, jak se připojit k privátnímu cloudu z počítače. Pokud se připojujete k privátnímu cloudu vzdáleně, použijte připojení VPN z bodu na místo.  Chcete-li získat rychlý přístup k privátnímu cloudu, postupujte podle následujících kroků.  Přístup k oblasti CloudSimple z místní sítě lze provést pomocí [sítě SITE-to-Site VPN](vpn-gateway.md) nebo [Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Vytvořit bránu

1. Spusťte portál CloudSimple a vyberte **možnost Síť**.
2. Vyberte **bránu VPN**.
3. Klepněte na **položku Nová brána VPN**.

    ![Vytvoření brány VPN](media/create-vpn-gateway.png)

4. V **části Konfigurace brány**zadejte následující nastavení a klepněte na tlačítko **Další**.

    * Jako typ brány vyberte **bod na web VPN.**
    * Zadejte název pro identifikaci brány.
    * Vyberte umístění Azure, kde se vaše služba CloudSimple nasazuje.
    * Zadejte podsíť klienta pro bránu typu Point-to-Site.  Při připojení budou z této podsítě uvedeny adresy DHCP.

5. V **poli Připojení/Uživatel**zadejte následující nastavení a klepněte na tlačítko **Další**.

    * Chcete-li automaticky povolit všem současným i budoucím uživatelům přístup k privátnímu cloudu prostřednictvím této brány point-to-site, vyberte **Možnost Automaticky přidat všechny uživatele**. Vyberete-li tuto možnost, budou automaticky vybráni všichni uživatelé v seznamu Uživatel. Automatickou volbu můžete přepsat zrušením výběru jednotlivých uživatelů v seznamu.
    * Chcete-li vybrat pouze jednotlivé uživatele, zaškrtněte políčka v seznamu Uživatel.

6. Část Sítě VLAN/podsítě umožňuje určit správu a uživatelské sítě VLAN/podsítě pro bránu a připojení.

    * **Možnosti automatického přidání** nastavují globální zásady pro tuto bránu. Nastavení platí pro aktuální bránu. Nastavení lze přepsat v oblasti **Vybrat.**
    * Vyberte **Přidat správu sítí VLAN/podsítí privátních cloudů**.
    * Chcete-li přidat všechny uživatelem definované sítě VLAN/podsítě, klepněte na tlačítko **Přidat uživatelem definované sítě VLAN/podsítě**.
    * Nastavení **výběru** přepíše globální nastavení v části **Automaticky přidat**.

7. Chcete-li zkontrolovat nastavení, klepněte na tlačítko **Další.** Kliknutím na ikony úprav proveďte změny.
8. Kliknutím na **Vytvořit** vytvořte bránu VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Připojení k CloudSimple pomocí technologie Point-to-Site VPN

Klient VPN je potřebný pro připojení k CloudSimple z vašeho počítače.  Stáhněte si [klienta OpenVPN](https://openvpn.net/community-downloads/) pro Windows nebo [Viskozitu](https://www.sparklabs.com/viscosity/download/) pro macOS a OS X.

1. Spusťte portál CloudSimple a vyberte **možnost Síť**.
2. Vyberte **bránu VPN**.
3. V seznamu bran VPN klikněte na bránu VPN point-to-site.
4. Vyberte **Uživatelé**.
5. Klepněte na tlačítko **Stáhnout konfiguraci sítě VPN**.

    ![Stažení konfigurace zařízení VPN](media/download-p2s-vpn-configuration.png)

6. Importujte konfiguraci v klientovi VPN.

    * Pokyny pro [import konfigurace v klientovi systému Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Pokyny pro [import konfigurace v macOS nebo OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Připojte se k CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Vytvoření sítě VLAN pro virtuální počítače s úlohou

Po vytvoření privátního cloudu vytvořte síť VLAN, do které nasadíte virtuální počítače s úlohami nebo aplikacemi.

1. Na portálu CloudSimple vyberte **možnost Síť**.
2. Klepněte na **položku VLAN/podsítě**.
3. Klepněte na **tlačítko Vytvořit síť VLAN/podsíť**.

    ![Vytvořit síť VLAN/podsíť](media/create-new-vlan-subnet.png)

4. Vyberte **privátní cloud** pro novou síť VLAN/podsíť.
5. V seznamu vyberte ID sítě VLAN.  
6. Zadejte název podsítě k identifikaci podsítě.
7. Určete rozsah a masku PODSítě CIDR.  Tato oblast se nesmí překrývat s existujícími podsítěmi.
8. Klepněte na **tlačítko Odeslat**.

    ![Vytvoření podrobností o síti VLAN/podsíti](media/create-new-vlan-subnet-details.png)

Bude vytvořena síť VLAN/podsíť.  Nyní můžete použít toto ID sítě VLAN k vytvoření skupiny distribuovaných portů v programu Private Cloud vCenter.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Připojení prostředí k virtuální síti Azure

CloudSimple poskytuje okruh ExpressRoute pro váš privátní cloud. Virtuální síť v Azure můžete připojit k okruhu ExpressRoute. Podrobné informace o nastavení připojení naleznou v části [Připojení virtuální sítě Azure pomocí služby ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/).

## <a name="sign-in-to-vcenter"></a>Přihlášení k virtuálnímu centru

Teď se můžete přihlásit k vCenter nastavit virtuální počítače a zásady.

1. Chcete-li získat přístup k programu vCenter, začněte z portálu CloudSimple. Na domovské stránce klikněte v části **Běžné úkoly**na **tlačítko Spustit klienta vSphere**.  Vyberte privátní cloud a klikněte na **Spustit klienta vSphere** v privátním cloudu.

    ![Spuštění klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Vyberte svého preferovaného klienta vSphere pro přístup k vCenter a přihlaste se pomocí svého uživatelského jména a hesla.  Výchozí hodnoty jsou:
    * Uživatelské jméno: **CloudOwner\@cloudsimple.local**
    * Heslo: **CloudSimple123!**  

Obrazovky vCenter v dalších postupech jsou z klienta vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Změna hesla virtuálního centra

CloudSimple doporučuje změnit heslo při prvním přihlášení do vCenter.  
Heslo, které nastavíte, musí splňovat následující požadavky:

* Maximální životnost: Heslo musí být měněno každých 365 dní
* Omezit opakované použití: Uživatelé nemohou znovu použít žádné z předchozích pěti hesel.
* Délka: 8 - 20 znaků
* Speciální znak: Alespoň jeden speciální znak
* Abecední znaky: Alespoň jeden znak s velkou písmenou, A-Z a alespoň jeden znak s malou písmena, a-z
* Čísla: Alespoň jeden číselný znak, 0-9
* Maximální identické sousední znaky: Tři

    Příklad: CC nebo CCC je přijatelná jako součást hesla, ale CCCC není.

Pokud nastavíte heslo, které nesplňuje požadavky:

* Pokud používáte klienta vSphere Flash, hlásí chybu
* Pokud použijete klienta HTML5, nehlásí chybu. Klient změnu nepřijme a staré heslo bude nadále fungovat.

## <a name="access-nsx-manager"></a>Přístup k nsx manažerovi

Správce NSX je nasazen s výchozím heslem. 

* Uživatelské jméno: **admin**
* Heslo: **CloudSimple123!**

Plně kvalifikovaný název domény (FQDN) a IP adresu nsx manažera najdete na portálu CloudSimple.

1. Spusťte portál CloudSimple a vyberte **Zdroje**.
2. Klikněte na privátní cloud, který chcete použít.
3. Vybrat **síť pro správu vSphere**
4. Použijte hlavní obchodní adresu nebo IP adresu **NSX Managera** a připojte se pomocí webového prohlížeče.

    ![Najít FSDN manažera NSX](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Vytvoření skupiny portů

Vytvoření skupiny distribuovaných portů ve vSphere:

1. Postupujte podle pokynů v části "Přidat skupinu distribuovaných portů" v [průvodci sítí vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Při nastavování skupiny distribuovaných portů zadejte ID sítě VLAN vytvořené v [části Vytvoření sítě VLAN pro virtuální počítače s pracovními vytížením](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Další kroky

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* [Připojení k místní síti pomocí Azure ExpressRoute](on-premises-connection.md)
* [Nastavení sítě VPN mezi webovými servery z místního prostředí](vpn-gateway.md)
