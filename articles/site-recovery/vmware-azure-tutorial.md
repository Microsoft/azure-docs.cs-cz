---
title: Nastavení zotavení po havárii do Azure pro místní virtuální počítače VMware pomocí Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 173e31e3b1f855d488f7f8baf6659b1521ea7aa5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Nastavení zotavení po havárii do Azure pro místní virtuální počítače VMware

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware se systémem Windows. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zadat zdroj a cíl replikace.
> * Nastavit zdrojové prostředí replikace, včetně místních komponent Azure Site Recovery, a cílové prostředí replikace.
> * Vytvořit zásadu replikace.
> * Povolit replikaci virtuálního počítače.

Tento kurz je třetí částí série. Tento kurz předpokládá, že jste už dokončili úlohy z předchozích kurzů:

* [Příprava Azure](tutorial-prepare-azure.md). Tento kurz popisuje nastavení účtu úložiště Azure a sítě, ověření správných oprávnění účtu Azure a vytvoření trezoru služby Recovery Services.
* [Příprava VMware v místním prostředí](vmware-azure-tutorial-prepare-on-premises.md). V tomto kurzu připravíte účty, aby služba Site Recovery měla přístup k serverům VMware za účelem zjištění virtuálních počítačů a volitelného provedení nabízené instalace komponenty služby mobility Site Recovery při povolení replikace virtuálního počítače. Také se ujistíte, že vaše servery a virtuální počítače VMware splňují požadavky služby Site Recovery.

Než začnete, doporučujeme [zkontrolovat architekturu](vmware-azure-architecture.md) určenou pro scénáře zotavení po havárii.


## <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. V části **Trezory služby Recovery Services** vyberte název trezoru **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V části **Cíl ochrany** > **Kde jsou vaše počítače umístěné?** vyberte **Místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V části **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere Hypervisor**. Pak vyberte **OK**.

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí


Pro nastavení zdrojového prostředí potřebujete jeden vysoce dostupný místní počítač, který bude hostovat místní komponenty Site Recovery. Mezi tyto komponenty patří konfigurační server, procesový server a hlavní cílový server:

- Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat.
- Procesní server funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure. Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.
- Hlavní cílový server zpracovává replikační data během navrácení služeb z Azure po obnovení.

Pokud chcete nastavit konfigurační server jako vysoce dostupný virtuální počítač VMware, stáhněte připravenou šablonu OVF (Open Virtualization Format) a importujte ji do VMware. Tím vytvoříte požadovaný virtuální počítač. Jakmile nastavíte konfigurační server, zaregistrujte ho do vybraného trezoru. Po registraci služba Site Recovery zjistí místní virtuální počítače VMware.

> [!TIP]
> V tomto kurzu se k vytvoření virtuálního počítače VMware s konfiguračním serverem používá šablona OVF. Pokud nemůžete použít tento postup, můžete provést [ruční instalaci](physical-manage-configuration-server.md). 


### <a name="download-the-vm-template"></a>Stažení šablony virtuálního počítače

