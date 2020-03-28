---
title: Nastavení zotavení po havárii virtuálního počítače VMware do Azure pomocí Azure Site Recovery
description: Zjistěte, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 37fdd42adf66ebcb11b357ece6ea63384630d9f4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238864"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Nastavení zotavení po havárii do Azure pro místní virtuální počítače VMware

Tento článek popisuje, jak povolit replikaci pro místní virtuální počítače VMware pro zotavení po havárii do Azure pomocí služby [Azure Site Recovery.](site-recovery-overview.md)

Toto je třetí kurz v řadě, který ukazuje, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware. V předchozím kurzu jsme [připravili místní prostředí VMware](vmware-azure-tutorial-prepare-on-premises.md) pro zotavení po havárii do Azure.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte nastavení zdrojové replikace a místní konfigurační server Site Recovery.
> * Nastavte nastavení cíle replikace.
> * Vytvořit zásadu replikace.
> * Povolte replikaci pro virtuální ho dovážka VMware.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny naleznete v článku v části Jak na webu recovery obsah.

## <a name="before-you-start"></a>Než začnete

Dokončete předchozí kurzy:
1. Ujistěte se, že jste [do Azure nastavili Azure](tutorial-prepare-azure.md) pro místní zotavení po havárii v Systému VMware.
2. Podle [těchto kroků](vmware-azure-tutorial-prepare-on-premises.md) připravte místní nasazení VMware pro zotavení po havárii do Azure.
3. V tomto kurzu vám ukážeme, jak replikovat jeden virtuální virtuální ms. Pokud nasazujete více virtuálních počítačích VMware, měli byste použít [nástroj plánovače nasazení](https://aka.ms/asr-deployment-planner). [Přečtěte si další informace](site-recovery-deployment-planner.md) o tomto nástroji.
4. Tento kurz používá řadu možností, které můžete chtít udělat jinak:
    - Kurz používá šablonu OVA k vytvoření konfiguračního serveru VMware VM. Pokud to z nějakého důvodu nemůžete udělat, nastavte konfigurační server ručně podle [těchto pokynů.](physical-manage-configuration-server.md)
    - V tomto kurzu site recovery automaticky stáhne a nainstaluje MySQL na konfigurační server. Pokud chcete, můžete jej místo toho nastavit ručně. [Další informace](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Výběr cíle ochrany

1. V okně **Trezory Recovery Services** vyberte název trezoru. Pro tento scénář používáme **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V **adrese Protection goal** > **Where are your machines located**vyberte **On-premises**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V části **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere Hypervisor**. Pak vyberte **OK**.



## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Ve zdrojovém prostředí potřebujete k hostování těchto místních součástí site recovery jeden vysoce dostupný místní počítač:

- **Konfigurační server**: Konfigurační server koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.
- **Procesní server**: Procesový server funguje jako replikační brána. Přijímá data replikace; optimalizuje ji pomocí ukládání do mezipaměti, komprese a šifrování a odesílá ji do účtu úložiště mezipaměti v Azure. Procesní server také nainstaluje agenta služby Mobility service na virtuální chod, které chcete replikovat, a provede automatické zjišťování místních virtuálních počítačích VMware.
- **Hlavní cílový server**: Hlavní cílový server zpracovává data replikace během navrácení služeb po selhání z Azure.


Všechny tyto součásti jsou nainstalovány společně na jednom místním počítači, který se označuje jako *konfigurační server*. Ve výchozím nastavení pro zotavení po havárii společnosti VMware nastavíme konfigurační server jako vysoce dostupný virtuální virtuální počítače VMware. Chcete-li to provést, stáhněte si připravenou šablonu Open Virtualization Application (OVA) a importujte šablonu do vmware a vytvořte virtuální počítač. 

- Na portálu je k dispozici nejnovější verze konfiguračního serveru. Můžete si ji také stáhnout přímo ze služby [Stažení softwaru](https://aka.ms/asrconfigurationserver).
- Pokud z nějakého důvodu nelze použít šablonu OVA k nastavení virtuálního počítače, postupujte [podle těchto pokynů](physical-manage-configuration-server.md) k ručnímu nastavení konfiguračního serveru.
- Licence poskytnutá se šablonou OVF je zkušební licence platná po dobu 180 dnů. Systém Windows spuštěný na virtuálním počítači musí být aktivován s požadovanou licencí. 


### <a name="download-the-vm-template"></a>Stažení šablony virtuálního počítače

1. V trezoru přejděte na připravit**zdroj** **infrastruktury** > .
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte šablonu OVF pro konfigurační server.



## <a name="import-the-template-in-vmware"></a>Import šablony do VMware


1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **Soubor** vyberte **možnost Nasadit šablonu OVF** a spusťte **Průvodce nasazením šablony OVF**. 

     ![Šablona OVF](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. V části **Select source** (Vybrat zdroj) zadejte umístění staženého souboru OVF.
4. V části **Review details** (Kontrola podrobností) vyberte **Next** (Další).
5. V části **Select name and folder** (Vybrat název a složku) a **Select configuration** (Vybrat konfiguraci) přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. Na stránce **Ready to complete** (Připraveno k dokončení) vyberte k vytvoření virtuálního počítače s výchozím nastavením možnosti **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit).

   > [!TIP]
   > Pokud chcete přidat další nic, zrušte po > **dokončení** **nasazení možnost INIC**. Ve výchozím nastavení obsahuje šablona jeden síťový adaptér. Po nasazení můžete přidat další síťové adaptéry.

## <a name="add-an-additional-adapter"></a>Přidání dalšího adaptéru

Pokud chcete na konfigurační server přidat další nic, přidejte ji před registrací serveru v úschovně. Jakmile ho zaregistrujete, další adaptéry nebudete moct přidat.

1. V inventáři konzole vSphere Client klikněte pravým tlačítkem na virtuální počítač a vyberte **Edit Settings** (Upravit nastavení).
2. V části **Hardware** vyberte **Add** (Přidat) > **Ethernet Adapter** (Adaptér sítě Ethernet). Pak vyberte **Další**.
3. Vyberte typ adaptéru a síť. 
4. Vyberte **Connect at power on** (Připojit při spuštění), aby se po spuštění virtuálního počítače připojila virtuální síťová karta. Vyberte **další** > **dokončení**. Pak vyberte **OK**.


## <a name="register-the-configuration-server"></a>Registrace konfiguračního serveru 

Po nastavení konfiguračního serveru jej zaregistrujete v úschovně.

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce.
4. Při prvním přihlášení se během pár vteřin spustí nástroj pro konfiguraci služby Azure Site Recovery.
5. Zadejte název, pod kterým se konfigurační server zaregistruje do služby Site Recovery. Pak vyberte **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat. Ujistěte se, že jsou tomuto uživateli přiřazeny potřebné [role.](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements)
7. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Za chvilku se spustí automaticky průvodce správou konfiguračního serveru.


### <a name="configure-settings-and-add-the-vmware-server"></a>Konfigurace nastavení a přidání serveru VMware

Dokončete nastavení a registraci konfiguračního serveru. Než budete pokračovat, ujistěte se, že jsou splněny všechny [předpoklady](vmware-azure-deploy-configuration-server.md#prerequisites) pro úspěšné nastavení konfiguračního serveru.


1. V Průvodci správou konfiguračního serveru vyberte **možnost Nastavení připojení**. Z rozevíracích nabídek nejprve vyberte nic, kterou vestavěný procesní server používá pro zjišťování a nabízenou instalaci služby mobility ve zdrojových počítačích, a pak vyberte nic, kterou konfigurační server používá pro připojení k Azure. Potom vyberte **Uložit**. Toto nastavení nelze po konfiguraci změnit.
2. V **části Select Recovery Services vault**vyberte předplatné Azure a příslušnou skupinu prostředků a trezor.
3. V části **Nainstalovat software třetí strany** přijměte licenční smlouvu. Vyberte **Stáhnout a nainstalovat** a nainstalujte MySQL Server. Pokud jste umístili MySQL v cestě, tento krok lze přeskočit. [Další](vmware-azure-deploy-configuration-server.md#configure-settings) informace
4. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
5. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
6. Zadejte přihlašovací údaje uživatele, které bude konfigurační server používat pro připojení k serveru VMware. Ujistěte se, že jsou uživatelské jméno a heslo správné a že je uživatel součástí skupiny Administrators ve virtuálním počítači, který chcete chránit. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **Přidat** a pak **Pokračovat**.
7. V části **Nakonfigurovat přihlašovací údaje virtuálního počítače** zadejte uživatelské jméno a heslo, která se po povolení replikace použijí pro automatickou instalaci služby mobility na virtuální počítače.
    - Když chcete replikovat počítače s Windows, je nutné, aby na nich měl tento účet oprávnění místního správce.
    - U počítačů s Linuxem zadejte údaje superuživatele.
8. Vyberte **Dokončit konfiguraci** a dokončete registraci.
9. Po dokončení registrace otevřete portál Azure a ověřte, zda jsou konfigurační server a server VMware uvedeny na**serverech** > pro**konfiguraci** > **infrastruktury** >  **obnovy služby Recovery**Services .


Po registraci konfiguračního serveru se site recovery připojí k serverům VMware pomocí zadaného nastavení a zjišťuje virtuální počítače.

> [!NOTE]
> Může trvat 15 minut nebo déle, než se název účtu objeví na portálu. Chcete-li aktualizovat okamžitě, vyberte***název serveru*** >  **Konfigurační server** > **.**

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Vyberte možnost Připravit > **cíl** **infrastruktury**. Vyberte předplatné Azure, které chcete použít. Používáme model Resource Manager.
2. Site Recovery zkontroluje, zda máte jednu nebo více virtuálních sítí. Měli byste je mít, když nastavujete komponenty Azure v [prvním kurzu](tutorial-prepare-azure.md) v této sérii.

   ![Karta Cíl](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Otevřete [portál Azure](https://portal.azure.com). Vyhledejte a vyberte **trezory služby Recovery Services**.
2. Vyberte trezor služby Recovery Services (v tomto kurzu **ContosoVMVault**).
3. Chcete-li vytvořit zásady replikace, vyberte možnost**Zásady replikace infrastruktury** >  **obnovení lokality** > **+Zásady replikace**.
4. V části **Vytvoření zásady replikace** zadejte název zásady. Používáme **VMwareRepPolicy**.
5. V části **Prahová hodnota cíle bodu obnovení (RPO)** použijte výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
6. V části **Uchování bodu obnovení** zadejte, po jakou delší dobu se má každý bod obnovení uchovat. V tomto kurzu použijeme 72 hodin. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
7. U možnosti **Frekvence snímků konzistentní vzhledem k aplikacím ** zadejte, jak často se vytvářejí snímky konzistentní vzhledem k aplikaci. Používáme výchozí hodnotu 60 minut. Vyberte **OK** a vytvořte zásadu.

   ![Vytvoření zásady replikace](./media/vmware-azure-tutorial/replication-policy.png)

- Tato zásada se automaticky přidruží ke konfiguračnímu serveru.
- Ve výchozím nastavení se pro navrácení služeb po obnovení automaticky vytvoří zásada párování. Pokud má zásada replikace název například **rep-policy**, zásada navrácení služeb po obnovení bude mít název **rep-policy-failback**. Tato zásada se nepoužije, dokud nespustíte navrácení služeb po obnovení z Azure.

## <a name="enable-replication"></a>Povolení replikace

Povolte replikaci pro virtuální chod následujícím způsobem:

1. Vyberte **možnost Replikovat** > **zdroj**aplikace .
2. U možnosti **Zdroj** vyberte **Místní** a v **Umístění zdroje** vyberte konfigurační server.
3. V části **Typ počítače** vyberte **Virtuální počítače**.
4. V části **vCenter/vSphere Hypervisor** vyberte hostitele vSphere nebo server vCenter, který spravuje hostitele.
5. Vyberte procesový server (standardně se instaluje na virtuální počítač konfiguračního serveru). Pak vyberte **OK**. Stav každého procesního serveru je uveden podle doporučených limitů a dalších parametrů. Zvolte server pro zpracování v pořádku. Nelze vybrat [kritický](vmware-physical-azure-monitor-process-server.md#process-server-alerts) procesový server. Můžete buď [vyřešit a vyřešit](vmware-physical-azure-troubleshoot-process-server.md) chyby **nebo** nastavit [horizontální navýšení kapacity procesu serveru](vmware-azure-set-up-process-server-scale.md).
6. V části **Cíl** vyberte předplatné a skupinu prostředků, ve které chcete vytvořit virtuální počítače, jejichž služby se převezmou při selhání. Používáme model nasazení Resource Manager. 
7. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se vytvoří po převzetí služeb při selhání.
8. Vyberte možnost **Nakonfigurovat pro vybrané počítače** a použijte tak nastavení sítě pro všechny virtuální počítače, na kterých jste povolili replikaci. Chcete-li vybrat síť Azure pro každý počítač, **vyberte možnost Konfigurovat později.**
9. Ve **virtuálních počítačích** > **Vyberte virtuální počítače**vyberte každý počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Pak vyberte **OK**. Pokud nejste schopni zobrazit nebo vybrat konkrétní virtuální počítač, [další informace](https://aka.ms/doc-plugin-VM-not-showing) o řešení problému.
10. V **části Vlastnosti** > **Konfigurace vlastností**vyberte účet, který má procesový server použít k automatické instalaci služby Mobility Service do počítače.
11. V >  **nastavení replikace**Konfigurace nastavení**replikace**ověřte, zda je vybrána správná zásada replikace.
12. Vyberte **Povolit replikaci**. Jakmile bude replikace virtuálního počítače povolená, nainstaluje Site Recovery automaticky službu mobility.
13. Průběh úlohy **Povolit ochranu** můžete sledovat v části**Úlohy** > **obnovení webu** **nastavení** > . Po dokončení úlohy **Finalize Protection** a dokončení generování bodů obnovení je počítač připraven k převzetí služeb při selhání.
14. Může to trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.
15. Chcete-li sledovat virtuální počítače, které přidáte, zkontrolujte čas posledního zjištěné ho pro virtuální počítače v **konfiguračních serverech** > **poslední kontakt na**. Pokud chcete přidat virtuální počítače bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (nevybírejte ho) a vyberte **Aktualizovat**.

## <a name="next-steps"></a>Další kroky
Po povolení replikace spusťte postupnou dobu, abyste se ujistili, že vše funguje podle očekávání.
> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](site-recovery-test-failover-to-azure.md)
