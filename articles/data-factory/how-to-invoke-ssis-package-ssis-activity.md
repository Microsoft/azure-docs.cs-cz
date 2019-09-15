---
title: Spuštění SSIS balíčku s aktivitou spuštění balíčku SSIS – Azure | Microsoft Docs
description: Tento článek popisuje, jak spustit balíček služba SSIS (SQL Server Integration Services) (SSIS) v kanálu Azure Data Factory pomocí aktivity spustit balíček SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 09/13/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9057cefa5108924c57dbc85bbb895b31e804a51c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000643"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Spusťte balíček SSIS s aktivitou spustit balíček SSIS v Azure Data Factory
Tento článek popisuje, jak spustit balíček služba SSIS (SQL Server Integration Services) (SSIS) v kanálu Azure Data Factory (ADF) pomocí aktivity balíčku Execute SSIS. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud už žádnou z těchto [kroků nepoužíváte, vytvořte Integration runtime Azure-SSIS (IR): Zřizování Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Spuštění balíčku v Azure Portal
V této části použijete uživatelské rozhraní (UI) ADF (/App) k vytvoření kanálu ADF s aktivitou spuštění balíčku SSIS, na které běží balíček SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou Execute SSIS Package
V tomto kroku vytvoříte kanál pomocí uživatelského rozhraní a aplikace ADF. Přidáte do kanálu aktivitu spustit balíček SSIS a nakonfigurujete ji tak, aby běžela balíček SSIS. 

1. Na stránce s přehledem nebo domovskou stránkou ADF v Azure Portal klikněte na dlaždici **autora & monitorování** a spusťte na samostatné kartě uživatelské rozhraní a aplikaci ADF. 

   ![Domovská stránka datové továrny](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na stránce **Začínáme** klikněte na **vytvořit kanál**: 

   ![Stránka Začínáme](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. Na panelu nástrojů **aktivity** rozbalte **obecné**a přetáhněte &AMP; aktivitu **spustit balíček SSIS** na plochu návrháře kanálu. 

   ![Přetáhněte aktivitu spustit balíček SSIS na plochu návrháře](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Na kartě **Obecné** u aktivity spustit SSIS balíček zadejte název a popis aktivity. Nastavte volitelné hodnoty timeout a opakování.

   ![Nastavení vlastností na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Na kartě **Nastavení** aktivity spustit balíček SSIS vyberte Azure-SSIS IR, ve kterém chcete balíček spustit. Pokud váš balíček používá ověřování systému Windows pro přístup k úložištím dat, například k serverům SQL nebo sdíleným složkám souborů v místním počítači, souborům Azure atd., zaškrtněte políčko **ověřování systému Windows** a zadejte hodnoty přihlašovacích údajů pro spuštění balíčku (**doména** **Uživatelské jméno a heslo).** // Alternativně můžete jako své hodnoty použít tajné kódy uložené v Azure Key Vault (integrace). Provedete to tak, že kliknete na zaškrtávací políčko **trezoru klíčů Azure** vedle příslušného přihlašovacích údajů, vyberete/upravíte existující propojenou službu integrace nebo vytvoříte novou a potom pro hodnotu přihlašovacích údajů vyberete název tajného kódu/verzi.  Když vytváříte nebo upravujete propojenou službu integrace, můžete vybrat nebo upravit existující integrace nebo vytvořit nové, ale udělte přístup k vašemu integrace spravované identitě ADF, pokud jste to ještě neudělali. Tajná klíčová okna můžete zadat také přímo v následujícím formátu `<AKV linked service name>/<secret name>/<secret version>`:. Pokud váš balíček potřebuje 32 spuštění runtime, zaškrtněte políčko **32 bitového** běhu. 

   Jako **umístění balíčku**vyberte **SSISDB**, **systém souborů (balíček)** nebo **systém souborů (projekt)** . Pokud jako umístění balíčku vyberete **SSISDB** , která se automaticky vybere v případě, že jste Azure-SSIS IR zřídili pomocí katalogu SSIS (SSISDB) hostovaného serverem Azure SQL Database Server/spravované instance, musíte určit, který balíček se má spustit. nasazeno do SSISDB. Pokud je váš Azure-SSIS IR spuštěný a políčko **Ruční Ruční zadání** je nezaškrtnuté, můžete procházet a vybírat existující složky/projekty/balíčky/prostředí z SSISDB. Kliknutím na tlačítko **aktualizovat** načtěte vaše nově přidané složky/projekty/balíčky/prostředí z SSISDB, aby byly dostupné pro procházení a výběr. Chcete-li procházet nebo vybrat prostředí pro spuštění balíčku, je nutné nakonfigurovat vaše projekty předem, aby byla tato prostředí přidána jako odkazy ze stejných složek v SSISDB. Další informace najdete v tématu [vytváření a mapování SSIS prostředí](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   V části **úroveň protokolování**vyberte předdefinovaný rozsah protokolování pro spuštění balíčku. Pokud chcete místo toho zadat vlastní název protokolování, zaškrtněte políčko přizpůsobené. 

   ![Nastavení vlastností na kartě nastavení – automaticky](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Pokud váš Azure-SSIS IR není spuštěný nebo je zaškrtnuté políčko **Ruční** zadání, můžete zadat cestu k balíčku a prostředí z SSISDB přímo v následujících formátech: `<folder name>/<project name>/<package name>.dtsx` a. `<folder name>/<environment name>`

   ![Nastavení vlastností na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Pokud jako umístění balíčku vyberete **systém souborů (balíček)** , který se automaticky vybere v případě, že jste Azure-SSIS IR zřídili bez SSISDB, musíte určit, který balíček se má spustit, zadáním cesty UNC (Universal Naming Convention) k vaší soubor balíčku (`.dtsx`) v **cestě k balíčku**. Pokud například uložíte balíček do souborů Azure, bude jeho cesta k `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`balíčku. 
   
   Pokud nakonfigurujete balíček v samostatném souboru, budete také muset zadat cestu UNC ke konfiguračnímu souboru (`.dtsConfig`) v **cestě konfigurace**. Pokud například uložíte konfiguraci do souborů Azure, bude `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`její cesta konfigurace.

   ![Nastavení vlastností na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Pokud jako umístění balíčku vyberete možnost **systém souborů (projekt)** , je nutné určit balíček, který se má spustit, zadáním cesty UNC k souboru projektu (`.ispac`) v cestě k **projektu** a souborem balíčku (`.dtsx`) z projektu v  **Název balíčku** Pokud například uložíte projekt do souborů Azure, bude `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`jeho cesta k projektu.

   ![Nastavení vlastností na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Dále je nutné zadat přihlašovací údaje pro přístup k souborům projektu, balíčku nebo konfiguračního souboru. Pokud jste dříve zadali hodnoty přihlašovacích údajů pro spuštění balíčku (viz výše), můžete je znovu použít zaškrtnutím políčka **stejné jako pověření pro spuštění balíčku** . V opačném případě musíte zadat hodnoty pro přihlašovací údaje k balíčku (**heslo**k**uživatelskému jménu**/**domény**/). Pokud například uložíte projekt/balíček/konfiguraci do služby soubory Azure, **doména** `Azure`je, **uživatelské jméno** `<storage account name>`a **heslo** `<storage account key>`. Alternativně můžete použít tajné klíče uložené ve vašich integrace jako jejich hodnoty (viz výše). Tyto přihlašovací údaje budou použity pro přístup k balíčku a podřízeným balíčkům v úloze spustit balíček, a to vše z vlastní cesty/stejného projektu i konfigurací, včetně těch, které jsou zadány v balíčcích. 
   
   Pokud jste při vytváření balíčku pomocí nástroje SQL Server Data Tools (SSDT) použili úroveň ochrany**EncryptSensitiveWithPassword** **EncryptAllWithPassword**/, musíte v šifrování zadat hodnotu pro heslo.  **heslo**. Alternativně můžete jako svou hodnotu použít tajný kód uložený ve vašem integrace (viz výše). Pokud jste použili úroveň ochrany **EncryptSensitiveWithUserKey** , je nutné znovu zadat citlivé hodnoty do konfiguračních souborů/nebo do vlastnosti**správce**/připojení **parametrů SSIS**. **Potlačí** záložky (viz níže). Pokud jste použili úroveň ochrany **EncryptAllWithUserKey** , není tato úroveň podporovaná, takže budete muset balíček překonfigurovat tak, aby používal jinou úroveň ochrany prostřednictvím SSDT nebo `dtutil` nástroje příkazového řádku. 
   
   V části **úroveň protokolování**vyberte předdefinovaný rozsah protokolování pro spuštění balíčku. Pokud chcete místo toho zadat vlastní název protokolování, zaškrtněte políčko přizpůsobené. Pokud chcete protokolovat provádění balíčků mimo použití standardních zprostředkovatelů protokolů, které je možné zadat v balíčku, musíte zadat cestu UNC v **cestě k protokolování**zadáním cesty UNC. Pokud například uložíte protokoly do souborů Azure, vaše cesta protokolování bude `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. V této cestě se vytvoří podsložka pro každý jednotlivý balíček, který se pojmenuje po spuštění s ID spuštění aktivity balíčku SSIS, ve kterém se soubory protokolů generují každých pět minut. 
   
   Nakonec musíte zadat přihlašovací údaje pro přístup do složky protokolu. Pokud jste dříve zadali hodnoty přihlašovacích údajů k balíčku (viz výše), můžete je znovu použít zaškrtnutím políčka **stejné jako přístupové přihlašovací údaje balíčku** . V opačném případě musíte zadat hodnoty přihlašovacích údajů pro přístup k přihlášení (**heslo**k**doméně**//). Pokud například uložíte protokoly do souborů Azure, **doména** `Azure`je; **uživatelské jméno** `<storage account name>` `<storage account key>`je a **heslo** . Alternativně můžete použít tajné klíče uložené ve vašich integrace jako jejich hodnoty (viz výše). Tyto přihlašovací údaje budou použity k ukládání protokolů. 
   
   U všech výše uvedených cest UNC musí mít plně kvalifikovaný název souboru méně než 260 znaků a název adresáře musí být kratší než 248 znaků.

5. Na kartě **parametry SSIS** pro aktivitu spustit balíček SSIS, pokud je spuštěný Azure-SSIS IR, jako umístění balíčku se vybere **SSISDB** a na kartě **Nastavení** není zaškrtnuté políčko **ručně zadané položky** . pro přiřazení hodnot k těmto parametrům se zobrazí parametry ve vybraném projektu nebo balíčku z SSISDB. V opačném případě je můžete zadat jednu po sobě, aby k nim bylo možné ručně přiřadit hodnoty – Ujistěte se prosím, že existují a jsou správně zadané pro spuštění balíčku, aby bylo úspěšné. 
   
   Pokud jste při vytváření balíčku používali úroveň ochrany **EncryptSensitiveWithUserKey** prostřednictvím SSDT a systém souborů **(** /balíček) je jako umístění balíčku vybraný **(projekt)** , musíte ho taky znovu zadat. vaše citlivé parametry k přiřazení hodnot do konfiguračních souborů nebo na této kartě. 
   
   Při přiřazování hodnot k vašim parametrům můžete přidat dynamický obsah pomocí výrazů, funkcí, systémových proměnných ADF a parametrů kanálu nebo proměnných kanálu ADF. Alternativně můžete použít tajné klíče uložené ve vašich integrace jako jejich hodnoty (viz výše).

   ![Nastavení vlastností na kartě Parametry SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Na kartě **Správci připojení** pro spuštění aktivity balíčku SSIS, pokud je vaše Azure-SSIS IR spuštěná, jako umístění balíčku se vybere **SSISDB** a políčko **Ruční Ruční zadání** na kartě **Nastavení** není zaškrtnuté, existující Správci připojení ve vybraném projektu nebo balíčku z SSISDB se zobrazí pro přiřazení hodnot ke svým vlastnostem. V opačném případě je můžete zadat jeden po sobě a přiřadit hodnoty jejich vlastnostem ručně – ověřte, zda existují a zda jsou správně zadány pro spuštění balíčku, aby bylo úspěšné. 
   
   Pokud jste při vytváření balíčku používali úroveň ochrany **EncryptSensitiveWithUserKey** prostřednictvím SSDT a systém souborů **(** /balíček) je jako umístění balíčku vybraný **(projekt)** , musíte ho taky znovu zadat. vlastnosti vašeho citlivého Správce připojení, které jim přiřadí hodnoty v konfiguračních souborech nebo na této kartě. 
   
   Při přiřazování hodnot k vlastnostem Správce připojení můžete přidat dynamický obsah pomocí výrazů, funkcí, systémových proměnných ADF a parametrů kanálu nebo proměnných kanálu ADF. Alternativně můžete použít tajné klíče uložené ve vašich integrace jako jejich hodnoty (viz výše).

   ![Nastavení vlastností na kartě Správci připojení](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Na kartě **přepsání vlastností** u aktivity spustit balíček SSIS můžete zadat cesty existujících vlastností ve vybraném balíčku jeden po jednom, aby byly hodnoty přiřazovat ručně – ověřte, zda existují a jsou správně zadány pro váš balíček. spuštění proběhlo úspěšně, například pro přepsání hodnoty proměnné uživatele, zadejte cestu v následujícím formátu: `\Package.Variables[User::<variable name>].Value`. 
   
   Pokud jste při vytváření balíčku používali úroveň ochrany **EncryptSensitiveWithUserKey** prostřednictvím SSDT a systém souborů **(** /balíček) je jako umístění balíčku vybraný **(projekt)** , musíte ho taky znovu zadat. vaše citlivé vlastnosti, které jim přiřadí hodnoty v konfiguračních souborech nebo na této kartě. 
   
   Při přiřazování hodnot k vašim vlastnostem můžete přidat dynamický obsah pomocí výrazů, funkcí, systémových proměnných ADF a parametrů kanálu nebo proměnných kanálu ADF.

   ![Nastavte vlastnosti na kartě přepsání vlastností.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Hodnoty přiřazené v konfiguračních souborech a na kartě *parametry SSIS* se dají přepsat pomocí vlastností **Správci**/připojení. záložky se**přepíší** , ale přiřazené na kartě **Správci připojení** můžou. přepsalo se taky pomocí karty **vlastnosti Overrides** .

8. Pokud chcete ověřit konfiguraci kanálu, klikněte na **ověřit** na panelu nástrojů. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>** .

9. Publikování kanálu do ADF kliknutím na tlačítko **publikovat vše** . 

### <a name="run-the-pipeline"></a>Spuštění kanálu
V tomto kroku aktivujete spuštění kanálu. 

1. Pokud chcete aktivovat spuštění kanálu, klikněte na **aktivační událost** na panelu nástrojů a pak klikněte na **aktivovat**. 

   ![Aktivovat](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. V okně **Spuštění kanálu** vyberte **Dokončit**. 

### <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Zobrazí se spuštění kanálu a jeho stav spolu s dalšími informacemi (například počáteční čas spuštění). Pokud chcete zobrazení aktualizovat, klikněte na **Aktualizovat**.

   ![Spuštění kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se pouze jedno spuštění aktivity, protože kanál má pouze jednu aktivitu (aktivita vykonání balíčku SSIS).

   ![Spuštění aktivit](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Můžete spustit následující **dotaz** pro databázi SSISDB na serveru SQL Azure a ověřit, zda byl balíček spuštěn. 

   ```sql
   select * from catalog.executions
   ```

   ![Ověření provedení balíčku](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. ID spuštění SSISDB můžete také získat z výstupu spuštění aktivity kanálu a pomocí ID zkontrolujete komplexnější protokoly spouštění a chybové zprávy v SQL Server Management Studio (SSMS).

   ![Získá ID spuštění.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Naplánování kanálu pomocí triggeru

Pro svůj kanál můžete také vytvořit plánovanou aktivační událost, aby se kanál spouštěl podle plánu (každou hodinu, každý den atd.). Příklad najdete v tématu [Vytvoření datové továrny – Data Factory uživatelského rozhraní](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Spuštění balíčku pomocí prostředí PowerShell
V této části použijete Azure PowerShell k vytvoření kanálu ADF s aktivitou spustit SSIS balíčku, která spouští balíček SSIS. 

Pomocí podrobných pokynů, [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-az-ps), nainstalujte nejnovější Azure PowerShell moduly.

### <a name="create-an-adf-with-azure-ssis-ir"></a>Vytvoření ADF pomocí Azure – SSIS IR
Můžete buď použít stávající ADF, na kterém už je Azure-SSIS IR zřízená, nebo vytvořit nový ADF pomocí Azure-SSIS IR podle podrobných pokynů v tomto [kurzu: Nasaďte balíčky SSIS do Azure přes](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)PowerShell.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou Execute SSIS Package 
V tomto kroku vytvoříte kanál s aktivitou spustit SSIS s balíčkem. Aktivita spustí balíček SSIS. 

1. Ve složce **C:\ADF\RunSSISPackage** vytvořte soubor JSON s názvem **RunSSISPackagePipeline. JSON** s obsahem podobným následujícímu příkladu:

   > [!IMPORTANT]
   > Před uložením souboru nahraďte názvy objektů, popisy/cesty, hodnoty vlastností nebo parametrů, hesla a další hodnoty proměnných. 

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

   Pokud chcete spouštět balíčky uložené v souborové systémy/sdílené složky/soubory Azure, můžete zadat hodnoty pro vlastnosti umístění balíčku nebo protokolu následujícím způsobem.

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

   Pokud chcete spouštět balíčky v projektech uložených v souborové systémy/sdílené složky/soubory Azure, můžete zadat hodnoty pro vlastnost umístění balíčku následujícím způsobem.

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

2. V Azure PowerShell přepněte do `C:\ADF\RunSSISPackage` složky.

3. Pokud chcete vytvořit kanál **RunSSISPackagePipeline**, spusťte rutinu **set-AzDataFactoryV2Pipeline** .

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Tady je ukázkový výstup:

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

Spusťte následující skript PowerShellu, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud nedokončí kopírování dat. Zkopírujte/vložte následující skript v okně PowerShellu a stiskněte klávesu Enter. 

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
V předchozím kroku jste kanál spustili na vyžádání. Můžete také vytvořit aktivační proceduru Schedule pro spuštění kanálu podle plánu (každou hodinu, každý den atd.).

1. Ve složce **C:\ADF\RunSSISPackage** vytvořte soubor JSON s názvem **MyTrigger. JSON** s následujícím obsahem: 

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
2. V **Azure PowerShell**přepněte do složky **C:\ADF\RunSSISPackage** .
3. Spusťte rutinu **set-AzDataFactoryV2Trigger** , která vytvoří Trigger. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Ve výchozím nastavení je aktivační událost v zastaveném stavu. Spusťte Trigger spuštěním rutiny **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Spuštěním rutiny **Get-AzDataFactoryV2Trigger** ověřte, že je aktivační událost spuštěná. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Po další hodiny spusťte následující příkaz. Pokud je například aktuální čas 3:25. UTC, spusťte příkaz ve 4. UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Můžete spustit následující dotaz pro databázi SSISDB na serveru SQL Azure a ověřit, zda byl balíček spuštěn. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Další kroky
Podívejte se na tento příspěvek na blogu:
-   [Modernizovat a rozšíří pracovní postupy ETL/ELT s aktivitami SSIS v kanálech ADF.](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