1. V daném trezoru klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte šablonu OVF pro konfigurační server.

  > [!TIP]
  Nejnovější verzi šablony konfiguračního serveru můžete stáhnout přímo z webu [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>Import šablony do VMware

1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **File** (Soubor) vyberte **Deploy OVF Template** (Nasadit šablonu OVF). Tím spustíte průvodce nasazením šablony OVF. 

     ![Šablona OVF](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. V části **Select source** (Vybrat zdroj) zadejte umístění staženého souboru OVF.
4. V části **Review details** (Kontrola podrobností) vyberte **Next** (Další).
5. V části **Select name and folder** (Vybrat název a složku) a **Select configuration** (Vybrat konfiguraci) přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. V části **Ready to complete** (Připraveno k dokončení):

    * Pokud chcete vytvořit virtuální počítač s výchozím nastavením, vyberte **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit).

    * Pokud chcete přidat další síťové rozhraní, zrušte zaškrtnutí políčka **Power on after deployment** (Spustit po nasazení). Pak vyberte **Dokončit**. Ve výchozím nastavení se šablona konfiguračního serveru nasadí s jedním síťovým adaptérem. Po nasazení můžete přidat další síťové adaptéry.

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
4. Při prvním přihlášení se spustí nástroj pro konfiguraci služby Azure Site Recovery.
5. Zadejte název, pod kterým se konfigurační server zaregistruje do služby Site Recovery. Pak vyberte **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.
7. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Automaticky se spustí průvodce správou konfiguračního serveru.

### <a name="configure-settings-and-add-the-vmware-server"></a>Konfigurace nastavení a přidání serveru VMware

1. V průvodci správou konfiguračního serveru vyberte **Nastavit připojení** a pak vyberte síťový adaptér, přes který bude procesový server přijímat provoz replikace z virtuálních počítačů. Potom vyberte **Uložit**. Po dokončení konfigurace není možné toto nastavení změnit.
2. V části **Vyberte trezor služby Recovery Services** vyberte své předplatné Azure a příslušnou skupinu prostředků a trezor.
3. V části **Nainstalovat software třetí strany** přijměte licenční smlouvu. Vyberte **Stáhnout a nainstalovat** a nainstalujte MySQL Server.
4. Vyberte **Nainstalovat VMware PowerCLI**. Než to uděláte, zkontrolujte, jestli jsou zavřená všechna okna prohlížeče. Potom vyberte **Pokračovat**.
5. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
6. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
7. Zadejte přihlašovací údaje, které bude konfigurační server používat pro připojení k serveru VMware. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **Přidat** a pak **Pokračovat**.
8. V části **Nakonfigurovat přihlašovací údaje virtuálního počítače** zadejte uživatelské jméno a heslo, které se po povolení replikace použijí pro automatickou instalaci služby mobility na počítače. Když chcete replikovat počítače s Windows, je nutné, aby na nich měl tento účet oprávnění místního správce. U počítačů s Linuxem zadejte údaje superuživatele.
9. Vyberte **Dokončit konfiguraci** a dokončete registraci. 
10. Jakmile bude registrace dokončená, zkontrolujte Azure Portal, jestli je ve vybraném trezoru na stránce **Zdroj** uvedený konfigurační server a VMware server. Pak vyberte **OK** a nakonfigurujte nastavení cíle.


Site Recovery se připojí k serverům VMware pomocí zadaného nastavení a vyhledá virtuální počítače.

> [!NOTE]
> Může trvat 15 minut nebo déle, než se název účtu objeví na portálu. Pokud chcete provést aktualizaci okamžitě, vyberte **Konfigurační servery** > ***název serveru*** > **Aktualizovat server**.

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Vyberte **Příprava infrastruktury** > **Cíl**. Vyberte předplatné Azure, které chcete použít.
2. Zadejte, jestli vaše cílové nasazení využívá model nasazení Azure Resource Manager nebo Classic.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

   ![Karta Cíl](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Otevřete [Azure Portal](https://portal.azure.com) a vyberte **Všechny prostředky**.
2. Vyberte trezor služby Recovery Services s názvem**ContosoVMVault**.
3. Zásadu replikace vytvoříte tak, že vyberete **Infrastruktura Site Recovery** > **Zásady replikace** > **+ Zásada replikace**.
4. V části **Vytvoření zásady replikace** zadejte jako název zásady **VMwareRepPolicy**.
5. V části **Prahová hodnota cíle bodu obnovení (RPO)** použijte výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
6. V části **Uchování bodu obnovení** použijte výchozí hodnotu 24 hodin a nastavte délku okna uchování jednotlivých bodů obnovení. Pro účely tohoto kurzu použijte 72 hodin. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
7. V části **Frekvence snímků konzistentní vzhledem k aplikacím** použijte jako frekvenci pro vytváření snímků konzistentních vzhledem k aplikacím výchozí hodnotu 60 minut. Vyberte **OK** a vytvořte zásadu.

   ![Vytvoření zásady replikace](./media/vmware-azure-tutorial/replication-policy.png)

Tato zásada se automaticky přidruží ke konfiguračnímu serveru. Ve výchozím nastavení se pro navrácení služeb po obnovení automaticky vytvoří zásada párování. Pokud má zásada replikace název například **rep-policy**, zásada navrácení služeb po obnovení bude mít název **rep-policy-failback**. Tato zásada se nepoužije, dokud nespustíte navrácení služeb po obnovení z Azure.

## <a name="enable-replication"></a>Povolení replikace

Jakmile bude replikace virtuálního počítače povolená, Site Recovery nainstaluje službu mobility. Může to trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.

Replikaci povolte následujícím způsobem:

1. Vyberte **Replikovat aplikaci** > **Zdroj**.
2. V části **Zdroj** vyberte konfigurační server.
3. V části **Typ počítače** vyberte **Virtuální počítače**.
4. V části **vCenter/vSphere Hypervisor** vyberte vCenter Server, který spravuje hostitele vSphere, nebo vyberte samotného hostitele.
5. Vyberte procesní server (konfigurační server). Pak vyberte **OK**.
6. V části **Cíl** vyberte předplatné a skupinu prostředků, ve které chcete vytvořit virtuální počítače, jejichž služby se převezmou při selhání. Zvolte model nasazení (Classic nebo Resource Manager), který chcete v Azure použít pro virtuální počítače, jejichž služby se převezmou při selhání.
7. Vyberte účet úložiště Azure, který chcete použít pro replikaci dat.
8. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se vytvoří po převzetí služeb při selhání.
9. Výběrem možnosti **Nakonfigurovat pro vybrané počítače** použijte nastavení sítě pro všechny počítače, které jste vybrali pro ochranu. Vyberte **Nakonfigurovat později** a vyberte síť Azure pro konkrétní počítač.
10. V části **Virtuální počítače** > **Výběr virtuálních počítačů** vyberte každý počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Pak vyberte **OK**.
11. V části **Vlastnosti** > **Konfigurace vlastností** vyberte účet, který použije procesový server při automatické instalaci služby mobility na počítači.
12. V části **Nastavení replikace** > **Konfigurace nastavení replikace** zkontrolujte, jestli je vybraná správná zásada replikace.
13. Vyberte **Povolit replikaci**.

Průběh úlohy **Povolení ochrany** můžete sledovat tady: **Nastavení** > **Úlohy** > **Úlohy Site Recovery**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.

Pokud chcete monitorovat virtuální počítače, které jste přidali, zkontrolujte čas posledního zjištění virtuálních počítačů v části **Konfigurační servery** > **Poslední kontakt**. Pokud chcete přidat virtuální počítače bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (nevybírejte ho) a vyberte **Aktualizovat**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](site-recovery-test-failover-to-azure.md)
