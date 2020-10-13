---
title: Řešení Azure VMware podle CloudSimple – nastavení privátního cloudu jako lokality pro zotavení po havárii pomocí nástroje VMware Site Recovery Manager
description: Popisuje, jak nastavit privátní cloud CloudSimple jako lokalitu pro zotavení po havárii pomocí nástroje VMware Site Recovery Manager.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6714b2c5d87141fd94d0f96d9cf07913442d18d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267053"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Nastavení privátního cloudu jako cíle zotavení po havárii pomocí nástroje VMware Site Recovery Manager

Privátní cloud CloudSimple můžete použít jako web zotavení po havárii (DR) pro místní úlohy VMware.

Řešení zotavení po havárii je založené na vSphere replikaci a VMware Site Recovery Manageru (SRM). Podobně jako primární lokalita chráněná místním webem pro obnovení může být použit podobný přístup.

Řešení CloudSimple:

* Eliminuje nutnost nastavit datacentrum speciálně pro zotavení po havárii.
* Umožňuje využívat umístění Azure, kde je CloudSimple nasazená pro celosvětově geografickou odolnost.
* Nabízí možnost snížit náklady na nasazení a celkové náklady na vlastnictví pro vytvoření DR.

Řešení CloudSimple vyžaduje, abyste provedete následující kroky:

* Instalace, konfigurace a Správa replikace vSphere a SRM ve vašem privátním cloudu.
* Zadejte vlastní licence pro SRM, pokud je privátní cloud chráněným webem. Když použijete jako lokalitu pro obnovení, nepotřebujete k webu CloudSimple žádné další licence SRM.

V tomto řešení máte plnou kontrolu nad replikací vSphere a SRM. Známá rozhraní uživatelského rozhraní, rozhraní API a rozhraní příkazového řádku umožňují používat stávající skripty a nástroje.

![Nasazení Site Recovery Manageru](media/srm-deployment.png)

Můžete použít libovolné verze vRA a SRM, které jsou kompatibilní s Vaším privátním cloudem a místními prostředími. Příklady v této příručce používají vRA 6,5 a SRM 6,5. Tyto verze jsou kompatibilní s vSphere 6,5, který podporuje CloudSimple.

## <a name="deploy-the-solution"></a>Nasazení řešení

Následující části popisují, jak nasadit řešení zotavení po havárii pomocí SRM ve vašem privátním cloudu.

