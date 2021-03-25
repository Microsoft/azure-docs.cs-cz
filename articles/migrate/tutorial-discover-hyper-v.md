---
title: Zjišťování serverů na Hyper-V s využitím Azure Migrate Discovery a Assessment
description: Naučte se zjišťovat místní servery na technologii Hyper-V pomocí nástroje Azure Migrate Discovery and Assessment Tool.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: mvc
ms.openlocfilehash: d108774aea2d7ac7773dab06efc977dd9e501eb5
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108770"
---
# <a name="tutorial-discover-servers-running-on-hyper-v-with-azure-migrate-discovery-and-assessment"></a>Kurz: zjišťování serverů běžících na technologii Hyper-V s Azure Migrate: zjišťování a posouzení

Jako součást cesty migrace do Azure zjistíte místní inventář a zatížení. 

V tomto kurzu se dozvíte, jak zjišťovat místní servery na hostitelích Hyper-V pomocí nástroje Azure Migrate: Discovery and Assessment pomocí jednoduchého zařízení Azure Migrate. Zařízení nasadíte jako server na hostiteli Hyper-V, abyste mohli průběžně zjišťovat metadata počítačů a výkonu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení účtu Azure
> * Připravte prostředí Hyper-V pro zjišťování.
> * Vytvořte projekt.
> * Nastavte zařízení Azure Migrate.
> * Spusťte nepřetržité zjišťování.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti.  

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, Projděte si tyto požadavky.

**Požadavek** | **Podrobnosti**
--- | ---
**Hostitel Hyper-V** | Hostitelé Hyper-V, na kterých se nacházejí servery, můžou být samostatné nebo v clusteru.<br/><br/> V hostiteli musí být spuštěný systém Windows Server 2019, Windows Server 2016 nebo Windows Server 2012 R2.<br/><br/> Ověřte, zda jsou povolena příchozí připojení na portu WinRM 5985 (HTTP), aby se zařízení mohlo připojit k vyžádanému metadatům serveru a datům výkonu pomocí model CIM (Common Information Model) (CIM).
**Nasazení zařízení** | Hostitel Hyper-V potřebuje prostředky k přidělení serveru pro zařízení:<br/><br/> – 16 GB paměti RAM, 8 vCPU a přibližně 80 GB diskového úložiště.<br/><br/> – Externí virtuální přepínač a přístup k Internetu na zařízení, přímo nebo prostřednictvím proxy serveru.
**Servery** | Na serverech může běžet libovolný operační systém Windows nebo Linux. 

## <a name="prepare-an-azure-user-account"></a>Příprava uživatelského účtu Azure

Pokud chcete vytvořit projekt a zaregistrovat Azure Migrate zařízení, budete potřebovat účet s:
- Oprávnění přispěvatele nebo vlastníka v předplatném Azure.
- Oprávnění k registraci aplikací Azure Active Directory (AAD).

Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem vašeho předplatného. Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte oprávnění následujícím způsobem:

1. V Azure Portal vyhledejte "předplatná" a v části **služby** vyberte **předplatná**.

    ![Vyhledávací pole pro hledání předplatného Azure](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Na stránce **předplatná** vyberte předplatné, ve kterém chcete vytvořit projekt. 
3. V předplatném vyberte **řízení přístupu (IAM)**  >  **Kontrola přístupu**.
4. V části **kontrolovat přístup** vyhledejte příslušný uživatelský účet.
5. V nabídce **Přidat přiřazení role** klikněte na **Přidat**.

    ![Vyhledejte uživatelský účet pro kontrolu přístupu a přiřazení role.](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. V části **Přidat přiřazení role** vyberte roli přispěvatel nebo vlastník a v našem příkladu vyberte účet (azmigrateuser). Potom klikněte na **Uložit**.

    ![Otevře stránku přidat přiřazení role, která účtu přiřadí roli.](./media/tutorial-discover-hyper-v/assign-role.png)

1. Aby bylo možné zařízení zaregistrovat, váš účet Azure potřebuje **oprávnění k registraci aplikací AAD.**
1. V Azure Portal přejděte na   >    >  **uživatelské nastavení** Azure Active Directory uživatelé.
1. V **nastavení uživatele** ověřte, že uživatelé Azure AD můžou registrovat aplikace (ve výchozím nastavení nastavené na **Ano** ).

    ![Ověřte v uživatelských nastaveních, která můžou uživatelé registrovat v aplikacích Active Directory.](./media/tutorial-discover-hyper-v/register-apps.png)

9. Pokud je nastavení ' Registrace aplikací ' nastaveno na hodnotu ' ne ', požádejte tenanta/globálního správce, aby přiřadil požadované oprávnění. Alternativně může tenant nebo globální správce přiřadit roli **vývojář aplikace** k účtu, aby umožnil registraci aplikace AAD. [Další informace](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Příprava hostitelů technologie Hyper-V

Hostitele Hyper-V můžete připravit ručně nebo pomocí skriptu. Přípravné kroky jsou shrnuté v tabulce. Tento skript automaticky připraví.

**Krok** | **Skript** | **Ruční**
--- | --- | ---
Ověření požadavků hostitele | Kontroluje, zda je na hostiteli spuštěná podporovaná verze technologie Hyper-V a role Hyper-V.<br/><br/>Povolí službu WinRM a na hostiteli otevře porty 5985 (HTTP) a 5986 (HTTPS) (potřebné pro kolekci metadat). | V hostiteli musí být spuštěný systém Windows Server 2019, Windows Server 2016 nebo Windows Server 2012 R2.<br/><br/> Ověřte, zda jsou povolena příchozí připojení na portu WinRM 5985 (HTTP), aby se zařízení mohlo připojit k vyžádanému metadatům serveru a datům výkonu pomocí model CIM (Common Information Model) (CIM).<br/><br/> Tento skript není aktuálně podporován na hostitelích s jiným než anglickým národním prostředím.  
Ověřit verzi PowerShellu | Kontroluje, zda spouštíte skript v podporované verzi prostředí PowerShell. | Podívejte se na hostitele Hyper-V, na kterém běží PowerShell verze 4,0 nebo novější.
Vytvoření účtu | Ověřuje, zda máte správná oprávnění pro hostitele Hyper-V.<br/><br/> Umožňuje vytvořit místní uživatelský účet se správnými oprávněními. | Možnost 1: Připravte účet s přístupem správce k hostitelskému počítači Hyper-V.<br/><br/> Možnost 2: Připravte účet místního správce nebo účet správce domény a přidejte tento účet do těchto skupin: Uživatelé vzdálené správy, Správci technologie Hyper-V a uživatelé nástroje Performance Monitor.
Povolit vzdálenou komunikaci PowerShellu | Povolí vzdálenou komunikaci PowerShellu na hostiteli, aby zařízení Azure Migrate mohlo spouštět na hostiteli příkazy PowerShellu přes připojení WinRM. | Pokud chcete nastavit, otevřete v každém hostiteli konzolu PowerShellu jako správce a spusťte tento příkaz: ``` powershell Enable-PSRemoting -force ```
Nastavení integračních služeb technologie Hyper-V | Kontroluje, zda jsou integrační služby technologie Hyper-V povoleny na všech serverech spravovaných hostitelem. | Povolte na každém serveru [integrační služby technologie Hyper-V](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) .<br/><br/> Pokud používáte systém Windows Server 2003, [postupujte podle těchto pokynů](prepare-windows-server-2003-migration.md).
Delegovat přihlašovací údaje, pokud se disky serveru nacházejí ve vzdálených sdílených složkách protokolu SMB | Delegáti pověření | Spuštěním tohoto příkazu povolíte zprostředkovateli CredSSP delegovat přihlašovací údaje na hostitelích, na kterých běží servery s disky ve sdílených složkách SMB: ```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> Tento příkaz můžete spustit vzdáleně na všech hostitelích Hyper-V.<br/><br/> Pokud přidáte nové uzly hostitele v clusteru, jsou automaticky přidány pro zjišťování, ale je nutné povolit zprostředkovatele CredSSP ručně.<br/><br/> Když zařízení nastavíte, dokončíte nastavení CredSSP [jeho povolením na zařízení](#delegate-credentials-for-smb-vhds). 

### <a name="run-the-script"></a>Spuštění skriptu

1. Stáhněte si skript z webu [Microsoft Download Center](https://aka.ms/migrate/script/hyperv). Skript je kryptograficky podepsán společností Microsoft.
2. Ověřte integritu skriptu buď pomocí MD5, nebo souborů SHA256 hash. Hodnoty hashtagu jsou uvedené níže. Spuštěním tohoto příkazu vygenerujte hodnotu hash pro tento skript:

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Příklad použití:

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. Po ověření integrity skriptu spusťte skript na každém hostiteli Hyper-V s tímto příkazem PowerShellu se zvýšenými oprávněními:

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
Hodnoty hash jsou:

**Hash** |  **Hodnota**
--- | ---
MD5 | 0ef418f31915d01f896ac42a80dc414e
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>Nastavení projektu

Nastavte nový projekt.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. V **přehledu** vyberte **vytvořit projekt**.
5. V nástroji **vytvořit projekt** vyberte své předplatné Azure a skupinu prostředků. Vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pole pro název a oblast projektu](./media/tutorial-discover-hyper-v/new-project.png)

7. Vyberte **Vytvořit**.
8. Počkejte několik minut, než se projekt nasadí. Nástroj **Azure Migrate: Discovery and Assessment** Tool je ve výchozím nastavení přidán do nového projektu.

![Stránka zobrazující Azure Migrate: Nástroj pro zjišťování a hodnocení se přidal ve výchozím nastavení.](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> Pokud jste již vytvořili projekt, můžete použít stejný projekt k registraci dalších zařízení pro zjišťování a vyhodnocení většího počtu serverů. [Další informace](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Nastavení zařízení

Azure Migrate používá odlehčené Azure Migrate zařízení. Zařízení provádí zjišťování serveru a odesílá metadata konfigurace a výkonu serveru Azure Migrate. Zařízení je možné nastavit tak, že nasadíte soubor VHD, který se dá stáhnout z projektu.

> [!NOTE]
> Pokud z nějakého důvodu nemůžete zařízení nastavit pomocí šablony, můžete ho nastavit pomocí skriptu PowerShellu na existujícím serveru Windows Server 2016. [Další informace](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).

V tomto kurzu se nastavuje zařízení na serveru, na kterém běží v prostředí Hyper-V, a to následujícím způsobem:

1. Zadejte název zařízení a vygenerujte klíč projektu na portálu.
1. Z Azure Portal Stáhněte komprimovaný VHD Hyper-V.
1. Vytvořte zařízení a ověřte, jestli se může připojit k Azure Migrate: zjišťování a posouzení.
1. Nakonfigurujete zařízení poprvé a zaregistrujete ho do projektu pomocí klíče projektu.

### <a name="1-generate-the-project-key"></a>1. vygenerujte klíč projektu.

1. V Azure Migrate **cíle migrace**  >    >  Vyberte **zjišťování a hodnocení**. 
2. V rozevíracích **seznamech servery**  >  **jsou vaše servery virtualizované?** vyberte **Ano, s technologií Hyper-V**.
3. V části **1: generovat klíč projektu** zadejte název zařízení Azure Migrate, které nastavíte pro zjišťování serverů. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
1. Kliknutím na **vygenerovat klíč** spustíte vytváření požadovaných prostředků Azure. Během vytváření prostředků prosím Nezavírejte stránku zjišťovacího serveru.
1. Po úspěšném vytvoření prostředků Azure se vygeneruje **klíč projektu** .
1. Zkopírujte klíč, protože ho budete potřebovat k dokončení registrace zařízení během jeho konfigurace.

### <a name="2-download-the-vhd"></a>2. Stáhněte VHD.

V **2: Stáhněte zařízení Azure Migrate** vyberte. Soubor VHD a klikněte na **Stáhnout**.

### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.

2. Spusťte následující příkaz prostředí PowerShell, který vygeneruje hodnotu hash pro soubor ZIP.
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Příklady použití: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Ověřte nejnovější verze zařízení a hodnoty hash:

    - Pro veřejný cloud Azure:

        **Scénář** | **Stáhnout** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 GB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140422) |  40aa037987771794428b1c6ebee2614b092e6d69ac56d48a2bbc75eeef86c99a

    - Pro Azure Government:

        **Scénář** _ | _ *Stáhnout** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="3-create-an-appliance"></a>3. vytvoření zařízení

Naimportujte stažený soubor a vytvořte zařízení.

1. Extrahujte soubor VHD s příponou ZIP do složky na hostiteli Hyper-V, která bude hostovat zařízení. Jsou extrahovány tři složky.
2. Spusťte Správce technologie Hyper-V. V nabídce **Akce** klikněte na **importovat virtuální počítač**.
2. V Průvodci importem virtuálního počítače > **než začnete**, klikněte na **Další**.
3. V části **Vyhledat složku** zadejte složku obsahující extrahovaný virtuální pevný disk. Potom klikněte na **Další**.
1. V nabídce **Vybrat virtuální počítač** klikněte na **Další**.
2. V části **zvolit typ importu** klikněte na **zkopírovat virtuální počítač (vytvořit nové jedinečné ID)**. Potom klikněte na **Další**.
3. V části **zvolit cíl** ponechte výchozí nastavení. Klikněte na **Next** (Další).
4. V části **složky úložiště** ponechte výchozí nastavení. Klikněte na **Next** (Další).
5. V části **zvolit síť** zadejte virtuální přepínač, který bude zařízení používat. Přepínač potřebuje připojení k Internetu, aby bylo možné odesílat data do Azure.
6. V části **Souhrn** zkontrolujte nastavení. Klikněte na **Dokončit**.
7. Ve Správci technologie Hyper-V > **Virtual Machines** spusťte zařízení.

### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro cloudy [veřejné](migrate-appliance.md#public-cloud-urls) a [státní správy](migrate-appliance.md#government-cloud-urls) .

### <a name="4-configure-the-appliance"></a>4. konfigurace zařízení

Nastavte zařízení poprvé.

> [!NOTE]
> Pokud zařízení nastavíte pomocí [skriptu PowerShellu](deploy-appliance-script.md) namísto staženého virtuálního pevného disku, první dva kroky v tomto postupu se netýkají.

1. Ve Správci technologie Hyper-V > **Virtual Machines** klikněte pravým tlačítkem na zařízení > **připojit**.
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k zařízení, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy zařízení kliknutím na zástupce aplikace.
1. Přijměte **licenční podmínky** a přečtěte si informace třetích stran.
1. Ve webové aplikaci > **nastavení požadavků** postupujte takto:
    - **Připojení**: aplikace zkontroluje, jestli má zařízení přístup k Internetu. Pokud zařízení používá proxy server:
      - Klikněte na **nastavení proxy serveru** a zadejte adresu proxy serveru (ve formuláři http://ProxyIPAddress nebo na http://ProxyFQDN) naslouchajícím portu.
      - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
      - Podporuje se jen proxy protokolu HTTP.
      - Pokud jste přidali podrobnosti proxy serveru nebo zakážete proxy server nebo ověřování, kliknutím na **Uložit** spusťte kontrolu připojení znovu.
    - **Časová synchronizace**: čas je ověřený. Čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování serveru fungovalo správně.
    - **Instalovat aktualizace**: Azure Migrate: zjišťování a posouzení kontroluje, jestli má zařízení nainstalované nejnovější aktualizace. Po dokončení kontroly můžete kliknout na **Zobrazit služby zařízení** a zobrazit stav a verze komponent spuštěných na zařízení.

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Vložte **klíč projektu** zkopírovaný z portálu. Pokud nemáte klíč, podívejte se na **Azure Migrate: zjišťování a hodnocení> zjistit> spravovat existující zařízení**, vyberte název zařízení, který jste zadali v době generování klíče, a zkopírujte odpovídající klíč.
1. K ověření pomocí Azure budete potřebovat kód zařízení. Kliknutím na **přihlášení** se otevře modální okno s kódem zařízení, jak je znázorněno níže.

    ![Modální zobrazení kódu zařízení](./media/tutorial-discover-vmware/device-code.png)

1. Kliknutím na **zkopírovat kód & přihlášením** zkopírujte kód zařízení a otevřete výzvu k přihlášení Azure na nové kartě prohlížeče. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
1. Na kartě nový vložte kód zařízení a přihlaste se pomocí uživatelského jména a hesla Azure.
   
   Přihlášení pomocí PIN kódu se nepodporuje.
3. Pokud kartu přihlášení omylem zavřete bez přihlašování, budete muset aktualizovat kartu prohlížeče Configuration Manageru a povolit tak tlačítko pro přihlášení znovu.
1. Po úspěšném přihlášení se vraťte na předchozí kartu pomocí Správce konfigurace zařízení.
4. Pokud má uživatelský účet Azure použitý k protokolování správná oprávnění k prostředkům Azure vytvořeným během generování klíče, zahájí se registrace zařízení.
1. Po úspěšné registraci zařízení si můžete zobrazit podrobnosti o registraci kliknutím na **Zobrazit podrobnosti**.

### <a name="delegate-credentials-for-smb-vhds"></a>Pověření delegáta pro virtuální pevné disky SMB

Pokud používáte na SMB virtuální pevné disky, musíte povolit delegování přihlašovacích údajů ze zařízení na hostitele Hyper-V. Provedete to pomocí zařízení:

1. Na zařízení spusťte tento příkaz. HyperVHost1/HyperVHost2 jsou příklady názvů hostitelů.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Případně to udělejte v Editor místních zásad skupiny na zařízení:
    - V konfiguraci počítače **Zásady místního počítače**  >  klikněte na **šablony pro správu**  >    >  **delegování přihlašovacích údajů** systému.
    - Dvakrát klikněte na **Povolit delegování nových přihlašovacích údajů** a vyberte **povoleno**.
    - V nabídce **Možnosti** klikněte na **Zobrazit** a do seznamu přidejte každého hostitele Hyper-V, který chcete zjistit, a použijte příkaz **WSMan/** jako předponu.
    - V  **delegování přihlašovacích údajů** poklikejte na možnost **umožňuje delegovat nové přihlašovací údaje pomocí ověřování serveru jenom s protokolem NTLM**. Znovu přidejte všechny hostitele Hyper-V, které chcete vyhledat, do seznamu s použitím nástroje **WSMan/** jako předpony.

## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Připojte se ze zařízení k hostitelům nebo clusterům Hyper-V a spusťte zjišťování serveru.

1. V **kroku 1: zadání přihlašovacích údajů hostitele Hyper-v** klikněte na **Přidat přihlašovací údaje** , abyste zadali popisný název pro přihlašovací údaje. přidejte **uživatelské jméno** a **heslo** pro hostitele nebo cluster Hyper-V, které zařízení použije k zjišťování serverů. Klikněte na **Uložit**.
1. Pokud chcete přidat více přihlašovacích údajů najednou, klikněte na **Přidat další** a uložte a přidejte další přihlašovací údaje. Pro zjišťování serverů v prostředí technologie Hyper-V je podporováno více přihlašovacích údajů.
1. V **kroku 2: zadání podrobností o hostiteli nebo clusteru technologie Hyper-v** klikněte na **Přidat zdroj zjišťování** a zadejte hostitele Hyper-v nebo **IP adresu clusteru nebo plně kvalifikovaný název domény** a popisný název přihlašovacích údajů pro připojení k hostiteli nebo clusteru.
1. Můžete buď **přidat jednu položku** najednou, nebo **Přidat více položek** do jednoho přechodu. K dispozici je také možnost poskytnout podrobnosti o hostiteli nebo clusteru Hyper-V prostřednictvím **importu CSV**.

    - Pokud zvolíte možnost **přidat jednu položku**, je nutné zadat popisný název pro přihlašovací údaje a hostitele Hyper-V/ **IP adresu clusteru nebo plně kvalifikovaný název domény** a kliknout na **Uložit**.
    - Pokud zvolíte možnost **Přidat více položek** _(ve výchozím nastavení je vybrána možnost)_, můžete přidat více záznamů najednou zadáním názvu hostitele nebo clusteru Hyper-V nebo **plně kvalifikovaného názvu domény** s popisným názvem pro přihlašovací údaje v textovém poli. **Ověřte** přidané záznamy a klikněte na **Uložit**.
    - Pokud zvolíte **importovat sdílený svazek clusteru**, můžete si stáhnout soubor šablony CSV, naplnit soubor pomocí hostitele Hyper-V/ **IP adresy clusteru/plně kvalifikovaného názvu domény** a popisného názvu pro přihlašovací údaje. Pak soubor naimportujete do zařízení, **ověříte** záznamy v souboru a kliknete na **Uložit**.

1. Po kliknutí na Uložit se zařízení pokusí ověřit připojení k hostitelům nebo clusterům Hyper-V a zobrazit **stav ověření** v tabulce pro každého hostitele nebo cluster.
    - Pro úspěšné ověřené hostitele nebo clustery můžete zobrazit další podrobnosti kliknutím na jejich IP adresu nebo plně kvalifikovaný název domény.
    - Pokud se ověření pro hostitele nepovede, přečtěte si chybu kliknutím na **ověření selhalo** ve sloupci Stav v tabulce. Opravte problém a znovu ověřte.
    - Chcete-li odebrat hostitele nebo clustery, klikněte na tlačítko **Odstranit**.
    - Z clusteru nelze odebrat konkrétního hostitele. Můžete odebrat jenom celý cluster.
    - Cluster můžete přidat i v případě, že v clusteru dojde k problémům s konkrétními hostiteli.
1. Kdykoli před zahájením zjišťování můžete znovu **ověřit** připojení k hostitelům/clusterům.
1. Kliknutím na **Start Discovery (spustit zjišťování) spustíte** zjišťování serveru z úspěšně ověřených hostitelů nebo clusterů. Po úspěšném spuštění zjišťování můžete zjistit stav zjišťování u každého hostitele nebo clusteru v tabulce.

Spustí se zjišťování. Zabere přibližně 2 minuty na hostitele, aby se metadata zjištěných serverů zobrazovala v Azure Portal.

## <a name="verify-servers-in-the-portal"></a>Ověření serverů na portálu

Po dokončení zjišťování můžete ověřit, že se servery zobrazují na portálu.

1. Otevřete řídicí panel služby Azure Migrate.
2. Na stránce **Azure Migrate-servery**  >  **Azure Migrate: zjišťování a posouzení** klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="next-steps"></a>Další kroky

- [Posuzuje servery v prostředí Hyper-V](tutorial-assess-hyper-v.md) pro migraci na virtuální počítače Azure.
- [Zkontrolujte data](migrate-appliance.md#collected-data---hyper-v) , která zařízení shromažďuje během zjišťování.


