---
title: Řešení Azure VMware (AVS) – k hostování infrastruktury virtuálních klientských počítačů pomocí VMware horizontu použijte lokalitu privátního cloudu služby AVS
description: Popisuje, jak můžete použít svůj privátní cloudový web služby AVS k hostování infrastruktury virtuálních klientů pomocí horizontu VMware.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f909ce297344e80ed6004631d5218d6a7fcd5085
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025244"
---
# <a name="use-avs-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Použití lokality privátního cloudu funkce AVS k hostování infrastruktury virtuálních klientských počítačů pomocí horizontu VMware

K hostování infrastruktury virtuálních klientských počítačů (VDI) pomocí VMware horizont 7. x můžete použít svůj privátní cloudový web služby AVS. Následující obrázek ukazuje architekturu logického řešení pro infrastrukturu virtuálních klientských počítačů (VDI).

![Nasazení horizontu](media/horizon-deployment.png)

V tomto řešení máte plnou kontrolu nad správcem zobrazení horizontu a se svazkem aplikace. Známá rozhraní uživatelského rozhraní, rozhraní API a rozhraní příkazového řádku umožňují používat stávající skripty a nástroje.

Řešení AVS vyžaduje, abyste provedete následující kroky:

* Instalace, konfigurace a Správa VMware Horizon 7. x v privátním cloudu služby AVS
* Poskytněte své vlastní licence na horizont.

## <a name="deploy-the-solution"></a>Nasazení řešení

Následující části popisují, jak nasadit řešení VDI pomocí horizontu v privátním cloudu služby AVS.

