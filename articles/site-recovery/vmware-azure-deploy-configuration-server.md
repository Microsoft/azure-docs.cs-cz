---
title: Nasazení konfiguračního serveru pro zotavení po havárii VMware pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje postup nasazení konfiguračního serveru pro zotavení po havárii VMware pomocí Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/06/2018
ms.author: raynew
ms.openlocfilehash: 0a5eb81ea4456be3d01c9d446798ce1732dff630
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857244"
---
# <a name="deploy-a-configuration-server"></a>Nasazení konfiguračního serveru

Při použití se nasadíte místní konfigurační server [Azure Site Recovery](site-recovery-overview.md) pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure. Konfigurace serveru souřadnice komunikaci mezi místní VMware a Azure. Spravuje taky data replikace. Tento článek vás provede kroky potřebné k nasazení konfiguračního serveru při replikaci virtuálních počítačů VMware do Azure. [Postupujte podle tohoto článku](physical-azure-set-up-source.md) Pokud je potřeba nastavit konfigurační server pro replikaci fyzických serverů.

>[!TIP]
Informace o roli konfiguračního serveru jako součást Azure Site Recovery architektury [tady](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Nasazení konfiguračního serveru pomocí šablony pro soubory OVA

Konfigurační server, musíte nastavit jako vysoce dostupný virtuální počítač VMware s určitým minimální požadavky na hardware a nastavení velikosti. Pohodlný a snadný, nasazení Site Recovery poskytuje ke stažení šablony OVA (Open Virtualization aplikace) nastavit konfigurační server, který splňuje všechny požadavky mandátem uvedených níže.

## <a name="prerequisites"></a>Požadavky

V následující tabulce jsou shrnuté požadavky na minimální hardware pro konfigurační server.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="capacity-planning"></a>Plánování kapacity

Požadavky na velikost pro konfigurační server závisí na potenciální četnost změn dat. Tuto tabulku použijte jako vodítko.

| **CPU** | **Paměť** | **Velikost mezipaměti disku** | **Frekvence změny dat** | **Chráněné počítače** |
| --- | --- | --- | --- | --- |
| 8 virtuálních procesorů (2 sockets * @ 2,5 GHz 4 jádra) |16 GB |300 GB |500 GB nebo méně |Replikace méně než 100 počítačů. |
| 12 virtuálních procesorů (2 sockets * @ 2,5 GHz 6 jader) |18 GB |600 GB |500 GB až 1 TB |Replikace počítačů 100 150. |
| 16 virtuálních procesorů (2 sockets * @ 2,5 GHz 8 jader) |32 GB |1 TB |1 TB na 2 TB |Replikace počítačů 150 až 200. |

Pokud replikujete více než jeden virtuální počítač VMware, přečtěte si [důležité informace o plánování kapacity](/site-recovery-plan-capacity-vmware.md). Spustit [nástroj Plánovač nasazení](site-recovery-deployment-planner.md) pro replikaci VMWare.

## <a name="download-the-template"></a>Stažení šablony

1. V daném trezoru klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte si šablonu otevřít virtualizace aplikace OVA () pro konfigurační server.

  > [!TIP]
>Můžete také stáhnout nejnovější verzi šablona konfiguračního serveru přímo z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

>[!NOTE]
Licence, které jsou poskytovány s šablonou OVA je zkušební licenci, která je platná po dobu 180 dnů. Po uplynutí této doby zákazník potřebuje aktivovat systém windows s licencí opatřené.

## <a name="import-the-template-in-vmware"></a>Import šablony do VMware

1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **File** (Soubor) vyberte **Deploy OVF Template** (Nasadit šablonu OVF). Tím spustíte průvodce nasazením šablony OVF.

     ![Šablona OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. V **výběr zdroje**, zadejte umístění staženého souboru OVF.
4. V **podrobnosti**vyberte **Další**.
5. V **vyberte název a složku** a **vyberte konfiguraci**, přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. V části **Ready to complete** (Připraveno k dokončení):

    * Pokud chcete vytvořit virtuální počítač s výchozím nastavením, vyberte **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit).

    * Chcete-li přidat další síťové rozhraní, zrušte **Power on po nasazení**a pak vyberte **Dokončit**. Ve výchozím nastavení se šablona konfiguračního serveru nasadí s jedním síťovým adaptérem. Po nasazení můžete přidat další síťové adaptéry.

## <a name="add-an-additional-adapter"></a>Přidání dalšího adaptéru

Pokud chcete přidat další síťový adaptér ke konfiguračnímu serveru, přidejte ji předtím, než zaregistrujete server v trezoru. Jakmile ho zaregistrujete, další adaptéry nebudete moct přidat.

1. V inventáři konzole vSphere Client klikněte pravým tlačítkem na virtuální počítač a vyberte **Edit Settings** (Upravit nastavení).
2. V části **Hardware** vyberte **Add** (Přidat) > **Ethernet Adapter** (Adaptér sítě Ethernet). Pak vyberte **Další**.
3. Vyberte typ adaptéru a síť. 
4. Chcete-li připojit virtuální síťové karty, pokud je zapnutý virtuální počítač, vyberte **připojit na zapnutí**. Potom vyberte **Další** > **Dokončit** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registrace konfiguračního serveru pomocí služby Azure Site Recovery

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce.
4. Spustí se při prvním přihlášení se během několika sekund nástroj pro konfiguraci Azure Site Recovery.
5. Zadejte název, pod kterým se konfigurační server zaregistruje do služby Site Recovery. Pak vyberte **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.
7. Nástroj provede několik konfiguračních úloh a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Spustí Průvodce správou konfiguračního serveru **automaticky** několik sekund.

### <a name="configure-settings"></a>Konfigurace nastavení

1. V průvodci správou konfiguračního serveru vyberte **Nastavit připojení** a pak vyberte síťový adaptér, přes který bude procesový server přijímat provoz replikace z virtuálních počítačů. Potom vyberte **Uložit**. Toto nastavení nelze změnit po dokončení konfigurace.
2. V **trezor Recovery Services vyberte**, přihlaste se k Microsoft Azure, vyberte své předplatné Azure a příslušnou skupinu prostředků a trezor.

    > [!NOTE]
    > Po registraci, neexistuje žádný flexibilitu, chcete-li změnit trezor služby recovery services.
    
3. V **nainstalovat software třetí strany**,

    |Scénář   |Postup  |
    |---------|---------|
    |Můžete stáhnout a nainstalovat MySQL ručně?     |  Ano. Stažení aplikace MySQL a umístěte ho do složky **C:\Temp\ASRSetup**, nainstalujte ručně. Teď, když je přijímáte > klikněte na **stáhněte a nainstalujte**, uvádí, že na portálu *už nainstalovaná*. Můžete přejít k dalšímu kroku.       |
    |Můžete vyhnout stažení nástroje MySQL online?     |   Ano. Umístěte do složky aplikace Instalační program MySQL **C:\Temp\ASRSetup**. Přijměte podmínky > klikněte na **stáhnout a nainstalovat**, portál použije instalační program přidali a nainstaluje aplikaci. Můžete přejít na další krok po instalaci.    |
    |Chci stáhnout a nainstalovat MySQL pomocí Azure Site Recovery     |  Přijměte licenční smlouvu a klikněte na **stáhnout a nainstalovat**. Potom můžete přejít na další krok po instalaci.       |
4. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
5. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
6. Zadejte přihlašovací údaje, které bude konfigurační server používat pro připojení k serveru VMware. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **přidat**a potom **pokračovat**. Zde zadané přihlašovací údaje se uloží místně.
7. V **nakonfigurovat přihlašovací údaje virtuálního počítače**, zadejte uživatelské jméno a heslo pro automatickou instalaci služby Mobility při replikaci virtuálních počítačů. Pro **Windows** počítače, účet potřebuje oprávnění místního správce na počítačích, které chcete replikovat. Pro **Linux**, zadejte podrobnosti pro kořenový účet.
8. Vyberte **Dokončit konfiguraci** a dokončete registraci.
9. Po dokončení registrace otevřete Azure portal, zkontrolujte, že konfigurační server a VMware server jsou uvedené na **trezor služby Recovery Services** > **spravovat**  >  **Infrastruktura site Recovery** > **konfigurační servery**.

## <a name="faq"></a>Nejčastější dotazy

1. Můžete použít virtuální počítač, kde je nainstalován konfigurační server pro různé účely?

    **Ne**, doporučujeme použít virtuální počítač pro jediným účelem konfiguračního serveru. Postupujte podle všechny specifikace podle [předchozí části](vmware-azure-deploy-configuration-server.md#Prerequisites) pro efektivní správu zotavení po havárii.
2. Můžete přepnout v úložišti už zaregistrovaný na konfiguračním serveru pomocí nově vytvořeného trezoru?

    **Ne**, jakmile se trezor zaregistrován u konfiguračního serveru, nejde změnit.
3. Můžete použít stejný konfigurační server pro ochranu fyzických a virtuálních počítačů?

    **Ano**, lze použít stejný konfigurační server pro replikaci fyzických a virtuálních počítačů. Nicméně by bylo možné provést fyzický počítač zpět jenom pro virtuální počítač VMware.
4. Jaký je účel konfigurační server a kde se používá?

    Přečtěte si naše architektura Azure Site Recovery [tady](vmware-azure-architecture.md) získat další informace o konfiguračním serveru a jeho funkcí poskytujete.
5. Kde najdete nejnovější verzi konfiguračního serveru?

    Přečtěte si článek na kroky pro upgrade konfiguračního serveru [prostřednictvím portálu](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Můžete ji také přímo stáhnout [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
6. Kde lze stáhnout přístupové heslo pro konfigurační server?

    Odkazovat na [v tomto článku](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) stáhnout přístupové heslo.
7. Kde lze stáhnout registrační klíče trezoru?

    V **trezor služby Recovery Services**, **spravovat** > **infrastruktura Site Recovery** > **konfigurační servery**. Na serverech, vyberte **stáhnout registrační klíč** ke stažení souboru s přihlašovacími údaji.

## <a name="upgrade-the-configuration-server"></a>Upgradujte konfigurační server

Konfigurační server upgradovat na nejnovější verzi, přečtěte si uvedeného postupu [zde](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)

## <a name="troubleshoot-deployment-issues"></a>Řešení problémů při nasazování

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Další postup

Nastavení zotavení po havárii [virtuálních počítačů VMware](vmware-azure-tutorial.md) do Azure.
