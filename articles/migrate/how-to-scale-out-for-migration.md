---
title: Nastavení Azure Migrate zařízení se škálováním na více instancí pro migraci VMware bez agenta
description: Přečtěte si, jak nastavit Azure Migrate zařízení se škálováním na více instancí pro migraci virtuálních počítačů Hyper-V.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 5ca821cb4f85deb77595e4a9029cc10298dbb884
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611969"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>Škálování migrace virtuálních počítačů VMware bez agenta do Azure

Tento článek vám pomůže pochopit, jak používat zařízení se škálováním na více instancí k migraci velkého počtu virtuálních počítačů VMware do Azure s využitím metody bez agentů nástroje pro migraci Azure Migrate serveru pro migraci virtuálních počítačů VMware.

Pomocí metody migrace bez agenta pro virtuální počítače VMware můžete:

- Replikace až 300 virtuálních počítačů z jednoho serveru vCenter současně pomocí jednoho zařízení Azure Migrate.
- Replikaci až 500 virtuálních počítačů z jednoho serveru vCenter současně nasazením druhého zařízení se škálováním na více instancí pro migraci.

V tomto článku se dozvíte, jak:

- Přidání zařízení se škálováním na více instancí pro migraci virtuálních počítačů VMware bez agenta
- Migrujte až 500 virtuálních počítačů současně pomocí zařízení se škálováním na více instancí.

##  <a name="prerequisites"></a>Požadavky

Než začnete, musíte provést následující kroky:

- Vytvořte projekt Azure Migrate.
- Nasaďte zařízení Azure Migrate (primární zařízení) a dokončete zjišťování virtuálních počítačů VMware spravovaných serverem vCenter.
- Nakonfigurujte replikaci pro jeden nebo víc virtuálních počítačů, které se mají migrovat.
> [!IMPORTANT]
> Předtím, než budete moct přidat zařízení se škálováním na více instancí pro migraci, budete muset mít aspoň jeden virtuální počítač replikování v projektu.

Informace o tom, jak postupovat výše, najdete v kurzu migrace [virtuálních počítačů VMware do Azure s metodou migrace bez agenta](./tutorial-migrate-vmware.md).

## <a name="deploy-a-scale-out-appliance"></a>Nasazení zařízení se škálováním na více instancí

Pokud chcete přidat zařízení se škálováním na více instancí, postupujte podle níže uvedených kroků:

1. Klikněte na **zjišťování**  >  **jsou virtualizované vaše počítače?** 
1. Vyberte možnost **Ano, s VMware vSphere hypervisorem.**
1. V dalším kroku vyberte možnost replikace bez agenta.
1. V nabídce vybrat typ zařízení vyberte **škálovat na stávající primární zařízení** .
1. Vyberte primární zařízení (zařízení, pomocí kterého bylo provedeno zjišťování), u kterého chcete škálovat kapacitu.

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="Stránka zjišťování počítačů pro registraci škálování na více instancí":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. vygenerujte klíč projektu Azure Migrate.

1. V části **generovat Azure Migrate klíč projektu** zadejte název přípony pro zařízení se škálováním na více instancí. Přípona může obsahovat pouze alfanumerické znaky a má omezení délky 14 znaků.
2. Kliknutím na **vygenerovat klíč** spustíte vytváření požadovaných prostředků Azure. Nezavírejte stránku zjišťování během vytváření prostředků.
3. Zkopírujte vygenerovaný klíč. K dokončení registrace zařízení se škálováním na více instancí budete potřebovat klíč později.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. Stáhněte si instalační program pro zařízení se škálováním na více instancí.

V nabídce **stáhnout Azure Migrate zařízení** klikněte na  **Stáhnout**. Abyste mohli nasadit zařízení se škálováním na více systémů na stávajícím serveru se systémem Windows Server 2016 a požadovanou hardwarovou konfiguraci (32-GB RAM, 8 vCPU, kolem 80 GB diskového úložiště a internetového přístupu, ať už přímo nebo prostřednictvím proxy serveru), potřebujete stáhnout skript instalačního programu PowerShellu.
:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="Stáhnout skript pro zařízení se škálováním na více instancí":::

