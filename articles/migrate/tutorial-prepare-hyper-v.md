---
title: Příprava virtuálních počítačů Hyper-V pro posouzení/migraci pomocí Azure Migrate
description: Přečtěte si, jak připravit na posouzení/migraci virtuálních počítačů Hyper-V pomocí Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: ca9020a9c306eea39d75c15c96b5f9fe9bcc11fe
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/15/2020
ms.locfileid: "84770539"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Příprava na posouzení a migraci virtuálních počítačů Hyper-V do Azure

Tento článek vám pomůže připravit se na posouzení a migraci místních virtuálních počítačů Hyper-V do Azure pomocí [Azure Migrate: posouzení serveru](migrate-services-overview.md#azure-migrate-server-assessment-tool)a [Azure Migrate: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool).


Tento kurz je první v řadě, kde se dozvíte, jak vyhodnotit a migrovat virtuální počítače Hyper-V do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte Azure pro práci s Azure Migrate.
> * Připravte se na vyhodnocení virtuálních počítačů Hyper-V.
> * Příprava na migraci virtuálních počítačů Hyper-V 

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/pricing/free-trial/), ještě než začnete.


## <a name="prepare-azure"></a>Příprava Azure

Tabulka shrnuje úkoly, které je potřeba provést v Azure. Postupujte podle pokynů v tabulce.

**Úkol** | **Podrobnosti** | **Oprávnění**
--- | --- | ---
**Vytvoření projektu Azure Migrate** | Azure Migrate projekt poskytuje centrální umístění pro orchestraci a správu hodnocení a migrace pomocí nástrojů Azure Migrate, nástrojů společnosti Microsoft a nabídek třetích stran. | Váš účet Azure potřebuje oprávnění přispěvatele nebo vlastníka ve skupině prostředků, ve které se projekt nachází.
**Registrovat zařízení** | Azure Migrate používá ke zjišťování a vyhodnocení virtuálních počítačů Hyper-V odlehčené zařízení Azure Migrate. [Přečtěte si další informace](migrate-appliance-architecture.md#appliance-registration). | Aby bylo možné zařízení zaregistrovat, váš účet Azure potřebuje oprávnění přispěvatele nebo vlastníka v předplatném Azure.
**Vytvoření aplikace Azure AD** | Při registraci zařízení Azure Migrate vytvoří aplikaci Azure Active Directory (Azure AD), která se používá pro komunikaci mezi agenty běžícími na zařízení a Azure Migrate. | Váš účet Azure potřebuje oprávnění k vytváření aplikací Azure AD.
**Vytvoření virtuálního počítače** | Potřebujete oprávnění k vytvoření virtuálního počítače ve skupině prostředků a virtuální síti a pro zápis na spravovaný disk Azure. | Účet Azure potřebuje roli Přispěvatel virtuálních počítačů.


### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu

Ověřte, že máte oprávnění k vytvoření projektu Azure Migrate.

1. V Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM)**.
2. V části **kontrolovat přístup**Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Měli byste mít oprávnění **Přispěvatel** nebo **Owner** .
    - Pokud jste právě vytvořili bezplatný účet Azure, jste vlastníkem svého předplatného.
    - Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte roli.


### <a name="assign-permissions-to-create-azure-ad-apps"></a>Přiřazení oprávnění k vytváření aplikací Azure AD

Můžete přiřadit oprávnění pro Azure Migrate vytvoření aplikace Azure AD během registrace zařízení pomocí jedné z následujících metod:

- Tenant nebo globální správce může udělit oprávnění uživatelům v tenantovi, aby mohli vytvářet a registrovat aplikace služby Azure AD.
- Tenant nebo globální správce může k účtu přiřadit roli vývojáře aplikace (která má oprávnění).

> [!NOTE]
> - Aplikace nemá žádná jiná přístupová oprávnění k předplatnému, kromě výše popsaných výše.
> - Tato oprávnění budete potřebovat, jenom když zaregistrujete nové zařízení. Po nastavení zařízení můžete oprávnění odebrat.


