---
title: Nastavení zotavení po havárii do Azure pro místní virtuální počítače VMware pomocí Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 90389325f49e41b32d89990b4a32dbb8ec90c261
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437041"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Nastavení zotavení po havárii do Azure pro místní virtuální počítače VMware

[Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že zajišťuje provoz a dostupnost obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure, včetně replikace, převzetí služeb při selhání a zotavení.


V tomto kurzu vám ukážeme, jak nastavit a povolit replikaci virtuálního počítače VMware do Azure pomocí Azure Site Recovery. Kurzy vám mají ukázat, jak nasadit Site Recovery se základním nastavením. Používají nejjednodušší cestu a neuvidíte v nich všechny možnosti. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zadat zdroj a cíl replikace.
> * Nastavit zdrojové prostředí replikace, včetně místních komponent Azure Site Recovery, a cílové prostředí replikace.
> * Vytvořit zásadu replikace.
> * Povolit replikaci virtuálního počítače.

## <a name="before-you-start"></a>Než začnete

Než začnete, je vhodné postupovat takto:

- [Podívejte se na architekturu](vmware-azure-architecture.md) tohoto scénáře zotavení po havárii.
- Pokud chcete získat podrobnější informace o nastavení zotavení po havárii pro virtuální počítače VMware, přečtěte si a použijte následující zdroje informací:
    - [Přečtěte si časté dotazy](vmware-azure-common-questions.md) o zotavení po havárii pro VMware.
    - [Přečtěte si další informace](vmware-physical-azure-support-matrix.md) o tom, co se podporuje a vyžaduje pro VMware.
-  Podrobné pokyny ke všem možnostem nasazení pro VMware najdete v našich **postupech**:
    - Nastavení [zdroje replikace](vmware-azure-set-up-source.md) a [konfiguračního serveru](vmware-azure-deploy-configuration-server.md)
    - Nastavení [cíle replikace](vmware-azure-set-up-target.md)
    - Konfigurace [zásady replikace](vmware-azure-set-up-replication.md) a [povolení replikace](vmware-azure-enable-replication.md)


## <a name="select-a-protection-goal"></a>Výběr cíle ochrany

1. V okně **Trezory Recovery Services** vyberte název trezoru. Pro tento scénář používáme **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V části **Cíl ochrany** > **Kde jsou vaše počítače umístěné?** vyberte **Místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V části **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere Hypervisor**. Pak vyberte **OK**.


## <a name="plan-your-deployment"></a>Plánování nasazení

V tomto kurzu vám ukážeme, jak replikovat jeden virtuální počítač a v **Plánování nasazení** vybereme možnost **Ano, mám to hotové**. Pokud nasazujete více virtuálních počítačů, doporučujeme tento krok nepřeskakovat. Pomůže vám náš [nástroj Plánovač nasazení](https://aka.ms/asr-deployment-planner). [Přečtěte si další informace](site-recovery-deployment-planner.md) o tomto nástroji.

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Jako první krok nasazení můžete nastavit zdrojové prostředí. Potřebujete jeden vysoce dostupný místní počítač, který bude hostovat místní komponenty Site Recovery. Mezi tyto komponenty patří konfigurační server, procesový server a hlavní cílový server:

- Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat.
- Procesní server funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure. Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.
- Hlavní cílový server zpracovává replikační data během navrácení služeb z Azure po obnovení.

Pokud chcete nastavit konfigurační server jako vysoce dostupný virtuální počítač VMware, stáhněte připravenou šablonu OVA (Open Virtualization Application) a importujte ji do VMware. Tím vytvoříte požadovaný virtuální počítač. Jakmile nastavíte konfigurační server, zaregistrujte ho do vybraného trezoru. Po registraci služba Site Recovery zjistí místní virtuální počítače VMware.

> [!TIP]
> V tomto kurzu se k vytvoření virtuálního počítače VMware s konfiguračním serverem používá šablona OVA. Pokud to nemůžete udělat, můžete [nastavit konfigurační server ručně](physical-manage-configuration-server.md).

> [!TIP]
> V tomto kurzu Site Recovery stáhne a nainstaluje na konfigurační server MySQL. Pokud nechcete, aby Site Recovery provedl tuto akci, můžete ho nastavit ručně. [Další informace](vmware-azure-deploy-configuration-server.md#configure-settings).


### <a name="download-the-vm-template"></a>Stažení šablony virtuálního počítače

1. V daném trezoru klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte šablonu OVF pro konfigurační server.

 > [!TIP]
 >Nejnovější verzi šablony konfiguračního serveru můžete stáhnout přímo z webu [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

>[!NOTE]
Licence, která je součástí šablony OVF, je zkušební licence platná po dobu 180 dnů. Zákazník musí aktivovat Windows s použitím zakoupené licence.

## <a name="import-the-template-in-vmware"></a>Import šablony do VMware

1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **File** (Soubor) vyberte **Deploy OVF Template** (Nasadit šablonu OVF). Spustíte tak **průvodce nasazením šablony OVF**. 

     ![Šablona OVF](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. V části **Select source** (Vybrat zdroj) zadejte umístění staženého souboru OVF.
4. V části **Review details** (Kontrola podrobností) vyberte **Next** (Další).
5. V části **Select name and folder** (Vybrat název a složku) a **Select configuration** (Vybrat konfiguraci) přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. Na stránce **Ready to complete** (Připraveno k dokončení) vyberte k vytvoření virtuálního počítače s výchozím nastavením možnosti **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit).

    > [!TIP]
  Pokud chcete přidat další síťové rozhraní, zrušte zaškrtnutí políčka **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit). Ve výchozím nastavení obsahuje šablona jeden síťový adaptér. Po nasazení můžete přidat další síťové adaptéry.

## <a name="add-an-additional-adapter"></a>Přidání dalšího adaptéru

Pokud chcete na konfigurační server přidat další síťový adaptér, udělejte to, než zaregistrujete server do trezoru. Jakmile ho zaregistrujete, další adaptéry nebudete moct přidat.

1. V inventáři konzole vSphere Client klikněte pravým tlačítkem na virtuální počítač a vyberte **Edit Settings** (Upravit nastavení).
2. V části **Hardware** vyberte **Add** (Přidat) > **Ethernet Adapter** (Adaptér sítě Ethernet). Pak vyberte **Další**.
3. Vyberte typ adaptéru a síť. 
4. Vyberte **Connect at power on** (Připojit při spuštění), aby se po spuštění virtuálního počítače připojila virtuální síťová karta. Vyberte **Next** (Další) > **Finish** (Dokončit). Pak vyberte **OK**.


## <a name="register-the-configuration-server"></a>Registrace konfiguračního serveru 

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce.
4. Při prvním přihlášení se během pár vteřin spustí nástroj pro konfiguraci služby Azure Site Recovery.
5. Zadejte název, pod kterým se konfigurační server zaregistruje do služby Site Recovery. Pak vyberte **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.
7. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Za chvilku se spustí automaticky průvodce správou konfiguračního serveru.

### <a name="configure-settings-and-add-the-vmware-server"></a>Konfigurace nastavení a přidání serveru VMware

1. V průvodci správou konfiguračního serveru vyberte **Nastavit připojení** a pak vyberte síťový adaptér, přes který bude procesový server přijímat provoz replikace z virtuálních počítačů. Potom vyberte **Uložit**. Po dokončení konfigurace není možné toto nastavení změnit.
2. V části **Vyberte trezor služby Recovery Services** vyberte své předplatné Azure a příslušnou skupinu prostředků a trezor.
3. V části **Nainstalovat software třetí strany** přijměte licenční smlouvu. Vyberte **Stáhnout a nainstalovat** a nainstalujte MySQL Server. Pokud jste v cestě umístili MySQL, tento krok se přeskočí.
4. Vyberte **Nainstalovat VMware PowerCLI**. Než to uděláte, zkontrolujte, jestli jsou zavřená všechna okna prohlížeče. Potom vyberte **Pokračovat**.
5. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
6. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
7. Zadejte přihlašovací údaje uživatele, které bude konfigurační server používat pro připojení k serveru VMware. Ujistěte se, že jsou uživatelské jméno a heslo správné a že je uživatel součástí skupiny Administrators ve virtuálním počítači, který chcete chránit. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **Přidat** a pak **Pokračovat**.
8. V části **Nakonfigurovat přihlašovací údaje virtuálního počítače** zadejte uživatelské jméno a heslo, která se po povolení replikace použijí pro automatickou instalaci služby mobility na virtuální počítače.
    - Když chcete replikovat počítače s Windows, je nutné, aby na nich měl tento účet oprávnění místního správce.
    - U počítačů s Linuxem zadejte údaje superuživatele.
9. Vyberte **Dokončit konfiguraci** a dokončete registraci.
10. Jakmile bude registrace dokončená, zkontrolujte Azure Portal, jestli je ve vybraném trezoru na stránce **Zdroj** uvedený konfigurační server a VMware server. Pak vyberte **OK** a nakonfigurujte nastavení cíle.


Site Recovery se připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače.

> [!NOTE]
> Může trvat 15 minut nebo déle, než se název účtu objeví na portálu. Pokud chcete provést aktualizaci okamžitě, vyberte **Konfigurační servery** > ***název serveru*** > **Aktualizovat server**.

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Vyberte **Příprava infrastruktury** > **Cíl**. Vyberte předplatné Azure, které chcete použít. Používáme model Resource Manager.
2. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure. Měli byste je mít, když nastavujete komponenty Azure v [prvním kurzu](tutorial-prepare-azure.md) v této sérii.

   ![Karta Cíl](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Otevřete [Azure Portal](https://portal.azure.com) a vyberte **Všechny prostředky**.
2. Vyberte trezor služby Recovery Services (v tomto kurzu **ContosoVMVault**).
3. Zásadu replikace vytvoříte tak, že vyberete **Infrastruktura Site Recovery** > **Zásady replikace** > **+ Zásada replikace**.
4. V části **Vytvoření zásady replikace** zadejte název zásady. Používáme **VMwareRepPolicy**.
5. V části **Prahová hodnota cíle bodu obnovení (RPO)** použijte výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
6. V části **Uchování bodu obnovení** zadejte, po jakou delší dobu se má každý bod obnovení uchovat. V tomto kurzu použijeme 72 hodin. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
7. U možnosti **Frekvence snímků konzistentní vzhledem k aplikacím**  zadejte, jak často se vytvářejí snímky konzistentní vzhledem k aplikaci. Používáme výchozí hodnotu 60 minut. Vyberte **OK** a vytvořte zásadu.

   ![Vytvoření zásady replikace](./media/vmware-azure-tutorial/replication-policy.png)

- Tato zásada se automaticky přidruží ke konfiguračnímu serveru.
- Ve výchozím nastavení se pro navrácení služeb po obnovení automaticky vytvoří zásada párování. Pokud má zásada replikace název například **rep-policy**, zásada navrácení služeb po obnovení bude mít název **rep-policy-failback**. Tato zásada se nepoužije, dokud nespustíte navrácení služeb po obnovení z Azure.

## <a name="enable-replication"></a>Povolení replikace

Replikaci je možné povolit následujícím způsobem:

1. Vyberte **Replikovat aplikaci** > **Zdroj**.
2. U možnosti **Zdroj** vyberte **Místní** a v **Umístění zdroje** vyberte konfigurační server.
3. V části **Typ počítače** vyberte **Virtuální počítače**.
4. V části **vCenter/vSphere Hypervisor** vyberte hostitele vSphere nebo server vCenter, který spravuje hostitele.
5. Vyberte procesový server (standardně se instaluje na virtuální počítač konfiguračního serveru). Pak vyberte **OK**.
6. V části **Cíl** vyberte předplatné a skupinu prostředků, ve které chcete vytvořit virtuální počítače, jejichž služby se převezmou při selhání. Používáme model nasazení Resource Manager. 
7. Vyberte účet úložiště Azure, který chcete použít k replikaci dat, a síť a podsíť Azure, ke kterým se připojí virtuální počítače Azure, když se vytvoří po převzetí služeb při selhání.
8. Vyberte možnost **Nakonfigurovat pro vybrané počítače** a použijte tak nastavení sítě pro všechny virtuální počítače, na kterých jste povolili replikaci. Vyberte **Nakonfigurovat později** a vyberte síť Azure pro konkrétní počítač.
9. V části **Virtuální počítače** > **Výběr virtuálních počítačů** vyberte každý počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Pak vyberte **OK**. Pokud nejste schopni zobrazení/vybrat konkrétní virtuální počítač, klikněte na tlačítko [tady](https://aka.ms/doc-plugin-VM-not-showing) vyřešit.
10. V části **Vlastnosti** > **Konfigurace vlastností** vyberte účet, který použije procesový server při automatické instalaci služby mobility na počítači.
11. V části **Nastavení replikace** > **Konfigurace nastavení replikace** zkontrolujte, jestli je vybraná správná zásada replikace.
12. Vyberte **Povolit replikaci**. Jakmile bude replikace virtuálního počítače povolená, nainstaluje Site Recovery automaticky službu mobility.
13. Průběh úlohy **Povolení ochrany** můžete sledovat tady: **Nastavení** > **Úlohy** > **Úlohy Site Recovery**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.
- Může to trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.
- Pokud chcete monitorovat virtuální počítače, které jste přidali, zkontrolujte čas posledního zjištění virtuálních počítačů v části **Konfigurační servery** > **Poslední kontakt**. Pokud chcete přidat virtuální počítače bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (nevybírejte ho) a vyberte **Aktualizovat**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](site-recovery-test-failover-to-azure.md)
