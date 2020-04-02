---
title: Posouzení virtuálních počítačů VMware pro migraci do Azure
description: Popisuje, jak posoudit místní virtuální počítače VMware pro migraci do Azure pomocí Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 03/23/2019
ms.openlocfilehash: 944b7c12a353a29a172576974261eece63ebf668
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548750"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>Posouzení virtuálních počítačů VMware pomocí azure migrate server assessment

Tento článek ukazuje, jak posoudit místní virtuální počítače VMware pomocí nástroje [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)


Tento kurz je druhý v řadě, který ukazuje, jak posoudit a migrovat virtuální počítače VMware do Azure. V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavte projekt Migrace Azure.
> * Nastavte zařízení Azure Migrate, které běží místně k vyhodnocení virtuálních počítačů.
> * Spusťte průběžné zjišťování místních virtuálních počítačů. Zařízení odesílá data o konfiguraci a výkonu zjištěných virtuálních počítačů do Azure.
> * Skupina objevila virtuální chod a vyhodnotila skupinu virtuálních mích.
> * Přezkoumat hodnocení.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář, takže můžete rychle nastavit proof-of-concept. Kurzy používají výchozí možnosti tam, kde je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v článcích s postupy.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.

## <a name="prerequisites"></a>Požadavky

- [Dokončete první kurz](tutorial-prepare-vmware.md) v této sérii. Pokud tak nechcete, pokyny v tomto kurzu nebude fungovat.
- Zde je to, co jste měli udělat v prvním tutoriálu:
    - [Připravte Azure](tutorial-prepare-vmware.md#prepare-azure) na práci s Azure Migrate.
    - [Připravte společnost VMware k posouzení.](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) To zahrnuje kontrolu nastavení vmware, nastavení účtu, který Azure Migrate můžete použít pro přístup k serveru vCenter.
    - [Ověřte,](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) co potřebujete k nasazení zařízení Azure Migrate pro hodnocení VMware.

## <a name="set-up-an-azure-migrate-project"></a>Nastavení projektu migrace Azure

Nastavte nový projekt Migrace Azure takto:

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
1. V části **Služby** vyberte **Azure Migrate**.
1. V **části Přehled**vyberte v části Zjišťování **vyhodnocovat a migrovat servery**vyberte **Posuzovat a migrovat servery**.

   ![Tlačítko pro posouzení a migraci serverů](./media/tutorial-assess-vmware/assess-migrate.png)

1. V **možnosti Začínáme**vyberte **Přidat nástroje**.
1. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.     
1. V **části Podrobnosti projektu**zadejte název projektu a zeměpisnou polohu, ve které chcete projekt vytvořit. Podporovány jsou Asie, Evropa, Velká Británie a Spojené státy.

   Zeměpisná oblast projektu slouží pouze k ukládání metadat shromážděných z místních virtuálních počítačů. Při spouštění migrace můžete vybrat jakoukoli cílovou oblast.

   ![Pole pro název projektu a oblast](./media/tutorial-assess-vmware/migrate-project.png)

1. Vyberte **další**.
1. V **nástroji Pro výběr vyberte**možnost Migrace **Azure: Vyhodnocení serveru** > **Další**.

   ![Výběr pro nástroj hodnocení serveru](./media/tutorial-assess-vmware/assessment-tool.png)

1. V části **Vybrat nástroj pro migraci** vyberte **V tuto chvíli přeskočit přidání nástroje pro migraci** > **Další**.
1. V **části Revize + přidání nástrojů**zkontrolujte nastavení a vyberte Přidat **nástroje**.
1. Počkejte několik minut, než se projekt Azure Migrate nasadí. Budete přesměrováni na stránku projektu. Pokud se projekt nezobrazí, můžete k němu přejít z části **Servery** na řídicím panelu služby Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate

Azure Migrate:Vyhodnocení serveru používá zjednodušené zařízení Azure Migrate. Zařízení provádí zjišťování virtuálních počítačích a odesílá metadata virtuálního počítače a data o výkonu do Migrace Azure.
- Zařízení lze nastavit na virtuálním počítači VMware pomocí stažené šablony OVA. Případně můžete nastavit zařízení na virtuálním počítači nebo fyzickém počítači pomocí instalačního skriptu prostředí PowerShell.
- Tento kurz používá šablonu OVA. Pokud chcete nastavit zařízení pomocí skriptu, přečtěte si [tento článek.](deploy-appliance-script.md)

Po vytvoření zařízení zkontrolujte, zda se může připojit k Azure Migrate:Server Assessment, poprvé ho nakonfigurovat a zaregistrovat v projektu Azure Migrate.



### <a name="download-the-ova-template"></a>Stažení šablony OVA

1. V **serverech cílů** > **migrace, které** > **Azure migruje: Vyhodnocení serveru**, vyberte **Zjistit**.
1. V **Discover machines** > **Jsou vaše počítače virtualizované?**, vyberte **Ano, s hypervisorem VMWare vSphere**.
1. Chcete-li stáhnout soubor šablony OVA, vyberte **možnost Stáhnout.**

   ![Výběry pro stažení souboru OVA](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Ověření zabezpečení

Před nasazením souboru OVA zkontrolujte, zda je zabezpečený:

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
1. Spusťte následující příkaz pro generování hash pro soubor OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

Pro verzi 2.19.07.30 by se vygenerovaný hodnota hash měl shodovat s těmito hodnotami:

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c

### <a name="create-the-appliance-vm"></a>Vytvoření virtuálního virtuálního zařízení

Importujte stažený soubor a vytvořte virtuální hospo-

1. V konzole klienta vSphere vyberte **Soubor** > **nasazení šablony OVF**.

   ![Příkaz nabídky pro nasazení šablony OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

1. V Průvodci nasazením šablony OVF > **zdroj**zadejte umístění souboru OVA.
1. V **části Název** a **umístění**zadejte popisný název virtuálního soudu. Vyberte objekt zásob, ve kterém bude virtuální ms hostován.
1. V **hostiteli/clusteru**určete hostitele nebo cluster, na kterém bude virtuální počítač spuštěn.
1. V **úložišti**zadejte cíl úložiště pro virtuální ho.
1. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
1. V **mapování sítě**určete síť, ke které se bude virtuální montovna připojovat. Síť potřebuje připojení k internetu k odesílání metadat do Azure Migrate Server Assessment.
1. Zkontrolujte a potvrďte nastavení a pak vyberte **Dokončit**.

### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že virtuální počítač zařízení se může připojit k [adresám URL Azure](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>Konfigurace zařízení

Přístroj nastavte poprvé.

> [!NOTE]
> Pokud nastavíte zařízení pomocí [skriptu prostředí PowerShell](deploy-appliance-script.md) namísto stažené ova, první dva kroky v tomto postupu nejsou relevantní.

1. V konzole klienta vSphere klepněte pravým tlačítkem myši na virtuální ms a pak vyberte **otevřít konzolu**.
1. Zadejte jazyk, časové pásmo a heslo pro zařízení.
1. Otevřete prohlížeč na libovolném počítači, který se může připojit k virtuálnímu počítači, a otevřete adresu URL webové aplikace zařízení: **https:// název zařízení nebo IP*adresu*: 44368**.

   Případně můžete aplikaci otevřít z plochy zařízení výběrem zástupce aplikace.
1. Ve webové aplikaci > **Nastavení požadavků**postupujte takto:
   - **Licence**: Přijměte licenční podmínky a přečtěte si informace třetích stran.
   - **Připojení**: Aplikace zkontroluje, zda má virtuální uživatel přístup k internetu. Pokud virtuální virtuální server používá proxy server:
     - Vyberte **nastavení proxy serveru**a zadejte adresu http://ProxyIPAddress http://ProxyFQDNproxy a port naslouchání ve formuláři nebo .
     - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
     - Všimněte si, že je podporován pouze proxy server HTTP.
   - **Synchronizace času**: Čas na zařízení by měl být synchronizován s časem na internetu, aby zjišťování fungovalo správně.
   - **Aktualizace instalace**: Zařízení zajišťuje instalaci nejnovějších aktualizací.
   - **Instalace vDDK**: Zařízení zkontroluje, zda je nainstalována sada VMWare vSphere Virtual Disk Development Kit (VDDK). Pokud není nainstalován, stáhněte si vddk 6.7 ze společnosti VMware a extrahujte stažený obsah zipu do určeného umístění na zařízení.

     Migrace serveru Azure používá VDDK k replikaci počítačů během migrace do Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Registrace zařízení pomocí Migrace Azure

1. Vyberte **možnost Přihlásit se**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných míst.
1. Na nové kartě se přihlaste pomocí uživatelského jména a hesla Azure.
   
   Přihlášení pomocí kódu PIN není podporováno.
1. Po úspěšném přihlášení se vraťte do webové aplikace.
1. Vyberte předplatné, ve kterém byl vytvořen projekt Migrace Azure a vyberte projekt.
1. Zadejte název zařízení. Název by měl být alfanumerický s 14 znaky nebo méně.
1. Vyberte **Zaregistrovat**.


## <a name="start-continuous-discovery"></a>Spuštění průběžného zjišťování

Zařízení se musí připojit k serveru vCenter, aby zjistilo údaje o konfiguraci a výkonu virtuálních počítačů.

### <a name="specify-vcenter-server-details"></a>Zadání podrobností vCenter Serveru
1. V **části Zadejte podrobnosti o serveru vCenter**zadejte název (FQDN) nebo ADRESU IP instance serveru vCenter. Můžete ponechat výchozí port nebo zadat vlastní port, na kterém server vCenter naslouchá.
2. V **poli Uživatelské jméno** a **Heslo**zadejte přihlašovací údaje účtu serveru vCenter, které bude zařízení používat ke zjišťování virtuálních počítačů v instanci serveru vCenter. 

    - Měli jste nastavit účet s požadovanými oprávněními v [předchozím kurzu](tutorial-prepare-vmware.md#set-up-an-account-for-assessment).
    - Pokud chcete obor zjišťování na konkrétní objekty VMware (vCenter Server datových center, clustery, složky clusterů, hostitelé, složka hostitelů nebo jednotlivé virtuální počítače.), přečtěte si pokyny v [tomto článku](set-discovery-scope.md) omezit účet používaný Azure Migrate.

3. Vyberte **Ověřit připojení,** abyste se ujistili, že se zařízení může připojit k serveru vCenter.
4. V **části Zjišťování aplikací a závislostí na virtuálních počítačích**volitelně klikněte na Přidat **pověření**a určete operační systém, pro který jsou pověření relevantní, a uživatelské jméno a heslo pověření. Potom klepněte na tlačítko **Přidat**..

    - Volitelně zde přidáte pověření, pokud jste vytvořili účet, který chcete použít pro [funkci zjišťování aplikace](how-to-discover-applications.md)nebo funkci [analýzy závislostí bez agenta](how-to-create-group-machine-dependencies-agentless.md).
    - Pokud tyto funkce nepoužíváte, můžete toto nastavení přeskočit.
    - Zkontrolujte přihlašovací údaje potřebné pro [zjišťování aplikací](migrate-support-matrix-vmware.md#application-discovery)nebo pro [analýzu bez agentů](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements).

5. **Uložit a spustit zjišťování**, chcete-li zahájit zjišťování virtuálních ms.

Zjišťování funguje takto:
- Zjistíte metadata virtuálního aplikace na portálu přibližně 15 minut.
- Zjišťování nainstalovaných aplikací, rolí a funkcí nějakou dobu trvá. Doba trvání závisí na počtu zjištěných virtuálních počítačů. U 500 virtuálních počítačů trvá přibližně jednu hodinu, než se inventář aplikací zobrazí na portálu Migrace Azure.

### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po zjišťování můžete ověřit, že se virtuální počítače zobrazují na webu Azure Portal:

1. Otevřete řídicí panel Migrace Azure.
1. V **Azure Migrate – servery, které** > **Azure migrate: Vyhodnocení serveru**, vyberte ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="set-up-an-assessment"></a>Nastavení hodnocení

Pomocí Azure Migrate Server Assessment můžete vytvořit dva typy hodnocení:

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Hodnocení založená na shromážděných údajích o výkonnosti | **Doporučená velikost virtuálního počítače:** Na základě dat o využití procesoru a paměti.<br/><br/> **Doporučený typ disku (standardní nebo prémiový spravovaný disk):** Na základě vodítek viopa a propustnost místních disků.
**Jako místní** | Hodnocení založená na místním dimenzování | **Doporučená velikost virtuálního počítače:** Na základě místní velikosti virtuálního počítače.<br/><br> **Doporučený typ disku**: Na základě nastavení typu úložiště, které jste vybrali pro posouzení.

## <a name="run-an-assessment"></a>Spuštění posouzení

Spusťte hodnocení takto:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření hodnocení.
1. Na kartě **Servery** vyberte na dlaždici **Azure Migrate: Server Assessment** možnost **Assess**.

   ![Umístění tlačítka Posoudit](./media/tutorial-assess-vmware/assess.png)

1. V **poli Posoudit servery**zadejte název hodnocení.
1. Vyberte **Zobrazit vše**a zkontrolujte vlastnosti hodnocení.

   ![Vlastnosti posouzení](./media/tutorial-assess-vmware/view-all.png)

1. V **poli Vybrat nebo vytvořit skupinu**vyberte Vytvořit **nový**a zadejte název skupiny. Skupina shromažďuje jeden nebo více virtuálních virtuálních byl v usa z hodnocení.
1. V **části Přidat počítače do skupiny**vyberte virtuální počítače, které chcete přidat do skupiny.
1. Chcete-li vytvořit skupinu a spustit hodnocení, vyberte **možnost Vytvořit hodnocení.**

   ![Posoudit servery](./media/tutorial-assess-vmware/assessment-create.png)

1. Po vytvoření hodnocení jej zobrazte v **části Servery, které** > **Azure migruje: Hodnocení hodnocení** > **serveru**.
1. Vyberte **Vyhodnocení exportu,** chcete-li jej stáhnout jako soubor aplikace Excel.

## <a name="review-an-assessment"></a>Přezkoumat posouzení

Hodnocení popisuje:

- **Připravenost Azure:** Jestli jsou virtuální počítače vhodné pro migraci do Azure.
- **Měsíční odhad nákladů**: Odhadované měsíční náklady na výpočetní výkon a úložiště pro spouštění virtuálních počítačů v Azure.
- Měsíční odhad nákladů na úložiště : Odhadované náklady na diskové úložiště po **migraci.**

Zobrazení hodnocení:

1. V**oblasti Serverů** **cílů** > migrace vyberte **Hodnocení** v **Azure Migrate: Server Assessment**.
1. V **posudku**vyberte hodnocení, které ho otevřete.

   ![Shrnutí hodnocení](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Kontrola připravenosti Azure

1. V **připravenosti Azure**ověřte, jestli jsou virtuální počítače připravené k migraci do Azure.
1. Zkontrolujte stav virtuálního soudu:
    - **Připraveno pro Azure**: Používá se při migraci Azure doporučuje odhady velikosti virtuálních počítače a nákladů pro virtuální počítače v hodnocení.
    - **Připraveno s podmínkami**: Zobrazuje problémy a navrhovanou nápravu.
    - **Není připraven pro Azure**: Zobrazuje problémy a navrhované nápravy.
    - **Připravenost neznámý**: Používá se v případě, že Azure Migrate nelze posoudit připravenost z důvodu problémů s dostupností dat.

1. Vyberte stav **připravenosti Azure.** Můžete zobrazit podrobnosti připravenosti virtuálního účtu. Můžete také přejít k podrobnostem a zobrazit podrobnosti o virtuálních počítačích, včetně výpočetních, úložných a síťových nastavení.

### <a name="review-cost-details"></a>Zkontrolovat podrobnosti o nákladech

Souhrn hodnocení zobrazuje odhadované náklady na výpočetní prostředky a úložiště pro spouštění virtuálních počítačů v Azure. Náklady se agregují pro všechny virtuální společnosti v posuzované skupině. Můžete přejít k podrobnostem a zobrazit podrobnosti o nákladech pro konkrétní virtuální hod.

> [!NOTE]
> Odhady nákladů jsou založeny na doporučení velikosti počítače, jeho disků a jeho vlastnostech. Odhady jsou pro spuštění místních virtuálních počítačů jako virtuálních počítačích IaaS. Azure Migrate Server Assessment nebere v úvahu PaaS nebo SaaS náklady.

Agregované náklady na úložiště pro vyměřenou skupinu jsou rozděleny na různé typy disků úložiště. 

### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Azure Migrate Server Assessment přiřazuje hodnocení spolehlivosti hodnocení založené na výkonu, od 1 hvězdičky (nejnižší) do 5 hvězdiček (nejvyšší).

![Hodnocení spolehlivosti](./media/tutorial-assess-vmware/confidence-rating.png)

Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení pro velikost hodnocení. Hodnocení je založeno na dostupnosti datových bodů potřebných k výpočtu hodnocení:

**Dostupnost datových bodů** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček

[Přečtěte si o doporučených postupech](best-practices-assessment.md#best-practices-for-confidence-ratings) pro hodnocení spolehlivosti.

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte zařízení Migrace Azure. Také jste vytvořili a zkontrolovali hodnocení.

Pokud se chcete dozvědět, jak migrovat virtuální počítače VMware do Azure pomocí migrace migrace serveru Azure, pokračujte třetím kurzem v řadě:

> [!div class="nextstepaction"]
> [Migrace virtuálních počítačů VMware](./tutorial-migrate-vmware.md)
