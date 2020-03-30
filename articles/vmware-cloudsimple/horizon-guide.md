---
title: Řešení Azure VMware by CloudSimple – použití privátního cloudového webu k hostování infrastruktury virtuálních desktopů pomocí technologie VMware Horizon
description: Popisuje, jak můžete pomocí webu CloudSimple Private Cloud hostovat infrastrukturu virtuálních desktopů pomocí technologie VMware Horizon.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025244"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Použití webu CloudSimple Private Cloud k hostování infrastruktury virtuálních desktopů pomocí technologie VMware Horizon

Pomocí webu CloudSimple Private Cloud můžete hostovat infrastrukturu virtuálních desktopů (VDI) pomocí platformy VMware Horizon 7.x. Následující obrázek znázorňuje architekturu logického řešení pro VDI.

![Nasazení Horizon](media/horizon-deployment.png)

S tímto řešením máte plnou kontrolu nad Horizon View Manager a App Volume. Známá rozhraní uživatelského rozhraní, rozhraní API a rozhraní rozhraní se konstatování umožňují použití existujících skriptů a nástrojů.

CloudSimple řešení vyžaduje, abyste provést následující kroky:

* Instalujte, konfigurujte a spravujte v privátní cloud.
* Poskytněte si vlastní licence Horizon.

## <a name="deploy-the-solution"></a>Nasazení řešení

Následující části popisují, jak nasadit řešení VDI pomocí programu Horizont ve vašem privátním cloudu.

