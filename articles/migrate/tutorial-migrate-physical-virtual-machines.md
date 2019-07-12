---
title: Migrace místních fyzických počítačů nebo virtualizované počítačů do Azure pomocí Azure Migrate migrace serveru | Dokumentace Microsoftu
description: Tento článek popisuje, jak migrovat místní fyzické počítače nebo virtualizované počítače do Azure pomocí služby Azure Migrate serveru migrace.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 006f7270570eba17fb44421b1c181d9ad68ad2ee
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854182"
---
# <a name="migrate-physical-or-virtualized-servers-to-azure"></a>Migrujte fyzické nebo virtualizované servery do Azure 

Tento článek popisuje, jak migrovat fyzické nebo virtualizované servery do Azure. Nástroj pro migraci serveru migrace Azure nabízí migrace fyzickou a virtualizovanou servery pomocí replikace založené na agentovi. Pomocí tohoto nástroje můžete migrovat širokou škálu počítačů do Azure:

- Migrace místních fyzických serverů.
- Migrace virtuálních počítačů Virtualizovat platformy, jako je Xen, KVM.
- Migrace virtuálních počítačů VMware nebo Hyper-V. To je užitečné, pokud z nějakého důvodu nemůžete použít standardní migrace tok, který nabízí službu Azure Migrate migrace serveru [Hyper-V](tutorial-migrate-hyper-v.md), [VMware bez agentů](tutorial-migrate-vmware.md) migrace, nebo [VMware založené na agentovi](tutorial-migrate-vmware-agent.md) migrace.
- Migrate virtuální počítače spuštěné v privátních cloudech.
- Migrace virtuálních počítačů spuštěných ve veřejných cloudech, jako je například Amazon Web Services (AWS) nebo Google Cloud Platform (GCP).


[Azure Migrate](migrate-services-overview.md) poskytuje instance centrální rozbočovač pro sledování zjišťování, vyhodnocení a migraci místních aplikací a úloh a cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro vyhodnocení a migrace, jakož i nabídky softwaru třetích stran výrobce (ISV).


V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Příprava pro migraci do Azure migrovat Server migračního nástroje Azure.
> * Zkontrolujte požadavky pro počítače, že který chcete migrovat a připravte počítače pro zařízení Azure Migrate replikace, který se používá k zjišťování a migrace počítačů do Azure.
> * Přidáte nástroj pro migraci serveru migraci Azure v centru Azure Migrate.
> * Nastavení replikace zařízení.
> * Instalace služby Mobility na počítačích, které chcete migrovat.
> * Povolení replikace.
> * Spuštění testu migrace, abyste měli jistotu, že všechno funguje podle očekávání.
> * Spusťte úplné migrace do Azure.

> [!NOTE]
> Kurzy vám ukážou, nejjednodušší způsob nasazení pro scénář tak, že můžete rychle nastavit testování konceptu. Kurzy použijte výchozí možnosti, kde je to možné a nezobrazovat všechna možná nastavení a cesty. Podrobné pokyny najdete v tématu postupy pro Azure Migrate.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Kontrola](migrate-architecture.md) architekturu migrace.
2. Ujistěte se, že váš účet Azure je přiřazena role Přispěvatel virtuálních počítačů, tak, že máte oprávnění pro:

    - Vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Vytvoření virtuálního počítače ve vybrané virtuální síti
    - Zapsat do služby Azure spravovaného disku. 