1. [Ověřte, zda jsou verze produktu VMware kompatibilní.](#verify-that-vmware-product-versions-are-compatible)
2. [Odhad velikosti prostředí DR](#estimate-the-size-of-your-dr-environment)
3. [Vytvoření privátního cloudu pro vaše prostředí](#create-a-private-cloud-for-your-environment)
4. [Nastavení sítě privátního cloudu pro řešení SRM](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Nastavte připojení VPN typu Site-to-site mezi vaší místní sítí a privátním cloudem a otevřete požadované porty.](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Nastavení služeb infrastruktury ve vašem privátním cloudu](#set-up-infrastructure-services-in-your-private-cloud)
7. [Instalace zařízení replikace vSphere do místního prostředí](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Instalace zařízení replikace vSphere do prostředí privátního cloudu](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Instalace serveru SRM v místním prostředí](#install-srm-server-in-your-on-premises-environment)
10. [Instalace SRM serveru do privátního cloudu](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Ověřte, zda jsou verze produktu VMware kompatibilní.

Konfigurace v této příručce se řídí následujícími požadavky na kompatibilitu:

* Stejná verze SRM musí být nasazená v privátním cloudu a v místním prostředí.
* V privátním cloudu a v místním prostředí musí být nasazená stejná verze replikace vSphere.
* Verze řadiče služeb platformy (PSC) ve vašem privátním cloudu a v místním prostředí musí být kompatibilní.
* Verze vCenter v privátním cloudu a v místním prostředí musí být kompatibilní.
* Verze replikace SRM a vSphere musí být vzájemně kompatibilní a s verzemi PSC a vCenter.

Odkazy na relevantní dokumentaci k VMware a informace o kompatibilitě najdete v dokumentaci k [vmware Site Recovery Manageru](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) .

Pokud chcete zjistit verze vCenter a PSC v privátním cloudu, otevřete portál CloudSimple. Přejděte na **prostředky**, vyberte svůj privátní cloud a klikněte na kartu **síť pro správu vSphere** .

![verze vCenter & PSC v privátním cloudu](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Odhad velikosti prostředí DR

1. Ověřte, jestli je vaše identifikovaná místní konfigurace v rámci podporovaných omezení. V případě SRM 6,5 jsou tato omezení popsána v článku znalostní báze VMware na základě [provozních omezení pro Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110).
2. Ujistěte se, že máte dostatečnou šířku pásma sítě, aby splňovala požadavky na velikost pracovního vytížení a RPO. Článek znalostní báze VMware o [výpočtu požadavků na šířku pásma pro replikaci vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) poskytuje pokyny k omezení šířky pásma.
3. Pomocí nástroje CloudSimple Sizer můžete odhadnout prostředky, které jsou potřeba na webu DR, a chránit tak vaše místní prostředí.

### <a name="create-a-private-cloud-for-your-environment"></a>Vytvoření privátního cloudu pro vaše prostředí

Vytvořte privátní cloud z portálu CloudSimple podle pokynů a určení velikosti v tématu [Vytvoření privátního cloudu](create-private-cloud.md).

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Nastavení sítě privátního cloudu pro řešení SRM

Přístup k portálu CloudSimple k nastavení sítě privátního cloudu pro řešení SRM.

Vytvořte síť VLAN pro síť řešení SRM a přiřaďte ji k podsíti CIDR. Pokyny najdete v tématu [Vytvoření a správa sítí VLAN a podsítí](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Nastavte připojení VPN typu Site-to-site mezi vaší místní sítí a privátním cloudem a otevřete požadované porty.

Nastavení propojení mezi lokalitami mezi vaší místní sítí a Vaším privátním cloudem. Pokyny najdete v tématu [Konfigurace připojení VPN k privátnímu cloudu CloudSimple](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Nastavení služeb infrastruktury ve vašem privátním cloudu

Konfigurujte služby infrastruktury v privátním cloudu, abyste usnadnili správu úloh a nástrojů.

Můžete přidat externího poskytovatele identity, jak je popsáno v tématu [použití Azure AD jako zprostředkovatele identity pro vCenter v CloudSimple privátním cloudu](azure-ad.md) , pokud chcete provést některou z následujících akcí:

* Identifikujte uživatele z místní služby Active Directory (AD) ve vašem privátním cloudu.
* Nastavte reklamu pro všechny uživatele ve vašem privátním cloudu.
* Použijte Azure AD.

Pokud chcete pro vaše úlohy v privátním cloudu poskytnout vyhledávání IP adres, správu IP adres a služby překladu IP adres, nastavte server DHCP a DNS, jak je popsáno v tématu [nastavení aplikací DNS a DHCP a úloh v privátním cloudu CloudSimple](dns-dhcp-setup.md).

Doména *. cloudsimple.io se používá v rámci virtuálních počítačů pro správu a hostitelů v privátním cloudu. Chcete-li vyřešit požadavky na tuto doménu, nakonfigurujte předávání DNS na serveru DNS, jak je popsáno v tématu [Vytvoření podmíněného předávání](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Instalace zařízení replikace vSphere do místního prostředí

Nainstalujte zařízení vSphere Replication (vRA) do místního prostředí podle pokynů v dokumentaci k VMware. Instalace se skládá z těchto kroků vysoké úrovně:

1. Připravte své místní prostředí pro instalaci vRA.

2. Nasaďte vRA do svého místního prostředí pomocí OVF v VR ISO od vmware.com. V případě vRA 6,5 obsahuje [Tento blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) relevantní informace.

3. Zaregistrujte své místní vRA pomocí vCenter Single Sign-On v místní lokalitě. Podrobné pokyny pro vSphere Replication 6,5 najdete v VMware vSphere dokumentu VMware o [instalaci a konfiguraci replikace 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>Instalace zařízení replikace vSphere do prostředí privátního cloudu

Než začnete, ověřte, že máte následující:

* Dostupnost IP adres z podsítí v místním prostředí do podsítě pro správu vašeho privátního cloudu
* Dostupnost IP adresy z podsítě replikace v místním prostředí vSphere do podsítě řešení SRM vašeho privátního cloudu

Pokyny najdete v tématu [Konfigurace připojení VPN k privátnímu cloudu CloudSimple](set-up-vpn.md). Postup je podobný jako u místní instalace.

CloudSimple doporučuje použití plně kvalifikovaných názvů domény namísto IP adres během instalace vRA a SRM. Pokud chcete zjistit plně kvalifikovaný název domény vCenter a PSC v privátním cloudu, otevřete portál CloudSimple. Přejděte na **prostředky**, vyberte svůj privátní cloud a klikněte na kartu **síť pro správu vSphere** .

![Hledání plně kvalifikovaného názvu domény vCenter/PSC v privátním cloudu](media/srm-resources.png)

CloudSimple vyžaduje, abyste nenainstalovali vRA a SRM pomocí výchozího uživatele cloudowner, ale místo toho vytvoříte nového uživatele. To vám umožní zajistit vysokou dobu provozu a dostupnost vašeho privátního cloudového prostředí vCenter. Výchozí uživatel cloudowner v privátním cloudu vCenter does't má ale dostatečná oprávnění k vytvoření nového uživatele s oprávněními správce.

Před instalací vRA a SRM musíte eskalovat oprávnění vCenter uživatele cloudowner a pak vytvořit uživatele s oprávněními správce v doméně jednotného přihlašování (vCenter). Podrobnosti o výchozím modelu uživatele a oprávnění privátního cloudu najdete v tématu [informace o modelu oprávnění privátního cloudu](learn-private-cloud-permissions.md).

Instalace se skládá z těchto kroků vysoké úrovně:

1. [Eskalace oprávnění](escalate-private-cloud-privileges.md).
2. Vytvořte uživatele v privátním cloudu pro replikaci vSphere a instalaci SRM. V uživatelském rozhraní vCenter je vysvětleno níže [: vytvoření uživatele v privátním cloudu pro instalaci vRA & SRM](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation).
3. Připravte prostředí privátního cloudu pro instalaci vRA.
4. Nasaďte vRA do privátního cloudu pomocí OVF v VR ISO od vmware.com. V případě vRA 6,5 má [Tento blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) relevantní informace.
5. Nakonfigurujte pravidla brány firewall pro vRA. Je vysvětleno níže v [portálu CloudSimple: Konfigurace pravidel brány firewall pro vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. V privátním cloudu Zaregistrujte vRA privátního cloudu pomocí vCenter Single Sign-On.
7. Nakonfigurujte připojení replikace vSphere mezi oběma zařízeními. Zajistěte, aby byly požadované porty otevřeny v rámci bran firewall. Seznam čísel portů, která musí být otevřená pro vSphere replikaci 6,5, najdete v [tomto článku znalostní báze VMware](https://kb.vmware.com/s/article/2087769) .

Podrobné pokyny k instalaci pro vSphere Replication 6,5 najdete v VMware vSphere dokumentu VMware [instalace a konfigurace replikace 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>uživatelské rozhraní vCenter: vytvoření uživatele v privátním cloudu pro instalaci vRA a SRM

Přihlaste se k vCenter pomocí přihlašovacích údajů uživatele cloudowner po eskalaci oprávnění z portálu CloudSimple.

Vytvořte nového uživatele, `srm-soln-admin` v vCenter a přidejte ho do skupiny Administrators v vCenter.
Odhlaste se z vCenter jako uživatel cloudowner a přihlaste se jako uživatel *SRM-soln-admin* .

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>Portál CloudSimple: Konfigurace pravidel brány firewall pro vRA

Nakonfigurujte pravidla brány firewall, jak je popsáno v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md) na otevřené porty, aby bylo možné komunikovat mezi těmito nástroji:

* vRA v síti řešení SRM a v hostitelích vCenter a ESXi v síti pro správu.
* zařízení vRA na těchto dvou lokalitách.

Seznam čísel portů, která musí být otevřená pro vSphere replikaci 6,5, najdete v tomto [článku znalostní báze VMware](https://kb.vmware.com/s/article/2087769) .

### <a name="install-srm-server-in-your-on-premises-environment"></a>Instalace serveru SRM v místním prostředí

Než začnete, ověřte následující:

* Zařízení replikace vSphere se instaluje do místních a privátních cloudových prostředí.
* Zařízení pro replikaci vSphere na obou lokalitách jsou vzájemně propojená.
* Zkontrolovali jste informace VMware o požadavcích a osvědčených postupech. V případě SRM 6,5 se můžete podívat na požadavky na dokumenty VMware [a osvědčené postupy pro SRM 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Dodržujte dokumentaci VMware a proveďte instalaci serveru SRM v modelu nasazení topologie dvou lokalit s jednou instancí vCenter na řadič služeb platformy, jak je popsáno v tomto [dokumentu VMware](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Pokyny k instalaci pro SRM 6,5 jsou k dispozici v dokumentu VMware [Instalování Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-private-cloud"></a>Instalace SRM serveru do privátního cloudu

Než začnete, ověřte následující:

* Zařízení replikace vSphere se instaluje do místních a privátních cloudových prostředí.
* Zařízení pro replikaci vSphere na obou lokalitách jsou vzájemně propojená.
* Zkontrolovali jste informace VMware o požadavcích a osvědčených postupech. V případě SRM 6,5 můžete odkazovat na [požadavky a osvědčené postupy pro instalaci serveru Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Následující kroky popisují instalaci privátního cloudu SRM.

1. [uživatelské rozhraní vCenter: Nainstalujte SRM](#vcenter-ui-install-srm)
2. [Portál CloudSimple: Konfigurace pravidel brány firewall pro SRM](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [uživatelské rozhraní vCenter: Konfigurace SRM](#vcenter-ui-configure-srm)
4. [Portál CloudSimple: oprávnění de-eskalace](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>uživatelské rozhraní vCenter: Nainstalujte SRM

Po přihlášení na Server vCenter pomocí přihlašovacích údajů SRM-soln-admin Sledujte dokumentaci VMware a proveďte instalaci serveru SRM v modelu nasazení topologie dvou lokalit s jednou instancí vCenter na řadič služeb platformy, jak je popsáno v tomto [dokumentu VMware](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Pokyny k instalaci pro SRM 6,5 jsou k dispozici v dokumentu VMware [Instalování Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>Portál CloudSimple: Konfigurace pravidel brány firewall pro SRM

Nakonfigurujte pravidla brány firewall, jak je popsáno v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md) tak, aby umožňovaly komunikaci mezi:

Server SRM a vCenter/PSC v privátním cloudu.
Servery SRM na obou webech

Seznam čísel portů, která musí být otevřená pro vSphere replikaci 6,5, najdete v [tomto článku znalostní báze VMware](https://kb.vmware.com/s/article/2087769) .

#### <a name="vcenter-ui-configure-srm"></a>uživatelské rozhraní vCenter: Konfigurace SRM

Po instalaci SRM do privátního cloudu proveďte následující úlohy, jak je popsáno v částech Průvodce instalací a konfigurací nástroje VMware Site Recovery Manager. V případě SRM 6,5 jsou pokyny k dispozici v dokumentu VMware [Instalování Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Připojte instance serveru nástroje Site Recovery Manager na webu chráněného a obnovení.
2. Navažte připojení klienta k instanci serveru vzdálené správy Site Recovery.
3. Nainstalujte licenční klíč nástroje Site Recovery Manager.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>Portál CloudSimple: oprávnění de-eskalace

Chcete-li zrušit oprávnění, přečtěte si téma oprávnění ke zrušení [Eskalace](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Průběžná správa řešení SRM

Máte plnou kontrolu nad replikací vSphere a SRM software ve vašem prostředí privátního cloudu a očekáváte, že vykonáte nezbytnou správu životního cyklu softwaru. Před aktualizací nebo upgradem replikace vSphere nebo SRM zajistěte, aby byla nová verze softwaru kompatibilní s privátním cloudem vCenter a PSC.

> [!NOTE]
> CloudSimple v současné době zkoumá možnosti pro nabídku spravované služby zotavení po havárii. 

## <a name="multiple-replication-configuration"></a>Konfigurace vícenásobné replikace

 [Replikaci založené na poli a technologie vSphere replikace je možné používat společně s SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) ve stejnou dobu. Musí se ale použít pro samostatnou sadu virtuálních počítačů (daný virtuální počítač se dá chránit buď replikací založenými na poli, nebo replikací vSphere, ale ne oběma). Kromě toho je možné lokalitu CloudSimple nakonfigurovat jako lokalitu pro obnovení pro více chráněných webů. Informace o konfiguracích s více lokalitami najdete v tématu [SRM možnosti pro více lokalit](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) .

## <a name="references"></a>Reference

* [Dokumentace k VMware Site Recovery Manageru](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Provozní omezení pro Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110)
* [Výpočet požadavků na šířku pásma pro replikaci vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [OVF možnosti při nasazení vSphere replikace 6,5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere instalace a konfigurace replikace 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Předpoklady a osvědčené postupy pro SRM 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery Manager v topologii Two-Site s jednou instancí vCenter Server na řadič služeb platformy](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Průvodce instalací a konfigurací nástroje VMware Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Blog VMware v SRM s replikací založenými na poli a replikací vSphere](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [Blog VMware na SRM možnosti pro více lokalit](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Čísla portů, která musí být otevřená pro vSphere replikaci na 5,8. x, 6. x a 8](https://kb.vmware.com/s/article/2147112)