> [!TIP]
> Kontrolní součet staženého souboru ZIP můžete ověřit pomocí těchto kroků:
>
> 1. Otevřete příkazový řádek jako správce.
> 2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad použití pro veřejný cloud: ```C:\>Get-FileHash -Path .\AzureMigrateInstaller-VMware-Public-Scaleout.zip -Algorithm SHA256 ```
> 3. Stáhněte si nejnovější verzi instalačního programu zařízení se škálováním na více instancí z portálu, pokud vypočítaná hodnota hash neodpovídá tomuto řetězci: 1E6B6E3EE8B2A800818B925F5DA67EF7874DAD87E32847120B32F3E21F5960F9

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Spusťte skript instalačního programu Azure Migrate
Skript instalačního programu provede následující akce:

- Nainstaluje agenta brány a Správce konfigurace zařízení, aby bylo možné provádět další souběžné replikace serveru.
- Nainstalujte role Windows, včetně aktivační služby Windows, služby IIS a prostředí PowerShell ISE.
- Stáhněte a nainstalujte zapisovatelný modul IIS. [Další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) o trvalé podrobnosti nastavení pro Azure Migrate.
- Vytvoří následující soubory pod cestou:
    - **Konfigurační soubory**:%ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**:%ProgramData%\Microsoft Azure\Logs

Spusťte skript následujícím způsobem:

1. Extrahujte soubor zip do složky na serveru, který bude hostitelem zařízení se škálováním na více instancí.  Ujistěte se, že nespouštíte skript na serveru s existujícím zařízením Azure Migrate.
2. Na výše uvedeném serveru s oprávněním správce (zvýšené) spusťte PowerShell.
3. Změňte adresář PowerShellu na složku, ve které byl obsah extrahován ze staženého souboru ZIP.
4. Spusťte skript s názvem **AzureMigrateInstaller.ps1**  pomocí následujícího příkazu:

    - Pro veřejný cloud: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```

    Skript spustí Správce konfigurace zařízení, jakmile dokončí provádění.

Pokud provedete všechny problémy, můžete získat přístup k protokolům skriptu na adrese: <br/> C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log pro řešení potíží.


### <a name="4-configure-the-appliance"></a>4. konfigurace zařízení

Než začnete, zajistěte, aby [tyto koncové body Azure](migrate-appliance.md#public-cloud-urls) byly dostupné ze zařízení se škálováním na více instancí.

- Otevřete prohlížeč na jakémkoli počítači, který se může připojit k serveru zařízení se škálováním na více instancí, a otevřete adresu URL nástroje Configuration Manager pro zařízení: **https://,*název zařízení s možností horizontálního rozšíření kapacity nebo IP adresa*: 44368**.

   Alternativně můžete otevřít Configuration Manager z plochy serveru se škálováním na více zařízení pomocí zástupce nástroje Configuration Manager.
- Přijměte **licenční podmínky** a přečtěte si informace třetích stran.
- V části Configuration Manager > **nastavit požadavky** proveďte následující kroky:
   - **Připojení**: zařízení kontroluje, jestli má server přístup k Internetu. Pokud server používá proxy server:
     1. Klikněte na **instalační program proxy** a zadejte adresu proxy serveru (ve formuláři http://ProxyIPAddress nebo http://ProxyFQDN) portu pro naslouchání.
     2. Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
     3. Podporuje se jen proxy protokolu HTTP.
     4. Pokud jste přidali podrobnosti proxy serveru nebo zakážete proxy server nebo ověřování, kliknutím na **Uložit** spusťte kontrolu připojení znovu.
   - **Čas synchronizace**: čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování fungovalo správně.
   - **Nainstalovat aktualizace**: zařízení zajišťuje, že jsou nainstalované nejnovější aktualizace. Po dokončení kontroly můžete kliknout na **Zobrazit služby zařízení** a zobrazit stav a verze služeb spuštěných na serveru zařízení.
   - **Instalace VDDK**: zařízení kontroluje, jestli je nainstalovaná VMware vSphere Virtual disk Development Kit (VDDK). Pokud není nainstalovaný, Stáhněte si z VMware VDDK 6,7 a Extrahujte stažený obsah zip do zadaného umístění na zařízení, jak je uvedené v **pokynech k instalaci** na obrazovce Configuration Manager zařízení.


### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Vložte **klíč projektu Azure Migrate** zkopírovaný z portálu. Pokud tento klíč nemáte, přečtěte si na stránce **posouzení serveru> zjistit> spravovat existující zařízení**, vyberte název primárního zařízení, najděte k němu zařízení se škálováním na více instancí a zkopírujte odpovídající klíč.
1. K ověření pomocí Azure budete potřebovat kód zařízení. Kliknutím na **přihlášení** se otevře modální okno s kódem zařízení, jak je znázorněno níže.
:::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modální zobrazení kódu zařízení":::

1. Kliknutím na **zkopírovat kód & přihlášením** zkopírujte kód zařízení a otevřete výzvu k přihlášení Azure na nové kartě prohlížeče. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
1. Na kartě nový vložte kód zařízení a přihlaste se pomocí uživatelského jména a hesla Azure.
   
   Přihlášení pomocí PIN kódu se nepodporuje.
3. Pokud kartu přihlášení omylem zavřete bez přihlašování, budete muset aktualizovat kartu prohlížeče Configuration Manageru a povolit tak tlačítko pro přihlášení znovu.
1. Po úspěšném přihlášení se vraťte na předchozí kartu pomocí Správce konfigurace zařízení.
1. Pokud má uživatelský účet Azure použitý k protokolování správná oprávnění k prostředkům Azure vytvořeným během generování klíče, zahájí se registrace zařízení.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="Panel 2 – Správce konfigurace zařízení":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>Importovat konfiguraci zařízení z primárního zařízení

Pokud chcete dokončit registraci zařízení se škálováním na více instancí, klikněte na **importovat** a získejte potřebné konfigurační soubory z primárního zařízení.

1. Kliknutím na **importovat** se otevře automaticky otevírané okno s pokyny, jak naimportovat nezbytné konfigurační soubory z primárního zařízení.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="Importovat modální konfiguraci":::
1. Přihlaste se k primárnímu zařízení (Vzdálená plocha) a spusťte následující příkazy PowerShellu:

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. Zkopírujte soubor zip vytvořený spuštěním příkazů výše na zařízení se škálováním na více instancí. Soubor zip obsahuje konfigurační soubory potřebné k registraci zařízení se škálováním na více instancí.
1. V automaticky otevíraném okně otevřeném v předchozím kroku vyberte umístění zkopírovaného konfiguračního souboru zip a klikněte na **Uložit**.

Po úspěšném naimportování souborů se registrace zařízení se škálováním na více instancí dokončí a zobrazí se časové razítko posledního úspěšného importu. Kliknutím na **Zobrazit podrobnosti** můžete zobrazit také podrobnosti o registraci.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="Snímek obrazovky ukazuje registraci zařízení se škálováním na více instancí s Azure Migrate projektu.":::

V tuto chvíli byste měli znovu ověřit, jestli se zařízení se škálováním na více instancí může připojit k serveru vCenter. Kliknutím na znovu **ověřit** ověřte vCenter Server připojení ze zařízení se škálováním na více instancí.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="Snímek obrazovky ukazuje zobrazení přihlašovacích údajů a zdrojů zjišťování, které se mají ověřit.":::

> [!IMPORTANT]
> Pokud upravíte přihlašovací údaje vCenter Server na primárním zařízení, nezapomeňte znovu importovat konfigurační soubory do zařízení se škálováním na více instancí, abyste získali nejnovější konfiguraci a pokračovali v probíhajících replikacích.<br/> Pokud už nepotřebujete zařízení se škálováním na více instancí, ujistěte se, že jste zakázali zařízení se škálováním na více instancí. [**Přečtěte si další informace**](./common-questions-appliance.md) o tom, jak zakázat zařízení se škálováním na více instancí v případě potřeby.

## <a name="replicate"></a>Replikace

1. Po zaregistrování zařízení se škálováním na více instancí klikněte na dlaždici Azure Migrate: Migrace serveru na **replikovat**.

2.  Podle pokynů na obrazovce spusťte replikaci více virtuálních počítačů. 

U zařízení se škálováním na více instancí teď můžete replikovat 500 virtuálních počítačů současně. Virtuální počítače můžete také migrovat v dávkách 200 až po Azure Portal.

Nástroj pro migraci Azure Migrate serveru se postará o distribuci virtuálních počítačů mezi primárním zařízením a zařízením se škálováním na více instancí pro replikaci. Po dokončení replikace můžete migrovat virtuální počítače.

> [!TIP]
> Pro zajištění optimálního výkonu, pokud chcete migrovat velký počet virtuálních počítačů, doporučujeme migrovat virtuální počítače v dávkách 200.
  
## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili:
- Jak nakonfigurovat zařízení se škálováním na více instancí
- Jak replikovat virtuální počítače pomocí zařízení se škálováním na více instancí


[Přečtěte si další informace](./tutorial-migrate-vmware.md) o migraci serverů do Azure pomocí Azure Migrate: Nástroj pro migraci serveru.