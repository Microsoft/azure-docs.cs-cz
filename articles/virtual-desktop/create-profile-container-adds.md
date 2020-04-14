---
title: Vytvoření kontejneru profilu FSLogix, služba Azure Files Active Directory Domain Services – Azure
description: Tento článek popisuje, jak vytvořit kontejner profilu FSLogix pomocí souborů Azure a služby Azure Active Directory Domain Services.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dd01b950435fadb96a961b6bb1c6b28ff436907a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265769"
---
# <a name="create-an-fslogix-profile-container-with-azure-files"></a>Vytvoření kontejneru profilu FSLogix pomocí souborů Azure

V tomto článku se zobrazí informace o tom, jak vytvořit kontejner profilu FSLogix pomocí souborů Azure a služby Azure Active Directory Domain Services (AD DS).

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste už nastavili instanci Služby Azure AD DS. Pokud ji ještě nemáte, postupujte podle pokynů v části [Nejprve vytvořit základní spravovanou doménu](../active-directory-domain-services/tutorial-create-instance.md) a pak se vraťte sem.

## <a name="add-azure-ad-ds-admins"></a>Přidání správců Azure AD DS

Chcete-li přidat další správce, vytvořte nového uživatele a udělte jim oprávnění.

Přidání správce:

1. Na postranním panelu vyberte **Azure Active Directory,** pak vyberte **Všichni uživatelé**a pak vyberte **Nový uživatel**.

2.  Zadejte podrobnosti o uživateli do polí.

3. V podokně Služby Azure Active Directory na levé straně obrazovky vyberte **Skupiny**.

4. Vyberte skupinu **Správci řadiče domény aadud.**

5. V levém podokně vyberte **Členové**a v hlavním podokně vyberte **Přidat členy.** Zobrazí se seznam všech uživatelů dostupných ve službě Azure AD. Vyberte název uživatelského profilu, který jste právě vytvořili.

## <a name="set-up-an-azure-storage-account"></a>Nastavení účtu Úložiště Azure

Teď je čas povolit ověřování Azure AD DS přes blok zpráv serveru (SMB). 

Povolení ověřování:

1. Pokud jste to ještě neudělali, nastavte a nasaďte účet úložiště Azure pro obecné účely v2 podle pokynů v [části Vytvoření účtu úložiště Azure](../storage/common/storage-account-create.md).

2. Po dokončení nastavení účtu vyberte **Přejít na zdroj**.

3. V yberte **Konfigurace** z podokna na levé straně obrazovky a pak povolte **ověřování Azure Active Directory pro soubory Azure** v hlavním podokně. Jakmile budete mít hotovo, vyberte **Uložit**.

4. V podokně na levé straně obrazovky vyberte **Přehled** a v hlavním podokně vyberte **Soubory.**

5. Vyberte **Sdílená složka souboru** a do polí, která se zobrazí na pravé straně obrazovky, zadejte **název** a **kvótu.**

## <a name="assign-access-permissions-to-an-identity"></a>Přiřazení přístupových oprávnění k identitě

Ostatní uživatelé budou potřebovat přístupová oprávnění pro přístup ke sdílené složce. Chcete-li to provést, budete muset přiřadit každému uživateli roli s příslušnými přístupovými oprávněními.

Přiřazení přístupových oprávnění uživatelům:

