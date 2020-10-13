---
title: Vytvoření Active Directory Domain Services kontejnerů profilů FSLogix Azure Files – Azure
description: Tento článek popisuje, jak vytvořit kontejner profilu FSLogix se soubory Azure a Azure Active Directory Domain Services.
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ea834ed874f3011d95f8b924df860576f72bc4ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88825609"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>Vytvoření kontejneru profilu se službou Azure Files a Azure služba AD DS

Tento článek vám ukáže, jak vytvořit kontejner profilu FSLogix pomocí souborů Azure a Azure Active Directory Domain Services (služba AD DS).

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste už nastavili instanci služby Azure služba AD DS. Pokud ho ještě nemáte, postupujte podle pokynů v části [Vytvoření základní spravované domény](../active-directory-domain-services/tutorial-create-instance.md) a potom se vraťte sem.

## <a name="add-azure-ad-ds-admins"></a>Přidat správce Azure služba AD DS

Pokud chcete přidat další správce, vytvořte nového uživatele a udělte mu oprávnění.

Přidání správce:

1. Na bočním panelu vyberte **Azure Active Directory** a pak vyberte **Všichni uživatelé**a pak vyberte **Nový uživatel**.

2.  Zadejte do polí Podrobnosti o uživateli.

3. V podokně Azure Active Directory na levé straně obrazovky vyberte **skupiny**.

4. Vyberte skupinu **AAD DC Administrators** .

5. V levém podokně vyberte **členy**a potom v hlavním podokně vyberte **přidat členy** . Zobrazí se seznam všech uživatelů dostupných ve službě Azure AD. Vyberte název profilu uživatele, který jste právě vytvořili.

## <a name="set-up-an-azure-storage-account"></a>Nastavení účtu Azure Storage

Teď je čas povolit ověřování Azure služba AD DS přes protokol SMB (Server Message Block).

Povolení ověřování:

1. Pokud jste to ještě neudělali, nastavte a nasaďte účet pro obecné účely v2 Azure Storage podle pokynů uvedených v části [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md).

2. Až dokončíte nastavování účtu, vyberte **Přejít k prostředku**.

3. V podokně na levé straně obrazovky vyberte **Konfigurace** a potom povolte **Azure Active Directory ověřování pro soubory Azure** v hlavním podokně. Jakmile budete mít hotovo, vyberte **Uložit**.

4. V podokně na levé straně obrazovky vyberte **Přehled** a pak v hlavním podokně vyberte **soubory** .

5. Vyberte **sdílení souborů** a zadejte **název** a **kvótu** do polí, která se zobrazí na pravé straně obrazovky.

## <a name="assign-access-permissions-to-an-identity"></a>Přiřazení přístupových oprávnění k identitě

Jiní uživatelé budou potřebovat přístupová oprávnění pro přístup ke sdílené složce souborů. K tomu je potřeba přiřadit každému uživateli roli s příslušnými přístupovými oprávněními.

Přiřazení přístupových oprávnění uživatelům:

1. V Azure Portal otevřete sdílenou složku, kterou jste vytvořili v části [Nastavení účtu Azure Storage](#set-up-an-azure-storage-account).

2. Vyberte **Access Control (IAM)**.

3. Vyberte **Přidat přiřazení role**.

4. Na kartě **Přidat přiřazení role** vyberte v seznamu role příslušnou integrovanou roli. Abyste získali správná oprávnění, musíte aspoň vybrat **soubor úložiště datový Přispěvatel SMB** pro tento účet.

5. Pro **přiřazení přístupu k**vyberte **Azure Active Directory uživatel, skupina nebo instanční objekt**.

6. Vyberte jméno nebo e-mailovou adresu pro cílovou Azure Active Directory identitu.

7. Vyberte **Uložit**.

## <a name="get-the-storage-account-access-key"></a>Získání přístupového klíče účtu úložiště

V dalším kroku budete muset získat přístupový klíč pro váš účet úložiště.

Získání přístupového klíče účtu úložiště:

1. Na bočním panelu Azure Portal vyberte **účty úložiště**.

2. V seznamu účtů úložiště vyberte účet, pro který jste povolili Azure služba AD DS a ve výše uvedených krocích jste vytvořili vlastní role.

3. V části **Nastavení**vyberte **přístupové klíče** a zkopírujte klíč z **klíč1**.

4. Přejděte na kartu **Virtual Machines** a vyhledejte libovolný virtuální počítač, který se stane součástí fondu hostitelů.

5. Vyberte název virtuálního počítače (VM) v části **Virtual Machines (adVM)** a vyberte **připojit** .

    Tím se stáhne soubor RDP, který vám umožní přihlásit se k virtuálnímu počítači pomocí vlastních přihlašovacích údajů.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky karty RDP okna připojit k virtuálnímu počítači](media/rdp-tab.png)

6. Až se přihlásíte k virtuálnímu počítači, spusťte příkazový řádek jako správce.

