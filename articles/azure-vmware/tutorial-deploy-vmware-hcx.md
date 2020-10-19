---
title: Kurz – nasazení a konfigurace nástroje VMware HCX
description: Přečtěte si, jak nasadit a nakonfigurovat řešení VMware HCX pro privátní cloud řešení Azure VMware.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 607ff3cb04002883b49b4c5bc37d312cbb83c8e5
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173628"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Nasazení a konfigurace VMware HCX

V tomto článku Vás provedeme postupy nasazení a konfigurace místního konektoru VMware HCX pro váš privátní cloud řešení Azure VMware. Pomocí VMware HCX můžete migrovat úlohy VMware do řešení Azure VMware a dalších připojených lokalit prostřednictvím různých typů migrace. Protože řešení Azure VMware nasazuje a konfiguruje správce cloudu HCX, musíte si stáhnout, aktivovat a nakonfigurovat konektor HCX v místním datacentru VMware.

VMware HCX Advanced Connector je předem nasazený v řešení Azure VMware. Podporuje až tři připojení lokality (z místního prostředí do cloudu nebo Cloud do cloudu). Pokud potřebujete více než tři připojení k webu, odešlete [žádost o podporu](https://rc.portal.azure.com/#create/Microsoft.Support) , která povolí doplněk [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) . Doplněk je aktuálně ve verzi Preview. 

>[!NOTE]
>VMware HCX Enterprise Edition (EE) je k dispozici v řešení Azure VMware jako služba ve verzi Preview. Je zdarma a podléhá podmínkám a ujednáním pro službu verze Preview. Jakmile je služba VMware HCX EE všeobecně dostupná, dostanete 30denní oznámení o tom, že účtování bude přepínat. Také budete mít možnost vypnout nebo odhlásit službu.

Nejdříve si důkladně přečtěte [výše uvedené](#before-you-begin)požadavky na [verzi softwaru a požadavky](#software-version-requirements)v [Prerequisites](#prerequisites) tomto článku. 

Pak vás provedeme všemi potřebnými postupy:

> [!div class="checklist"]
> * Nasaďte místní HCX VAJÍČKo VMware (HCX Connector).
> * Aktivujte konektor VMware HCX.
> * Párování místního konektoru HCX s vaším cloudovým správcem řešení Azure VMware HCX.
> * Nakonfigurujte Interconnect (profil sítě, výpočetní profil a síť služby).
> * Dokončete nastavení tak, že zkontrolujete stav zařízení a ověříte, že je migrace možná.

Až budete hotovi, můžete postupovat podle doporučených dalších kroků na konci tohoto článku.  

## <a name="before-you-begin"></a>Než začnete
   
* Přečtěte si základní [řadu kurzů](tutorial-network-checklist.md)řešení Azure VMware Software-Defined DATACENTER (SDDC).
* Projděte si [dokumentaci k VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html), včetně uživatelské příručky HCX.
* Projděte si téma [migrace Virtual Machines se VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) na dokumentech VMware.
* Volitelně si přečtěte téma [požadavky na nasazení VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Volitelně si můžete prohlédnout související materiály VMware na HCX, jako je například [série blogu](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)VMware vSphere. 
* Volitelně můžete požádat o podnikovou aktivaci řešení Azure VMware HCX prostřednictvím kanálů podpory řešení Azure VMware.
* Volitelně [Zkontrolujte síťové porty vyžadované pro HCX](https://ports.vmware.com/home/VMware-HCX).
* I když je správce cloudového řešení Azure VMware HCX předkonfigurovaný ze sítě/22, která je poskytována pro privátní cloud řešení Azure VMware, místní konektor HCX vyžaduje, abyste přidělili rozsahy sítě z místní sítě. Tyto sítě a rozsahy jsou popsány dále v tomto článku.

Změna velikosti úloh na výpočetní prostředky a prostředky úložiště je základní krok plánování. Vyřešte krok změny velikosti jako součást prvotního plánování pro prostředí privátního cloudu. 

Velikost úloh můžete změnit na základě [odhadu řešení Azure VMware](../migrate/how-to-create-azure-vmware-solution-assessment.md) na portálu Azure Migrate.

## <a name="software-version-requirements"></a>Požadavky na verzi softwaru

Na součástech infrastruktury musí běžet požadovaná minimální verze. 
                                                         
| Typ součásti    | Požadavky na zdrojové prostředí    | Požadavky na cílové prostředí   |
| --- | --- | --- |
| vCenter Server   | 5,1<br/><br/>Pokud používáte 5,5 U1 nebo starší, použijte samostatné uživatelské rozhraní HCX pro operace HCX.  | 6,0; U2 a novější   |
| ESXi   | 5,0    | ESXi 6,0 a novější   |
| NSX    | Pro HCX síťové rozšíření logických přepínačů ve zdroji: NSXv 6.2 + nebo NSX-T 2.4 +.   | NSXv 6.2 + nebo NSX-T 2.4 +<br/><br/>Pro směrování blízkosti HCX: NSXv 6.4 +. (Směrování blízkosti se nepodporuje s NSX-T.) |
| vCloud ředitel   | Nepožadováno. Neexistuje žádná interoperabilita s vCloud Directorem ve zdrojové lokalitě. | Při integraci cílového prostředí s vCloud Directorem je minimum 9.1.0.2.  |

## <a name="prerequisites"></a>Požadavky

### <a name="network-and-ports"></a>Síť a porty

* Nakonfigurujte [Global REACH Azure ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) mezi místními a Azure VMware SDDC ExpressRoute okruhy.

* Všechny požadované porty by se měly otevřít pro komunikaci mezi místními komponentami a Azure VMware Solution SDDC. Další informace najdete v [dokumentaci k VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html).


### <a name="ip-addresses"></a>IP adresy

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Nasazení vajíček konektoru VMware HCX v místním prostředí

>[!NOTE]
>Před nasazením virtuálního zařízení do místního serveru vCenter budete muset stáhnout VAJÍČKa konektoru VMware HCX. 

1. Otevřete okno prohlížeče, přihlaste se ke Správci Azure VMware HCX Manager na `https://x.x.x.9` portu 443 s přihlašovacími údaji uživatele **cloudadmin** a pak pokračujte na **podporu**.

   >[!TIP]
   >Poznamenejte si IP adresu HCX Cloud Manageru v řešení Azure VMware. IP adresu zjistíte tak, že v podokně řešení Azure VMware přejdete na **Spravovat**  >  **připojení** a pak vyberete kartu **HCX** . 
   >
   >Heslo vCenter bylo definováno při nastavování privátního cloudu.

1. Vyberte odkaz ke **stažení** pro stažení souboru vajíček konektoru VMware HCX.

1. Přejít na místní Server vCenter. Vyberte šablonu OVF, což je soubor vajíček, který jste stáhli, a nasaďte konektor HCX na místní Server vCenter.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Vyberte název a umístění a vyberte prostředek/cluster, do kterého nasazujete konektor HCX. Pak zkontrolujte podrobnosti a požadované prostředky.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Přečtěte si licenční smlouvy. Pokud souhlasíte, vyberte požadované úložiště a síť a pak vyberte **Další**.

1. Do **vlastní šablony**zadejte všechny požadované informace. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Vyberte **Další**, ověřte konfiguraci a pak vyberte **Dokončit** pro nasazení HCX konektoru konektoru.
     
   >[!NOTE]
   >Obecně platí, že konektor VMware HCX, který nasazujete, je nasazený do sítě pro správu clusteru.  
   
   > [!IMPORTANT]
   > Možná budete muset virtuální zařízení zapnout ručně.  V takovém případě počkejte 10-15 minut, než budete pokračovat k dalšímu kroku.

Kompletní přehled tohoto postupu najdete v článku [Řešení Azure VMware: video o nasazení zařízení HCX](https://www.youtube.com/embed/BwSnQeefnso) . 


## <a name="activate-vmware-hcx"></a>Aktivace VMware HCX

Po nasazení vajíček konektoru VMware HCX do místního prostředí a spuštění zařízení jste připraveni aktivovat. Nejdřív je potřeba získat licenční klíč z portálu řešení Azure VMware.

1. Na portálu řešení Azure VMware klikněte na **Spravovat**  >  **připojení**, vyberte kartu **HCX** a pak vyberte **Přidat**.

1. Pomocí přihlašovacích údajů **správce** se přihlaste k místnímu správci VMware HCX na adrese `https://HCXManagerIP:9443` . 

   > [!IMPORTANT]
   > Nezapomeňte zahrnout `9443` číslo portu s IP adresou VMware HCX Manageru.

1. V nástroji **licencování**zadejte klíč pro **HCX Advanced Key**.  
   
    > [!NOTE]
    > Nástroj VMware HCX Manager musí mít otevřený přístup k Internetu, nebo je nakonfigurován proxy server.

1. V **umístění datacentra**zadejte nejbližší místo pro instalaci programu VMware HCX Manager do místního prostředí.

1. V části **název systému**upravte název nebo přijměte výchozí nastavení.
   
1. Vyberte **Ano, pokračovat**.
    
1. V části **připojit Server vCenter**zadejte plně kvalifikovaný název domény nebo IP adresu vašeho serveru vCenter a příslušné přihlašovací údaje a potom vyberte **pokračovat**.
   
1. V části **Konfigurace jednotného přihlašování/PSC**zadejte plně kvalifikovaný název domény nebo IP adresu vašeho kontroleru služeb platformy a pak vyberte **pokračovat**.
   
   >[!NOTE]
   >Obvykle je tato položka stejná jako plně kvalifikovaný název domény nebo IP adresa serveru vCenter.

1. Ověřte, zda jsou všechny vstupy správné, a vyberte **restartovat**.
    
   > [!NOTE]
   > Po restartu se budete muset setkat po restartování, než se zobrazí výzva k dalšímu kroku.

Po restartování služby je důležité, abyste na zobrazené obrazovce zobrazili stránku vCenter, která se zobrazuje jako zelená. VCenter i SSO musí mít odpovídající parametry konfigurace, které by měly být stejné jako na předchozí obrazovce.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Ucelený přehled tohoto postupu najdete v [Řešení Azure VMware: Aktivace HCX](https://www.youtube.com/embed/BkAV_TNYxdE) videa.


## <a name="configure-the-vmware-hcx-connector"></a>Konfigurace konektoru VMware HCX

Nyní jste připraveni přidat párování webu, vytvořit síťový a výpočetní profil a povolit služby, jako je například migrace, síťové rozšíření nebo zotavení po havárii. 

### <a name="add-a-site-pairing"></a>Přidání párování webů

Můžete se připojit (spárovat) správce cloudu VMware HCX v řešení Azure VMware pomocí konektoru VMware HCX ve vašem datovém centru. 

1. Přihlaste se k místnímu serveru vCenter a v části **Domů**vyberte **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. V části **infrastruktura**vyberte **párování lokalit**a potom vyberte možnost **připojit ke vzdálené lokalitě** (uprostřed obrazovky). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Zadejte adresu URL nebo IP adresu vzdáleného HCXu, kterou jste si poznamenali dříve, uživatelské jméno řešení Azure VMware cloudadmin@vsphere.local a heslo. Potom vyberte **Připojit**.

   > [!NOTE]
   > Vzdálená adresa URL HCX je vaše IP adresa HCX cloudového správce řešení Azure VMware, což je adresa ". 9" sítě pro správu. Pokud je vaše vCenter například 192.168.4.2, vaše HCX adresa URL bude 192.168.4.9.
   >
   > Heslo je stejné heslo, které jste použili k přihlášení do vCenter. Toto heslo jste definovali na úvodní obrazovce nasazení.

   Zobrazí se obrazovka s informacemi o tom, že váš HCX cloudový správce v řešení Azure VMware a váš místní konektor HCX jsou připojené (spárované).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF":::

Ucelený přehled tohoto postupu najdete v článku [Řešení Azure VMware: video párování webu HCX](https://www.youtube.com/embed/sKizDCRHOko) .



### <a name="create-network-profiles"></a>Vytvoření profilů sítě

VMware HCX nasadí podmnožinu virtuálních zařízení (automatizované), které vyžadují více segmentů IP adres. Při vytváření profilů sítě můžete definovat segmenty IP adres, které jste identifikovali během [fáze přípravy a plánování před nasazením segmentů sítě VMware HCX](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Vytvoříte čtyři síťové profily:

   - Správa
   - vMotion
   - Replikace
   - Pro odesílání

1. V části **infrastruktura**vyberte **propojit**  >  **službu Multi-Site**  >  **Network profily sítě**  >  **vytvořit profil sítě**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Pro každý profil sítě vyberte síť a skupinu portů, zadejte název a vytvořte fond IP adres pro tento segment. Potom vyberte **Vytvořit**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF":::

Ucelený přehled tohoto postupu najdete v článku [Řešení Azure VMware: video o profilu HCX Network Profile](https://www.youtube.com/embed/NhyEcLco4JY) .


### <a name="create-a-compute-profile"></a>Vytvořit výpočetní profil

1. Vyberte **výpočetní profily**  >  **vytvořit výpočetní profil**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Zadejte název profilu a vyberte **pokračovat**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Vyberte služby, které chcete povolit, například migraci, síťové rozšíření nebo zotavení po havárii, a pak vyberte **pokračovat**.
  
   > [!NOTE]
   > Obecně se tady nic nemění.

1. V části **vybrat prostředky služby**vyberte jeden nebo více prostředků služby (clustery) a povolte tak vybrané služby VMware HCX Services.  

1. Po zobrazení clusterů v místním datovém centru vyberte **pokračovat**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. V části **vybrat úložiště dat**vyberte prostředek úložiště úložiště dat pro nasazení zařízení s modulem VMware HCX Interconnect. Potom vyberte **Pokračovat**.

   Pokud je vybráno více prostředků, VMware HCX použije první vybraný prostředek, dokud nebude jeho kapacita vyčerpána.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. V části **Vybrat síťový profil pro správu**vyberte profil sítě pro správu, který jste vytvořili v předchozích krocích. Potom vyberte **Pokračovat**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Profil sítě pro správu umožňuje, aby zařízení VMware HCX komunikovala s vCenter. Prostřednictvím tohoto profilu lze dosáhnout hostitelů ESXi.

1. V části **Vybrat profil sítě pro odesílání**vyberte profil sítě pro odesílání, který jste vytvořili v předchozích krocích. Potom vyberte **Pokračovat**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. V části **Vybrat profil sítě vMotion**vyberte profil sítě vMotion, který jste vytvořili v předchozích krocích. Potom vyberte **Pokračovat**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. V části **Vybrat profil sítě pro replikaci vSphere**vyberte profil sítě replikace, který jste vytvořili v předchozích krocích. Potom vyberte **Pokračovat**.

   Ve většině případů je profil sítě replikace stejný jako síťový profil pro správu.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. V části **Vybrat distribuované přepínače pro síťová rozšíření**vyberte distribuované virtuální přepínače, které obsahují virtuální počítače, které se mají migrovat do řešení Azure VMware na rozšířené síti vrstvy 2. Potom vyberte **Pokračovat**.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Zkontrolujte pravidla připojení a vyberte **pokračovat**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Vyberte **Dokončit** a vytvořte tak výpočetní profil.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Úplný přehled tohoto postupu najdete v článku [Řešení Azure VMware: video o COMPUTE Profile](https://www.youtube.com/embed/qASXi5xrFzM) .

### <a name="create-a-service-mesh"></a>Vytvoření sítě služby

Teď je čas nakonfigurovat síť mezi místními a SDDC řešení Azure VMware.

1. V části **infrastruktura**vyberte **Interconnect**  >  **Service mřížka**  >  **vytvořit síť služby**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Zkontrolujte weby, které jsou předem vyplněné, a pak vyberte **pokračovat**. 

   >[!NOTE]
   >Pokud je to vaše první konfigurace sítě pro služby, nebudete muset tuto obrazovku upravovat.  

1. V rozevíracích seznamech vyberte zdrojové a vzdálené výpočetní profily a pak vyberte **pokračovat**.  

   Výběry definují prostředky, ve kterých můžou virtuální počítače využívat služby VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Zkontrolujte služby, které budou povoleny, a pak vyberte **pokračovat**.  

1. V **Možnosti Pokročilá konfigurace – přepsat síťové profily pro odesílání**vyberte **pokračovat**.  

   Profily sítě pro odesílání se připojují k síti, přes kterou jsou dosažitelná zařízení s propojením vzdálené lokality.  
  
1. V **rozšířené konfiguraci – zařízení rozšíření sítě škálovat**, zkontrolujte a vyberte **pokračovat**. 

1. V **pokročilých technikách konfigurace a provozu**zkontrolujte a proveďte jakékoli změny, které máte pocit, a pak vyberte **pokračovat**.

1. Přečtěte si náhled topologie a vyberte **pokračovat**.

1. Zadejte uživatelsky přívětivý název pro tuto síť a vyberte **Dokončit** .  

1. Vyberte **Zobrazit úlohy** a sledujte nasazení. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF":::

   Po úspěšném dokončení nasazení sítě se služby zobrazí jako zelené.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Zkontrolujte stav zařízení pomocí kontroly stavu sítě. 
1. Vyberte **Interconnect**  >  **zařízení**Interconnect.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Kompletní přehled tohoto postupu najdete v článku [Řešení Azure VMware:](https://www.youtube.com/embed/FyZ0d3P_T24) video o síti.



### <a name="optional-create-a-network-extension"></a>Volitelné Vytvoření rozšíření sítě

Pokud chcete z místního prostředí do řešení Azure VMware Rozmístit všechny sítě, postupujte takto:

1. V části **služby**vyberte **rozšíření sítě**a potom vyberte **vytvořit rozšíření sítě**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Vyberte všechny sítě, které chcete pro řešení Azure VMware využívat, a pak vyberte **Další**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF":::

1. Pro každou síť, kterou rozšiřujete, zadejte IP adresu místní brány a pak vyberte **Odeslat**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF":::

   Dokončení rozšíření sítě trvá několik minut. V takovém případě uvidíte změnu stavu na **rozšíření dokončeno**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Kompletní přehled tohoto kroku najdete v článku [Řešení Azure VMware: video o rozšíření sítě](https://www.youtube.com/embed/cNlp0f_tTr0) .


## <a name="next-steps"></a>Další kroky

Pokud jste dosáhli tohoto bodu a stav tunelového propojení zařízení je zapnutý a zelený, můžete **migrovat a chránit** virtuální počítače s řešeními VMware Azure pomocí VMware HCX. Řešení Azure VMware podporuje migrace úloh (s rozšířením sítě nebo bez něj). I nadále můžete migrovat úlohy ve svém prostředí vSphere spolu s místními vytvářením sítí a nasazením virtuálních počítačů do těchto sítí.  

Další informace o použití HCX najdete v technické dokumentaci k VMware:

* [Dokumentace k VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Migrace Virtual Machines s využitím VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
