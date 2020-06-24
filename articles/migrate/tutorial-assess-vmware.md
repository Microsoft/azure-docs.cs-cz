---
title: Posouzení virtuálních počítačů VMware pomocí posouzení serveru Azure Migrate
description: Popisuje, jak vyhodnotit místní virtuální počítače VMware pro migraci do Azure pomocí vyhodnocení Azure Migrate serveru.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 231daff5972e9b2f115df9e6184c43a553f55b83
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771304"
---
# <a name="assess-vmware-vms-with-server-assessment"></a>Vyhodnocení virtuálních počítačů VMware (vyhodnocení serveru)

V tomto článku se dozvíte, jak pomocí nástroje [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool zhodnotit místní virtuální počítače VMware.


Tento kurz je druhý v řadě, který ukazuje, jak vyhodnotit a migrovat virtuální počítače VMware do Azure. V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavte Azure Migrate projekt.
> * Nastavte zařízení Azure Migrate, která se místně spustí pro vyhodnocení virtuálních počítačů.
> * Spusťte nepřetržité zjišťování místních virtuálních počítačů. Zařízení odesílá údaje o konfiguraci a výkonu pro zjištěné virtuální počítače do Azure.
> * Seskupit zjištěné virtuální počítače a vyhodnotit skupinu virtuálních počítačů.
> * Projděte si posouzení.

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v článcích s postupy.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/pricing/free-trial/), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

