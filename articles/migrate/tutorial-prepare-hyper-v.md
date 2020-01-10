---
title: Příprava virtuálních počítačů Hyper-V pro posouzení/migraci pomocí Azure Migrate
description: Přečtěte si, jak připravit na posouzení/migraci virtuálních počítačů Hyper-V pomocí Azure Migrate.
ms.topic: tutorial
ms.date: 01/01/2020
ms.custom: mvc
ms.openlocfilehash: a76c249f3d179a34fbb14e6c8bfb3666816fa160
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720204"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Příprava na posouzení a migraci virtuálních počítačů Hyper-V do Azure

Tento článek popisuje, jak připravit na posouzení a migraci místních virtuálních počítačů Hyper-V do Azure s [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Tento kurz je první v řadě, kde se dozvíte, jak vyhodnotit a migrovat virtuální počítače Hyper-V do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte Azure. Nastavte oprávnění pro účet a prostředky Azure pro práci s Azure Migrate.
> * Připravte místní hostitele Hyper-V a virtuální počítače pro vyhodnocování serveru. Můžete připravit pomocí konfiguračního skriptu nebo ručně.
> * Příprava na nasazení Azure Migrate zařízení Zařízení se používá ke zjišťování a vyhodnocení místních virtuálních počítačů.
> * Připravte místní hostitele Hyper-V a virtuální počítače pro migraci serveru.


> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v tématu Postupy pro vyhodnocení a migraci technologie Hyper-V.


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prepare-azure"></a>Příprava Azure

### <a name="azure-permissions"></a>Oprávnění Azure

Je nutné nastavit oprávnění pro nasazení Azure Migrate.

- Oprávnění pro účet Azure k vytvoření projektu Azure Migrate.
- Oprávnění pro váš účet k registraci zařízení Azure Migrate. Zařízení se používá ke zjišťování a hodnocení virtuálních počítačů Hyper-V, které migrujete. Při registraci zařízení Azure Migrate vytvoří dvě aplikace Azure Active Directory (Azure AD), které zařízení jednoznačně identifikují:
    - První aplikace komunikuje s koncovými body služby Azure Migrate.
    - Druhá aplikace přistupuje k Azure Key Vault vytvořenému během registrace, aby se ukládaly informace o aplikaci Azure AD a nastavení konfigurace zařízení.



### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu

Ověřte, že máte oprávnění k vytvoření projektu Azure Migrate.

1. V Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM)** .
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Měli byste mít oprávnění **Přispěvatel** nebo **Owner** .
    - Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného.
    - Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte roli.


### <a name="assign-permissions-to-register-the-appliance"></a>Přiřazení oprávnění k registraci zařízení

Můžete přiřadit oprávnění pro Azure Migrate k vytváření aplikací Azure AD vytvořených během registrace zařízení pomocí jedné z následujících metod:

- Tenant nebo globální správce může udělit oprávnění uživatelům v tenantovi, aby mohli vytvářet a registrovat aplikace služby Azure AD.
- Tenant nebo globální správce může k účtu přiřadit roli vývojáře aplikace (která má oprávnění).

Je potřeba poznamenat, že:

- Aplikace nemají žádná jiná přístupová oprávnění k předplatnému, kromě výše popsaných výše.
- Tato oprávnění budete potřebovat, jenom když zaregistrujete nové zařízení. Po nastavení zařízení můžete oprávnění odebrat.


#### <a name="grant-account-permissions"></a>Udělení oprávnění účtu

Tenant nebo globální správce může udělit oprávnění následujícím způsobem:

