---
title: Řešení Azure VMware od CloudSimple – nastavení privátního cloudu jako lokality pro zotavení po havárii pomocí Správce obnovení webu VMware
description: Popisuje, jak nastavit cloudový privátní cloud jako lokalitu pro zotavení po havárii pro místní úlohy VMware.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa8b2da683d68a337df38e13726f22c5af43540a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565923"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Nastavení privátního cloudu jako cíle zotavení po havárii pomocí Správce obnovení webu společnosti VMware

CloudSimple Private Cloud můžete použít jako web zotavení po havárii (DR) pro místní úlohy VMware.

Řešení zotavení po havárii je založeno na službě vSphere Replication a Správci obnovení lokality vMware (SRM). Podobný přístup lze sledovat k povolení privátního cloudu jako primární lokality, která je chráněna místní lokalitou pro obnovení.

CloudSimple řešení:

* Eliminuje potřebu nastavit datové centrum speciálně pro zotavení po havárii.
* Umožňuje využít umístění Azure, kde se CloudSimple nasazuje pro celosvětovou geografickou odolnost.
* Poskytuje možnost snížit náklady na nasazení a celkové náklady na vlastnictví pro vytvoření zotavení po havárii.

CloudSimple řešení vyžaduje, abyste provést následující kroky:

* Instalujte, konfigurujte a spravujte vSphere Replication a SRM ve svém privátním cloudu.
* Poskytněte vlastní licence pro SRM, pokud je privátní cloud chráněným webem. Nepotřebujete žádné další licence SRM pro web CloudSimple, pokud se používá jako lokalita pro obnovení.

S tímto řešením máte plnou kontrolu nad replikací vSphere a SRM. Známá rozhraní uživatelského rozhraní, rozhraní API a rozhraní rozhraní se konstatování umožňují použití existujících skriptů a nástrojů.

![Nasazení správce obnovení lokality](media/srm-deployment.png)

Můžete použít libovolné verze vRA a SRM, které jsou kompatibilní s vaším privátním cloudem a místními prostředími. Příklady v této příručce používají vRA 6.5 a SRM 6.5. Tyto verze jsou kompatibilní s vSphere 6.5, který je podporován CloudSimple.

## <a name="deploy-the-solution"></a>Nasazení řešení

Následující části popisují, jak nasadit řešení zotavení po havárii pomocí SRM ve vašem privátním cloudu.

