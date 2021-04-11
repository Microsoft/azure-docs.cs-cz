---
title: Kurz – nasazení a konfigurace nástroje VMware HCX
description: Přečtěte si, jak nasadit a nakonfigurovat řešení VMware HCX pro privátní cloud řešení Azure VMware.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9ee451ce4be59c51299d66e4042ed344026100cf
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010996"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Nasazení a konfigurace VMware HCX

V tomto článku se dozvíte, jak nasadit a nakonfigurovat místní konektor VMware HCX pro privátní cloud řešení Azure VMware. Pomocí VMware HCX můžete migrovat úlohy VMware do řešení Azure VMware a dalších připojených lokalit prostřednictvím různých typů migrace. Protože řešení Azure VMware nasazuje a konfiguruje správce cloudu HCX, musíte si stáhnout, aktivovat a nakonfigurovat konektor HCX v místním datacentru VMware.

VMware HCX Advanced Connector je předem nasazený v řešení Azure VMware. Podporuje až tři připojení lokality (z místního prostředí do cloudu nebo Cloud do cloudu). Pokud potřebujete více než tři připojení k webu, odešlete [žádost o podporu](https://portal.azure.com/#create/Microsoft.Support) , která povolí doplněk [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) .  

>[!TIP]
>I když nástroj maximální konfigurace VMware popisuje páry webů, které jsou maximálně 25 mezi on-premises Connectorem a správcem cloudu, tato licence omezuje tyto možnosti na tři pro HCX Advanced a 10 pro HCX Enterprise Edition.

>[!NOTE]
>VMware HCX Enterprise je k dispozici s řešením Azure VMware jako službou ve verzi Preview. Je zdarma a podléhá podmínkám a ujednáním pro službu verze Preview. Jakmile je podniková služba VMware HCX Enterprise všeobecně dostupná, dostanete 30denní oznámení o tom, že účtování bude přepínat. Také budete mít možnost vypnout nebo odhlásit službu. Přechod na starší verzi HCx Enterprise na HCX Advanced je možný bez opětovného nasazení, ale budete muset protokolovat lístek podpory, aby tato akce probíhat. Při plánování downgradu zajistěte, aby se žádné migrace neplánovaly, ani funkce, jako je RAV, se nepoužívají.

Nejdřív si přečtěte, [než začnete](#before-you-begin), [požadavky na verzi softwaru](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)a [požadavky](#prerequisites). 

Pak vás provedeme všemi potřebnými postupy:

> [!div class="checklist"]
> * Stáhněte si VAJÍČKo konektoru VMware HCX.
> * Nasazení místní sady VMware HCX VAJÍČK (VMware HCX Connector).
> * Aktivujte konektor VMware HCX.
> * Párování místního konektoru VMware HCX s vaším cloudovým správcem řešení Azure VMware HCX.
> * Nakonfigurujte Interconnect (profil sítě, výpočetní profil a síť služby).
> * Dokončete nastavení tak, že zkontrolujete stav zařízení a ověříte, že je migrace možná.

Až budete hotovi, postupujte podle doporučených dalších kroků na konci tohoto článku.  

## <a name="before-you-begin"></a>Než začnete

Při přípravě nasazení doporučujeme Přečtěte si následující dokumentaci k VMware:

* [Uživatelská příručka k VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [Migrace Virtual Machines s využitím VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Požadavky na nasazení VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [Série blogů VMware – migrace do cloudu](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [Síťové porty vyžadované pro VMware HCX](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>Požadavky

Pokud plánujete použít VMware HCX Enterprise, ujistěte se, že jste požádali o aktivaci pomocí kanálů podpory řešení Azure VMware.


### <a name="on-premises-vsphere-environment"></a>Místní prostředí vSphere

Ujistěte se, že vaše místní prostředí vSphere (zdrojové prostředí) splňuje [minimální požadavky](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html). 

### <a name="network-and-ports"></a>Síť a porty

* [Azure ExpressRoute Global REACH](tutorial-expressroute-global-reach-private-cloud.md) je nakonfigurovaný mezi místními a ExpressRoutemi okruhy privátního cloudu řešení Azure VMware.

* [Všechny požadované porty](https://ports.vmware.com/home/VMware-HCX) jsou otevřené pro komunikaci mezi místními komponentami a s privátním řešením Azure VMware.

### <a name="ip-addresses"></a>IP adresy

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>Stáhnout VAJÍČKa konektoru VMware HCX

Než virtuální zařízení nasadíte do místního serveru vCenter, musíte si stáhnout VAJÍČKu konektoru VMware HCX.  

1. V Azure Portal vyberte privátní cloud řešení Azure VMware. 

1. Vyberte **Spravovat**  >  **připojení** a vyberte kartu **HCX** a určete IP adresu správce Azure VMware Solution HCX Manager. 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="Snímek obrazovky s IP adresou HCX VMware" lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. Vyberte **Spravovat**  >  **identitu**. 

   Adresy URL a přihlašovací údaje uživatele pro zobrazení privátního cloudu vCenter a správce NSX-T.

   > [!TIP]
   > Heslo vCenter bylo definováno při nastavování privátního cloudu. Jedná se o stejné heslo, které použijete k přihlášení ke službě Azure VMware Solution HCX Manager. Pro generování nových hesel vCenter a NSX-T můžete vybrat **vygenerovat nové heslo** .

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Zobrazí adresy URL a přihlašovací údaje privátního cloudu vCenter a NSX Manageru." border="true":::


1. Otevřete okno prohlížeče a přihlaste se k Azure VMware Solution HCX Manageru na `https://x.x.x.9` portu 443 s **cloudadmin \@ vSphere.** přihlašovací údaje místního uživatele

1. Vyberte   >  **aktualizace systému** pro správu a pak vyberte **odkaz pro stažení požadavku**.

1. Vyberte možnost podle vašeho výběru a Stáhněte si soubor s VAJÍČKy VMware HCX Connectoru.

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Nasazení vajíček konektoru VMware HCX v místním prostředí

1. V místním serveru vCenter vyberte [šablonu OVF](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) pro nasazení konektoru VMware HCX do místního serveru vCenter. 

   > [!TIP]
   > Vyberete soubor vajíček, který jste si stáhli v předchozí části.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Snímek obrazovky s přechodem na šablonu OVF" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Vyberte název a umístění a vyberte prostředek nebo cluster, do kterého nasazujete konektor VMware HCX. Pak zkontrolujte podrobnosti a požadované prostředky a vyberte **Další**.  

1. Přečtěte si licenční smlouvy. Pokud souhlasíte, vyberte požadované úložiště a síť a pak vyberte **Další**.

1. Vyberte úložiště a vyberte **Další**.

1. Vyberte segment sítě pro správu VMware HCX, který jste předtím definovali v části [požadavky na IP adresy](#ip-addresses) .  Pak vyberte **Další**.

1. V poli **přizpůsobit šablonu** zadejte všechny požadované informace a pak vyberte **Další**. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Snímek obrazovky s poli pro přizpůsobení šablony" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Ověřte konfiguraci a pak kliknutím na **Dokončit** nasaďte vajíček konektoru VMware HCX.
   
   > [!IMPORTANT]
   > Virtuální zařízení budete muset zapnout ručně.  Po zapnutí počkejte, než se dokročí k dalšímu kroku, počkejte 10-15 minut.

Kompletní přehled tohoto postupu najdete v článku [Řešení Azure VMware: video o nasazení zařízení HCX](https://www.youtube.com/embed/UKmSTYrL6AY) . 


## <a name="activate-vmware-hcx"></a>Aktivace VMware HCX

Po nasazení vajíček konektoru VMware HCX do místního prostředí a spuštění zařízení jste připraveni aktivovat. Nejdřív je potřeba získat licenční klíč z portálu řešení Azure VMware.

1. Na portálu řešení Azure VMware klikněte na **Spravovat**  >  **připojení**, vyberte kartu **HCX** a pak vyberte **Přidat**.

1. Pomocí přihlašovacích údajů **správce** se přihlaste k místnímu správci VMware HCX na adrese `https://HCXManagerIP:9443` . 

   > [!TIP]
   > Heslo **správce** jste definovali během nasazování souboru s vajíčky VMware HCX Manageru.

   > [!IMPORTANT]
   > Nezapomeňte zahrnout `9443` číslo portu s IP adresou VMware HCX Manageru.

1. V nástroji **licencování** zadejte klíč pro **HCX Advanced Key** a vyberte **aktivovat**.  
   
    > [!NOTE]
    > Nástroj VMware HCX Manager musí mít nakonfigurován otevřený přístup k Internetu nebo proxy server.

1. V **umístění datacentra** zadejte nejbližší místo pro instalaci programu VMware HCX Manager do místního prostředí. Potom vyberte **Pokračovat**.

1. V části **název systému** upravte název nebo přijměte výchozí nastavení a vyberte **pokračovat**.
   
1. Vyberte **Ano, pokračovat**.

1. V části **připojit Server vCenter** zadejte plně kvalifikovaný název domény nebo IP adresu vašeho serveru vCenter a příslušné přihlašovací údaje a potom vyberte **pokračovat**.
   
   > [!TIP]
   > Server vCenter je místo, kde jste nasadili konektor VMware HCX ve svém datovém centru.

1. V části **Konfigurace jednotného přihlašování/PSC** zadejte plně kvalifikovaný název domény nebo IP adresu vašeho kontroleru služeb platformy a pak vyberte **pokračovat**.
   
   > [!NOTE]
   > Obvykle je stejný jako plně kvalifikovaný název domény nebo IP adresa serveru vCenter.

1. Ověřte správnost zadaných informací a vyberte **restartovat**.
    
   > [!NOTE]
   > Po restartu se budete muset setkat po restartování, než se zobrazí výzva k dalšímu kroku.

Po restartování služeb se na obrazovce, která se zobrazí, zobrazí položka vCenter se zobrazuje zeleně. VCenter i SSO musí mít odpovídající parametry konfigurace, které by měly být stejné jako na předchozí obrazovce.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Snímek obrazovky řídicího panelu se stavem zelených vCenter" lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Ucelený přehled tohoto postupu najdete v [Řešení Azure VMware: Aktivace HCX](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720) videa.

   > [!IMPORTANT]
   > Bez ohledu na to, jestli používáte HCX Advanced nebo HCX Enterprise, budete možná muset nainstalovat opravu z [článku znalostní báze](https://kb.vmware.com/s/article/81558)VMware pro 81558. 

## <a name="configure-the-vmware-hcx-connector"></a>Konfigurace konektoru VMware HCX

Nyní jste připraveni přidat párování webu, vytvořit síťový a výpočetní profil a povolit služby, jako je například migrace, síťové rozšíření nebo zotavení po havárii. 

### <a name="add-a-site-pairing"></a>Přidání párování webů

Správce cloudu VMware HCX můžete v řešení Azure VMware připojit nebo spárovat pomocí konektoru VMware HCX ve vašem datovém centru. 

1. Přihlaste se k místnímu serveru vCenter a v části **Domů** vyberte **HCX**.

1. V části **infrastruktura** vyberte **párování lokalit** a potom vyberte možnost **připojit ke vzdálené lokalitě** (uprostřed obrazovky). 

1. Zadejte adresu URL nebo IP adresu cloudového správce Azure VMware Solution HCX, kterou jste si poznamenali dříve `https://x.x.x.9` , řešení Azure VMware cloudadmin \@ vSphere. Local username a heslo. Potom vyberte **Připojit**.

   > [!NOTE]
   > Chcete-li úspěšně vytvořit pár lokalit:
   > * Váš konektor VMware HCX musí být schopný směrovat se na vaši IP adresu cloudového správce HCX přes port 443.
   >
   > * Použijte stejné heslo, které jste použili k přihlášení do vCenter. Toto heslo jste definovali na úvodní obrazovce nasazení.

   Zobrazí se obrazovka s informacemi o tom, že váš správce cloudu VMware HCX v řešení Azure VMware a váš místní konektor VMware HCX jsou připojené (spárované).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Snímek obrazovky zobrazující párování HCX Manageru v řešení Azure VMware a konektoru VMware HCX":::

Ucelený přehled tohoto postupu najdete v článku [Řešení Azure VMware: video párování webu HCX](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720) .

### <a name="create-network-profiles"></a>Vytvoření profilů sítě

Konektor VMware HCX nasazuje podmnožinu virtuálních zařízení (automatizovaných), které vyžadují více segmentů IP adres. Když vytváříte profily sítě, použijete segmenty IP, které jste identifikovali během [fáze přípravy a plánování předběžného nasazení HCX sítě VMware](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Vytvoříte čtyři síťové profily:

   - Správa
   - vMotion
   - Replikace
   - Pro odesílání

1. V části **infrastruktura** vyberte **propojit**  >  **službu Multi-Site**  >  **Network profily sítě**  >  **vytvořit profil sítě**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Snímek obrazovky s výběrem pro zahájení vytváření profilu sítě" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Pro každý profil sítě vyberte síť a skupinu portů, zadejte název a vytvořte fond IP adres segmentu. Potom vyberte **Vytvořit**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Snímek obrazovky s podrobnostmi o novém profilu sítě.":::

Ucelený přehled tohoto postupu najdete v článku [Řešení Azure VMware: video o profilu HCX Network Profile](https://www.youtube.com/embed/O0rU4jtXUxc) .


### <a name="create-a-compute-profile"></a>Vytvořit výpočetní profil

1. V části **infrastruktura** vyberte   >  **výpočetní profily** propojení  >  **vytvořit výpočetní profil**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Snímek obrazovky, který zobrazuje výběry pro začátek vytváření výpočetního profilu." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Zadejte název profilu a vyberte **pokračovat**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Snímek obrazovky, který zobrazuje položku s názvem výpočetního profilu a tlačítkem pokračovat." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Vyberte služby, které chcete povolit, například migraci, síťové rozšíření nebo zotavení po havárii, a pak vyberte **pokračovat**.
  
   > [!NOTE]
   > Obecně se tady nic nemění.

1. V části **vybrat prostředky služby** vyberte jeden nebo více prostředků služby (clustery) a povolte tak vybrané služby VMware HCX Services.  

1. Po zobrazení clusterů v místním datovém centru vyberte **pokračovat**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Snímek obrazovky se zobrazením vybraných prostředků služby a tlačítka pro pokračování" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. V části **vybrat úložiště dat** vyberte prostředek úložiště úložiště dat pro nasazení zařízení s modulem VMware HCX Interconnect. Potom vyberte **Pokračovat**.

   Pokud je vybráno více prostředků, VMware HCX použije první vybraný prostředek, dokud nebude jeho kapacita vyčerpána.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Snímek obrazovky, který zobrazuje vybraný prostředek úložiště dat a tlačítko pokračovat." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. V části **Vybrat síťový profil pro správu** vyberte profil sítě pro správu, který jste vytvořili v předchozích krocích. Potom vyberte **Pokračovat**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Snímek obrazovky, který zobrazuje výběr profilu sítě pro správu a tlačítka pro pokračování" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. V části **Vybrat profil sítě pro odesílání** vyberte profil sítě pro odesílání, který jste vytvořili v předchozím postupu. Potom vyberte **Pokračovat**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Snímek obrazovky, který zobrazuje výběr profilu sítě pro odesílání a tlačítka pro pokračování" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. V části **Vybrat profil sítě vMotion** vyberte profil sítě vMotion, který jste vytvořili v předchozích krocích. Potom vyberte **Pokračovat**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Snímek obrazovky, který zobrazuje výběr profilu sítě vMotion a tlačítka pro pokračování" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. V části **Vybrat profil sítě pro replikaci vSphere** vyberte profil sítě replikace, který jste vytvořili v předchozích krocích. Potom vyberte **Pokračovat**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Snímek obrazovky, který zobrazuje výběr profilu sítě pro replikaci a tlačítko pro pokračování" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. V části **Vybrat distribuované přepínače pro síťová rozšíření** vyberte přepínače, které obsahují virtuální počítače, které se mají migrovat do řešení Azure VMware na rozšířené síti vrstvy 2. Potom vyberte **Pokračovat**.

   > [!NOTE]
   > Pokud nemigrujete virtuální počítače na rozšířené sítě úrovně 2 (L2), můžete tento krok přeskočit.
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Snímek obrazovky, který zobrazuje výběr distribuovaných virtuálních přepínačů a tlačítka pro pokračování" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Zkontrolujte pravidla připojení a vyberte **pokračovat**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Snímek obrazovky zobrazující pravidla připojení a tlačítko pokračovat" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Vyberte **Dokončit** a vytvořte tak výpočetní profil.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Snímek obrazovky, který zobrazuje informace o výpočetním profilu." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Úplný přehled tohoto postupu najdete v článku [Řešení Azure VMware: video o COMPUTE Profile](https://www.youtube.com/embed/e02hsChI3b8) .

### <a name="create-a-service-mesh"></a>Vytvoření sítě služby

Teď je čas nakonfigurovat síť mezi místním a privátním cloudem řešení VMware Azure.



> [!NOTE]
> Postup úspěšného vytvoření sítě služby pomocí řešení Azure VMware:
>
> * Porty UDP 500/4500 se otevírají mezi místními adresami profilů sítě pro odchozí připojení VMware HCX Connectorem a adresou profilů sítě pro odchozí připojení řešení Azure VMware HCX Cloud.
>
> * Nezapomeňte si prohlédnout [požadované porty VMware HCX](https://ports.vmware.com/home/VMware-HCX).

1. V části **infrastruktura** vyberte **Interconnect**  >  **Service mřížka**  >  **vytvořit síť služby**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Snímek obrazovky s výběrem pro zahájení vytváření sítě" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Zkontrolujte weby, které jsou předem vyplněné, a pak vyberte **pokračovat**. 

   > [!NOTE]
   > Pokud je to vaše první konfigurace sítě pro služby, nebudete muset tuto obrazovku upravovat.  

1. V rozevíracích seznamech vyberte zdrojové a vzdálené výpočetní profily a pak vyberte **pokračovat**.  

   Výběry definují prostředky, ve kterých můžou virtuální počítače využívat služby VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Snímek obrazovky, který zobrazuje výběr zdrojového výpočetního profilu." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Snímek obrazovky, který zobrazuje výběr vzdáleného výpočetního profilu." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Zkontrolujte služby, které budou povoleny, a pak vyberte **pokračovat**.  

1. V **Možnosti Pokročilá konfigurace – přepsat síťové profily pro odesílání** vyberte **pokračovat**.  

   Profily sítě pro odesílání se připojují k síti, přes kterou jsou dosažitelná zařízení s propojením vzdálené lokality.  
  
1. V **rozšířené konfiguraci – zařízení rozšíření sítě škálovat**, zkontrolujte a vyberte **pokračovat**. 

   Můžete mít až osm sítí VLAN na každé zařízení, ale můžete nasadit jiné zařízení a přidat další osm sítí VLAN. Pro další zařízení musíte mít také adresní prostor IP adres a je to jedna IP adresa pro každé zařízení.  Další informace najdete v tématu [omezení konfigurace VMware HCX](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Snímek obrazovky, který ukazuje, kde zvýšit počet sítí VLAN" lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. V **pokročilých technikách konfigurace a provozu** zkontrolujte a proveďte jakékoli změny, které máte pocit, a pak vyberte **pokračovat**.

1. Přečtěte si náhled topologie a vyberte **pokračovat**.

1. Zadejte uživatelsky přívětivý název pro tuto síť a vyberte **Dokončit** .  

1. Vyberte **Zobrazit úlohy** a sledujte nasazení. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Snímek obrazovky, který zobrazuje tlačítko pro zobrazení úkolů.":::

   Po úspěšném dokončení nasazení sítě se služby zobrazí jako zelené.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Snímek obrazovky, který zobrazuje zelené indikátory na službách." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Zkontrolujte stav zařízení pomocí kontroly stavu sítě. 

1. Vyberte   >  **zařízení** Interconnect.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Snímek obrazovky zobrazující výběry pro kontrolu stavu zařízení." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Kompletní přehled tohoto postupu najdete v článku [Řešení Azure VMware:](https://www.youtube.com/embed/COY3oIws108) video o síti.

### <a name="optional-create-a-network-extension"></a>Volitelné Vytvoření rozšíření sítě

Pokud chcete z místního prostředí do řešení Azure VMware Rozmístit všechny sítě, postupujte takto:

1. V části **služby** vyberte **rozšíření sítě**  >  **vytvořit rozšíření sítě**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Snímek obrazovky zobrazující výběry pro začátek vytvoření síťového rozšíření." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Vyberte všechny sítě, které chcete pro řešení Azure VMware využívat, a pak vyberte **Další**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Snímek obrazovky, který zobrazuje výběr sítě.":::

1. Pro každou síť, kterou rozšiřujete, zadejte IP adresu místní brány a pak vyberte **Odeslat**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Snímek obrazovky, který zobrazuje záznam IP adresy brány.":::

   Dokončení rozšíření sítě trvá několik minut. V takovém případě uvidíte změnu stavu na **rozšíření dokončeno**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Snímek obrazovky zobrazující stav rozšíření je dokončený." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Kompletní přehled tohoto postupu najdete v článku [Řešení Azure VMware: video o rozšíření sítě](https://www.youtube.com/embed/gYR0nftKui0) .


## <a name="next-steps"></a>Další kroky

Pokud je stav tunelového propojení HCX a zelený, můžete **migrovat a chránit** virtuální počítače řešení VMware Azure pomocí VMware HCX. Řešení Azure VMware podporuje migrace úloh (s rozšířením sítě nebo bez něj). I nadále můžete migrovat úlohy ve svém prostředí vSphere spolu s místními vytvářením sítí a nasazením virtuálních počítačů do těchto sítí.  

Další informace o použití HCX najdete v technické dokumentaci k VMware:

* [Dokumentace k VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Migrace Virtual Machines s využitím VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [HCX požadované porty](https://ports.vmware.com/home/VMware-HCX)
* [Před schválením licenčního klíče nastavte proxy server HCX.](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-920242B3-71A3-4B24-9ACF-B20345244AB2.html?hWord=N4IghgNiBcIA4CcD2APAngAgBIGEAaIAvkA)
