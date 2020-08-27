---
title: Posouzení fyzických serverů pro migraci do Azure pomocí posouzení serveru Azure Migrate
description: Popisuje postup vyhodnocení místních fyzických serverů pro migraci do Azure pomocí Azure Migrate posouzení serveru.
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: a8a7cc3734bc7a36afc307526cd41634ccaf00dc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933813"
---
# <a name="assess-physical-servers-with-azure-migrateserver-assessment"></a>Vyhodnotit fyzické servery pomocí Azure Migrate: posouzení serveru

V tomto článku se dozvíte, jak pomocí nástroje Azure Migrate: Server Assessment Tool zhodnotit místní fyzické servery.

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů, které vám pomůžou zjišťovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum zahrnuje nástroje pro Azure Migrate a nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Tento kurz je druhý v řadě, který ukazuje, jak vyhodnocovat a migrovat fyzické servery do Azure. V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavte Azure Migrate projekt.
> * Nastavte Azure Migrate zařízení, které běží místně, aby posoudilo fyzické servery.
> * Spusťte nepřetržité zjišťování místních fyzických serverů. Zařízení odesílá údaje o konfiguraci a výkonu pro zjištěné servery do Azure.
> * Seskupit zjištěné servery a vyhodnotit skupinu serverů.
> * Projděte si posouzení.

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v článcích s postupy.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Předpoklady

- [Dokončete](tutorial-prepare-physical.md) první kurz v této sérii. Pokud to neuděláte, pokyny v tomto kurzu nebudou fungovat.
- Tady je seznam toho, co byste měli udělat v prvním kurzu:
    - [Nastavte oprávnění Azure](tutorial-prepare-physical.md) pro Azure Migrate.
    - [Připravte fyzické servery](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) na posouzení. Je třeba ověřit požadavky na zařízení. Měli byste mít také nastavený účet pro zjišťování fyzického serveru. Požadované porty by měly být k dispozici a měli byste si uvědomit o adresách URL potřebných pro přístup k Azure.




## <a name="set-up-an-azure-migrate-project"></a>Nastavení Azure Migrateho projektu

Následujícím způsobem nastavte nový projekt Azure Migrate.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. Na stránce **Přehled** v části **Zjistit, posoudit a migrovat servery** klikněte na **Posoudit a migrovat servery**.

    ![Zjišťování a vyhodnocení serverů](./media/tutorial-assess-physical/assess-migrate.png)

4. V části **Začínáme** klikněte na **Přidat nástroje**.
5. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.  
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

    - Zeměpisná oblast projektu se používá jenom k ukládání metadat shromážděných z místních serverů.
    - Při spouštění migrace můžete vybrat jakoukoli cílovou oblast.

    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-physical/migrate-project.png)


7. Klikněte na **Next** (Další).
8. V **nástroji vybrat nástroj pro posouzení**vyberte **Azure Migrate: vyhodnocení serveru**  >  **Další**.

    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-physical/assessment-tool.png)

9. V části **Vybrat nástroj pro migraci** vyberte **V tuto chvíli přeskočit přidání nástroje pro migraci** > **Další**.
10. V části **Revize + přidat nástroje**zkontrolujte nastavení a klikněte na **Přidat nástroje**.
11. Počkejte několik minut, než se projekt Azure Migrate nasadí. Budete přesměrováni na stránku projektu. Pokud se projekt nezobrazí, můžete k němu přejít z části **Servery** na řídicím panelu služby Azure Migrate.


## <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate

Azure Migrate: posouzení serveru spouští odlehčené zařízení.

- Toto zařízení provádí zjišťování fyzického serveru a odesílá údaje o metadatech a výkonu serveru pro Azure Migrate posouzení serveru.
- Nastavení zařízení:
    - Stáhněte si soubor ZIP pomocí skriptu Azure Migrate Installer z Azure Portal.
    - Extrahujte obsah ze souboru ZIP. Spusťte konzolu PowerShellu s oprávněními správce.
    - Spusťte skript prostředí PowerShell pro spuštění webové aplikace zařízení.
    - Nakonfigurujete zařízení poprvé a zaregistrujete ho do projektu Azure Migrate.
- Pro jeden Azure Migrate projekt můžete nastavit více zařízení. U všech zařízení můžete zjistit libovolný počet fyzických serverů. Pro každé zařízení je možné zjistit maximálně 1000 serverů.

### <a name="generate-the-azure-migrate-project-key"></a>Vygenerovat klíč projektu Azure Migrate

