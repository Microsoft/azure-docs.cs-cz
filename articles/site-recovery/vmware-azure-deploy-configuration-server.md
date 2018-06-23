---
title: Nasazení se konfigurační server pro obnovení po havárii VMware s Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak nasadit konfigurační server pro obnovení po havárii VMware s Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/06/2018
ms.author: raynew
ms.openlocfilehash: 841176d8c5f215d18edf25b1f191792b37555fa9
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318115"
---
# <a name="deploy-a-configuration-server"></a>Nasazení konfiguračního serveru

Při použití se nasadit server konfigurace místní [Azure Site Recovery](site-recovery-overview.md) pro zotavení po havárii virtuálních počítačů VMware a fyzické servery do Azure. Konfigurace serveru souřadnice komunikace mezi místními VMware a Azure. Spravuje taky data replikace. Tento článek vás provede kroky potřebné k nasazení konfigurační server při replikaci virtuálních počítačů VMware do Azure. [Postupujte podle tohoto článku](physical-azure-set-up-source.md) Pokud budete potřebovat k nastavení konfigurace serveru pro replikaci fyzický server.

>[!TIP]
Informace o roli konfigurační server jako součást architektury Azure Site Recovery najdete [zde](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Nasazení konfigurační server prostřednictvím vajíčka šablony

Konfigurace server musí být nastavený jako vysoce dostupný virtuální počítač VMware s určité minimální požadavky na hardware a nastavení velikosti. Pro nasazení pohodlný a snadný Site Recovery poskytuje šablonu vajíčka (Open Virtualization aplikace) ke stažení pro nastavení konfigurace serveru, který splňuje všechny níže uvedené mandátem požadavky.

## <a name="prerequisites"></a>Požadavky

Minimální požadavky na hardware pro server konfigurace jsou shrnuty v následující tabulce.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="capacity-planning"></a>Plánování kapacity

Nastavení velikosti požadavky pro konfiguraci serveru závisí na potenciální míry změny dat. Tuto tabulku použijte jako vodítko.

| **CPU** | **Paměť** | **Velikost disku mezipaměti** | **Míry změny dat** | **Chráněné počítače** |
| --- | --- | --- | --- | --- |
| 8 Vcpu (2 sockets * @ 2,5 GHz 4 jádra) |16 GB |300 GB |500 GB nebo méně |Replikovat počítače méně než 100. |
| 12 Vcpu (2 sockets * @ 2,5 GHz 6 jader) |18 GB |600 GB |500 GB až 1 TB |Replikovat počítače 100 150. |
| 16 Vcpu (2 sockets * @ 2,5 GHz 8 jader) |32 GB |1 TB |1 TB 2 TB |Replikovat počítače 150 až 200. |

Pokud replikujete více než jeden virtuální počítač VMware, přečtěte si [informace o plánování kapacity](/site-recovery-plan-capacity-vmware.md). Spustit [nástroj pro nasazení planner](site-recovery-deployment-planner.md) pro replikaci VMWare.

## <a name="download-the-template"></a>Stažení šablony

1. V daném trezoru klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte si šablonu otevřete virtualizace aplikace (vajíčka) pro konfigurační server.

  > [!TIP]
>Můžete také stáhnout nejnovější verzi šablony serveru konfigurace přímo z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

>[!NOTE]
Licence, které jsou součástí šablony vajíčka je zkušební licence, která je platná pro 180 dní. Po této doby zákazník musí aktivovat systém windows s opatřené licencí.

## <a name="import-the-template-in-vmware"></a>Import šablony do VMware

1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **File** (Soubor) vyberte **Deploy OVF Template** (Nasadit šablonu OVF). Tím spustíte průvodce nasazením šablony OVF.

     ![Šablona OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. V **zvolit zdroj**, zadejte umístění stažené OVF.
4. V **podrobnosti**, vyberte **Další**.
5. V **vybrat název a složku** a **vyberte konfigurace**, přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. V části **Ready to complete** (Připraveno k dokončení):

    * Pokud chcete vytvořit virtuální počítač s výchozím nastavením, vyberte **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit).

    * Chcete-li přidat další síťové rozhraní, zrušte **zapnout po nasazení**a potom vyberte **Dokončit**. Ve výchozím nastavení se šablona konfiguračního serveru nasadí s jedním síťovým adaptérem. Po nasazení můžete přidat další síťové adaptéry.

## <a name="add-an-additional-adapter"></a>Přidání dalšího adaptéru

Pokud chcete přidat další síťový adaptér na konfiguračním serveru, přidejte ji předtím, než zaregistrujete server v trezoru. Jakmile ho zaregistrujete, další adaptéry nebudete moct přidat.

1. V inventáři konzole vSphere Client klikněte pravým tlačítkem na virtuální počítač a vyberte **Edit Settings** (Upravit nastavení).
2. V části **Hardware** vyberte **Add** (Přidat) > **Ethernet Adapter** (Adaptér sítě Ethernet). Pak vyberte **Další**.
3. Vyberte typ adaptéru a síť. 
4. Chcete-li připojit virtuální síťovou kartu, pokud je virtuální počítač je zapnutá, vyberte **Connect v zapnutí**. Potom vyberte **Další** > **Dokončit** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Zaregistrujte konfigurační server službou Azure Site Recovery

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce.
4. Spustí se při prvním přihlášení, během několika sekund nástroje Konfigurace obnovení lokality Azure.
5. Zadejte název, pod kterým se konfigurační server zaregistruje do služby Site Recovery. Pak vyberte **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.
7. Nástroj provede několik konfiguračních úloh a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Spustí Průvodce konfigurací serveru správy **automaticky** v několik sekund.

### <a name="configure-settings"></a>Konfigurace nastavení

1. V průvodci správou konfiguračního serveru vyberte **Nastavit připojení** a pak vyberte síťový adaptér, přes který bude procesový server přijímat provoz replikace z virtuálních počítačů. Potom vyberte **Uložit**. Toto nastavení nelze změnit po dokončení své konfigurace.
2. V **trezor služeb zotavení vyberte**, přihlaste se k Microsoft Azure, vyberte předplatné Azure a příslušnou skupinu zdrojů a trezoru.
    >[!NOTE]
    > Po registraci neexistuje žádné flexibilně změnit trezoru služeb zotavení.
3. V **nainstalovat software třetích stran**,

    |Scénář   |Postup  |
    |---------|---------|
    |Může stáhnout a nainstalovat MySQL ručně?     |  Ano. & Stáhnout aplikaci MySQL umístěte do složky **C:\Temp\ASRSetup**, pak nainstalovat ručně. Teď, když je přijmout podmínky > klikněte na **stáhněte a nainstalujte**, uvádí portálu *již nainstalována*. Můžete pokračovat k dalšímu kroku.       |
    |Můžete vyhnout stahování MySQL online?     |   Ano. Instalační program aplikace MySQL umístěte do složky **C:\Temp\ASRSetup**. Přijměte podmínky > klikněte na **stáhněte a nainstalujte**, portál použije instalační program, které jste přidali a nainstaluje aplikaci. Můžete pokračovat na další krok po instalaci.    |
    |Chci stáhnout a nainstalovat MySQL prostřednictvím Azure Site Recovery     |  Přijměte licenční smlouvu a klikněte na **stáhněte a nainstalujte**. Pak můžete pokračovat na další krok po instalaci.       |
4. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
5. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
6. Zadejte přihlašovací údaje, které bude konfigurační server používat pro připojení k serveru VMware. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **přidat**a potom **pokračovat**. Pověření zadaná v tomto poli se místně uloží.
7. V **konfigurace virtuálního počítače pověření**, zadejte uživatelské jméno a heslo virtuálních počítačů můžete automaticky nainstalovat službu Mobility během replikace. Pro **Windows** počítače, že účet potřebuje oprávnění místního správce na počítačích, které chcete replikovat. Pro **Linux**, zadejte podrobnosti pro kořenový účet.
8. Vyberte **Dokončit konfiguraci** a dokončete registraci.
9. Po dokončení registrace, otevřete portál Azure, zkontrolujte, že konfigurační server a VMware server jsou uvedené **trezoru služeb zotavení** > **spravovat**  >  **Infrastruktury obnovení lokality** > **konfigurační servery**.

## <a name="faq"></a>Nejčastější dotazy

1. Můžete použít virtuální počítač, kde je nainstalován konfigurační server pro různé účely? **Ne**, konfigurační server musí být server jedinému účelu a použití jako sdílený server není podporováno.
2. Můžete přepnout trezoru již zaregistrován v konfigurační server s nově vytvořený trezoru? **Ne**, jakmile trezoru je zaregistrován k serveru configuration, nelze změnit.
3. Můžete použít stejný konfigurační server pro ochranu fyzické a virtuální počítače? **Ano**, stejný konfigurační server lze použít pro replikaci fyzické a virtuální počítače. Však není podporováno navrácení služeb po obnovení do fyzického počítače.
4. Kde se použije konfigurační server? Odkazovat na naše architektura Azure Site Recovery [sem](vmware-azure-architecture.md) Další informace o konfiguraci serveru a jeho funkce.
5. Kde nejnovější verze konfigurace serveru Můžete stáhnout přímo z [Microsoft Download Center](https://aka.ms/asrconfigurationserver). Přečtěte si článek na postup upgradu konfigurační server [zde](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).

## <a name="upgrade-the-configuration-server"></a>Upgrade na konfiguračním serveru

Konfigurační server upgradovat na nejnovější verzi, přečtěte si téma kroky uvedené v [sem](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)

## <a name="troubleshoot-deployment-issues"></a>Řešení problémů při nasazování

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Další postup

Nastavit zotavení po havárii [virtuální počítače VMware](vmware-azure-tutorial.md) do Azure.
