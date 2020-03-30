---
title: Nasazení konfiguračního serveru v azure site recovery
description: Tento článek popisuje, jak nasadit konfigurační server pro zotavení po havárii společnosti VMware pomocí azure site recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: 5209dab5e0934cc98bb1334a1565cc13998a7d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257378"
---
# <a name="deploy-a-configuration-server"></a>Nasazení konfiguračního serveru

Místní konfigurační server nasadíte, když do Azure použijete [Azure Site Recovery](site-recovery-overview.md) pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů. Konfigurační server koordinuje komunikaci mezi místním vmware a Azure. Spravuje také replikaci dat. Tento článek vás provede kroky potřebné k nasazení konfiguračního serveru při replikaci virtuálních počítačů VMware do Azure. Pokud potřebujete nastavit konfigurační server pro replikaci fyzického serveru, přečtěte si informace [o nastavení konfiguračního serveru pro zotavení fyzických serverů po havárii do Azure](physical-azure-set-up-source.md).

> [!TIP]
> Informace o roli konfiguračního serveru jako součást architektury Azure Site Recovery najdete [v tématu Architektura zotavení po havárii v systému VMware na Azure](vmware-azure-architecture.md).

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Nasazení konfiguračního serveru pomocí šablony OVA

Konfigurační server musí být nastaven jako vysoce dostupný virtuální vdom VMware s určitými minimálními požadavky na hardware a změnu velikosti. Pro pohodlné a snadné nasazení poskytuje site recovery šablonu open virtualizační aplikace (OVA) ke stažení pro nastavení konfiguračního serveru, který splňuje všechny zde uvedené požadavky.

## <a name="prerequisites"></a>Požadavky

Minimální požadavky na hardware pro konfigurační server jsou shrnuty v následujících částech.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Požadavky na oprávnění služby Azure Active Directory

Chcete-li zaregistrovat konfigurační server pomocí služeb Azure Site Recovery, musíte mít ve službě Azure Active Directory (Azure AD) nastaveného uživatele s jedním z následujících oprávnění.