- [Dokončete první kurz](tutorial-prepare-vmware.md) v této sérii. Pokud to neuděláte, pokyny v tomto kurzu nebudou fungovat.
- Tady je seznam toho, co byste měli udělat v prvním kurzu:
    - [Připravte Azure](tutorial-prepare-vmware.md#prepare-azure) pro práci s Azure Migrate.
    - [Připravte VMware na posouzení](tutorial-prepare-vmware.md#prepare-for-assessment) pro posouzení. To zahrnuje kontrolu nastavení VMware a nastavení účtu, který Azure Migrate může použít pro přístup k vCenter Server.
    - [Ověřte](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) , co potřebujete k nasazení Azure Migrate zařízení pro vyhodnocení VMware.

## <a name="set-up-an-azure-migrate-project"></a>Nastavení Azure Migrateho projektu

Vytvořte nový Azure Migrate projekt následujícím způsobem:

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
1. V části **Služby** vyberte **Azure Migrate**.
1. V části **Přehled**v části **zjišťování, vyhodnocení a migrace serverů**vyberte možnost **zhodnotit a migrovat servery**.

   ![Tlačítko pro vyhodnocení a migraci serverů](./media/tutorial-assess-vmware/assess-migrate.png)

1. V části **Začínáme**vyberte **Přidat nástroje**.
1. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.     
1. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pole pro název a oblast projektu](./media/tutorial-assess-vmware/migrate-project.png)

1. Vyberte **Další**.
1. V **nástroji vybrat nástroj pro posouzení**vyberte **Azure Migrate: vyhodnocení serveru**  >  **Další**.

   ![Výběr nástroje pro vyhodnocování serveru](./media/tutorial-assess-vmware/assessment-tool.png)

1. V části **Vybrat nástroj pro migraci** vyberte **V tuto chvíli přeskočit přidání nástroje pro migraci** > **Další**.
1. V okně **Revize + přidat nástroje**zkontrolujte nastavení a vyberte **Přidat nástroje**.
1. Počkejte několik minut, než se projekt Azure Migrate nasadí. Budete přesměrováni na stránku projektu. Pokud se projekt nezobrazí, můžete k němu přejít z části **Servery** na řídicím panelu služby Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate

Azure Migrate: posouzení serveru používá odlehčené Azure Migrate zařízení. Zařízení provádí zjišťování virtuálních počítačů a odesílá data o výkonu a data virtuálních počítačů do Azure Migrate. Zařízení je možné nastavit několika různými způsoby.

- Nastavte na virtuálním počítači VMware pomocí stažené šablony vajíček. Toto je metoda použitá v tomto kurzu.
- Nastavte na VIRTUÁLNÍm počítači VMware nebo fyzickém počítači pomocí skriptu instalačního programu PowerShell. [Tato metoda](deploy-appliance-script.md) by se měla použít, pokud nemůžete nastavit virtuální počítač pomocí šablony pro vajíčka nebo pokud jste v Azure Government.

Po vytvoření zařízení zkontrolujete, že se může připojit k Azure Migrate: posouzení serveru, nakonfigurovat ho poprvé a zaregistrujte ho v projektu Azure Migrate.


### <a name="download-the-ova-template"></a>Stažení šablony pro VAJÍČKy

1. V **Azure Migrate cíle migrace**  >  **Servers**  >  **Azure Migrate: Server Assessment**vyberte **Vyhledat**.
1. V možnosti **zjišťovat počítače**  >  **jsou vaše počítače virtualizované?** vyberte **Ano s hypervisorem VMware vSphere**.
1. Vyberte **Stáhnout** a Stáhněte soubor šablony vajíček.

   ![Výběry pro stažení souboru vajíček](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením ověřte, zda je soubor sady vajíček zabezpečený:

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor sady vajíček:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Ověřte nejnovější verze zařízení a hodnoty hash:

    - Pro veřejný cloud Azure:
    
        **Algoritmus** | **Stáhnout** | **SHA256**
        --- | --- | ---
        VMware (10,9 GB) | [Nejnovější verze](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd

    - Pro Azure Government:
    
        **Algoritmus** | **Stáhnout** | **SHA256**
        --- | --- | ---
        VMware (63,1 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="create-the-appliance-vm"></a>Vytvoření virtuálního počítače zařízení

Naimportujte stažený soubor a vytvořte virtuální počítač:

1. V klientské konzole vSphere vyberte **soubor**  >  **nasadit šablonu OVF**.

   ![Příkaz nabídky pro nasazení šablony OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

1. V Průvodci nasazením šablony OVF > **zdroj**zadejte umístění souboru vajíček.
1. Do pole **název** a **umístění**zadejte popisný název virtuálního počítače. Vyberte objekt inventáře, do kterého bude virtuální počítač hostovat.
1. V části **hostitel nebo cluster**zadejte hostitele nebo cluster, na kterém se virtuální počítač spustí.
1. V části **Storage (úložiště**) zadejte cíl úložiště pro virtuální počítač.
1. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
1. V části **mapování sítě**určete síť, ke které se bude virtuální počítač připojovat. Síť potřebuje připojení k Internetu, aby mohla odesílat metadata Azure Migrate posouzení serveru.
1. Zkontrolujte a potvrďte nastavení a pak vyberte **Dokončit**.

## <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se virtuální počítač zařízení může připojit k adresám URL Azure pro cloudy [veřejné](migrate-appliance.md#public-cloud-urls) a [státní správy](migrate-appliance.md#government-cloud-urls) .

### <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavte zařízení poprvé.

> [!NOTE]
> Pokud zařízení nastavíte pomocí [skriptu PowerShellu](deploy-appliance-script.md) namísto stažené sady vajíček, první dva kroky v tomto postupu nejsou relevantní.

1. V klientské konzole vSphere klikněte pravým tlačítkem myši na virtuální počítač a vyberte možnost **otevřít konzolu**.
1. Zadejte jazyk, časové pásmo a heslo pro zařízení.
1. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k VIRTUÁLNÍmu počítači, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy zařízení tak, že vyberete zástupce aplikace.
1. Ve webové aplikaci > **nastavení požadavků**postupujte takto:
   - **Licence**: přijměte licenční podmínky a přečtěte si informace třetích stran.
   - **Připojení**: aplikace kontroluje, jestli má virtuální počítač přístup k Internetu. Pokud virtuální počítač používá proxy server:
     - Vyberte **nastavení proxy serveru**a zadejte adresu proxy serveru a port naslouchání ve formuláři http://ProxyIPAddress nebo http://ProxyFQDN .
     - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
     - Podporuje se jen proxy protokolu HTTP.
   - **Čas synchronizace**: čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování fungovalo správně.
   - **Nainstalovat aktualizace**: zařízení zajišťuje, že jsou nainstalované nejnovější aktualizace.
   - **Instalace VDDK**: zařízení kontroluje, jestli je nainstalovaná sada Virtual disks Development Kit (VDDK) VMware vSphere. Pokud není nainstalovaný, Stáhněte si z VMware VDDK 6,7 a Extrahujte stažený obsah zip do zadaného umístění na zařízení.

     Migrace Azure Migrate serveru používá VDDK k replikaci počítačů během migrace do Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Vyberte **Přihlásit se**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
1. Na nové kartě se přihlaste pomocí uživatelského jména a hesla Azure.
   
   Přihlášení pomocí PIN kódu se nepodporuje.
1. Po úspěšném přihlášení se vraťte k webové aplikaci.
1. Vyberte předplatné, ve kterém byl vytvořen Azure Migrate projekt, a pak vyberte projekt.
1. Zadejte název zařízení. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
1. Vyberte **Zaregistrovat**.


## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Aby bylo možné zjistit konfiguraci a údaje o výkonu virtuálních počítačů, musí se zařízení připojit k vCenter Server.

### <a name="specify-vcenter-server-details"></a>Zadání podrobností vCenter Serveru
1. V části **zadat vCenter Server podrobnosti**zadejte název (FQDN) nebo IP adresu instance vCenter Server. Můžete ponechat výchozí port nebo zadat vlastní port, na kterém vCenter Server naslouchá.
2. Do pole **uživatelské jméno** a **heslo**zadejte přihlašovací údaje účtu vCenter Server, které zařízení použije ke zjištění virtuálních počítačů v instanci vCenter Server. 

    - V [předchozím kurzu](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)byste měli mít nastavený účet s požadovanými oprávněními.
    - Chcete-li omezit obor zjišťování na konkrétní objekty VMware (vCenter Server datových center, clustery, složku clusterů, hostitele, složku hostitelů nebo jednotlivé virtuální počítače), přečtěte si pokyny v [tomto článku](set-discovery-scope.md) a omezte účet používaný v Azure Migrate.

3. Vyberte **ověřit připojení** a ujistěte se, že se zařízení může připojit k vCenter Server.
4. V části **zjistit aplikace a závislosti na virtuálních počítačích**volitelně klikněte na **Přidat přihlašovací údaje**a zadejte operační systém, pro který jsou přihlašovací údaje relevantní, a přihlašovací údaje a uživatelské jméno a heslo. Pak klikněte na **Přidat**.

    - Přihlašovací údaje můžete volitelně přidat tady, pokud jste vytvořili účet, který se má používat pro [funkci zjišťování aplikací](how-to-discover-applications.md), nebo [funkci analýzy závislostí bez agenta](how-to-create-group-machine-dependencies-agentless.md).
    - Pokud tyto funkce nepoužíváte, můžete toto nastavení přeskočit.
    - Zkontrolujte přihlašovací údaje potřebné pro [zjišťování aplikací](migrate-support-matrix-vmware.md#application-discovery-requirements)nebo pro [analýzu bez agentů](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. **Uložením a spuštěním zjišťování vyhajte**zjišťování virtuálních počítačů.

Zjišťování funguje následujícím způsobem:
- Zobrazení zjištěných metadat virtuálního počítače na portálu trvá přibližně 15 minut.
- Zjišťování nainstalovaných aplikací, rolí a funkcí nějakou dobu trvá. Doba trvání závisí na počtu zjištěných virtuálních počítačů. Pro virtuální počítače 500 trvá na portálu Azure Migrate přibližně jednu hodinu, než se inventář aplikace zobrazí.

### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po zjištění můžete ověřit, že se virtuální počítače zobrazují v Azure Portal:

1. Otevřete řídicí panel Azure Migrate.
1. V **Azure Migrate-servery**  >  **Azure Migrate: vyhodnocování serveru**vyberte ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="set-up-an-assessment"></a>Nastavení posouzení

Pomocí vyhodnocení Azure Migrate serveru můžete vytvořit dva typy posouzení:

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě shromážděných dat o výkonu | **Doporučená velikost virtuálního počítače**: na základě dat využití procesoru a paměti.<br/><br/> **Doporučený typ disku (spravovaný disk Standard nebo Premium)**: na základě vstupně-výstupních operací a propustnosti místních disků.
**Jako místní** | Posouzení na základě místních velikostí | **Doporučená velikost virtuálního počítače**: v závislosti na velikosti místního virtuálního počítače.<br/><br> **Doporučený typ disku**: na základě nastavení typu úložiště, které jste vybrali pro posouzení.

## <a name="run-an-assessment"></a>Spuštění posouzení

Proveďte posouzení následujícím způsobem:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření hodnocení.
1. Na kartě **servery** na dlaždici **Azure Migrate: posouzení serveru** vyberte možnost **vyhodnotit**.

   ![Umístění tlačítka pro vyhodnocení](./media/tutorial-assess-vmware/assess.png)

1. V poli **vyhodnotit servery**zadejte název posouzení.
1. Vyberte **Zobrazit vše**a pak zkontrolujte vlastnosti posouzení.

   ![Vlastnosti posouzení](./media/tutorial-assess-vmware/view-all.png)

1. V **Vyberte nebo vytvořte skupinu**vyberte **vytvořit novou**a zadejte název skupiny. Skupina shromažďuje jeden nebo více virtuálních počítačů dohromady pro posouzení.
1. V části **přidat počítače do skupiny**vyberte virtuální počítače, které chcete do skupiny přidat.
1. Vyberte **vytvořit vyhodnocení** a vytvořte skupinu a spusťte posouzení.

   ![Vyhodnotit servery](./media/tutorial-assess-vmware/assessment-create.png)

1. Po vytvoření posouzení ho zobrazte na stránce **servery**  >  **Azure Migrate: vyhodnocení vyhodnocení serveru**  >  **Assessments**.
1. Vyberte **vyhodnocování exportu** a stáhněte ho jako excelový soubor.

## <a name="review-an-assessment"></a>Kontrola posouzení

Posouzení popisuje:

- **Připravenost na Azure**: jestli jsou virtuální počítače vhodné pro migraci do Azure.
- **Odhad měsíčních nákladů**: Odhadované měsíční náklady na výpočetní prostředky a úložiště pro provoz virtuálních počítačů v Azure.
- **Odhad měsíčních nákladů na úložiště**: Odhadované náklady na diskové úložiště po migraci.

Zobrazení posouzení:

1. V případě **migrace na**  >  **serverech**vyberte **hodnocení** v **Azure Migrate: posouzení serveru**.
1. V **posouzení**vyberte posouzení, které chcete otevřít.

   ![Souhrn posouzení](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Kontrola připravenosti na Azure

1. V **Azure Readiness**ověřte, jestli jsou virtuální počítače připravené k migraci do Azure.
1. Zkontrolujte stav virtuálního počítače:
    - **Připraveno pro Azure**: používá se, když Azure Migrate doporučuje velikost virtuálního počítače a odhad nákladů pro virtuální počítače ve vyhodnocování.
    - **Připraveno s podmínkami**: zobrazuje problémy a navrhovanou nápravu.
    - **Nepřipraveno pro Azure**: zobrazuje problémy a navrhovanou nápravu.
    - **Připravenost neznámá**: používá se, když Azure Migrate nemůže posoudit připravenost z důvodu problémů s dostupností dat.

1. Vyberte stav **připravenosti na Azure** . Můžete si prohlédnout podrobnosti připravenosti na virtuální počítače. Můžete také přejít k podrobnostem a zobrazit podrobnosti o virtuálním počítači, včetně výpočetních prostředků, úložiště a nastavení sítě.

### <a name="review-cost-details"></a>Podrobnosti o kontrole nákladů

Souhrn posouzení zobrazuje odhadované náklady na výpočetní prostředky a úložiště pro provoz virtuálních počítačů v Azure. Náklady se sčítají pro všechny virtuální počítače v hodnocené skupině. Můžete přejít k podrobnostem a zobrazit podrobnosti o cenách pro konkrétní virtuální počítače.

> [!NOTE]
> Odhad nákladů vychází z doporučení týkajících se velikosti počítače, jeho disků a vlastností. Odhady slouží ke spouštění místních virtuálních počítačů jako virtuálních počítačů IaaS. Posouzení Azure Migrate serveru nebere v úvahu náklady na PaaS nebo SaaS.

Agregované náklady na úložiště pro vyhodnocenou skupinu jsou rozdělené do různých typů úložných disků. 

### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Azure Migrate posouzení serveru přiřadí hodnocení spolehlivosti pro posouzení na základě výkonu, od jedné hvězdičky (nejnižší) až po pět hvězdiček (nejvyšší).

![Hodnocení spolehlivosti](./media/tutorial-assess-vmware/confidence-rating.png)

Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení týkajících se velikosti posouzení. Hodnocení je založeno na dostupnosti datových bodů potřebných k výpočtu posouzení:

**Dostupnost datového bodu** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček

[Seznamte se s osvědčenými postupy](best-practices-assessment.md#best-practices-for-confidence-ratings) pro hodnocení spolehlivosti.

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte zařízení Azure Migrate. Také jste vytvořili a zkontrolovali posouzení.

Pokud se chcete dozvědět, jak migrovat virtuální počítače VMware do Azure pomocí migrace serveru Azure Migrate, pokračujte na třetí kurz v této sérii:

> [!div class="nextstepaction"]
> [Migrace virtuálních počítačů VMware](./tutorial-migrate-vmware.md)