1. [Ověřte, zda jsou verze produktů VMware kompatibilní](#verify-that-vmware-product-versions-are-compatible)
2. [Odhad velikosti desktopového prostředí](#estimate-the-size-of-your-desktop-environment)
3. [Vytvoření privátního cloudu pro vaše prostředí](#create-a-private-cloud-for-your-environment)
4. [Instalace zařízení VMware Horizon do svého privátního cloudu](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Ověřte, zda jsou verze produktů VMware kompatibilní

* Ověřte, zda jsou vaše aktuální a plánované verze horizon, svazků aplikací, sjednocené přístupové brány a Správce uživatelského prostředí kompatibilní mezi sebou navzájem a s vCenter a PSC v privátním cloudu. Informace o kompatibilitě naleznete [v tématu VMware Compatibility Matrix for Horizon 7.5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Chcete-li zjistit aktuální verze vCenter a PSC ve vašem privátním cloudu, přejděte na **zdroje** na [portálu CloudSimple](access-cloudsimple-portal.md), vyberte privátní cloud a klikněte na kartu **vSphere Management Network** .

![verze vCenter a PSC](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Odhad velikosti desktopového prostředí

* Ověřte, zda je vaše identifikovaná konfigurace v rámci provozních limitů společnosti VMware.
* Odhadněte prostředky, které jsou potřebné pro všechny plochy a součásti správy Horizon.

### <a name="create-a-private-cloud-for-your-environment"></a>Vytvoření privátního cloudu pro vaše prostředí

1. Vytvořte privátní cloud z portálu CloudSimple podle pokynů v [části Konfigurace prostředí privátního cloudu](quickstart-create-private-cloud.md).  CloudSimple vytvoří výchozí ho uživatele vCenter s názvem "cloudowner" v každém nově vytvořeném privátním cloudu. Podrobnosti o výchozím modelu privátního cloudu a modelu oprávnění najdete [v tématu Naučte se model oprávnění privátního cloudu](learn-private-cloud-permissions.md).
2. Vytvořte síť VLAN ve svém privátním cloudu pro rovinu správy Horizon a přiřaďte mu podsíť CIDR. Pokyny naleznete v [tématu Vytvoření a správa sítí VLAN/podsítí](create-vlan-subnet.md). Jedná se o síť, ve které budou nainstalovány všechny součásti řešení (Unified Access Gateway, Connection Server, App Volume Server a User Environment Manager server).
3. Rozhodněte se, jestli chcete použít externího poskytovatele identity s privátním cloudem vCenter. Pokud ano, zvolte jednu z těchto možností:
    * Jako externího zprostředkovatele identity použijte místní službu Active Directory. Pokyny naleznete [v tématu vCenter Identity Sources](set-vcenter-identity.md).
    * Nastavte server Služby Active Directory v síti Private Cloud v síti Horizon management VLAN, který bude používat jako externí zprostředkovatel identity. Pokyny naleznete [v tématu vCenter Identity Sources](set-vcenter-identity.md).
    * Nastavte server DHCP a DNS v síti VLAN pro správu horizon v privátním cloudu. Pokyny najdete v tématu [Nastavení aplikací a úloh DNS a DHCP v privátním cloudu CloudSimple](dns-dhcp-setup.md).
4. Nakonfigurujte předávání DNS na serveru DNS nainstalovaném v privátním cloudu. Pokyny naleznete [v tématu Vytvoření podmíněného předávání](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Instalace zařízení VMware Horizon do svého privátního cloudu

Následující diagram nasazení znázorňuje řešení Horizon nasazené v privátním cloudu. Sjednocená přístupová brána, služba AD/DC, zobrazení a server svazků aplikací jsou nainstalovány v síti VLAN 234 vytvořené uživatelem. Sjednocená přístupová brána má přiřazenou veřejnou IP adresu, která je dostupná z Internetu. Virtuální počítače Horizon fondu desktop jsou nasazeny v Síti VLAN 235 poskytovat další izolaci a zabezpečení.

![Nasazení Horizon v privátním cloudu](media/horizon-private-cloud.png)

V následujících částech jsou uvedeny pokyny k nastavení nasazení podobnému tomu, které je znázorněno na obrázku. Než začnete, ověřte, zda máte následující:

* Privátní cloud vytvořený pomocí portálu CloudSimple s dostatečnou kapacitou pro spouštění fondů stolních počítačů.
* Dostatečná šířka pásma mezi místním prostředím a prostředím Privátního cloudu pro podporu síťového provozu pro vaše stolní počítače.
* Tunelové propojení VPN mezi webem a webem, které se nastaví mezi místním datovým centrem a privátním cloudem.
* Dostupnost IP adres z podsítí koncových uživatelů v místním prostředí do podsítí CloudSimple Private Cloud.
* AD/DHCP/DNS nainstalované pro váš privátní cloud.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>Portál CloudSimple: Vytvoření vyhrazené sítě VLAN/podsítě pro fondy stolních počítačů

Vytvořte síť VLAN pro fondy stolních počítačů Horizon a přiřaďte mu podsíť CIDR. Pokyny naleznete v [tématu Vytvoření a správa sítí VLAN/podsítí](create-vlan-subnet.md). Jedná se o síť, kde budou spuštěny všechny desktopové virtuální počítače.

Při zabezpečení nasazení programu Horizon postupujte podle standardních doporučených postupů zabezpečení:

* Povolit pouze desktop RDP provoz / SSH provoz na ploše virtuálních počítačů.
* Povolit pouze provoz správy mezi horizon management roviny VLAN a stolní fond VLAN.
* Povolit pouze provoz správy z místní sítě.

Tyto osvědčené postupy můžete vynutit konfigurací [pravidel brány firewall](firewall.md) z portálu CloudSimple.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Portál CloudSimple: Konfigurace pravidel brány firewall pro zabezpečení roviny správy Horizon

Nastavte následující pravidla na portálu CloudSimple. Pokyny naleznete v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md).

1. Nakonfigurujte pravidla brány firewall v bráně firewall CloudSimple N-S tak, aby umožňovala komunikaci mezi místními podsítěmi a sítí VLAN pro správu Horizon, aby byly povoleny pouze síťové porty uvedené v [seznamu portů Horizon](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) dokumentu VMware.

2. Vytvořte pravidla brány firewall E-W mezi sítí Horizon management VLAN a fondem stolních počítačů VLAN v privátním cloudu.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>Portál CloudSimple: Vytvoření veřejné IP adresy pro sjednocenou přístupovou bránu

Vytvořte veřejnou IP adresu pro zařízení Sjednocená přístupová brána, která umožní připojení desktopových klientů z Internetu. Pokyny naleznete v tématu [Přidělení veřejných IP adres](public-ips.md).

Po dokončení instalace je veřejná IP adresa přiřazena a uvedena na stránce Veřejné IP adresy.

#### <a name="cloudsimple-portal-escalate-privileges"></a>Portál CloudSimple: Eskalovat oprávnění

Výchozí uživatel "cloudowner" nemá v programu Private Cloud vCenter dostatečná oprávnění k instalaci programu Horizont, takže oprávnění vcenter uživatele musí být eskalována. Další informace naleznete v tématu [Escalate privileges](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>uživatelské rozhraní vCenter: Vytvoření uživatele v privátním cloudu pro instalaci programu Horizon

1. Přihlaste se k vCenter pomocí přihlašovacích údajů uživatele vlastníka cloudu.
2. Vytvořte nového uživatele, 'horizon-soln-admin', v vCenter a přidejte uživatele do skupiny administrators v vCenter.
3. Odhlaste se z vCenter jako uživatel "cloudowner" a přihlaste se jako uživatel "horizon-soln-admin".

#### <a name="vcenter-ui-install-vmware-horizon"></a>Uvcenter vCenter: Instalace vmware Horizon

Jak je uvedeno v části dřívější logické architektury, řešení Horizon má následující součásti:

* Zobrazení VMware Horizon
* Sjednocená přístupová brána VMware
* Správce svazků aplikací VMware
* Správce uživatelského prostředí společnosti VMware

Součásti nainstalujte následujícím způsobem:

1. Nainstalujte a nakonfigurujte sjednocenou přístupovou bránu podle pokynů uvedených v dokumentu VMware [nasazení a konfigurace sjednocené přístupové brány VMware](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Nainstalujte zobrazení Horizon Do privátního cloudu podle pokynů v [Průvodci instalací zobrazení](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Nainstalujte Správce svazků aplikací podle pokynů v [části Instalace a konfigurace svazků aplikací VMware](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html).

4. Nainstalujte a nakonfigurujte Správce uživatelského prostředí podle pokynů v části [Instalace a konfigurace správce uživatelského prostředí společnosti VMware](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Odeslání žádosti o podporu pro nahrání předbalených svazků aplikací VMware Horizon

Jako součást procesu instalace Správce svazků aplikací používá předem zabalené svazky k zřízení zásobníků aplikací a zapisovatelných svazků. Tyto svazky slouží jako šablony pro balíčky aplikací a zapisovatelné svazky.

Nahrání svazků do úložiště dat privátního cloudu vyžaduje heslo root ESXi. O pomoc můžete požádat [o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Připojte instalační balíček AppVolumes tak, aby pracovníci podpory CloudSimple mohli nahrát šablony do vašeho prostředí Privátního cloudu.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>Portál CloudSimple: Deeskalovat oprávnění

Nyní můžete [de-eskalovat oprávnění](escalate-private-cloud-privileges.md#de-escalate-privileges) uživatele "cloudowner".

## <a name="ongoing-management-of-your-horizon-solution"></a>Průběžná správa vašeho řešení Horizon

Máte plnou kontrolu nad softwarem Horizon a App Volume Manager ve vašem prostředí Privátního cloudu a očekává se, že budete provádět potřebnou správu životního cyklu softwaru. Před aktualizací nebo upgradem horizonnebo app volume se ujistěte, že všechny nové verze softwaru jsou kompatibilní s privátním cloudovým virtuálním počítačem a psc.
