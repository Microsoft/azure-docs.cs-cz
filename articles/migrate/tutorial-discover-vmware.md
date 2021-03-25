---
title: Zjišťování serverů běžících v prostředí VMware s Azure Migrate Discovery and Assessment
description: Naučte se zjišťovat místní servery běžící v prostředí VMware pomocí nástroje Azure Migrate Discovery and Assessment Tool.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/17/2021
ms.custom: mvc
ms.openlocfilehash: 1a0ad751a216e8da772fd5fdc96a0dc67cb27d01
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109844"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-discovery-and-assessment"></a>Kurz: zjišťování serverů běžících v prostředí VMware pomocí Azure Migrate: zjišťování a posouzení

Jako součást cesty migrace do Azure zjistíte místní inventář a zatížení.

V tomto kurzu se dozvíte, jak zjišťovat servery běžící v prostředí VMware pomocí Azure Migrate: Nástroj pro zjišťování a vyhodnocení pomocí odlehčeného Azure Migrateho zařízení. Zařízení nasadíte jako server běžící v vCenter Server, abyste nepřetržitě zjistili servery a jejich metadata o výkonu, aplikace spuštěné na serverech, závislosti serverů a instance SQL Server a databáze.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte účet Azure.
> * Připravte prostředí VMware pro zjišťování.
> * Vytvořte projekt.
> * Nastavte zařízení Azure Migrate.
> * Spusťte nepřetržité zjišťování.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti, pokud je to možné.  

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, Projděte si tyto požadavky.


**Požadavek** | **Podrobnosti**
--- | ---
**Hostitel vCenter Server/ESXi** | Potřebujete vCenter Server, na kterém běží verze 5,5, 6,0, 6,5 nebo 6,7.<br/><br/> Servery musí být hostované na hostiteli ESXi, na kterém běží verze 5,5 nebo novější.<br/><br/> Na vCenter Server povolte příchozí připojení na portu TCP 443, aby zařízení mohlo shromažďovat metadata o konfiguraci a výkonu.<br/><br/> Ve výchozím nastavení se zařízení připojí k vCenter Server na portu 443. Pokud vCenter Server naslouchá na jiném portu, můžete port změnit při zadání podrobností vCenter Server v nástroji Configuration Manager pro zařízení.<br/><br/> Na hostitelích ESXi se ujistěte, že je na portu TCP 443 povolený příchozí přístup, který umožňuje zjišťování nainstalovaných aplikací a analýzy závislostí bez agentů na serverech.
**Náplně** | vCenter Server potřebuje prostředky k přidělení serveru pro Azure Migrate zařízení:<br/><br/> -32 GB paměti RAM, 8 vCPU a asi 80 GB diskového úložiště.<br/><br/> – Externí virtuální přepínač a přístup k Internetu na serveru zařízení, přímo nebo prostřednictvím proxy serveru.
**Servery** | Všechny verze operačních systémů Windows a Linux jsou podporované pro zjišťování konfigurace a metadat výkonu. <br/><br/> Aby bylo možné provádět zjišťování aplikací na serverech, jsou podporovány všechny verze operačních systémů Windows a Linux. [Zde](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) najdete verzi operačního systému podporovanou pro analýzu závislostí bez agentů.<br/><br/> Aby bylo možné provést zjišťování nainstalovaných aplikací a analýzu závislostí bez agentů, musí být na serverech nainstalovány a spuštěny nástroje VMware (novější než 10.2.0). Windows servery musí mít nainstalované prostředí PowerShell verze 2,0 nebo novější.<br/><br/> Pokud chcete zjistit SQL Server instance a databáze, podívejte se [sem](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases) na podporované verze a edice SQL Server, podporované verze operačního systému Windows a mechanismy ověřování.

## <a name="prepare-an-azure-user-account"></a>Příprava uživatelského účtu Azure

Pokud chcete vytvořit projekt a zaregistrovat Azure Migrate zařízení, budete potřebovat účet s:

- Oprávnění přispěvatele nebo vlastníka v předplatném Azure
- Oprávnění k registraci aplikací Azure Active Directory (AAD)
- Vlastník nebo přispěvatel plus oprávnění správce přístupu k předplatnému Azure k vytvoření Key Vault používaného během migrace serveru bez agenta

Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem vašeho předplatného. Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte oprávnění následujícím způsobem:

1. V Azure Portal vyhledejte "předplatná" a v části **služby** vyberte **předplatná**.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Vyhledávací pole pro hledání předplatného Azure":::


