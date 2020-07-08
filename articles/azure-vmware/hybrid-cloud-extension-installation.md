---
title: Instalace hybridního cloudového rozšíření (HCX)
description: Nastavení řešení HCX (VMware Hybrid Cloud Extension) pro privátní cloud Azure VMware Solution (AVS)
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: 3037d12ebbb036098cfc00a42521513bc2df6170
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367542"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Nainstalovat HCX pro řešení VMware pro Azure

V tomto článku se seznámíme s postupy pro nastavení řešení HCX (VMWare Hybrid Cloud Extension) pro privátní cloud řešení Azure VMWare (AVS). HCX umožňuje migrovat úlohy VMware do cloudu a další připojené lokality prostřednictvím různých integrovaných typů migrace HCX.

HCX Advanced – výchozí instalace podporuje až tři externí weby. Pokud potřebujete víc než tři lokality, zákazníci mají možnost povolit doplněk HCX Enterprise prostřednictvím podpory. HCX Enterprise Installation pro zákazníky přináší další poplatky za obecnou dostupnost (GA), ale poskytuje [Další funkce](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).


[Před zahájením, před začátkem](#before-you-begin), [požadavky na verzi softwaru](#software-version-requirements)a [požadavky nejprve důkladně](#prerequisites) prostudujte. 

Pak projdeme všemi potřebnými postupy:

> [!div class="checklist"]
> * Nasazení místní HCXé VAJÍČKy
> * Aktivace a konfigurace HCX
> * Konfigurace sítě pro odchozí připojení a síť služby
> * Dokončení instalace kontrolou stavu zařízení

Po dokončení instalace můžete postupovat podle doporučených dalších kroků uvedených na konci tohoto článku.  

## <a name="before-you-begin"></a>Než začnete
    
* Přečtěte si část [kurz](tutorial-network-checklist.md) Basic AVS software definované datacentrum (SDDC).
* Přečtěte si [dokumentaci k VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) , včetně uživatelské příručky HCX.
* Kontrola dokumentů VMware [s migrací Virtual Machines pomocí VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* Volitelně si přečtěte téma [požadavky na nasazení VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* Volitelně si můžete prohlédnout související materiály VMware na HCX, jako je například [série blogů](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) VMware VSPHERE v HCX. 
* Seřazení služby AVS HCX Enterprise Activation prostřednictvím kanálů podpory služby AVS

Přizpůsobení velikosti úloh na výpočetní prostředky a prostředky úložiště je zásadní krok plánování při přípravě na použití řešení HCX privátního cloudu. Vyřešte krok změny velikosti jako součást prvotního plánování prostředí privátního cloudu.   

## <a name="software-version-requirements"></a>Požadavky na verzi softwaru
Na součástech infrastruktury musí běžet požadovaná minimální verze. 
                                                         
| Typ součásti    | Požadavky na zdrojové prostředí    | Požadavky na cílové prostředí   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Pokud používáte 5,5 U1 nebo starší, použijte samostatné uživatelské rozhraní HCX pro operace HCX.  | 6,0 U2 a vyšší   |
| ESXi   | 5.0    | ESXi 6,0 a vyšší   |
| NSX    | Pro HCX síťové rozšíření logických přepínačů ve zdroji: NSXv 6.2 + nebo NSX-T 2.4 +   | NSXv 6.2 + nebo NSX-T 2.4 +<br/><br/>Pro směrování blízkosti HCX: NSXv 6.4 + (směrování blízkosti se nepodporuje s NSX-T). |
| vCloud ředitel   | Nepožadováno – bez interoperability s vCloud ředitelem ve zdrojové lokalitě | Při integraci cílového prostředí s vCloud Directorem je minimum 9.1.0.2.  |

## <a name="prerequisites"></a>Požadavky

* Globální dosah by měl být nakonfigurovaný mezi místními a SDDC ER okruhy.

* Všechny požadované porty by se měly otevírat mezi místními a SDDC (v [dokumentaci k VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Jedna IP adresa pro HCX Manager v místním prostředí a minimálně dvě IP adresy pro zařízení Interconnect (IX) a rozšíření sítě (NE).

* Místní HCX IX a NE zařízení by měly být schopné dosáhnout infrastruktury vCenter a ESXi.

* Aby bylo možné nasadit zařízení WAN Interconnect kromě bloku síťových adres CIDR/22 používaného pro nasazení SDDC v Azure Portal, HCX vyžaduje blok/29. Nezapomeňte tento požadavek zvážit do plánování vaší sítě.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Nasazení HCX vajíček v místním prostředí VMware

1. Přihlaste se ke službě AVS SDDC vCenter a vyberte **HCX**.

    ![Vyberte HCX v nástroji pro systém AVS vCenter.](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. V části **Správa**vyberte možnost **aktualizace systému** a pak vyberte **odkaz pro stažení požadavku** . Stáhněte si soubor VMware HCX vajíček.

    ![Získat aktualizace systému](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Pak přejdete na místní Server vCenter a vyberte šablonu OVF, která se nasadí do místního serveru vCenter.  

    ![Vybrat šablonu OVF](./media/hybrid-cloud-extension-installation/select-template.png)

1. Vyberte název a umístění a pak vyberte prostředek/cluster, kde musí být nasazený HCX. Pak zkontrolujte podrobnosti a požadované prostředky.  

    ![Zkontrolovat podrobnosti šablony](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Přečtěte si licenční podmínky a pokud souhlasíte, vyberte požadované úložiště a síť. Pak vyberte **Další**.

1. Do **vlastní šablony**zadejte všechny požadované informace. 

    ![Přizpůsobení šablony](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Vyberte **Další**, ověřte konfiguraci a vyberte **Dokončit** pro nasazení HCX vajíček.

## <a name="activate-hcx"></a>Aktivovat HCX

Po instalaci proveďte následující kroky.

1. Přihlaste se k místnímu HCX Manageru `https://HCXManagerIP:9443` a přihlaste se pomocí svého uživatelského jména a hesla. 

   > [!IMPORTANT]
   > Nezapomeňte zahrnout `9443` číslo portu s IP adresou HCX Manageru.

1. V nástroji **licencování**zadejte svůj **HCX rozšířený klíč**.  

    ![Zadejte HCX klíč.](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX Manager musí mít nakonfigurován otevřený přístup k Internetu nebo proxy.

1. V případě potřeby upravte informace vCenter v **vCenter**.

    ![Konfigurace VCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. V části **umístění Datacenter**v případě potřeby upravte umístění datového centra.

    ![Umístění databáze](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>Konfigurace HCX 

1. Přihlaste se k místnímu serveru vCenter a v části **Domů**vyberte **HCX**.

    ![HCX v VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. V části **infrastruktura**vyberte **párování lokalit**  >  **Přidat párování webu**.

    ![Přidat párování webů](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Zadejte adresu URL nebo IP adresu vzdáleného HCXu, funkce AVS cloudadmin uživatelské jméno a heslo a pak vyberte **připojit**.

   V systému se zobrazuje připojená lokalita.
   
    ![Připojení k webu](./media/hybrid-cloud-extension-installation/site-connection.png)

1. V části **infrastruktura**vyberte **propojit**  >  **službu Multi-Site**  >  **Network profily sítě**  >  **vytvořit profil sítě**.

    ![Vytvořit profil sítě](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. Pro nový profil sítě zadejte HCX IX a NE rozsahy IP adres (minimálně dvě IP adresy se vyžadují pro zařízení IX a NE).
    
   ![Zadejte rozsahy IP adres](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > Zařízení rozšíření sítě (HCX-NE) má relaci 1:1 s distribuovaným virtuálním přepínačem (DVS).  

1. Teď vyberte **COMPUTE profil**  >  **vytvořit výpočetní profil**.

1. Zadejte název výpočetního profilu a vyberte **pokračovat**.  

    ![Vytvořit výpočetní profil](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Vyberte služby, které chcete povolit, například migraci, síťové rozšíření nebo zotavení po havárii, a pak vyberte **pokračovat**.

    ![Vybrat služby](./media/hybrid-cloud-extension-installation/select-services.png)

1. V části **vybrat prostředky služby**vyberte jeden nebo více prostředků služby, pro které by měly být povolené vybrané služby HCX. Vyberte **Pokračovat**.
    
   ![Výběr prostředků služby](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > Vyberte konkrétní clustery, ve kterých jsou zdrojové virtuální počítače cílené na migraci pomocí HCX.

1. Vyberte **úložiště dat** a vyberte **pokračovat**. 
      
    Vyberte jednotlivé výpočetní prostředky a prostředky úložiště pro nasazení zařízení HCX Interconnect. Pokud je vybráno více prostředků, HCX použije první vybraný prostředek, dokud nebude jeho kapacita vyčerpána.  
    
    ![Vybrat prostředky nasazení](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. Vyberte profil sítě pro správu vytvořený v seznamu **síťové profily** a vyberte **pokračovat**.  
      
    Vyberte profil sítě, pomocí kterého je možné dosáhnout rozhraní pro správu serveru vCenter a hostitele ESXi. Pokud jste tento profil sítě ještě nedefinovali, můžete ho vytvořit tady.  
    
    ![Vybrat síťový profil pro správu](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. Vyberte **odchozí připojení k síti** a vyberte **pokračovat**.
      
    Vyberte jeden nebo více profilů sítě, aby byla splněna jedna z následujících podmínek:  
    * Zařízení Interconnect na vzdálené lokalitě se dají kontaktovat přes tuto síť.  
    * Zařízení na dálku se můžou připojit k zařízením s místním propojením přes tuto síť.  
    
    Pokud máte sítě typu Point-to-Point, jako je přímá připojení, které nejsou sdíleny napříč více lokalitami, můžete tento krok přeskočit, protože jsou výpočetní profily sdíleny s více lokalitami. V takových případech se profily sítě pro odesílání můžou přepsat a zadat během vytváření sítě služby InterConnect.  
    
    ![Vybrat profil sítě pro odesílání](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. Vyberte **vMotion síťový profil** a vyberte **pokračovat**.
      
   Vyberte profil sítě, přes který je možné dosáhnout rozhraní vMotion hostitelů ESXi. Pokud jste tento profil sítě ještě nedefinovali, můžete ho vytvořit tady. Pokud nemáte vMotion síť, vyberte možnost **profil sítě pro správu**.  
    
   ![Vybrat vMotion síťový profil](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. V části **Vybrat profil sítě pro replikaci vSphere**vyberte profil sítě vSphere replikačního rozhraní hostitelů ESXi a pak vyberte **pokračovat**.
      
   Ve většině případů je tento profil stejný jako síťový profil pro správu.  
    
   ![Vybrat profil sítě pro replikaci vSphere](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. V části **Výběr distribuovaných přepínačů pro síťová rozšíření**vyberte Služba DVS, na které máte sítě, které budou integrované a připojené k virtuálním počítačům.  Vyberte **Pokračovat**.  
      
    ![Výběr distribuovaných virtuálních přepínačů](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Zkontrolujte pravidla připojení a vyberte **pokračovat**.  

    ![Vytvořit výpočetní profil](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

1.  Vyberte **Dokončit** a vytvořte tak výpočetní profil.

## <a name="configure-network-uplink"></a>Konfigurace odchozího připojení k síti

Teď nakonfigurujte změnu profilu sítě v síťovém připojení AVS SDDC pro odchozí připojení.

1. Přihlaste se k SDDC NSX-T pro vytvoření nového logického přepínače nebo použijte existující logický přepínač, který se dá použít pro odchozí připojení k síti mezi místními a funkci AVS SDDC.

1. Vytvořte profil sítě pro odchozí připojení HCX v prostředí AVS SDDC, které se dá použít pro místní připojení k funkci AVS SDDC Communications.  
    
   ![Vytvořit profil sítě pro odchozí připojení](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. Zadejte název profilu sítě a minimálně 4-5 bezplatných IP adres na základě potřebného rozšíření sítě L2.  
    
   ![Konfigurace profilu sítě pro odchozí připojení](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. Výběrem **vytvořit** DOKONČÍTE konfiguraci SDDC pro funkci AVS.

## <a name="configure-service-mesh"></a>Konfigurace sítě

Teď nakonfigurujte síť mezi místními a SDDC službou AVS.

1. Přihlaste se ke službě AVS SDDC vCenter a vyberte **HCX**.

2. V části **infrastruktura**vyberte **Interconnect**  >  **Service sítě**  >  **vytvořit síť** a nakonfigurujte profily sítě a výpočetní profily vytvořené v předchozích krocích.    
      
    ![Konfigurace sítě](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

3. Vyberte spárované weby a povolte hybridní možnosti a vyberte **pokračovat**.   
    
    ![Vybrat spárované weby](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

4. Vyberte zdrojové a vzdálené výpočetní profily pro povolení služeb hybridního prostředí a vyberte **pokračovat**.
      
    Výběry definují prostředky, ve kterých virtuální počítače můžou využívat služby HCX.  
      
    ![Povolit služby hybridního řešení](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

5. Vyberte služby, které chcete povolit, a vyberte **pokračovat**.  
      
    ![Výběr služeb HCX Services](./media/hybrid-cloud-extension-installation/hcx-services.png)

6. V **rozšířené konfiguraci – přepsat síťové profily pro odesílání** vyberte **pokračovat**.  
      
    Síťové profily odchozího připojení se používají pro připojení k síti, přes kterou je možné oslovit zařízení s propojením vzdálené lokality.  
      
    ![Přepsat profily pro odesílání](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

7. Vyberte možnost **Konfigurovat zařízení rozšíření sítě škálovat**. 
      
    ![Horizontální navýšení kapacity rozšíření sítě](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

8. Zadejte počet zařízení odpovídající počtu přepínačů Služba DVS.  
      
    ![Konfigurace počtu zařízení](./media/hybrid-cloud-extension-installation/appliance-scale.png)

9. Chcete-li přeskočit, vyberte **pokračovat** .  
      
    ![Konfigurace techniků provozu](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

10. Přečtěte si náhled topologie a vyberte **pokračovat**. 

11. Zadejte uživatelsky přívětivý název pro tuto síť a vyberte **Dokončit** .  
      
    ![Dokončete síť služby.](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    Síť je nasazená a nakonfigurovaná.  
      
    ![Nasazená síť služby](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Kontrolovat stav zařízení
Pokud chcete zjistit stav zařízení, vyberte **propojit**  >  **zařízení**. 
      
![Stav zařízení](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Další kroky

Když je **stav tunelového propojení** zařízení v režimu **až** zelený, jste připraveni k migraci a ochraně virtuálních počítačů AVS pomocí HCX. Další informace najdete v [dokumentaci k VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) a [migrace Virtual Machines s využitím VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) v technické dokumentaci k VMware.
