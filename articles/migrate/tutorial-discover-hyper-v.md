---
title: Zjišťování virtuálních počítačů Hyper-V pomocí serveru Azure Migrate Assessment
description: Naučte se zjišťovat místní virtuální počítače Hyper-V pomocí nástroje Azure Migrate Server Assessment Tool.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 1b860c739ab9ed9737f9f946cb13c731fa4722db
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753055"
---
# <a name="tutorial-discover-hyper-v-vms-with-server-assessment"></a>Kurz: zjišťování virtuálních počítačů Hyper-V pomocí posouzení serveru

Jako součást cesty migrace do Azure zjistíte místní inventář a zatížení. 

V tomto kurzu se dozvíte, jak zjistit místní virtuální počítače Hyper-V pomocí nástroje Azure Migrate: Nástroj pro vyhodnocení serveru s využitím odlehčeného Azure Migrateho zařízení. Zařízení nasadíte jako virtuální počítač Hyper-V, abyste mohli průběžně zjišťovat metadata počítačů a výkonu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení účtu Azure
> * Připravte prostředí Hyper-V pro zjišťování.
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
**Hostitel Hyper-V** | Hostitelé Hyper-V, na kterých jsou virtuální počítače umístěné, můžou být samostatné nebo v clusteru.<br/><br/> V hostiteli musí být spuštěný systém Windows Server 2019, Windows Server 2016 nebo Windows Server 2012 R2.<br/><br/> Ověřte, že jsou povolená příchozí připojení na portu WinRM 5985 (HTTP), aby se zařízení mohlo připojit k vyžádanému metadatům virtuálních počítačů a datům výkonu pomocí model CIM (Common Information Model) (CIM) relace.
**Nasazení zařízení** | Hostitel Hyper-v potřebuje prostředky k přidělení virtuálního počítače pro zařízení:<br/><br/> – Windows Server 2016<br/><br/> – 16 GB paměti RAM<br/><br/> – Osm vCPU<br/><br/> – Přibližně 80 GB diskového úložiště.<br/><br/> – Externí virtuální přepínač.<br/><br/> – Přístup k Internetu na virtuálním počítači přímo nebo prostřednictvím proxy serveru.
**Virtuální počítače** | Virtuální počítače můžou běžet s operačním systémem Windows nebo Linux. 

