---
title: Spuštění balíčku SSIS s aktivitou spustit balíček SSIS
description: Tento článek popisuje, jak spustit balíček SQL Server Integration Services (SSIS) v kanálu Azure Data Factory pomocí aktivity spouštění balíčku SSIS.
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
ms.openlocfilehash: f400463f6102d46d9da48bbb10466ad4ca04a69b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413238"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Spuštění balíčku SSIS pomocí aktivity Spustit balíček SSIS ve službě Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak spustit balíček SQL Server Integration Services (SSIS) v kanálu Azure Data Factory pomocí aktivity spouštění balíčku SSIS. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vytvořte runtime integrace Azure-SSIS (IR), pokud ho ještě nemáte podle podrobných pokynů v [kurzu: Zřizování Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Spuštění balíčku na webu Azure Portal
V této části použijete uživatelské rozhraní data factory (UI) nebo aplikace k vytvoření kanálu data factory s aktivitou spouštění balíčku SSIS, který spouští balíček SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou balíčku SSIS
V tomto kroku použijete k vytvoření kanálu unové hospo- nastavení datové továrny nebo aplikace. Přidáte spustit aktivitu balíčku SSIS do kanálu a nakonfigurujte ji tak, aby spouštěla balíček SSIS. 

1. Na přehledu datové továrny nebo na domovské stránce na webu Azure Portal vyberte dlaždici **Author & Monitor** a spusťte ui datové továrny nebo aplikaci na samostatné kartě. 

   ![Domovská stránka datové továrny](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na stránce **Začínáme** vyberte **Vytvořit kanál**. 

   ![Stránka Začínáme](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. V panelu nástrojů **Aktivity** rozbalte **položku Obecné**. Potom **přetáhněte aktivitu Spustit balíček SSIS** na povrch návrháře kanálu. 

   ![Přetáhněte aktivitu Spustit balíček SSIS na povrch návrháře.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Na kartě **Obecné** pro aktivitu Spouštět balíček SSIS zadejte název a popis aktivity. Nastavte volitelné hodnoty **časového času** **a opakování.**

   ![Nastavení vlastností na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. Na kartě **Nastavení** pro aktivitu spouštění balíčku SSIS vyberte infračervený přenos Azure-SSIS, ve kterém chcete balíček spustit. Pokud váš balíček používá ověřování systému Windows pro přístup k úložištím dat (například sql servery nebo sdílené složky v místním prostředí nebo soubory Azure Files), zaškrtněte políčko **Ověřování systému Windows.** Zadejte hodnoty pověření pro spuštění balíčku do polí **Doména**, **Uživatelské jméno**a **Heslo.** 

    Alternativně můžete použít tajné kódy uložené v trezoru klíčů Azure jako jejich hodnoty. Chcete-li tak učinit, zaškrtněte políčko **AZURE KEY VAULT** vedle příslušného pověření. Vyberte nebo upravte stávající propojenou službu trezoru klíčů nebo vytvořte novou. Pak vyberte tajný název nebo verzi pro hodnotu pověření.

    Když vytváříte nebo upravujete propojenou službu trezoru klíčů, můžete vybrat nebo upravit stávající trezor klíčů nebo vytvořit nový. Pokud jste tak ještě neučinili, ujistěte se, že jste službě Data Factory udělili přístup ke spravované identitě klíčů. Můžete také zadat své tajné klíče `<Key vault linked service name>/<secret name>/<secret version>`přímo v následujícím formátu: . Pokud váš balíček potřebuje ke spuštění 32bitový runtime, zaškrtněte políčko **32bitový běhový** čas.

   Pro **umístění balíčku**vyberte **položku SSISDB**, **Systém souborů (balíček),** **Systém souborů (Projekt)** nebo **Vložený balíček**. Pokud jako umístění balíčku vyberete **SSISDB,** které se automaticky vybere, pokud byla vaše instražce Azure-SSIS zřízena pomocí katalogu SSIS (SSISDB) hostovaného serverem Azure SQL Database nebo spravovanou instancí, zadejte balíček, který se má spustit, který byl nasazen do SSISDB. 

    Pokud je vaše zařízení Azure-SSIS IR spuštěno a políčko **Ruční zadávání** je zaškrtnuté, procházejte a vyberte existující složky, projekty, balíčky nebo prostředí z Databáze SSISDB. Vyberte **Aktualizovat,** chcete-li načíst nově přidané složky, projekty, balíčky nebo prostředí z Databáze SSISDB, aby byly k dispozici pro procházení a výběr. Chcete-li procházet nebo vybrat prostředí pro spuštění balíčků, je nutné předem nakonfigurovat projekty tak, aby tato prostředí přidávala jako odkazy ze stejných složek v rámci SSISDB. Další informace naleznete v [tématu Vytvoření a mapování prostředí SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   V **části Protokolování vyberte**předdefinovaný rozsah protokolování pro spuštění balíčku. Pokud chcete místo toho zadat vlastní název protokolování, zaškrtněte toto **políčko Vlastní** nastavení. 

   ![Nastavení vlastností na kartě Nastavení – automaticky](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Pokud vaše zařízení Azure-SSIS IR není spuštěno nebo je zaškrtnuté políčko **Ruční zadávání,** zadejte cesty balíčku `<folder name>/<project name>/<package name>.dtsx` `<folder name>/<environment name>`a prostředí z Databáze SSISDB přímo v následujících formátech: a .

   ![Nastavení vlastností na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Pokud jako umístění balíčku vyberete **systém souborů (balíček),** který se automaticky vybere, pokud byla vaše služba Azure-SSIS IR zřízena bez SSISDB, zadejte balíček, který chcete spustit, poskytnutím cesty UNC k souboru balíčku (`.dtsx`) v poli Cesta k **balíčku.** Například pokud ukládáte balíček v Azure Files, jeho cesta k balíčku je `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Pokud balíček nakonfigurujete v samostatném souboru, musíte`.dtsConfig`také zadat cestu UNC ke konfiguračnímu souboru ( ) v poli **Cesta konfigurace.** Pokud například uložíte konfiguraci v Azure Files, bude `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`jeho cesta konfigurace .

   ![Nastavení vlastností na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Pokud jako umístění balíčku vyberete **systém souborů (Projekt),** zadejte balíček, který`.ispac`chcete spustit, zadáním cesty UNC k souboru projektu ( ) v poli **Cesta projektu** a souborbalíčku (`.dtsx`) z projektu v poli Název **balíčku.** Pokud například uložíte projekt v Azure Files, `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`jeho cesta k projektu je .

   ![Nastavení vlastností na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Dále zadejte pověření pro přístup k projektu, balíček nebo konfigurační soubory. Pokud jste dříve zadali hodnoty pověření pro spuštění balíčku (viz předchozí), můžete je znovu použít zaškrtnutím políčka **Stejné jako pověření pro spuštění balíčku.** V opačném případě zadejte hodnoty přihlašovacích údajů pro přístup k balíčku do polí **Doména**, **Uživatelské jméno**a **Heslo.** Pokud například uložíte projekt, balíček nebo konfiguraci do `Azure`souborů Azure, `<storage account name>`doména je `<storage account key>`, uživatelské jméno je a heslo je . 

   Alternativně můžete použít tajné kódy uložené v trezoru klíčů jako jejich hodnoty (viz předchozí). Tato pověření se používají pro přístup k balíčku a podřízené balíčky v úlohy spustit balíček, všechny z jejich vlastní cesty nebo stejného projektu, stejně jako konfigurace, které zahrnují ty, které jsou zadány v balíčcích. 

   Pokud jako umístění balíčku vyberete **vložený balíček,** přetáhněte ho tak, aby byl spuštěn, nebo **ho nahrajte** ze složky souborů do dodanýho pole. Váš balíček bude automaticky komprimován a vložen do datové části aktivity. Po vložení si můžete balíček **stáhnout** později pro úpravy. Můžete také **Parametrizovat** vložený balíček přiřazením k parametru kanálu, který lze použít ve více aktivitách, a tím optimalizovat velikost datové části kanálu. Pokud váš vložený balíček není všechny šifrované a zjistíme použití spustit balíček úlohy v něm, bude automaticky zaškrtnuto políčko **Spustit úlohu balíčku** a příslušné podřízené balíčky s jejich odkazy na systém souborů budou automaticky přidány, abyste je také vložili. Pokud nemůžeme zjistit použití úlohy spustit balíček, budete muset ručně zaškrtnout políčko **Spustit úlohu balíčku** a přidat příslušné podřízené balíčky s odkazy na jejich systém souborů jeden po druhém, abyste je také vložit. Pokud podřízené balíčky používají odkazy na SQL Server, ujistěte se, že SQL Server je přístupný pomocí zařízení Azure-SSIS IR.  Použití odkazů na projekt pro podřízené balíčky není aktuálně podporováno.
   
   ![Nastavení vlastností na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Pokud jste při vytváření balíčku pomocí nástrojů SQL Server Data Tools použili úroveň ochrany **EncryptAllWithPassword** nebo **EncryptSensitiveWithPassword,** zadejte hodnotu hesla do pole **Šifrovací heslo.** Alternativně můžete použít tajný klíč uložený v trezoru klíčů jako jeho hodnotu (viz předchozí). Pokud jste použili úroveň ochrany **EncryptSensitiveWithUserKey,** zadejte znovu citlivé hodnoty v konfiguračních souborech nebo na **kartách Parametry SSIS**, **Správci připojení**nebo **Přepsání vlastností** (viz později). 

   Pokud jste použili úroveň ochrany **EncryptAllWithUserKey,** není podporována. Je třeba překonfigurovat balíček použít jinou úroveň ochrany `dtutil` prostřednictvím SQL Server Data Tools nebo nástroj příkazového řádku. 
   
   V **části Protokolování vyberte**předdefinovaný rozsah protokolování pro spuštění balíčku. Pokud chcete místo toho zadat vlastní název protokolování, zaškrtněte toto **políčko Vlastní** nastavení. Pokud chcete protokolovat spuštění balíčku nad rámec použití standardních zprostředkovatelů protokolu, které lze zadat v balíčku, zadejte složku protokolu poskytnutím jeho cesty UNC v poli **Protokolování cesty.** Pokud například ukládáte protokoly do souborů Azure, `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`je vaše cesta protokolování . Podsložka je vytvořena v této cestě pro každý jednotlivý balíček spustit a pojmenované po Spuštění SSIS balíček aktivity spustit ID, ve kterém jsou generovány soubory protokolu každých pět minut. 
   
   Nakonec zadejte pověření pro přístup ke složce protokolu. Pokud jste dříve zadali hodnoty přihlašovacích údajů k přístupu k balíčku (viz předchozí), můžete je znovu použít zaškrtnutím políčka **Stejné jako pověření pro přístup k balíčku.** V opačném případě zadejte hodnoty přihlašovacích údajů pro protokolování do polí **Doména**, **Uživatelské jméno**a **Heslo.** Pokud například ukládáte protokoly do souborů Azure, doména `Azure` `<storage account name>`je , uživatelské `<storage account key>`jméno je a heslo je . 

    Alternativně můžete použít tajné kódy uložené v trezoru klíčů jako jejich hodnoty (viz předchozí). Tato pověření se používají k ukládání protokolů. 
   
   Pro všechny dříve uvedené cesty UNC musí být plně kvalifikovaný název souboru menší než 260 znaků. Název adresáře musí mít méně než 248 znaků.

1. Na kartě **Parametry SSIS** pro aktivitu spustit balíček SSIS, pokud je spuštěna vaše zařízení Azure-SSIS IR, je jako umístění balíčku **vybrána služba SSISDB** a políčko **Ruční zadávání na** kartě **Nastavení** je zaškrtnuté, zobrazí se existující parametry SSIS ve vybraném projektu nebo balíčku z SSISDB, abyste jim přiřadili hodnoty. V opačném případě je můžete zadat jeden po druhém a přiřadit jim hodnoty ručně. Ujistěte se, že existují a jsou správně zadány pro spuštění balíčku úspěšné. 
   
   Pokud jste **použili encryptSensitiveWithUserKey** úroveň ochrany při vytváření balíčku přes SQL Server Datové nástroje a **souborový systém (Balíček)** nebo **souborový systém (Project)** je vybrán jako umístění balíčku, je také nutné znovu zadat citlivé parametry přiřadit hodnoty k nim v konfiguračních souborech nebo na této kartě. 
   
   Když přiřazujete hodnoty svým parametrům, můžete přidat dynamický obsah pomocí výrazů, funkcí, systémových proměnných data factory a parametrů nebo proměnných kanálu Data Factory. Alternativně můžete použít tajné kódy uložené v trezoru klíčů jako jejich hodnoty (viz předchozí).

   ![Nastavení vlastností na kartě Parametry SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Na kartě **Správce připojení** pro aktivitu spustit balíček SSIS, pokud je spuštěna služba Azure-SSIS IR, je jako umístění balíčku vybrána **služba SSISDB** a políčko **Ruční položky** na kartě **Nastavení** je zaškrtnuto, zobrazí se stávající správci připojení ve vybraném projektu nebo balíčku z Databáze SSISDB, abyste jim přiřadili hodnoty k jejich vlastnostem. V opačném případě je můžete zadat jeden po druhém a přiřadit hodnoty k jejich vlastnostem ručně. Ujistěte se, že existují a jsou správně zadány pro spuštění balíčku úspěšné. 
   
   Pokud jste **použili úroveň ochrany EncryptSensitiveWithUserKey** při vytváření balíčku prostřednictvím sql server datové nástroje a **systém souborů (balíček)** nebo **systém souborů (Project)** je vybrán jako umístění balíčku, je také nutné znovu zadat citlivé vlastnosti správce připojení přiřadit hodnoty k nim v konfiguračních souborech nebo na této kartě. 
   
   Když přiřazujete hodnoty vlastnostem správce připojení, můžete přidat dynamický obsah pomocí výrazů, funkcí, systémových proměnných data factory a parametrů nebo proměnných kanálu Data Factory. Alternativně můžete použít tajné kódy uložené v trezoru klíčů jako jejich hodnoty (viz předchozí).

   ![Nastavení vlastností na kartě Správce připojení](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. Na kartě **Přepsání vlastností** pro aktivitu Spouštět balíček SSIS zadejte cesty existujících vlastností ve vybraném balíčku jeden po druhém a přiřaďte jim hodnoty ručně. Ujistěte se, že existují a jsou správně zadány pro spuštění balíčku úspěšné. Chcete-li například přepsat hodnotu uživatelské proměnné, zadejte její `\Package.Variables[User::<variable name>].Value`cestu v následujícím formátu: . 
   
   Pokud jste **použili encryptSensitiveWithUserKey** úroveň ochrany při vytváření balíčku přes SQL Server Datové nástroje a **systém souborů (Balíček)** nebo **systém souborů (Project)** je vybrán jako umístění balíčku, je také nutné znovu zadat citlivé vlastnosti přiřadit hodnoty k nim v konfiguračních souborech nebo na této kartě. 
   
   Když přiřazujete hodnoty svým vlastnostem, můžete přidat dynamický obsah pomocí výrazů, funkcí, systémových proměnných datové továrny a parametrů nebo proměnných kanálu datové továrny.

   ![Nastavení vlastností na kartě Přepsání vlastností](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Hodnoty přiřazené v konfiguračních souborech a na kartě **Parametry SSIS** lze přepsat pomocí karet **Správci připojení** nebo **Přepsání vlastností.** Hodnoty přiřazené na kartě **Správci připojení** lze také přepsat pomocí karty **Přepsání vlastností.**

1. Chcete-li ověřit konfiguraci kanálu, vyberte na panelu nástrojů **možnost Ověřit.** Chcete-li zavřít **sestavu ověření kanálu**, vyberte položku **>>**.

1. Chcete-li publikovat kanál do datové továrny, vyberte **publikovat vše**. 

### <a name="run-the-pipeline"></a>Spuštění kanálu
V tomto kroku spustíte spuštění kanálu. 

1. Pokud chcete spustit kanál, vyberte na panelu nástrojů **možnost Aktivovat** a vyberte **Aktivovat nyní**. 

   ![Aktivovat](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. V okně **Spuštění kanálu** vyberte **Dokončit**. 

### <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se spuštění kanálu a jeho stav spolu s dalšími informacemi, jako je například čas **spuštění.** Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.

   ![Spuštění kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se pouze jedna aktivita spustit, protože kanál má pouze jednu aktivitu. Je to spustit aktivitu balíčku SSIS.

   ![Spuštění aktivit](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Spusťte následující dotaz proti databázi SSISDB na serveru SQL a ověřte, zda byl balíček proveden. 

   ```sql
   select * from catalog.executions
   ```

   ![Ověření spuštění balíčků](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Můžete také získat ID spuštění SSISDB z výstupu spuštění aktivity kanálu a pomocí ID zkontrolovat komplexnější protokoly spuštění a chybové zprávy v SQL Server Management Studio.

   ![Získejte ID popravy.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Naplánování kanálu pomocí aktivační události

Můžete také vytvořit naplánovanou aktivační událost pro váš kanál tak, aby kanál běží podle plánu, například každou hodinu nebo denně. Příklad najdete v [tématu Vytvoření datové továrny – ui datové továrny](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Spuštění balíčku s PowerShellem
V této části použijete Azure PowerShell k vytvoření kanálu datové továrny s aktivitou spouštění balíčku SSIS, která spouští váš balíček SSIS. 

Nainstalujte nejnovější moduly Azure PowerShellpodle podrobných pokynů v části [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Vytvoření datové továrny pomocí azure-SSIS IR
Můžete buď použít existující datovou továrnu, která už má Azure-SSIS IR zřízené nebo vytvořit novou datovou továrnu s Azure-SSIS IR. Postupujte podle podrobných pokynů v [kurzu: Nasazení balíčků SSIS do Azure přes PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou balíčku SSIS 
V tomto kroku vytvoříte kanál s aktivitou spouštění balíčku SSIS. Aktivita spustí balíček SSIS. 

1. Vytvořte soubor JSON s názvem *RunSSISPackagePipeline.json* ve složce *C:\ADF\RunSSISPackage* s obsahem podobným následujícímu příkladu.

   > [!IMPORTANT]
   > Před uložením souboru nahraďte názvy objektů, popisy a cesty, hodnoty vlastností nebo parametrů, hesla a další hodnoty proměnných. 
    
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

   Chcete-li spouštět balíčky uložené v systémech souborů, sdílených složek nebo soubory Azure, zadejte hodnoty pro balíček a log umístění vlastnosti takto:

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

   Chcete-li spouštět balíčky v rámci projektů uložených v systémech souborů, sdílených složek nebo soubory Azure, zadejte hodnoty pro vlastnost umístění balíčku následujícím způsobem:

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

   Chcete-li spustit vložené balíčky, zadejte hodnoty vlastnosti umístění balíčku následujícím způsobem:

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

2. V Prostředí Azure PowerShell přepněte do složky *C:\ADF\RunSSISPackage.*

3. Chcete-li vytvořit kanál **RunSSISPackagePipeline**, spusťte rutinu **Set-AzDataFactoryV2Pipeline.**

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
Ke spuštění kanálu použijte rutinu **Invoke-AzDataFactoryV2Pipeline.** Tato rutina vrací ID spuštění kanálu pro budoucí monitorování.

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

Kanál můžete taky monitorovat pomocí portálu Azure. Podrobné pokyny naleznete v tématu [Sledování kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Naplánování kanálu pomocí aktivační události
V předchozím kroku jste spustili kanál na vyžádání. Můžete také vytvořit aktivační událost plánu pro spuštění kanálu podle plánu, například každou hodinu nebo denně.

1. Vytvořte soubor JSON s názvem *MyTrigger.json* ve složce *C:\ADF\RunSSPACKAGE* s následujícím obsahem: 
        
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
    
1. V Prostředí Azure PowerShell přepněte do složky *C:\ADF\RunSSISPackage.*
1. Spusťte rutinu **Set-AzDataFactoryV2Trigger,** která vytvoří aktivační událost. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Ve výchozím nastavení je aktivační událost v zastaveném stavu. Spusťte aktivační událost spuštěním rutiny **Start-AzDataFactoryV2Trigger.** 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Zkontrolujte, zda je aktivační událost spuštěna spuštěním rutiny **Get-AzDataFactoryV2Trigger.** 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Spusťte následující příkaz po další hodině. Pokud je například aktuální čas 3:25 PM UTC, spusťte příkaz v 16:00 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Spusťte následující dotaz proti databázi SSISDB na serveru SQL a ověřte, zda byl balíček proveden. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Další kroky
Podívejte se na následující příspěvek na blogu:
- [Modernizace a rozšíření pracovních postupů ETL/ELT pomocí aktivit SSIS v kanálech Azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
