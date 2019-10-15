---
title: Nasazení konfiguračního serveru pro zotavení po havárii VMware pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak nasadit konfigurační server pro zotavení po havárii VMware pomocí Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: 5812cc73fb1da58c591d0593e079851e05bd0940
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331956"
---
# <a name="deploy-a-configuration-server"></a>Nasazení konfiguračního serveru

Místní konfigurační server nasadíte při použití [Azure Site Recovery](site-recovery-overview.md) k zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure. Konfigurační server koordinuje komunikaci mezi místními VMware a Azure. Také spravuje replikaci dat. Tento článek vás provede kroky potřebnými k nasazení konfiguračního serveru při replikaci virtuálních počítačů VMware do Azure. Pokud potřebujete nastavit konfigurační server pro replikaci fyzického serveru, [postupujte podle tohoto článku](physical-azure-set-up-source.md) .

> [!TIP]
> O roli konfiguračního serveru se můžete dozvědět jako součást [Azure Site Recovery architektury.](vmware-azure-architecture.md)

## <a name="deployment-of-configuration-server-through-ova-template"></a>Nasazení konfiguračního serveru prostřednictvím šablony sady vajíček

Konfigurační server musí být nastavený jako vysoce dostupný virtuální počítač VMware s určitým minimálním požadavky na hardware a velikost. Pro pohodlné a snadné nasazení Site Recovery poskytuje šablonu pro stažení vajíček (Open Virtualization Application) k nastavení konfiguračního serveru, který splňuje všechny požadavky, které jsou uvedené níže.

## <a name="prerequisites"></a>Předpoklady

Minimální požadavky na hardware pro konfigurační server jsou shrnuté v následující tabulce.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Požadavky na oprávnění Azure Active Directory

Pro registraci konfiguračního serveru pomocí služeb Azure Site Recovery potřebujete uživatele s **jednou z následujících** oprávnění nastaveným v AAD (Azure Active Directory).

1. Uživatel by měl mít roli "vývojář aplikace" k vytvoření aplikace.
   1. Ověření se přihlaste k Azure Portal</br>
   1. Přechod na Azure Active Directory > rolí a správců</br>
   1. Ověřte, zda je uživateli přiřazena role vývojář aplikace. V takovém případě použijte k [povolení oprávnění](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles)uživatele s tímto oprávněním nebo se obraťte na správce.
    