1. Na webu Azure Portal otevřete sdílenou složku, kterou jste vytvořili v [části Nastavení účtu Úložiště Azure](#set-up-an-azure-storage-account).

2. Vyberte **řízení přístupu (IAM).**

3. Vyberte **Přidat přiřazení role**.

4. Na kartě **Přidat přiřazení role** vyberte příslušnou předdefinovanou roli ze seznamu rolí. Chcete-li získat správná oprávnění, musíte pro účet alespoň vybrat **přispěvatele sdílení smb souboru úložiště.**

5. **Chcete-li přiřadit přístup k aplikaci**, vyberte **položku Uživatel, skupina nebo instanční objekt služby Azure Active Directory**.

6. Vyberte jméno nebo e-mailovou adresu pro cílovou identitu služby Azure Active Directory.

7. Vyberte **Uložit**.

## <a name="get-the-storage-account-access-key"></a>Získání přístupového klíče účtu úložiště

Dále budete muset získat přístupový klíč pro svůj účet úložiště.

Jak získat přístupový klíč účtu úložiště:

1. Na postranním panelu portálu Azure vyberte **Účty úložiště**.

2. Ze seznamu účtů úložiště vyberte účet, pro který jste povolili Azure AD DS a vytvořili vlastní role v krocích výše.

3. V části **Nastavení**vyberte **přístupové klávesy** a zkopírujte klíč z **klávesy 1**.

4. Přejděte na kartu **Virtuální počítače** a vyhledejte libovolný virtuální počítač, který se stane součástí fondu hostitelů.

5. Vyberte název virtuálního počítače (VM) v části **Virtuální počítače (adVM)** a vyberte **Připojit**

    Tím stáhnete soubor RDP, který vám umožní přihlásit se k virtuálnímu virtuálnímu virtuálnímu serveru s vlastními přihlašovacími údaji.

    ![Snímek obrazovky s kartou RDP okna Připojit k virtuálnímu počítači](media/rdp-tab.png)

6. Až se přihlásíte k virtuálnímu virtuálnímu provozu, spusťte příkazový řádek jako správce.

7. Spusťte následující příkaz:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - Nahraďte `<desired-drive-letter>` písmenem jednotky podle vašeho `y:`výběru (například).
    - Nahraďte všechny `<storage-account-name>` instance s názvem účtu úložiště, který jste zadali dříve.
    - Nahraďte `<share-name>` název sdílené složky, kterou jste vytvořili dříve.
    - Nahraďte `<storage-account-key>` klíčem účtu úložiště z Azure.

    Příklad:  
  
     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Spusťte následující příkaz a udělte uživateli úplný přístup ke sdílené složce Azure Files.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - Nahraďte `<mounted-drive-letter>` písmenem jednotky, kterou má uživatel použít.
    - Nahraďte `<user-email>` hlavní název uživatele, který bude používat tento profil pro přístup k hostitelským virtuálním uživatelům relace.

    Příklad:
     
     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>Vytvoření kontejneru profilů

Teď, když jsou vaše profily připravené k použití, vytvoříme kontejner profilu FSLogix.

Postup konfigurace kontejneru profilu FSLogix:

1. Přihlaste se k virtuálnímu virtuálnímu směrovači hostitele relace, který jste nakonfigurovali na začátku tohoto článku, [a potom stáhněte a nainstalujte agenta FSLogix](/fslogix/install-ht/).

2. Rozbalte stažený soubor agenta FSLogix a přejděte na verzi **x64** > **Releases**a spusťte soubor **FSLogixAppsSetup.exe**.

3. Jakmile se instalátor spustí, vyberte **Souhlasím s licenčními podmínkami.** Pokud je to možné, zadejte nový klíč.

4. Vyberte **Install** (Nainstalovat).

5. Otevřete **jednotku C**a pak přejděte na **programfiles** > **FSLogix** > **Apps** a ujistěte se, že byl agent FSLogix správně nainstalován.

     >[!NOTE]
     > Pokud je ve fondu hostitelů více virtuálních počítačích, budete muset opakovat kroky 1 až 5 pro každý virtuální počítač.

6. Spusťte **Editor registru** (RegEdit) jako správce.

7. Přejděte na **položku** > **Počítačová HKEY_LOCAL_MACHINE** > **software** > **FSLogix**, klepněte pravým tlačítkem myši na **položku FSLogix**, vyberte možnost **Nový**a potom vyberte možnost **Klíč**.

8. Vytvořte nový klíč s názvem **Profily**.

9.  Klikněte pravým tlačítkem myši na **Profily**, vyberte **Nový**a pak vyberte **Hodnotu DWORD (32bitová).** Pojmenujte hodnotu **Enabled** a nastavte hodnotu **Data** na **hodnotu 1**.

    ![Snímek obrazovky s klíčem Profily. Soubor REG_DWORD je zvýrazněn a jeho hodnota Data je nastavena na hodnotu 1.](media/dword-value.png)

10. Klepněte pravým tlačítkem myši na **položku Profily**, vyberte **nový**a pak vyberte **hodnotu více řetězců**. Pojmenujte hodnotu **VHDLocations** a nastavte zadejte identifikátor URI pro sdílenou složku `\\fsprofile.file.core.windows.net\share` Souborů Azure jako hodnotu Data.

    ![Snímek obrazovky s klávesou Profily zobrazující soubor VHDLocations. Jeho hodnota Data zobrazuje identifikátor URI pro sdílenou složku Souborů Azure.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Přiřazení uživatelů k hostiteli relace

Nyní budete muset přiřadit uživatele k hostiteli relace.

Přiřazení uživatelů:

1. Spusťte prostředí Windows PowerShell jako správce a pak spusťte následující rutinu pro přihlášení k prostředí Windows Virtual Desktop pomocí prostředí PowerShell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Po zobrazení výzvy k zadání pověření zadejte stejného uživatele, kterému byla udělena role TenantCreator, RdS Owner nebo RdS Contributor v tenantovi virtuální plochy Windows.

2. Spusťte následující rutiny a přiřaďte uživatele ke skupině vzdálené plochy:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Stejně jako předchozí rutiny, ujistěte se, že nahradit `<your-wvd-tenant>`, `<wvd-pool>`a `<user-principal>` s příslušnými hodnotami.

    Příklad:

     ```powershell
     $pool1 = "contoso"
     
     $tenant = "contoso"
     
     $appgroup = "Desktop Application Group"
     
     $user1 = "jane.doe@contoso.com"
     
     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Ujistěte se, že váš profil funguje

Nyní vše, co musíte udělat, je ujistit se, že profil, který jste vytvořili, existuje a funguje tak, jak má.

Ověření profilu:

1. Otevřete prohlížeč a přejděte do [webového klienta Windows Virtual Desktop](https://rdweb.wvd.microsoft.com/webclient/index.html).

2. Přihlaste se pomocí uživatelského účtu přiřazeného ke skupině Vzdálená plocha.

3. Po navázání relace uživatele otevřete portál Azure a přihlaste se pomocí účtu pro správu.

4. Na postranním panelu vyberte **Účty úložiště**.

5. Vyberte účet úložiště, který jste nakonfigurovali jako sdílenou složku pro fond hostitelů relací a povolili ho pomocí služby Azure AD DS.

6. Vyberte ikonu **Soubory** a rozbalte sdílenou složku.

    Pokud je vše nastaveno správně, měli byste vidět **adresář** s názvem, `<user SID>-<username>`který je formátován takto: .

## <a name="next-steps"></a>Další kroky

Pokud hledáte alternativní způsoby, jak vytvořit kontejnery profilu FSLogix, podívejte se na následující články:

- [Vytvořte kontejner profilu pro fond hostitelů pomocí sdílené složky](create-host-pools-user-profile.md).
- [Vytvoření kontejneru profilu FSLogix pro fond hostitelů pomocí souborů Azure NetApp](create-fslogix-profile-container.md)

Podrobnější informace o konceptech souvisejících s kontejnery FSlogix pro soubory Azure najdete v [kontejnerech profilů FSLogix a souborech Azure](fslogix-containers-azure-files.md).
