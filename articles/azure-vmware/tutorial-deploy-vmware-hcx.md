---
title: Kurz – nasazení a konfigurace nástroje VMware HCX
description: Naučte se, jak nasadit a nakonfigurovat řešení VMware HCX pro privátní cloud řešení Azure VMware.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: a52b2d06e65de0d056653f12065e36d83d6d8e0c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167759"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Nasazení a konfigurace VMware HCX

V tomto článku Vás provedeme postupy nasazení a konfigurace konektoru VMware HCX On-Premises Connector pro váš privátní cloud řešení Azure VMware. Pomocí VMware HCX můžete migrovat úlohy VMware do řešení Azure VMware a dalších připojených lokalit prostřednictvím různých typů migrace.  Vzhledem k tomu, že řešení Azure VMware nasazuje a konfiguruje správce cloudu, musíte si stáhnout, aktivovat a nakonfigurovat konektor v místním datacentru VMware.  

HCX Advanced Connector pro VMware, který je předem nasazený v řešení Azure VMware, podporuje až tři připojení lokality (místní do cloudu nebo Cloud do cloudu). Pokud potřebujete více než tři připojení lokality, odešlete [žádost o podporu](https://rc.portal.azure.com/#create/Microsoft.Support) , která povolí doplněk [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) (aktuálně ve *verzi Preview*).  

>[!NOTE]
>VMware HCX Enterprise Edition (EE) je k dispozici v rámci řešení Azure VMware jako funkce nebo služby ve *verzi Preview* . Řešení VMware HCX EE pro Azure VMware je ve *verzi Preview*, ale bezplatná funkce nebo služba a podléhá podmínkám a ujednáním služby ve verzi Preview. Jakmile se služba VMware HCX EE dostane do GA, dostanete 30denní oznámení o tom, že účtování přepíná. Můžete také vypnout nebo odhlásit službu.

Než [začnete](#before-you-begin), důkladně si prostudujte, jaké jsou [požadavky na verzi softwaru](#software-version-requirements)a [požadavky.](#prerequisites) 

Pak vás provedeme všemi potřebnými postupy:

> [!div class="checklist"]
> * Nasazení místní sady VMware HCX vajíček (HCX Connector)
> * Aktivace konektoru VMware HCX
> * Párování místního konektoru HCX s vaším cloudovým správcem řešení Azure VMware HCX
> * Konfigurace propojení (profil sítě, výpočetní profil a síť služby)
> * Dokončete nastavení tak, že zkontrolujete stav zařízení a ověříte, že je migrace možná.

Po dokončení můžete postupovat podle doporučených dalších kroků na konci tohoto článku.  

## <a name="before-you-begin"></a>Než začnete
   
* Přečtěte si základní [řadu kurzů](tutorial-network-checklist.md)řešení Azure VMware Software-Defined DATACENTER (SDDC).
* Projděte si [dokumentaci k VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html), včetně uživatelské příručky HCX.
* Projděte si dokumentaci [k VMware s migrací Virtual Machines pomocí VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Volitelně si přečtěte téma [požadavky na nasazení VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Volitelně si můžete prohlédnout související materiály VMware na HCX, jako je například [série blogů](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) VMware VSPHERE v HCX. 
* Volitelně můžete požádat o podnikovou aktivaci řešení Azure VMware HCX prostřednictvím kanálů podpory řešení Azure VMware.
* Volitelně [Zkontrolujte síťové porty vyžadované pro HCX](https://ports.vmware.com/home/VMware-HCX).
* I když je správce cloudového řešení Azure VMware HCX předkonfigurovaný z/22 poskytnutý pro privátní cloud řešení Azure VMware, služba HCX On-Premises Connector vyžaduje, aby byly rozsahy sítě přidělené zákazníkem ze své místní sítě. Tyto sítě a rozsahy jsou popsány dále v dokumentu.

Změna velikosti úloh na výpočetní prostředky a prostředky úložiště je základní krok plánování. Vyřešte krok změny velikosti jako součást prvotního plánování prostředí privátního cloudu. 

Velikost úloh můžete také provádět provedením [vyhodnocení řešení Azure VMware](../migrate/how-to-create-azure-vmware-solution-assessment.md) na portálu Azure Migrate.

## <a name="software-version-requirements"></a>Požadavky na verzi softwaru

Na součástech infrastruktury musí běžet požadovaná minimální verze. 
                                                         
| Typ součásti    | Požadavky na zdrojové prostředí    | Požadavky na cílové prostředí   |
| --- | --- | --- |
| vCenter Server   | 5,1<br/><br/>Pokud používáte 5,5 U1 nebo starší, použijte samostatné uživatelské rozhraní HCX pro operace HCX.  | 6,0 U2 a vyšší   |
| ESXi   | 5,0    | ESXi 6,0 a vyšší   |
| NSX    | Pro HCX síťové rozšíření logických přepínačů ve zdroji: NSXv 6.2 + nebo NSX-T 2.4 +   | NSXv 6.2 + nebo NSX-T 2.4 +<br/><br/>Pro směrování blízkosti HCX: NSXv 6.4 + (směrování blízkosti se u NSX-T nepodporuje). |
| vCloud ředitel   | Nepožadováno – bez interoperability s vCloud ředitelem ve zdrojové lokalitě | Při integraci cílového prostředí s vCloud Directorem je minimum 9.1.0.2.  |

## <a name="prerequisites"></a>Předpoklady

### <a name="network-and-ports"></a>Síť a porty

* [ExpressRoute Global REACH](tutorial-expressroute-global-reach-private-cloud.md) nakonfigurovaný mezi místními a Azure VMware SDDC ExpressRoute okruhy.

* Všechny požadované porty by se měly otevřít pro komunikaci mezi místními komponentami a místními prostředími do Azure VMware Solution SDDC (viz [dokumentace k VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).


### <a name="ip-addresses"></a>IP adresy

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Nasazení vajíček konektoru VMware HCX v místním prostředí

>[!NOTE]
>Před nasazením virtuálního zařízení do místního serveru vCenter budete muset stáhnout VAJÍČKa konektoru VMware HCX. 

1. Otevřete okno prohlížeče, přihlaste se ke Správci Azure VMware HCX Manager na `https://x.x.x.9` portu 443 s přihlašovacími údaji uživatele **cloudadmin** a pak pokračujte na **podporu**.

   >[!TIP]
   >Poznamenejte si IP adresu HCX Cloud Manageru v řešení Azure VMware. Pokud chcete zjistit IP adresu HCX Cloud Manageru, v okně řešení Azure VMware klikněte na **Spravovat**  >  **připojení** a pak vyberte kartu **HCX** . 
   >
   >Heslo vCenter bylo definováno při nastavování privátního cloudu.

1. Vyberte odkaz ke **stažení** pro stažení souboru vajíček konektoru VMware HCX.

1. Z místního serveru vCenter vyberte šablonu OVF, což je soubor vajíček, který jste stáhli, a nasaďte konektor HCX na místní Server vCenter.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Vyberte název a umístění, prostředek/cluster, ve kterém nasadíte konektor HCX, a pak zkontrolujte podrobnosti a požadované prostředky.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Přečtěte si licenční podmínky a pokud souhlasíte, vyberte požadované úložiště a síť a pak vyberte **Další**.

1. Do **vlastní šablony**zadejte všechny požadované informace. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Vyberte **Další**, ověřte konfiguraci a pak vyberte **Dokončit** a nasaďte HCX konektorů.
     
   >[!NOTE]
   >Obecně platí, že konektor VMware HCX, který nasazujete, je nasazený do sítě pro správu clusteru.  
   
   > [!IMPORTANT]
   > Po dokončení nasazení budete možná muset virtuální zařízení zapnout ručně.
   > Počkejte 10-15 minut po zapnutí zařízení HCX a přejděte k dalšímu kroku.

Úplný přehled tohoto kroku najdete v článku [Řešení Azure VMware – video o nasazení VMware HCX pro zařízení](https://www.youtube.com/embed/BwSnQeefnso) . 


## <a name="activate-vmware-hcx"></a>Aktivace VMware HCX

Po nasazení vajíček konektoru VMware HCX do místního prostředí a spuštění zařízení jste připraveni ho aktivovat. Nejdřív je potřeba získat licenční klíč z portálu řešení Azure VMware v Azure.

1. Na portálu řešení Azure VMware klikněte na **Spravovat**  >  **připojení**, vyberte kartu **HCX** a pak vyberte **Přidat**.

1. Přihlaste se k místnímu správci VMware HCX `https://HCXManagerIP:9443` a přihlaste se k přihlašovacím údajům uživatele **správce** . 

   > [!IMPORTANT]
   > Nezapomeňte zahrnout `9443` číslo portu s IP adresou VMware HCX Manageru.

1. V nástroji **licencování**zadejte svůj **HCX rozšířený klíč**.  
   
    > [!NOTE]
    > Nástroj VMware HCX Manager musí mít otevřený přístup k Internetu, nebo je nakonfigurován proxy server.

1. V **umístění datacentra**zadejte nejbližší místo pro instalaci programu VMware HCX Manager do místního prostředí.

1. Změňte **název systému** nebo přijměte výchozí nastavení.
   
1. Můžete buď dokončit později, nebo pokračovat, vybrat možnost **Ano, pokračovat** v pokračování.
    
1. V části **připojit Server vCenter**zadejte plně kvalifikovaný název domény nebo IP adresu vašeho serveru vCenter a příslušné přihlašovací údaje a potom vyberte **pokračovat**.
   
1. V části **Konfigurace jednotného přihlašování/PSC**zadejte plně kvalifikovaný název domény nebo IP adresu vašeho PSC a pak vyberte **pokračovat**.
   
   >[!NOTE]
   >Obvykle je stejný jako plně kvalifikovaný název domény (FQDN) nebo IP adresa vCenter.

1. Ověřte, zda jsou všechny vstupy správné, a vyberte **restartovat**.
    
   > [!NOTE]
   > Po restartu se budete muset setkat po restartování, než se zobrazí výzva k dalšímu kroku.
   >
   >Po restartování služby je důležité, abyste na zobrazené obrazovce zobrazili stránku vCenter, která se zobrazuje jako zelená. Rozhraní vCenter i jednotné přihlašování mají odpovídající parametry konfigurace, které by měly být stejné jako na předchozí obrazovce.

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Ucelený přehled tohoto kroku najdete v článku o [Řešení Azure VMware – video o aktivaci VMware HCX](https://www.youtube.com/embed/BkAV_TNYxdE) .


## <a name="configure-vmware-hcx-connector"></a>Konfigurace konektoru VMware HCX

Nyní jste připraveni přidat párování webu, vytvořit síťový a výpočetní profil a povolit služby, jako je například migrace, síťové rozšíření nebo zotavení po havárii. 

### <a name="add-a-site-pairing"></a>Přidání párování webů

Můžete se připojit (spárovat) správce cloudu VMware HCX v řešení Azure VMware pomocí konektoru VMware HCX ve vašem datovém centru. 

1. Přihlaste se k místnímu serveru vCenter a v části **Domů**vyberte **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. V části **infrastruktura**vyberte **párování lokalit**a pak vyberte možnost **připojit k vzdálené lokalitě** (uprostřed obrazovky). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Zadejte **adresu URL nebo IP adresu vzdáleného HCX** , kterou jste si poznamenali dříve, a cloudadmin@vsphere.local uživatelské jméno a **heslo**pro řešení Azure VMware a pak vyberte **připojit**.

   > [!NOTE]
   > **Vzdálená adresa URL HCX** je vaše řešení Azure VMware privátní cloud HCX Cloud Manageru, obvykle adresa ". 9" sítě pro správu.  Pokud je vaše vCenter například 192.168.4.2, vaše HCX adresa URL bude 192.168.4.9.
   >
   > **Heslo** bude stejné jako heslo, které jste použili k přihlášení do vCenter. Toto heslo jste definovali na úvodní obrazovce nasazení.

   Zobrazí se obrazovka s HCX Cloud Managerem v řešení Azure VMware a připojená místní konektor HCX (spárované).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter.":::

Kompletní přehled tohoto kroku najdete v článku [Řešení Azure VMware – video párování webu VMware HCX](https://www.youtube.com/embed/sKizDCRHOko) .



### <a name="create-network-profiles"></a>Vytvoření profilů sítě

VMware HCX nasadí podmnožinu virtuálních zařízení (automatizované), které vyžadují více segmentů IP adres.  Při vytváření profilů sítě můžete definovat segmenty IP adres, které jste identifikovali během [fáze přípravy a plánování před nasazením segmentů sítě VMware HCX](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Vytvoří se čtyři síťové profily:

   - Správa
   - vMotion
   - Replikace
   - Pro odesílání

1. V části **infrastruktura**vyberte **propojit**  >  **službu Multi-Site**  >  **Network profily sítě**  >  **vytvořit profil sítě**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Pro každý profil sítě vyberte síť, skupinu portů, zadejte název, vytvořte fond IP adres pro tento konkrétní segment a pak vyberte **vytvořit**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter.":::

Kompletní přehled tohoto kroku najdete v článku [Řešení Azure VMware – VMware HCX Create Network Profile](https://www.youtube.com/embed/NhyEcLco4JY) video.


### <a name="create-a-compute-profile"></a>Vytvořit výpočetní profil

1. Vyberte **výpočetní profily**  >  **vytvořit výpočetní profil**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Zadejte název profilu a vyberte **pokračovat**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Vyberte služby, které chcete povolit, například migraci, síťové rozšíření nebo zotavení po havárii, a pak vyberte **pokračovat**.
  
   > [!NOTE]
   > Obecně se tady nic nemění.

1. V části **vybrat prostředky služby**vyberte jeden nebo více prostředků služby (clustery) a povolte tak vybrané služby VMware HCX Services.  

1. Po zobrazení clusterů v místním datovém centru vyberte **pokračovat**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. V části **vybrat úložiště dat**vyberte prostředek úložiště úložiště dat pro nasazení zařízení VMware HCX Interconnect a pak vyberte **pokračovat**.

   Pokud je vybráno více prostředků, VMware HCX použije první vybraný prostředek, dokud nebude jeho kapacita vyčerpána.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Vyberte možnost **profil sítě pro správu**, profil sítě pro správu, který jste vytvořili v předchozích krocích, a pak vyberte **pokračovat**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Profil sítě pro správu umožňuje zařízením VMware HCX komunikovat s vCenter a k těmto hostitelům ESXi se dá získat.

1. Vyberte možnost **odchozí připojení profil sítě**, síťový profil pro odesílání, který jste vytvořili v předchozích krocích, a pak vyberte **pokračovat**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Vyberte **profil sítě vMotion**, profil sítě vMotion, který jste vytvořili v předchozích krocích, a pak vyberte **pokračovat**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Zvolte **profil sítě pro replikaci vSphere**, profil sítě pro replikaci, který jste vytvořili v předchozích krocích, a pak zvolte **pokračovat**.

   Ve většině případů je profil sítě replikace stejný jako síťový profil pro správu.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Vyberte **distribuované přepínače pro rozšíření sítě**, distribuované virtuální přepínače obsahující virtuální počítače, které se mají migrovat do řešení Azure VMware na rozšířené síti vrstvy 2, a pak vyberte **pokračovat**.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Zkontrolujte pravidla připojení a vyberte **pokračovat**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Vyberte **Dokončit** a vytvořte tak výpočetní profil.

   Zobrazí se obrazovka podobná té, kterou vidíte níže.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Úplný přehled tohoto kroku najdete v [Řešení Azure VMware – VMware HCX vytvořit video s výpočetním profilem](https://www.youtube.com/embed/qASXi5xrFzM) .




### <a name="create-service-mesh"></a>Vytvoření sítě služby

Teď je čas nakonfigurovat síť mezi místními a SDDC řešení Azure VMware.

1. V části **infrastruktura**vyberte **Interconnect**  >  **Service mřížka**  >  **vytvořit síť služby**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Zkontrolujte weby, které jsou předem vyplněné, a pak vyberte **pokračovat**. 

   >[!NOTE]
   >Pokud je to vaše první konfigurace sítě pro služby, nebudete muset tuto obrazovku upravovat.  

1. Vyberte rozevírací seznam pro zdrojové a vzdálené výpočetní profily a pak vyberte **pokračovat**.  

   Výběry definují prostředky, ve kterých můžou virtuální počítače využívat služby VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Zkontrolujte služby, které budou povolené, a pak vyberte **pokračovat**.  

1. V **rozšířené konfiguraci – přepsat síťové profily pro odesílání** vyberte **pokračovat**.  

   Profily sítě pro odesílání se připojují k síti, přes kterou je možné oslovit zařízení pro vzdálenou lokalitu.  
  
1. V **rozšířené konfiguraci – zařízení rozšíření sítě škálovat**, zkontrolujte a vyberte **pokračovat**. 

1. V **pokročilých technikách konfigurace a provozu**zkontrolujte a proveďte jakékoli změny, které jsou potřeba, a pak vyberte **pokračovat**.

1. Přečtěte si náhled topologie a vyberte **pokračovat**.

1. Zadejte uživatelsky přívětivý název pro tuto síť a vyberte **Dokončit** .  

1. Vyberte **Zobrazit úlohy** a sledujte nasazení. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter.":::

   Po úspěšném dokončení nasazení sítě se služba zobrazí jako zelená.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Ověřte stav sítě, a to tak, že zkontrolujete stav zařízení a vyberete **propojit**  >  **zařízení**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Ucelený přehled tohoto kroku najdete v článku [Řešení Azure VMware – VMware HCX Create Service pro zobrazení sítě](https://www.youtube.com/embed/FyZ0d3P_T24) .



### <a name="optional-create-a-network-extension"></a>Volitelné Vytvoření rozšíření sítě

Pokud chcete rozšíření jakýchkoli sítí z místního prostředí do řešení Azure VMware, postupujte podle těchto kroků a tyto sítě rozšíříte.

1. V části **služby**vyberte **rozšíření sítě**a potom vyberte **vytvořit rozšíření sítě**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Vyberte všechny sítě, které chcete zvětšit do řešení Azure VMware, a pak vyberte **Další**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter.":::

1. Pro každou síť, kterou rozšiřujete, zadejte IP adresu místní brány a pak vyberte **Odeslat**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter.":::

   Dokončení rozšíření sítě trvá několik minut. V takovém případě uvidíte změnu stavu na **rozšíření dokončeno**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Přejít na místní Server vCenter a vybrat šablonu OVF, která se nasadí do místního serveru vCenter." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Úplný přehled tohoto kroku najdete v článku o [Řešení Azure VMware – video o rozšíření VMware HCX Network](https://www.youtube.com/embed/cNlp0f_tTr0) .


## <a name="next-steps"></a>Další kroky

Pokud jste dosáhli tohoto **bodu a stav** tunelového propojení zařízení je zapnutý a zelený, můžete migrovat a chránit virtuální počítače s řešeními Azure VMware pomocí VMware HCX.  Řešení Azure VMware podporuje migrace úloh (s rozšířením nebo s rozšířením sítě).  V prostředí vSphere můžete stále migrovat úlohy v místním vytváření sítí a nasazování virtuálních počítačů do těchto sítí.  Další informace o použití HCX najdete v technické dokumentaci k VMware:

* [Dokumentace k VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Migrace Virtual Machines s využitím VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) 