1. Pokud roli vývojář aplikace nelze přiřadit, zajistěte, aby byl příznak "uživatel může registrovat aplikaci" nastaven na hodnotu true, aby uživatel mohl vytvořit identitu. Pokud chcete povolit výše uvedená oprávnění,
   1. Přihlásit se na Azure Portal
   1. Přejít na Azure Active Directory > nastavení uživatele
   1. V části * * Registrace aplikací "," mohou uživatelé registrovat aplikace "musí být zvolena možnost" Ano ".

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory Federation Services (AD FS) (ADFS) se **nepodporuje**. Použijte prosím účet spravovaný prostřednictvím [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

## <a name="capacity-planning"></a>Plánování kapacit

Požadavky na velikost pro konfigurační server závisí na četnosti změn dat. Tuto tabulku použijte jako vodítko.

| **VČETNĚ** | **Rezident** | **Velikost disku mezipaměti** | **Frekvence změny dat** | **Chráněné počítače** |
| --- | --- | --- | --- | --- |
| 8 vCPU (2 sokety × 4 jádra \@ 2,5 GHz) |16 GB |300 GB |500 GB nebo méně |Replikujte méně než 100 počítačů. |
| 12 vCPU (2 sokety × 6 jader \@ 2,5 GHz) |18 GB |600 GB |500 GB až 1 TB |Replikujte 100-150 počítačů. |
| 16 vCPU (2 sokety × 8 jader \@ 2,5 GHz) |32 GB |1 TB |1 TB až 2 TB |Replikujte 150-200 počítačů. |

Pokud provádíte replikaci více než jednoho virtuálního počítače VMware, načtěte si [informace o plánování kapacity](site-recovery-plan-capacity-vmware.md). Spusťte [nástroj Plánovač nasazení](site-recovery-deployment-planner.md) pro replikaci VMware.

## <a name="download-the-template"></a>Stažení šablony

1. V daném trezoru klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte si šablonu Open Application Virtualization (VAJÍČK) pro konfigurační server.

   > [!TIP]
   >Nejnovější verzi šablony konfiguračního serveru si můžete stáhnout také přímo z [webu Microsoft Download Center](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> Licence, která je součástí šablony vajíček, je zkušební licence platná po dobu 180 dnů. Po tomto období musí zákazník aktivovat Windows pomocí předem získané licence.

## <a name="import-the-template-in-vmware"></a>Import šablony do VMware

1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **File** (Soubor) vyberte **Deploy OVF Template** (Nasadit šablonu OVF). Tím spustíte průvodce nasazením šablony OVF.

     ![Šablona OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. V části **Vybrat zdroj**zadejte umístění staženého ovf.
4. V **podrobnostech revize**vyberte **Další**.
5. V části **Vybrat název a složku** a **Vyberte Konfigurace**přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon. Použití možnosti dynamického zajišťování může mít vliv na výkon konfiguračního serveru.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. V části **Ready to complete** (Připraveno k dokončení):

    * Pokud chcete vytvořit virtuální počítač s výchozím nastavením, vyberte **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit).

    * Pokud chcete přidat další síťové rozhraní, zrušte **po nasazení možnost zapnout**a pak vyberte **Dokončit**. Ve výchozím nastavení se šablona konfiguračního serveru nasadí s jedním síťovým adaptérem. Po nasazení můžete přidat další síťové adaptéry.

> [!IMPORTANT]
> Neměňte konfigurace prostředků (paměť/jádra/omezení procesoru), upravte/odstraňte nainstalované služby nebo soubory na konfiguračním serveru po nasazení. To bude mít vliv na registraci konfiguračního serveru se službami Azure a výkonem konfiguračního serveru.

## <a name="add-an-additional-adapter"></a>Přidání dalšího adaptéru

> [!NOTE]
> Pokud plánujete zachovat IP adresy zdrojových počítačů při převzetí služeb při selhání a chcete se později vrátit k místnímu navrácení služeb po obnovení do místního prostředí, jsou vyžadovány dva síťové adaptéry. Ke zdrojovému počítači bude připojeno jedno síťové rozhraní a jiné síťové rozhraní se bude používat pro připojení k Azure.

Pokud chcete přidat další síťovou kartu ke konfiguračnímu serveru, přidejte ji před registrací serveru v trezoru. Jakmile ho zaregistrujete, další adaptéry nebudete moct přidat.

1. V inventáři konzole vSphere Client klikněte pravým tlačítkem na virtuální počítač a vyberte **Edit Settings** (Upravit nastavení).
2. V části **Hardware** vyberte **Add** (Přidat) > **Ethernet Adapter** (Adaptér sítě Ethernet). Pak vyberte **Další**.
3. Vyberte typ adaptéru a síť.
4. Pokud chcete připojit virtuální síťovou kartu, když je virtuální počítač zapnutý, vyberte **připojit se v Power-on**. Pak vyberte **další** > **Dokončit** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registrace konfiguračního serveru pomocí služby Azure Site Recovery Services

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce.
4. Při prvním přihlášení se spustí nástroj pro konfiguraci Azure Site Recovery, a to během několika sekund.
5. Zadejte název, pod kterým se konfigurační server zaregistruje do služby Site Recovery. Pak vyberte **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure.</br>
    a. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.</br>
    b. Ujistěte se, že zvolený uživatelský účet má oprávnění k vytvoření aplikace v Azure. Pokud chcete povolit požadovaná oprávnění, postupujte podle pokynů uvedených [tady](#azure-active-directory-permission-requirements).
7. Nástroj provede několik konfiguračních úloh a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Průvodce správou konfiguračního serveru se spustí **automaticky** během několika sekund.

### <a name="configure-settings"></a>Konfigurace nastavení

1. V Průvodci správou konfiguračního serveru vyberte **nastavení připojení**. V rozevíracích seznamech vyberte nejdřív síťovou kartu, kterou používá integrovaný procesový Server pro zjišťování a nabízenou instalaci služby mobility na zdrojových počítačích, a pak vyberte síťovou kartu, kterou konfigurační server používá pro připojení k Azure. Potom vyberte **Uložit**. Po nakonfigurování tohoto nastavení už toto nastavení nemůžete změnit. Důrazně doporučujeme, abyste nezměnili IP adresu konfiguračního serveru. Ujistěte se, že IP adresa přiřazená ke konfiguračnímu serveru je statická IP adresa a nikoli IP adresa DHCP.
2. V části **vybrat Recovery Services trezor**se přihlaste k Microsoft Azure s přihlašovacími údaji použitými v **kroku 6** "[registrace konfiguračního serveru pomocí služeb Azure Site Recovery Services](#register-the-configuration-server-with-azure-site-recovery-services)".
3. Po přihlášení vyberte své předplatné Azure a příslušnou skupinu prostředků a trezor.

    > [!NOTE]
    > Po registraci není nijak flexibilní měnit trezor služby Recovery Services.
    > Změna trezoru služby Recovery Services by vyžadovala zrušení přidružení konfiguračního serveru od aktuálního trezoru a zastavila se replikace všech chráněných virtuálních počítačů v konfiguračním serveru. Další [informace](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)

4. V **instalaci softwaru třetí strany**

    |Scénář   |Postup při sledování  |
    |---------|---------|
    |Je možné stáhnout & instalaci MySQL ručně?     |  Ano. Stáhněte si aplikaci MySQL & umístěte ji do složky **C:\Temp\ASRSetup**a pak ji nainstalujte ručně. Když teď souhlasíte s podmínkami > klikněte na **Stáhnout a nainstalovat**, portál už říká, že je *nainstalovaný*. Můžete přejít k dalšímu kroku.       |
    |Můžu se vyhnout stažení MySQL online?     |   Ano. Uložte aplikaci pro instalaci MySQL do složky **C:\Temp\ASRSetup**. Přijměte podmínky > klikněte na **Stáhnout a nainstalovat**, portál použije instalační program, který jste přidali, a nainstaluje aplikaci. Můžete přejít k dalšímu kroku po instalaci.    |
    |Chci stáhnout & instalovat MySQL prostřednictvím Azure Site Recovery     |  Přijměte licenční smlouvu & klikněte na **Stáhnout a nainstalovat**. Potom můžete přejít k dalšímu kroku po instalaci.       |

5. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
6. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
7. Zadejte přihlašovací údaje, které bude konfigurační server používat pro připojení k serveru VMware. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **Přidat**a pak **pokračovat**. Přihlašovací údaje, které tady zadáte, se uloží místně.
8. V části **Konfigurovat přihlašovací údaje virtuálního počítače**zadejte uživatelské jméno a heslo virtuálních počítačů pro automatickou instalaci služby mobility během replikace. Pro počítače s **Windows** vyžaduje účet oprávnění místního správce na počítačích, které chcete replikovat. Pro **Linux**zadejte podrobnosti kořenového účtu.
9. Vyberte **Dokončit konfiguraci** a dokončete registraci.
10. Po dokončení registrace otevřete Azure Portal, ověřte, že je konfigurační server a server VMware uvedený v **Recovery Services trezoru** > **Správa** > **Site Recovery infrastruktura**@no__t **-5. Servery**.

## <a name="upgrade-the-configuration-server"></a>Upgrade konfiguračního serveru

Chcete-li upgradovat konfigurační server na nejnovější verzi, postupujte podle těchto [kroků](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Podrobné pokyny, jak upgradovat všechny součásti Site Recovery, získáte kliknutím [sem](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Správa konfiguračního serveru

Aby nedošlo k přerušení probíhající replikace, zajistěte, aby se po registraci konfiguračního serveru do trezoru nezměnila IP adresa konfiguračního serveru. Další informace o běžných úlohách správy konfiguračního serveru najdete [tady](vmware-azure-manage-configuration-server.md).

## <a name="faq"></a>Časté otázky

1. Jak dlouho je licence poskytovaná na konfiguračním serveru nasazeném prostřednictvím OVF platná? Co se stane, když licenci znovu neaktivuji?

    Licence, která je součástí šablony vajíček, je zkušební licence platná po dobu 180 dnů. Před vypršením platnosti musíte licenci aktivovat. V opačném případě to může vést k častému vypnutí konfiguračního serveru, což by způsobilo překážku aktivit replikace.

2. Můžu použít virtuální počítač, na kterém je nainstalovaný konfigurační server, pro různé účely?

    **Ne**, doporučujeme použít virtuální počítač pro výhradní účely konfiguračního serveru. Ujistěte se, že dodržujete všechny specifikace uvedené v [požadavcích](#prerequisites) pro efektivní správu zotavení po havárii.
3. Můžu přepnout trezor, který je už zaregistrovaný na konfiguračním serveru pomocí nově vytvořeného trezoru?

    **Ne**. Pokud je trezor zaregistrovaný na konfiguračním serveru, nedá se změnit.
4. Můžu použít stejný konfigurační server pro ochranu fyzických i virtuálních počítačů?

    **Ano**, stejný konfigurační server lze použít pro replikaci fyzických a virtuálních počítačů. Fyzický počítač se ale může vrátit zpátky jenom na virtuální počítač VMware.
5. Jaký je účel konfiguračního serveru a kde se používá?

    Další informace o konfiguračním serveru a jeho funkcích najdete v tématu [Architektura replikace z VMware do Azure](vmware-azure-architecture.md) .
6. Kde můžu najít nejnovější verzi konfiguračního serveru?

    Postup upgradu konfiguračního serveru prostřednictvím portálu najdete v tématu [upgrade konfiguračního serveru](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Podrobné pokyny, jak upgradovat všechny součásti Site Recovery, najdete [tady](https://aka.ms/asr_how_to_upgrade).
7. Kde můžu stáhnout heslo pro konfigurační server?

    Pokud si chcete stáhnout heslo, přečtěte si [Tento článek](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) .
8. Můžu změnit heslo?

    **Ne**, **důrazně doporučujeme, abyste heslo konfiguračního serveru nezměnili** . Změna v heslech: přerušení replikace chráněných počítačů a vede do kritického stavu.
9. Kde můžu stahovat registrační klíče trezoru?

    V **trezoru Recovery Services** **spravujte** **konfigurační servery** > **Site Recovery infrastruktury**@no__t 4. V části servery vyberte **Stáhnout registrační klíč** a Stáhněte si soubor s přihlašovacími údaji trezoru.
10. Můžu naklonovat existující konfigurační server a použít ho k orchestraci replikace?

    **Ne**, použití klonované součásti konfiguračního serveru se nepodporuje. Klonování procesového serveru se škálováním na více instancí je také nepodporovaný scénář. Klonování Site Recovery komponent má vliv na probíhající replikaci.

11. Můžu změnit IP adresu konfiguračního serveru?

    **Ne**, důrazně doporučujeme, abyste nezměnili IP adresu konfiguračního serveru. Ujistěte se, že všechny IP adresy přiřazené ke konfiguračnímu serveru jsou statické IP adresy a ne IP adresy DHCP.
12. Můžu nastavit konfigurační server v Azure?

    Doporučuje se nastavit konfigurační server v místním prostředí s přímým dohledem v centru a minimalizovat latence přenosu dat. Můžete provést naplánované zálohování konfiguračního serveru pro [účely navrácení služeb po obnovení](vmware-azure-manage-configuration-server.md#failback-requirements).

Další Nejčastější dotazy týkající se konfiguračního serveru najdete v naší [dokumentaci k častým otázkám konfiguračního serveru](vmware-azure-common-questions.md#configuration-server) .

## <a name="troubleshoot-deployment-issues"></a>Řešení problémů při nasazování

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Další kroky

Nastavte zotavení po havárii [virtuálních počítačů VMware](vmware-azure-tutorial.md) do Azure.
