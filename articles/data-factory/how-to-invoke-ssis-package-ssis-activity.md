---
title: Spuštění balíčku SSIS pomocí aktivity balíčku Execute SSIS
description: Tento článek popisuje, jak spustit balíček služba SSIS (SQL Server Integration Services) (SSIS) v kanálu Azure Data Factory pomocí aktivity spustit balíček SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/14/2019
ms.openlocfilehash: 6027c2d94535ca2ef5c41e7027fe070c6ccb21a0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388563"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Spusťte balíček SSIS s aktivitou spustit balíček SSIS v Azure Data Factory
Tento článek popisuje, jak spustit balíček služba SSIS (SQL Server Integration Services) (SSIS) v kanálu Azure Data Factory pomocí aktivity spustit balíček SSIS. 

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vytvořte prostředí Azure-SSIS Integration runtime (IR), pokud ho ještě nemáte, podle podrobných pokynů v tomto [kurzu: zřizování Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Spuštění balíčku v Azure Portal
V této části použijete Data Factory uživatelské rozhraní (UI) nebo aplikace k vytvoření kanálu Data Factory s aktivitou spustit SSIS balíčku, která spouští balíček SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou Execute SSIS Package
V tomto kroku použijete Data Factory uživatelské rozhraní nebo aplikaci k vytvoření kanálu. Přidáte do kanálu aktivitu spustit balíček SSIS a nakonfigurujete ji tak, aby běžela balíček SSIS. 

1. Na Data Factory přehled nebo na domovské stránce v Azure Portal na samostatné kartě spusťte Data Factory uživatelské rozhraní nebo aplikaci na dlaždici **autora &ho monitoru** . 

   ![Data Factory domovskou stránku](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na stránce **Pusťme se do toho** vyberte **Vytvořit kanál**. 

   ![Stránka Začínáme](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Na panelu nástrojů **aktivity** rozbalte **Obecné**. Poté přetáhněte aktivitu **spustit balíček SSIS** na plochu návrháře kanálu. 

   ![Přetáhněte aktivitu spustit balíček SSIS na plochu návrháře](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Na kartě **Obecné** u aktivity spustit SSIS balíček zadejte název a popis aktivity. Nastavte volitelné hodnoty **timeout** a **opakování** .

   ![Nastavení vlastností na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. Na kartě **Nastavení** aktivity spustit balíček SSIS vyberte Azure-SSIS IR, ve kterém chcete balíček spustit. Pokud váš balíček používá ověřování systému Windows pro přístup k úložištím dat (například k serverům SQL nebo sdíleným složkám souborů v místním nebo Azure), zaškrtněte políčko **ověřování systému Windows** . Do polí **doména**, **uživatelské jméno**a **heslo** zadejte hodnoty přihlašovacích údajů pro spuštění balíčku. 

    Alternativně můžete jako své hodnoty použít tajné klíče uložené ve vašem trezoru klíčů Azure. Uděláte to tak, že vedle příslušných přihlašovacích údajů zaškrtněte políčko **Trezor klíčů Azure** . Vyberte nebo upravte existující propojenou službu trezoru klíčů nebo vytvořte novou. Pak vyberte tajný název nebo verzi pro hodnotu přihlašovacích údajů.

    Když vytváříte nebo upravujete propojenou službu trezoru klíčů, můžete vybrat nebo upravit existující Trezor klíčů nebo vytvořit nový. Pokud jste to ještě neudělali, ujistěte se, že jste vašemu trezoru klíčů udělili Data Factory spravovaný přístup k identitám. Tajná klíčová okna můžete zadat také přímo v následujícím formátu: `<Key vault linked service name>/<secret name>/<secret version>`. Pokud váš balíček potřebuje 32 spuštění runtime, zaškrtněte políčko **32 modul runtime** .

   V případě **umístění balíčku**vyberte **SSISDB**, **systém souborů (balíček)** , **systém souborů (projekt**) nebo **vložený balíček**. Pokud jako umístění balíčku vyberete **SSISDB** , která se automaticky vybere v případě, že jste Azure-SSIS IR zřídili pomocí katalogu SSIS (SSISDB), který je hostovaný Azure SQL Database serverem nebo spravovanou instancí, určete balíček, který se má spustit, který byl nasazený na SSISDB. 

    Pokud je spuštěný Azure-SSIS IR a políčko **Ruční Ruční zadání** není zaškrtnuto, vyhledejte a vyberte existující složky, projekty, balíčky nebo prostředí z SSISDB. Vyberte **aktualizovat** a načtěte vaše nově přidané složky, projekty, balíčky nebo prostředí z SSISDB, aby byly dostupné pro procházení a výběr. Chcete-li procházet nebo vybrat prostředí pro spuštění balíčku, je nutné nakonfigurovat vaše projekty předem, aby byla tato prostředí přidána jako odkazy ze stejných složek v SSISDB. Další informace najdete v tématu [Vytvoření a mapování SSIS prostředí](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   V části **úroveň protokolování**vyberte předdefinovaný rozsah protokolování pro spuštění balíčku. Pokud chcete místo toho zadat vlastní název protokolování, zaškrtněte políčko **vlastní** . 

   ![Nastavení vlastností na kartě nastavení – automaticky](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Pokud vaše Azure-SSIS IR není spuštěná nebo je zaškrtnuté políčko **Ruční** zadání, zadejte cestu k balíčku a prostředí z SSISDB přímo v následujících formátech: `<folder name>/<project name>/<package name>.dtsx` a `<folder name>/<environment name>`.

   ![Nastavení vlastností na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Pokud jako umístění balíčku vyberete **systém souborů (balíček)** , který se automaticky vybere, pokud jste Azure-SSIS IR zřídili bez SSISDB, určete balíček, který se má spustit, zadáním cesty UNC (Universal Naming Convention) k souboru balíčku (`.dtsx`) v poli **cesta k balíčku** . Pokud například uložíte balíček do souborů Azure, jeho cesta k balíčku je `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Pokud nakonfigurujete balíček v samostatném souboru, musíte zadat cestu UNC ke konfiguračnímu souboru (`.dtsConfig`) v poli **cesta konfigurace** . Pokud například uložíte konfiguraci do služby soubory Azure, jeho konfigurační cesta se `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Nastavení vlastností na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Pokud jako umístění balíčku vyberete možnost **systém souborů (projekt)** , určete balíček, který se má spustit, zadáním cesty UNC k souboru projektu (`.ispac`) v poli **cesta k projektu** a souboru balíčku (`.dtsx`) z projektu v poli **název balíčku** . Pokud například uložíte projekt do souborů Azure, jeho cesta k projektu je `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Nastavení vlastností na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Pak zadejte přihlašovací údaje pro přístup k vašemu projektu, balíčku nebo konfiguračnímu souboru. Pokud jste dříve zadali hodnoty přihlašovacích údajů pro spuštění balíčku (viz předchozí), můžete je znovu použít zaškrtnutím políčka **stejné jako přihlašovací údaje pro spuštění balíčku** . V opačném případě zadejte hodnoty přihlašovacích údajů k balíčku do polí **doména**, **uživatelské jméno**a **heslo** . Pokud například uložíte projekt, balíček nebo konfiguraci do služby soubory Azure, doména je `Azure`, uživatelské jméno je `<storage account name>`a heslo je `<storage account key>`. 

   Alternativně můžete použít tajné klíče uložené ve vašem trezoru klíčů jako jejich hodnoty (viz předchozí). Tyto přihlašovací údaje se používají pro přístup k balíčku a podřízeným balíčkům v úloze spustit balíček, vše z vlastní cesty nebo stejného projektu a také konfigurací, které zahrnují ty, které jsou uvedené v balíčcích. 

   Pokud jako umístění balíčku vyberete **vložený balíček** , můžete balíček spustit nebo ho **nahrát** ze složky souboru do zadaného pole. Balíček se automaticky komprimuje a vloží do datové části aktivity. Po vložení si můžete balíček později **Stáhnout** a upravit. Vložený balíček můžete také **parametrizovat** tak, že ho přiřadíte k parametru kanálu, který se dá použít ve více aktivitách, takže se optimalizuje velikost datové části kanálu. Pokud vložený balíček není zašifrovaný a zjistili jsme, že se v něm používá úloha spustit balíček, automaticky se vybere **úloha spustit balíček** a příslušné podřízené balíčky s jejich odkazy na systém souborů se automaticky přidají, abyste je mohli vložit. Pokud nemůžete detekovat úlohu použití balíčku Execute, budete muset ručně zaškrtnout políčko **spustit balíček** a přidat relevantní podřízené balíčky s jejich systémem souborů na jeden po jednom, aby je bylo možné vložit. Pokud podřízené balíčky používají SQL Server odkazy, ujistěte se prosím, že je SQL Server přístupná pro vaše Azure-SSIS IR.  Použití odkazů projektu pro podřízené balíčky není aktuálně podporováno.
   
   ![Nastavení vlastností na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Pokud jste při vytváření balíčku pomocí nástroje SQL Server Data Tools použili úroveň ochrany **EncryptAllWithPassword** nebo **EncryptSensitiveWithPassword** , zadejte v poli **Šifrovací heslo** hodnotu pro heslo. Alternativně můžete jako svou hodnotu použít tajný kód uložený v trezoru klíčů (viz předchozí). Pokud jste použili úroveň ochrany **EncryptSensitiveWithUserKey** , znovu zadejte své citlivé hodnoty do konfiguračních souborů nebo na karty **SSIS**, **Správce připojení**nebo **přepsání vlastností** (viz později). 

   Pokud jste použili úroveň ochrany **EncryptAllWithUserKey** , není podporována. Musíte znovu nakonfigurovat balíček tak, aby používal jinou úroveň ochrany prostřednictvím nástroje SQL Server Data Tools nebo nástroje příkazového řádku `dtutil`. 
   
   V části **úroveň protokolování**vyberte předdefinovaný rozsah protokolování pro spuštění balíčku. Pokud chcete místo toho zadat vlastní název protokolování, zaškrtněte políčko **vlastní** . Pokud chcete protokolovat provádění balíčků mimo použití standardních zprostředkovatelů protokolů, které lze zadat v balíčku, zadejte svou složku protokolu zadáním cesty UNC do pole **cesta protokolování** . Pokud například uložíte protokoly do souborů Azure, vaše cesta protokolování se `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. V této cestě se vytvoří podsložka pro každý jednotlivý balíček, který se pojmenuje po spuštění s ID spuštění aktivity balíčku SSIS, ve kterém se soubory protokolů generují každých pět minut. 
   
   Nakonec zadejte pověření pro přístup do složky protokolu. Pokud jste dříve zadali hodnoty přihlašovacích údajů pro přístup k balíčku (viz předchozí), můžete je znovu použít tak, že vyberete **stejné jako Přístupová pověření k balíčku** . V opačném případě zadejte hodnoty přihlašovacích údajů pro přístup k přihlášení do polí **doména**, **uživatelské jméno**a **heslo** . Pokud například ukládáte protokoly do souborů Azure, doména je `Azure`, uživatelské jméno je `<storage account name>`a heslo `<storage account key>`. 

    Alternativně můžete použít tajné klíče uložené ve vašem trezoru klíčů jako jejich hodnoty (viz předchozí). Tyto přihlašovací údaje se používají k ukládání protokolů. 
   
   U všech výše zmíněných cest UNC musí mít plně kvalifikovaný název souboru méně než 260 znaků. Název adresáře musí být kratší než 248 znaků.

1. Na kartě **parametry SSIS** pro aktivitu spustit SSIS balíčku, pokud je vaše Azure-SSIS IR spuštěná, jako umístění balíčku se vybere **SSISDB** a na kartě nastavení se zobrazí zaškrtávací políčko **Ruční zadání** na kartě **Nastavení** se zobrazí stávající parametry SSIS ve vybraném projektu nebo balíčku z SSISDB, abyste jim přiřadili hodnoty. V opačném případě je můžete zadat jednu po jedné a přiřadit k nim hodnoty ručně. Ujistěte se, že existují a jsou správně zadány pro spuštění balíčku, aby bylo úspěšné. 
   
   Pokud jste při vytváření balíčku použili úroveň ochrany **EncryptSensitiveWithUserKey** pomocí nástroje SQL Server Data Tools a systém souborů ( **balíček** ) nebo **systém souborů (projekt)** , musíte také znovu zadat citlivé parametry a přiřadit jim jim hodnoty v konfiguračních souborech nebo na této kartě. 
   
   Při přiřazování hodnot k parametrům můžete přidat dynamický obsah pomocí výrazů, funkcí, Data Factory systémových proměnných a Data Factory parametrů kanálu nebo proměnných. Alternativně můžete použít tajné klíče uložené ve vašem trezoru klíčů jako jejich hodnoty (viz předchozí).

   ![Nastavení vlastností na kartě Parametry SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Na kartě **Správci připojení** pro aktivitu spustit SSIS balíčku, pokud je vaše Azure-SSIS IR spuštěná, jako umístění balíčku se vybere **SSISDB** a na kartě nastavení se zruší zaškrtávací políčko **Ruční položky** na kartě **Nastavení** , zobrazí se stávající Správci připojení ve vybraném projektu nebo balíčku z SSISDB, abyste jim přiřadili hodnoty jejich vlastností. V opačném případě je můžete zadat jednu po jedné a přiřadit hodnoty jejich vlastnostem ručně. Ujistěte se, že existují a jsou správně zadány pro spuštění balíčku, aby bylo úspěšné. 
   
   Pokud jste při vytváření balíčku použili úroveň ochrany **EncryptSensitiveWithUserKey** pomocí nástroje SQL Server Data Tools a systém souborů ( **balíček** ) nebo **systém souborů (projekt)** je jako umístění balíčku vybraný, musíte znovu zadat vlastnosti citlivý Správce připojení a přiřadit jim hodnoty v konfiguračních souborech nebo na této kartě. 
   
   Když přiřazujete hodnoty k vlastnostem Správce připojení, můžete přidat dynamický obsah pomocí výrazů, funkcí, Data Factory systémových proměnných a Data Factory parametrů kanálu nebo proměnných. Alternativně můžete použít tajné klíče uložené ve vašem trezoru klíčů jako jejich hodnoty (viz předchozí).

   ![Nastavení vlastností na kartě Správci připojení](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. Na kartě **přepsání vlastností** aktivity spustit balíček SSIS zadejte cesty existujících vlastností vybraného balíčku jeden po jednom, aby byly hodnoty přiřazeny ručně. Ujistěte se, že existují a jsou správně zadány pro spuštění balíčku, aby bylo úspěšné. Chcete-li například přepsat hodnotu proměnné uživatele, zadejte její cestu v následujícím formátu: `\Package.Variables[User::<variable name>].Value`. 
   
   Pokud jste při vytváření balíčku použili úroveň ochrany **EncryptSensitiveWithUserKey** pomocí nástroje SQL Server Data Tools a systém souborů ( **balíček** ) nebo **systém souborů (projekt)** je jako umístění balíčku vybraný, musíte znovu zadat citlivé vlastnosti a přiřadit jim hodnoty v konfiguračních souborech nebo na této kartě. 
   
   Když přiřazujete hodnoty k vlastnostem, můžete přidat dynamický obsah pomocí výrazů, funkcí, Data Factory systémových proměnných a Data Factory parametrů kanálu nebo proměnných.

   ![Nastavte vlastnosti na kartě přepsání vlastností.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Hodnoty přiřazené v konfiguračních souborech a na kartě **parametry SSIS** lze přepsat pomocí karet **Správce připojení** nebo **přepsání vlastností** . Hodnoty přiřazené na kartě **Správci připojení** lze také přepsat pomocí karty **vlastnosti Overrides** .

1. Pokud chcete ověřit konfiguraci kanálu, vyberte **ověřit** na panelu nástrojů. Pokud chcete **sestavu ověření kanálu**zavřít, vyberte **>>** .

1. Pokud chcete publikovat kanál do Data Factory, vyberte **publikovat vše**. 

### <a name="run-the-pipeline"></a>Spuštění kanálu
V tomto kroku aktivujete spuštění kanálu. 

1. Pokud chcete aktivovat spuštění kanálu, vyberte **aktivační událost** na panelu nástrojů a vyberte **aktivovat nyní**. 

   ![Aktivovat](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. V okně **Spuštění kanálu** vyberte **Dokončit**. 

### <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se spuštění kanálu a jeho stav spolu s dalšími informacemi, jako je například čas **spuštění** . Pokud chcete zobrazení aktualizovat, vyberte **Aktualizovat**.

   ![Spuštění kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se pouze jedno spuštění aktivity, protože kanál má pouze jednu aktivitu. Je to aktivita SSIS balíčku Execute.

   ![Spuštění aktivit](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Spusťte následující dotaz pro databázi SSISDB na serveru SQL Server a ověřte, zda byl balíček spuštěn. 

   ```sql
   select * from catalog.executions
   ```

   ![Ověření provedení balíčku](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. ID spuštění SSISDB můžete také získat z výstupu aktivity kanálu a pomocí tohoto ID ověřit komplexnější protokoly spouštění a chybové zprávy v SQL Server Management Studio.

   ![Získá ID spuštění.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Naplánování kanálu pomocí triggeru

Můžete také vytvořit plánovanou aktivační událost pro svůj kanál, aby kanál běžel podle plánu, například každou hodinu nebo každý den. Příklad najdete v tématu [Vytvoření datové továrny – Data Factory uživatelského rozhraní](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Spuštění balíčku pomocí prostředí PowerShell
V této části použijete Azure PowerShell k vytvoření kanálu Data Factory s aktivitou spustit SSIS balíčku, která spouští balíček SSIS. 

Pomocí podrobných pokynů, [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-az-ps), nainstalujte nejnovější Azure PowerShell moduly.

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Vytvoření datové továrny pomocí Azure-SSIS IR
Můžete buď použít stávající objekt pro vytváření dat, který už Azure-SSIS IR zřízený, nebo vytvořit novou datovou továrnu s Azure-SSIS IR. Postupujte podle podrobných pokynů v tomto [kurzu: nasazení balíčků SSIS do Azure prostřednictvím PowerShellu](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou Execute SSIS Package 
V tomto kroku vytvoříte kanál s aktivitou spustit SSIS s balíčkem. Aktivita spustí balíček SSIS. 

1. Ve složce *C:\ADF\RunSSISPackage* vytvořte soubor JSON s názvem *RunSSISPackagePipeline. JSON* s obsahem podobným následujícímu příkladu.

   > [!IMPORTANT]
   > Před uložením souboru nahraďte názvy objektů, popisy a cesty, vlastnosti nebo hodnoty parametrů, hesla a další hodnoty proměnných. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Pokud chcete spouštět balíčky uložené v souborové systémy, sdílené složky nebo soubory Azure, zadejte následující hodnoty vlastností balíčku a umístění protokolu:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                           },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Pokud chcete spouštět balíčky v projektech uložených v systémech souborů, sdílených složkách nebo souborech Azure, zadejte hodnoty pro vlastnost umístění balíčku následujícím způsobem:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Chcete-li spustit vložené balíčky, zadejte hodnoty pro vlastnost umístění balíčku následujícím způsobem:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. V Azure PowerShell přepněte do složky *C:\ADF\RunSSISPackage* .

3. Pokud chcete vytvořit kanál **RunSSISPackagePipeline**, spusťte rutinu **set-AzDataFactoryV2Pipeline** .

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Zde je ukázkový výstup:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Spuštění kanálu
Ke spuštění kanálu použijte rutinu **Invoke-AzDataFactoryV2Pipeline** . Tato rutina vrací ID spuštění kanálu pro budoucí monitorování.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorování kanálu

Spusťte následující skript PowerShellu, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud nedokončí kopírování dat. Zkopírujte nebo vložte následující skript v okně PowerShellu a vyberte Enter. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Kanál můžete také monitorovat pomocí Azure Portal. Podrobné pokyny najdete v tématu [monitorování kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Naplánování kanálu pomocí triggeru
V předchozím kroku jste kanál spustili na vyžádání. Můžete také vytvořit aktivační proceduru Schedule pro spuštění kanálu podle plánu, například každou hodinu nebo každý den.

1. Ve složce *C:\ADF\RunSSISPackage* vytvořte soubor JSON s názvem *MyTrigger. JSON* s následujícím obsahem: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. V Azure PowerShell přepněte do složky *C:\ADF\RunSSISPackage* .
1. Spusťte rutinu **set-AzDataFactoryV2Trigger** , která vytvoří Trigger. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Ve výchozím nastavení je aktivační událost v zastaveném stavu. Spusťte Trigger spuštěním rutiny **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Spuštěním rutiny **Get-AzDataFactoryV2Trigger** ověřte, že je aktivační událost spuštěná. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Po další hodiny spusťte následující příkaz. Pokud je například aktuální čas 3:25. UTC, spusťte příkaz ve 4. UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Spusťte následující dotaz pro databázi SSISDB na serveru SQL Server a ověřte, zda byl balíček spuštěn. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Další kroky
Podívejte se na tento příspěvek na blogu:
- [Modernizovat a rozšíří pracovní postupy ETL/ELT s aktivitami SSIS v kanálu Azure Data Factory.](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
