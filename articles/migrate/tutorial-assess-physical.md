---
title: Posouzení fyzických serverů pro migraci do Azure pomocí azure migrate server assessment
description: Popisuje, jak posoudit místní fyzické servery pro migraci do Azure pomocí Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: b36cba18bd154cd5d14e16a9f8bf85cda6bf87a8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535430"
---
# <a name="assess-physical-servers-with-azure-migrateserver-assessment"></a>Posouzení fyzických serverů pomocí Azure Migrate:Vyhodnocení serveru

Tento článek ukazuje, jak posoudit místní fyzické servery pomocí nástroje Azure Migrate:Server Assessment.

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů, které vám pomůžou objevovat, vyhodnocovat a migrovat aplikace, infrastrukturu a úlohy do Microsoft Azure. Centrum obsahuje nástroje pro migraci Azure a nabídky nezávislého dodavatele softwaru (ISV) od jiných výrobců.

Tento kurz je druhý v řadě, který ukazuje, jak posoudit a migrovat fyzické servery do Azure. V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavte projekt Migrace Azure.
> * Nastavte zařízení Azure Migrate, které běží místně k posouzení fyzických serverů.
> * Spusťte průběžné zjišťování místních fyzických serverů. Zařízení odesílá data o konfiguraci a výkonu zjištěných serverů do Azure.
> * Seskupení zjištěných serverů a vyhodnocení skupiny serverů.
> * Přezkoumat hodnocení.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář, takže můžete rychle nastavit proof-of-concept. Kurzy používají výchozí možnosti tam, kde je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny naleznete v článcích s postupy.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.


## <a name="prerequisites"></a>Požadavky

- [Dokončete](tutorial-prepare-physical.md) první kurz v této sérii. Pokud tak nechcete, pokyny v tomto kurzu nebude fungovat.
- Zde je to, co jste měli udělat v prvním tutoriálu:
    - [Nastavte oprávnění Azure](tutorial-prepare-physical.md) pro Migraci Azure.
    - [Připravte fyzické servery](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) k posouzení. Požadavky na spotřebič by měly být ověřeny. Měli byste mít také účet nastaven pro zjišťování fyzického serveru. Požadované porty by měly být k dispozici a měli byste si být vědomi adres URL potřebných pro přístup k Azure.




## <a name="set-up-an-azure-migrate-project"></a>Nastavení projektu migrace Azure

Následujícím způsobem nastavte nový projekt Azure Migrate.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. Na stránce **Přehled** v části **Zjistit, posoudit a migrovat servery** klikněte na **Posoudit a migrovat servery**.

    ![Zjišťování a posuzování serverů](./media/tutorial-assess-physical/assess-migrate.png)