#### <a name="grant-account-permissions"></a>Udělení oprávnění účtu

Tenant nebo globální správce může udělit oprávnění následujícím způsobem:

1. V Azure AD by měl tenant nebo globální správce přejít na **Azure Active Directory**  >  **Users**  >  **uživatelských nastavení**uživatelů.
2. Správce by měl nastavit **Registrace aplikací** **Ano**.

    ![Oprávnění služby Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Toto je výchozí nastavení, které není citlivé. [Přečtěte si další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Přiřazení role vývojáře aplikace

Tenant/globální správce může přiřadit roli vývojář aplikace k účtu. [Přečtěte si další informace](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

### <a name="assign-azure-account-permissions"></a>Přiřazení oprávnění účtu Azure

Přiřaďte k účtu roli Přispěvatel virtuálních počítačů, abyste měli oprávnění k těmto akcím:

- Vytvoření virtuálního počítače ve vybrané skupině prostředků
- Vytvoření virtuálního počítače ve vybrané virtuální síti
- Zapište na spravovaný disk Azure. 


### <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

[Nastavte síť Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Místní počítače se replikují do Azure Managed disks. Když převezmete služby při selhání do Azure pro migraci, virtuální počítače Azure se z těchto spravovaných disků vytvoří a připojí se k síti Azure, kterou jste nastavili.


## <a name="prepare-for-assessment"></a>Příprava na posouzení

Hyper-V pro vyhodnocení virtuálních počítačů můžete připravit ručně nebo pomocí konfiguračního skriptu. Toto jsou přípravné kroky. Pokud připravujete skript pomocí skriptu, nakonfigurují se automaticky.

**Krok** | **Skript** | **Ruční**
--- | --- | ---
**Ověření požadavků na hostitele Hyper-V** | Skript kontroluje, zda je na hostiteli spuštěná podporovaná verze technologie Hyper-V a role Hyper-V.<br/><br/> Povolí službu WinRM a na hostiteli otevře porty 5985 (HTTP) a 5986 (HTTPS) (potřebné pro kolekci metadat). | Ověřte [požadavky na hostitele Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) pro vyhodnocování serveru.<br/><br/> Ujistěte se, že jsou na hostitelích Hyper-V otevřené [požadované porty](migrate-support-matrix-hyper-v.md#port-access) .
**Ověřit verzi PowerShellu** | Kontroluje, zda spouštíte skript v podporované verzi prostředí PowerShell. | Podívejte se na hostitele Hyper-V, na kterém běží PowerShell verze 4,0 nebo novější.
**Vytvoření účtu** | Ověřuje, že jste (uživatel, který spouští skript), má oprávnění správce na hostiteli Hyper-V.<br/><br/>  Umožňuje vytvořit místní uživatelský účet (nejedná se o správce), který služba Azure Migrate používá ke komunikaci s hostitelem Hyper-V. Tento uživatelský účet se přidá do těchto skupin na hostiteli:<br/><br/> – Uživatelé vzdálené správy<br/><br/> – Správci technologie Hyper-V<br/><br/>-Performance Monitor Users | Nastavte doménu nebo místní uživatelský účet s oprávněními správce v hostitelích nebo clusteru Hyper-V.<br/><br/> – Pro všechny hostitele a clustery, které chcete zahrnout do zjišťování, potřebujete jeden účet.<br/><br/> – Účet může být místní nebo doménový účet. Doporučujeme, aby měl oprávnění správce na hostitelích nebo clusterech Hyper-V.<br/><br/> Případně, pokud nechcete přiřazovat oprávnění správce, jsou potřeba následující oprávnění: Uživatelé vzdálené správy; Správci technologie Hyper-V; Sledování výkonu uživatele.
**Povolit vzdálenou komunikaci PowerShellu** | Povolí vzdálenou komunikaci PowerShellu na hostiteli, aby zařízení Azure Migrate mohlo spouštět na hostiteli příkazy PowerShellu přes připojení WinRM.| Pokud chcete nastavit, otevřete v každém hostiteli konzolu PowerShellu jako správce a spusťte tento příkaz:<br/><br/>``` Enable-PSRemoting -force ```
**Nastavení integračních služeb technologie Hyper-V** | Kontroluje, zda jsou integrační služby technologie Hyper-V povoleny na všech virtuálních počítačích spravovaných hostitelem. |  Povolte na každém virtuálním počítači [integrační služby technologie Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) .<br/><br/> Pokud používáte systém Windows Server 2003, [postupujte podle těchto pokynů](prepare-windows-server-2003-migration.md).
**Delegovat přihlašovací údaje, pokud se disky virtuálních počítačů nacházejí ve vzdálených sdílených složkách protokolu SMB** | Skript deleguje přihlašovací údaje. | [Povolte zprostředkovateli CredSSP](#enable-credssp-to-delegate-credentials) delegovat přihlašovací údaje.

### <a name="run-the-script"></a>Spuštění skriptu

Spusťte skript následujícím způsobem:

1. Ujistěte se, že na hostiteli Hyper-V máte nainstalovanou verzi prostředí PowerShell verze 4,0 nebo novější.
2. Stáhněte si skript z webu [Microsoft Download Center](https://aka.ms/migrate/script/hyperv). Skript je kryptograficky podepsán společností Microsoft.
3. Ověřte integritu skriptu buď pomocí MD5, nebo souborů SHA256 hash. Hodnoty hashtagu jsou uvedené níže. Spuštěním tohoto příkazu vygenerujte hodnotu hash pro tento skript:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Příklad použití:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```

4. Po ověření integrity skriptu spusťte skript na každém hostiteli Hyper-V pomocí tohoto příkazu PowerShellu:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Hodnoty hashtagu

Hodnoty hash jsou:

| **Kontrole** | **Hodnota** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |



### <a name="enable-credssp-to-delegate-credentials"></a>Povolit zprostředkovatele CredSSP k delegování přihlašovacích údajů

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

1. [Ověřte](migrate-appliance.md#appliance---hyper-v) požadavky na zařízení.
2. Zkontrolujte adresy URL Azure, které bude zařízení potřebovat pro přístup k [veřejným](migrate-appliance.md#public-cloud-urls) a [státním](migrate-appliance.md#government-cloud-urls) cloudům. Pokud používáte bránu firewall nebo proxy založenou na adrese URL, ujistěte se, že umožňuje přístup k požadovaným adresám URL.
3. Zkontrolujte data, která bude zařízení shromažďovat během zjišťování a posouzení.
4. [Zkontrolujte](migrate-appliance.md#collected-data---hyper-v) požadavky na přístup k portu pro dané zařízení.


## <a name="prepare-for-hyper-v-migration"></a>Příprava na migraci technologie Hyper-V

1. [Kontrola](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) Požadavky na hostitele Hyper-V pro migraci a adresy URL Azure, ke kterým hostitelé a clustery Hyper-V potřebují přístup pro migraci virtuálních počítačů.
2. [Projděte si](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) požadavky na virtuální počítače Hyper-V, které chcete migrovat do Azure.
3. Před migrací do Azure jsou na virtuálních počítačích potřeba nějaké změny.
    - Před zahájením migrace je důležité tyto změny provést. Pokud před provedením změny migrujete virtuální počítač, nemusí se virtuální počítač spustit v Azure.
    - Zkontrolujte změny v [systému Windows](prepare-for-migration.md#windows-machines) a [Linux](prepare-for-migration.md#linux-machines) , které je třeba provést.



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavte oprávnění účtu Azure.
> * Připraví hostitelé a virtuální počítače Hyper-V pro posouzení a migraci.
> * Připraveno pro nasazení zařízení Azure Migrate.

Přejděte k dalšímu kurzu, abyste vytvořili Azure Migrate projekt, nasadili zařízení a zjistili a vyhodnotili virtuální počítače Hyper-V pro migraci do Azure.

> [!div class="nextstepaction"]
> [Posouzení virtuálních počítačů Hyper-V](./tutorial-assess-hyper-v.md)