3. [Nastavit síť Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Při replikaci do Azure, virtuální počítače Azure vytvořené a připojené k síti, kterou zadáte při nastavování migrace.


## <a name="prepare-azure"></a>Příprava Azure

Nastavte oprávnění Azure, předtím, než je možné migrovat pomocí migrace serveru migrace Azure.

- **Vytvoření projektu**: Váš účet Azure potřebuje oprávnění k vytvoření projektu Azure Migrate. 
- **Registrace zařízení Azure Migrate replikace**: Zařízení replikace vytváří a registruje aplikaci pro Azure Active Directory ve svém účtu Azure. Delegování oprávnění pro tento.
- **Vytvoření služby Key Vault**: K migraci počítačů, Azure Migrate vytvoří trezor klíčů ve skupině prostředků, ke správě přístupových klíčů k účtu úložiště replikace v rámci vašeho předplatného. Vytvoření trezoru, budete potřebovat oprávnění k přiřazení role u skupiny prostředků, ve kterém je umístěn projekt Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Přidělování oprávnění k vytvoření projektu

1. Na webu Azure Portal, otevřete předplatné a vyberte **řízení přístupu (IAM)** .
2. V **zkontrolovat přístup**vyhledejte příslušný účet a klikněte na něj chcete-li zobrazit oprávnění.
3. Měli byste mít **Přispěvatel** nebo **vlastníka** oprávnění.
    - Pokud jste právě vytvořili bezplatný účet Azure, vlastník předplatného.
    - Pokud si nejste vlastník předplatného, pracujete s vlastníkem přiřazení role.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Přiřadit oprávnění k registraci zařízení replikace

Migrace na základě agenta delegování oprávnění pro Azure migrace serveru migrace k vytvoření a registrace aplikace Azure AD ve vašem účtu. Můžete přiřadit oprávnění pomocí jedné z následujících metod:

- Tenanta nebo globální správce může udělit oprávnění uživatelům v tenantovi, k vytvoření a registrace aplikace Azure AD.
- Tenanta nebo globální správce může k účtu přiřadit roli vývojář aplikace, (který má oprávnění).

Je vhodné poznamenat, že:

- Aplikace nemají žádné jiné oprávnění přístupu na předplatné, než je popsaný výše.
- Pouze potřebujete tato oprávnění při registraci nového zařízení replikace. Po nastavení replikace zařízení můžete odebrat oprávnění. 


#### <a name="grant-account-permissions"></a>Udělte účtu oprávnění

Tenanta nebo globální správce může udělit oprávnění následujícím způsobem

1. Ve službě Azure AD, by měl tenanta nebo globální správce přejděte na **Azure Active Directory** > **uživatelé** > **uživatelská nastavení**.
2. Správce by měl nastavit **registrace aplikací** k **Ano**.

    ![Oprávnění Azure AD](./media/tutorial-migrate-physical-virtual-machines/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, která nejsou citlivá. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Přiřazení role aplikace pro vývojáře 

Tenanta nebo globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-permissions-to-create-key-vault"></a>Přidělování oprávnění k vytvoření služby Key Vault

Přiřadíte oprávnění k přiřazení role u skupiny prostředků, ve kterém projektu Azure Migrate nachází, následujícím způsobem:

1. Ve skupině prostředků na webu Azure Portal, vyberte **řízení přístupu (IAM)** .
2. V **zkontrolovat přístup**vyhledejte příslušný účet a klikněte na něj chcete-li zobrazit oprávnění. Potřebujete **vlastníka** (nebo **Přispěvatel** a **správce uživatelských přístupů**) oprávnění.
3. Pokud nemáte potřebná oprávnění, o ně požádat vlastníka skupiny prostředků. 

## <a name="prepare-for-migration"></a>Příprava migrace

### <a name="check-machine-requirements-for-migration"></a>Zkontrolujte požadavky na počítače pro migraci

Zajistěte, aby počítače v souladu s požadavky pro migraci do Azure. 

> [!NOTE]
> Migrace na základě agenta s Azure Migrate migrace serveru vychází z funkcí služby Azure Site Recovery. Dokumentace ke službě Site Recovery může propojit některé požadavky.

1. [Ověřte](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) požadavky na servery VMware.
2. [Ověřte](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) virtuálního počítače podporují požadavky pro migraci.
3. Ověřte nastavení virtuálního počítače. Místní virtuální počítače replikovat do Azure musí být v souladu s [požadavky na virtuální počítač Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Příprava počítače, aby zařízení replikace

Migrace serveru do Azure migrovat používá replikace zařízení Pokud chcete počítače replikovat do Azure. Zařízení replikace se spustí následující součásti.

- **Konfigurační server**: Konfigurační server koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat.
- **Procesový server:** Procesní server funguje jako replikační brána. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do účtu úložiště mezipaměti v Azure. 

Než začnete, musíte připravit počítač s Windows Server 2016 a hostovat zařízení replikace. Na počítači, by měly splňovat [tyto požadavky](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements).


## <a name="add-the-azure-migrate-server-migration-tool"></a>Přidat nástroj pro migraci serveru migrace Azure

Nastavte projekt Azure Migrate a pak k němu přidat nástroj pro migraci serveru migrace Azure.

1. Na webu Azure Portal > **všechny služby**, vyhledejte **Azure Migrate**.
2. V části **služby**vyberte **Azure Migrate**.
3. V **přehled**, klikněte na tlačítko **posoudit a migrovat servery**.
4. V části **zjišťování, vyhodnocení a migrace serverů**, klikněte na tlačítko **posouzení a migraci serverů**.

    ![Zkoumání a posouzení servery](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. V **zjišťování, vyhodnocení a migrace serverů**, klikněte na tlačítko **přidat nástroje**.
6. V **migrace projektu**, vyberte své předplatné Azure a pokud ho nemáte, vytvořte skupinu prostředků.
7. V **Project Details**, zadejte název projektu a zeměpisné oblasti, ve kterém chcete vytvořit projekt a klikněte na tlačítko **další**

    ![Vytvoření projektu Azure Migrate](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Projekt Azure Migrate můžete vytvořit v některém z těchto zeměpisných oblastech.

    **Zeměpisné oblasti** | **Oblast**
    --- | ---
    Asie | Jihovýchodní Asie
    Evropa | Severní Evropa a západní Evropa
    Spojené státy | USA – východ nebo USA (střed) – západ

    Zeměpisné umístění vybrané pro tento projekt slouží jen k uložení metadat získaných z místních virtuálních počítačů. Můžete vybrat libovolnou oblast cílového skutečné migrace.
8. V **nástroj pro vyhodnocení vyberte**vyberte **přeskočit přidávání nástroj pro vyhodnocení nyní** > **Další**.
9. V **nástroj pro migraci vybrat**vyberte **Azure Migrate: Migrace serveru** > **Další**.
10. V **zkontrolujte + přidat nástroje**, zkontrolujte nastavení a klikněte na tlačítko **přidat nástroje**
11. Po přidání nástroj, zobrazí se v projektu Azure Migrate > **servery** > **nástrojů pro migraci**.

## <a name="set-up-the-replication-appliance"></a>Nastavení replikace zařízení

Prvním krokem migrace je nastavení replikace zařízení. Stáhněte si instalační soubor pro zařízení a spusťte ho na [počítače, které jste připravili](#prepare-a-machine-for-the-replication-appliance). Po instalaci zařízení zaregistrovat pomocí Azure Migrate serveru migrace.


### <a name="download-the-replication-appliance-installer"></a>Stažení instalačního programu zařízení replikace

1. V projektu Azure Migrate > **servery**v ***Azure Migrate: Migrace serveru**, klikněte na tlačítko **Discover**.

    ![Vyhledání virtuálních počítačů](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. V **zjistit počítače** > **jsou vaše počítače virtualizované?** , klikněte na tlačítko **nevirtualizované/jiné**.
4. V **cílové oblasti**, vyberte oblast Azure, do kterého chcete migrovat počítače.
5. Vyberte **potvrdit, že cílová oblast pro migraci název oblasti**.
6. Klikněte na tlačítko **vytvářet prostředky**. Tím se vytvoří trezor služby Azure Site Recovery na pozadí.
    - Pokud jste již nastavili migrace se službou Azure Migrate serveru migrace, možnost target nedá nakonfigurovat, protože prostředky byly dříve zřízeny.
    - Cílová oblast pro tento projekt nelze změnit po kliknutí na toto tlačítko.
    - Všechny následné migrace jsou do této oblasti.

7. V **chcete nainstalovat nové zařízení replikace?** vyberte **nainstalujte replikaci zařízení**.
9. V **stažení a instalace softwaru zařízení replikace**, stáhněte si instalační program zařízení a registrační klíč. Klíč je potřeba k registraci zařízení. Klíč je platný pět dní po jeho stažení.

    ![Stáhnout poskytovatele](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Zkopírujte soubor nastavení zařízení a soubor klíče k Windows serveru 2016 počítači, který jste vytvořili pro zařízení.
11. Spusťte soubor replikace nastavení zařízení, jak je popsáno v následujícím postupu.
12. Po restartování zařízení po dokončení instalace, v **zjistit počítače**, vyberte nové zařízení na **vyberte konfigurační Server**a klikněte na tlačítko **dokončení registrace**. Dokončení registrace provede několik poslední úkoly přípravy zařízení replikace.

    ![Dokončení registrace](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Může trvat až 15 minut po dokončení registrace, dokud zjištěné počítače v Azure Migrate serveru migrace. Při zjištění virtuálních počítačů, **zjištěných serverů** počet složitost.

![Vyhledaných serverů](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalace služby Mobility

Na počítačích, které chcete migrovat budete muset nainstalovat agenta služby Mobility. Agent instalační programy jsou dostupné v zařízení replikace. Najít správný instalační program a nainstalujte agenta na každém počítači, který chcete migrovat. Proveďte to následujícím způsobem:

1. Přihlaste se k zařízení replikace.
2. Přejděte do **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Vyhledejte počítač operační systém a verze Instalační služby. Kontrola [podporované operační systémy](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Zkopírujte instalační soubor do počítače, který chcete migrovat.
5. Ujistěte se, že máte heslo, které byl vygenerován při nasazení na zařízení.
    - Store tento soubor do dočasné textového souboru v počítači.
    - Můžete získat přístupové heslo na zařízení replikace. Z příkazového řádku, spusťte **C:\ProgramData\ASR\svsystems\bin\genpassphrase.exe - v** zobrazíte aktuální heslo.
    - Negenerovat přístupové heslo. Tím dojde k přerušení připojení a budete muset znovu registrovat zařízení replikace.


### <a name="install-on-windows"></a>Instalace v systému Windows

1. Extrahujte obsah souboru Instalační služby systému do místní složky (třeba C:\Temp) na počítači, následujícím způsobem:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Spusťte instalační program služby Mobility:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Registrace agenta pomocí replikace zařízení:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Instalace v systému Linux

1. Extrahujte obsah instalačního programu tarballu do místní složky (například /tmp/MobSvcInstaller) na počítači, následujícím způsobem:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Spusťte instalační skript:
    ```
    sudo ./install -r MS -q
    ```
3. Registrace agenta pomocí replikace zařízení:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replikace počítačů

1. V projektu Azure Migrate > **servery**, **Azure Migrate: Migrace serveru**, klikněte na tlačítko **replikovat**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. V **replikovat**, > **nastavení zdroje** > **jsou vaše počítače virtualizované?** vyberte **Ano, s VMware vSphere**.
3. V **místní zařízení**, vyberte název zařízení, které jste nastavili Azure Migrate.
4. V **vCenter server**, zadejte název serveru vCenter, Správa virtuálních počítačů, nebo server vSphere, na kterém jsou hostované virtuální počítače.
5. V **procesový Server**, vyberte název zařízení replikace.
6. V **hosta pověření**, zadejte účet správce virtuálního počítače, který se použije pro nabízenou instalaci služby Mobility. V tomto kurzu jsme instalovat službu Mobility ručně, takže můžete přidat libovolný fiktivní účet. Pak klikněte na tlačítko **Další: Virtuální počítače**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. V **virtuálních počítačů**v **importovat nastavení migrace z posouzení?** , ponechte výchozí nastavení **Ne, určíte nastavení migrace ručně**.
8. Zkontrolujte jednotlivé virtuální počítače, které chcete migrovat. Pak klikněte na tlačítko **Další: Cílová nastavení**.

    ![Vyberte virtuální počítače](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. V **cílit na nastavení**, vyberte předplatné a cílit na oblast, do kterého budete migrovat a zadejte skupinu prostředků, ve kterém budou umístěné virtuální počítače Azure po migraci.
10. V **virtuální sítě**, vyberte Azure virtuální sítě nebo podsítě do které budou připojeny virtuální počítače Azure po migraci.
11. V **zvýhodněné hybridní využití Azure**:

    - Vyberte **ne** Pokud nechcete použít zvýhodněné hybridní využití Azure. Pak klikněte na tlačítko **Další**.
    - Vyberte **Ano** Pokud máte počítače s Windows serverem, které se vztahuje aktivní Software Assurance nebo smlouvu Services systému Windows Server odběry a chcete použít tuto výhodu pro počítače, kterou migrujete. Pak klikněte na tlačítko **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. V **Compute**, zkontrolujte název virtuálního počítače, velikost, typ disku operačního systému a dostupnosti. Virtuální počítače musí splňovat [požadavky služby Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).

    - **Velikost virtuálního počítače**: Ve výchozím nastavení vybere Azure Migrate migrace serveru velikost podle nejvíce odpovídá v rámci předplatného Azure. Můžete také vybrat ruční velikost v **velikost virtuálního počítače Azure**. 
    - **Disk s operačním systémem**: Zadejte disk s operačním systémem (spouštěcí) pro virtuální počítač. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč operačního systému a instalační služby. 
    - **Skupina dostupnosti**: Pokud virtuální počítač by měl být ve skupině dostupnosti Azure po migraci, určete sadu. Sada musí být v cílové skupině prostředků, kterou zadáte pro migraci.

    ![COMPUTE nastavení](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. V **disky**, určete, jestli disky virtuálního počítače by měla být replikována do Azure a vyberte typ disku (SSD nebo pevný disk nebo premium spravované disky úrovně standard) v Azure. Pak klikněte na tlačítko **Další**.
    - Vyloučení disků z replikace.
    - Pokud se můžete vyloučit disky, nemusí být na virtuálním počítači Azure po migraci. 

    ![Nastavení disku](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. V **zkontrolovat a zahájit replikaci**, zkontrolujte nastavení a klikněte na tlačítko **replikovat** má spustit počáteční replikace pro servery.

> [!NOTE]
> Můžete aktualizovat nastavení replikace kdykoli před zahájením replikace **spravovat** > **replikaci počítačů**. Nastavení nelze změnit, jakmile replikace spustí.



## <a name="track-and-monitor"></a>Sledování a monitorování

- Po kliknutí na **replikovat** začne úloha spuštění replikace. 
- Když úloha spuštění replikace skončí úspěšně, počítačích začít jejich počáteční replikace do Azure.
- Po dokončení počáteční replikace začne rozdílová replikace. Přírůstkové změny, které s místními disky se replikují pravidelně repliky disků v Azure.


Můžete sledovat stav úlohy v oznámeních portálu.

Stav replikace můžete sledovat kliknutím na **replikaci serverů** v **Azure Migrate: Migrace serveru**.
![Monitorování replikace](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílovou replikaci pro virtuální počítače, můžete spustit testovací migrace. před spuštěním úplné migrace do Azure. Důrazně doporučujeme to provést nejméně jednou pro každý počítač, před zahájením migrace.

- Spuštění testu migrace kontroly, které migrace budou fungovat podle očekávání, aniž by to ovlivnilo místních počítačů, které zůstat funkční a pokračovat v replikaci. 
- Migrace test simuluje migrace tím, že vytvoříte virtuální počítač Azure pomocí replikovaných dat (obvykle migrace na virtuální síť li se o neprodukční ve vašem předplatném Azure).
- Můžete ověřit migraci, proveďte testování aplikací pomocí replikovaných testovací virtuální počítač Azure a řešit případné problémy dříve než úplné migrace.

Testovací migrace postupujte následovně:


1. V **cílů migrace** > **servery** > **Azure Migrate: Migrace serveru**, klikněte na tlačítko **Test migrovat servery**.

     ![Testování migrované servery](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač otestovat, a klikněte na **testovací migrace**.

    ![Testovací migrace.](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. V **testovací migrace**, vyberte virtuální síť Azure, ve kterém virtuální počítač Azure se umístí po migraci. Doporučujeme, že používáte-li se o neprodukční virtuální sítě.
4. **Testovací migrace** úlohy spustí. Monitorování úlohy v oznámeních portálu.
5. Po dokončení migrace se zobrazit migrovaný virtuální počítač Azure v **virtuálních počítačů** na webu Azure Portal. Název počítače má příponu **-Test**.
6. Po dokončení testu, klikněte pravým tlačítkem na virtuální počítač Azure v **replikaci počítačů**a klikněte na tlačítko **vyčistěte testovací migrace**.

    ![Vyčištění migrace](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že testovací migrace funguje podle očekávání, můžete migrovat na místních počítačích.

1. V projektu Azure Migrate > **servery** > **Azure Migrate: Migrace serveru**, klikněte na tlačítko **replikaci serverů**.

    ![Replikaci serverů](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. V **replikaci počítačů**, klikněte pravým tlačítkem na virtuální počítač > **migrace**.
3. V **migrace** > **vypnout virtuální počítače a provádět při plánované migraci bez ztráty**vyberte **Ano** > **OK** .
    - Ve výchozím nastavení Azure Migrate místní virtuální počítač vypne a spustí replikaci na vyžádání synchronizovat změny virtuálního počítače, ke kterým došlo od poslední replikace. Tím se zajistí bez ztráty.
    - Pokud nechcete vypnout virtuální počítač, vyberte **ne**
4. Spustí úlohu migrace pro virtuální počítač. Sledování úlohy v Azure oznámení.
5. Po dokončení úlohy můžete zobrazit a spravovat virtuální počítač z **virtuálních počítačů** stránky.

## <a name="complete-the-migration"></a>Dokončení migrace

1. Poté, co migrace dokončí, klikněte pravým tlačítkem na virtuální počítač > **zastavit migraci**. Zastaví se replikace místních počítačů a vyčistí informace o stavu replikace pro virtuální počítač.
2. Nainstalujte virtuální počítač Azure [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) agenta na migrované počítače.
3. Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery.
4. U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
5. Vyjmete provozu na migrované instance virtuálního počítače Azure.
6. Odeberte místní virtuální počítače z místního inventáře virtuálních počítačů.
7. Odeberte místní virtuální počítače ze záloh.
8. Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure. 

## <a name="post-migration-best-practices"></a>Osvědčené postupy po migraci

- Pro zvýšení odolnosti:
    - Zálohujte virtuální počítače Azure pomocí služby Azure Backup, abyste měli data zabezpečená. [Další informace](../backup/quick-backup-vm-portal.md).
    - Replikujte virtuální počítače Azure do sekundární oblasti pomocí služby Site Recovery, aby úlohy mohly neustále běžet a byly dostupné. [Další informace](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Pro zvýšení zabezpečení:
    - Zamknout a omezit příchozí provoz přístup s [Azure Security Center – Správa Just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Omezte síťový provoz na koncové body správy pomocí [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Nasaďte službu [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), která vám pomůže zabezpečit disky a zajistit bezpečnost dat před krádeží a neoprávněným přístupem.
    - Přečtěte si další informace o [zabezpečení prostředků IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) a navštivte [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Pro monitorování a správu:
    - Zvažte nasazení služby [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), která bude monitorovat využití prostředků a útratu.


## <a name="next-steps"></a>Další postup

Prozkoumat [cesty k migraci do cloudu](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) v rámci přechodu na Cloud Azure.