7. Spusťte následující příkaz:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - Nahraďte `<desired-drive-letter>` zvoleným písmenem jednotky (například `y:` ).
    - Nahraďte všechny výskyty `<storage-account-name>` názvem účtu úložiště, který jste zadali dříve.
    - Nahraďte `<share-name>` názvem sdílené složky, kterou jste vytvořili dříve.
    - Nahraďte `<storage-account-key>` klíčem účtu úložiště z Azure.

    Například:

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Spuštěním následujících příkazů Umožněte uživatelům virtuálních počítačů s Windows vytvářet vlastní kontejner profilů a přitom blokovat přístup k kontejnerům profilů z jiných uživatelů.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - Nahraďte `<mounted-drive-letter>` písmenem jednotky, kterou jste použili k namapování jednotky.
    - Nahraďte `<user-email>` hlavní název uživatele nebo skupiny Active Directory, který obsahuje uživatele, kteří budou potřebovat přístup ke sdílené složce.

    Například:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>Vytvoření kontejneru profilů

Teď, když jsou vaše profily připravené k použití, vytvoříme kontejner profilu FSLogix.

Postup konfigurace kontejneru profilu FSLogix:

1. Přihlaste se k virtuálnímu počítači hostitele relace, který jste nakonfigurovali na začátku tohoto článku, [a pak stáhněte a nainstalujte agenta FSLogix](/fslogix/install-ht/).

2. Rozbalte stažený soubor agenta FSLogix, klikněte na verze **x64**a  >  **Releases**pak otevřete **FSLogixAppsSetup.exe**.

3. Jakmile se instalační program spustí, vyberte Souhlasím **s licenčními podmínkami a ujednáními.** V případě potřeby zadejte nový klíč.

4. Vyberte **Nainstalovat**.

5. Otevřete **jednotku C**a pak vyhledejte **soubory Program Files**  >  **FSLogix**  >  **Apps** a ujistěte se, že je agent FSLogix správně nainstalovaný.

     >[!NOTE]
     > Pokud je ve fondu hostitelů víc virtuálních počítačů, budete muset pro každý virtuální počítač zopakovat kroky 1 až 5.

6. Spusťte **Editor registru** (Regedit) jako správce.

7. Přejděte na **počítač**  >  **HKEY_LOCAL_MACHINE**  >  **software**  >  **FSLogix**, klikněte pravým tlačítkem na **FSLogix**, vyberte **Nový**a pak vyberte **klíč**.

8. Vytvořte nový klíč s názvem **Profiles**.

9.  Klikněte pravým tlačítkem na **profily**, vyberte **Nový**a pak vyberte **hodnotu DWORD (32-bit).** Pojmenujte hodnotu **Enabled** a nastavte hodnotu **data** na **1**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s klíčem Profile REG_DWORD soubor je zvýrazněný a jeho datová hodnota je nastavená na 1.](media/dword-value.png)

10. Klikněte pravým tlačítkem na **profily**, vyberte **Nový**a potom vyberte **hodnotu s více řetězci**. Pojmenujte hodnotu **VHDLocations** a `\\fsprofile.file.core.windows.net\share` jako datovou hodnotu zadejte identifikátor URI pro sdílenou složku souborů Azure.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s klíčem Profiles, který ukazuje soubor VHDLocations Jeho hodnota dat zobrazuje identifikátor URI pro sdílenou složku služby soubory Azure.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Přiřazení uživatelů k hostiteli relace

Teď budete muset přiřadit uživatele k hostiteli relace.

Přiřazení uživatelů:

1. Spusťte Windows PowerShell jako správce a pak spuštěním následující rutiny se přihlaste k virtuálnímu počítači s Windows pomocí PowerShellu:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Po zobrazení výzvy k zadání přihlašovacích údajů zadejte stejného uživatele, kterému byla udělena role TenantCreator, RDS Owner nebo RDS Přispěvatel v tenantovi virtuálních klientů Windows.

2. Spuštěním následujících rutin přiřaďte uživatele ke skupině Vzdálená plocha:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Stejně jako v předchozích rutinách Nezapomeňte nahradit `<your-wvd-tenant>` , `<wvd-pool>` a `<user-principal>` s odpovídajícími hodnotami.

    Například:

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Ujistěte se, že váš profil funguje

Nyní stačí, abyste se ujistili, že profil, který jste vytvořili, existuje a funguje tak, jak má.

Ověření profilu:

1. Otevřete prohlížeč a navštivte [webového klienta pro virtuální počítače s Windows](https://rdweb.wvd.microsoft.com/arm/webclient).

2. Přihlaste se pomocí uživatelského účtu přiřazeného ke skupině Vzdálená plocha.

3. Jakmile je uživatelská relace navázána, otevřete Azure Portal a přihlaste se pomocí účtu správce.

4. Na bočním panelu vyberte **účty úložiště**.

5. Vyberte účet úložiště, který jste nakonfigurovali jako sdílenou složku pro fond hostitelů relací a povolili v Azure služba AD DS.

6. Vyberte ikonu **soubory** a potom rozbalte sdílenou složku.

    Pokud je všechno správně nastavené, měl by se zobrazit **adresář** s názvem, který se naformátoval takto: `<user SID>-<username>` .

## <a name="next-steps"></a>Další kroky

Pokud hledáte alternativní způsoby vytváření kontejnerů profilů FSLogix, přečtěte si následující články:

- [Vytvořte kontejner profilu pro fond hostitelů s použitím sdílené složky](create-host-pools-user-profile.md).
- [Vytvoření kontejneru profilu FSLogix pro fond hostitelů pomocí Azure NetApp Files](create-fslogix-profile-container.md)

Podrobnější informace o konceptech týkajících se FSlogix kontejnerů pro soubory Azure najdete v [kontejnerech profilů FSlogix a souborech Azure](fslogix-containers-azure-files.md).