Než začnete, můžete [zkontrolovat data](migrate-appliance.md#collected-data---hyper-v) , která zařízení shromáždí během zjišťování.

## <a name="prepare-an-azure-user-account"></a>Příprava uživatelského účtu Azure

Chcete-li vytvořit projekt Azure Migrate a zaregistrovat Azure Migrate zařízení, budete potřebovat účet s tímto:
- Oprávnění přispěvatele nebo vlastníka v předplatném Azure.
- Oprávnění k registraci aplikací Azure Active Directory.

Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem vašeho předplatného. Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte oprávnění následujícím způsobem:


1. V Azure Portal vyhledejte "předplatná" a v části **služby** vyberte **předplatná**.

    ![Vyhledávací pole pro hledání předplatného Azure](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Na stránce **předplatná** vyberte předplatné, ve kterém chcete vytvořit projekt Azure Migrate. 
3. V předplatném vyberte **řízení přístupu (IAM)**  >  **Kontrola přístupu**.
4. V části **kontrolovat přístup** vyhledejte příslušný uživatelský účet.
5. V nabídce **Přidat přiřazení role** klikněte na **Přidat**.

    ![Vyhledejte uživatelský účet pro kontrolu přístupu a přiřazení role.](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. V části **Přidat přiřazení role** vyberte roli přispěvatel nebo vlastník a v našem příkladu vyberte účet (azmigrateuser). Potom klikněte na **Uložit**.

    ![Otevře stránku přidat přiřazení role, která účtu přiřadí roli.](./media/tutorial-discover-hyper-v/assign-role.png)

7. Na portálu vyhledejte uživatele a v části **služby** vyberte **Uživatelé**.
8. V **nastavení uživatele** ověřte, že uživatelé Azure AD můžou registrovat aplikace (ve výchozím nastavení nastavené na **Ano** ).

    ![Ověřte v uživatelských nastaveních, která můžou uživatelé registrovat v aplikacích Active Directory.](./media/tutorial-discover-hyper-v/register-apps.png)

9. Alternativně může tenant nebo globální správce přiřadit roli **vývojář aplikací** k účtu, aby umožnil registraci aplikací AAD. [Přečtěte si další informace](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Příprava hostitelů technologie Hyper-V

Nastavte účet s přístupem správce na hostitelích Hyper-V. Zařízení používá tento účet ke zjišťování.

- Možnost 1: Připravte účet s přístupem správce k hostitelskému počítači Hyper-V.
- Možnost 2: Připravte účet místního správce nebo účet správce domény a přidejte tento účet do těchto skupin: Uživatelé vzdálené správy, Správci technologie Hyper-V a uživatelé nástroje Performance Monitor.


## <a name="set-up-a-project"></a>Nastavení projektu

Sestavte nový projekt Azure Migrate.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. V **přehledu** vyberte **vytvořit projekt**.
5. V nástroji **vytvořit projekt** vyberte své předplatné Azure a skupinu prostředků. Vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pole pro název a oblast projektu](./media/tutorial-discover-hyper-v/new-project.png)

7. Vyberte **Vytvořit**.
8. Počkejte několik minut, než se projekt Azure Migrate nasadí.

**Azure Migrate: Nástroj pro vyhodnocení serveru** se ve výchozím nastavení přidá do nového projektu.

![Stránka zobrazující Nástroj pro vyhodnocení serveru přidaný ve výchozím nastavení](./media/tutorial-discover-hyper-v/added-tool.png)


## <a name="set-up-the-appliance"></a>Nastavení zařízení

V tomto kurzu se nastavuje zařízení na virtuálním počítači s technologií Hyper-V následujícím způsobem:

- Zadejte název zařízení a vygenerujte Azure Migrate klíč projektu na portálu.
- Z Azure Portal Stáhněte komprimovaný VHD Hyper-V.
- Vytvořte zařízení a ověřte, že se může připojit k Azure Migrate posouzení serveru.
- Nakonfigurujte zařízení poprvé a zaregistrujte ho pomocí Azure Migrate projektu pomocí klíče Azure Migrate projektu.
> [!NOTE]
> Pokud z nějakého důvodu nemůžete zařízení nastavit pomocí šablony, můžete ho nastavit pomocí skriptu PowerShellu. [Přečtěte si další informace](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).


### <a name="generate-the-azure-migrate-project-key"></a>Vygenerovat klíč projektu Azure Migrate

1. V části **Cíle migrace** > **Servery** > **Azure Migrate: Hodnocení serverů** vyberte **Zjistit**.
2. V rozevíracích **seznamech počítačů**  >  , ve **kterých jsou počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**.
3. V **1: vygenerujte Azure Migrate klíč projektu**, zadejte název pro Azure Migrate zařízení, které nastavíte pro zjišťování virtuálních počítačů Hyper-V. název by měl být alfanumerický a nesmí obsahovat víc než 14 znaků.
1. Kliknutím na **vygenerovat klíč** spustíte vytváření požadovaných prostředků Azure. Během vytváření prostředků prosím Nezavírejte stránku zjišťovacích počítačů.
1. Po úspěšném vytvoření prostředků Azure se vygeneruje **klíč projektu Azure Migrate** .
1. Zkopírujte klíč, protože ho budete potřebovat k dokončení registrace zařízení během jeho konfigurace.

### <a name="download-the-vhd"></a>Stažení virtuálního pevného disku

V **2: Stáhněte zařízení Azure Migrate** vyberte. Soubor VHD a klikněte na **Stáhnout**. 


### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.

2. Spusťte následující příkaz prostředí PowerShell, který vygeneruje hodnotu hash pro soubor ZIP.
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Příklady použití: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Ověřte nejnovější verze zařízení a hodnoty hash:

    - Pro veřejný cloud Azure:

        **Scénář** | **Stáhnout** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 GB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140422) |  40aa037987771794428b1c6ebee2614b092e6d69ac56d48a2bbc75eeef86c99a

    - Pro Azure Government:

        **Scénář** _ | _ *Stáhnout** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="create-the-appliance-vm"></a>Vytvoření virtuálního počítače zařízení

Naimportujte stažený soubor a vytvořte virtuální počítač.

1. Extrahujte soubor VHD s příponou ZIP do složky na hostiteli Hyper-V, která bude hostovat virtuální počítač zařízení. Jsou extrahovány tři složky.
2. Spusťte Správce technologie Hyper-V. V nabídce **Akce** klikněte na **importovat virtuální počítač**.
2. V Průvodci importem virtuálního počítače > **než začnete**, klikněte na **Další**.
3. V části **Vyhledat složku** zadejte složku obsahující extrahovaný virtuální pevný disk. Potom klikněte na **Další**.
1. V nabídce **Vybrat virtuální počítač** klikněte na **Další**.
2. V části **zvolit typ importu** klikněte na **zkopírovat virtuální počítač (vytvořit nové jedinečné ID)**. Potom klikněte na **Další**.
3. V části **zvolit cíl** ponechte výchozí nastavení. Klikněte na **Next** (Další).
4. V části **složky úložiště** ponechte výchozí nastavení. Klikněte na **Next** (Další).
5. V části **zvolit síť** zadejte virtuální přepínač, který bude virtuální počítač používat. Přepínač potřebuje připojení k Internetu, aby bylo možné odesílat data do Azure.
6. V části **Souhrn** zkontrolujte nastavení. Klikněte na **Dokončit**.
7. Ve Správci technologie Hyper-V > **Virtual Machines** spusťte virtuální počítač.


### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se virtuální počítač zařízení může připojit k adresám URL Azure pro cloudy [veřejné](migrate-appliance.md#public-cloud-urls) a [státní správy](migrate-appliance.md#government-cloud-urls) .

### <a name="configure-the-appliance"></a>Konfigurace zařízení

Nastavte zařízení poprvé.

> [!NOTE]
> Pokud zařízení nastavíte pomocí [skriptu PowerShellu](deploy-appliance-script.md) namísto staženého virtuálního pevného disku, první dva kroky v tomto postupu se netýkají.

1. Ve Správci technologie Hyper-V > **Virtual Machines** klikněte pravým tlačítkem na virtuální počítač > **připojit**.
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k VIRTUÁLNÍmu počítači, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy zařízení kliknutím na zástupce aplikace.
1. Přijměte **licenční podmínky** a přečtěte si informace třetích stran.
1. Ve webové aplikaci > **nastavení požadavků** postupujte takto:
    - **Připojení**: aplikace kontroluje, jestli má virtuální počítač přístup k Internetu. Pokud virtuální počítač používá proxy server:
      - Klikněte na **nastavit proxy server** na a zadejte adresu proxy serveru (ve formuláři http://ProxyIPAddress nebo na http://ProxyFQDN) naslouchajícím portu.
      - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
      - Podporuje se jen proxy protokolu HTTP.
      - Pokud jste přidali podrobnosti proxy serveru nebo zakážete proxy server nebo ověřování, kliknutím na **Uložit** spusťte kontrolu připojení znovu.
    - **Časová synchronizace**: čas je ověřený. Čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování virtuálních počítačů fungovalo správně.
    - **Instalovat aktualizace**: posouzení Azure Migrate serveru kontroluje, jestli má zařízení nainstalované nejnovější aktualizace. Po dokončení kontroly můžete kliknout na **Zobrazit služby zařízení** a zobrazit stav a verze komponent spuštěných na zařízení.

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Vložte **klíč projektu Azure Migrate** zkopírovaný z portálu. Pokud tento klíč nemáte, Projděte si část **vyhodnocení serveru> zjistit> spravovat existující zařízení**, vyberte název zařízení, který jste zadali v době generování klíče, a zkopírujte odpovídající klíč.
1. Klikněte na **Přihlásit se**. Otevře se výzva k přihlášení Azure na nové kartě prohlížeče. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
1. Na nové kartě se přihlaste pomocí uživatelského jména a hesla Azure.
   
   Přihlášení pomocí PIN kódu se nepodporuje.
3. Po úspěšném přihlášení se vraťte k webové aplikaci. 
4. Pokud má uživatelský účet Azure použitý k protokolování správná oprávnění k prostředkům Azure vytvořeným během generování klíče, zahájí se registrace zařízení.
1. Po úspěšné registraci zařízení si můžete zobrazit podrobnosti o registraci kliknutím na **Zobrazit podrobnosti**.



### <a name="delegate-credentials-for-smb-vhds"></a>Pověření delegáta pro virtuální pevné disky SMB

Pokud používáte na SMB virtuální pevné disky, musíte povolit delegování přihlašovacích údajů ze zařízení na hostitele Hyper-V. Provedete to pomocí zařízení:

1. Na virtuálním počítači zařízení spusťte tento příkaz. HyperVHost1/HyperVHost2 jsou příklady názvů hostitelů.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Případně to udělejte v Editor místních zásad skupiny na zařízení:
    - V konfiguraci počítače **Zásady místního počítače**  >  **Computer Configuration** klikněte na **šablony pro správu**  >  **System**  >  **delegování přihlašovacích údajů** systému.
    - Dvakrát klikněte na **Povolit delegování nových přihlašovacích údajů** a vyberte **povoleno**.
    - V nabídce **Možnosti** klikněte na **Zobrazit** a do seznamu přidejte každého hostitele Hyper-V, který chcete zjistit, a použijte příkaz **WSMan/** jako předponu.
    - V  **delegování přihlašovacích údajů** poklikejte na možnost **umožňuje delegovat nové přihlašovací údaje pomocí ověřování serveru jenom s protokolem NTLM**. Znovu přidejte všechny hostitele Hyper-V, které chcete vyhledat, do seznamu s použitím nástroje **WSMan/** jako předpony.

## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Připojte se ze zařízení k hostitelům nebo clusterům Hyper-V a spusťte zjišťování virtuálních počítačů.

1. V **kroku 1: zadání přihlašovacích údajů hostitele Hyper-v** klikněte na **Přidat přihlašovací údaje** , abyste zadali popisný název pro přihlašovací údaje, Přidali jste **uživatelské jméno** a **heslo** pro hostitele nebo cluster Hyper-V, které zařízení použije ke zjištění virtuálních počítačů. Klikněte na **Uložit**.
1. Pokud chcete přidat více přihlašovacích údajů najednou, klikněte na **Přidat další** a uložte a přidejte další přihlašovací údaje. Pro zjišťování virtuálních počítačů Hyper-V se podporuje víc přihlašovacích údajů.
1. V **kroku 2: zadání podrobností o hostiteli nebo clusteru technologie Hyper-v** klikněte na **Přidat zdroj zjišťování** a zadejte hostitele Hyper-v nebo **IP adresu clusteru nebo plně kvalifikovaný název domény** a popisný název přihlašovacích údajů pro připojení k hostiteli nebo clusteru.
1. Můžete buď **přidat jednu položku** najednou, nebo **Přidat více položek** do jednoho přechodu. K dispozici je také možnost poskytnout podrobnosti o hostiteli nebo clusteru Hyper-V prostřednictvím **importu CSV**.


    - Pokud zvolíte možnost **přidat jednu položku**, je nutné zadat popisný název pro přihlašovací údaje a hostitele Hyper-V/ **IP adresu clusteru nebo plně kvalifikovaný název domény** a kliknout na **Uložit**.
    - Pokud zvolíte možnost **Přidat více položek** _(ve výchozím nastavení je vybrána možnost)_, můžete přidat více záznamů najednou zadáním názvu hostitele nebo clusteru Hyper-V nebo **plně kvalifikovaného názvu domény** s popisným názvem pro přihlašovací údaje v textovém poli. **Ověřte** přidané záznamy a klikněte na **Uložit**.
    - Pokud zvolíte **importovat sdílený svazek clusteru**, můžete si stáhnout soubor šablony CSV, naplnit soubor pomocí hostitele Hyper-V/ **IP adresy clusteru/plně kvalifikovaného názvu domény** a popisného názvu pro přihlašovací údaje. Pak soubor naimportujete do zařízení, **ověříte** záznamy v souboru a kliknete na **Uložit**.

1. Po kliknutí na Uložit se zařízení pokusí ověřit připojení k hostitelům nebo clusterům Hyper-V a zobrazit **stav ověření** v tabulce pro každého hostitele nebo cluster.
    - Pro úspěšné ověřené hostitele nebo clustery můžete zobrazit další podrobnosti kliknutím na jejich IP adresu nebo plně kvalifikovaný název domény.
    - Pokud se ověření pro hostitele nepovede, přečtěte si chybu kliknutím na **ověření selhalo** ve sloupci Stav v tabulce. Opravte problém a znovu ověřte.
    - Chcete-li odebrat hostitele nebo clustery, klikněte na tlačítko **Odstranit**.
    - Z clusteru nelze odebrat konkrétního hostitele. Můžete odebrat jenom celý cluster.
    - Cluster můžete přidat i v případě, že v clusteru dojde k problémům s konkrétními hostiteli.
1. Připojení k hostitelům a clusterům můžete kdykoli znovu **ověřit** před spuštěním zjišťování.
1. Kliknutím na **Spustit zjišťování Vyhajte** zjišťování virtuálních počítačů z úspěšně ověřených hostitelů/clusterů. Po úspěšném spuštění zjišťování můžete zjistit stav zjišťování u každého hostitele nebo clusteru v tabulce.

Spustí se zjišťování. Zabere přibližně 2 minuty na hostitele, aby se metadata zjištěných serverů zobrazovala v Azure Portal.

## <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po dokončení zjišťování můžete ověřit, že se virtuální počítače zobrazují na portálu.

1. Otevřete řídicí panel služby Azure Migrate.
2. V **Azure Migrate-servery**  >  **Azure Migrate: na stránce posouzení serveru** klikněte na ikonu, která zobrazuje počet **zjištěných serverů**.

## <a name="next-steps"></a>Další kroky

- [Vyhodnoťte virtuální počítače Hyper-V](tutorial-assess-hyper-v.md) pro migraci na virtuální počítače Azure.
- [Zkontrolujte data](migrate-appliance.md#collected-data---hyper-v) , která zařízení shromažďuje během zjišťování.