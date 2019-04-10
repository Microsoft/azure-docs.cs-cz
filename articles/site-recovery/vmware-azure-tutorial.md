---
title: Nastavení zotavení po havárii do Azure pro místní virtuální počítače VMware pomocí Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 4/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9e8f450825b7b4ad0402b8976d68bc23c18ce855
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357874"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Nastavení zotavení po havárii do Azure pro místní virtuální počítače VMware

Tento článek popisuje, jak povolit replikaci pro virtuální počítače VMware v místním, zotavení po havárii do Azure s využitím [Azure Site Recovery](site-recovery-overview.md) služby.

Toto je třetí kurz série, která ukazuje, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware. V předchozím kurzu jsme [připravili v místním prostředí VMware](vmware-azure-tutorial-prepare-on-premises.md) zotavení po havárii do Azure.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte nastavení replikace zdroje a místní konfigurační server Site Recovery.
> * Nastavte nastavení cíle replikace.
> * Vytvořit zásadu replikace.
> * Povolení replikace pro virtuální počítač VMware.

> [!NOTE]
> Kurzy vám ukážou, nejjednodušší způsob nasazení pro scénář. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny přečtěte si článek v části How To Site Recovery obsahu.

## <a name="before-you-start"></a>Než začnete

Proveďte z předchozích kurzů:
1. Ujistěte se, že jste [nastavení Azure](tutorial-prepare-azure.md) místní zotavení po havárii VMware do Azure.
2. Postupujte podle [tyto kroky](vmware-azure-tutorial-prepare-on-premises.md) k Příprava na místní nasazení VMware na zotavení po havárii do Azure.
3. V tomto kurzu jsme ukazují, jak replikovat jeden virtuální počítač. Pokud nasazujete více virtuálních počítačů VMware, abyste používali [nástroj Plánovač nasazení](https://aka.ms/asr-deployment-planner). [Přečtěte si další informace](site-recovery-deployment-planner.md) o tomto nástroji.
4. Tento kurz používá řadu možností, které můžete chtít udělat jinak:
    - Tento kurz používá šablonu OVA vytvořit konfigurační server virtuální počítač VMware. Pokud nemůžete udělat z nějakého důvodu, postupujte podle [tyto pokyny](physical-manage-configuration-server.md) ručně nastavit konfigurační server.
    - V tomto kurzu se Site Recovery automaticky stáhne a nainstaluje MySQL ke konfiguračnímu serveru. Pokud dáváte přednost, můžete můžete ho nastavit ručně místo. [Další informace](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Výběr cíle ochrany

1. V okně **Trezory Recovery Services** vyberte název trezoru. Pro tento scénář používáme **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V části **Cíl ochrany** > **Kde jsou vaše počítače umístěné?** vyberte **Místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V části **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere Hypervisor**. Pak vyberte **OK**.



## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Ve svém zdrojovém prostředí potřebujete jeden vysoce dostupný místní počítač pro hostování těchto místní komponenty Site Recovery:

- **Konfigurační server**: Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat.
- **Procesový server:** Procesní server funguje jako replikační brána. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do účtu úložiště mezipaměti v Azure. Procesní server nainstaluje agenta služby Mobility na virtuálních počítačích, které chcete replikovat, a automaticky vyhledá místní virtuální počítače VMware.
- **Hlavní cílový server**: Hlavní cílový server zpracovává replikační data během navrácení služeb z Azure po obnovení.


Všechny tyto součásti jsou nainstalovány společně na jedné vhodnost místních počítačů, které se označuje jako *konfigurační server*. Ve výchozím nastavení pro zotavení po havárii VMware můžeme nastavit konfigurační server jako vysoce dostupný virtuální počítač VMware. Chcete-li to provést, stáhněte připravenou šablonu otevřít virtualizace aplikace OVA () a importujte ji do VMware. Tím vytvoříte virtuální počítač. 

- Nejnovější verze konfiguračního serveru je k dispozici na portálu. Můžete také stáhnout přímo z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Pokud z nějakého důvodu nemůžete použít šablonu OVA nastavení virtuálního počítače, postupujte podle [tyto pokyny](physical-manage-configuration-server.md) ručně nastavit konfigurační server.
- Licence, které jsou součástí šablony OVF je zkušební licence, který je platný po dobu 180 dnů. Windows běží na virtuálním počítači musí být aktivováno s požadovanou licenci. 


### <a name="download-the-vm-template"></a>Stažení šablony virtuálního počítače

1. V daném trezoru klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte šablonu OVF pro konfigurační server.



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
   > Pokud chcete přidat další síťové rozhraní, zrušte zaškrtnutí políčka **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit). Ve výchozím nastavení obsahuje šablona jeden síťový adaptér. Po nasazení můžete přidat další síťové adaptéry.

## <a name="add-an-additional-adapter"></a>Přidání dalšího adaptéru

Pokud chcete přidat další síťový adaptér ke konfiguračnímu serveru, přidejte ji předtím, než zaregistrujete server v trezoru. Jakmile ho zaregistrujete, další adaptéry nebudete moct přidat.

1. V inventáři konzole vSphere Client klikněte pravým tlačítkem na virtuální počítač a vyberte **Edit Settings** (Upravit nastavení).
2. V části **Hardware** vyberte **Add** (Přidat) > **Ethernet Adapter** (Adaptér sítě Ethernet). Pak vyberte **Další**.
3. Vyberte typ adaptéru a síť. 
4. Vyberte **Connect at power on** (Připojit při spuštění), aby se po spuštění virtuálního počítače připojila virtuální síťová karta. Vyberte **Next** (Další) > **Finish** (Dokončit). Pak vyberte **OK**.


## <a name="register-the-configuration-server"></a>Registrace konfiguračního serveru 

Po nastavení konfiguračního serveru, registrace v trezoru.

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce.
4. Při prvním přihlášení se během pár vteřin spustí nástroj pro konfiguraci služby Azure Site Recovery.
5. Zadejte název, pod kterým se konfigurační server zaregistruje do služby Site Recovery. Pak vyberte **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.
7. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Za chvilku se spustí automaticky průvodce správou konfiguračního serveru.


### <a name="configure-settings-and-add-the-vmware-server"></a>Konfigurace nastavení a přidání serveru VMware

Dokončení nastavení a registrace konfiguračního serveru. 


1. Průvodce správou konfiguračního serveru, vyberte **nastavit připojení**. Z rozevíracích seznamů nejprve vyberte síťový adaptér, který používá integrovaný procesový server pro zjišťování a vynucená instalace služby mobility na zdrojový počítače a potom vyberte síťový adaptér, který konfigurační Server používá pro připojení k Azure. Potom vyberte **Uložit**. Toto nastavení nelze změnit po dokončení konfigurace.
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


Po registraci konfiguračního serveru Site Recovery se připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače.

> [!NOTE]
> Může trvat 15 minut nebo déle, než se název účtu objeví na portálu. Pokud chcete provést aktualizaci okamžitě, vyberte **Konfigurační servery** > ***název serveru*** > **Aktualizovat server**.

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Vyberte **Příprava infrastruktury** > **Cíl**. Vyberte předplatné Azure, které chcete použít. Používáme model Resource Manager.
2. Site Recovery zkontroluje, že máte jeden nebo více virtuálních sítí. Měli byste je mít, když nastavujete komponenty Azure v [prvním kurzu](tutorial-prepare-azure.md) v této sérii.

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

Povolte replikaci pro virtuální počítače následujícím způsobem:

1. Vyberte **Replikovat aplikaci** > **Zdroj**.
1. U možnosti **Zdroj** vyberte **Místní** a v **Umístění zdroje** vyberte konfigurační server.
1. V části **Typ počítače** vyberte **Virtuální počítače**.
1. V části **vCenter/vSphere Hypervisor** vyberte hostitele vSphere nebo server vCenter, který spravuje hostitele.
1. Vyberte procesový server (standardně se instaluje na virtuální počítač konfiguračního serveru). Pak vyberte **OK**.
1. V části **Cíl** vyberte předplatné a skupinu prostředků, ve které chcete vytvořit virtuální počítače, jejichž služby se převezmou při selhání. Používáme model nasazení Resource Manager. 
1. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se vytvoří po převzetí služeb při selhání.
1. Vyberte možnost **Nakonfigurovat pro vybrané počítače** a použijte tak nastavení sítě pro všechny virtuální počítače, na kterých jste povolili replikaci. Vyberte **Nakonfigurovat později** a vyberte síť Azure pro konkrétní počítač.
1. V části **Virtuální počítače** > **Výběr virtuálních počítačů** vyberte každý počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Pak vyberte **OK**. Pokud nejste schopni zobrazení/vybrat žádné konkrétní virtuální počítač [Další](https://aka.ms/doc-plugin-VM-not-showing) o řešení problému.
1. V části **Vlastnosti** > **Konfigurace vlastností** vyberte účet, který použije procesový server při automatické instalaci služby mobility na počítači.
1. V části **Nastavení replikace** > **Konfigurace nastavení replikace** zkontrolujte, jestli je vybraná správná zásada replikace.
1. Vyberte **Povolit replikaci**. Jakmile bude replikace virtuálního počítače povolená, nainstaluje Site Recovery automaticky službu mobility.
1. Průběh úlohy **Povolení ochrany** můžete sledovat tady: **Nastavení** > **Úlohy** > **Úlohy Site Recovery**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.
1. Může to trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.
1. Pokud chcete monitorovat virtuální počítače, které jste přidali, zkontrolujte čas posledního zjištění virtuálních počítačů v části **Konfigurační servery** > **Poslední kontakt**. Pokud chcete přidat virtuální počítače bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (nevybírejte ho) a vyberte **Aktualizovat**.

## <a name="next-steps"></a>Další postup
Po povolení replikace, spusťte procházení, abyste měli jistotu, že všechno funguje podle očekávání.
> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](site-recovery-test-failover-to-azure.md)
