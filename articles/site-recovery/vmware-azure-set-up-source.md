---
title: Nastavení zdrojového prostředí a konfigurace určují zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak nastavit v místním prostředí a konfiguračního serveru pro zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: e09e93ef85449c51e35b8da7ad93ee7088a0e7b4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566308"
---
# <a name="set-up-the-source-environment-and-configuration-server"></a>Nastavení zdrojového prostředí a konfigurace serveru

Tento článek popisuje, jak nastavit zdrojové prostředí místní zotavení po havárii virtuálních počítačů VMware do Azure s využitím [Azure Site Recovery](site-recovery-overview.md). Její součástí jsou kroky pro nastavení místního počítače jako konfigurační server Site Recovery, a automaticky zjišťování místních virtuálních počítačů. 

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste provedli následující

- Plánované nasazení zotavení po havárii pomocí [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). Plánovač pomáhá zajistit dostatečné prostředky a šířky pásma pro zotavení po havárii a cíl bodu obnovení (RPO) ve vaší organizaci.
- [Nastavení prostředků Azure](tutorial-prepare-azure.md) v [webu Azure portal](http://portal.azure.com).
- [Nastavení VMware v místním prostředí](vmware-azure-tutorial-prepare-on-premises.md) prostředky, včetně vyhrazený účet pro automatické zjišťování místních virtuálních počítačů VMware.
- Je možné [projděte si nejčastější dotazy](vmware-azure-common-questions.md#configuration-server) o nasazení konfiguračního serveru a správu.


## <a name="choose-protection-goals"></a>Zvolte cíle ochrany

1. V okně **Trezory Recovery Services** vyberte název trezoru. 
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V části **Cíl ochrany** > **Kde jsou vaše počítače umístěné?** vyberte **Místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V části **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere Hypervisor**. Pak vyberte **OK**.

## <a name="about-the-configuration-server"></a>Informace o konfiguračním serveru

Při nastavování zotavení po havárii virtuálních počítačů VMware do Azure nasadíte místní konfigurační server.

- Konfigurace serveru souřadnice komunikaci mezi místní VMware a Azure. Spravuje taky data replikace.
- Nasazení konfiguračního serveru jako virtuálního počítače VMware.
- Site Recovery poskytuje šablonu OVA, kterou můžete stáhnout z webu Azure portal a importovat do vCenter serveru a nastavit konfigurační server virtuálního počítače.
- [Další informace](vmware-azure-architecture.md) o součásti konfiguračního serveru a procesů.


>[!NOTE]
Nepoužívejte tyto pokyny, pokud nasazujete konfiguračního serveru pro zotavení po havárii místních fyzických počítačů do Azure. V tomto scénáři použijte [v tomto článku](physical-azure-set-up-source.md).


## <a name="before-you-deploy-the-configuration-server"></a>Před nasazení konfiguračního serveru

Pokud instalaci konfiguračního serveru jako virtuálního počítače VMware pomocí šablony pro soubory OVA, virtuální počítač se nainstalují všechny požadavky. Pokud chcete zkontrolovat požadavky, použijte v následujících článcích.

- [Další informace o](vmware-azure-configuration-server-requirements.md) hardwaru, softwaru a požadavky na kapacitu pro konfigurační server.
- Pokud replikujete více virtuálních počítačů VMware, měli byste zkontrolovat [důležité informace o plánování kapacity](site-recovery-plan-capacity-vmware.md)a spusťte [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) nástroj pro replikaci VMWare.
- Licence Windows pro soubory OVA šablony je zkušební licence, který je platný po dobu 180 dnů. Po uplynutí této doby budete muset aktivovat Windows s platnou licencí. 
- Šablona OVA poskytuje jednoduchý způsob, jak nastavit konfigurační server jako virtuální počítač VMware. Pokud z nějakého důvodu potřebujete vytvořit virtuální počítač VMware bez šablony, zkontrolovat předpoklady a postupujte podle pokynů pro ruční v [v tomto článku](physical-azure-set-up-source.md).
- Váš účet Azure potřebuje oprávnění k vytvoření aplikace Azure AD.


>[!IMPORTANT]
Konfigurační server musí být nasazený, jak je popsáno v tomto článku. Kopírování nebo klonování existující konfigurační server není podporováno.

### <a name="set-up-azure-account-permissions"></a>Nastavení oprávnění účtu Azure

Tenanta nebo globální správce můžete přiřadit oprávnění k vytvoření aplikace Azure AD k účtu nebo přiřadit roli pro vývojáře aplikací (který má oprávnění) k účtu.


Tenanta nebo globální správce může udělit oprávnění pro účet následujícím způsobem:

1. Ve službě Azure AD, by měl tenanta nebo globální správce přejděte na **Azure Active Directory** > **uživatelé** > **uživatelská nastavení**.
2. Správce by měl nastavit **registrace aplikací** k **Ano**.

 > [!NOTE]
 > Toto je výchozí nastavení, která nejsou citlivá. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Můžete také tenanta nebo globální správce má oprávnění k přiřazení role k účtu. [Další informace](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

 


## <a name="download-the-ova-template"></a>Stáhnout šablonu pro soubory OVA

1. V daném trezoru klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. V části **Připravit zdroj** vyberte **+ Konfigurační server**.
3. V části **Přidat server** zkontrolujte, jestli se v části **Typ serveru** zobrazí **Konfigurační server pro VMware**.
4. Stáhněte si šablonu otevřít virtualizace aplikace OVA () pro konfigurační server.

  > [!TIP]
>Můžete také stáhnout nejnovější verzi šablona konfiguračního serveru z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).


## <a name="import-the-ova-template-in-vmware"></a>Import OVA šablony do VMware

1. Pomocí klientské konzoly VMware vSphere Client se přihlaste k VMware vCenter Serveru nebo hostiteli vSphere ESXi.
2. V nabídce **File** (Soubor) vyberte **Deploy OVF Template** (Nasadit šablonu OVF). Tím spustíte průvodce nasazením šablony OVF.

     ![Šablona OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. V **výběr zdroje**, zadejte umístění staženého souboru OVF.
4. V **podrobnosti**vyberte **Další**.
5. V **vyberte název a složku** a **vyberte konfiguraci**, přijměte výchozí nastavení.
6. V nabídce **Select storage** (Vybrat úložiště) vyberte v části **Select virtual disk format** (Vybrat formát virtuálního disku) možnost **Thick Provision Eager Zeroed** (Vytvoření celého disku s vynulováním celého disku), která zajistí nejlepší výkon.
7. Na zbývajících stránkách průvodce přijměte výchozí nastavení.
8. V **připraví k dokončení**, chcete-li vytvořit virtuální počítač s výchozím nastavením, vyberte **Power on po nasazení** > **Dokončit**.
9. Ve výchozím nastavení je nasazen virtuální počítač s jedním síťovým adaptérem. Pokud chcete přidat doplněk síťové rozhraní, zrušte zaškrtnutí políčka **Power on po nasazení**a klikněte na tlačítko **Dokončit**. Potom postupujte podle následující postup.

## <a name="add-an-adapter-to-the-configuration-server"></a>Přidat adaptér ke konfiguračnímu serveru

Pokud chcete přidat další síťový adaptér ke konfiguračnímu serveru, přidejte ji předtím, než zaregistrujete server v trezoru. Jakmile ho zaregistrujete, další adaptéry nebudete moct přidat.

1. V inventáři konzole vSphere Client klikněte pravým tlačítkem na virtuální počítač a vyberte **Edit Settings** (Upravit nastavení).
2. V části **Hardware** vyberte **Add** (Přidat) > **Ethernet Adapter** (Adaptér sítě Ethernet). Pak vyberte **Další**.
3. Vyberte typ adaptéru a síť.
4. Chcete-li připojit virtuální síťové karty, pokud je zapnutý virtuální počítač, vyberte **připojit na zapnutí**. Potom vyberte **Další** > **Dokončit** > **OK**.

## <a name="register-the-configuration-server"></a>Registrace konfiguračního serveru 
Zapněte požadovaný virtuální počítač a zaregistrujte konfigurační server v trezoru Site Recovery.

1. Z klientské konzole VMware vSphere Client zapněte požadovaný virtuální počítač.
2. Virtuální počítač se spustí do prostředí Windows serveru 2016 instalace. Přijměte licenční smlouvu a zadejte heslo správce.
3. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce.



## <a name="set-up-the-configuration-server"></a>Nastavení konfigurace serveru

Jako součást svého nasazení budete muset nainstalovat MySQL na konfiguračním serveru virtuálního počítače. Můžete to provést několika způsoby:

- Umožní Site Recovery stažení a instalaci MySQL. Pokud chcete použít tuto možnost, nemusíte dělat nic. před zahájením instalace nastavit konfigurační server.
- Stáhněte a nainstalujte MySQL ručně: před nastavením konfiguračního serveru stáhněte instalační program MySQL a vložit ho **C:\Temp\ASRSetup** složky. Nyní nainstalujte MySQL. 
- Ruční stažení a nechat nainstalovat Site Recovery. Před nastavením konfiguračního serveru stáhněte instalační program MySQL a vložit ho do **C:\Temp\ASRSetup** složky.


1. Při prvním přihlášení k virtuálnímu počítači, spustí se nástroj pro konfiguraci Azure Site Recovery.
2. Zadejte název, který slouží k registraci konfiguračního serveru v trezoru Site Recovery. Pak vyberte **Další**.
3. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání spojení vyberte **Přihlásit se**, abyste se mohli přihlásit ke svému předplatnému Azure. Všimněte si, že účet musí mít přístup k trezoru, ve kterém chcete zaregistrujte konfigurační server.
4. Nástroj provede několik konfiguračních úloh a pak restartuje počítač.
5. Znovu se přihlaste k počítači. Průvodce správou konfiguračního serveru, se automaticky spustí během několika sekund.
6. V průvodci vyberte **nastavit připojení**.
7. Vyberte síťový adaptér, který procesového serveru (běžícího ve výchozím nastavení na konfiguračním serveru) používá pro příjem přenosů replikace z virtuálních počítačů.
8. Potom vyberte **Uložit**. Všimněte si, že po registraci konfiguračního serveru nelze změnit nastavení trezoru. 
9. V **trezor Recovery Services vyberte**, přihlaste se k Microsoft Azure, vyberte své předplatné Azure a příslušnou skupinu prostředků a trezor. 
10. V **nainstalovat software třetí strany**, nainstalovat MySQL:
     - Site Recovery zpracovává MySQL stažení a instalaci, klikněte na tlačítko **stáhněte a nainstalujte**. Počkejte na dokončení a potom pokračovat v Průvodci instalace.
     - Pokud jste si stáhli MySQL a Site Recovery tento nástroj nainstalovat, klikněte na tlačítko **stáhnout a nainstalovat**. Počkejte na dokončení instalace a pokračovat v průvodci.
     - Pokud jste ručně stáhli a nainstalovali MySQL, klikněte na tlačítko **stáhnout a nainstalovat**. Aplikace bude zobrazovat jako **už nainstalovaná**. Pokračujte v průvodci.
11. V **ověřit konfiguraci zařízení**, instalační program ověří požadavky, než budete pokračovat. 
12. V **konfigurovat vCenter serveru nebo serveru vSphere ESXi**, zadejte plně kvalifikovaný název nebo IP adresa serveru vCenter nebo hostitele vSphere, na které virtuální počítače, které chcete replikovat, se nachází. Zadejte port, na kterém server naslouchá a zadejte popisný název pro VMware server v trezoru.
13. Zadejte přihlašovací údaje, které bude konfigurační server pro připojení k serveru VMware a automatickému zjišťování virtuálních počítačů VMware, které jsou dostupné pro replikaci. Vyberte **přidat** >  **pokračovat**. Přihlašovací údaje se uloží místně.
14. V **nakonfigurovat přihlašovací údaje virtuálního počítače**, zadejte přihlašovací údaje, které Site Recovery se používají pro automatickou instalaci služby Mobility, když povolíte replikaci pro virtuální počítač.
    - Když chcete replikovat počítače s Windows, je nutné, aby na nich měl tento účet oprávnění místního správce.
    - U počítačů s Linuxem zadejte údaje superuživatele.
15. Vyberte **Dokončit konfiguraci** a dokončete registraci.
16. Po dokončení registrace, otevřete na webu Azure portal a zkontrolujte, že konfigurační server a VMware server zobrazí **trezor služby Recovery Services** > **spravovat**  >   **Infrastruktura Site Recovery** > **konfigurační servery**.


## <a name="exclude-antivirus-on-the-configuration-server"></a>Vyloučit antivirový na konfiguračním serveru

Pokud na konfiguračním serveru virtuální počítač je spuštěný antivirový software, ujistěte se, že následující složky jsou vyloučeny z antivirového operace. Tím se zajistí, že replikace a připojení fungovat podle očekávání: 

- Agent C:\Program Files\Microsoft Azure Recovery Services
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manageru
- Kolekce chyb nástroj pro C:\Program Files\Microsoft Azure Site Recovery
- C:\thirdparty
- C:\Temp
- C:\strawberry
- C:\ProgramData\MySQL
- Soubory (x86) \MySQL C:\Program
- C:\ProgramData\ASR
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\Inetpub
- Site Recovery instalační adresář. Příklad: soubory E:\Program (x86) \Microsoft Azure Site Recovery


## <a name="next-steps"></a>Další postup
- Pokud narazíte na potíže, přečtěte si [běžné otázky](vmware-azure-common-questions.md#configuration server) a [řešení potíží s](vmware-azure-troubleshoot-configuration-server.md) pro konfigurační server.
- Nyní [nastavení cílového prostředí](./vmware-azure-set-up-target.md) v Azure.
