---
title: Nainstalovat VMware HCX
description: Nastavení řešení VMware HCX pro privátní cloud řešení Azure VMware
ms.topic: how-to
ms.date: 09/24/2020
ms.openlocfilehash: cdeffa41db5aac597d8dfcf3a735cbeb7f0d8a8e
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91370840"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Nainstalovat HCX pro řešení VMware pro Azure

V tomto článku Vás provedeme postupy pro nastavení řešení VMWare HCX pro privátní cloud řešení Azure VMWare. HCX umožňuje migrovat úlohy VMware do cloudu a další připojené lokality prostřednictvím různých integrovaných typů migrace HCX.

HCX Advanced – výchozí instalace podporuje až tři připojení lokality (v místním prostředí nebo cloudu do cloudu). Pokud potřebujete víc než tři připojení k webu nebo HCX [podnikové funkce](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) , zákazníci mají možnost povolit doplněk HCX Enterprise prostřednictvím podpory, která je aktuálně ve verzi Preview. HCX EE je k dispozici ve službě AVS jako funkce nebo služba ve verzi Preview. Zatímco HCX EE pro službu AVS je ve verzi Preview, je to bezplatná funkce nebo služba a podléhá podmínkám a ujednáním služby ve verzi Preview. Jakmile bude služba HCX EE v GA, dostanete 30denní oznámení o tom, že účtování bude přepnuto. Také budete mít možnost přepnout/odhlásit službu.


