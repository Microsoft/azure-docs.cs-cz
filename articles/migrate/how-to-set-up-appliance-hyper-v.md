---
title: Nastavení zařízení Azure Migrate pro Hyper-V
description: Naučte se, jak nastavit zařízení Azure Migrate pro vyhodnocení a migraci serverů v Hyper-V.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 85ab07e0b81bf258a56956f5f0f7e80bad6a32fe
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775217"
---
# <a name="set-up-an-appliance-for-servers-on-hyper-v"></a>Nastavení zařízení pro servery na Hyper-V

Podle tohoto článku nastavte zařízení Azure Migrate pro zjišťování a hodnocení serverů v Hyper-V pomocí nástroje [Azure Migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool.

[Zařízení Azure Migrate](migrate-appliance.md) je odlehčené zařízení, které používá Azure Migrate: zjišťování a hodnocení/migrace za účelem zjišťování místních serverů na technologii Hyper-V a posílání metadat serveru a dat o výkonu do Azure.

Zařízení můžete nasadit pomocí několika metod:

- Nastavení na serveru technologie Hyper-V pomocí staženého virtuálního pevného disku. Tato metoda je popsaná v tomto článku.
- Nastavení na serveru na technologii Hyper-V nebo na fyzickém serveru pomocí skriptu PowerShellového instalačního programu. [Tato metoda](deploy-appliance-script.md) by se měla použít, pokud nemůžete nastavit server pomocí VHD, nebo pokud jste v Azure Government.

Po vytvoření zařízení zkontrolujete, že se může připojit k Azure Migrate: zjišťování a posouzení, nakonfigurovat ho poprvé a zaregistrujte ho v projektu Azure Migrate.

## <a name="appliance-deployment-vhd"></a>Nasazení zařízení (VHD)

Nastavení zařízení pomocí šablony VHD:

- Zadejte název zařízení a vygenerujte Azure Migrate klíč projektu na portálu.
- Z Azure Portal Stáhněte komprimovaný VHD Hyper-V.
- Vytvořte zařízení a ověřte, jestli se může připojit k Azure Migrate: zjišťování a posouzení.
- Nakonfigurujte zařízení poprvé a zaregistrujte ho pomocí Azure Migrate projektu pomocí klíče Azure Migrate projektu.

### <a name="generate-the-azure-migrate-project-key"></a>Vygenerovat klíč projektu Azure Migrate

1. V **cíli migrace** se  >  **systémy Windows, Linux a SQL Server**  >  **Azure Migrate: zjišťování a posouzení** vyberte **Vyhledat**.
2. V rozevíracích **seznamech servery**  >  **jsou vaše servery virtualizované?** vyberte **Ano, s technologií Hyper-V**.
3. V části **1: vygenerovat klíč projektu** zadejte název zařízení Azure Migrate, které nastavíte pro zjišťování serverů v technologii Hyper-V. název by měl být alfanumerický s 14 znaky nebo méně.
1. Kliknutím na **vygenerovat klíč** spustíte vytváření požadovaných prostředků Azure. Nezavírejte stránku zjišťovací servery během vytváření prostředků.
1. Po úspěšném vytvoření prostředků Azure se vygeneruje **klíč projektu Azure Migrate** .
1. Zkopírujte klíč, protože ho budete potřebovat k dokončení registrace zařízení během jeho konfigurace.

### <a name="download-the-vhd"></a>Stažení virtuálního pevného disku

V **2: Stáhněte zařízení Azure Migrate** vyberte. Soubor VHD a klikněte na **Stáhnout**.

   ![Výběry pro zjišťování počítačů](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Výběry pro vygenerování klíče](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na serveru, na který jste stáhli soubor, otevřete okno příkazového řádku správce.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro virtuální pevný disk.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```





## <a name="create-the-appliance"></a>Vytvoření zařízení

Naimportujte stažený soubor a vytvořte zařízení.

1. Extrahujte soubor VHD s příponou ZIP do složky na hostiteli Hyper-V, která bude hostovat zařízení. Jsou extrahovány tři složky.
2. Spusťte Správce technologie Hyper-V. V nabídce **Akce** klikněte na **importovat virtuální počítač**.

    ![Nasazení VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. V Průvodci importem virtuálního počítače > **než začnete**, klikněte na **Další**.
3. V části **Vyhledat složku** zadejte složku obsahující extrahovaný virtuální pevný disk. Potom klikněte na **Další**.
1. V nabídce **Vybrat virtuální počítač** klikněte na **Další**.
2. V části **zvolit typ importu** klikněte na **zkopírovat virtuální počítač (vytvořit nové jedinečné ID)**. Potom klikněte na **Další**.
3. V části **zvolit cíl** ponechte výchozí nastavení. Klikněte na **Next** (Další).
4. V části **složky úložiště** ponechte výchozí nastavení. Klikněte na **Next** (Další).
5. V části **zvolit síť** zadejte virtuální přepínač, který bude server používat. Přepínač potřebuje připojení k Internetu, aby bylo možné odesílat data do Azure.
6. V části **Souhrn** zkontrolujte nastavení. Klikněte na **Dokončit**.
7. Ve Správci technologie Hyper-V > **Virtual Machines** spusťte virtuální počítač.


### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro cloudy [veřejné](migrate-appliance.md#public-cloud-urls) a [státní správy](migrate-appliance.md#government-cloud-urls) .

### <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavte zařízení poprvé.

> [!NOTE]
> Pokud zařízení nastavíte pomocí [skriptu PowerShellu](deploy-appliance-script.md) namísto staženého virtuálního pevného disku, první dva kroky v tomto postupu se netýkají.

1. Ve Správci technologie Hyper-V > **Virtual Machines** klikněte pravým tlačítkem na server > **připojit**.
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč v jakémkoli systému, který se může připojit k zařízení, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy zařízení kliknutím na zástupce aplikace.
1. Přijměte **licenční podmínky** a přečtěte si informace třetích stran.
1. Ve webové aplikaci > **nastavení požadavků** postupujte takto:
    - **Připojení**: aplikace kontroluje, jestli má server přístup k Internetu. Pokud server používá proxy server:
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
4. Pokud má uživatelský účet Azure použitý k protokolování správná [oprávnění](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) k prostředkům Azure vytvořeným během generování klíče, zahájí se registrace zařízení.
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

Připojte se ze zařízení k hostitelům nebo clusterům Hyper-V a spusťte zjišťování.

1. V **kroku 1: zadání přihlašovacích údajů hostitele Hyper-v** klikněte na **Přidat přihlašovací údaje** , abyste zadali popisný název pro přihlašovací údaje. přidejte **uživatelské jméno** a **heslo** pro hostitele nebo cluster Hyper-V, které zařízení použije k zjišťování serverů. Klikněte na **Uložit**.
1. Pokud chcete přidat více přihlašovacích údajů najednou, klikněte na **Přidat další** a uložte a přidejte další přihlašovací údaje. Pro zjišťování serverů technologie Hyper-V je podporováno více přihlašovacích údajů.
1. V **kroku 2: zadání podrobností o hostiteli nebo clusteru technologie Hyper-v** klikněte na **Přidat zdroj zjišťování** a zadejte hostitele Hyper-v nebo **IP adresu clusteru nebo plně kvalifikovaný název domény** a popisný název přihlašovacích údajů pro připojení k hostiteli nebo clusteru.
1. Můžete buď **přidat jednu položku** najednou, nebo **Přidat více položek** do jednoho přechodu. K dispozici je také možnost poskytnout podrobnosti o hostiteli nebo clusteru Hyper-V prostřednictvím **importu CSV**.

    ![Výběry pro přidání zdroje zjišťování](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Pokud zvolíte možnost **přidat jednu položku**, je nutné zadat popisný název pro přihlašovací údaje a hostitele Hyper-V/ **IP adresu clusteru nebo plně kvalifikovaný název domény** a kliknout na **Uložit**.
    - Pokud zvolíte možnost **Přidat více položek** _(ve výchozím nastavení je vybrána možnost)_, můžete přidat více záznamů najednou zadáním názvu hostitele nebo clusteru Hyper-V nebo **plně kvalifikovaného názvu domény** s popisným názvem pro přihlašovací údaje v textovém poli. Ověřte * přidané záznamy a klikněte na **Uložit**.
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
2. V **Azure Migrate – Windows, Linux a SQL Server**  >  **Azure Migrate: stránka zjišťování a posouzení** klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si [vyhodnocení technologie Hyper-V](tutorial-assess-hyper-v.md) pomocí Azure Migrate: zjišťování a posouzení.