1. V Azure AD by měl tenant/globální správce přejít na **Azure Active Directory** > **Uživatelé** > **uživatelských nastavení**.
2. Správce by měl nastavit **Registrace aplikací** **Ano**.

    ![Oprávnění služby Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Přiřazení role vývojáře aplikace

Tenant/globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-hyper-v-for-assessment"></a>Příprava technologie Hyper-V pro posouzení

Hyper-V pro vyhodnocení virtuálních počítačů můžete připravit ručně nebo pomocí konfiguračního skriptu. Tady je seznam toho, co je potřeba připravit:

- Ověřte nastavení hostitele Hyper-V a ujistěte se, že jsou na hostitelích Hyper-V otevřené požadované porty.
- Nastavte pro každého hostitele vzdálenou komunikaci PowerShellu, aby zařízení Azure Migrate mohlo na hostiteli spouštět příkazy PowerShellu přes připojení WinRM.
- Pověření delegáta, pokud se disky virtuálních počítačů nacházejí ve vzdálených sdílených složkách protokolu SMB.
- Nastavte účet, který bude zařízení používat ke zjištění virtuálních počítačů na hostitelích Hyper-V.
- Nastavte integrační služby technologie Hyper-V na každém virtuálním počítači, který chcete zjišťovat a hodnotit.



## <a name="prepare-with-a-script"></a>Příprava pomocí skriptu

Skript provede následující akce:

- Kontroluje, zda spouštíte skript v podporované verzi prostředí PowerShell.
- Ověřuje, že jste (uživatel, který spouští skript), má oprávnění správce na hostiteli Hyper-V.
- Umožňuje vytvořit místní uživatelský účet (nejedná se o správce), který služba Azure Migrate používá ke komunikaci s hostitelem Hyper-V. Tento uživatelský účet se přidá do těchto skupin na hostiteli:
    - Uživatelé vzdálené správy
    - Správci technologie Hyper-V
    - Uživatelé sledování výkonu
- Kontroluje, zda je na hostiteli spuštěná podporovaná verze technologie Hyper-V a role Hyper-V.
- Povolí službu WinRM a na hostiteli otevře porty 5985 (HTTP) a 5986 (HTTPS) (potřebné pro kolekci metadat).
- Povolí vzdálenou komunikaci PowerShellu na hostiteli.
- Kontroluje, zda je na všech virtuálních počítačích spravovaných hostitelem povolena integrační služba technologie Hyper-V.
- V případě potřeby povolí CredSSP v hostiteli.

Spusťte skript následujícím způsobem:

1. Ujistěte se, že na hostiteli Hyper-V máte nainstalovanou verzi prostředí PowerShell verze 4,0 nebo novější.
2. Stáhněte si skript z webu [Microsoft Download Center](https://aka.ms/migrate/script/hyperv). Skript je kryptograficky podepsán společností Microsoft.
3. Ověřte integritu skriptu buď pomocí MD5, nebo souborů SHA256 hash. Hodnoty hashtagu jsou uvedené níže. Spuštěním tohoto příkazu vygenerujte hodnotu hash pro tento skript:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Příklad použití:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.  Po ověření integrity skriptu spusťte skript na každém hostiteli Hyper-V pomocí tohoto příkazu PowerShellu:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Hodnoty hashtagu

Hodnoty hash jsou:

| **Hash** | **Hodnota** |
| --- | --- |
| **ALGORITMY** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-hyper-v-manually"></a>Ruční Příprava technologie Hyper-V

Pomocí postupů v této části můžete ručně připravit technologii Hyper-V namísto použití skriptu.

### <a name="verify-powershell-version"></a>Ověřit verzi PowerShellu

Ujistěte se, že na hostiteli Hyper-V máte nainstalovanou verzi prostředí PowerShell verze 4,0 nebo novější.



### <a name="set-up-an-account-for-vm-discovery"></a>Nastavení účtu pro zjišťování virtuálních počítačů

Azure Migrate potřebuje oprávnění ke zjišťování místních virtuálních počítačů.

- Nastavte doménu nebo místní uživatelský účet s oprávněními správce v hostitelích nebo clusteru Hyper-V.

    - Pro všechny hostitele a clustery, které chcete zahrnout do zjišťování, potřebujete jeden účet.
    - Účet může být místní nebo doménový účet. Doporučujeme, aby měl oprávnění správce na hostitelích nebo clusterech Hyper-V.
    - Případně, pokud nechcete přiřadit oprávnění správce, jsou potřeba následující oprávnění:
        - Uživatelé vzdálené správy
        - Správci technologie Hyper-V
        - Uživatelé sledování výkonu

### <a name="verify-hyper-v-host-settings"></a>Ověřit nastavení hostitele Hyper-V

1. Ověřte [požadavky na hostitele Hyper-V](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) pro vyhodnocování serveru.
2. Ujistěte se, že jsou na hostitelích Hyper-V otevřené [požadované porty](migrate-support-matrix-hyper-v.md#assessment-port-requirements) .

### <a name="enable-powershell-remoting-on-hosts"></a>Povolit vzdálenou komunikaci PowerShellu na hostitelích

Na každém hostiteli nastavte vzdálenou komunikaci PowerShellu následujícím způsobem:

1. Na každém hostiteli otevřete konzolu PowerShellu jako správce.
2. Spusťte tento příkaz:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Povolit integrační služby na virtuálních počítačích

V každém virtuálním počítači by měly být povolené integrační služby, aby Azure Migrate mohl zachytit informace o operačním systému na VIRTUÁLNÍm počítači.

Na virtuálních počítačích, které chcete zjišťovat a hodnotit, povolte na každém virtuálním počítači [integrační služby technologie Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) .


### <a name="enable-credssp-on-hosts"></a>Povolení CredSSP na hostitelích

Pokud má hostitel virtuální počítače s disky, které jsou umístěné ve sdílených složkách SMB, dokončete tento krok na hostiteli.

- Tento příkaz můžete spustit vzdáleně na všech hostitelích Hyper-V.
- Pokud přidáte nové uzly hostitele v clusteru, budou automaticky přidány pro zjišťování, ale v případě potřeby je třeba ručně povolit zprostředkovatele CredSSP v nových uzlech.

Povolte následujícím způsobem:

1. Identifikujte hostitele Hyper-V se spuštěnými virtuálními počítači Hyper-V s disky ve sdílených složkách SMB.
2. U každého identifikovaného hostitele Hyper-V spusťte následující příkaz:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Když zařízení nastavíte, dokončíte nastavení CredSSP [jeho povolením na zařízení](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Tento postup je popsaný v dalším kurzu této série.


## <a name="prepare-for-appliance-deployment"></a>Příprava na nasazení zařízení

Před nastavením zařízení Azure Migrate a zahájením posouzení v dalším kurzu připravte na nasazení zařízení.

1. [Ověřte](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) požadavky na zařízení.
2. [Zkontrolujte](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) adresy URL Azure, ke kterým bude zařízení potřebovat přístup.
3. Zkontrolujte data, která bude zařízení shromažďovat během zjišťování a posouzení.
4. [Poznamenejte si](migrate-support-matrix-hyper-v.md#assessment-port-requirements) požadavky na přístup k portu pro dané zařízení.


## <a name="prepare-for-hyper-v-migration"></a>Příprava na migraci technologie Hyper-V

1. [Kontrola](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) Požadavky na hostitele Hyper-V pro migraci.
2. [Projděte si](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) požadavky na virtuální počítače Hyper-V, které chcete migrovat do Azure.
3. [Poznamenejte](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) si adresy URL Azure, ke kterým hostitelé a clustery Hyper-V potřebují přístup pro migraci virtuálních počítačů.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavte oprávnění účtu Azure.
> * Připraví hostitelé a virtuální počítače Hyper-V pro posouzení a migraci.
> * Připraveno pro nasazení zařízení Azure Migrate.

Přejděte k dalšímu kurzu, abyste vytvořili Azure Migrate projekt, nasadili zařízení a zjistili a vyhodnotili virtuální počítače Hyper-V pro migraci do Azure.

> [!div class="nextstepaction"]
> [Posouzení virtuálních počítačů Hyper-V](./tutorial-assess-hyper-v.md)