1. [Ověřte, zda jsou verze produktů VMware kompatibilní](#verify-that-vmware-product-versions-are-compatible)
2. [Odhad velikosti prostředí zotavení po Havárii](#estimate-the-size-of-your-dr-environment)
3. [Vytvoření privátního cloudu pro vaše prostředí](#create-a-private-cloud-for-your-environment)
4. [Nastavení privátní sítě cloudu pro řešení SRM](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Nastavení připojení VPN site-to-site mezi místní sítí a privátním cloudem a otevření požadovaných portů](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Nastavení infrastrukturních služeb v privátním cloudu](#set-up-infrastructure-services-in-your-private-cloud)
7. [Instalace zařízení vSphere Replication v místním prostředí](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Instalace zařízení vSphere Replication ve vašem prostředí Privátního cloudu](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Instalace serveru SRM v místním prostředí](#install-srm-server-in-your-on-premises-environment)
10. [Instalace serveru SRM v privátním cloudu](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Ověřte, zda jsou verze produktů VMware kompatibilní

Konfigurace v této příručce podléhají následujícím požadavkům na kompatibilitu:

* Stejná verze SRM musí být nasazená ve vašem privátním cloudu a v místním prostředí.
* Stejná verze vSphere Replication musí být nasazena ve vašem privátním cloudu a v místním prostředí.
* Verze řadiče služby platformy (PSC) ve vašem privátním cloudu a v místním prostředí musí být kompatibilní.
* Verze vCenter ve vašem privátním cloudu a místní prostředí musí být kompatibilní.
* Verze replikace SRM a vSphere musí být vzájemně kompatibilní a s verzemi psc a vCenter.

Odkazy na příslušnou dokumentaci společnosti VMware a informace o kompatibilitě naleznete v dokumentaci [správce webu společnosti VMware.](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)

Chcete-li zjistit verze vCenter a PSC ve vašem privátním cloudu, otevřete portál CloudSimple. Přejděte na **Zdroje**, vyberte svůj privátní cloud a klikněte na kartu **vSphere Management Network** .

![vCenter & psc verze v privátním cloudu](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Odhad velikosti prostředí zotavení po Havárii

1. Ověřte, zda je vaše identifikovaná místní konfigurace v rámci podporovaných limitů. U srm 6.5 jsou limity popsány v článku znalostní báze VMware o [provozních limitech pro správce site recovery 6.5](https://kb.vmware.com/s/article/2147110).
2. Ujistěte se, že máte dostatečnou šířku pásma sítě, aby splňovaly vaše požadavky na velikost pracovního vytížení a rpo. Článek znalostní báze VMware o výpočtu požadavků na [šířku pásma pro službu Replikace vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) poskytuje pokyny k omezením šířky pásma.
3. Pomocí nástroje CloudSimple sizer můžete odhadnout prostředky, které jsou potřeba na webu zotavení po Havárii k ochraně místního prostředí.

### <a name="create-a-private-cloud-for-your-environment"></a>Vytvoření privátního cloudu pro vaše prostředí

Vytvořte privátní cloud z portálu CloudSimple podle pokynů a doporučení pro velikost v [části Vytvoření privátního cloudu](create-private-cloud.md).

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Nastavení privátní sítě cloudu pro řešení SRM

Přístup k portálu CloudSimple k nastavení privátní hocloudové sítě pro řešení SRM.

Vytvořte síť VLAN pro síť řešení SRM a přiřaďte jí podsíť CIDR. Pokyny naleznete v [tématu Vytvoření a správa sítí VLAN/podsítí](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Nastavení připojení VPN site-to-site mezi místní sítí a privátním cloudem a otevření požadovaných portů

Nastavení připojení site-to-site mezi místní sítí a privátním cloudem. Pokyny naleznete [v tématu Konfigurace připojení VPN k privátnímu cloudu CloudSimple](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Nastavení infrastrukturních služeb v privátním cloudu

Nakonfigurujte služby infrastruktury v privátním cloudu, abyste mohli snadno spravovat své úlohy a nástroje.

Můžete přidat externí hospodařících, jak je popsáno v [použití Azure AD jako zprostředkovatele identity pro vCenter na CloudSimple Privátní cloud,](azure-ad.md) pokud chcete provést některou z následujících akcí:

* Identifikujte uživatele z místní služby Active Directory (AD) ve vašem privátním cloudu.
* Nastavte službu AD v privátním cloudu pro všechny uživatele.
* Použijte Azure AD.

Chcete-li zajistit vyhledávání ADRES IP, správu IP adres a služby překladu názvů pro vaše úlohy v privátním cloudu, nastavte server DHCP a DNS, jak je popsáno v části [Nastavení aplikací a úloh DNS a DHCP ve vašem cloudu CloudSimple Private Cloud](dns-dhcp-setup.md).

Doména *.cloudsimple.io používá virtuální počítače pro správu a hostitelé ve vašem privátním cloudu. Chcete-li vyřešit požadavky na tuto doménu, nakonfigurujte předávání DNS na serveru DNS, jak je popsáno v [části Vytvoření podmíněného předávání](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Instalace zařízení replikace vSphere v místním prostředí

Nainstalujte zařízení vSphere Replication Appliance (vRA) do místního prostředí podle dokumentace společnosti VMware. Instalace se skládá z těchto kroků na vysoké úrovni:

1. Připravte si místní prostředí pro instalaci vRA.

2. Nasaďte vRA ve svém místním prostředí pomocí OVF ve VR ISO od vmware.com. Pro vRA 6.5, [tento blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) má příslušné informace.

3. Zaregistrujte své místní vRA s vCenter jednotné přihlášení na místním webu. Podrobné pokyny pro replikaci vSphere 6.5 naleznete v dokumentu [VMware VMware vSphere Replication 6.5 Instalace a konfigurace](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>Instalace zařízení vSphere Replication ve vašem prostředí Privátního cloudu

Než začnete, ověřte, zda máte následující:

* Dosažitelnost IP adres z podsítí v místním prostředí do podsítě pro správu privátního cloudu
* Dosažitelnost IP adres z podsítě replikace v místním prostředí vSphere do podsítě řešení SRM vašeho privátního cloudu

Pokyny naleznete [v tématu Konfigurace připojení VPN k privátnímu cloudu CloudSimple](set-up-vpn.md). Kroky jsou podobné jako u místní instalace.

CloudSimple doporučuje používat fqdns místo IP adres během instalace vRA a SRM. Chcete-li zjistit hlavní název vCentru a PSC v privátním cloudu, otevřete portál CloudSimple. Přejděte na **Zdroje**, vyberte svůj privátní cloud a klikněte na kartu **vSphere Management Network** .

![Hledání fqdn vcenter/PSC v privátním cloudu](media/srm-resources.png)

CloudSimple vyžaduje, abyste neinstalovali vRA a SRM pomocí výchozího uživatele "cloudowner", ale místo toho vytvořte nového uživatele. To se provádí, aby se zajistilo vysokou dostupnost a dostupnost pro vaše prostředí private cloud vCenter. Výchozí uživatel cloudowner v privátním cloudu vCenter však nemá dostatečná oprávnění k vytvoření nového uživatele s oprávněními správce.

Před instalací vRA a SRM je nutné eskalovat oprávnění vCenter uživatele vlastníka cloudu a potom vytvořit uživatele s oprávněními správce v doméně spřiho vCenter. Podrobnosti o výchozím uživateli privátního cloudu a modelu oprávnění najdete [v tématu Naučte se model oprávnění privátního cloudu](learn-private-cloud-permissions.md).

Instalace se skládá z těchto kroků na vysoké úrovni:

1. [Eskalovat oprávnění](escalate-private-cloud-privileges.md).
2. Vytvořte uživatele ve svém privátním cloudu pro instalaci replikace vSphere a SRM. Vysvětleno níže v [uživatelském rozhraní vCenter: Vytvořte uživatele v privátním cloudu pro instalaci vRA & SRM](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation).
3. Připravte si prostředí Privátního cloudu na instalaci vRA.
4. Nasaďte vRA ve svém privátním cloudu pomocí OVF ve VR ISO od vmware.com. Pro vRA 6.5, [tento blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) má relevantní informace.
5. Konfigurace pravidel brány firewall pro vRA. Vysvětleno níže na [portálu CloudSimple: Konfigurace pravidel brány firewall pro vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. Zaregistrujte privátní cloud vRA pomocí jednotného přihlašování vCenter na webu Privátního cloudu.
7. Konfigurace připojení replikace vSphere mezi těmito dvěma zařízeními. Ujistěte se, že požadované porty jsou otevřeny přes brány firewall. Seznam čísel portů, která musí být otevřena pro vSphere Replication 6.5, naleznete v [tomto článku znalostní báze VMware.](https://kb.vmware.com/s/article/2087769)

Podrobné pokyny k instalaci aplikace vSphere Replication 6.5 naleznete v dokumentu [vMware VMware vSphere Replication 6.5 Instalace a konfigurace](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>Uživatelské rozhraní vCenter: Vytvoření uživatele v privátním cloudu pro instalaci vRA a SRM

Po eskalování oprávnění z portálu CloudSimple se přihlaste k virtuálnímu centru pomocí přihlašovacích údajů vlastníka cloudu.

Vytvořte nového `srm-soln-admin`uživatele , v programu vCenter a přidejte ho do skupiny administrators v programu vCenter.
Odhlaste se z vCenter jako uživatel vlastníka cloudu a přihlaste se jako uživatel *srm-soln-admin.*

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>Portál CloudSimple: Konfigurace pravidel brány firewall pro vRA

Nakonfigurujte pravidla brány firewall, jak je popsáno v [části Nastavení tabulek brány firewall a pravidel](firewall.md) pro otevření portů, aby byla povolena komunikace mezi:

* vRA v síti řešení SRM a hostitelé vCenter a ESXi v síti pro správu.
* vRA spotřebičů na obou místech.

Seznam čísel portů, která musí být otevřena pro vSphere Replication 6.5, naleznete v tomto [článku znalostní báze VMware.](https://kb.vmware.com/s/article/2087769)

### <a name="install-srm-server-in-your-on-premises-environment"></a>Instalace serveru SRM v místním prostředí

Než začnete, ověřte následující:

* Zařízení replikace vSphere je nainstalováno v místním prostředí a prostředí privátního cloudu.
* Zařízení replikace vSphere v obou lokalitách jsou vzájemně propojena.
* Prověřili jste informace společnosti VMware o požadavcích a doporučených postupech. V případě srm 6.5 se můžete obrátit na dokument VMware [Požadavky a doporučené postupy pro SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Postupujte podle dokumentace společnosti VMware a proveďte instalaci serveru SRM v modelu nasazení Topologie dvou lokalit s jednou instancí vCenter na řadič služby platformy, jak je popsáno v tomto [dokumentu v systému VMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Pokyny k instalaci pro srm 6.5 jsou k dispozici v dokumentu VMware [Instalace Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-private-cloud"></a>Instalace serveru SRM v privátním cloudu

Než začnete, ověřte následující:

* Zařízení replikace vSphere je nainstalováno v místním prostředí a prostředí privátního cloudu.
* Zařízení replikace vSphere v obou lokalitách jsou vzájemně propojena.
* Prověřili jste informace společnosti VMware o požadavcích a doporučených postupech. V případě srm 6.5 naleznete [požadavky a doporučené postupy pro instalaci serveru správce množosti 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Následující kroky popisují instalaci SRM privátního cloudu.

1. [Uv. vCenter: Instalace SRM](#vcenter-ui-install-srm)
2. [Portál CloudSimple: Konfigurace pravidel brány firewall pro SRM](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [Uvrozhraní vCenter: Konfigurace SRM](#vcenter-ui-configure-srm)
4. [CloudSimple portál: de-eskalovat oprávnění](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>Uv. vCenter: Instalace SRM

Po přihlášení do aplikace vCenter pomocí přihlašovacích údajů srm-soln-admin postupujte podle dokumentace společnosti VMware a proveďte instalaci serveru SRM v modelu nasazení "Topologie dvou lokalit s jednou instancí vCenter na řadič služby platformy", jak je popsáno v tomto [dokumentu VMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Pokyny k instalaci pro srm 6.5 jsou k dispozici v dokumentu VMware [Instalace Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>Portál CloudSimple: Konfigurace pravidel brány firewall pro SRM

Nakonfigurujte pravidla brány firewall, jak je popsáno v [části Nastavení tabulek brány firewall a pravidel](firewall.md) pro umožnění komunikace mezi:

SRM server a vCenter / PSC v privátním cloudu.
Servery SRM na obou pracovištích

Seznam čísel portů, která musí být otevřena pro vSphere Replication 6.5, naleznete v [tomto článku znalostní báze VMware.](https://kb.vmware.com/s/article/2087769)

#### <a name="vcenter-ui-configure-srm"></a>Uvrozhraní vCenter: Konfigurace SRM

Po instalaci služby SRM v privátním cloudu proveďte následující úkoly, jak je popsáno v částech průvodce instalací a konfigurací nástroje Site Recovery Manager společnosti VMware. Pro srm 6.5 jsou pokyny k dispozici v dokumentu VMware [Instalace Správce obnovení webu](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Připojte instance serveru Správce obnovení lokality v chráněných a obnovovacích sítích.
2. Vytvořte připojení klienta k instanci serveru Správce vzdáleného obnovení sítě.
3. Nainstalujte licenční klíč Správce obnovení webu.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>Portál CloudSimple: Deeskalovat oprávnění

Chcete-li de-eskalovat oprávnění, naleznete [v tématu De-escalate oprávnění](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Průběžná správa vašeho řešení SRM

Máte plnou kontrolu nad softwarem vSphere Replication a SRM ve vašem prostředí Privátního cloudu a očekává se, že provedete potřebnou správu životního cyklu softwaru. Před aktualizací nebo upgradem vSphere Replication nebo SRM se ujistěte, že je jakákoli nová verze softwaru kompatibilní s privátním cloudovým virtuálním zařízením vCenter a PSC.

> [!NOTE]
> CloudSimple v současné době zkoumá možnosti pro nabízení spravované služby ZOTAVENÍ po havárii. 

## <a name="multiple-replication-configuration"></a>Konfigurace více replikací

 [Technologie replikace založené na poli a replikace vSphere lze použít společně s SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) současně. Musí se však použít na samostatnou sadu virtuálních počítačů (daný virtuální počítač může být chráněný replikací založenou na poli nebo replikací vSphere, ale ne obojí). Kromě toho cloudsimple stránky lze nakonfigurovat jako lokalitu pro obnovení pro více chráněných lokalit. Informace o konfiguracích s více pracemi naleznete v [části Možnosti více webů SRM.](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/)

## <a name="references"></a>Odkazy

* [Dokumentace správce webu společnosti VMware](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Provozní limity pro Správce obnovy lokality 6.5](https://kb.vmware.com/s/article/2147110)
* [Výpočet požadavků na šířku pásma pro replikaci vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [Volby OVF při nasazování replikace vSphere 6.5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [Instalace a konfigurace aplikace VMware vSphere Replikace 6.5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Požadavky a doporučené postupy pro SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Správce obnovení lokality v topologii se dvěma lokalitami s jednou instancí serveru vCenter na řadič služby platformy](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Průvodce instalací a konfigurací webu VMware](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Blog VMware o SRM s replikací založenou na poli vs. vSphere](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware Blog o Možnosti SRM Multi-site](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Čísla portů, která musí být otevřena pro replikaci vSphere 5.8.x, 6.x a 8](https://kb.vmware.com/s/article/2147112)
