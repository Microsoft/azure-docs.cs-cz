---
title: Zjištění instancí AWS pomocí posouzení serveru Azure Migrate
description: Naučte se zjišťovat instance AWS pomocí posouzení serveru Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 8fb17dc880b74da3ca4e96df10946878fde31909
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98541406"
---
# <a name="tutorial-discover-aws-instances-with-server-assessment"></a>Kurz: zjištění instancí AWS pomocí posouzení serveru

Jako součást cesty migrace do Azure zjistíte, že vaše servery jsou vyhodnoceny a migrace.

V tomto kurzu se dozvíte, jak zjistit instance Amazon Web Services (AWS) pomocí nástroje Azure Migrate: Server Assessment Tool s použitím odlehčeného zařízení Azure Migrate. Zařízení nasadíte jako fyzický server, abyste mohli průběžně zjišťovat metadata o počítači a výkonu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte účet Azure.
> * Připravte instance AWS pro zjišťování.
> * Jak vytvořit projekt Azure Migrate.
> * Nastavte zařízení Azure Migrate.
> * Spusťte nepřetržité zjišťování.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti.  

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, Projděte si tyto požadavky.

**Požadavek** | **Podrobnosti**
--- | ---
**Náplně** | Potřebujete virtuální počítač EC2, na kterém chcete spustit zařízení Azure Migrate. Počítač by měl mít:<br/><br/> – Nainstalovaný systém Windows Server 2016.<br/> _Spuštění zařízení na počítači s Windows serverem 2019 se nepodporuje_.<br/><br/> – 16 GB RAM, 8 vCPU, přibližně 80 GB diskového úložiště a externí virtuální přepínač.<br/><br/> – Statická nebo dynamická IP adresa s přístupem k Internetu, a to buď přímo, nebo prostřednictvím proxy serveru.
**Instance systému Windows** | Povolí příchozí připojení na portu WinRM 5985 (HTTP), takže zařízení může vyžádat metadata o konfiguraci a výkonu.
**Instance systému Linux** | Povolí příchozí připojení na portu 22 (TCP).<br/><br/> Instance by se měly používat `bash` jako výchozí prostředí, jinak se zjišťování nezdaří.

## <a name="prepare-an-azure-user-account"></a>Příprava uživatelského účtu Azure

Chcete-li vytvořit projekt Azure Migrate a zaregistrovat Azure Migrate zařízení, budete potřebovat účet s tímto:
- Oprávnění přispěvatele nebo vlastníka v předplatném Azure.
- Oprávnění k registraci aplikací Azure Active Directory (AAD).

Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem vašeho předplatného. Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte oprávnění následujícím způsobem:

1. V Azure Portal vyhledejte "předplatná" a v části **služby** vyberte **předplatná**.

    ![Vyhledávací pole pro hledání předplatného Azure](./media/tutorial-discover-aws/search-subscription.png)

2. Na stránce **předplatná** vyberte předplatné, ve kterém chcete vytvořit projekt Azure Migrate. 
3. V předplatném vyberte **řízení přístupu (IAM)**  >  **Kontrola přístupu**.
4. V části **kontrolovat přístup** vyhledejte příslušný uživatelský účet.
5. V nabídce **Přidat přiřazení role** klikněte na **Přidat**.

    ![Vyhledejte uživatelský účet pro kontrolu přístupu a přiřazení role.](./media/tutorial-discover-aws/azure-account-access.png)

6. V části **Přidat přiřazení role** vyberte roli přispěvatel nebo vlastník a v našem příkladu vyberte účet (azmigrateuser). Potom klikněte na **Uložit**.

    ![Otevře stránku přidat přiřazení role, která účtu přiřadí roli.](./media/tutorial-discover-aws/assign-role.png)

