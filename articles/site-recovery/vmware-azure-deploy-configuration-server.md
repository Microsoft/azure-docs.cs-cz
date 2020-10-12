---
title: Nasazení konfiguračního serveru v Azure Site Recovery
description: Tento článek popisuje, jak nasadit konfigurační server pro zotavení po havárii VMware pomocí Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: f6c47d4cbfe6311333d95b07c0553afa2b3bb15c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287739"
---
# <a name="deploy-a-configuration-server"></a>Nasazení konfiguračního serveru

Místní konfigurační server nasadíte při použití [Azure Site Recovery](site-recovery-overview.md) k zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure. Konfigurační server koordinuje komunikaci mezi místními VMware a Azure. Také spravuje replikaci dat. Tento článek vás provede kroky potřebnými k nasazení konfiguračního serveru při replikaci virtuálních počítačů VMware do Azure. Pokud potřebujete nastavit konfigurační server pro replikaci fyzického serveru, přečtěte si téma [nastavení konfiguračního serveru pro zotavení po havárii fyzických serverů do Azure](physical-azure-set-up-source.md).

> [!TIP]
> Další informace o roli konfiguračního serveru v rámci architektury Azure Site Recovery najdete v tématu [Architektura zotavení po havárii z VMware do Azure](vmware-azure-architecture.md).

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Nasazení konfiguračního serveru prostřednictvím šablony vajíček

Konfigurační server musí být nastavený jako vysoce dostupný virtuální počítač VMware s určitým minimálním požadavky na hardware a velikost. Pro pohodlné a snadné nasazení Site Recovery poskytuje šablonu aplikace Open Virtualization Virtualization (vajíček), která umožňuje nastavit konfigurační server, který splňuje všechny požadavky, které jsou tady uvedené.

## <a name="prerequisites"></a>Požadavky

Minimální požadavky na hardware pro konfigurační server jsou shrnuté v následujících oddílech.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Požadavky na oprávnění Azure Active Directory

K registraci konfiguračního serveru pomocí služeb Azure Site Recovery musíte mít v Azure Active Directory (Azure AD) uživatel s jednou z následujících oprávnění.

