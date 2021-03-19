---
title: Nastavení zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery
description: Zjistěte, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: bd87265140a0bfaeb7ef4dada6dd76be1269654b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369365"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Nastavení zotavení po havárii do Azure pro místní virtuální počítače VMware

Tento článek popisuje, jak povolit replikaci pro místní virtuální počítače VMware, pro zotavení po havárii do Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) .

Toto je třetí kurz v řadě, ve kterém se dozvíte, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware. V předchozím kurzu jsme [připravili místní prostředí VMware](vmware-azure-tutorial-prepare-on-premises.md) pro zotavení po havárii do Azure.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte nastavení replikace zdroje a místní Site Recovery konfigurační server.
> * Nastavte nastavení cíle replikace.
> * Vytvořit zásadu replikace.
> * Povolte replikaci pro virtuální počítač VMware.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu k nasazení scénáře. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny najdete v článku v části jak Site Recovery obsahu.

## <a name="before-you-start"></a>Než začnete

Dokončete předchozí kurzy:
1. Ujistěte se, že jste [nastavili Azure](tutorial-prepare-azure.md) pro místní zotavení po havárii VMware do Azure.
2. Pomocí [těchto kroků](vmware-azure-tutorial-prepare-on-premises.md) Připravte místní nasazení VMware na zotavení po havárii do Azure.
3. V tomto kurzu si ukážeme, jak replikovat jeden virtuální počítač. Pokud nasazujete více virtuálních počítačů VMware, měli byste použít [nástroj Plánovač nasazení](https://aka.ms/asr-deployment-planner). [Přečtěte si další informace](site-recovery-deployment-planner.md) o tomto nástroji.
4. V tomto kurzu se používá několik možností, které byste mohli chtít udělat jinak:
    - V tomto kurzu se pomocí šablony vajíček vytvoří virtuální počítač VMware konfiguračního serveru. Pokud to z nějakého důvodu nemůžete udělat, postupujte podle [těchto pokynů](physical-manage-configuration-server.md) a nastavte konfigurační server ručně.
    - V tomto kurzu Site Recovery automaticky stáhne a nainstaluje MySQL na konfigurační server. Pokud chcete, můžete ho místo toho nastavit ručně. [Další informace](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Výběr cíle ochrany

1. V okně **Trezory Recovery Services** vyberte název trezoru. Pro tento scénář používáme **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V nabídce **cíl ochrany**  >  ,**kde jsou umístěny vaše počítače**, vyberte možnost **místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V části **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere Hypervisor**. Pak vyberte **OK**.



## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Ve vašem zdrojovém prostředí potřebujete jeden vysoce dostupný místní počítač, který bude hostovat tyto místní Site Recovery komponenty:

- **Konfigurační server**: konfigurační server koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.
- **Procesový Server**: procesový server funguje jako brána replikace. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do účtu úložiště mezipaměti v Azure. Procesový Server také nainstaluje agenta služby mobility na virtuální počítače, které chcete replikovat, a provádí automatické zjišťování místních virtuálních počítačů VMware.
- **Hlavní cílový server**: hlavní cílový Server zpracovává replikační data během navrácení služeb po obnovení z Azure.


Všechny tyto součásti jsou nainstalovány společně na jednom místním počítači, který je známý jako *konfigurační server*. Ve výchozím nastavení se pro zotavení po havárii VMware nastaví konfigurační server jako vysoce dostupný virtuální počítač VMware. Pokud to chcete provést, Stáhněte si připravenou šablonu Open Application Virtualization (VAJÍČK) a importujte ji do VMware a vytvořte virtuální počítač.

- Na portálu je k dispozici nejnovější verze konfiguračního serveru. Můžete ho také stáhnout přímo z [webu Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Pokud z nějakého důvodu nemůžete použít šablonu vajíček k nastavení virtuálního počítače, postupujte podle [těchto pokynů](physical-manage-configuration-server.md) a nastavte konfigurační server ručně.
- Licence, která je součástí šablony OVF, je zkušební licence platná po dobu 180 dnů. Systém Windows běžící na virtuálním počítači musí být aktivovaný pomocí požadované licence.


### <a name="download-the-vm-template"></a>Stažení šablony virtuálního počítače

1. V trezoru pokračujte na **Příprava**  >  **zdroje** infrastruktury.
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte si šablonu vajíček pro konfigurační server.



## <a name="import-the-template-in-vmware"></a>Import šablony do VMware


1. Přihlaste se k serveru VMware vCenter nebo vSphere hostitele ESXi pomocí klienta VMware vSphere.
2. V nabídce **soubor** vyberte **nasadit šablonu OVF** a spusťte **Průvodce nasazením šablony OVF**.

     ![Snímek obrazovky s příkazem nasadit šablonu OVF v klientovi VMWare vSphere](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. V části **Select source** (Vybrat zdroj) zadejte umístění staženého souboru OVF.
4. V části **Review details** (Kontrola podrobností) vyberte **Next** (Další).
5. V části **Select name and folder** (Vybrat název a složku) a **Select configuration** (Vybrat konfiguraci) přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. Na stránce **Ready to complete** (Připraveno k dokončení) vyberte k vytvoření virtuálního počítače s výchozím nastavením možnosti **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit).

   > [!TIP]
   > Pokud chcete přidat další síťové rozhraní, zrušte **po**  >  **dokončení** nasazení volbu Zapnout. Ve výchozím nastavení obsahuje šablona jeden síťový adaptér. Po nasazení můžete přidat další síťové adaptéry.

## <a name="add-an-additional-adapter"></a>Přidání dalšího adaptéru

Pokud chcete přidat další síťovou kartu ke konfiguračnímu serveru, přidejte ji před registrací serveru v trezoru. Jakmile ho zaregistrujete, další adaptéry nebudete moct přidat.

1. V inventáři konzole vSphere Client klikněte pravým tlačítkem na virtuální počítač a vyberte **Edit Settings** (Upravit nastavení).
2. V části **Hardware** vyberte **Add** (Přidat) > **Ethernet Adapter** (Adaptér sítě Ethernet). Pak vyberte **Další**.
3. Vyberte typ adaptéru a síť.
4. Vyberte **Connect at power on** (Připojit při spuštění), aby se po spuštění virtuálního počítače připojila virtuální síťová karta. Vyberte **Další** > **Dokončit**. Pak vyberte **OK**.


## <a name="register-the-configuration-server"></a>Registrace konfiguračního serveru

Po nastavení konfiguračního serveru ho zaregistrujete v trezoru.

1. Z konzoly klienta VMware vSphere zapněte virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce.
4. Při prvním přihlášení se během pár vteřin spustí nástroj pro konfiguraci služby Azure Site Recovery.
5. Zadejte název, pod kterým se konfigurační server zaregistruje do služby Site Recovery. Pak vyberte **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat. Zajistěte, aby k tomuto uživateli byly přiřazeny potřebné [role](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements) .
7. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Za chvilku se spustí automaticky průvodce správou konfiguračního serveru.


### <a name="configure-settings-and-add-the-vmware-server"></a>Konfigurace nastavení a přidání serveru VMware

Dokončete nastavení a registraci konfiguračního serveru. Než budete pokračovat, ujistěte se, že všechny [požadavky](vmware-azure-deploy-configuration-server.md#prerequisites) jsou splněné pro úspěšné nastavení konfiguračního serveru.


1. V Průvodci správou konfiguračního serveru vyberte **nastavení připojení**. V rozevíracích seznamech vyberte nejdřív síťovou kartu, kterou používá integrovaný procesový Server pro zjišťování a nabízenou instalaci služby mobility na zdrojových počítačích, a pak vyberte síťovou kartu, kterou konfigurační server používá pro připojení k Azure. Pak vyberte **Uložit**. Po nakonfigurování tohoto nastavení už toto nastavení nemůžete změnit.
2. V části **vybrat Recovery Services trezor** vyberte své předplatné Azure a příslušnou skupinu prostředků a trezor.
3. V části **Nainstalovat software třetí strany** přijměte licenční smlouvu. Vyberte **Stáhnout a nainstalovat** a nainstalujte MySQL Server. Pokud jste v cestě umístili MySQL, tento krok se dá přeskočit. Další [informace](vmware-azure-deploy-configuration-server.md#configure-settings)
4. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
5. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
6. Zadejte přihlašovací údaje uživatele, které bude konfigurační server používat pro připojení k serveru VMware. Ujistěte se, že jsou uživatelské jméno a heslo správné a že je uživatel součástí skupiny Administrators ve virtuálním počítači, který chcete chránit. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **Přidat** a pak **Pokračovat**.
7. V části **Nakonfigurovat přihlašovací údaje virtuálního počítače** zadejte uživatelské jméno a heslo, která se po povolení replikace použijí pro automatickou instalaci služby mobility na virtuální počítače.
    - Když chcete replikovat počítače s Windows, je nutné, aby na nich měl tento účet oprávnění místního správce.
    - U počítačů s Linuxem zadejte údaje superuživatele.
8. Vyberte **Dokončit konfiguraci** a dokončete registraci.
9. Po dokončení registrace otevřete Azure Portal a ověřte, že je konfigurační server a server VMware uvedený v **Recovery Services trezoru**  >  **Správa**  >  **Site Recovery**  >  **konfiguračních serverů** infrastruktury.


Po registraci konfiguračního serveru se Site Recovery připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače.

> [!NOTE]
> Může trvat 15 minut nebo déle, než se název účtu objeví na portálu. Pokud chcete aktualizaci hned aktualizovat, vyberte **konfigurační servery**  >  **_název serveru_*_ > _* obnovit server**.

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Vyberte **připravit**  >  **cíl** infrastruktury. Vyberte předplatné Azure, které chcete použít. Používáme model Resource Manager.
2. Site Recovery kontroluje, zda máte jednu nebo více virtuálních sítí. Měli byste je mít, když nastavujete komponenty Azure v [prvním kurzu](tutorial-prepare-azure.md) v této sérii.

   ![Snímek obrazovky přípravy infrastruktury > možnosti cíle](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Otevřete [Azure Portal](https://portal.azure.com). Vyhledejte a vyberte **trezory Recovery Services**.
2. Vyberte trezor služby Recovery Services (v tomto kurzu **ContosoVMVault**).
3. Pokud chcete vytvořit zásadu replikace, vyberte **Site Recovery**  >  **Zásady replikace** infrastruktury  >  **a zásady replikace**.
4. V části **Vytvoření zásady replikace** zadejte název zásady. Používáme **VMwareRepPolicy**.
5. V části **Prahová hodnota cíle bodu obnovení (RPO)** použijte výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
6. V části **Uchování bodu obnovení** zadejte, po jakou delší dobu se má každý bod obnovení uchovat. V tomto kurzu použijeme 72 hodin. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
7. U možnosti **Frekvence snímků konzistentní vzhledem k aplikacím** zadejte, jak často se vytvářejí snímky konzistentní vzhledem k aplikaci. Používáme výchozí hodnotu 60 minut. Vyberte **OK** a vytvořte zásadu.

   ![Snímek obrazovky s možnostmi vytvoření zásad replikace](./media/vmware-azure-tutorial/replication-policy.png)

- Tato zásada se automaticky přidruží ke konfiguračnímu serveru.
- Ve výchozím nastavení se pro navrácení služeb po obnovení automaticky vytvoří zásada párování. Pokud má zásada replikace název například **rep-policy**, zásada navrácení služeb po obnovení bude mít název **rep-policy-failback**. Tato zásada se nepoužije, dokud nespustíte navrácení služeb po obnovení z Azure.

Poznámka: ve scénáři VMware-to-Azure se snímek konzistentní vzhledem k chybě provádí v intervalu 5 minut.

## <a name="enable-replication"></a>Povolení replikace

Replikaci pro virtuální počítače povolíte takto:

1. Vyberte **replikovat**  >  **zdroj** aplikace.
2. U možnosti **Zdroj** vyberte **Místní** a v **Umístění zdroje** vyberte konfigurační server.
3. V části **Typ počítače** vyberte **Virtuální počítače**.
4. V části **vCenter/vSphere Hypervisor** vyberte hostitele vSphere nebo server vCenter, který spravuje hostitele.
5. Vyberte procesový server (standardně se instaluje na virtuální počítač konfiguračního serveru). Pak vyberte **OK**. Stav každého procesového serveru je uvedený podle doporučených omezení a dalších parametrů. Vyberte procesový Server v pořádku. Nelze zvolit [kritický](vmware-physical-azure-monitor-process-server.md#process-server-alerts) procesový Server. Můžete buď vyřešit chyby [, vyřešit](vmware-physical-azure-troubleshoot-process-server.md) je, **nebo** nastavit [procesový Server se škálováním na více](vmware-azure-set-up-process-server-scale.md)instancí.
6. V části **Cíl** vyberte předplatné a skupinu prostředků, ve které chcete vytvořit virtuální počítače, jejichž služby se převezmou při selhání. Používáme model nasazení Resource Manager.
7. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se vytvoří po převzetí služeb při selhání.
8. Vyberte možnost **Nakonfigurovat pro vybrané počítače** a použijte tak nastavení sítě pro všechny virtuální počítače, na kterých jste povolili replikaci. Vyberte **Konfigurovat později** a vyberte síť Azure na jeden počítač.
9. V **Virtual Machines**  >  **vyberte virtuální počítače**, vyberte všechny počítače, které chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Pak vyberte **OK**. Pokud si nejste schopni zobrazit nebo vybrat žádný konkrétní virtuální počítač, [Přečtěte si další informace](./vmware-azure-troubleshoot-replication.md) o řešení tohoto problému.
10. V části **vlastnosti**  >  **Konfigurace vlastností** vyberte účet, který má procesový Server použít k automatické instalaci služby mobility na počítač.
11. V **nastavení replikace**  >  **nakonfigurujte nastavení replikace** a ověřte, jestli je vybraná správná zásada replikace.
12. Vyberte **Povolit replikaci**. Jakmile bude replikace virtuálního počítače povolená, nainstaluje Site Recovery automaticky službu mobility.
13. Průběh úlohy **Povolení ochrany** můžete sledovat v části **Nastavení**  >  **úlohy**  >  **Site Recovery úlohy**. Po spuštění úlohy **Dokončit ochranu** a dokončení generování bodu obnovení je počítač připravený na převzetí služeb při selhání.
14. Může to trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.
15. Pokud chcete monitorovat virtuální počítače, které jste přidali, Projděte si poslední čas zjištěných virtuálních počítačů v části **konfigurační servery**  >  **Poslední kontakt na**. Pokud chcete přidat virtuální počítače bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (nevybírejte ho) a vyberte **Aktualizovat**.

## <a name="next-steps"></a>Další kroky
Po povolení replikace spusťte přechod a ujistěte se, že vše funguje podle očekávání.
> [!div class="nextstepaction"]
> [Provedení postupu zotavení po havárii](site-recovery-test-failover-to-azure.md)