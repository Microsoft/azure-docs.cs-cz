---
title: Řešení Azure VMware (AVS) – nastavení privátního cloudu pro službu AVS jako lokality pro zotavení po havárii pomocí nástroje VMware Site Recovery Manager
description: Popisuje, jak nastavit privátní cloud služby AVS jako lokalitu pro zotavení po havárii pro místní úlohy VMware.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ccdf385d2581923e1fad4fa5c42f351c4f0947ca
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083148"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Nastavení privátního cloudu pro funkci AVS jako cíle zotavení po havárii pomocí nástroje VMware Site Recovery Manager

Privátní cloud služby AVS můžete použít jako web zotavení po havárii (DR) pro místní úlohy VMware.

Řešení zotavení po havárii je založené na vSphere replikaci a VMware Site Recovery Manageru (SRM). Podobným způsobem je možné povolit privátní cloud služby AVS jako primární lokalitu chráněnou místní lokalitou pro obnovení.

Řešení AVS:

* Eliminuje nutnost nastavit datacentrum speciálně pro zotavení po havárii.
* Umožňuje využívat umístění Azure, kde je nasazená službou AVS pro celosvětově geografickou odolnost.
* Nabízí možnost snížit náklady na nasazení a celkové náklady na vlastnictví pro vytvoření DR.

Řešení AVS vyžaduje, abyste provedete následující kroky:

* Instalace, konfigurace a Správa replikace vSphere a SRM v privátním cloudu služby AVS
* Pokud je privátní cloud služby AVS chráněným webem, zadejte vlastní licence pro SRM. Když použijete jako lokalitu pro obnovení, nepotřebujete žádné další licence SRM pro web služby AVS.

V tomto řešení máte plnou kontrolu nad replikací vSphere a SRM. Známá rozhraní uživatelského rozhraní, rozhraní API a rozhraní příkazového řádku umožňují používat stávající skripty a nástroje.

![Nasazení Site Recovery Manageru](media/srm-deployment.png)

Můžete použít všechny verze vRA a SRM, které jsou kompatibilní s Vaším privátním cloudem a místním prostředím služby AVS. Příklady v této příručce používají vRA 6,5 a SRM 6,5. Tyto verze jsou kompatibilní s vSphere 6,5, který podporuje služba AVS.

## <a name="deploy-the-solution"></a>Nasazení řešení

Následující části popisují, jak nasadit řešení zotavení po havárii pomocí SRM v privátním cloudu služby AVS.

