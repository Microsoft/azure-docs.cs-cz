---
title: Nasazení konfiguračního serveru pro zotavení po havárii VMware pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje postup nasazení konfiguračního serveru pro zotavení po havárii VMware pomocí Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2019
ms.author: ramamill
ms.openlocfilehash: c25ca8c27b84f34b025ec5abce00c8d8c70e5df6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125691"
---
# <a name="deploy-a-configuration-server"></a>Nasazení konfiguračního serveru

Při použití se nasadíte místní konfigurační server [Azure Site Recovery](site-recovery-overview.md) pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure. Konfigurace serveru souřadnice komunikaci mezi místní VMware a Azure. Spravuje taky data replikace. Tento článek vás provede kroky potřebné k nasazení konfiguračního serveru při replikaci virtuálních počítačů VMware do Azure. [Postupujte podle tohoto článku](physical-azure-set-up-source.md) Pokud je potřeba nastavit konfigurační server pro replikaci fyzických serverů.

> [!TIP]
> Informace o roli konfiguračního serveru jako součást Azure Site Recovery architektury [tady](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Nasazení konfiguračního serveru pomocí šablony pro soubory OVA

Konfigurační server, musíte nastavit jako vysoce dostupný virtuální počítač VMware s určitým minimální požadavky na hardware a nastavení velikosti. Pohodlný a snadný, nasazení Site Recovery poskytuje ke stažení šablony OVA (Open Virtualization aplikace) nastavit konfigurační server, který splňuje všechny požadavky mandátem uvedených níže.

## <a name="prerequisites"></a>Požadavky

V následující tabulce jsou shrnuté požadavky na minimální hardware pro konfigurační server.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Požadavky na oprávnění k Azure Active Directory

Vyžadujete, aby uživatel s **jednu z následujících** oprávnění nastavil v AAD (Azure Active Directory) zaregistrujte konfigurační server pomocí služby Azure Site Recovery.

1. Uživatel by měl mít roli "Aplikace pro vývojáře" k vytvoření aplikace.
   1. Pokud chcete ověřit, přihlaste se k webu Azure portal</br>
   1. Přejděte do služby Azure Active Directory > role a správci</br>
   1. Ověření, pokud "Aplikace pro vývojáře" role je přiřazená uživateli. Pokud ne, použití uživatele s tímto oprávněním nebo se obraťte na [správce, aby povolil oprávnění](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
1. Pokud nelze přiřadit roli "Vývojář aplikace", ujistěte se, že příznak "Uživatel může zaregistrovat aplikaci" je nastavený na hodnotu true pro uživatele vytvořit identitu. Povolení vyšší oprávnění
   1. Přihlášení k webu Azure Portal
   1. Přejděte do služby Azure Active Directory > uživatelské nastavení
   1. V části ** registrace aplikací ","Uživatelé můžou registrovat aplikace"je třeba zvolit jako"Ano".

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory Federation Services(ADFS) je **nepodporuje**. Použijte prosím účet spravovaný prostřednictvím [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

## <a name="capacity-planning"></a>Plánování kapacity

Požadavky na velikost pro konfigurační server závisí na potenciální četnost změn dat. Tuto tabulku použijte jako vodítko.

| **CPU** | **Paměť** | **Velikost mezipaměti disku** | **Frekvence změny dat** | **Chráněné počítače** |
| --- | --- | --- | --- | --- |
| 8 virtuálních procesorů (2 sockets * 4 jádra \@ 2,5 GHz) |16 GB |300 GB |500 GB nebo méně |Replikace méně než 100 počítačů. |
| 12 virtuálních procesorů (2 sockets * 6 jader \@ 2,5 GHz) |18 GB |600 GB |500 GB až 1 TB |Replikace počítačů 100 150. |
| 16 virtuálních procesorů (2 sockets * 8 jader \@ 2,5 GHz) |32 GB |1 TB |1 TB na 2 TB |Replikace počítačů 150 až 200. |

Pokud replikujete více než jeden virtuální počítač VMware, přečtěte si [důležité informace o plánování kapacity](site-recovery-plan-capacity-vmware.md). Spustit [nástroj Plánovač nasazení](site-recovery-deployment-planner.md) pro replikaci VMWare.

## <a name="download-the-template"></a>Stažení šablony

1. V daném trezoru klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte si šablonu otevřít virtualizace aplikace OVA () pro konfigurační server.

   > [!TIP]
   >Můžete také stáhnout nejnovější verzi šablona konfiguračního serveru přímo z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> Licence, které jsou poskytovány s šablonou OVA je zkušební licence, který je platný po dobu 180 dnů. Po uplynutí této doby zákazník potřebuje aktivovat systém windows s licencí opatřené.

## <a name="import-the-template-in-vmware"></a>Import šablony do VMware

1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **File** (Soubor) vyberte **Deploy OVF Template** (Nasadit šablonu OVF). Tím spustíte průvodce nasazením šablony OVF.

     ![Šablona OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. V **výběr zdroje**, zadejte umístění staženého souboru OVF.
4. V **podrobnosti**vyberte **Další**.
5. V **vyberte název a složku** a **vyberte konfiguraci**, přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon. Použití dynamického zajišťování možností zřizování může mít vliv na výkon konfiguračního serveru.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. V části **Ready to complete** (Připraveno k dokončení):

    * Pokud chcete vytvořit virtuální počítač s výchozím nastavením, vyberte **Power on after deployment** (Spustit po nasazení) > **Finish** (Dokončit).

    * Chcete-li přidat další síťové rozhraní, zrušte **Power on po nasazení**a pak vyberte **Dokončit**. Ve výchozím nastavení se šablona konfiguračního serveru nasadí s jedním síťovým adaptérem. Po nasazení můžete přidat další síťové adaptéry.

> [!IMPORTANT]
> Po nasazení neměňte konfigurace prostředků (omezení paměti a jader/procesoru), úprava nebo odstranění nainstalované služby nebo soubory na konfiguračním serveru. To se negativně registrace konfiguračního serveru pomocí služeb Azure a výkonu konfiguračního serveru.

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
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure.</br>
    a. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.</br>
    b. Ujistěte se, že zvolená uživatelský účet má oprávnění k vytvoření aplikace v Azure. Pokud chcete povolit požadovaná oprávnění, postupujte podle pokynů uvedených [tady](#azure-active-directory-permission-requirements).
7. Nástroj provede několik konfiguračních úloh a pak restartuje počítač.
8. Znovu se přihlaste k počítači. Spustí Průvodce správou konfiguračního serveru **automaticky** několik sekund.

### <a name="configure-settings"></a>Konfigurace nastavení

1. Průvodce správou konfiguračního serveru, vyberte **nastavit připojení**. Z rozevíracích seznamů nejprve vyberte síťový adaptér, který používá integrovaný procesový server pro zjišťování a vynucená instalace služby mobility na zdrojový počítače a potom vyberte síťový adaptér, který konfigurační Server používá pro připojení k Azure. Potom vyberte **Uložit**. Toto nastavení nelze změnit po dokončení konfigurace. Důrazně doporučujeme neměnit IP adresu konfiguračního serveru. Ujistěte se, že IP adresa přiřazená ke konfiguračnímu serveru je STATICKÁ IP adresa a ne IP DHCP.
2. V **trezor Recovery Services vyberte**, přihlaste se k Microsoft Azure pomocí přihlašovacích údajů používaných pro **kroku 6** z "[registrace konfiguračního serveru pomocí služby Azure Site Recovery Services](#register-the-configuration-server-with-azure-site-recovery-services)" .
3. Po přihlášení vyberte své předplatné Azure a příslušnou skupinu prostředků a trezor.

    > [!NOTE]
    > Po registraci, neexistuje žádný flexibilitu, chcete-li změnit trezor služby recovery services.
    > Změna trezor služby recovery services by vyžadoval zrušení přidružení konfiguračního serveru z aktuální trezoru a replikaci všech chráněných virtuálních počítačů v rámci konfigurace serveru je zastavená. Další [informace](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)

4. V **nainstalovat software třetí strany**,

    |Scénář   |Postup  |
    |---------|---------|
    |Můžete stáhnout a nainstalovat MySQL ručně?     |  Ano. Stažení aplikace MySQL a umístěte ho do složky **C:\Temp\ASRSetup**, nainstalujte ručně. Teď, když je přijímáte > klikněte na **stáhněte a nainstalujte**, uvádí, že na portálu *už nainstalovaná*. Můžete přejít k dalšímu kroku.       |
    |Můžete vyhnout stažení nástroje MySQL online?     |   Ano. Umístěte do složky aplikace Instalační program MySQL **C:\Temp\ASRSetup**. Přijměte podmínky > klikněte na **stáhnout a nainstalovat**, portál použije instalační program přidali a nainstaluje aplikaci. Můžete přejít na další krok po instalaci.    |
    |Chci stáhnout a nainstalovat MySQL pomocí Azure Site Recovery     |  Přijměte licenční smlouvu a klikněte na **stáhnout a nainstalovat**. Potom můžete přejít na další krok po instalaci.       |

5. Než budete pokračovat, v části **Ověřit konfiguraci zařízení** se ověří požadavky.
6. V části **Konfigurovat vCenter Server nebo server vSphere ESXi** zadejte plně kvalifikovaný název domény nebo IP adresu vCenter Serveru nebo hostitele vSphere, na kterém jsou umístěné virtuální počítače, které chcete replikovat. Zadejte port, na kterém server naslouchá. Zadejte popisný název, který se použije pro server VMware v trezoru.
7. Zadejte přihlašovací údaje, které bude konfigurační server používat pro připojení k serveru VMware. Služba Site Recovery je použije k automatickému zjištění virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **přidat**a potom **pokračovat**. Zde zadané přihlašovací údaje se uloží místně.
8. V **nakonfigurovat přihlašovací údaje virtuálního počítače**, zadejte uživatelské jméno a heslo pro automatickou instalaci služby Mobility při replikaci virtuálních počítačů. Pro **Windows** počítače, účet potřebuje oprávnění místního správce na počítačích, které chcete replikovat. Pro **Linux**, zadejte podrobnosti pro kořenový účet.
9. Vyberte **Dokončit konfiguraci** a dokončete registraci.
10. Po dokončení registrace otevřete Azure portal, zkontrolujte, že konfigurační server a VMware server jsou uvedené na **trezor služby Recovery Services** > **spravovat**  >  **Infrastruktura site Recovery** > **konfigurační servery**.

## <a name="upgrade-the-configuration-server"></a>Upgradujte konfigurační server

Konfigurační server upgradovat na nejnovější verzi, postupujte podle těchto [kroky](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Podrobné pokyny o tom, jak upgradovat všechny součásti Site Recovery, klikněte na tlačítko [tady](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Správa konfiguračního serveru

Vyhnout se přerušením v probíhající replikaci, ujistěte se, že IP adresa konfiguračního serveru nezmění po registraci konfiguračního serveru k trezoru. Další informace o běžných úloh správy serveru konfigurace [tady](vmware-azure-manage-configuration-server.md).

## <a name="faq"></a>Nejčastější dotazy

1. Jak dlouhé je, že platnost Licence na konfiguračním serveru, které jsou nasazené prostřednictvím OVF k dispozici? Co se stane, když není můžu aktivovat licenci?

    Licence, které jsou poskytovány s šablonou OVA je zkušební licence, který je platný po dobu 180 dnů. Před vypršením platnosti budete muset aktivovat licenci. Jinak to mít za následek časté vypnutí konfiguračního serveru a hinderance replikace aktivitám, čímž vznikne.

2. Můžete použít virtuální počítač, ve kterém je nainstalován konfigurační server pro různé účely?

    **Ne**, doporučujeme použít virtuální počítač pro jediným účelem konfiguračního serveru. Ujistěte se, postupujte podle požadavků uvedených v [požadavky](#prerequisites) pro efektivní správu zotavení po havárii.
3. Můžete přepnout v úložišti už zaregistrovaný na konfiguračním serveru pomocí nově vytvořeného trezoru?

    **Ne**, jakmile se trezor zaregistrován u konfiguračního serveru, nejde změnit.
4. Můžete použít stejný konfigurační server pro ochranu fyzických a virtuálních počítačů?

    **Ano**, lze použít stejný konfigurační server pro replikaci fyzických a virtuálních počítačů. Nicméně by bylo možné provést fyzický počítač zpět jenom pro virtuální počítač VMware.
5. Jaký je účel konfigurační server a kde se používá?

    Odkazovat na [replikaci z VMware do Azure replikace architektura](vmware-azure-architecture.md) získat další informace o konfiguračním serveru a jeho funkcí poskytujete.
6. Kde najdete nejnovější verzi konfiguračního serveru?

    Kroky pro upgrade konfiguračního serveru pomocí portálu, najdete v části [upgradujte konfigurační server](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Podrobné pokyny o tom, jak upgradovat všechny součásti Site Recovery, najdete v tématu [tady](https://aka.ms/asr_how_to_upgrade).
7. Kde lze stáhnout přístupové heslo pro konfigurační server?

    Odkazovat na [v tomto článku](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) stáhnout přístupové heslo.
8. Můžete změnit heslo?

    **Ne**, jste **důrazně doporučuje kvůli změně přístupového hesla** konfiguračního serveru. Změna přístupové heslo přestane fungovat replikaci chráněných počítačů a vede k kritickém stavu.
9. Kde lze stáhnout registrační klíče trezoru?

    V **trezor služby Recovery Services**, **spravovat** > **infrastruktura Site Recovery** > **konfigurační servery**. Na serverech, vyberte **stáhnout registrační klíč** ke stažení souboru s přihlašovacími údaji.
10. Můžete naklonovat stávající konfigurační Server a použít ho pro orchestraci replikace?

    **Ne**, použití klonovaný součásti serveru Configuration není podporováno. Klon horizontální navýšení kapacity procesového serveru se také o nepodporovaný scénář. Součásti klonování Site Recovery vliv na probíhající replikace.

11. Můžete změnit IP adresa konfiguračního serveru?

    **Ne**, se důrazně doporučuje ne změnit IP adresu konfiguračního serveru. Zkontrolujte všechny IP adresy přiřazené ke konfiguračnímu serveru jsou statické IP adresy a ne IP adresy DHCP.
12. Můžete nastavit konfigurační server v Azure?

    Doporučujeme nastavit konfigurační server v místním prostředí s přímým přístupem řádku přístup pomocí Vcenter a minimalizovat latenci přenosu dat. Můžete provádět plánované zálohování konfiguračního serveru pro [účely navrácení služeb po obnovení](vmware-azure-manage-configuration-server.md#failback-requirements).

Další nejčastější dotazy na konfiguračním serveru, najdete v našich [dokumentaci na běžné dotazy týkající se konfigurace serveru](vmware-azure-common-questions.md#configuration-server) .

## <a name="troubleshoot-deployment-issues"></a>Řešení problémů s nasazením

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Další postup

Nastavení zotavení po havárii [virtuálních počítačů VMware](vmware-azure-tutorial.md) do Azure.