1. Aby bylo možné zařízení zaregistrovat, váš účet Azure potřebuje **oprávnění k registraci aplikací AAD.**
1. V Azure Portal přejděte na   >    >  **uživatelské nastavení** Azure Active Directory uživatelé.
1. V **nastavení uživatele** ověřte, že uživatelé Azure AD můžou registrovat aplikace (ve výchozím nastavení nastavené na **Ano** ).

    ![Ověřte v uživatelských nastaveních, která můžou uživatelé registrovat v aplikacích Active Directory.](./media/tutorial-discover-aws/register-apps.png)

1. Pokud je nastavení ' Registrace aplikací ' nastaveno na hodnotu ' ne ', požádejte tenanta/globálního správce, aby přiřadil požadované oprávnění. Alternativně může tenant nebo globální správce přiřadit roli **vývojář aplikace** k účtu, aby umožnil registraci aplikace AAD. [Další informace](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-aws-instances"></a>Příprava instancí AWS

Nastavte účet, který může zařízení použít pro přístup k AWS instancím.

- Pro **Windows servery** nastavte místní uživatelský účet na všech serverech Windows, které chcete zahrnout do zjišťování. Přidejte uživatelský účet do následujících skupin:-Remote Management Users-Performance Monitor Users-Performance Log Users.
 - Pro **servery se systémem** Linux budete potřebovat kořenový účet na serverech se systémem Linux, které chcete zjistit. Alternativní postup najdete v pokynech v tabulce [podpory](migrate-support-matrix-physical.md#physical-server-requirements) .
- Azure Migrate používá ověřování hesla při zjišťování instancí AWS. Instance AWS ve výchozím nastavení nepodporují ověřování hesla. Než budete moct zjistit instanci, musíte povolit ověřování hesla.
    - U počítačů s Windows povolte port WinRM 5985 (HTTP). To umožňuje vzdálené volání rozhraní WMI.
    - Pro počítače se systémem Linux:
        1. Přihlaste se ke každému počítači se systémem Linux.
        2. Otevřete sshd_config soubor: VI/etc/ssh/sshd_config
        3. V souboru vyhledejte řádek **PasswordAuthentication** a změňte hodnotu na **Ano**.
        4. Uložte soubor a zavřete ho. Restartujte službu SSH.
    - Pokud ke zjištění virtuálních počítačů se systémem Linux používáte uživatele root, ujistěte se, že se na virtuálních počítačích povoluje přihlašovací jméno uživatele root.
        1. Přihlaste se ke každému počítači se systémem Linux
        2. Otevřete sshd_config soubor: VI/etc/ssh/sshd_config
        3. V souboru vyhledejte řádek **PermitRootLogin** a změňte hodnotu na **Ano**.
        4. Uložte soubor a zavřete ho. Restartujte službu SSH.

## <a name="set-up-a-project"></a>Nastavení projektu

Nastavte nový projekt Azure Migrate.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. V **přehledu** vyberte **vytvořit projekt**.
5. V nástroji **vytvořit projekt** vyberte své předplatné Azure a skupinu prostředků. Vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pole pro název a oblast projektu](./media/tutorial-discover-aws/new-project.png)

7. Vyberte **Vytvořit**.
8. Počkejte několik minut, než se projekt Azure Migrate nasadí. **Azure Migrate: Nástroj pro vyhodnocení serveru** se ve výchozím nastavení přidá do nového projektu.

![Stránka zobrazující Nástroj pro vyhodnocení serveru přidaný ve výchozím nastavení](./media/tutorial-discover-aws/added-tool.png)

> [!NOTE]
> Pokud jste již vytvořili projekt, můžete použít stejný projekt k registraci dalších zařízení pro zjišťování a vyhodnocení většího počtu serverů. [Další informace](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Nastavení zařízení

Zařízení Azure Migrate je jednoduché zařízení, které využije Azure Migrate Server Assessment k tomu, abyste mohli provádět následující akce:

- Objevte místní servery.
- Odesílat metadata a data o výkonu pro zjištěné servery pro Azure Migrate posouzení serveru.

[Přečtěte si další informace](migrate-appliance.md) o zařízení Azure Migrate.

Nastavení zařízení:
1. Zadejte název zařízení a vygenerujte Azure Migrate klíč projektu na portálu.
1. Stáhněte si soubor ZIP pomocí skriptu Azure Migrate Installer z Azure Portal.
1. Extrahujte obsah ze souboru ZIP. Spusťte konzolu PowerShellu s oprávněními správce.
1. Spusťte skript prostředí PowerShell pro spuštění webové aplikace zařízení.
1. Nakonfigurujte zařízení poprvé a zaregistrujte ho pomocí Azure Migrate projektu pomocí klíče Azure Migrate projektu.

### <a name="1-generate-the-azure-migrate-project-key"></a>1. vygenerujte klíč projektu Azure Migrate.

1. V části **Cíle migrace** > **Servery** > **Azure Migrate: Hodnocení serverů** vyberte **Zjistit**.
2. V možnosti **zjišťovat počítače**  >  **jsou virtualizované počítače?** vyberte **fyzické nebo jiné (AWS, GCP, Xen atd.)**.
3. V **1: vygenerujte Azure Migrate klíč projektu**, zadejte název pro Azure Migrate zařízení, které nastavíte pro zjišťování fyzických nebo virtuálních serverů. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
1. Kliknutím na **vygenerovat klíč** spustíte vytváření požadovaných prostředků Azure. Během vytváření prostředků prosím Nezavírejte stránku zjišťovacích počítačů.
1. Po úspěšném vytvoření prostředků Azure se vygeneruje **klíč projektu Azure Migrate** .
1. Zkopírujte klíč, protože ho budete potřebovat k dokončení registrace zařízení během jeho konfigurace.

### <a name="2-download-the-installer-script"></a>2. Stáhněte si instalační skript.

V **2: Stáhněte zařízení Azure Migrate** a klikněte na **Stáhnout**.

### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad použití pro veřejný cloud: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Příklad použití pro oficiální Cloud: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Ověřte nejnovější verze zařízení a hodnoty hash:
    - Pro veřejný cloud:

        **Scénář** | **Stáhnout** _ | _ *Hodnota hash**
        --- | --- | ---
        Fyzický (85 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140334) | 207157bab39303dca1c2b93562d6f1deaa05aa7c992f480138e17977641163fb

    - Pro Azure Government:

        **Scénář** | **Stáhnout** _ | _ *Hodnota hash**
        --- | --- | ---
        Fyzický (85 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140338) | ca67e8dbe21d113ca93bfe94c1003ab7faba50472cb03972d642be8a466f78ce
 

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Spusťte skript instalačního programu Azure Migrate
Skript instalačního programu provede následující akce:

- Nainstaluje agenty a webovou aplikaci pro zjišťování a hodnocení fyzických serverů.
- Nainstalujte role Windows, včetně aktivační služby Windows, služby IIS a prostředí PowerShell ISE.
- Stáhněte a nainstalujte zapisovatelný modul IIS. [Další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) o trvalé podrobnosti nastavení pro Azure Migrate.
- Vytvoří následující soubory pod cestou:
    - **Konfigurační soubory**:%ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**:%ProgramData%\Microsoft Azure\Logs

Spusťte skript následujícím způsobem:

1. Extrahujte soubor zip do složky na serveru, který bude hostitelem zařízení.  Ujistěte se, že nespouštíte skript na počítači v existujícím zařízení Azure Migrate.
2. Na výše uvedeném serveru s oprávněním správce (zvýšené) spusťte PowerShell.
3. Změňte adresář PowerShellu na složku, do které byl obsah extrahován ze staženého souboru ZIP.
4. Spusťte skript s názvem **AzureMigrateInstaller.ps1** spuštěním následujícího příkazu:

    - Pro veřejný cloud: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Pro Azure Government: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    Skript spustí webovou aplikaci zařízení po úspěšném dokončení.

Pokud přecházíte mezi všemi problémy, získáte přístup k protokolům skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log pro řešení potíží.

### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se virtuální počítač zařízení může připojit k adresám URL Azure pro cloudy [veřejné](migrate-appliance.md#public-cloud-urls) a [státní správy](migrate-appliance.md#government-cloud-urls) .

### <a name="4-configure-the-appliance"></a>4. konfigurace zařízení

Nastavte zařízení poprvé.

1. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k zařízení, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy kliknutím na zástupce aplikace.
2. Přijměte **licenční podmínky** a přečtěte si informace třetích stran.
1. Ve webové aplikaci > **nastavení požadavků** postupujte takto:
    - **Připojení**: aplikace kontroluje, jestli má server přístup k Internetu. Pokud server používá proxy server:
        - Klikněte na **nastavit proxy server** na a zadejte adresu proxy serveru (ve formuláři http://ProxyIPAddress nebo na http://ProxyFQDN) naslouchajícím portu.
        - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
        - Podporuje se jen proxy protokolu HTTP.
        - Pokud jste přidali podrobnosti proxy serveru nebo zakážete proxy server nebo ověřování, kliknutím na **Uložit** spusťte kontrolu připojení znovu.
    - **Časová synchronizace**: čas je ověřený. Čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování serveru fungovalo správně.
    - **Instalovat aktualizace**: posouzení Azure Migrate serveru kontroluje, jestli má zařízení nainstalované nejnovější aktualizace. Po dokončení kontroly můžete kliknout na **Zobrazit služby zařízení** a zobrazit stav a verze komponent spuštěných na zařízení.

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Vložte **klíč projektu Azure Migrate** zkopírovaný z portálu. Pokud tento klíč nemáte, Projděte si část **vyhodnocení serveru> zjistit> spravovat existující zařízení**, vyberte název zařízení, který jste zadali v době generování klíče, a zkopírujte odpovídající klíč.
1. K ověření pomocí Azure budete potřebovat kód zařízení. Kliknutím na **přihlášení** se otevře modální okno s kódem zařízení, jak je znázorněno níže.

    ![Modální zobrazení kódu zařízení](./media/tutorial-discover-vmware/device-code.png)

1. Kliknutím na **zkopírovat kód & přihlášením** zkopírujte kód zařízení a otevřete výzvu k přihlášení Azure na nové kartě prohlížeče. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
1. Na kartě nový vložte kód zařízení a přihlaste se pomocí uživatelského jména a hesla Azure.
   
   Přihlášení pomocí PIN kódu se nepodporuje.
3. Pokud kartu přihlášení omylem zavřete bez přihlašování, budete muset aktualizovat kartu prohlížeče Configuration Manageru a povolit tak tlačítko pro přihlášení znovu.
1. Po úspěšném přihlášení se vraťte na předchozí kartu pomocí Správce konfigurace zařízení.
4. Pokud má uživatelský účet Azure použitý k protokolování správná [oprávnění](./tutorial-discover-physical.md) k prostředkům Azure vytvořeným během generování klíče, zahájí se registrace zařízení.
1. Po úspěšné registraci zařízení si můžete zobrazit podrobnosti o registraci kliknutím na **Zobrazit podrobnosti**.


## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Nyní se z zařízení připojte k fyzickým serverům, které se mají zjistit, a spusťte zjišťování.

1. V **kroku 1: zadejte přihlašovací údaje pro zjišťování fyzických nebo virtuálních serverů s Windows a Linux**, klikněte na **Přidat přihlašovací údaje**.
1. Pro Windows Server vyberte typ zdroje jako **Windows Server**, zadejte popisný název pro přihlašovací údaje, přidejte uživatelské jméno a heslo. Klikněte na **Uložit**.
1. Pokud používáte ověřování založené na heslech pro server Linux, vyberte typ zdroje jako server pro **Linux (založený na heslech)**, zadejte popisný název pro přihlašovací údaje a přidejte uživatelské jméno a heslo. Klikněte na **Uložit**.
1. Pokud používáte ověřování pomocí klíče SSH pro Linux Server, můžete vybrat typ zdroje jako **Server Linux (založený na klíči SSH)**, zadat popisný název pro přihlašovací údaje, přidat uživatelské jméno a vybrat soubor privátního klíče SSH. Klikněte na **Uložit**.

    - Azure Migrate podporuje privátní klíč SSH generovaný pomocí příkazu ssh-keygen s využitím algoritmů RSA, DSA, ECDSA a ed25519.
    - Aktuálně Azure Migrate nepodporuje klíč SSH založený na hesle. Použijte prosím klíč SSH bez hesla.
    - V současné době Azure Migrate nepodporuje soubor privátního klíče SSH generovaný pomocí výstupu.
    - Azure Migrate podporuje formát OpenSSH souboru privátního klíče SSH, jak je znázorněno níže:
    
    ![Formát podporovaný privátním klíčem SSH](./media/tutorial-discover-physical/key-format.png)


1. Pokud chcete přidat více přihlašovacích údajů najednou, klikněte na **Přidat další** a uložte a přidejte další přihlašovací údaje. Pro zjišťování fyzických serverů je podporováno více přihlašovacích údajů.
1. V **kroku 2: zadání podrobností o fyzickém nebo virtuálním serveru** klikněte na **Přidat zdroj zjišťování** a určete **IP adresu nebo plně kvalifikovaný název domény** serveru a popisný název přihlašovacích údajů pro připojení k serveru.
1. Můžete buď **přidat jednu položku** najednou, nebo **Přidat více položek** do jednoho přechodu. K dispozici je také možnost zadat podrobnosti o serveru prostřednictvím **importu CSV**.


    - Pokud zvolíte možnost **přidat jednu položku**, můžete zvolit typ operačního systému, zadat popisný název pro přihlašovací údaje, přidat **IP adresu serveru nebo plně kvalifikovaný název domény** a kliknout na **Uložit**.
    - Pokud zvolíte možnost **Přidat více položek**, můžete najednou přidat několik záznamů zadáním **IP adresy serveru nebo plně kvalifikovaného názvu domény** s popisným názvem pro přihlašovací údaje v textovém poli. **Ověřte** přidané záznamy a klikněte na **Uložit**.
    - Pokud zvolíte možnost **importovat sdílený svazek clusteru** _(ve výchozím nastavení je vybraný)_, můžete si stáhnout soubor šablony CSV a tento soubor naplnit pomocí **IP adresy serveru nebo plně kvalifikovaného názvu domény** a popisného názvu pro přihlašovací údaje. Pak soubor naimportujete do zařízení, **ověříte** záznamy v souboru a kliknete na **Uložit**.

1. Když kliknete na Uložit, zařízení se pokusí ověřit připojení k serverům, které jste přidali, a zobrazit v tabulce **stav ověření** na každém serveru.
    - Pokud se ověření serveru nepovede, zkontrolujte chybu kliknutím na tlačítko **ověření** ve sloupci Stav v tabulce. Opravte problém a znovu ověřte.
    - Pokud chcete odebrat server, klikněte na **Odstranit**.
1. Před zahájením zjišťování můžete znovu **ověřit** připojení k serverům.
1. Kliknutím na **Spustit zjišťování zahajte** zjišťování úspěšně ověřených serverů. Po úspěšném spuštění zjišťování můžete zjistit stav zjišťování proti každému serveru v tabulce.


Spustí se zjišťování. Bude trvat přibližně 2 minuty na server, aby se metadata zjištěného serveru zobrazila v Azure Portal.

## <a name="verify-servers-in-the-portal"></a>Ověření serverů na portálu

Po dokončení zjišťování můžete ověřit, že se servery zobrazují na portálu.

1. Otevřete řídicí panel služby Azure Migrate.
2. V **Azure Migrate-servery**  >  **Azure Migrate: na stránce posouzení serveru** klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="next-steps"></a>Další kroky

- [Posuzuje fyzické servery](tutorial-migrate-aws-virtual-machines.md) pro migraci na virtuální počítače Azure.
- [Zkontrolujte data](migrate-appliance.md#collected-data---physical) , která zařízení shromažďuje během zjišťování.
