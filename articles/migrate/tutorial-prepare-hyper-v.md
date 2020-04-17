---
title: Příprava virtuálních počítačů Hyper-V na vyhodnocení a migraci pomocí migrace Azure
description: Zjistěte, jak se připravit na vyhodnocení/migraci virtuálních počítačích Hyper-V pomocí Migrace Azure.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: e86e634812e9f2782a003a11b48cfecf91d95359
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535294"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Příprava na vyhodnocení a migraci virtuálních počítačů Hyper-V do Azure

Tento článek popisuje, jak se připravit na vyhodnocení místních virtuálních počítačů Hyper-V pomocí Azure Migrate:Server Assessment (migrate-services-overview.md#azure-migrate-server-assessment-tool) a migraci virtuálních počítačů Hyper-V s [migrací Azure:Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool).


Tento kurz je první z řady, která ukazuje, jak posoudit a migrovat hypervirtuální počítače do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte Azure. Nastavte oprávnění pro svůj účet Azure a prostředky pro práci s Azure Migrate.
> * Připravte místní hostitele Hyper-V a virtuální chod y pro vyhodnocení serveru. Můžete připravit pomocí konfiguračního skriptu nebo ručně.
> * Připravte se na nasazení zařízení Azure Migrate. Zařízení se používá ke zjišťování a vyhodnocovat místní virtuální počítač.
> * Připravte místní hostitele Hyper-V a virtuální chody pro migraci serveru.


> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář, takže můžete rychle nastavit proof-of-concept. Kurzy používají výchozí možnosti tam, kde je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v návodech pro hodnocení a migraci technologie Hyper-V.


Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.


## <a name="prepare-azure"></a>Příprava Azure

### <a name="azure-permissions"></a>Oprávnění Azure

Potřebujete nastavit oprávnění pro nasazení Migrace Azure.

**Úkol** | **Podrobnosti** 
--- | --- 
**Vytvoření projektu migrace Azure** | Váš účet Azure potřebuje oprávnění Contributer nebo Owner k vytvoření projektu. | 
**Registrace poskytovatelů prostředků** | Azure Migrate používá zjednodušené zařízení Azure Migrate ke zjišťování a vyhodnocování virtuálních počítačích Hyper-V pomocí Azure Migrate Server Assessment.<br/><br/> Během registrace zařízení jsou poskytovatelé prostředků registrováni s předplatným zvoleným v zařízení. [Další informace](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Chcete-li zaregistrovat zprostředkovatele prostředků, potřebujete roli přispěvatele nebo vlastníka v předplatném.
**Vytvoření aplikace Azure AD** | Při registraci zařízení Azure Migrate vytvoří aplikaci Azure Active Directory (Azure AD), která se používá pro komunikaci mezi agenty spuštěnými na zařízení s jejich příslušnými službami spuštěnými v Azure. [Další informace](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Potřebujete oprávnění k vytváření aplikací Azure AD (k dispozici v roli Application Developer).



### <a name="assign-permissions-to-create-project"></a>Přiřazení oprávnění k vytvoření projektu

Zkontrolujte, zda máte oprávnění k vytvoření projektu Migrace Azure.

1. Na webu Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM).**
2. V **části Zkontrolovat přístup**vyhledejte příslušný účet a kliknutím na něj zobrazte oprávnění.
3. Měli byste mít oprávnění **přispěvatele** nebo **vlastníka.**
    - Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem předplatného.
    - Pokud nejste vlastníkem předplatného, spolupracujte s vlastníkem a přiřaďte roli.


### <a name="assign-permissions-to-register-the-appliance"></a>Přiřazení oprávnění k registraci zařízení

Můžete přiřadit oprávnění pro Azure Migrate k vytvoření aplikace Azure AD během registrace zařízení pomocí jedné z následujících metod:

- Klient/globální správce můžete udělit oprávnění uživatelům v tenantovi, k vytvoření a registraci aplikací Azure AD.
- Klient/globální správce můžete přiřadit role Vývojář aplikace (která má oprávnění) k účtu.

> [!NOTE]
> - Aplikace nemá žádná jiná přístupová oprávnění k předplatnému, než jsou popsána výše.
> - Tato oprávnění potřebujete pouze při registraci nového zařízení. Po nastavení zařízení můžete odebrat oprávnění.


#### <a name="grant-account-permissions"></a>Udělit oprávnění k účtu

Klient/globální správce může udělit oprávnění následujícím způsobem:

1. Ve službě Azure AD by měl klient/globální správce přejít na**nastavení uživatelů****služby** >  **Azure Active Directory** > .
2. Správce by měl nastavit **registrace aplikací** na **Ano**.

    ![Oprávnění Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Přiřadit roli vývojáře aplikací

Klient/globální správce můžete přiřadit roli vývojáře aplikací k účtu. [Další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-hyper-v-for-assessment"></a>Příprava technologie Hyper-V k posouzení

Můžete připravit Hyper-V pro vyhodnocení virtuálního počítače ručně nebo pomocí konfiguračního skriptu. Přípravné kroky jsou následující:
- [Ověřit](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) Nastavení hostitele Technologie Hyper-V a ujistěte se, že [požadované porty](migrate-support-matrix-hyper-v.md#port-access) jsou otevřené na hostitelích Hyper-V.
- Nastavte vzdálené hlasování prostředí PowerShell u každého hostitele, aby zařízení Azure Migrate mohlo na hostiteli spouštět příkazy Prostředí PowerShell přes připojení WinRM.
- Delegujte pověření, pokud jsou disky virtuálních počítačů umístěné ve vzdálených sdílených složek SMB.
- Nastavte účet, který zařízení použije ke zjišťování virtuálních počítačů na hostitelích Hyper-V.
- Nastavte služby integrace Technologie Hyper-V na každém virtuálním počítači, který chcete zjistit a vyhodnotit. Výchozí nastavení při povolení integračních služeb jsou dostatečné pro Azure Migrate.

    ![Povolit integrační služby](./media/tutorial-prepare-hyper-v/integrated-services.png)


## <a name="prepare-with-a-script"></a>Příprava pomocí skriptu

Skript provádí následující akce:

- Zkontroluje, zda používáte skript v podporované verzi PowerShellu.
- Ověří, že vy (uživatel spouštějící skript) máte oprávnění správce na hostiteli Hyper-V.
- Umožňuje vytvořit místní uživatelský účet (nikoli správce), který služba Azure Migrate používá ke komunikaci s hostitelem Hyper-V. Tento uživatelský účet je přidán do těchto skupin na hostiteli:
    - Uživatelé vzdálené správy
    - Hyper-V Administrators
    - Uživatelé sledování výkonu
- Zkontroluje, zda hostitel používá podporovanou verzi technologie Hyper-V a roli Hyper-V.
- Povolí službu WinRM a otevře porty 5985 (HTTP) a 5986 (HTTPS) na hostiteli (potřebné pro shromažďování metadat).
- Povolí vzdálené hlasování prostředí PowerShell na hostiteli.
- Zkontroluje, zda je služba Integrace Hyper-V povolena na všech virtuálních počítačích spravovaných hostitelem.
- V případě potřeby povolí credssp na hostiteli.

Spusťte skript takto:

1. Ujistěte se, že máte na hostiteli Hyper-V nainstalovanou powershellovou verzi 4.0 nebo novější.
2. Stáhněte si skript ze služby [Stažení softwaru](https://aka.ms/migrate/script/hyperv). Skript je kryptograficky podepsán společností Microsoft.
3. Ověřte integritu skriptu pomocí souborů hash MD5 nebo SHA256. Hodnoty hashtagů jsou uvedeny níže. Spusťte tento příkaz a vygenerujte hash skriptu:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Příklad použití:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.    Po ověření integrity skriptu spusťte skript na každém hostiteli Technologie Hyper-V pomocí tohoto příkazu prostředí PowerShell:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Hodnoty hashtagu

Hodnoty hash jsou:

| **Hodnota hash** | **Hodnotu** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **ŠA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-manually"></a>Ruční příprava

Postupujte podle postupů v této části připravit Hyper-V ručně, namísto použití skriptu.

### <a name="verify-powershell-version"></a>Ověření verze PowerShellu

Ujistěte se, že máte na hostiteli Hyper-V nainstalovanou powershellovou verzi 4.0 nebo novější.



### <a name="set-up-an-account-for-vm-discovery"></a>Nastavení účtu pro zjišťování virtuálních účtů

Azure Migrate potřebuje oprávnění ke zjišťování místních virtuálních počítačů.

- Nastavte doménu nebo místní uživatelský účet s oprávněními správce u hostitelů/clusteru Hyper-V.

    - Potřebujete jeden účet pro všechny hostitele a clustery, které chcete zahrnout do zjišťování.
    - Účet může být místní nebo doménový účet. Doporučujeme, aby měl oprávnění správce pro hostitele nebo clustery Hyper-V.
    - Pokud nechcete přiřazovat oprávnění správce, jsou zapotřebí následující oprávnění:
        - Uživatelé vzdálené správy
        - Hyper-V Administrators
        - Uživatelé sledování výkonu

### <a name="verify-hyper-v-host-settings"></a>Ověření nastavení hostitele technologie Hyper-V

1. Ověřte [požadavky hostitele Technologie Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) pro vyhodnocení serveru.
2. Ujistěte se, že [požadované porty](migrate-support-matrix-hyper-v.md#port-access) jsou otevřené na hostitelích Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Povolení vzdálené komunikace prostředí PowerShell u hostitelů

Nastavte vzdálené hlasování prostředí PowerShell u každého hostitele takto:

1. Na každém hostiteli otevřete jako správce konzolu PowerShellu.
2. Spusťte tento příkaz:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Povolení integračních služeb na virtuálních počítačích

Integrační služby by měly být povolené na každém virtuálním počítači, aby Azure Migrate mohl zachytit informace o operačním systému na virtuálním počítači.

Na virtuálních počítačích, které chcete zjistit a vyhodnotit, povolte [služby integrace Technologie Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) na každém virtuálním počítači.


### <a name="enable-credssp-on-hosts"></a>Povolit credSSP na hostitelích

Pokud má hostitel virtuální počítače s disky umístěné ve sdílených složek SMB, dokončete tento krok na hostiteli.

- Tento příkaz můžete spustit vzdáleně na všech hostitelích Hyper-V.
- Pokud přidáte nové hostitelské uzly v clusteru, budou automaticky přidány pro zjišťování, ale budete muset ručně povolit CredSSP na nové uzly v případě potřeby.

Povolit takto:

1. Identifikujte hostitele Technologie Hyper-V se spuštěnými virtuálními počítači Hyper-V s disky ve sdílených složek SMB.
2. Na každém identifikovaném hostiteli technologie Hyper-V spusťte následující příkaz:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Při nastavování spotřebiče dokončete nastavení CredSSP [povolením přístroje](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). To je popsáno v dalším kurzu v této sérii.


## <a name="prepare-for-appliance-deployment"></a>Příprava na nasazení zařízení

Před nastavením zařízení Azure Migrate a počátečním hodnocením v dalším kurzu se připravte na nasazení zařízení.

1. [Ověřte](migrate-appliance.md#appliance---hyper-v) požadavky na spotřebič.
2. Zkontrolujte adresy URL Azure, ke kterým bude zařízení potřebovat přístup ve [veřejných](migrate-appliance.md#public-cloud-urls) a [vládních](migrate-appliance.md#government-cloud-urls) cloudech. Pokud používáte bránu firewall nebo proxy server založený na adrese URL, ujistěte se, že umožňuje přístup k požadovaným adresám URL.
3. Zkontrolujte data, která bude zařízení shromažďovat během zjišťování a hodnocení.
4. [Zkontrolujte](migrate-appliance.md#collected-data---hyper-v) požadavky na přístup k portu pro zařízení.




## <a name="prepare-for-hyper-v-migration"></a>Příprava na migraci hyperv

1. [Recenze](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Požadavky na hostitele Hyper-V pro migraci a adresy URL Azure, ke kterým hostitelé a clustery Hyper-V potřebují přístup k migraci virtuálních počítačů.
2. [Projděte si](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) požadavky na virtuální počítače Hyper-V, které chcete migrovat do Azure.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavte oprávnění účtu Azure.
> * Připravené hyper-V hostitele a virtuálních discích pro hodnocení a migraci.
> * Připraveno pro nasazení zařízení Azure Migrate.

Pokračujte k dalšímu kurzu k vytvoření projektu Migrace Azure, nasazení zařízení a zjišťování a vyhodnocování virtuálních počítačích Hyper-V pro migraci do Azure.

> [!div class="nextstepaction"]
> [Posouzení virtuálních měn Hyper-V](./tutorial-assess-hyper-v.md)
