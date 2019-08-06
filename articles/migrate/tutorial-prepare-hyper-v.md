---
title: Příprava virtuálních počítačů Hyper-V pro posouzení a migraci do Azure pomocí Azure Migrate | Microsoft Docs
description: V této části najdete popis postupu přípravy na posouzení a migraci virtuálních počítačů Hyper-V do Azure pomocí Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 991fbe97582279fdeb996ddebe8e982c4adf3036
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827280"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Příprava na posouzení a migraci virtuálních počítačů Hyper-V do Azure

Tento článek popisuje, jak připravit na posouzení a migraci místních virtuálních počítačů Hyper-V do Azure s [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. 

Tento kurz je první v řadě, kde se dozvíte, jak vyhodnotit a migrovat virtuální počítače Hyper-V do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte Azure. Nastavte oprávnění pro účet a prostředky Azure pro práci s Azure Migrate.
> * Připravte místní hostitele Hyper-V a virtuální počítače pro vyhodnocování serveru.
> * Připravte místní hostitele Hyper-V a virtuální počítače pro migraci serveru.


> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v tématu Postupy pro vyhodnocení a migraci technologie Hyper-V.


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prepare-azure"></a>Příprava Azure

### <a name="azure-permissions"></a>Oprávnění Azure

Je nutné nastavit oprávnění pro nasazení Azure Migrate.

- Oprávnění pro účet Azure k vytvoření projektu Azure Migrate. 
- Oprávnění pro váš účet k registraci zařízení Azure Migrate. Zařízení se používá pro zjišťování a migraci technologie Hyper-V. Při registraci zařízení Azure Migrate vytvoří dvě aplikace Azure Active Directory (Azure AD), které zařízení jednoznačně identifikují:
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

Pomocí jedné z následujících metod můžete přiřadit oprávnění pro Azure Migrate k vytváření aplikací Azure AD, které vytváří během registrace zařízení.

- Tenant nebo globální správce může udělit oprávnění uživatelům v tenantovi, aby mohli vytvářet a registrovat aplikace služby Azure AD.
- Tenant nebo globální správce může k účtu přiřadit roli vývojáře aplikace (která má oprávnění).

Je potřeba poznamenat, že:

- Aplikace nemají žádná jiná přístupová oprávnění k předplatnému, kromě výše popsaných výše.
- Tato oprávnění budete potřebovat, jenom když zaregistrujete nové zařízení. Po nastavení zařízení můžete oprávnění odebrat. 


#### <a name="grant-account-permissions"></a>Udělení oprávnění účtu

Tenant nebo globální správce může udělit oprávnění následujícím způsobem:

1. V Azure AD by měl tenant nebo globální správce přejít na **Azure Active Directory** > **uživatelských nastavení** **uživatelů** > .
2. Správce by měl nastavit **Registrace aplikací** **Ano**.

    ![Oprávnění služby Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, které není citlivé. [Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Přiřazení role vývojáře aplikace 

Tenant/globální správce může přiřadit roli vývojář aplikace k účtu. [Další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-hyper-v-assessment"></a>Příprava na posouzení technologie Hyper-V

Při přípravě na posouzení technologie Hyper-V postupujte následovně:

1. Ověřte nastavení hostitele Hyper-V.
2. Nastavte pro každého hostitele vzdálenou komunikaci PowerShellu, aby zařízení Azure Migrate mohlo na hostiteli spouštět příkazy PowerShellu přes připojení WinRM.
3. Pokud se disky virtuálních počítačů nacházejí ve vzdálených úložištích SMB, je nutné delegování přihlašovacích údajů. 
    - Povolte delegování CredSSP, aby zařízení Azure Migrate mohlo fungovat jako klient a delegování přihlašovacích údajů na hostitele. T
    - Povolíte každému hostiteli, aby fungoval jako delegát pro zařízení, jak je popsáno níže.
    - Později při nastavení zařízení budete na zařízení umožňovat delegování.
4. Zkontrolujte požadavky na zařízení a přístup k adrese URL/portu potřebný pro dané zařízení.
5. Nastavte účet, který bude zařízení používat pro zjišťování virtuálních počítačů.
6. Nastavte integrační služby technologie Hyper-V na každém virtuálním počítači, který chcete zjišťovat a hodnotit.


Tato nastavení můžete nakonfigurovat ručně pomocí níže uvedených postupů. Případně spustíte konfigurační skript požadavků technologie Hyper-V.

### <a name="hyper-v-prerequisites-configuration-script"></a>Konfigurační skript požadavků technologie Hyper-V

Tento skript ověří hostitele Hyper-V a nakonfiguruje nastavení, která potřebujete ke zjišťování a vyhodnocení virtuálních počítačů Hyper-V. Tady je postup:

- Kontroluje, zda jste na skriptu spuštěni v podporované verzi prostředí PowerShell.
- Ověřuje, že jste (uživatel, který spouští skript), má oprávnění správce na hostiteli Hyper-V.
- Umožňuje vytvořit místní uživatelský účet (nikoli správce), který se používá pro službu Azure Migrate ke komunikaci s hostitelem Hyper-V. Tento uživatelský účet se přidá do těchto skupin na hostiteli:
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
3. Ověřte integritu skriptu buď pomocí souborů hash MD5, nebo SHA256. Spuštěním tohoto příkazu vygenerujte hodnotu hash pro tento skript:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Příklad použití: 
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```
    
    Hodnoty hash jsou:
    Hash | Hodnota
    --- | ---
    **Hodnota hash MD5** | 0ef418f31915d01f896ac42a80dc414e
    **Hodnota hash SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2


4.  Po ověření integrity skriptu spusťte skript na každém hostiteli Hyper-V pomocí tohoto příkazu PowerShellu:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```


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

Když nastavíte zařízení, které dokončíte nastavením CredSSP, [jeho povolením na zařízení](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Tento postup je popsaný v dalším kurzu této série.


### <a name="verify-appliance-settings"></a>Ověření nastavení zařízení

Před nastavením zařízení Azure Migrate a zahájením posouzení v dalším kurzu připravte na nasazení zařízení.

1. [Ověřte](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) požadavky na zařízení.
2. [Zkontrolujte](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) adresy URL Azure, ke kterým bude zařízení potřebovat přístup.
3. Zkontrolujte data, která bude zařízení shromažďovat během zjišťování a posouzení.
4. [Poznamenejte si](migrate-support-matrix-hyper-v.md#assessment-port-requirements) požadavky na přístup k portu pro dané zařízení.


### <a name="set-up-an-account-for-vm-discovery"></a>Nastavení účtu pro zjišťování virtuálních počítačů

Azure Migrate potřebuje oprávnění ke zjišťování místních virtuálních počítačů.

- Nastavte doménu nebo místní uživatelský účet s oprávněními správce v hostitelích nebo clusteru Hyper-V.

    - Pro všechny hostitele a clustery, které chcete zahrnout do zjišťování, potřebujete jeden účet.
    - Účet může být místní nebo doménový účet. Doporučujeme, aby měl oprávnění správce na hostitelích nebo clusterech Hyper-V.
    - Případně, pokud nechcete přiřadit oprávnění správce, jsou potřeba následující oprávnění:
        - Uživatelé vzdálené správy
        - Správci technologie Hyper-V
        - Uživatelé sledování výkonu

### <a name="enable-integration-services-on-vms"></a>Povolit integrační služby na virtuálních počítačích

V každém virtuálním počítači by měly být povolené integrační služby, aby Azure Migrate mohl zachytit informace o operačním systému na VIRTUÁLNÍm počítači.

Na virtuálních počítačích, které chcete zjišťovat a hodnotit, povolte na každém virtuálním počítači [integrační služby technologie Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) . 

## <a name="prepare-for-hyper-v-migration"></a>Příprava na migraci technologie Hyper-V

1. [Kontrola](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) Požadavky na hostitele Hyper-V pro migraci.
2. [Projděte si](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) požadavky na virtuální počítače Hyper-V, které chcete migrovat do Azure.
3. [Poznamenejte](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) si adresy URL Azure, ke kterým hostitelé a clustery Hyper-V potřebují přístup pro migraci virtuálních počítačů.

## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:
 
> [!div class="checklist"] 
> * Nastavte oprávnění účtu Azure.
> * Připraví hostitelé a virtuální počítače Hyper-V pro posouzení a migraci.

Přejděte k dalšímu kurzu a vytvořte projekt Azure Migrate a vyhodnoťte virtuální počítače Hyper-V pro migraci do Azure.

> [!div class="nextstepaction"] 
> [Posouzení virtuálních počítačů Hyper-V](./tutorial-assess-hyper-v.md) 