1. V **Azure Migrate cíle migrace**  >  **Servers**  >  **Azure Migrate: Server Assessment**vyberte **Vyhledat**.
2. V možnosti **zjišťovat počítače**  >  **jsou virtualizované počítače?** vyberte **fyzické nebo jiné (AWS, GCP, Xen atd.)**.
3. V **1: vygenerujte Azure Migrate klíč projektu**, zadejte název pro Azure Migrate zařízení, které nastavíte pro zjišťování fyzických nebo virtuálních serverů. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
1. Kliknutím na **vygenerovat klíč** spustíte vytváření požadovaných prostředků Azure. Během vytváření prostředků prosím Nezavírejte stránku zjišťovacích počítačů.
1. Po úspěšném vytvoření prostředků Azure se vygeneruje **klíč projektu Azure Migrate** .
1. Zkopírujte klíč, protože ho budete potřebovat k dokončení registrace zařízení během jeho konfigurace.

### <a name="download-the-installer-script"></a>Stažení instalačního skriptu

V **2: Stáhněte zařízení Azure Migrate**a klikněte na **Stáhnout**.

   ![Výběry pro zjišťování počítačů](./media/tutorial-assess-physical/servers-discover.png)


   ![Výběry pro vygenerování klíče](./media/tutorial-assess-physical/generate-key-physical.png)


### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad použití pro veřejný cloud: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Příklad použití pro oficiální Cloud: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Ověřte nejnovější verze zařízení a hodnoty hash:
    - Pro veřejný cloud:

        **Scénář** | **Stáhnout*** | **Hodnota hash**
        --- | --- | ---
        Fyzický (85 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140334) | 5d0a3dbce4b5010980d59d49859f809acfeb17f5a36f57af4dac44a0a62dde1f

    - Pro Azure Government:

        **Scénář** | **Stáhnout*** | **Hodnota hash**
        --- | --- | ---
        Fyzický (85 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140338) | 1545f9ce8874cedef6347c1a1332f8b5eabd6811a017440a2382525fb0430309

### <a name="run-the-azure-migrate-installer-script"></a>Spusťte skript instalačního programu Azure Migrate

Skript instalačního programu provede následující akce:

- Nainstaluje agenty a webovou aplikaci pro zjišťování a hodnocení fyzických serverů.
- Nainstalujte role Windows, včetně aktivační služby Windows, služby IIS a prostředí PowerShell ISE.
- Stáhněte a nainstalujte zapisovatelný modul IIS. [Přečtěte si další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) o trvalé podrobnosti nastavení pro Azure Migrate.
- Vytvoří následující soubory pod cestou:
    - **Konfigurační soubory**:%ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**:%ProgramData%\Microsoft Azure\Logs

Spusťte skript následujícím způsobem:

1. Extrahujte soubor. zip do složky na serveru, který bude hostovat zařízení.  Ujistěte se, že nespouštíte skript na počítači v existujícím zařízení Azure Migrate.
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

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro cloudy [veřejné](migrate-appliance.md#public-cloud-urls) a [státní správy](migrate-appliance.md#government-cloud-urls) .


### <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavte zařízení poprvé.

1. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k zařízení, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy kliknutím na zástupce aplikace.
2. Přijměte **licenční podmínky**a přečtěte si informace třetích stran.
1. Ve webové aplikaci > **nastavení požadavků**postupujte takto:
    - **Připojení**: aplikace kontroluje, jestli má server přístup k Internetu. Pokud server používá proxy server:
        - Klikněte na **nastavit proxy server** na a zadejte adresu proxy serveru (ve formuláři http://ProxyIPAddress nebo na http://ProxyFQDN) naslouchajícím portu.
        - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
        - Podporuje se jen proxy protokolu HTTP.
        - Pokud jste přidali podrobnosti proxy serveru nebo zakážete proxy server nebo ověřování, kliknutím na **Uložit** spusťte kontrolu připojení znovu.
    - **Časová synchronizace**: čas je ověřený. Čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování serveru fungovalo správně.
    - **Instalovat aktualizace**: posouzení Azure Migrate serveru kontroluje, jestli má zařízení nainstalované nejnovější aktualizace. Po dokončení kontroly můžete kliknout na **Zobrazit služby zařízení** a zobrazit stav a verze komponent spuštěných na zařízení.

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Vložte **klíč projektu Azure Migrate** zkopírovaný z portálu. Pokud tento klíč nemáte, Projděte si část **vyhodnocení serveru> zjistit> spravovat existující zařízení**, vyberte název zařízení, který jste zadali v době generování klíče, a zkopírujte odpovídající klíč.
1. Klikněte na **Přihlásit se**. Otevře se výzva k přihlášení Azure na nové kartě prohlížeče. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
1. Na nové kartě se přihlaste pomocí uživatelského jména a hesla Azure.
   
   Přihlášení pomocí PIN kódu se nepodporuje.
3. Po úspěšném přihlášení se vraťte k webové aplikaci. 
4. Pokud má uživatelský účet Azure použitý k protokolování správná [oprávnění](tutorial-prepare-physical.md) k prostředkům Azure vytvořeným během generování klíče, zahájí se registrace zařízení.
1. Po úspěšné registraci zařízení si můžete zobrazit podrobnosti o registraci kliknutím na **Zobrazit podrobnosti**.


## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Nyní se z zařízení připojte k fyzickým serverům, které se mají zjistit, a spusťte zjišťování.

1. V **kroku 1: zadejte přihlašovací údaje pro zjišťování fyzických nebo virtuálních serverů s Windows a Linux**, klikněte na **Přidat přihlašovací údaje** a zadejte popisný název pro přihlašovací údaje, přidejte **uživatelské jméno** a **heslo** pro server s Windows nebo Linux. Klikněte na **Uložit**.
1. Pokud chcete přidat více přihlašovacích údajů najednou, klikněte na **Přidat další** a uložte a přidejte další přihlašovací údaje. Pro zjišťování fyzických serverů je podporováno více přihlašovacích údajů.
1. V **kroku 2: zadání podrobností o fyzickém nebo virtuálním serveru**klikněte na **Přidat zdroj zjišťování** a určete **IP adresu nebo plně kvalifikovaný název domény** serveru a popisný název přihlašovacích údajů pro připojení k serveru.
1. Můžete buď **přidat jednu položku** najednou, nebo **Přidat více položek** do jednoho přechodu. K dispozici je také možnost zadat podrobnosti o serveru prostřednictvím **importu CSV**.

    ![Výběry pro přidání zdroje zjišťování](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Pokud zvolíte možnost **přidat jednu položku**, můžete zvolit typ operačního systému, zadat popisný název pro přihlašovací údaje, přidat **IP adresu serveru nebo plně kvalifikovaný název domény** a kliknout na **Uložit**.
    - Pokud zvolíte možnost **Přidat více položek**, můžete najednou přidat několik záznamů zadáním **IP adresy serveru nebo plně kvalifikovaného názvu domény** s popisným názvem pro přihlašovací údaje v textovém poli. **Ověřte** přidané záznamy a klikněte na **Uložit**.
    - Pokud zvolíte možnost **importovat sdílený svazek clusteru** _(ve výchozím nastavení je vybraný)_, můžete si stáhnout soubor šablony CSV a tento soubor naplnit pomocí **IP adresy serveru nebo plně kvalifikovaného názvu domény** a popisného názvu pro přihlašovací údaje. Pak soubor naimportujete do zařízení, **ověříte** záznamy v souboru a kliknete na **Uložit**.

1. Když kliknete na Uložit, zařízení se pokusí ověřit připojení k serverům, které jste přidali, a zobrazit v tabulce **stav ověření** na každém serveru.
    - Pokud se ověření serveru nepovede, zkontrolujte chybu kliknutím na tlačítko **ověření** ve sloupci Stav v tabulce. Opravte problém a znovu ověřte.
    - Pokud chcete odebrat server, klikněte na **Odstranit**.
1. Před zahájením zjišťování můžete znovu **ověřit** připojení k serverům.
1. Kliknutím na **Spustit zjišťování zahajte**zjišťování úspěšně ověřených serverů. Po úspěšném spuštění zjišťování můžete zjistit stav zjišťování proti každému serveru v tabulce.


Spustí se zjišťování. Bude trvat přibližně 2 minuty na server, aby se metadata zjištěného serveru zobrazila v Azure Portal.

### <a name="verify-servers-in-the-portal"></a>Ověřit servery na portálu

Po zjištění můžete ověřit, že se servery zobrazují v Azure Portal.

1. Otevřete řídicí panel Azure Migrate.
2. V **Azure Migrate-servery**  >  **Azure Migrate: na stránce posouzení serveru** klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="set-up-an-assessment"></a>Nastavení posouzení

Existují dva typy hodnocení, které můžete vytvořit pomocí Azure Migrate: posouzení serveru.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě shromážděných dat o výkonu | **Doporučená velikost virtuálního počítače**: na základě dat využití procesoru a paměti.<br/><br/> **Doporučený typ disku (spravovaný disk Standard nebo Premium)**: na základě vstupně-výstupních operací a propustnosti místních disků.
**Jako místní** | Posouzení na základě místních velikostí. | **Doporučená velikost virtuálního počítače**: na základě velikosti místního serveru<br/><br> **Doporučený typ disku**: na základě nastavení typu úložiště, které jste vybrali pro posouzení.


### <a name="run-an-assessment"></a>Spuštění posouzení

Proveďte posouzení následujícím způsobem:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření hodnocení.
2. Na kartě **servery** na dlaždici **Azure Migrate: vyhodnocování serveru** klikněte na možnost **vyhodnotit**.

    ![Posouzení](./media/tutorial-assess-physical/assess.png)

2. V poli **vyhodnotit servery**zadejte název posouzení.
3. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.

    ![Vlastnosti posouzení](./media/tutorial-assess-physical/view-all.png)

3. V **Vyberte nebo vytvořte skupinu**vyberte **vytvořit novou**a zadejte název skupiny. Skupina shromažďuje jeden nebo více serverů dohromady pro posouzení.
4. V části **přidat počítače do skupiny**vyberte servery, které chcete přidat do skupiny.
5. Kliknutím na **vytvořit posouzení** vytvořte skupinu a spusťte posouzení.

    ![Vytvoření posouzení](./media/tutorial-assess-physical/assessment-create.png)

6. Po vytvoření posouzení ho zobrazte na stránce **servery**  >  **Azure Migrate: vyhodnocení vyhodnocení serveru**  >  **Assessments**.
7. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.



## <a name="review-an-assessment"></a>Kontrola posouzení

Posouzení popisuje:

- **Připravenost na Azure**: jestli jsou servery vhodné pro migraci do Azure.
- **Odhad měsíčních nákladů**: Odhadované měsíční náklady na výpočetní prostředky a úložiště pro provoz serverů v Azure.
- **Odhad měsíčních nákladů na úložiště**: Odhadované náklady na diskové úložiště po migraci.

### <a name="view-an-assessment"></a>Zobrazit posouzení

1. V případě **migrace**  >   na**serverech**klikněte na **posouzení** v **Azure Migrate: posouzení serveru**.
2. V **posouzení**klikněte na posouzení a otevřete ho.

    ![Souhrn posouzení](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Kontrola připravenosti na Azure

1. V části **připravenost na Azure**ověřte, jestli jsou servery připravené na migraci do Azure.
2. Zkontrolujte stav:
    - **Připraveno pro Azure**: Azure Migrate doporučuje velikost virtuálního počítače a odhad nákladů pro virtuální počítače ve vyhodnocování.
    - **Připraveno s podmínkami**: zobrazuje problémy a navrhovanou nápravu.
    - **Nepřipraveno pro Azure**: zobrazuje problémy a navrhovanou nápravu.
    - **Připravenost neznámá**: používá se, když Azure Migrate nedokáže vyhodnotit připravenost kvůli problémům s dostupností dat.

2. Klikněte na stav **připravenosti na Azure** . Můžete zobrazit podrobnosti o připravenosti serveru a přejít k podrobnostem a zobrazit podrobnosti o serveru, včetně výpočetních prostředků, úložiště a nastavení sítě.



### <a name="review-cost-details"></a>Podrobnosti o kontrole nákladů

Toto zobrazení ukazuje odhadované náklady na výpočetní prostředky a úložiště pro provozování virtuálních počítačů v Azure.

1. Projděte si měsíční náklady na výpočetní prostředky a úložiště. Náklady se sčítají pro všechny servery v hodnocené skupině.

    - Odhad nákladů vychází z doporučení na velikost počítače a jeho disků a vlastností.
    - Zobrazí se Odhadované měsíční náklady na výpočetní prostředky a úložiště.
    - Odhad nákladů slouží ke spuštění místních serverů jako virtuálních počítačů IaaS. Posouzení Azure Migrate serveru nebere v úvahu náklady na PaaS nebo SaaS.

2. Můžete zkontrolovat odhady měsíčních nákladů na úložiště. Toto zobrazení ukazuje agregované náklady na úložiště pro vyhodnocenou skupinu rozdělené přes různé typy úložných disků.
3. Můžete přejít k podrobnostem a zobrazit podrobnosti o konkrétních serverech.


### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Když spustíte posouzení na základě výkonu, bude posouzení k posouzení přiřazeno hodnocení spolehlivosti.

![Hodnocení spolehlivosti](./media/tutorial-assess-physical/confidence-rating.png)

- Je uděleno hodnocení od 1 hvězdičky (nejnižší) do 5 hvězdiček (nejvyšší).
- Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení týkajících se velikosti, která poskytuje posouzení.
- Hodnocení spolehlivosti je založeno na dostupnosti datových bodů potřebných k výpočtu posouzení.

Hodnocení spolehlivosti pro posouzení je následující.

**Dostupnost datového bodu** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček

[Přečtěte si další](best-practices-assessment.md#best-practices-for-confidence-ratings) informace o osvědčených postupech pro hodnocení spolehlivosti.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavení zařízení Azure Migrate
> * Vytvoření a přezkoumání posouzení

Další informace o tom, jak migrovat fyzické servery do Azure pomocí Azure Migrate: Migrace serveru, najdete v dalším kurzu v řadě.

> [!div class="nextstepaction"]
> [Migrace fyzických serverů](./tutorial-migrate-physical-virtual-machines.md)