2. Na stránce **předplatná** vyberte předplatné, ve kterém chcete vytvořit projekt.
3. V předplatném vyberte **řízení přístupu (IAM)**  >  **Kontrola přístupu**.
4. V části **kontrolovat přístup** vyhledejte příslušný uživatelský účet.
5. V nabídce **Přidat přiřazení role** klikněte na **Přidat**.
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="Vyhledejte uživatelský účet pro kontrolu přístupu a přiřazení role.":::
    
6. V části **Přidat přiřazení role** vyberte roli přispěvatel nebo vlastník a v našem příkladu vyberte účet (azmigrateuser). Potom klikněte na **Uložit**.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="Otevře stránku přidat přiřazení role, která účtu přiřadí roli.":::

1. Aby bylo možné zařízení zaregistrovat, váš účet Azure potřebuje **oprávnění k registraci aplikací AAD.**
1. V Azure Portal přejděte na   >    >  **uživatelské nastavení** Azure Active Directory uživatelé.
1. V **nastavení uživatele** ověřte, že uživatelé Azure AD můžou registrovat aplikace (ve výchozím nastavení nastavené na **Ano** ).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="Ověřte v uživatelských nastaveních, která můžou uživatelé registrovat v aplikacích Active Directory.":::

9. Pokud je nastavení ' Registrace aplikací ' nastaveno na hodnotu ' ne ', požádejte tenanta/globálního správce, aby přiřadil požadované oprávnění. Alternativně může tenant nebo globální správce přiřadit roli **vývojář aplikace** k účtu, aby umožnil registraci aplikace AAD. [Další informace](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>Příprava VMware

V vCenter Server ověřte, že váš účet má oprávnění k vytvoření virtuálního počítače pomocí souboru sady vajíček. To je potřeba, když nasadíte Azure Migrate zařízení jako virtuální počítač VMware pomocí souboru sady vajíček.

Azure Migrate potřebuje vCenter Server účet jen pro čtení pro zjišťování a hodnocení serverů, které běží ve vašem prostředí VMware. Pokud chcete také provést zjišťování nainstalovaných aplikací a analýzu závislostí bez agenta, musí mít účet povolená oprávnění pro **Virtual Machines > operace hostů**.

### <a name="create-an-account-to-access-vcenter"></a>Vytvoření účtu pro přístup k serveru vCenter

V vSphere webovém klientovi nastavte účet následujícím způsobem:

1. Pomocí účtu s oprávněními správce ve webovém klientovi vSphere > vyberte možnost **Správa**.
2. **Přístup**, vyberte možnost **Uživatelé a skupiny jednotného přihlašování**.
3. V **Uživatelé** přidejte nového uživatele.
4. Do pole **Nový uživatel** zadejte podrobnosti o účtu. Pak klikněte na **OK**.
5. V části **globální oprávnění** vyberte uživatelský účet a přiřaďte k účtu roli jen **pro čtení** . Pak klikněte na **OK**.
6. Pokud chcete také provést zjišťování nainstalovaných aplikací a analýzu závislostí bez agenta, přejít na **role** > vyberte roli jen **pro čtení** a v části **oprávnění** vyberte **operace hostů**. Oprávnění můžete rozšířit na všechny objekty pod vCenter Server tím, že vyberete zaškrtávací políčko šířit do podřízených.

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="Zaškrtnutím políčka povolíte operace hostů u role jen pro čtení.":::


> [!NOTE]
> Zjišťování můžete omezit na konkrétní vCenter Server datacentra, clustery, složku clusterů, hostitele, složku hostitelů nebo jednotlivé servery, a to pomocí oboru vCenter Server. [**Přečtěte si další informace**](set-discovery-scope.md) o určení rozsahu vCenter Server uživatelského účtu.

### <a name="create-an-account-to-access-servers"></a>Vytvoření účtu pro přístup k serverům

Potřebujete uživatelský účet s potřebnými oprávněními na serverech, abyste mohli provádět zjišťování nainstalovaných aplikací, analýzu závislostí bez agenta a zjišťování instancí SQL Server a databází. Uživatelský účet můžete zadat v nástroji Configuration Manager pro zařízení. Zařízení neinstaluje žádné agenty na servery.

1. Pro Windows servery vytvořte účet (místní nebo doména) s oprávněními správce na serverech. Chcete-li zjistit SQL Server instance a databáze, budete potřebovat účet Windows nebo SQL Server, aby byl členem role serveru sysadmin. [Přečtěte si další informace](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) o přiřazení požadované role k uživatelskému účtu.
2. U serverů se systémem Linux vytvořte účet s oprávněními root. Alternativně můžete vytvořit účet s těmito oprávněními pro soubory/bin/netstat a/bin/ls: CAP_DAC_READ_SEARCH a CAP_SYS_PTRACE.

> [!NOTE]
> V nástroji Configuration Manager teď můžete přidat několik přihlašovacích údajů k serveru, abyste mohli provádět zjišťování nainstalovaných aplikací, analýzu závislostí bez agentů a zjišťování SQL Server instancí a databází. Můžete přidat více přihlašovacích údajů k doméně/Windows (jiné než doména)/Linux (nedoménové) a/nebo SQL Server ověřování. [**Další informace**](add-server-credentials.md)

## <a name="set-up-a-project"></a>Nastavení projektu

Nastavte nový projekt.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. V **přehledu** vyberte **vytvořit projekt**.
5. V nástroji **vytvořit projekt** vyberte své předplatné Azure a skupinu prostředků. Vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="Pole pro název a oblast projektu":::

7. Vyberte **Vytvořit**.
8. Počkejte několik minut, než se projekt nasadí. Nástroj **Azure Migrate: Discovery and Assessment** Tool je ve výchozím nastavení přidán do nového projektu.

> [!NOTE]
> Pokud jste již vytvořili projekt, můžete použít stejný projekt k registraci dalších zařízení ke zjišťování a hodnocení více. serverů. [ **Další informace**](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Nastavení zařízení

Azure Migrate: zjišťování a posouzení používá odlehčené Azure Migrate zařízení. Zařízení provádí zjišťování serveru a odesílá metadata konfigurace a výkonu serveru Azure Migrate. Zařízení je možné nastavit nasazením šablony vajíček, kterou je možné stáhnout z projektu.

> [!NOTE]
> Pokud z nějakého důvodu nemůžete zařízení nastavit pomocí šablony, můžete ho nastavit pomocí skriptu PowerShellu na existujícím serveru Windows Server 2016. [**Další informace**](deploy-appliance-script.md#set-up-the-appliance-for-vmware)

### <a name="deploy-with-ova"></a>Nasazení pomocí vajíček

K nastavení zařízení pomocí šablony vajíček:

1. Zadejte název zařízení a vygenerujte klíč projektu na portálu.
1. Stáhněte soubor šablony vajíček a naimportujte ho do vCenter Server. Ověřte, jestli je VAJÍČKa zabezpečená.
1. Vytvořte zařízení ze souboru s VAJÍČKy a ověřte, že se může připojit k Azure Migrate.
1. Nakonfigurujete zařízení poprvé a zaregistrujete ho do projektu pomocí klíče projektu.

### <a name="1-generate-the-project-key"></a>1. vygenerujte klíč projektu.

1. V **cíli migrace** se  >  **systémy Windows, Linux a SQL Server**  >  **Azure Migrate: zjišťování a posouzení** vyberte **Vyhledat**.
2. V možnosti **zjišťovat servery**  >  **jsou vaše servery virtualizované?** vyberte **Ano, pomocí VMware vSphere hypervisor**.
3. V části **1: vygenerovat klíč projektu** zadejte název zařízení Azure Migrate, které nastavíte pro zjišťování serverů v prostředí VMware. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
1. Kliknutím na **vygenerovat klíč** spustíte vytváření požadovaných prostředků Azure. Nezavírejte stránku zjišťování během vytváření prostředků.
1. Po úspěšném vytvoření prostředků Azure se vygeneruje **klíč projektu** .
1. Zkopírujte klíč, protože ho budete potřebovat k dokončení registrace zařízení během jeho konfigurace.

### <a name="2-download-the-ova-template"></a>2. Stáhněte si šablonu pro VAJÍČKu

V **2: Stáhněte zařízení Azure Migrate** vyberte. Soubor vajíček a klikněte na **Stáhnout**.

### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením ověřte, zda je soubor sady vajíček zabezpečený:

1. Na serveru, na který jste stáhli soubor, otevřete okno příkazového řádku správce.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor sady vajíček:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Příklady použití: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Ověřte nejnovější verze zařízení a hodnoty hash:

    - Pro veřejný cloud Azure:
    
        **Algoritmus** | **Stáhnout** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Pro Azure Government:
    
        **Algoritmus** | **Stáhnout** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-server"></a>3. vytvoření serveru zařízení

Import staženého souboru a vytvoření serveru v prostředí VMware

1. V klientské konzole vSphere klikněte na **soubor**  >  **nasadit šablonu OVF**.
2. V Průvodci nasazením šablony OVF > **zdroj** zadejte umístění souboru vajíček.
3. Do pole **název** a **umístění** zadejte popisný název serveru. Vyberte objekt inventáře, do kterého bude Server hostovat.
5. V části **hostitel nebo cluster** zadejte hostitele nebo cluster, na kterém bude server běžet.
6. V části **Storage (úložiště**) zadejte cíl úložiště serveru.
7. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
8. V části **mapování sítě** určete síť, ke které se bude server připojovat. K posílání metadat do Azure Migrate potřebuje síť připojení k Internetu.
9. Zkontrolujte a ověřte všechna nastavení a pak klikněte na **Finish** (Dokončit).


### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se server zařízení může připojit k adresám URL Azure pro cloudy [veřejné](migrate-appliance.md#public-cloud-urls) a [státní správy](migrate-appliance.md#government-cloud-urls) .


### <a name="4-configure-the-appliance"></a>4. konfigurace zařízení

Nastavte zařízení poprvé.

> [!NOTE]
> Pokud zařízení nastavíte pomocí [**skriptu PowerShellu**](deploy-appliance-script.md) namísto stažené sady vajíček, první dva kroky v tomto postupu nejsou relevantní.

1. V klientské konzole vSphere klikněte pravým tlačítkem na server a pak vyberte **otevřít konzolu**.
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k zařízení, a otevřete adresu URL Správce konfigurace zařízení: `https://appliance name or IP address: 44368` .

   Alternativně můžete otevřít Configuration Manager z plochy serveru zařízení tak, že vyberete zástupce Configuration Manageru.
1. Přijměte **licenční podmínky** a přečtěte si informace třetích stran.
1. V části Configuration Manager > **nastavit požadavky** proveďte následující kroky:
   - **Připojení**: zařízení kontroluje, jestli má server přístup k Internetu. Pokud server používá proxy server:
     - Klikněte na **instalační program proxy** a zadejte adresu proxy serveru `http://ProxyIPAddress` nebo `http://ProxyFQDN` port naslouchání.
     - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
     - Podporuje se jen proxy protokolu HTTP.
     - Pokud jste přidali podrobnosti proxy serveru nebo zakážete proxy server nebo ověřování, kliknutím na **Uložit** spusťte kontrolu připojení znovu.
   - **Čas synchronizace**: čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování fungovalo správně.
   - **Nainstalovat aktualizace**: zařízení zajišťuje, že jsou nainstalované nejnovější aktualizace. Po dokončení kontroly můžete kliknout na **Zobrazit služby zařízení** a zobrazit stav a verze služeb spuštěných na serveru zařízení.
   - **Instalace VDDK**: zařízení kontroluje, jestli je nainstalovaná VMware vSphere Virtual disk Development Kit (VDDK). Pokud není nainstalovaný, Stáhněte si z VMware VDDK 6,7 a Extrahujte stažený obsah souboru zip do zadaného umístění na zařízení, jak je uvedeno v **pokynech k instalaci**.

     Migrace Azure Migrate serveru používá VDDK k replikaci serverů během migrace do Azure. 
1. Pokud chcete, můžete kdykoli **znovu spustit požadované součásti** , abyste zkontrolovali, jestli zařízení splňuje všechny požadavky.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Panel 1 v nástroji Configuration Manager pro zařízení":::

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Vložte **klíč projektu** zkopírovaný z portálu. Pokud nemáte klíč, podívejte se na **Azure Migrate: zjišťování a hodnocení> zjistit> spravovat existující zařízení**, vyberte název zařízení, který jste zadali v době generování klíče, a zkopírujte odpovídající klíč.
1. K ověření pomocí Azure budete potřebovat kód zařízení. Kliknutím na **přihlášení** se otevře modální okno s kódem zařízení, jak je znázorněno níže.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modální zobrazení kódu zařízení":::

1. Kliknutím na **zkopírovat kód & přihlášením** zkopírujte kód zařízení a otevřete výzvu k přihlášení Azure na nové kartě prohlížeče. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
1. Na kartě nový vložte kód zařízení a přihlaste se pomocí uživatelského jména a hesla Azure.
   
   Přihlášení pomocí PIN kódu se nepodporuje.
3. Pokud kartu přihlášení omylem zavřete bez přihlašování, budete muset aktualizovat kartu prohlížeče Configuration Manageru a povolit tak tlačítko pro přihlášení znovu.
1. Po úspěšném přihlášení se vraťte na předchozí kartu pomocí Správce konfigurace zařízení.
1. Pokud má uživatelský účet Azure použitý k protokolování správná oprávnění k prostředkům Azure vytvořeným během generování klíče, zahájí se registrace zařízení.
1. Po úspěšné registraci zařízení si můžete zobrazit podrobnosti o registraci kliknutím na **Zobrazit podrobnosti**.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Panel 2 – Správce konfigurace zařízení":::

## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

### <a name="provide-vcenter-server-details"></a>Zadejte vCenter Server podrobnosti.

Zařízení se musí připojit k vCenter Server, aby bylo možné zjistit údaje o konfiguraci a výkonu serverů.

1. V **kroku 1: zadejte vCenter Server přihlašovací údaje**, klikněte na **Přidat přihlašovací údaje** , abyste zadali popisný název pro přihlašovací údaje, přidejte **uživatelské jméno** a **heslo** pro účet vCenter Server, který bude zařízení používat pro zjišťování serverů, na kterých vCenter Server.
    - Měli byste mít nastaven účet s požadovanými oprávněními, jak je popsáno výše v tomto článku.
    - Chcete-li omezit obor zjišťování na konkrétní objekty VMware (vCenter Server datových center, clustery, složku clusterů, hostitele, složku hostitelů nebo jednotlivé servery), přečtěte si pokyny v [tomto článku](set-discovery-scope.md) a omezte účet používaný v Azure Migrate.
1. V **kroku 2: zadejte vCenter Server podrobnosti**, klikněte na **Přidat zdroj zjišťování** , abyste v rozevíracím seznamu vybrali popisný název pro přihlašovací údaje, zadejte **IP adresu nebo plně kvalifikovaný název domény** vCenter Server. **Port** můžete ponechat výchozí (443) nebo zadejte vlastní port, na kterém vCenter Server naslouchá, a klikněte na **Uložit**.
1. Po kliknutí na **Uložit** se zařízení pokusí ověřit připojení k vCenter Server s poskytnutými přihlašovacími údaji a zobrazit **stav ověření** v tabulce proti vCenter Server IP adrese nebo plně kvalifikovanému názvu domény.
1. Před zahájením zjišťování můžete vCenter Server kdykoli znovu **ověřit** připojení.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panel 3 – Správce konfigurace zařízení pro podrobnosti o vCenter Server":::

### <a name="provide-server-credentials"></a>Zadání přihlašovacích údajů serveru

V **kroku 3: poskytnutí přihlašovacích údajů serveru pro inventarizaci softwaru, analýzu závislostí bez agentů a zjišťování SQL Server instancí a databází**, můžete buď zadat více přihlašovacích údajů serveru, nebo pokud tyto funkce nechcete používat, můžete tento krok přeskočit a pokračovat v vCenter Server zjišťování. Svůj záměr můžete změnit kdykoli později.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Panel 3 – Správce konfigurace zařízení pro podrobnosti o serveru":::

Pokud chcete tyto funkce použít, můžete zadat přihlašovací údaje serveru podle následujících kroků. Zařízení se pokusí automaticky mapovat přihlašovací údaje na servery, aby se prováděly funkce zjišťování.

- Přihlašovací údaje serveru můžete přidat kliknutím na tlačítko **Přidat pověření** . Otevře se modální místo, kde můžete zvolit **typ pověření** z rozevírací nabídky.
- Můžete zadat přihlašovací údaje Domain/Windows (nepatří do domény)/Linux (nedoménové)/SQL Server ověřování. [Přečtěte si další informace](add-server-credentials.md) o tom, jak zadat přihlašovací údaje a jak je zpracovat.
- Pro každý typ přihlašovacích údajů musíte zadat popisný název přihlašovacích údajů, přidat **uživatelské jméno** a **heslo** a kliknout na **Uložit**.
- Pokud zvolíte možnost přihlašovací údaje domény, musíte také zadat plně kvalifikovaný název domény pro doménu. Plně kvalifikovaný název domény je nutný k ověření pravosti přihlašovacích údajů u služby Active Directory této domény.
- Projděte si [požadovaná oprávnění](add-server-credentials.md#required-permissions) k účtu pro zjišťování nainstalovaných aplikací, analýzu závislostí bez agenta nebo pro zjišťování instancí a databází SQL Server.
- Pokud chcete přidat více přihlašovacích údajů najednou, klikněte na **Přidat další** a uložte a přidejte další přihlašovací údaje.
- Když kliknete na **Uložit** nebo **Přidat další**, zařízení ověří přihlašovací údaje domény s adresářovou službou Active Directory této domény pro svůj pravost. K tomu je potřeba zabránit jakémukoli uzamčení účtu, když zařízení provede více iterací k mapování přihlašovacích údajů na příslušné servery.
- V tabulce přihlašovacích údajů můžete zobrazit **stav ověření** pro všechna pověření domény. Budou ověřeny pouze přihlašovací údaje domény.
- Pokud se ověření nepovede, můžete kliknout na stav **selhání** a zobrazit tak chybu. po vyřešení problému klikněte na znovu **ověřit přihlašovací údaje** , aby se znovu ověřily neúspěšná pověření domény.

     :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Panel 3 – Správce konfigurace zařízení pro poskytování více přihlašovacích údajů":::

### <a name="start-discovery"></a>Zahájení zjišťování

1. Kliknutím na **Spustit zjišťování Zahajte** vCenter Server zjišťování. Po úspěšném spuštění zjišťování můžete v tabulce sources (zdroje) ověřit stav zjišťování s vCenter Server IP adresou nebo plně kvalifikovaným názvem domény.
1. Pokud jste zadali přihlašovací údaje serveru, inventář softwaru (zjišťování nainstalovaných aplikací) se automaticky spustí po dokončení zjišťování vCenter Server. Inventář softwaru se provádí jednou za 12 hodin.
1. [Inventář softwaru](how-to-discover-applications.md) identifikuje instance SQL Server spuštěné na serverech a používá je, pokusí se o připojení k instancím pomocí ověřování systému Windows nebo přihlašovacích údajů SQL Server ověřování poskytnutých na zařízení a shromažďování dat v SQL Server databázích a jejich vlastnostech. Zjišťování SQL se provádí každých 24 hodin.
1. Během inventarizace softwaru budou přidané přihlašovací údaje k serverům iterovat na servery a ověřeny pro analýzu závislostí bez agentů. Pro servery z portálu můžete povolit analýzu závislostí bez agentů. Pro povolení analýzy závislostí bez agentů je možné vybrat pouze servery, na kterých je ověřování úspěšné.

> [!Note]
>Azure Migrate zašifruje komunikaci mezi Azure Migrate zařízením a instancemi SQL Server zdrojového kódu (s vlastností šifrovat připojení nastavenou na hodnotu TRUE). Tato připojení jsou šifrovaná pomocí [**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (nastavená na hodnotu true); Transportní vrstva bude používat protokol SSL k šifrování kanálu a obejít řetěz certifikátů k ověření vztahu důvěryhodnosti. Server zařízení musí být nastavený tak, aby [**důvěřoval kořenové autoritě certifikátu**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Pokud se při spuštění na serveru nezřídí žádný certifikát, SQL Server vygeneruje certifikát podepsaný svým držitelem, který se používá k šifrování přihlašovacích paketů. [**Další informace**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)

Zjišťování funguje následujícím způsobem:

- Zobrazení inventáře zjištěných serverů na portálu trvá přibližně 15 minut.
- Zjišťování nainstalovaných aplikací může nějakou dobu trvat. Doba trvání závisí na počtu zjištěných serverů. Pro servery 500 trvá trvat přibližně jednu hodinu, než se zjištěný inventář zobrazí na portálu Azure Migrate.
- Po dokončení zjišťování serverů můžete na serverech na portálu povolit analýzu závislostí bez agentů.
- Data SQL Server instance a databází se začnou zobrazovat na portálu během 24 hodin od zahájení zjišťování.

## <a name="next-steps"></a>Další kroky

- [Vyhodnoťte servery](./tutorial-assess-vmware-azure-vm.md) pro migraci na virtuální počítače Azure.
- [Posuzuje servery SQL](./tutorial-assess-sql.md) pro migraci do Azure SQL.
- [Zkontrolujte data](migrate-appliance.md#collected-data---vmware) , která zařízení shromažďuje během zjišťování.