1. [Ověřte, zda jsou verze produktu VMware kompatibilní.](#verify-that-vmware-product-versions-are-compatible)
2. [Odhad velikosti prostředí DR](#estimate-the-size-of-your-dr-environment)
3. [Vytvoření privátního cloudu služby AVS pro vaše prostředí](#create-an-avs-private-cloud-for-your-environment)
4. [Nastavení privátního cloudu služby AVS pro řešení SRM](#set-up-avs-private-cloud-networking-for-the-srm-solution)
5. [Nastavte připojení VPN typu Site-to-site mezi vaší místní sítí a privátním cloudem služby AVS a otevřete požadované porty.](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports)
6. [Nastavení služeb infrastruktury v privátním cloudu služby AVS](#set-up-infrastructure-services-in-your-avs-private-cloud)
7. [Instalace zařízení replikace vSphere do místního prostředí](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Instalace zařízení replikace vSphere v prostředí privátního cloudu služby AVS](#install-vsphere-replication-appliance-in-your-avs-private-cloud-environment)
9. [Instalace serveru SRM v místním prostředí](#install-srm-server-in-your-on-premises-environment)
10. [Instalace serveru SRM v privátním cloudu služby AVS](#install-srm-server-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Ověřte, zda jsou verze produktu VMware kompatibilní.

Konfigurace v této příručce se řídí následujícími požadavky na kompatibilitu:

* V privátním cloudu služby AVS a v místním prostředí musí být nasazená stejná verze SRM.
* V privátním cloudu služby AVS a v místním prostředí musí být nasazená stejná verze replikace vSphere.
* Verze řadiče služeb platformy (PSC) ve vašem privátním cloudu služby AVS a v místním prostředí musí být kompatibilní.
* Verze vCenter v privátním cloudu služby AVS a v místním prostředí musí být kompatibilní.
* Verze replikace SRM a vSphere musí být vzájemně kompatibilní a s verzemi PSC a vCenter.

Odkazy na relevantní dokumentaci k VMware a informace o kompatibilitě najdete v dokumentaci k [vmware Site Recovery Manageru](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) .

Pokud chcete zjistit verze vCenter a PSC v privátním cloudu služby AVS, otevřete portál AVS. Přejděte na **prostředky**, vyberte svůj privátní cloud služby AVS a klikněte na kartu **síť pro správu vSphere** .

![verze vCenter & PSC v privátním cloudu pro funkci AVS](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Odhad velikosti prostředí DR

1. Ověřte, jestli je vaše identifikovaná místní konfigurace v rámci podporovaných omezení. V případě SRM 6,5 jsou tato omezení popsána v článku znalostní báze VMware na základě [provozních omezení pro Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110).
2. Ujistěte se, že máte dostatečnou šířku pásma sítě, aby splňovala požadavky na velikost pracovního vytížení a RPO. Článek znalostní báze VMware o [výpočtu požadavků na šířku pásma pro replikaci vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) poskytuje pokyny k omezení šířky pásma.
3. Pomocí nástroje Sizer služby AVS můžete odhadnout prostředky, které jsou potřeba na webu DR, a chránit tak vaše místní prostředí.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>Vytvoření privátního cloudu služby AVS pro vaše prostředí

Pomocí pokynů v tématu [Vytvoření privátního cloudu](create-private-cloud.md)pro funkci AVS vytvořte privátní cloud služby AVS z portálu služby AVS.

### <a name="set-up-avs-private-cloud-networking-for-the-srm-solution"></a>Nastavení privátního cloudu služby AVS pro řešení SRM

Přístup k portálu služby AVS a nastavení privátního cloudu služby AVS pro řešení SRM

Vytvořte síť VLAN pro síť řešení SRM a přiřaďte ji k podsíti CIDR. Pokyny najdete v tématu [Vytvoření a správa sítí VLAN a podsítí](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports"></a>Nastavte připojení VPN typu Site-to-site mezi vaší místní sítí a privátním cloudem služby AVS a otevřete požadované porty.

Nastavení propojení mezi lokalitami mezi vaší místní sítí a Vaším privátním cloudem služby AVS. Pokyny najdete v tématu [Konfigurace připojení VPN k privátnímu cloudu služby AVS](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-avs-private-cloud"></a>Nastavení služeb infrastruktury v privátním cloudu služby AVS

Nakonfigurujte služby infrastruktury v privátním cloudu služby AVS, abyste usnadnili správu úloh a nástrojů.

Můžete přidat externího poskytovatele identity, jak je popsáno v tématu [použití Azure AD jako zprostředkovatele identity pro vCenter v privátním cloudu služby AVS](azure-ad.md) , pokud chcete provést některou z následujících akcí:

* Identifikujte uživatele z místní služby Active Directory (AD) ve vašem privátním cloudu služby AVS.
* Nastavte reklamu v privátním cloudu služby AVS pro všechny uživatele.
* Použijte Azure AD.

Pokud chcete pro své úlohy v privátním cloudu služby AVS zadat vyhledávání IP adres, správu IP adres a služby překladu názvů, nastavte server DHCP a DNS, jak je popsáno v tématu [nastavení aplikací DNS a DHCP a úloh v privátním cloudu služby AVS](dns-dhcp-setup.md).

Doména *. cloudsimple.io se používá v rámci virtuálních počítačů pro správu a hostitelů v privátním cloudu služby AVS. Chcete-li vyřešit požadavky na tuto doménu, nakonfigurujte předávání DNS na serveru DNS, jak je popsáno v tématu [Vytvoření podmíněného předávání](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Instalace zařízení replikace vSphere do místního prostředí

Nainstalujte zařízení vSphere Replication (vRA) do místního prostředí podle pokynů v dokumentaci k VMware. Instalace se skládá z těchto kroků vysoké úrovně:

1. Připravte své místní prostředí pro instalaci vRA.

2. Nasaďte vRA do svého místního prostředí pomocí OVF v VR ISO od vmware.com. V případě vRA 6,5 obsahuje [Tento blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) relevantní informace.

3. Zaregistrujte své místní vRA s jednotným přihlašováním vCenter v místní lokalitě. Podrobné pokyny pro vSphere Replication 6,5 najdete v VMware vSphere dokumentu VMware o [instalaci a konfiguraci replikace 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-avs-private-cloud-environment"></a>Instalace zařízení replikace vSphere v prostředí privátního cloudu služby AVS

Než začnete, ověřte, že máte následující:

* Dostupnost IP adres z podsítí v místním prostředí do podsítě pro správu privátního cloudu služby AVS
* Dostupnost IP adresy z podsítě replikace v místním prostředí vSphere do podsítě řešení SRM vašeho privátního cloudu služby AVS

Pokyny najdete v tématu [Konfigurace připojení VPN k privátnímu cloudu služby AVS](set-up-vpn.md). Postup je podobný jako u místní instalace.

V průběhu instalace vRA a SRM doporučuje aplikace AVS používat namísto IP adres plně kvalifikované názvy domény. Pokud chcete zjistit plně kvalifikovaný název domény vCenter a PSC v privátním cloudu služby AVS, otevřete portál AVS. Přejděte na **prostředky**, vyberte svůj privátní cloud služby AVS a klikněte na kartu **síť pro správu vSphere** .

![Hledání plně kvalifikovaného názvu domény vCenter/PSC v privátním cloudu AVS](media/srm-resources.png)

Možnost AVS vyžaduje, abyste neinstalovali vRA a SRM pomocí výchozího uživatele cloudowner, ale místo toho vytvoříte nového uživatele. To vám umožní zajistit vysokou provozuschopnost a dostupnost vašeho prostředí pro vCenter privátního cloudu. Výchozí uživatel cloudowner ve službě AVS Private Cloud vCenter ale nemá dostatečná oprávnění k vytvoření nového uživatele s oprávněními správce.

Před instalací vRA a SRM musíte eskalovat oprávnění vCenter uživatele cloudowner a pak vytvořit uživatele s oprávněními správce v doméně jednotného přihlašování (vCenter). Podrobnosti o výchozím uživatelském a přístupovém modelu privátního cloudu služby AVS najdete v tématu [informace o modelu oprávnění privátního cloudu služby AVS](learn-private-cloud-permissions.md).

Instalace se skládá z těchto kroků vysoké úrovně:

1. [Eskalace oprávnění](escalate-private-cloud-privileges.md).
2. Vytvořte uživatele v privátním cloudu služby AVS pro replikaci vSphere a instalaci SRM. V uživatelském rozhraní vCenter je vysvětleno níže [: vytvoření uživatele v privátním cloudu služby AVS pro instalaci vRA &AMP; SRM](#vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation).
3. Připravte prostředí privátního cloudu služby AVS pro instalaci vRA.
4. Nasaďte vRA do privátního cloudu služby AVS pomocí OVF v VR ISO od vmware.com. V případě vRA 6,5 má [Tento blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) relevantní informace.
5. Nakonfigurujte pravidla brány firewall pro vRA. Je vysvětleno níže na [portálu AVS: Konfigurace pravidel brány firewall pro vRA](#avs-portal-configure-firewall-rules-for-vra).
6. Zaregistrujte si privátní cloud vRA s jednotným přihlašováním vCenter na webu privátního cloudu pro funkci AVS.
7. Nakonfigurujte připojení replikace vSphere mezi oběma zařízeními. Zajistěte, aby byly požadované porty otevřeny v rámci bran firewall. Seznam čísel portů, která musí být otevřená pro vSphere replikaci 6,5, najdete v [tomto článku znalostní báze VMware](https://kb.vmware.com/s/article/2087769) .

Podrobné pokyny k instalaci pro vSphere Replication 6,5 najdete v VMware vSphere dokumentu VMware [instalace a konfigurace replikace 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation"></a>uživatelské rozhraní vCenter: vytvoření uživatele v privátním cloudu služby AVS pro instalaci vRA a SRM

Přihlaste se k vCenter pomocí přihlašovacích údajů uživatele cloudowner po eskalaci oprávnění z portálu AVS.

Vytvořte nového uživatele `srm-soln-admin`v vCenter a přidejte ho do skupiny Administrators v vCenter.
Odhlaste se z vCenter jako uživatel cloudowner a přihlaste se jako uživatel *SRM-soln-admin* .

#### <a name="avs-portal-configure-firewall-rules-for-vra"></a>Portál pro funkci AVS: Konfigurace pravidel brány firewall pro vRA

Nakonfigurujte pravidla brány firewall, jak je popsáno v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md) na otevřené porty, aby bylo možné komunikovat mezi těmito nástroji:

* vRA v síti řešení SRM a v hostitelích vCenter a ESXi v síti pro správu.
* zařízení vRA na těchto dvou lokalitách.

Seznam čísel portů, která musí být otevřená pro vSphere replikaci 6,5, najdete v tomto [článku znalostní báze VMware](https://kb.vmware.com/s/article/2087769) .

### <a name="install-srm-server-in-your-on-premises-environment"></a>Instalace serveru SRM v místním prostředí

Než začnete, ověřte následující:

* Zařízení replikace vSphere je nainstalované v prostředích místní a AVS privátní cloud.
* Zařízení pro replikaci vSphere na obou lokalitách jsou vzájemně propojená.
* Zkontrolovali jste informace VMware o požadavcích a osvědčených postupech. V případě SRM 6,5 se můžete podívat na požadavky na dokumenty VMware [a osvědčené postupy pro SRM 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Dodržujte dokumentaci VMware a proveďte instalaci serveru SRM v modelu nasazení topologie dvou lokalit s jednou instancí vCenter na řadič služeb platformy, jak je popsáno v tomto [dokumentu VMware](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Pokyny k instalaci pro SRM 6,5 jsou k dispozici v dokumentu VMware [Instalování Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-avs-private-cloud"></a>Instalace serveru SRM v privátním cloudu služby AVS

Než začnete, ověřte následující:

* Zařízení replikace vSphere je nainstalované v prostředích místní a AVS privátní cloud.
* Zařízení pro replikaci vSphere na obou lokalitách jsou vzájemně propojená.
* Zkontrolovali jste informace VMware o požadavcích a osvědčených postupech. V případě SRM 6,5 můžete odkazovat na [požadavky a osvědčené postupy pro instalaci serveru Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Následující kroky popisují instalaci SRM privátního cloudu pro funkci AVS.

1. [uživatelské rozhraní vCenter: Nainstalujte SRM](#vcenter-ui-install-srm)
2. [Portál pro funkci AVS: Konfigurace pravidel brány firewall pro SRM](#avs-portal-configure-firewall-rules-for-srm)
3. [uživatelské rozhraní vCenter: Konfigurace SRM](#vcenter-ui-configure-srm)
4. [Portál pro funkci AVS: oprávnění ke zrušení eskalace](#avs-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>uživatelské rozhraní vCenter: Nainstalujte SRM

Po přihlášení na Server vCenter pomocí přihlašovacích údajů SRM-soln-admin Sledujte dokumentaci VMware a proveďte instalaci serveru SRM v modelu nasazení topologie dvou lokalit s jednou instancí vCenter na řadič služeb platformy, jak je popsáno v tomto [dokumentu VMware](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Pokyny k instalaci pro SRM 6,5 jsou k dispozici v dokumentu VMware [Instalování Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="avs-portal-configure-firewall-rules-for-srm"></a>Portál pro funkci AVS: Konfigurace pravidel brány firewall pro SRM

Nakonfigurujte pravidla brány firewall, jak je popsáno v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md) tak, aby umožňovaly komunikaci mezi:

Server SRM a vCenter/PSC v privátním cloudu AVS.
Servery SRM na obou webech

Seznam čísel portů, která musí být otevřená pro vSphere replikaci 6,5, najdete v [tomto článku znalostní báze VMware](https://kb.vmware.com/s/article/2087769) .

#### <a name="vcenter-ui-configure-srm"></a>uživatelské rozhraní vCenter: Konfigurace SRM

Po instalaci SRM do privátního cloudu pro funkci AVS proveďte následující úlohy, jak je popsáno v částech Průvodce instalací a konfigurací nástroje VMware Site Recovery Manager. V případě SRM 6,5 jsou pokyny k dispozici v dokumentu VMware [Instalování Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Připojte instance serveru nástroje Site Recovery Manager na webu chráněného a obnovení.
2. Navažte připojení klienta k instanci serveru vzdálené správy Site Recovery.
3. Nainstalujte licenční klíč nástroje Site Recovery Manager.

#### <a name="avs-portal-de-escalate-privileges"></a>Portál pro funkci AVS: oprávnění ke zrušení eskalace

Chcete-li zrušit oprávnění, přečtěte si téma oprávnění ke zrušení [Eskalace](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Průběžná správa řešení SRM

Máte plnou kontrolu nad replikací vSphere a SRM softwarem v prostředí privátního cloudu služby AVS a očekáváte, že budete provádět správu životního cyklu softwaru potřebného. Před aktualizací nebo upgradem replikace vSphere nebo SRM zajistěte, aby byla nová verze softwaru kompatibilní s privátním cloudem služby AVS a vCenter a PSC.

> [!NOTE]
> Služba AVS v současné době zkoumá možnosti nabídky spravované služby DR. 

## <a name="multiple-replication-configuration"></a>Konfigurace vícenásobné replikace

 [Replikaci založené na poli a technologie vSphere replikace je možné používat společně s SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) ve stejnou dobu. Musí se ale použít pro samostatnou sadu virtuálních počítačů (daný virtuální počítač se dá chránit buď replikací založenými na poli, nebo replikací vSphere, ale ne oběma). Kromě toho je možné lokalitu služby AVS nakonfigurovat jako lokalitu pro obnovení pro více chráněných webů. Informace o konfiguracích s více lokalitami najdete v tématu [SRM možnosti pro více lokalit](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) .

## <a name="references"></a>Odkazy

* [Dokumentace k VMware Site Recovery Manageru](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Provozní omezení pro Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110)
* [Výpočet požadavků na šířku pásma pro replikaci vSphere](https://kb.vmware.com/s/article/2037268)
* [OVF možnosti při nasazení vSphere replikace 6,5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere instalace a konfigurace replikace 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Předpoklady a osvědčené postupy pro SRM 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery Manager v topologii dvou lokalit s jednou instancí vCenter Server na řadič služeb platformy](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Průvodce instalací a konfigurací nástroje VMware Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Blog VMware v SRM s replikací založenými na poli a replikací vSphere](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [Blog VMware na SRM možnosti pro více lokalit](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Čísla portů, která musí být otevřená pro vSphere replikaci na 5,8. x, 6. x a 8](https://kb.vmware.com/s/article/2147112)