[Před zahájením, před začátkem](#before-you-begin), [požadavky na verzi softwaru](#software-version-requirements)a [požadavky nejprve důkladně](#prerequisites) prostudujte. 

Pak vás provedeme všemi potřebnými postupy:

> [!div class="checklist"]
> * Nasazení místní HCX vajíček (konektor)
> * Aktivace a konfigurace HCX
> * Konfigurace sítě pro odchozí připojení a síť služby
> * Dokončení instalace kontrolou stavu zařízení

Po dokončení instalace můžete postupovat podle doporučených dalších kroků uvedených na konci tohoto článku.  

## <a name="before-you-begin"></a>Než začnete
    
* Přečtěte si základní [řadu kurzů](tutorial-network-checklist.md)pro Azure VMware Solution software definované DATACENTER (SDDC).
* Projděte si [dokumentaci k VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) , která zahrnuje uživatelskou příručku HCX.
* Projděte si dokumentaci [k VMware s migrací Virtual Machines pomocí VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Volitelně si přečtěte téma [požadavky na nasazení VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Volitelně si můžete prohlédnout související materiály VMware na HCX, jako je například [série blogů](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) VMware VSPHERE v HCX. 
* Vyžádejte si podnikovou aktivaci řešení Azure VMware HCX prostřednictvím kanálů podpory řešení Azure VMware.

Při přípravě na použití řešení Azure VMware pro privátní cloud HCX je zásadní krok plánování pro změnu velikosti úloh na výpočetní prostředky a prostředky úložiště. Vyřešte krok změny velikosti jako součást prvotního plánování prostředí privátního cloudu. 

Velikost úloh můžete také provádět provedením [posouzení řešení Azure VMware](../migrate/how-to-create-azure-vmware-solution-assessment.md) na portálu Azure Migrate.

## <a name="software-version-requirements"></a>Požadavky na verzi softwaru

Na součástech infrastruktury musí běžet požadovaná minimální verze. 
                                                         
| Typ součásti    | Požadavky na zdrojové prostředí    | Požadavky na cílové prostředí   |
| --- | --- | --- |
| vCenter Server   | 5,1<br/><br/>Pokud používáte 5,5 U1 nebo starší, použijte samostatné uživatelské rozhraní HCX pro operace HCX.  | 6,0 U2 a vyšší   |
| ESXi   | 5,0    | ESXi 6,0 a vyšší   |
| NSX    | Pro HCX síťové rozšíření logických přepínačů ve zdroji: NSXv 6.2 + nebo NSX-T 2.4 +   | NSXv 6.2 + nebo NSX-T 2.4 +<br/><br/>Pro směrování blízkosti HCX: NSXv 6.4 + (pro NSX-T není podporováno směrování blízkosti). |
| vCloud ředitel   | Nepožadováno – bez interoperability s vCloud ředitelem ve zdrojové lokalitě | Při integraci cílového prostředí s vCloud Directorem je minimum 9.1.0.2.  |

## <a name="prerequisites"></a>Požadavky

* ExpressRoute Global Reach by měly být nakonfigurované mezi místními a SDDC ExpressRoutemi okruhy v rámci řešení Azure VMware.

* Všechny požadované porty by se měly otevírat mezi místními a Azure VMware Solution SDDC (viz [dokumentace k VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Jedna IP adresa pro HCX Manager v místním prostředí a minimálně dvě IP adresy pro zařízení Interconnect (IX) a rozšíření sítě (NE).

* Místní HCX IX a NE zařízení by měly být schopné dosáhnout infrastruktury vCenter a ESXi.

* Aby bylo možné nasadit zařízení WAN Interconnect, konkrétní rozsahy CIDR jsou již od zákazníka přiděleny \ 22 pro vytvoření privátního cloudu.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Nasazení HCX vajíček v místním prostředí VMware

1. Přihlaste se ke službě Azure VMware Solution Manager HCX Manager na `https://x.x.x.9` portu 443 s přihlašovacími údaji uživatele **cloudadmin** a pak pokračujte na **podporu**.

1. Vyberte odkaz ke stažení pro soubor VMware HCX vajíček, který se má nasadit do vCenter.

1. Přejít na místní Server vCenter a vybrat nově staženou šablonu OVF pro nasazení do místního serveru vCenter.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-template.png" alt-text="Pak přejdete na místní Server vCenter a vyberte šablonu OVF, která se nasadí do místního serveru vCenter.":::

1. Vyberte název a umístění a pak vyberte prostředek/cluster, kde musí být nasazený HCX. Pak zkontrolujte podrobnosti a požadované prostředky.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-template.png" alt-text=" Vyberte název a umístění a pak vyberte prostředek/cluster, kde musí být nasazený HCX. Pak zkontrolujte podrobnosti a požadované prostředky.":::

1. Přečtěte si licenční podmínky a pokud souhlasíte, vyberte požadované úložiště a síť. Pak vyberte **Další**.

1. Do **vlastní šablony**zadejte všechny požadované informace. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/customize-template.png" alt-text="Do vlastní šablony zadejte všechny požadované informace.":::

1. Vyberte **Další**, ověřte konfiguraci a vyberte **Dokončit** pro nasazení HCX vajíček.

## <a name="activate-hcx"></a>Aktivovat HCX

Po instalaci proveďte následující kroky.

1. Přihlaste se k místnímu HCX Manageru `https://HCXManagerIP:9443` a přihlaste se pomocí přihlašovacích údajů pro uživatelské jméno **správce** . 

   > [!IMPORTANT]
   > Nezapomeňte zahrnout `9443` číslo portu s IP adresou HCX Manageru.

1. V nástroji **licencování**zadejte svůj **HCX rozšířený klíč**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-key.png" alt-text="V nástroji licencování zadejte svůj HCX rozšířený klíč.":::
    
    > [!NOTE]
    > HCX Manager musí mít nakonfigurován otevřený přístup k Internetu nebo proxy.

1. V případě potřeby upravte informace vCenter v **vCenter**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-vcenter.png" alt-text="V případě potřeby upravte informace vCenter v vCenter.":::

1. V části **umístění Datacenter**v případě potřeby upravte umístění datového centra.

   :::image type="content" source="media/hybrid-cloud-extension-installation/system-location.png" alt-text="V části umístění Datacenter v případě potřeby upravte umístění datového centra.":::

## <a name="configure-hcx"></a>Konfigurace HCX 

1. Přihlaste se k místnímu serveru vCenter a v části **Domů**vyberte **HCX**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-vcenter.png" alt-text="Přihlaste se k místnímu serveru vCenter a v části domů vyberte HCX.":::

1. V části **infrastruktura**vyberte **párování lokalit**  >  **Přidat párování webu**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-pairing.png" alt-text="V části infrastruktura vyberte párování lokalit > přidat párování lokalit.":::

1. Zadejte adresu URL nebo IP adresu vzdáleného HCX, Azure VMware Solution cloudadmin uživatelské jméno a heslo a pak vyberte **připojit**.

   V systému se zobrazuje připojená lokalita.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-connection.png" alt-text="V systému se zobrazuje připojená lokalita.":::

1. V části **infrastruktura**vyberte **propojit**  >  **službu Multi-Site**  >  **Network profily sítě**  >  **vytvořit profil sítě**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-network-profile.png" alt-text="V části infrastruktura vyberte Interconnect > síť pro více lokalit > profily sítě > vytvořit profil sítě.":::

1. Pro nový profil sítě zadejte HCX IX a NE rozsahy IP adres (minimálně dvě IP adresy se vyžadují pro zařízení IX a NE).

   :::image type="content" source="media/hybrid-cloud-extension-installation/enter-address-ranges.png" alt-text="Pro nový profil sítě zadejte HCX IX a NE rozsahy IP adres (minimálně dvě IP adresy se vyžadují pro zařízení IX a NE).":::
  
   > [!NOTE]
   > Zařízení rozšíření sítě (HCX-NE) má relaci 1:1 s distribuovaným virtuálním přepínačem (DVS).  

1. Teď vyberte **COMPUTE profil**  >  **vytvořit výpočetní profil**.

1. Zadejte název výpočetního profilu a vyberte **pokračovat**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-compute-profile.png" alt-text="Zadejte název výpočetního profilu a vyberte pokračovat.":::

1. Vyberte služby, které chcete povolit, například migraci, síťové rozšíření nebo zotavení po havárii, a pak vyberte **pokračovat**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-services.png" alt-text="Vyberte služby, které chcete povolit, například migraci, síťové rozšíření nebo zotavení po havárii, a pak vyberte pokračovat.":::

1. V části **vybrat prostředky služby**vyberte jeden nebo více prostředků služby, pro které by měly být povolené vybrané služby HCX. Vyberte **pokračovat**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-service-resources.png" alt-text="V části vybrat prostředky služby vyberte jeden nebo více prostředků služby, pro které by měly být povolené vybrané služby HCX. Vyberte pokračovat.":::
  
   > [!NOTE]
   > Vyberte konkrétní clustery, ve kterých jsou zdrojové virtuální počítače cílené na migraci pomocí HCX.

1. Vyberte **úložiště dat** a vyberte **pokračovat**. 
      
   Vyberte jednotlivé výpočetní prostředky a prostředky úložiště pro nasazení zařízení HCX Interconnect. Pokud je vybráno více prostředků, HCX použije první vybraný prostředek, dokud nebude jeho kapacita vyčerpána.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployment-resources.png" alt-text="Vyberte jednotlivé výpočetní prostředky a prostředky úložiště pro nasazení zařízení HCX Interconnect. Pokud je vybráno více prostředků, HCX použije první vybraný prostředek, dokud nebude jeho kapacita vyčerpána.":::

1. Vyberte profil sítě pro správu vytvořený v seznamu **síťové profily** a vyberte **pokračovat**.  
      
   Vyberte profil sítě, pomocí kterého je možné dosáhnout rozhraní pro správu serveru vCenter a hostitele ESXi. Pokud jste tento profil sítě ještě nedefinovali, můžete ho vytvořit tady.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/management-network-profile.png" alt-text="Vyberte profil sítě, pomocí kterého je možné dosáhnout rozhraní pro správu serveru vCenter a hostitele ESXi. Pokud jste tento profil sítě ještě nedefinovali, můžete ho vytvořit tady.":::

1. Vyberte **odchozí připojení k síti** a vyberte **pokračovat**.
      
   Vyberte jeden nebo více profilů sítě, aby byla splněna jedna z následujících podmínek:  
   * Zařízení Interconnect na vzdálené lokalitě se dají kontaktovat přes tuto síť.  
   * Zařízení na dálku se můžou připojit k zařízením s místním propojením přes tuto síť.  
    
   Pokud máte sítě typu Point-to-Point, jako je přímá připojení, které nejsou sdíleny napříč více lokalitami, můžete tento krok přeskočit, protože jsou výpočetní profily sdíleny s více lokalitami. V takových případech se profily sítě pro odesílání můžou přepsat a zadat během vytváření sítě služby InterConnect.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/uplink-network-profile.png" alt-text="Vyberte odchozí připojení k síti a vyberte pokračovat.":::

1. Vyberte **vMotion síťový profil** a vyberte **pokračovat**.
      
   Vyberte profil sítě, přes který je možné dosáhnout rozhraní vMotion hostitelů ESXi. Pokud jste tento profil sítě ještě nedefinovali, můžete ho vytvořit tady. Pokud nemáte vMotion síť, vyberte možnost **profil sítě pro správu**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vmotion-network-profile.png" alt-text="Vyberte vMotion síťový profil a vyberte pokračovat.":::

1. V části **Vybrat profil sítě pro replikaci vSphere**vyberte profil sítě vSphere replikačního rozhraní hostitelů ESXi a pak vyberte **pokračovat**.
      
   Ve většině případů je tento profil stejný jako síťový profil pro správu.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png" alt-text="V části vybrat profil sítě pro replikaci vSphere vyberte profil sítě vSphere replikačního rozhraní hostitelů ESXi a pak vyberte pokračovat.":::

1. V části **Výběr distribuovaných přepínačů pro síťová rozšíření**vyberte Služba DVS, na které máte sítě, které budou integrované a připojené k virtuálním počítačům.  Vyberte **pokračovat**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/distributed-switches.png" alt-text="V části Výběr distribuovaných přepínačů pro síťová rozšíření vyberte Služba DVS, na které máte sítě, které budou integrované a připojené k virtuálním počítačům.  Vyberte pokračovat.":::

1. Zkontrolujte pravidla připojení a vyberte **pokračovat**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-compute-profile.png" alt-text="Zkontrolujte pravidla připojení a vyberte pokračovat.":::

1.  Vyberte **Dokončit** a vytvořte tak výpočetní profil.

## <a name="configure-network-uplink"></a>Konfigurace odchozího připojení k síti

Teď nakonfigurujte změnu profilu sítě v Azure VMware Solution SDDC pro odchozí připojení k síti.

1. Přihlaste se k SDDC NSX-T pro vytvoření nového logického přepínače nebo použijte existující logický přepínač, který se dá použít pro odchozí připojení k síti mezi místními a Azure VMware Solution SDDC.

1. Vytvořte profil sítě pro odchozí připojení HCX v Azure VMware Solution SDDC, které se dá použít pro komunikaci mezi místními a Azure VMware řešení SDDC.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-profile-uplink.png" alt-text="Vytvořte profil sítě pro odchozí připojení HCX v Azure VMware Solution SDDC, které se dá použít pro komunikaci mezi místními a Azure VMware řešení SDDC.":::

1. Zadejte název profilu sítě a minimálně 4-5 bezplatných IP adres na základě potřebného rozšíření sítě L2.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-uplink-profile.png" alt-text="Zadejte název profilu sítě a minimálně 4-5 bezplatných IP adres na základě potřebného rozšíření sítě L2.":::

1. Výběrem **vytvořit** DOKONČÍTE konfiguraci SDDC řešení Azure VMware.

## <a name="configure-service-mesh"></a>Konfigurace sítě

Teď nakonfigurujte síť mezi místními a SDDC řešení Azure VMware.

1. Přihlaste se k Azure VMware Solution SDDC vCenter a vyberte **HCX**.

2. V části **infrastruktura**vyberte **Interconnect**  >  **Service sítě**  >  **vytvořit síť** a nakonfigurujte profily sítě a výpočetní profily vytvořené v předchozích krocích.    

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-service-mesh.png" alt-text="V části infrastruktura vyberte Interconnect > Service Network > vytvořit síť služby a nakonfigurujte síťové a výpočetní profily vytvořené v předchozích krocích.":::

3. Vyberte spárované weby a povolte hybridní možnosti a vyberte **pokračovat**.   

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-paired-sites.png" alt-text="Vyberte spárované weby a povolte hybridní možnosti a vyberte pokračovat.":::

4. Vyberte zdrojové a vzdálené výpočetní profily pro povolení služeb hybridního prostředí a vyberte **pokračovat**.
      
   Výběry definují prostředky, ve kterých virtuální počítače můžou využívat služby HCX.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/enable-hybridity.png" alt-text="Výběry definují prostředky, ve kterých virtuální počítače můžou využívat služby HCX.":::

5. Vyberte služby, které chcete povolit, a vyberte **pokračovat**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-services.png" alt-text="Vyberte služby, které chcete povolit, a vyberte pokračovat.":::

6. V **rozšířené konfiguraci – přepsat síťové profily pro odesílání** vyberte **pokračovat**.  
      
   Síťové profily odchozího připojení se používají pro připojení k síti, přes kterou je možné oslovit zařízení s propojením vzdálené lokality.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/override-uplink-profiles.png" alt-text="Síťové profily odchozího připojení se používají pro připojení k síti, přes kterou je možné oslovit zařízení s propojením vzdálené lokality.":::

7. Vyberte možnost **Konfigurovat zařízení rozšíření sítě škálovat**. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-extension-scale-out.png" alt-text="Vyberte možnost konfigurovat zařízení rozšíření sítě škálovat.":::

8. Zadejte počet zařízení odpovídající počtu přepínačů Služba DVS.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/appliance-scale.png" alt-text="Zadejte počet zařízení odpovídající počtu přepínačů Služba DVS.":::

9. Chcete-li přeskočit, vyberte **pokračovat** .  

   :::image type="content" source="media/hybrid-cloud-extension-installation/traffic-engineering.png" alt-text="Chcete-li přeskočit, vyberte pokračovat.":::

10. Přečtěte si náhled topologie a vyberte **pokračovat**. 

11. Zadejte uživatelsky přívětivý název pro tuto síť a vyberte **Dokončit** .  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-service-mesh.png" alt-text="Dokončete síť služby.":::

   Síť je nasazená a nakonfigurovaná.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployed-service-mesh.png" alt-text="Nasazená síť služby":::

## <a name="check-appliance-status"></a>Kontrolovat stav zařízení
Pokud chcete zjistit stav zařízení, vyberte **propojit**  >  **zařízení**. 

:::image type="content" source="media/hybrid-cloud-extension-installation/appliance-status.png" alt-text="Ověřte stav zařízení.":::

## <a name="next-steps"></a>Další kroky

Když je **stav tunelového propojení** zařízení v režimu **nahoru** a zelený, budete připraveni k migraci a ochraně virtuálních počítačů s řešeními Azure VMware pomocí HCX. Další informace najdete v [dokumentaci k VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) a [migrace Virtual Machines s využitím VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) v technické dokumentaci k VMware.