1. [Ověřte, zda jsou verze produktu VMware kompatibilní.](#verify-that-vmware-product-versions-are-compatible)
2. [Odhad velikosti desktopového prostředí](#estimate-the-size-of-your-desktop-environment)
3. [Vytvoření privátního cloudu služby AVS pro vaše prostředí](#create-an-avs-private-cloud-for-your-environment)
4. [Nainstalovat VMware Horizon do privátního cloudu služby AVS](#install-vmware-horizon-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Ověřte, zda jsou verze produktu VMware kompatibilní.

* Ověřte, že vaše aktuální a plánované verze nástroje Horizon, svazky aplikací, brána Unified Access a správce uživatelského prostředí jsou vzájemně kompatibilní a s vCenter a PSC v privátním cloudu služby AVS. Informace o kompatibilitě najdete v tématu [matice kompatibility VMware pro horizont 7,5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Pokud chcete zjistit aktuální verze vCenter a PSC v privátním cloudu služby AVS, přejděte na **prostředky** na [portálu služby AVS](access-cloudsimple-portal.md), vyberte svůj privátní cloud služby AVS a klikněte na kartu **síť pro správu vSphere** .

![verze vCenter a PSC](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Odhad velikosti desktopového prostředí

* Ověřte, jestli je vaše identifikovaná konfigurace v rámci provozních omezení VMware.
* Odhadujte prostředky, které jsou potřeba pro všechny vaše desktopové a řídicí komponenty vašeho horizontu.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>Vytvoření privátního cloudu služby AVS pro vaše prostředí

1. Pomocí pokynů v části [konfigurace privátního cloudu](quickstart-create-private-cloud.md)služby AVS vytvořte privátní cloud služby AVS z portálu AVS. V každém nově vytvořeném privátním cloudu pro funkci AVS vytvoří aplikace AVS výchozího uživatele vCenter s názvem "cloudowner". Podrobnosti o výchozím uživatelském a přístupovém modelu privátního cloudu služby AVS najdete v tématu [informace o modelu oprávnění privátního cloudu služby AVS](learn-private-cloud-permissions.md).
2. Vytvořte v privátním cloudu služby AVS síť VLAN pro rovinu správy horizontu a přiřaďte ji k podsíti CIDR. Pokyny najdete v tématu [Vytvoření a správa sítí VLAN a podsítí](create-vlan-subnet.md). Toto je síť, ve které se nainstalují všechny součásti řešení (Unified Access Gateway, server připojení, server svazku aplikace a servery správce prostředí uživatele).
3. Rozhodněte, jestli chcete použít externího poskytovatele identity s Vaším privátním cloudem služby AVS. Pokud ano, vyberte jednu z těchto možností:
    * Použijte místní službu Active Directory jako externího zprostředkovatele identity. Pokyny najdete v tématu [zdroje identity vCenter](set-vcenter-identity.md).
    * V privátním cloudu služby AVS nastavte Server Active Directory, který se použije jako externí poskytovatel identity. Pokyny najdete v tématu [zdroje identity vCenter](set-vcenter-identity.md).
    * V privátním cloudu služby AVS nastavte server DHCP a DNS v síti VLAN plochy správy horizontu. Pokyny najdete v tématu [nastavení aplikací DNS a DHCP a úloh v privátním cloudu služby AVS](dns-dhcp-setup.md).
4. Nakonfigurujte předávání DNS na serveru DNS nainstalovaném v privátním cloudu služby AVS. Pokyny najdete v tématu [Vytvoření podmíněného předávacího serveru](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-avs-private-cloud"></a>Nainstalovat VMware Horizon do privátního cloudu služby AVS

Následující diagram nasazení znázorňuje řešení Horizon nasazené v privátním cloudu služby AVS. V uživatelsky definovaných sítích VLAN 234 se nainstaluje služba Unified Access Gateway, AD/DC, View a Volume server aplikace. Brána Unified Access má přiřazenou veřejnou IP adresu, která je dosažitelná z Internetu. Virtuální počítače ve fondu plochy s horizontem jsou nasazené v síti VLAN 235, které poskytují další izolaci a zabezpečení.

![Nasazení horizontu v privátním cloudu AVS](media/horizon-private-cloud.png)

V následujících částech najdete informace o tom, jak nastavit nasazení podobné jako na obrázku, který je znázorněný na obrázku. Než začnete, ověřte, že máte následující:

* Privátní cloud služby AVS vytvořený pomocí portálu služby AVS s dostatečnou kapacitou pro spouštění fondů plochy.
* Dostatečnou šířku pásma mezi místním prostředím a prostředím privátního cloudu služby AVS pro podporu síťového provozu pro stolní počítače.
* Tunel VPN typu Site-to-site se nastavuje mezi místním datacentrem a privátním cloudem služby AVS.
* Dostupnost IP adres z podsítí koncových uživatelů v místním prostředí do podsítí privátního cloudu služby AVS
* Služba AD/DHCP/DNS je nainstalovaná pro váš privátní cloud služby AVS.

#### <a name="avs-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>Portál pro funkci AVS: vytvoření vyhrazené sítě VLAN/podsítě pro fondy počítačů

Vytvořte síť VLAN pro fondy stolních počítačů a přiřaďte ji k podsíti CIDR. Pokyny najdete v tématu [Vytvoření a správa sítí VLAN a podsítí](create-vlan-subnet.md). Jedná se o síť, ve které budou všechny virtuální počítače na ploše spuštěné.

Pro zabezpečení nasazení horizontu použijte standardní doporučené postupy zabezpečení:

* Povolí jenom provoz RDP na stolních počítačích jenom v provozu nebo přes SSH.
* Povoluje pouze provoz správy mezi sítí VLAN roviny správy a sítí VLAN fondu plochy.
* Povolí jenom provoz pro správu z místní sítě.

Tyto osvědčené postupy můžete vyhovět konfigurací [pravidel brány firewall](firewall.md) z portálu služby AVS.

#### <a name="avs-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Portál pro funkci AVS: Konfigurace pravidel brány firewall pro zabezpečenou rovinu správy horizontu

Na portálu AVS nastavte následující pravidla. Pokyny najdete v tématu [Nastavení tabulek a pravidel brány firewall](firewall.md).

1. Nakonfigurujte pravidla brány firewall v bráně firewall pro funkci AVS N-S, abyste umožnili komunikaci mezi místními podsítěmi a sítí VLAN pro správu horizontu, aby byly povolené jenom síťové porty uvedené v [seznamu portů VMware pro horizonty](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) dokumentů.

2. Vytvořte E-W pravidla brány firewall mezi sítí VLAN pro správu horizontu a sítí VLAN fondu ploch v privátním cloudu AVS.

#### <a name="avs-portal-create-a-public-ip-address-for-unified-access-gateway"></a>Portál služby AVS: vytvoření veřejné IP adresy pro bránu Unified Access Gateway

Vytvořte veřejnou IP adresu pro zařízení sjednocené přístupové brány, která povolí připojení klientů z Internetu. Pokyny najdete v tématu [přidělování veřejných IP adres](public-ips.md).

Po dokončení instalace se veřejná IP adresa přiřadí a zobrazí na stránce veřejné IP adresy.

#### <a name="avs-portal-escalate-privileges"></a>Portál pro funkci AVS: zvýšení oprávnění

Výchozí uživatel cloudowner nemá dostatečná oprávnění v případě instalace horizontu v privátním cloudu pro funkci AVS, takže musí být namířená oprávnění pro vCenter uživatele. Další informace najdete v tématu [Eskalace oprávnění](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-avs-private-cloud-for-horizon-installation"></a>uživatelské rozhraní vCenter: vytvoření uživatele v privátním cloudu služby AVS pro instalaci pomocí horizontu

1. Přihlaste se k vCenter pomocí pověření uživatele ' cloudowner '.
2. Vytvořte nového uživatele, ' horizont-soln-admin ', v vCenter a přidejte uživatele do skupiny Administrators v vCenter.
3. Odhlaste se z vCenter jako uživatel ' cloudowner ' a přihlaste se jako uživatel ' horizont-soln-admin '.

#### <a name="vcenter-ui-install-vmware-horizon"></a>uživatelské rozhraní vCenter: instalace horizontu VMware

Jak je uvedeno v předchozí části logické architektury, má řešení horizont následující komponenty:

* Zobrazení VMware Horizon
* Brána VMware Unified Access Gateway
* Správce svazků aplikace VMware
* Správce prostředí uživatele VMware

Součásti nainstalujte následujícím způsobem:

1. Nainstalujte a nakonfigurujte bránu Unified Access Gateway podle pokynů uvedených v dokumentu VMware [nasazení a konfigurace brány VMware Unified Access Gateway](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Pomocí pokynů v části [Zobrazit Instalační příručka](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)nainstalujte zobrazení horizontu do privátního cloudu AVS.

3. Podle pokynů v tématu [instalace a konfigurace svazků aplikace VMware](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)nainstalujte Správce svazků aplikace.

4. Nainstalujte a nakonfigurujte správce prostředí uživatele podle pokynů uvedených v části [o instalaci a konfiguraci správce prostředí pro správu uživatelů VMware](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Zabalení žádosti o podporu pro nahrání předbalených svazků aplikace v horizontu VMware

V rámci procesu instalace používá správce svazků aplikace předem zabalené svazky ke zřízení zásobníků aplikací a zapisovatelné svazky. Tyto svazky slouží jako šablony pro zásobníky aplikací a zapisovatelné svazky.

Nahrání svazků do úložiště dat privátního cloudu pro funkci AVS vyžaduje kořenové heslo ESXi. Potřebujete-li pomoc, odešlete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Připojte sadu instalačního programu AppVolumes, aby pracovníci podpory služby AVS mohli nahrávat šablony do prostředí privátního cloudu služby AVS.

#### <a name="avs-portal-de-escalate-privileges"></a>Portál pro funkci AVS: oprávnění ke zrušení eskalace

Nyní můžete zrušit [eskalaci oprávnění](escalate-private-cloud-privileges.md#de-escalate-privileges) uživatele ' cloudowner '.

## <a name="ongoing-management-of-your-horizon-solution"></a>Průběžná správa vašeho řešení horizontu

V prostředí privátního cloudu pro funkci AVS máte plnou kontrolu nad softwarem Horizontal and Volume Manager a očekáváte se, že se bude provádět nezbytná Správa životního cyklu softwaru. Zajistěte, aby všechny nové verze softwaru byly kompatibilní se službou AVS Private Cloud vCenter a PSC před aktualizací nebo upgradem svazku horizontu nebo aplikace.
