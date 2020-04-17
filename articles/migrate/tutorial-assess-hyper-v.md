---
title: Vyhodnoťte virtuální počítače Hyper-V pro migraci do Azure pomocí migrace Azure | Dokumenty společnosti Microsoft
description: Popisuje, jak posoudit místní virtuální počítače Hyper-V pro migraci do Azure pomocí Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: c627902268af3a91e172223c1741dd24ea21fa92
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535447"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Posouzení virtuálních počítačů Hyper-V pomocí vyhodnocení serveru Migrace Azure

Tento článek ukazuje, jak posoudit místní virtuální počítače Hyper-V pomocí nástroje [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)


Tento kurz je druhý v řadě, který ukazuje, jak posoudit a migrovat hyper-V virtuální počítače do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte projekt Migrace Azure.
> * Nastavte a zaregistrujte zařízení Azure Migrate.
> * Spusťte průběžné zjišťování místních virtuálních počítačů.
> * Skupina objevila virtuální chod a vyhodnotila skupinu.
> * Přezkoumat hodnocení.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář, takže můžete rychle nastavit proof-of-concept. Kurzy používají výchozí možnosti tam, kde je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny naleznete v článcích s postupy.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.


## <a name="prerequisites"></a>Požadavky

- [Dokončete](tutorial-prepare-hyper-v.md) první kurz v této sérii. Pokud tak nechcete, pokyny v tomto kurzu nebude fungovat.
- Zde je to, co jste měli udělat v prvním tutoriálu:
    - [Připravte Azure](tutorial-prepare-hyper-v.md#prepare-azure) na práci s Azure Migrate.
    - [Připravte si hyper-V](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) hostitele a vyhodnocení virtuálních počítačích.
    - [Ověřte,](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) co potřebujete k nasazení zařízení Azure Migrate pro vyhodnocení technologie Hyper-V.

## <a name="set-up-an-azure-migrate-project"></a>Nastavení projektu migrace Azure

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. Ve výsledcích hledání vyberte **Azure Migrate**.
3. Na stránce **Přehled** v části **Zjistit, posoudit a migrovat servery** klikněte na **Posoudit a migrovat servery**.

    ![Zjišťování a posuzování serverů](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. V části **Začínáme** klikněte na **Přidat nástroje**.
5. Na kartě **Migrace projektu** vyberte předplatné Azure a vytvořte skupinu prostředků, pokud ji nemáte.
6. V **poli Podrobnosti projektu**zadejte název projektu a oblast, ve které chcete projekt vytvořit. Zkontrolujte podporované zeměpisné oblasti pro [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [vládní cloudy](migrate-support-matrix.md#supported-geographies-azure-government).

    - Oblast projektu se používá pouze k ukládání metadat shromážděných z místních virtuálních počítačích.
    - Při migraci virtuálních počítačů můžete vybrat jinou cílovou oblast Azure. Všechny oblasti Azure jsou podporované pro cíl migrace.

    ![Vytvoření projektu migrace Azure](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Klikněte na **Další**.
8. V **nástroji Pro výběr vyberte**možnost Migrace **Azure: Vyhodnocení serveru** > **Další**.

    ![Vytvoření projektu migrace Azure](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. V části **Vybrat nástroj pro migraci** vyberte **V tuto chvíli přeskočit přidání nástroje pro migraci** > **Další**.
10. V **části Revize + přidat nástroje**zkontrolujte nastavení a klepněte na přidat **nástroje**.
11. Počkejte několik minut, než se projekt Azure Migrate nasadí. Budete přesměrováni na stránku projektu. Pokud se projekt nezobrazí, můžete k němu přejít z části **Servery** na řídicím panelu služby Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate


Azure Migrate:Vyhodnocení serveru používá zjednodušené zařízení Azure Migrate. Zařízení provádí zjišťování virtuálních počítačích a odesílá metadata virtuálního počítače a data o výkonu do Migrace Azure. Přístroj lze nastavit mnoha způsoby.

- Nastavení na virtuálním počítači Hyper-V pomocí staženého virtuálního pevného disku Hyper-V. Toto je metoda použitá v tomto kurzu.
- Nastavení na virtuálním počítači Hyper-V nebo fyzickém počítači pomocí instalačního skriptu prostředí PowerShell. [Tato metoda](deploy-appliance-script.md) by se měla použít, pokud nemůžete nastavit virtuální počítač pomocí virtuálního pevného disku, nebo pokud jste ve službě Azure Government.

Po vytvoření zařízení zkontrolujte, zda se může připojit k Azure Migrate:Server Assessment, poprvé ho nakonfigurovat a zaregistrovat v projektu Azure Migrate.

### <a name="download-the-vhd"></a>Stáhněte si virtuální pevný disk

Stáhněte si šablonu VHD pro zařízení zip.

1. V **serverech cílů** > **migrace, které** > **Azure migruje: Vyhodnocení serveru**, klepněte na tlačítko **Objevit**.
2. V **počítačích** > Discover Jsou vaše **Yes, with Hyper-V****počítače virtualizované?**
3. Chcete-li stáhnout soubor VHD, klepněte na **tlačítko Stáhnout.**

    ![Stažení Virtuálního softwaru](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Ověření zabezpečení

Před nasazením zkontrolujte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.

2. Spuštěním následujícího příkazu prostředí PowerShell vygenerujete algoritmus hash pro soubor ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Příklady použití: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  U zařízení verze 2.19.07.30 by se vygenerovaný hash měl shodovat s těmito nastaveními.

  **Algoritmus** | **Hodnota hash**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc444f475872e355f04fcb8f38606c84534c117d1609f2d124444569b31

### <a name="create-the-appliance-vm"></a>Vytvoření virtuálního virtuálního zařízení

Importujte stažený soubor a vytvořte virtuální ho.

1. Po stažení souboru Zip VHD do hostitele Hyper-V, na kterém bude umístěn virtuální počítač zařízení, extrahujte soubor zip.
    - V extrahovaném umístění se soubor rozbalí do složky s názvem **AzureMigrateAppliance_VersionNumber**.
    - Tato složka obsahuje podsložku, která se také nazývá **AzureMigrateAppliance_VersionNumber**.
    - Tato podsložka obsahuje tři další podsložky – **snímky**, **virtuální pevné disky**a **virtuální počítače**.

2. Spusťte Správce technologie Hyper-V. V **části Akce**klepněte na **položku Importovat virtuální počítač**.

    ![Nasazení virtuálního pevného disku](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. V Průvodci importem virtuálního počítače > **než začnete**, klepněte na tlačítko **Další**.
3. V **části Vyhledat složku**vyberte složku Virtuální **počítače.** Pak klikněte na **Další**.
1. V **povolte virtuální počítač**klepněte na tlačítko **Další**.
2. V **části Zvolit typ importu**klikněte na Kopírovat virtuální počítač **(vytvořit nové jedinečné ID).** Pak klikněte na **Další**.
3. V **části Zvolte cíl**ponechte výchozí nastavení. Klikněte na **Další**.
4. Ve **složkách úložiště**ponechejte výchozí nastavení. Klikněte na **Další**.
5. V **části Zvolit síť**zadejte virtuální přepínač, který bude virtuální počítač používat. Přepínač potřebuje připojení k internetu k odesílání dat do Azure. [Přečtěte si](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) o vytvoření virtuálního přepínače.
6. V **souhrnu**zkontrolujte nastavení. Klikněte na **Dokončit**.
7. Ve Správci technologie Hyper-V > **virtuální počítače**spusťte virtuální počítač.


## <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že virtuální počítač zařízení se může připojit k adresám URL Azure pro [veřejné](migrate-appliance.md#public-cloud-urls) a [vládní](migrate-appliance.md#government-cloud-urls) cloudy.

### <a name="configure-the-appliance"></a>Konfigurace zařízení

Přístroj nastavte poprvé.

> [!NOTE]
> Pokud zařízení nastavíte pomocí [skriptu prostředí PowerShell](deploy-appliance-script.md) namísto staženého virtuálního pevného disku, první dva kroky v tomto postupu nejsou relevantní.

1. Ve Správci technologie Hyper-V > **virtuálnípočítače**klikněte pravým tlačítkem myši na virtuální počítač > **Connect**.
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na libovolném počítači, který se může připojit k virtuálnímu počítači, a otevřete adresu URL webové aplikace zařízení: **https:// název zařízení nebo IP*adresu*: 44368**.

   Případně můžete aplikaci otevřít z plochy zařízení kliknutím na zástupce aplikace.
1. Ve webové aplikaci > **Nastavení požadavků**postupujte takto:
    - **Licence**: Přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: Aplikace zkontroluje, zda má virtuální uživatel přístup k internetu. Pokud virtuální virtuální server používá proxy server:
      - Klepněte na **položku Nastavení proxy serveru**a http://ProxyIPAddress zadejte adresu proxy a port pro naslouchání ve formuláři nebo http://ProxyFQDN.
      - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
      - Podporuje se jen proxy protokolu HTTP.
    - **Synchronizace času**: Čas je ověřen. Čas na zařízení by měl být synchronizován s časem internetu, aby zjišťování virtuálního počítači fungovalo správně.
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


### <a name="delegate-credentials-for-smb-vhds"></a>Delegovat pověření pro virtuální dispozice SMB

Pokud používáte virtuální disku na seminárních podinám, musíte povolit delegování přihlašovacích údajů ze zařízení do hostitelů Hyper-V. To vyžaduje následující:

- Umožňujete každému hostiteli fungovat jako delegát pro zařízení. Pokud jste postupovali podle výukových programů v pořadí, jste to udělali v předchozím kurzu, kdy jste připravovali Hyper-V pro hodnocení a migraci. Měli byste nastavit CredSSP pro hostitele [ručně](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts), nebo [spuštěním skriptu,](tutorial-prepare-hyper-v.md#prepare-with-a-script) který to dělá.
- Povolte delegování CredSSP, aby zařízení Azure Migrate mohlo fungovat jako klient a delegovat přihlašovací údaje na hostitele.

Povolte na přístroji takto:

#### <a name="option-1"></a>Možnost 1

Na virtuálním počítači zařízení spusťte tento příkaz. HyperVHost1/HyperVHost2 jsou příklady názvů hostitelů.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Příklad: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>2. možnost

Případně to udělejte v Editoru místních zásad skupiny na zařízení:

1. V části Konfigurace počítače zásad > **místního** **počítače**klepněte na položku**Delegování****systémových** > pověření šablony **pro správu** > .
2. Poklepejte na **povolit delegování nových přihlašovacích údajů**a vyberte **Povoleno**.
3. V **části Možnosti**klepněte na tlačítko **Zobrazit**a přidejte do seznamu každého hostitele Hyper-V, který chcete zjistit, jako předponu **wsman/** .
4. Potom v **delegování pověření**poklepejte na povolit **delegování nových pověření pomocí ověřování serveru pouze s NTLM**. Opět přidejte každý hostitel Hyper-V, který chcete objevit, do seznamu, s **wsman /** jako předponou.

## <a name="start-continuous-discovery"></a>Spuštění průběžného zjišťování

Připojte se ze zařízení k hostitelům nebo clusterům Technologie Hyper-V a spusťte zjišťování virtuálních zařízení.

1. V **části Uživatelské jméno** a **heslo**zadejte přihlašovací údaje účtu, které zařízení použije ke zjišťování virtuálních počítače. Zadejte popisný název pověření a klepněte na **tlačítko Uložit podrobnosti**.
2. Klikněte na **Přidat hostitele**a zadejte podrobnosti o hostiteli/clusteru Hyper-V.
3. Klikněte na **Validate** (Ověřit). Po ověření se zobrazí počet virtuálních počítačů, které lze zjistit na každém hostiteli nebo clusteru.
    - Pokud se ověření hostitele nezdaří, zkontrolujte chybu tak, že najeďte na ikonu ve sloupci **Stav.** Opravte problémy a znovu ověřte.
    - Chcete-li odebrat hostitele nebo clustery, vyberte možnost > **Odstranit**.
    - Konkrétního hostitele nelze z clusteru odebrat. Můžete odebrat pouze celý cluster.
    - Můžete přidat cluster, i když existují problémy s konkrétními hostiteli v clusteru.
4. Po ověření klikněte na **Uložit a spusťte zjišťování** a spusťte proces zjišťování.

Tím se spustí zjišťování. Trvá přibližně 1,5 minuty na hostitele, než se metadata zjištěných serverů zobrazí na webu Azure Portal.

### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po dokončení zjišťování můžete ověřit, že virtuální chody se zobrazí na portálu.

1. Otevřete řídicí panel Migrace Azure.
2. V **Azure Migrate – servery, které** > **Azure migruje:** Stránka Vyhodnocení serveru, klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="set-up-an-assessment"></a>Nastavení hodnocení

Existují dva typy hodnocení, které můžete spustit pomocí Azure Migrate Server Assessment.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Hodnocení založená na shromážděných údajích o výkonnosti | **Doporučená velikost virtuálního počítače:** Na základě dat o využití procesoru a paměti.<br/><br/> **Doporučený typ disku (standardní nebo prémiový spravovaný disk):** Na základě vodítek viopa a propustnost místních disků.
**Jako místní** | Hodnocení založená na místním dimenzování. | **Doporučená velikost virtuálního počítače:** Na základě místní velikosti virtuálního počítače<br/><br> **Doporučený typ disku**: Na základě nastavení typu úložiště, které vyberete pro posouzení.



### <a name="run-an-assessment"></a>Spuštění posouzení

Spusťte hodnocení takto:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření hodnocení.
2. V **serverech, které** > **Azure migruje: Hodnocení serveru**, klikněte na **Posoudit**.

    ![Posouzení](./media/tutorial-assess-hyper-v/assess.png)

3. V **poli Assess Servers**zadejte název hodnocení.
4. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.

    ![Vlastnosti posouzení](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. V **poli Vybrat nebo vytvořit skupinu**vyberte Vytvořit **nový**a zadejte název skupiny. Skupina shromažďuje jeden nebo více virtuálních virtuálních byl v usa z hodnocení.
4. V **části Přidat počítače do skupiny**vyberte virtuální počítače, které chcete přidat do skupiny.
5. Kliknutím na **Vytvořit hodnocení** vytvořte skupinu a spusťte hodnocení.

    ![Vytvoření posouzení](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Po vytvoření vyhodnocení jej zobrazte v **části Servery, které** > **Azure migrate: Server Assessment**.
7. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.


## <a name="review-an-assessment"></a>Přezkoumat posouzení

Hodnocení popisuje:

- **Připravenost Azure:** Jestli jsou virtuální počítače vhodné pro migraci do Azure.
- **Měsíční odhad nákladů**: Odhadované měsíční náklady na výpočetní výkon a úložiště pro spouštění virtuálních počítačů v Azure.
- Měsíční odhad nákladů na úložiště : Odhadované náklady na diskové úložiště po **migraci.**


### <a name="view-an-assessment"></a>Zobrazit hodnocení

1. V **cílech** >  migrace**Servery, které** > **Azure migruje: Vyhodnocení serveru**, klikněte na **Hodnocení**.
2. V **hodnocení**, klikněte na hodnocení otevřít.

    ![Shrnutí hodnocení](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Kontrola připravenosti Azure

1. V **připravenosti Azure**ověřte, jestli jsou virtuální počítače připravené k migraci do Azure.
2. Zkontrolujte stav virtuálního soudu:
    - **Připraveno pro Azure**: Azure Migrate doporučuje odhady velikosti virtuálních počítače a nákladů pro virtuální počítače v hodnocení.
    - **Připraveno s podmínkami**: Zobrazuje problémy a navrhovanou nápravu.
    - **Není připraven pro Azure**: Zobrazuje problémy a navrhované nápravy.
    - **Připravenost neznámý**: Používá se v případě, že Azure Migrate nemůže posoudit připravenost, z důvodu problémů s dostupností dat.

2. Klikněte na stav **připravenosti Azure.** Můžete zobrazit podrobnosti o připravenosti virtuálního počítače a přejít k podrobnostem, abyste viděli podrobnosti o virtuálním počítači, včetně výpočetních, úložných a síťových nastavení.

### <a name="review-cost-details"></a>Zkontrolovat podrobnosti o nákladech

Toto zobrazení zobrazuje odhadované náklady na výpočetní prostředky a úložiště pro spouštění virtuálních počítačů v Azure.

1. Zkontrolujte měsíční náklady na výpočetní prostředky a úložiště. Náklady se agregují pro všechny virtuální společnosti v posuzované skupině.

    - Odhady nákladů jsou založeny na doporučení velikosti pro počítač a jeho disky a vlastnosti.
    - Jsou zobrazeny odhadované měsíční náklady na výpočetní prostředky a úložiště.
    - Odhad nákladů je pro spuštění místních virtuálních počítačů jako virtuálních počítačích IaaS. Azure Migrate Server Assessment nebere v úvahu PaaS nebo SaaS náklady.

2. Můžete zkontrolovat odhady měsíčních nákladů na úložiště. Toto zobrazení zobrazuje agregované náklady na úložiště pro vyměřenou skupinu rozdělenou na různé typy disků úložiště.
3. Podrobnosti zobrazíte podrobnostmi o konkrétních virtuálních dětech.


### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Při spuštění hodnocení na základě výkonu je hodnocení přiřazeno hodnocení.

![Hodnocení spolehlivosti](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

Pokračujte třetím kurzem v řadě a dozvíte se, jak migrovat virtuální počítače Hyper-V do Azure pomocí migrace serveru Azure.

> [!div class="nextstepaction"]
> [Migrace virtuálních počítačů Hyper-V](./tutorial-migrate-hyper-v.md)