4. V části **Začínáme** klikněte na **Přidat nástroje**.
5. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.  
6. V **části Podrobnosti projektu**zadejte název projektu a zeměpisnou polohu, ve které chcete projekt vytvořit. Zkontrolujte podporované zeměpisné oblasti pro [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [vládní cloudy](migrate-support-matrix.md#supported-geographies-azure-government).

    - Zeměpis projektu se používá pouze k ukládání metadat shromážděných z místních serverů.
    - Při spouštění migrace můžete vybrat jakoukoli cílovou oblast.

    ![Vytvoření projektu migrace Azure](./media/tutorial-assess-physical/migrate-project.png)


7. Klikněte na **Další**.
8. V **nástroji Pro výběr vyberte**možnost Migrace **Azure: Vyhodnocení serveru** > **Další**.

    ![Vytvoření projektu migrace Azure](./media/tutorial-assess-physical/assessment-tool.png)

9. V části **Vybrat nástroj pro migraci** vyberte **V tuto chvíli přeskočit přidání nástroje pro migraci** > **Další**.
10. V **části Revize + přidat nástroje**zkontrolujte nastavení a klepněte na přidat **nástroje**.
11. Počkejte několik minut, než se projekt Azure Migrate nasadí. Budete přesměrováni na stránku projektu. Pokud se projekt nezobrazí, můžete k němu přejít z části **Servery** na řídicím panelu služby Azure Migrate.


## <a name="set-up-the-appliance"></a>Nastavte přístroj

Migrace Azure: Vyhodnocení serveru spouští zjednodušené zařízení.

- Toto zařízení provádí zjišťování fyzického serveru a odesílá metadata serveru a data o výkonu do Azure Migrate Server Assessment.
- Chcete-li přístroj nastavit, tak:
    - Stáhněte si zip soubor s instalačním skriptem Azure Migrate z webu Azure Portal.
    - Extrahujte obsah ze souboru zip. Spusťte konzolu PowerShell s oprávněními správce.
    - Spusťte skript Prostředí PowerShell a spusťte webovou aplikaci zařízení.
    - Nakonfigurujte zařízení poprvé a zaregistrujte ho pomocí projektu Azure Migrate.
- Můžete nastavit více zařízení pro jeden projekt Migrace Azure. Ve všech zařízeních můžete zjistit libovolný počet fyzických serverů. Na jedno zařízení lze objevit maximálně 250 serverů.

### <a name="download-the-installer-script"></a>Stažení instalačního skriptu

Stáhněte si zip soubor pro zařízení.

1. V **serverech cílů** > **migrace, které** > **Azure migruje: Vyhodnocení serveru**, klepněte na tlačítko **Objevit**.
2. V **discover strojích** > **Jsou vaše počítače virtualizované?**, klikněte na **Není virtualizováno/Jiné**.
3. Chcete-li stáhnout soubor zip, klepněte na tlačítko **Stáhnout.**

    ![Stáhnout instalační program](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Ověření zabezpečení

Před nasazením zkontrolujte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz pro generování hash pro soubor zip:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad využití pro veřejný cloud:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Příklad využití pro vládní cloud:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Ověřte hodnoty hash:
 
    - Pro veřejný cloud (pro nejnovější verzi zařízení):

        **Algoritmus** | **Hodnota hash**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e6de7b9f475b6542beef114b20bfdac3c

    - Pro azure government (pro nejnovější verzi zařízení):

        **Algoritmus** | **Hodnota hash**
          --- | ---
          MD5 | f81c155fc4a1409901caea948713913f

### <a name="run-the-azure-migrate-installer-script"></a>Spuštění skriptu instalačního programu Azure Migrate

Instalační skript provádí následující akce:

- Nainstaluje agenty a webovou aplikaci pro zjišťování a hodnocení fyzického serveru.
- Nainstalujte role systému Windows, včetně služby aktivace systému Windows, služby IIS a prostředí PowerShell ISE.
- Stáhněte a nainstaluje modul s přepychovým službou IIS. [Další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) s trvalými podrobnostmi o nastavení pro Azure Migrate.
- Vytvoří pod cestou následující soubory:
    - **Soubory konfigurace**: %Programdata%\Microsoft Azure\Config
    - **Soubory protokolu**: %Programdata%\Microsoft Azure\Protokoly

Spusťte skript takto:

1. Extrahujte soubor zip do složky na serveru, která bude hostitelem zařízení.  Ujistěte se, že nespustíte skript na počítači na existujícízařízení Azure Migrate.
2. Spusťte prostředí PowerShell na výše uvedeném serveru s oprávněním správce (se zvýšenými oprávněními).
3. Změňte adresář prostředí PowerShell na složku, do které byl obsah extrahován ze staženého zipového souboru.
4. Spusťte skript s názvem **AzureMigrateInstaller.ps1** spuštěním následujícího příkazu:

    - Pro veřejný cloud:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Pro Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Skript spustí webové aplikace zařízení po úspěšném dokončení.

Pokud narazíte na nějaké problémy, můžete získat přístup k protokolům skriptů na adrese C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log pro řešení potíží.

### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [veřejné](migrate-appliance.md#public-cloud-urls) a [vládní](migrate-appliance.md#government-cloud-urls) cloudy.


### <a name="configure-the-appliance"></a>Konfigurace zařízení

Přístroj nastavte poprvé.

1. Otevřete prohlížeč na libovolném počítači, který se může připojit k zařízení, a otevřete adresu URL webové aplikace zařízení: **https:// název zařízení nebo IP*adresu*: 44368**.

   Případně můžete aplikaci otevřít z plochy kliknutím na zástupce aplikace.
2. Ve webové aplikaci > **Nastavení požadavků**postupujte takto:
    - **Licence**: Přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: Aplikace zkontroluje, zda má server přístup k internetu. Pokud server používá proxy server:
        - Klepněte na **položku Nastavení proxy serveru**a http://ProxyIPAddress zadejte adresu proxy a port pro naslouchání ve formuláři nebo http://ProxyFQDN.
        - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
        - Podporuje se jen proxy protokolu HTTP.
    - **Synchronizace času**: Čas je ověřen. Čas na zařízení by měl být synchronizován s časem internetu, aby zjišťování serveru fungovalo správně.
    - **Aktualizace instalace**: Azure Migrate Server Assessment zkontroluje, zda má zařízení nainstalovány nejnovější aktualizace.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrace zařízení pomocí Migrace Azure

1. Klepněte na tlačítko **Přihlásit se**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných míst.
2. Na nové kartě se přihlaste pomocí přihlašovacích údajů Azure.
    - Přihlaste se pomocí svého uživatelského jména a hesla.
    - Přihlášení pomocí kódu PIN není podporováno.
3. Po úspěšném přihlášení se vraťte do webové aplikace.
4. Vyberte předplatné, ve kterém byl vytvořen projekt Migrace Azure. Pak vyberte projekt.
5. Zadejte název zařízení. Název by měl být alfanumerický s 14 znaky nebo méně.
6. Klepněte na tlačítko **Registrovat**.


## <a name="start-continuous-discovery"></a>Spuštění průběžného zjišťování

Nyní se připojte ze zařízení k fyzickým serverům, které mají být objeveny, a spusťte zjišťování.

1. Kliknutím na **Přidat pověření** určete přihlašovací údaje účtu, které bude zařízení používat ke zjišťování serverů.  
2. Zadejte **operační systém**, popisný název pověření a uživatelské jméno a heslo. Pak klikněte na **Přidat**.
Můžete přidat jednu sadu pověření pro servery Windows a Linux.
4. Klepněte na tlačítko **Přidat server**a zadejte podrobnosti o serveru – adresa FQDN/IP a popisný název pověření (jedna položka na řádek) pro připojení k serveru.
3. Klikněte na **Validate** (Ověřit). Po ověření je zobrazen seznam serverů, které lze zjistit.
    - Pokud se ověření serveru nezdaří, zkontrolujte chybu umístěním na ikonu ve sloupci **Stav.** Opravte problémy a znovu ověřte.
    - Chcete-li odebrat server, vyberte možnost > **Odstranit**.
4. Po ověření klikněte na **Uložit a spusťte zjišťování** a spusťte proces zjišťování.

Tím se spustí zjišťování. Trvá přibližně 1,5 minuty na server, než se metadata zjištěného serveru zobrazí na webu Azure Portal.

### <a name="verify-servers-in-the-portal"></a>Ověření serverů na portálu

Po zjišťování můžete ověřit, že se servery zobrazují na webu Azure Portal.

1. Otevřete řídicí panel Migrace Azure.
2. V **Azure Migrate – servery, které** > **Azure migruje:** Stránka Vyhodnocení serveru, klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="set-up-an-assessment"></a>Nastavení hodnocení

Existují dva typy hodnocení, které můžete vytvořit pomocí Migrace Azure: Vyhodnocení serveru.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Hodnocení založená na shromážděných údajích o výkonnosti | **Doporučená velikost virtuálního počítače:** Na základě dat o využití procesoru a paměti.<br/><br/> **Doporučený typ disku (standardní nebo prémiový spravovaný disk):** Na základě vodítek viopa a propustnost místních disků.
**Jako místní** | Hodnocení založená na místním dimenzování. | **Doporučená velikost virtuálního počítače:** Na základě velikosti místního serveru<br/><br> **Doporučený typ disku**: Na základě nastavení typu úložiště, které vyberete pro posouzení.


### <a name="run-an-assessment"></a>Spuštění posouzení

Spusťte hodnocení takto:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření hodnocení.
2. Na kartě **Servery** klikněte na dlaždici **Azure Migrate: Server Assessment** na **Assess**.

    ![Posouzení](./media/tutorial-assess-physical/assess.png)

2. V **poli Posoudit servery**zadejte název hodnocení.
3. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.

    ![Vlastnosti posouzení](./media/tutorial-assess-physical/view-all.png)

3. V **poli Vybrat nebo vytvořit skupinu**vyberte Vytvořit **nový**a zadejte název skupiny. Skupina shromažďuje jeden nebo více serverů společně pro posouzení.
4. V **části Přidat počítače do skupiny**vyberte servery, které chcete přidat do skupiny.
5. Kliknutím na **Vytvořit hodnocení** vytvořte skupinu a spusťte hodnocení.

    ![Vytvoření posouzení](./media/tutorial-assess-physical/assessment-create.png)

6. Po vytvoření hodnocení jej zobrazte v **části Servery, které** > **Azure migruje: Hodnocení hodnocení** > **serveru**.
7. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.



## <a name="review-an-assessment"></a>Přezkoumat posouzení

Hodnocení popisuje:

- **Připravenost Azure**: Jestli jsou servery vhodné pro migraci do Azure.
- **Měsíční odhad nákladů**: Odhadované měsíční náklady na výpočetní výkon a úložiště pro spouštění serverů v Azure.
- Měsíční odhad nákladů na úložiště : Odhadované náklady na diskové úložiště po **migraci.**

### <a name="view-an-assessment"></a>Zobrazit hodnocení

1. V**oblasti serverů** **cílů** >  migrace klikněte na **Hodnocení** v **Azure Migrate: Server Assessment**.
2. V **hodnocení**, klikněte na hodnocení otevřít.

    ![Shrnutí hodnocení](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Kontrola připravenosti Azure

1. V **připravenosti Azure**ověřte, jestli jsou servery připravené k migraci do Azure.
2. Zkontrolujte stav:
    - **Připraveno pro Azure**: Azure Migrate doporučuje odhady velikosti virtuálních počítače a nákladů pro virtuální počítače v hodnocení.
    - **Připraveno s podmínkami**: Zobrazuje problémy a navrhovanou nápravu.
    - **Není připraven pro Azure**: Zobrazuje problémy a navrhované nápravy.
    - **Připravenost neznámý**: Používá se v případě, že Azure Migrate nemůže posoudit připravenost, z důvodu problémů s dostupností dat.

2. Klikněte na stav **připravenosti Azure.** Můžete zobrazit podrobnosti o připravenosti serveru a přejít k podrobnostem o serveru, včetně výpočetních, úložných a síťových nastavení.



### <a name="review-cost-details"></a>Zkontrolovat podrobnosti o nákladech

Toto zobrazení zobrazuje odhadované náklady na výpočetní prostředky a úložiště pro spouštění virtuálních počítačů v Azure.

1. Zkontrolujte měsíční náklady na výpočetní prostředky a úložiště. Náklady jsou agregovány pro všechny servery v posuzované skupině.

    - Odhady nákladů jsou založeny na doporučení velikosti pro počítač a jeho disky a vlastnosti.
    - Jsou zobrazeny odhadované měsíční náklady na výpočetní prostředky a úložiště.
    - Odhad nákladů je pro spuštění místních serverů jako virtuální chod IaaS. Azure Migrate Server Assessment nebere v úvahu PaaS nebo SaaS náklady.

2. Můžete zkontrolovat odhady měsíčních nákladů na úložiště. Toto zobrazení zobrazuje agregované náklady na úložiště pro vyměřenou skupinu rozdělenou na různé typy disků úložiště.
3. Chcete-li zobrazit podrobnosti o konkrétních serverech, můžete přejít k podrobnostem.


### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Při spuštění hodnocení na základě výkonu je hodnocení přiřazeno hodnocení.

![Hodnocení spolehlivosti](./media/tutorial-assess-physical/confidence-rating.png)

- Uděluje se hodnocení od 1 hvězdičky (nejnižší) do 5 hvězdiček (nejvyšší).
- Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení velikosti, která hodnocení poskytuje.
- Hodnocení spolehlivosti je založeno na dostupnosti datových bodů potřebných k výpočtu hodnocení.

Hodnocení spolehlivosti pro hodnocení je následující.

**Dostupnost datových bodů** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček

[Přečtěte si další informace](best-practices-assessment.md#best-practices-for-confidence-ratings) o doporučených postupech pro hodnocení spolehlivosti.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavení zařízení Azure Migrate
> * Vytvoření a přezkoumání posouzení

Pokračujte třetím kurzem v řadě a dozvíte se, jak migrovat fyzické servery do Azure pomocí Migrace Azure: Migrace serveru.

> [!div class="nextstepaction"]
> [Migrace fyzických serverů](./tutorial-migrate-physical-virtual-machines.md)