1. Aby uživatel mohl vytvořit aplikaci, musí mít roli vývojář aplikace.
    - Pokud se chcete ověřit, přihlaste se k Azure Portal.</br>
    - Přejít na **Azure Active Directory**  >  **rolí a správců**.</br>
    - Ověřte, zda je role vývojář aplikace přiřazena uživateli. Pokud ne, použijte uživatele s tímto oprávněním nebo se obraťte na [správce, aby oprávnění povolil](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md#assign-roles).
    
2. Pokud se role vývojáře aplikace nedá přiřadit, ujistěte se, že příznak **Uživatelé můžou registrovat aplikace** nastavené na **hodnotu true** , aby uživatel mohl vytvořit identitu. Povolení těchto oprávnění:
    - Přihlaste se k portálu Azure.
    - Přejít na **Azure Active Directory**  >  **nastavení uživatele**.
    - V **App registrations**části registrace aplikací **můžou uživatelé registrovat aplikace**, vyberte **Ano**.

      ![AD_application_permission Azure](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory Federation Services (AD FS) se *nepodporuje*. Použijte účet spravovaný prostřednictvím [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="download-the-template"></a>Stažení šablony

1. V trezoru pokračujte na **Příprava**  >  **zdroje**infrastruktury.
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte si šablonu vajíček pro konfigurační server.

   > [!TIP]
   >Nejnovější verzi šablony konfiguračního serveru si můžete stáhnout také přímo z [webu služby Stažení softwaru](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> Licence, která je součástí šablony vajíček, je zkušební licence, která je platná 180 dní. Po uplynutí této doby musíte licenci zakoupit.

## <a name="import-the-template-in-vmware"></a>Import šablony do VMware

1. Přihlaste se k serveru VMware vCenter nebo vSphere hostitele ESXi pomocí klienta VMware vSphere.
2. V nabídce **soubor** vyberte **nasadit šablonu OVF** a spusťte průvodce **nasazením šablony OVF** .

     ![Nasadit šablonu OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. V části **Select source** (Vybrat zdroj) zadejte umístění staženého souboru OVF.
4. V části **Review details** (Kontrola podrobností) vyberte **Next** (Další).
5. V části **Select name and folder** (Vybrat název a složku) a **Select configuration** (Vybrat konfiguraci) přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon. Použití možnosti dynamického zajišťování může mít vliv na výkon konfiguračního serveru.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. V části **Ready to complete** (Připraveno k dokončení):

    * Pokud chcete nastavit virtuální počítač s výchozím nastavením, vyberte **po dokončení nasazení možnost zapnout**  >  **Finish**.
    * Pokud chcete přidat další síťové rozhraní, zrušte **po nasazení možnost zapnout**a pak vyberte **Dokončit**. Ve výchozím nastavení se šablona konfiguračního serveru nasadí s jedním síťovým adaptérem. Po nasazení můžete přidat další síťové adaptéry.

> [!IMPORTANT]
> Neměňte konfigurace prostředků, jako jsou paměť, jádra a omezení procesoru, nebo upravte nebo odstraňte nainstalované služby nebo soubory na konfiguračním serveru po nasazení. Tyto typy změn ovlivňují registraci konfiguračního serveru se službami Azure a výkonem konfiguračního serveru.

## <a name="add-an-additional-adapter"></a>Přidání dalšího adaptéru

> [!NOTE]
> Pokud máte v plánu zachovat IP adresy zdrojových počítačů při převzetí služeb při selhání a chcete se později vrátit do místního prostředí, potřebujete dvě síťové karty. Jedna síťová karta je připojená ke zdrojovým počítačům a druhá síťová karta se používá pro připojení k Azure.

Pokud chcete přidat další síťovou kartu ke konfiguračnímu serveru, přidejte ji před registrací serveru v trezoru. Jakmile ho zaregistrujete, další adaptéry nebudete moct přidat.

1. V inventáři konzole vSphere Client klikněte pravým tlačítkem na virtuální počítač a vyberte **Edit Settings** (Upravit nastavení).
2. V části **Hardware** vyberte **Add** (Přidat) > **Ethernet Adapter** (Adaptér sítě Ethernet). Pak vyberte **Další**.
3. Vyberte typ adaptéru a síť.
4. Pokud chcete připojit virtuální síťovou kartu, když je virtuální počítač zapnutý, vyberte **připojit se v Power-on**. Pak vyberte **Další**  >  **Dokončit**  >  **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registrace konfiguračního serveru pomocí služby Azure Site Recovery Services

1. Z konzoly klienta VMware vSphere zapněte virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce.
4. Při prvním přihlášení se spustí nástroj pro konfiguraci Azure Site Recovery, a to během několika sekund.
5. Zadejte název, pod kterým se konfigurační server zaregistruje do služby Site Recovery. Pak vyberte **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure.</br>
    a. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.</br>
    b. Ujistěte se, že zvolený uživatelský účet má oprávnění k vytvoření aplikace v Azure. Pokud chcete povolit požadovaná oprávnění, postupujte podle pokynů v části [Azure Active Directory požadavky na oprávnění](#azure-active-directory-permission-requirements).
7. Nástroj provede několik konfiguračních úloh a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Průvodce správou konfiguračního serveru se spustí automaticky během několika sekund.

### <a name="configure-settings"></a>Konfigurace nastavení

1. V Průvodci správou konfiguračního serveru vyberte **nastavení připojení**. V rozevíracích seznamech nejdřív Vyberte síťovou kartu, kterou používá integrovaný procesový Server pro zjišťování a nabízenou instalaci služby mobility na zdrojové počítače. Pak vyberte síťovou kartu, kterou konfigurační server používá pro připojení k Azure. Vyberte **Uložit**. Po dokončení konfigurace není možné toto nastavení změnit. Neměňte IP adresu konfiguračního serveru. Ujistěte se, že IP adresa přiřazená ke konfiguračnímu serveru je statická IP adresa a ne IP adresa DHCP.
2. V části **vybrat Recovery Services trezor**se přihlaste k Microsoft Azure s přihlašovacími údaji použitými v kroku 6 [registrace konfiguračního serveru pomocí služeb Azure Site Recovery Services](#register-the-configuration-server-with-azure-site-recovery-services).
3. Po přihlášení vyberte své předplatné Azure a příslušnou skupinu prostředků a trezor.

    > [!NOTE]
    > Po registraci není nijak flexibilní měnit trezor služby Recovery Services.
    > Změna trezoru služby Recovery Services vyžaduje zrušení přidružení konfiguračního serveru od aktuálního trezoru a zastavené replikace všech chráněných virtuálních počítačů v konfiguračním serveru. Další informace najdete v tématu [Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. Při **instalaci softwaru třetí strany**:

    |Scénář   |Jak postupovat  |
    |---------|---------|
    |Můžu si MySQL stáhnout a nainstalovat ručně?     |  Ano. Stáhněte si aplikaci MySQL, umístěte ji do složky **C:\Temp\ASRSetup**a pak ji nainstalujte ručně. Jakmile souhlasíte s podmínkami a vyberete **Stáhnout a nainstalovat**, portál už říká, že je *nainstalovaný*. Můžete přejít k dalšímu kroku.       |
    |Můžu se vyhnout stažení MySQL online?     |   Ano. Uložte aplikaci pro instalaci MySQL do složky **C:\Temp\ASRSetup**. Přijměte podmínky, vyberte **Stáhnout a nainstalovat**a portál použije instalační program, který jste přidali k instalaci aplikace. Až se instalace dokončí, přejděte k dalšímu kroku.    |
    |Chci stáhnout a nainstalovat MySQL prostřednictvím Azure Site Recovery.    |  Přijměte licenční smlouvu a vyberte **Stáhnout a nainstalovat**. Až se instalace dokončí, přejděte k dalšímu kroku.       |

5. Před pokračováním ověřte, že je v **nastavení ověřit konfiguraci zařízení**nutné ověřit požadavky.
6. V části **Konfigurace serveru vCenter Server/vSphere ESXi**zadejte plně kvalifikovaný název domény nebo IP adresu serveru vCenter nebo hostitele vSphere, kde se nacházejí virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
7. Zadejte přihlašovací údaje, které bude konfigurační server používat pro připojení k serveru VMware. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **Přidat**  >  **pokračovat**. Přihlašovací údaje, které tady zadáte, se uloží místně.
8. V části **Konfigurovat přihlašovací údaje virtuálního počítače**zadejte uživatelské jméno a heslo virtuálních počítačů, do kterých se během replikace automaticky nainstaluje služba mobility. Pro počítače s **Windows** vyžaduje účet oprávnění místního správce na počítačích, které chcete replikovat. Pro **Linux**zadejte podrobnosti kořenového účtu.
9. Vyberte **Dokončit konfiguraci** a dokončete registraci.
10. Po dokončení registrace otevřete Azure Portal a ověřte, že je konfigurační server a server VMware uvedený v **Recovery Services trezoru**  >  **Správa**  >  **Site Recovery**  >  **konfiguračních serverů**infrastruktury.

## <a name="upgrade-the-configuration-server"></a>Upgrade konfiguračního serveru

Chcete-li upgradovat konfigurační server na nejnovější verzi, přečtěte si téma [Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Pokyny, jak upgradovat všechny součásti Site Recovery, najdete v tématu [aktualizace služby v Site Recovery](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Správa konfiguračního serveru

Aby nedošlo k přerušení probíhající replikace, ujistěte se, že po registraci konfiguračního serveru do trezoru se IP adresa konfiguračního serveru nemění. Další informace o běžných úlohách správy konfiguračního serveru najdete v tématu [Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](vmware-azure-manage-configuration-server.md).

## <a name="troubleshoot-deployment-issues"></a>Řešení problémů při nasazování

Řešení problémů s připojením & najdete v našem [článku o řešení potíží](vmware-azure-troubleshoot-configuration-server.md) .

## <a name="faqs"></a>Nejčastější dotazy

* Jak dlouho je k dispozici licence na konfiguračním serveru nasazeném prostřednictvím OVF? Co se stane, když licenci znovu neaktivuji?

    Licence, která je součástí šablony vajíček, je zkušební licence platná po dobu 180 dnů. Před vypršením platnosti musíte licenci aktivovat. V opačném případě může to vést k častému vypnutí konfiguračního serveru a způsobit překážku pro aktivity replikace. Další informace najdete v tématu [Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](vmware-azure-manage-configuration-server.md#update-windows-license).

* Můžu použít virtuální počítač, na kterém je konfigurační server nainstalovaný pro různé účely?

    Ne. Virtuální počítač používejte výhradně pro účely konfiguračního serveru. Ujistěte se, že se budete řídit všemi specifikacemi uvedenými v [požadavcích](#prerequisites) pro efektivní správu zotavení po havárii.
* Můžu přepnout trezor, který je už zaregistrovaný na konfiguračním serveru pomocí nově vytvořeného trezoru?

    Ne. Po zaregistrování trezoru na konfiguračním serveru ho nelze změnit.
* Můžu použít stejný konfigurační server k ochraně fyzických i virtuálních počítačů?

    Ano. Stejný konfigurační server lze použít pro replikaci fyzických a virtuálních počítačů. Fyzický počítač se však může vrátit zpět pouze k VIRTUÁLNÍmu počítači VMware.
* Jaký je účel konfiguračního serveru a kde se používá?

    Další informace o konfiguračním serveru a jeho funkcích najdete v tématu [Architektura replikace z VMware do Azure](vmware-azure-architecture.md).
* Kde můžu najít nejnovější verzi konfiguračního serveru?

    Postup upgradu konfiguračního serveru prostřednictvím portálu najdete v tématu [upgrade konfiguračního serveru](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Pokyny, jak upgradovat všechny součásti Site Recovery, najdete v tématu [aktualizace služby v Site Recovery](https://aka.ms/asr_how_to_upgrade).
* Kde můžu stáhnout heslo pro konfigurační server?

    Pokud chcete stáhnout heslo, přečtěte si téma [Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).
* Můžu změnit heslo?

    Ne. Neměňte heslo ke konfiguračnímu serveru. Změna hesla přerušuje replikaci chráněných počítačů a vede do kritického stavu.
* Kde můžu stahovat registrační klíče trezoru?

    V **Recovery Services trezoru**vyberte **Spravovat**  >  **Site Recovery**  >  **konfigurační servery**infrastruktury. V části **servery**vyberte **Stáhnout registrační klíč** a Stáhněte si soubor s přihlašovacími údaji trezoru.
* Můžu naklonovat existující konfigurační server a použít ho k orchestraci replikace?

    Ne. Používání klonovaného konfiguračního serveru se nepodporuje. Naklonování procesového serveru se škálováním na více instancí je také nepodporovaný scénář. Klonování Site Recovery komponent má vliv na probíhající replikaci.

* Můžu změnit IP adresu konfiguračního serveru?

    Ne. Neměňte IP adresu konfiguračního serveru. Ujistěte se, že všechny IP adresy přiřazené ke konfiguračnímu serveru jsou statické IP adresy a ne IP adresy DHCP.
* Můžu nastavit konfigurační server v Azure?

    Nastavte konfigurační server v místním prostředí s přímým dohledem v centru a pro minimalizaci latencí přenosu dat. Můžete provést naplánované zálohování konfiguračního serveru pro [účely navrácení služeb po obnovení](vmware-azure-manage-configuration-server.md#failback-requirements).

* Můžu změnit ovladač mezipaměti na konfiguračním serveru nebo procesovém serveru se škálováním na více instancí?

    Ne, ovladač mezipaměti po dokončení instalace nelze změnit.

Další Nejčastější dotazy týkající se konfiguračních serverů najdete v tématu [Nejčastější dotazy ke konfiguračnímu serveru](vmware-azure-common-questions.md#configuration-server).

## <a name="next-steps"></a>Další kroky

Nastavte zotavení po havárii [virtuálních počítačů VMware](vmware-azure-tutorial.md) do Azure.