1. Uživatel musí mít roli vývojáře aplikace k vytvoření aplikace.
    - Pokud to ověříte, přihlaste se k portálu Azure.</br>
    - Přejděte na**role a správce služby** **Azure Active Directory** > .</br>
    - Ověřte, zda je uživateli přiřazena role vývojáře aplikace. Pokud ne, použijte uživatele s tímto oprávněním nebo požádejte [správce, aby oprávnění povolil](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
2. Pokud roli vývojáře aplikace nelze přiřadit, ujistěte se, že **uživatelé mohou zaregistrovat aplikace** příznak je nastaven jako **true** pro uživatele k vytvoření identity. Povolení těchto oprávnění:
    - Přihlaste se k portálu Azure.
    - Přejděte na**nastavení uživatele** **služby Azure Active Directory** > .
    - V části **Registrace aplikací** **mohou uživatelé registrace aplikací registrovat**vyberte **Ano**.

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Služba AD FS Není *podporována*. Použijte účet spravovaný prostřednictvím [služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

## <a name="download-the-template"></a>Stažení šablony

1. V trezoru přejděte na připravit**zdroj** **infrastruktury** > .
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte si šablonu OVA pro konfigurační server.

   > [!TIP]
   >Nejnovější verzi šablony konfiguračního serveru si můžete stáhnout také přímo ze [služby Stažení softwaru](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> Licence poskytnutá se šablonou OVA je zkušební licence platná po dobu 180 dnů. Po uplynutí této doby musíte získat licenci.

## <a name="import-the-template-in-vmware"></a>Import šablony do VMware

1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **Soubor** vyberte **nasadit šablonu OVF** a spusťte Průvodce **nasazením šablony OVF.**

     ![Nasazení šablony OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. V části **Select source** (Vybrat zdroj) zadejte umístění staženého souboru OVF.
4. V části **Review details** (Kontrola podrobností) vyberte **Next** (Další).
5. V části **Select name and folder** (Vybrat název a složku) a **Select configuration** (Vybrat konfiguraci) přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon. Použití možnosti thin zřizování může ovlivnit výkon konfiguračního serveru.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. V části **Ready to complete** (Připraveno k dokončení):

    * Pokud chcete nastavit virtuální počítač s výchozím nastavením, vyberte **Zapnout po dokončení nasazení** > **Finish**.
    * Chcete-li přidat další síťové rozhraní, zrušte zaškrtnutí **políčka Zapnout po nasazení**a vyberte příkaz **Dokončit**. Ve výchozím nastavení se šablona konfiguračního serveru nasadí s jedním síťovým adaptérem. Po nasazení můžete přidat další síťové adaptéry.

> [!IMPORTANT]
> Neměňte konfigurace prostředků, jako je paměť, jádra a omezení procesoru, ani po nasazení neupravujte ani neodstraňujte nainstalované služby nebo soubory na konfiguračním serveru. Tyto typy změn ovlivňují registraci konfiguračního serveru se službami Azure a výkon konfiguračního serveru.

## <a name="add-an-additional-adapter"></a>Přidání dalšího adaptéru

> [!NOTE]
> Dvě síťové karty jsou vyžadovány, pokud plánujete zachovat IP adresy zdrojových počítačů při převzetí služeb při selhání a chcete později znovu postoupit do místního prostředí. Jedna nic je připojena ke zdrojovým počítačům a druhá nic se používá pro připojení Azure.

Pokud chcete na konfigurační server přidat další nic, přidejte ji před registrací serveru v úschovně. Jakmile ho zaregistrujete, další adaptéry nebudete moct přidat.

1. V inventáři konzole vSphere Client klikněte pravým tlačítkem na virtuální počítač a vyberte **Edit Settings** (Upravit nastavení).
2. V části **Hardware** vyberte **Add** (Přidat) > **Ethernet Adapter** (Adaptér sítě Ethernet). Pak vyberte **Další**.
3. Vyberte typ adaptéru a síť.
4. Pokud chcete virtuální nic připojit, když je virtuální počítač zapnutý, vyberte **Připojit při zapnutí**. Pak vyberte **Další** > **dokončení** > **v pořádku**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registrace konfiguračního serveru pomocí služeb Azure Site Recovery

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do instalačního prostředí Windows Serveru 2016. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce.
4. Při prvním přihlášení se během několika sekund spustí nástroj Azure Site Recovery Configuration.
5. Zadejte název, pod kterým se konfigurační server zaregistruje do služby Site Recovery. Pak vyberte **Další**.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure.</br>
    a. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.</br>
    b. Ujistěte se, že vybraný uživatelský účet má oprávnění k vytvoření aplikace v Azure. Chcete-li povolit požadovaná oprávnění, postupujte podle pokynů v části [Požadavky na oprávnění služby Azure Active Directory](#azure-active-directory-permission-requirements).
7. Nástroj provede několik konfiguračních úloh a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Průvodce správou konfiguračního serveru se spustí automaticky během několika sekund.

### <a name="configure-settings"></a>Konfigurace nastavení

1. V Průvodci správou konfiguračního serveru vyberte **možnost Nastavení připojení**. Z rozevíracích seznamů nejprve vyberte nic, kterou integrovaný procesní server používá pro zjišťování a nabízenou instalaci služby mobility na zdrojových počítačích. Pak vyberte nic, které konfigurační server používá pro připojení k Azure. Vyberte **Uložit**. Po dokončení konfigurace není možné toto nastavení změnit. Neměňte IP adresu konfiguračního serveru. Ujistěte se, že adresa IP přiřazená konfiguračnímu serveru je statická adresa IP, nikoli adresa DHCP.
2. V **trezoru Select Recovery Services**se přihlaste k Microsoft Azure pomocí přihlašovacích údajů použitých v kroku 6 registrace [konfiguračního serveru pomocí služeb Azure Site Recovery](#register-the-configuration-server-with-azure-site-recovery-services).
3. Po přihlášení vyberte předplatné Azure a příslušnou skupinu prostředků a trezor.

    > [!NOTE]
    > Po registraci neexistuje žádná flexibilita pro změnu trezoru služeb obnovení.
    > Změna úložiště služeb pro obnovení vyžaduje oddělení konfiguračního serveru od aktuálního trezoru a replikace všech chráněných virtuálních počítačů pod konfiguračním serverem je zastavena. Další informace najdete [v tématu Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. Při **instalaci softwaru jiných výrobců**:

    |Scénář   |Kroky, které je třeba dodržovat  |
    |---------|---------|
    |Mohu si MySQL stáhnout a nainstalovat ručně?     |  Ano. Stáhněte si aplikaci MySQL, umístěte ji do složky **C:\Temp\ASRSetup**a nainstalujte ji ručně. Poté, co přijmete podmínky a **vyberete stáhnout a nainstalovat**, na portálu je uvedeno *již nainstalováno*. Můžete přejít k dalšímu kroku.       |
    |Mohu se vyhnout stahování MySQL online?     |   Ano. Umístěte instalační aplikaci MySQL do složky **C:\Temp\ASRSetup**. Přijměte podmínky, vyberte **Stáhnout a nainstalovat**a portál použije instalační program, který jste přidali k instalaci aplikace. Po dokončení instalace přejděte k dalšímu kroku.    |
    |Chci stáhnout a nainstalovat MySQL přes Azure Site Recovery.    |  Přijměte licenční smlouvu a vyberte **stáhnout a nainstalovat**. Po dokončení instalace přejděte k dalšímu kroku.       |

5. V **konfiguraci ověřit zařízení**jsou požadavky ověřeny před pokračováním.
6. Na **serveru Configure vCenter Server/vSphere ESXi**zadejte adresu FQDN nebo IP serveru vCenter nebo hostitele vSphere, kde jsou umístěny virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
7. Zadejte přihlašovací údaje, které bude konfigurační server používat pro připojení k serveru VMware. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **Přidat** > **pokračovat**. Zde zadané přihlašovací údaje jsou místně uloženy.
8. V **části Konfigurace přihlašovacích údajů virtuálního počítače**zadejte uživatelské jméno a heslo virtuálních počítačů, aby se služba mobility během replikace automaticky nainstalovala. U počítačů **se systémem Windows** potřebuje účet oprávnění místního správce v počítačích, které chcete replikovat. Pro **Linux**uveďte podrobnosti o kořenovém účtu.
9. Vyberte **Dokončit konfiguraci** a dokončete registraci.
10. Po dokončení registrace otevřete portál Azure a ověřte, zda jsou konfigurační server a server VMware uvedeny na**serverech** > pro**konfiguraci** > **infrastruktury** >  **obnovy služby Recovery**Services .

## <a name="upgrade-the-configuration-server"></a>Upgrade konfiguračního serveru

Informace o upgradu konfiguračního serveru na nejnovější verzi naleznete [v tématu Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Pokyny k upgradu všech součástí site recovery naleznete v tématu [Service updates in Site Recovery](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Správa konfiguračního serveru

Chcete-li zabránit přerušení probíhající replikace, ujistěte se, že se ip adresa konfiguračního serveru nezmění po registraci konfiguračního serveru do úložiště. Další informace o běžných úlohách správy konfiguračního serveru najdete [v tématu Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](vmware-azure-manage-configuration-server.md).

## <a name="troubleshoot-deployment-issues"></a>Řešení problémů při nasazování

V našem [článku o řešení potíží](vmware-azure-troubleshoot-configuration-server.md) se můžete podívat na řešení problémů s nasazením & připojení.

## <a name="faqs"></a>Nejčastější dotazy

* Jak dlouho je licence poskytována na konfiguračním serveru nasazeném prostřednictvím OVF platná? Co se stane, když licenci znovu neaktivuji?

    Licence poskytnutá se šablonou OVA je zkušební licence platná po dobu 180 dnů. Před vypršením platnosti je třeba licenci aktivovat. V opačném případě může vést k častému vypnutí konfiguračního serveru a způsobit překážku replikačních aktivit. Další informace naleznete [v tématu Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware](vmware-azure-manage-configuration-server.md#update-windows-license).

* Můžu použít virtuální hospodaříc, kde je konfigurační server nainstalovaný pro různé účely?

    Ne. Virtuální počítače použijte výhradně pro účely konfiguračního serveru. Ujistěte se, že budete dodržovat všechny specifikace uvedené v [požadavky](#prerequisites) pro efektivní řízení zotavení po havárii.
* Mohu přepnout úschovnu již zaregistrovanou na konfiguračním serveru s nově vytvořeným trezorem?

    Ne. Po zaregistrování úschovny na konfiguračním serveru jej nelze změnit.
* Můžu k ochraně fyzických i virtuálních počítačů použít stejný konfigurační server?

    Ano. Stejný konfigurační server lze použít pro replikaci fyzických a virtuálních počítačů. Fyzický počítač však může být selhání zpět pouze na virtuální počítač VMware.
* Jaký je účel konfiguračního serveru a kde se používá?

    Další informace o konfiguračním serveru a jeho funkcích najdete v [tématu Replikační architektura VMware do Azure](vmware-azure-architecture.md).
* Kde najdu nejnovější verzi konfiguračního serveru?

    Postup upgradu konfiguračního serveru prostřednictvím portálu naleznete v [tématu Upgrade konfiguračního serveru](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Pokyny k upgradu všech součástí site recovery naleznete v tématu [Service updates in Site Recovery](https://aka.ms/asr_how_to_upgrade).
* Kde mohu stáhnout přístupové heslo pro konfigurační server?

    Pokud chcete stáhnout přístupové heslo, [přečtěte si informace o tom, jak spravovat konfigurační server pro zotavení po havárii virtuálního počítače VMware](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).
* Mohu změnit přístupové heslo?

    Ne. Neměňte přístupové heslo konfiguračního serveru. Změna přístupové fráze přeruší replikaci chráněných počítačů a vede ke kritickému stavu.
* Kde si mohu stáhnout registrační klíče trezoru?

    V **úložišti služby Recovery Services**vyberte **položku Správa** > **konfiguračních serverů****infrastruktury** > obnovení lokality . V **části Servery**vyberte **Stáhnout registrační klíč** a stáhněte soubor přihlašovacích údajů úschovny.
* Je možné klonovat existující konfigurační server a použít jej pro orchestraci replikace?

    Ne. Použití součásti klonovaného konfiguračního serveru není podporováno. Klonování serveru procesu horizontálního navýšení kapacity je také nepodporovaný scénář. Klonování součásti obnovení lokality ovlivňuje probíhající replikace.

* Mohu změnit IP adresu konfiguračního serveru?

    Ne. Neměňte IP adresu konfiguračního serveru. Ujistěte se, že všechny IP adresy přiřazené konfiguračnímu serveru jsou statické IP adresy a ne IP adresy DHCP.
* Můžu v Azure nastavit konfigurační server?

    Nastavte konfigurační server v místním prostředí s přímým zorným polem s v-Center a minimalizovat latence přenosu dat. Pro [účely navrácení služeb po službě navrácení služeb po selhání](vmware-azure-manage-configuration-server.md#failback-requirements)můžete provést naplánované zálohování konfiguračního serveru .

* Mohu změnit ovladač mezipaměti na konfiguračním serveru nebo na horizontálním navýšení kapacity?

    Ne, ovladač mezipaměti nelze po dokončení nastavení změnit.

Další časté dotazy na konfiguračních serverech naleznete v [tématu Configuration server common questions](vmware-azure-common-questions.md#configuration-server).

## <a name="next-steps"></a>Další kroky

Nastavte zotavení virtuálních [počítačů VMware](vmware-azure-tutorial.md) do Azure.
