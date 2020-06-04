---
title: Posouzení virtuálních počítačů Hyper-V pro migraci do Azure pomocí Azure Migrate | Microsoft Docs
description: Popisuje, jak vyhodnotit místní virtuální počítače Hyper-V pro migraci do Azure pomocí vyhodnocení Azure Migrate serveru.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 2c4233df6566f3187c8366188b0eb960189b43c5
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84331759"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Posouzení virtuálních počítačů Hyper-V pomocí Azure Migrate posouzení serveru

V tomto článku se dozvíte, jak vyhodnotit místní virtuální počítače Hyper-V pomocí nástroje [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool.


Tento kurz je druhý v řadě, který ukazuje, jak vyhodnotit a migrovat virtuální počítače Hyper-V do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte Azure Migrate projekt.
> * Nastavení a registrace zařízení Azure Migrate.
> * Spusťte nepřetržité zjišťování místních virtuálních počítačů.
> * Seskupit zjištěné virtuální počítače a vyhodnotit skupinu.
> * Projděte si posouzení.

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. Kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v článcích s postupy.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/pricing/free-trial/), ještě než začnete.


## <a name="prerequisites"></a>Požadavky

- [Dokončete](tutorial-prepare-hyper-v.md) první kurz v této sérii. Pokud to neuděláte, pokyny v tomto kurzu nebudou fungovat.
- Tady je seznam toho, co byste měli udělat v prvním kurzu:
    - [Připravte Azure](tutorial-prepare-hyper-v.md#prepare-azure) pro práci s Azure Migrate.
    - [Připravte hostitele Hyper-V](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) a vyhodnocování virtuálních počítačů.
    - [Ověřte](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) , co potřebujete, abyste nasadili Azure Migrate zařízení pro posouzení technologie Hyper-V.

## <a name="set-up-an-azure-migrate-project"></a>Nastavení Azure Migrateho projektu

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. Ve výsledcích hledání vyberte **Azure Migrate**.
3. Na stránce **Přehled** v části **Zjistit, posoudit a migrovat servery** klikněte na **Posoudit a migrovat servery**.

    ![Zjišťování a vyhodnocení serverů](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. V části **Začínáme** klikněte na **Přidat nástroje**.
5. Na kartě **migrovat projekt** vyberte předplatné Azure a vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

    - Oblast projektu slouží pouze k uložení metadat shromážděných z místních virtuálních počítačů.
    - Při migraci virtuálních počítačů můžete vybrat jinou cílovou oblast Azure. Všechny oblasti Azure jsou podporovány pro cíl migrace.

    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Klikněte na **Další**.
8. V **nástroji vybrat nástroj pro posouzení**vyberte **Azure Migrate: vyhodnocení serveru**  >  **Další**.

    ![Vytvoření projektu Azure Migrate](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. V části **Vybrat nástroj pro migraci** vyberte **V tuto chvíli přeskočit přidání nástroje pro migraci** > **Další**.
10. V části **Revize + přidat nástroje**zkontrolujte nastavení a klikněte na **Přidat nástroje**.
11. Počkejte několik minut, než se projekt Azure Migrate nasadí. Budete přesměrováni na stránku projektu. Pokud se projekt nezobrazí, můžete k němu přejít z části **Servery** na řídicím panelu služby Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate


Azure Migrate: posouzení serveru používá odlehčené Azure Migrate zařízení. Zařízení provádí zjišťování virtuálních počítačů a odesílá data o výkonu a data virtuálních počítačů do Azure Migrate. Zařízení je možné nastavit několika různými způsoby.

- Nastavte na virtuálním počítači s technologií Hyper-V pomocí staženého virtuálního pevného disku Hyper-V. Toto je metoda použitá v tomto kurzu.
- Nastavte na VIRTUÁLNÍm počítači Hyper-V nebo na fyzickém počítači pomocí skriptu instalačního programu PowerShell. [Tato metoda](deploy-appliance-script.md) by se měla použít, pokud nemůžete nastavit virtuální počítač pomocí virtuálního pevného disku nebo pokud jste v Azure Government.

Po vytvoření zařízení zkontrolujete, že se může připojit k Azure Migrate: posouzení serveru, nakonfigurovat ho poprvé a zaregistrujte ho v projektu Azure Migrate.

### <a name="download-the-vhd"></a>Stažení virtuálního pevného disku

Stáhněte pro zařízení šablonu VHD s příponou.

1. V Azure Migrate **cíle migrace**  >  na**servery**  >  **: vyhodnocování serveru**klikněte na **zjistit**.
2. V rozevíracích **seznamech počítačů**, ve  >  **kterých jsou počítače virtualizované?** klikněte na **Ano, s technologií Hyper-V**.
3. Kliknutím na **Stáhnout** Stáhněte soubor VHD.

    ![Stáhnout virtuální počítač](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.

2. Spusťte následující příkaz prostředí PowerShell, který vygeneruje hodnotu hash pro soubor ZIP.
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Příklady použití: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Ověřte nejnovější verze zařízení a obsahuje následující hodnoty:

    - Pro veřejný cloud Azure:

        **Scénář** | **Stáhnout** | **SHA256**
        --- | --- | ---
        Hyper-V (8,93 MB) | [Nejnovější verze](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1

    - Pro Azure Government:

        **Případě*** | **Stáhnout** | **SHA256**
        --- | --- | ---
        Hyper-V (63,1 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3


### <a name="create-the-appliance-vm"></a>Vytvoření virtuálního počítače zařízení

Naimportujte stažený soubor a vytvořte virtuální počítač.

1. Po stažení souboru VHD s příponou ZIP na hostitele Hyper-V, na kterém bude virtuální počítač zařízení umístěn, rozbalte soubor zip.
    - V extrahovaném umístění se soubor extrahuje do složky s názvem **AzureMigrateAppliance_VersionNumber**.
    - Tato složka obsahuje podsložku, která se také označuje jako **AzureMigrateAppliance_VersionNumber**.
    - Tato podsložka obsahuje tři další podsložky – **snímky**, **virtuální pevné disky**a **Virtual Machines**.

2. Spusťte Správce technologie Hyper-V. V nabídce **Akce**klikněte na **importovat virtuální počítač**.

    ![Nasazení VHD](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. V Průvodci importem virtuálního počítače > **než začnete**, klikněte na **Další**.
3. V části **najít složku**vyberte složku **Virtual Machines** . Potom klikněte na **Další**.
1. V nabídce **Vybrat virtuální počítač**klikněte na **Další**.
2. V části **zvolit typ importu**klikněte na **zkopírovat virtuální počítač (vytvořit nové jedinečné ID)**. Potom klikněte na **Další**.
3. V části **zvolit cíl**ponechte výchozí nastavení. Klikněte na **Další**.
4. V části **složky úložiště**ponechte výchozí nastavení. Klikněte na **Další**.
5. V části **zvolit síť**zadejte virtuální přepínač, který bude virtuální počítač používat. Přepínač potřebuje připojení k Internetu, aby bylo možné odesílat data do Azure. [Přečtěte si informace](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) o vytvoření virtuálního přepínače.
6. V části **Souhrn**zkontrolujte nastavení. Klikněte na **Dokončit**.
7. Ve Správci technologie Hyper-V > **Virtual Machines**spusťte virtuální počítač.


## <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se virtuální počítač zařízení může připojit k adresám URL Azure pro cloudy [veřejné](migrate-appliance.md#public-cloud-urls) a [státní správy](migrate-appliance.md#government-cloud-urls) .

### <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavte zařízení poprvé.

> [!NOTE]
> Pokud zařízení nastavíte pomocí [skriptu PowerShellu](deploy-appliance-script.md) namísto staženého virtuálního pevného disku, první dva kroky v tomto postupu se netýkají.

1. Ve Správci technologie Hyper-V > **Virtual Machines**klikněte pravým tlačítkem na virtuální počítač > **připojit**.
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k VIRTUÁLNÍmu počítači, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy zařízení kliknutím na zástupce aplikace.
1. Ve webové aplikaci > **nastavení požadavků**postupujte takto:
    - **Licence**: přijměte licenční podmínky a přečtěte si informace třetích stran.
    - **Připojení**: aplikace kontroluje, jestli má virtuální počítač přístup k Internetu. Pokud virtuální počítač používá proxy server:
      - Klikněte na **nastavení proxy serveru**a zadejte adresu proxy serveru a port naslouchání ve formuláři http://ProxyIPAddress nebo http://ProxyFQDN .
      - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
      - Podporuje se jen proxy protokolu HTTP.
    - **Časová synchronizace**: čas je ověřený. Čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování virtuálních počítačů fungovalo správně.
    - **Instalovat aktualizace**: posouzení Azure Migrate serveru kontroluje, jestli má zařízení nainstalované nejnovější aktualizace.

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Klikněte na **Přihlásit se**. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
2. Na nové kartě se přihlaste pomocí svých přihlašovacích údajů Azure.
    - Přihlaste se pomocí svého uživatelského jména a hesla.
    - Přihlášení pomocí PIN kódu se nepodporuje.
3. Po úspěšném přihlášení se vraťte k webové aplikaci.
4. Vyberte předplatné, ve kterém byl vytvořen Azure Migrate projekt. Pak vyberte projekt.
5. Zadejte název zařízení. Název by měl být alfanumerický a nesmí obsahovat více než 14 znaků.
6. Klikněte na **Zaregistrovat**.


### <a name="delegate-credentials-for-smb-vhds"></a>Pověření delegáta pro virtuální pevné disky SMB

Pokud používáte na SMB virtuální pevné disky, musíte povolit delegování přihlašovacích údajů ze zařízení na hostitele Hyper-V. To vyžaduje následující:

- Povolíte každému hostiteli, aby fungoval jako delegát pro zařízení. Pokud jste postupovali podle kurzů v předchozím kurzu, provedli jste to v předchozím kurzu, když jste připravili technologii Hyper-V pro účely posouzení a migrace. Měli byste buď nastavit CredSSP pro hostitele [ručně](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts), nebo [Spustit skript](tutorial-prepare-hyper-v.md#prepare-with-a-script) , který to dělá.
- Povolte delegování CredSSP, aby zařízení Azure Migrate mohlo fungovat jako klient a delegování přihlašovacích údajů na hostitele.

Povolit na zařízení následujícím způsobem:

#### <a name="option-1"></a>Možnost 1

Na virtuálním počítači zařízení spusťte tento příkaz. HyperVHost1/HyperVHost2 jsou příklady názvů hostitelů.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Příklad: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>2. možnost

Případně to udělejte v Editor místních zásad skupiny na zařízení:

1. V konfiguraci počítače **Zásady místního počítače**  >  **Computer Configuration**klikněte na **šablony pro správu**  >  **System**  >  **delegování přihlašovacích údajů**systému.
2. Dvakrát klikněte na **Povolit delegování nových přihlašovacích údajů**a vyberte **povoleno**.
3. V nabídce **Možnosti**klikněte na **Zobrazit**a do seznamu přidejte každého hostitele Hyper-V, který chcete zjistit, a použijte příkaz **WSMan/** jako předponu.
4. Pak v **delegování přihlašovacích údajů**dvakrát klikněte na možnost **umožňuje delegovat nové přihlašovací údaje pomocí ověřování serveru jenom s protokolem NTLM**. Znovu přidejte všechny hostitele Hyper-V, které chcete vyhledat, do seznamu s použitím nástroje **WSMan/** jako předpony.

## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Připojte se ze zařízení k hostitelům nebo clusterům Hyper-V a spusťte zjišťování virtuálních počítačů.

1. Do pole **uživatelské jméno** a **heslo**zadejte přihlašovací údaje účtu, které zařízení použije ke zjištění virtuálních počítačů. Zadejte popisný název přihlašovacích údajů a klikněte na **Uložit podrobnosti**.
2. Klikněte na **Přidat hostitele**a zadejte podrobnosti o hostiteli nebo clusteru Hyper-V.
3. Klikněte na **Validate** (Ověřit). Po ověření se zobrazí počet virtuálních počítačů, které se dají zjistit u každého hostitele nebo clusteru.
    - Pokud se ověření pro hostitele nepovede, přečtěte si chybu, když najedete myší na ikonu ve sloupci **stav** . Opravte problémy a znovu ověřte.
    - Chcete-li odebrat hostitele nebo clustery, vyberte možnost > **Odstranit**.
    - Z clusteru nelze odebrat konkrétního hostitele. Můžete odebrat jenom celý cluster.
    - Cluster můžete přidat i v případě, že v clusteru dojde k problémům s konkrétními hostiteli.
4. Po ověření klikněte na **Uložit a spusťte zjišťování a** spusťte proces zjišťování.

Spustí se zjišťování. Metadata zjištěných serverů, které se mají zobrazit v Azure Portal, trvá přibližně 1,5 minut na hostitele.

### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po dokončení zjišťování můžete ověřit, že se virtuální počítače zobrazují na portálu.

1. Otevřete řídicí panel Azure Migrate.
2. V **Azure Migrate-servery**  >  **Azure Migrate: na stránce posouzení serveru** klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="set-up-an-assessment"></a>Nastavení posouzení

Existují dva typy posouzení, které můžete spustit pomocí Azure Migrate posouzení serveru.

**Posouzení** | **Zobrazí** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě shromážděných dat o výkonu | **Doporučená velikost virtuálního počítače**: na základě dat využití procesoru a paměti.<br/><br/> **Doporučený typ disku (spravovaný disk Standard nebo Premium)**: na základě vstupně-výstupních operací a propustnosti místních disků.
**Jako místní** | Posouzení na základě místních velikostí. | **Doporučená velikost virtuálního počítače**: na základě velikosti místního virtuálního počítače<br/><br> **Doporučený typ disku**: na základě nastavení typu úložiště, které jste vybrali pro posouzení.



### <a name="run-an-assessment"></a>Spuštění posouzení

Proveďte posouzení následujícím způsobem:

1. Projděte si [osvědčené postupy](best-practices-assessment.md) pro vytváření hodnocení.
2. V **Servers**  >  **Azure Migrate servery: vyhodnocování serveru**klikněte na **vyhodnotit**.

    ![Posouzení](./media/tutorial-assess-hyper-v/assess.png)

3. V poli **vyhodnotit servery**zadejte název posouzení.
4. Kliknutím na **Zobrazit vše** zobrazíte vlastnosti posouzení.

    ![Vlastnosti posouzení](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. V **Vyberte nebo vytvořte skupinu**vyberte **vytvořit novou**a zadejte název skupiny. Skupina shromažďuje jeden nebo více virtuálních počítačů dohromady pro posouzení.
4. V části **přidat počítače do skupiny**vyberte virtuální počítače, které chcete do skupiny přidat.
5. Kliknutím na **vytvořit posouzení** vytvořte skupinu a spusťte posouzení.

    ![Vytvoření posouzení](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Po vytvoření posouzení ho zobrazte na stránce **servery**  >  **Azure Migrate: posouzení serveru**.
7. Klikněte na **Exportovat posouzení** a stáhněte ho jako excelový soubor.


## <a name="review-an-assessment"></a>Kontrola posouzení

Posouzení popisuje:

- **Připravenost na Azure**: jestli jsou virtuální počítače vhodné pro migraci do Azure.
- **Odhad měsíčních nákladů**: Odhadované měsíční náklady na výpočetní prostředky a úložiště pro provoz virtuálních počítačů v Azure.
- **Odhad měsíčních nákladů na úložiště**: Odhadované náklady na diskové úložiště po migraci.


### <a name="view-an-assessment"></a>Zobrazit posouzení

1. V Azure Migrate **cíle migrace**  >   **Servers**  >  klikněte na posuzování**serveru**a pak na **posouzení**.
2. V **posouzení**klikněte na posouzení a otevřete ho.

    ![Souhrn posouzení](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Kontrola připravenosti na Azure

1. V **Azure Readiness**ověřte, jestli jsou virtuální počítače připravené k migraci do Azure.
2. Zkontrolujte stav virtuálního počítače:
    - **Připraveno pro Azure**: Azure Migrate doporučuje velikost virtuálního počítače a odhad nákladů pro virtuální počítače ve vyhodnocování.
    - **Připraveno s podmínkami**: zobrazuje problémy a navrhovanou nápravu.
    - **Nepřipraveno pro Azure**: zobrazuje problémy a navrhovanou nápravu.
    - **Připravenost neznámá**: používá se, když Azure Migrate nedokáže vyhodnotit připravenost kvůli problémům s dostupností dat.

2. Klikněte na stav **připravenosti na Azure** . Můžete si prohlédnout podrobnosti připravenosti na virtuální počítač a přejít k podrobnostem, kde najdete podrobnosti o virtuálním počítači, včetně výpočetních prostředků, úložiště a nastavení sítě.

### <a name="review-cost-details"></a>Podrobnosti o kontrole nákladů

Toto zobrazení ukazuje odhadované náklady na výpočetní prostředky a úložiště pro provozování virtuálních počítačů v Azure.

1. Projděte si měsíční náklady na výpočetní prostředky a úložiště. Náklady se sčítají pro všechny virtuální počítače v hodnocené skupině.

    - Odhad nákladů vychází z doporučení na velikost počítače a jeho disků a vlastností.
    - Zobrazí se Odhadované měsíční náklady na výpočetní prostředky a úložiště.
    - Odhad nákladů slouží ke spuštění místních virtuálních počítačů jako virtuálních počítačů IaaS. Posouzení Azure Migrate serveru nebere v úvahu náklady na PaaS nebo SaaS.

2. Můžete zkontrolovat odhady měsíčních nákladů na úložiště. Toto zobrazení ukazuje agregované náklady na úložiště pro vyhodnocenou skupinu rozdělené přes různé typy úložných disků.
3. Můžete přejít k podrobnostem a zobrazit podrobnosti pro konkrétní virtuální počítače.


### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

Když spustíte posouzení na základě výkonu, bude posouzení k posouzení přiřazeno hodnocení spolehlivosti.

![Hodnocení spolehlivosti](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

Pokud chcete zjistit, jak migrovat virtuální počítače Hyper-V do Azure pomocí migrace Azure Migrate serveru, přejděte k třetímu kurzu v řadě.

> [!div class="nextstepaction"]
> [Migrace virtuálních počítačů Hyper-V](./tutorial-migrate-hyper-v.md)
