---
title: Nastavení zařízení Azure Migrate pro VMware
description: Naučte se, jak nastavit zařízení Azure Migrate pro vyhodnocení a migraci virtuálních počítačů VMware.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: bac82b2939e5b6a674c75be2cd330dd0fa4b8487
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035752"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Nastavení zařízení pro virtuální počítače VMware

Podle tohoto článku nastavte zařízení Azure Migrate pro posouzení pomocí nástroje [Azure Migrate: Nástroj pro vyhodnocení serveru](migrate-services-overview.md#azure-migrate-server-assessment-tool) a migraci bez agenta pomocí nástroje [Azure Migrate: Server pro migraci](migrate-services-overview.md#azure-migrate-server-migration-tool) .

[Zařízení Azure Migrate](migrate-appliance.md) je jednoduché zařízení, které používá Azure Migrate: posouzení serveru a migrace serveru pro zjišťování serverů se systémem v vCenter Server, posílání konfigurace serveru a metadat výkonu do Azure a pro replikaci serverů pomocí migrace bez agenta.

Zařízení můžete nasadit pomocí několika metod:

- Vytvořte server na vCenter Server pomocí stažené šablony vajíček. Tato metoda je popsaná v tomto článku.
- Nastavte zařízení na existujícím serveru pomocí skriptu instalačního programu PowerShell. [Tato metoda](deploy-appliance-script.md) by se měla použít, pokud nemůžete použít šablonu vajíček nebo pokud jste v Azure Government.

Po vytvoření zařízení zkontrolujete, že se může připojit k Azure Migrate: posouzení serveru, zaregistrujte ho v projektu Azure Migrate a nakonfigurujte zařízení tak, aby zahájilo zjišťování.

## <a name="deploy-with-ova"></a>Nasazení pomocí vajíček

K nastavení zařízení pomocí šablony vajíček:
1. Zadejte název zařízení a vygenerujte Azure Migrate klíč projektu na portálu.
1. Stáhněte soubor šablony vajíček a naimportujte ho do vCenter Server. Ověřte, jestli je VAJÍČKa zabezpečená.
1. Vytvořte virtuální počítač zařízení ze souboru s VAJÍČKy a ověřte, že se může připojit k Azure Migrate.
1. Nakonfigurujete zařízení poprvé a zaregistrujete ho do projektu pomocí klíče projektu Azure Migrate.

### <a name="1-generate-the-azure-migrate-project-key"></a>1. vygenerujte klíč projektu Azure Migrate.

1. V části **Cíle migrace** > **Servery** > **Azure Migrate: Hodnocení serverů** vyberte **Zjistit**.
2. V možnosti **zjišťovat počítače**  >  **jsou vaše počítače virtualizované?** vyberte **Ano, pomocí VMware vSphere hypervisor**.
3. V **1: vygenerujte Azure Migrate klíč projektu**, zadejte název Azure Migrate zařízení, které nastavíte pro zjišťování serverů v prostředí VMware. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
1. Kliknutím na **vygenerovat klíč** spustíte vytváření požadovaných prostředků Azure. Během vytváření prostředků prosím Nezavírejte stránku zjišťování.
1. Po úspěšném vytvoření prostředků Azure se vygeneruje **klíč projektu Azure Migrate** .
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
3. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k serveru zařízení, a otevřete adresu URL Správce konfigurace zařízení: `https://appliance name or IP address: 44368` .

   Alternativně můžete otevřít Configuration Manager z plochy serveru zařízení tak, že vyberete zástupce Configuration Manageru.
1. Přijměte **licenční podmínky** a přečtěte si informace třetích stran.
1. V části Configuration Manager > **nastavit požadavky** proveďte následující kroky:
   - **Připojení**: zařízení kontroluje, jestli má server přístup k Internetu. Pokud server používá proxy server:
     - Klikněte na **nastavit proxy server** a zadejte adresu proxy serveru ve formuláři `http://ProxyIPAddress` nebo `http://ProxyFQDN` portu pro naslouchání.
     - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
     - Podporuje se jen proxy protokolu HTTP.
     - Pokud jste přidali podrobnosti proxy serveru nebo zakážete proxy server nebo ověřování, kliknutím na **Uložit** spusťte kontrolu připojení znovu.
   - **Čas synchronizace**: čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování fungovalo správně.
   - **Nainstalovat aktualizace**: zařízení zajišťuje, že jsou nainstalované nejnovější aktualizace. Po dokončení kontroly můžete kliknout na **Zobrazit služby zařízení** a zobrazit stav a verze služeb spuštěných na serveru zařízení.
   - **Instalace VDDK**: zařízení kontroluje, jestli je nainstalovaná VMware vSphere Virtual disk Development Kit (VDDK). Pokud není nainstalovaný, Stáhněte si z VMware VDDK 6,7 a Extrahujte stažený obsah souboru zip do zadaného umístění na zařízení, jak je uvedeno v **pokynech k instalaci**.

     Migrace Azure Migrate serveru používá VDDK k replikaci serverů během migrace do Azure. 
1. Pokud chcete, můžete kdykoli **znovu spustit požadované součásti** , abyste zkontrolovali, jestli zařízení splňuje všechny požadavky.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Panel 1 v nástroji Configuration Manager pro zařízení":::


## <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Vložte **klíč projektu Azure Migrate** zkopírovaný z portálu. Pokud tento klíč nemáte, Projděte si část **vyhodnocení serveru> zjistit> spravovat existující zařízení**, vyberte název zařízení, který jste zadali v době generování klíče, a zkopírujte odpovídající klíč.
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
    - Chcete-li omezit obor zjišťování na konkrétní objekty VMware (vCenter Server datových center, clustery, složku clusterů, hostitele, složku hostitelů nebo jednotlivé virtuální počítače), přečtěte si pokyny v [tomto článku](set-discovery-scope.md) a omezte účet používaný v Azure Migrate.
1. V **kroku 2: zadejte vCenter Server podrobnosti**, klikněte na **Přidat zdroj zjišťování** , abyste v rozevíracím seznamu vybrali popisný název pro přihlašovací údaje, zadejte **IP adresu nebo plně kvalifikovaný název domény** vCenter Server. **Port** můžete ponechat výchozí (443) nebo zadejte vlastní port, na kterém vCenter Server naslouchá, a klikněte na **Uložit**.
1. Po kliknutí na **Uložit** se zařízení pokusí ověřit připojení k vCenter Server s poskytnutými přihlašovacími údaji a zobrazit **stav ověření** v tabulce proti vCenter Server IP adrese nebo plně kvalifikovanému názvu domény.
1. Můžete znovu **ověřit** připojení k vCenter Server kdykoli před zahájením zjišťování.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panel 3 – Správce konfigurace zařízení pro podrobnosti o vCenter Server":::

### <a name="provide-server-credentials"></a>Zadání přihlašovacích údajů serveru

V **kroku 3: poskytnutí přihlašovacích údajů serveru pro inventarizaci softwaru, analýzu závislostí bez agentů a zjišťování SQL Server instancí a databází**, můžete buď zadat více přihlašovacích údajů serveru, nebo pokud tyto funkce nechcete využívat, můžete tento krok přeskočit a pokračovat v vCenter Server zjišťování. Svůj účel můžete kdykoli změnit později.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Panel 3 – Správce konfigurace zařízení pro podrobnosti o serveru":::

> [!Note]
> Zjišťování a hodnocení instancí SQL Server a databází spuštěných ve vašem prostředí VMware je teď ve verzi Preview. Chcete-li vyzkoušet tuto funkci, použijte [**Tento odkaz**](https://aka.ms/AzureMigrate/SQL) k vytvoření projektu v oblasti **Austrálie – východ** . Pokud projekt již máte v Austrálii – východ a chcete si vyzkoušet tuto funkci, ujistěte se, že jste tyto [**požadavky**](how-to-discover-sql-existing-project.md) dokončili na portálu.

Pokud chcete tyto funkce využít, můžete zadat přihlašovací údaje serveru podle následujících kroků. Zařízení se pokusí automaticky mapovat přihlašovací údaje na servery, aby se prováděly funkce zjišťování.

- Přihlašovací údaje serveru můžete přidat kliknutím na tlačítko **Přidat pověření** . Otevře se modální místo, kde můžete zvolit **typ pověření** z rozevírací nabídky.
- Můžete zadat přihlašovací údaje Domain/Windows (nepatří do domény)/Linux (nedoménové)/SQL Server ověřování. [Přečtěte si další informace](add-server-credentials.md) o tom, jak zadat přihlašovací údaje a jak je zpracovat.
- Pro každý typ přihlašovacích údajů musíte zadat popisný název přihlašovacích údajů, přidat **uživatelské jméno** a **heslo** a kliknout na **Uložit**.
- Pokud zvolíte možnost přihlašovací údaje domény, musíte také zadat plně kvalifikovaný název domény pro doménu. Plně kvalifikovaný název domény je nutný k ověření pravosti přihlašovacích údajů u služby Active Directory této domény.
- Projděte si [požadovaná oprávnění](add-server-credentials.md#required-permissions) k účtu pro zjišťování nainstalovaných aplikací, analýzu závislostí bez agenta nebo pro zjišťování instancí a databází SQL Server.
- Pokud chcete přidat více přihlašovacích údajů najednou, klikněte na **Přidat další** a uložte a přidejte další přihlašovací údaje.
- Když kliknete na **Uložit** nebo **Přidat další**, zařízení ověří přihlašovací údaje domény s adresářovou službou Active Directory této domény pro svůj pravost. K tomu je potřeba zabránit jakémukoli uzamčení účtu, když zařízení provede více iterací k mapování přihlašovacích údajů na příslušné servery.
- V tabulce přihlašovacích údajů můžete zobrazit **stav ověření** pro všechna pověření domény. Budou ověřeny pouze přihlašovací údaje domény.
- Pokud se ověření nepovede, můžete kliknout na stav **selhání** a zobrazit tak chybu. po vyřešení problému klikněte na znovu **ověřit přihlašovací údaje** , aby se znovu ověřily neúspěšná pověření domény.


### <a name="start-discovery"></a>Zahájení zjišťování

1. Kliknutím na **Spustit zjišťování Zahajte** vCenter Server zjišťování. Po úspěšném spuštění zjišťování můžete v tabulce sources (zdroje) ověřit stav zjišťování s vCenter Server IP adresou nebo plně kvalifikovaným názvem domény.
1. Pokud jste zadali přihlašovací údaje serveru, inventář softwaru (zjišťování nainstalovaných aplikací) se automaticky spustí po dokončení zjišťování vCenter Server. Inventář softwaru se provádí jednou za 12 hodin.
1. [Inventář softwaru](how-to-discover-applications.md) identifikuje instance SQL Server spuštěné na serverech a používá je, pokusí se o připojení k instancím pomocí ověřování systému Windows nebo přihlašovacích údajů SQL Server ověřování poskytnutých na zařízení a shromažďování dat v SQL Server databázích a jejich vlastnostech. Zjišťování SQL se provádí každých 24 hodin.
1. Během inventarizace softwaru budou přidané přihlašovací údaje k serverům iterovat na servery a ověřeny pro analýzu závislostí bez agentů. Pro servery z portálu můžete povolit analýzu závislostí bez agentů. Pro povolení analýzy závislostí bez agentů je možné vybrat pouze servery, na kterých je ověřování úspěšné.

Zjišťování funguje následujícím způsobem:
- Zobrazení inventáře zjištěných serverů na portálu trvá přibližně 15 minut.
- Zjišťování nainstalovaných aplikací může nějakou dobu trvat. Doba trvání závisí na počtu zjištěných serverů. Pro servery 500 trvá trvat přibližně jednu hodinu, než se zjištěný inventář zobrazí na portálu Azure Migrate.
- Po dokončení zjišťování serverů můžete na serverech na portálu povolit analýzu závislostí bez agentů.
- Data SQL Server instance a databází se začnou zobrazovat na portálu během 24 hodin od zahájení zjišťování.

## <a name="next-steps"></a>Další kroky

Přečtěte si kurzy pro [vyhodnocení VMware](./tutorial-assess-vmware-azure-vm.md) a [migraci bez agentů](tutorial-migrate-vmware.md).