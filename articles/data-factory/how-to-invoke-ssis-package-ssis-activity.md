---
title: Spouštění balíčků služby SSIS pomocí aktivity spustit balíčků služby SSIS - Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak spouštět balíčků SQL Server Integration Services (SSIS) v kanálu Azure Data Factory pomocí aktivity spustit balíčků služby SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f33259fff17633cc4864a342609f747ebb9902ba
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484838"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Spouštění balíčků služby SSIS pomocí aktivity spustit balíčků služby SSIS v Azure Data Factory
Tento článek popisuje, jak spouštět balíčků SQL Server Integration Services (SSIS) v kanálu Azure Data Factory (ADF) s využitím aktivity spuštění balíčku služby SSIS. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud nemáte již podle podrobných pokynů v, vytvořte prostředí Azure-SSIS Integration Runtime (IR) [kurzu: Zřízení prostředí Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Spuštění balíčku na webu Azure Portal
V této části použijete ADF uživatelské rozhraní (UI) nebo aplikaci k vytvoření ADF kanálu s aktivitou spuštění balíčku služby SSIS, na kterém běží váš balíček služby SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou spuštění balíčku služby SSIS
V tomto kroku použijete k vytvoření kanálu ADF uživatelského rozhraní nebo aplikaci. Přidat aktivitu spustit balíčků SSIS do tohoto kanálu a jeho spouštění balíčku služby SSIS nakonfigurovat. 

1. Na stránce Přehled Domů ADF na webu Azure portal, klikněte na **vytvořit a monitorovat** dlaždice pro spuštění na samostatné kartě uživatelské rozhraní ADF/aplikace. 

   ![Domovská stránka datové továrny](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na **pusťme se do práce** klikněte na **vytvořit kanál**: 

   ![Stránka Začínáme](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. V **aktivity** sady nástrojů, rozbalte **Obecné**, pak přetažením **spuštění balíčku služby SSIS** aktivity na plochu návrháře kanálu. 

   ![Přetáhněte aktivitu spustit balíčků služby SSIS na plochu návrháře](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Na **Obecné** kartu pro aktivity spuštění balíčku služby SSIS, zadejte název a popis aktivity. Nastavit volitelný časový limit a hodnoty opakování.

   ![Nastavte vlastnosti na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Na **nastavení** kartu pro aktivity spuštění balíčku služby SSIS, vyberte Azure-SSIS IR, ve kterém chcete spustit vašeho balíčku. Pokud váš balíček používá ověřování Windows pro přístup k úložišti dat, například SQL servery pro/sdílené složky v místním prostředí, soubory Azure, atd., zkontrolujte **ověřování Windows** zaškrtávací políčko a zadejte hodnoty pro vaše přihlašovací údaje spuštění balíčku (**Domény**/**uživatelské jméno**/**heslo**). Alternativně můžete použít tajnými kódy uloženými v trezoru klíč Azure (AKV) jako jejich hodnoty. Pokud chcete udělat, klikněte na **AZURE KEY VAULT** zaškrtávací políčko vedle příslušné přihlašovací údaje, vyberte nebo upravit existující AKV propojené služby nebo vytvořte novou a pak vyberte verzi tajného klíče název a hodnotu přihlašovacích údajů.  Když vám vytvořit či upravit AKV propojené služby, můžete vybrat nebo upravit existující AKV nebo vytvořte novou, ale prosím udělit přístup identit ADF spravovaných vaší službou AZURE, pokud jste tak již neučinili. Můžete také zadat tajné klíče přímo v následujícím formátu: `<AKV linked service name>/<secret name>/<secret version>`. Pokud váš balíček potřebuje 32bitový modul runtime pro spuštění, zkontrolujte, **32bitový modul runtime** zaškrtávací políčko. 

   Pro **balíček umístění**vyberte **SSISDB**, **systému souborů (balíček)** , nebo **systému souborů (projekt)** . Pokud vyberete **SSISDB** jako umístění balíčku, který je automaticky vybrána, pokud prostředí Azure-SSIS IR a byla opatřena katalogu služby SSIS (SSISDB) hostitelem serveru Azure SQL Database nebo spravované Instance, je třeba zadat váš balíček ke spuštění, který je nasazený do databáze SSISDB. Pokud běží v prostředí Azure-SSIS IR a **ruční položky** zaškrtávací políčko je zaškrtnuté políčko, můžete vyhledat a vybrat existující složky/projektů/balíčků nebo prostředí SSISDB. Klikněte na tlačítko **aktualizovat** tlačítko načíst nově přidané složky/projektů/balíčků nebo prostředí z SSISDB, takže jsou k dispozici pro procházení a výběr. 
   
   Pro **úroveň protokolování**, vyberte předdefinovaný obor protokolování pro spouštění vašeho balíčku. Zkontrolujte, **vlastní** zaškrtávací políčko, pokud chcete místo toho zadejte název vaší vlastní protokolování. 

   ![Nastavte vlastnosti na kartě nastavení - automaticky](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Pokud prostředí Azure-SSIS IR není spuštěný nebo **ruční položky** je zaškrtnuté políčko, můžete zadat balíček a prostředí cesty z databáze SSISDB přímo v následujících formátech: `<folder name>/<project name>/<package name>.dtsx` a `<folder name>/<environment name>`.

   ![Nastavte vlastnosti na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Pokud vyberete **systému souborů (balíček)** jako umístění balíčku, který je automaticky vybrána, pokud prostředí Azure-SSIS IR byl zřízený bez SSISDB, je třeba zadat váš balíček ke spuštění zadáním (Universal Naming Convention Cesta UNC) k souboru balíčku (`.dtsx`) v **cesta balíčku**. Například pokud ukládáte váš balíček ve službě soubory Azure, jeho cesta k balíčku bude `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Pokud nakonfigurujete v samostatném souboru balíčku, musíte také zadat cestu UNC do konfiguračního souboru (`.dtsConfig`) v **cesta ke konfiguračnímu**. Například pokud ukládáte konfiguraci ve službě soubory Azure, její konfigurace cesta bude `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Nastavte vlastnosti na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Pokud vyberete **systému souborů (projekt)** jako umístění balíčku, musíte zadat balíček ke spuštění zadáním cestu UNC k souboru projektu (`.ispac`) v **cesta k projektu** a balíčku souboru () `.dtsx`) z projektu v **název balíčku**. Například pokud ukládáte váš projekt ve službě soubory Azure, jeho cesta k projektu bude `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Nastavte vlastnosti na kartě Nastavení – ruční](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Dále je třeba zadat přihlašovací údaje pro přístup k souborům projektu/balíčku/konfigurace. Pokud jste dříve zadali hodnoty zadání přihlašovacích údajů spuštění balíčku (viz výše), můžete znovu použít, je kontrola **stejná hodnota jako balíček provádění pověření** zaškrtávací políčko. V opačném případě budete muset zadat hodnoty pro svoje přihlašovací údaje přístup k balíčku (**domény**/**uživatelské jméno**/**heslo**). Pokud ukládáte/balíčku/konfigurace projektu ve službě soubory Azure, například **domény** je `Azure`; **uživatelské jméno** je `<storage account name>`; a **heslo**je `<storage account key>`. Alternativně můžete použít tajnými kódy uloženými v vaší službou AZURE jako jejich hodnoty (viz výše). Tyto přihlašovací údaje se použijí pro přístup k balíčku a podřízené balíčky v balíčku provést úkol, vše z vlastní cesta/stejného projektu, jakož i konfigurace, včetně těch, které zadaný ve vašich balíčcích. 
   
   Pokud jste použili **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** ochrany úrovni při vytváření balíčku přes SQL Server Data Tools (SSDT), musíte zadat hodnotu pro vaše heslo **šifrovací heslo**. Alternativně můžete použít tajného klíče uložené v vaší službou AZURE jako svou hodnotu (viz výše). Pokud jste použili **EncryptSensitiveWithUserKey** úroveň ochrany, budete muset znovu zadat vaše citlivé hodnoty v nebo v konfiguračních souborech **SSIS parametry** /  **Správce připojení**/**přepíše vlastnost** karty (viz níže). Pokud jste použili **EncryptAllWithUserKey** úroveň ochrany, nepodporovaný, takže je potřeba překonfigurovat vašeho balíčku používat další úroveň ochrany pomocí SSDT nebo `dtutil` nástroj příkazového řádku. 
   
   Pro **úroveň protokolování**, vyberte předdefinovaný obor protokolování pro spouštění vašeho balíčku. Zkontrolujte, **vlastní** zaškrtávací políčko, pokud chcete místo toho zadejte název vaší vlastní protokolování. Pokud chcete protokolovat vašich spouštění balíčku nad rámec pomocí standardního protokolu zprostředkovatelů, které se dá nastavit v balíčku, budete muset určit zadáním jeho cestu UNC ve složce protokolu **cesta pro protokolování**. Například pokud ukládáte protokolů ve službě soubory Azure, cestu k protokolování bude `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Podsložku se vytvoří v této cestě pro každé spuštění jednotlivých balíčků a s názvem po spuštění aktivity spustit balíčků služby SSIS ID, ve kterém se soubory protokolu nevygeneruje každých pět minut. 
   
   A konečně také musíte zadat přihlašovací údaje pro přístup do složky protokolů. Pokud jste dříve zadali hodnoty pro váš balíček přihlašovacích údajů pro přístup (viz výše), můžete znovu použít, je kontrola **stejná hodnota jako balíček přihlašovacích údajů pro přístup** zaškrtávací políčko. V opačném případě budete muset zadat hodnoty pro přihlašovací údaje pro přístup k vaší protokolování (**domény**/**uživatelské jméno**/**heslo**). Pokud ukládáte protokolů ve službě soubory Azure, například **domény** je `Azure`; **uživatelské jméno** je `<storage account name>`; a **heslo** je `<storage account key>`. Alternativně můžete použít tajnými kódy uloženými v vaší službou AZURE jako jejich hodnoty (viz výše). Tyto přihlašovací údaje se použijí k ukládání protokolů. 
   
   U všech cest UNC uvedených výše plně kvalifikovaný název musí být kratší než 260 znaků a název adresáře musí být kratší než 248 znaků.

5. Na **SSIS parametry** kartu pro spuštění balíčku služby SSIS aktivitu, pokud je spuštěný Azure-SSIS IR, **SSISDB** je zvolen jako umístění balíčku a **ruční položky** zaškrtávací políčko na **nastavení** je karta není zaškrtnuto, zobrazí se existující parametry služby SSIS v vybraný projekt/balíčku z databáze SSISDB přiřazení hodnoty k nim. V opačném případě můžete zadat jejich jeden po druhém ručně přiřadit hodnoty k nim – Ujistěte se prosím, že existují a jsou správně zadány pro spouštění balíčku proběhla úspěšně. 
   
   Pokud jste použili **EncryptSensitiveWithUserKey** úroveň ochrany při vytváření balíčku pomocí SSDT a **systému souborů (balíček)** /**systému souborů (projekt)** je vybrán jako umístění balíčku, budete také muset znovu zadat citlivé parametry k přiřazení hodnot k jejich v konfiguračních souborech nebo na této kartě. 
   
   Při přiřazování hodnot pro parametry, můžete přidat dynamický obsah pomocí výrazů, funkce, ADF systémové proměnné a proměnné parametrů kanálu ADF. Alternativně můžete použít tajnými kódy uloženými v vaší službou AZURE jako jejich hodnoty (viz výše).

   ![Nastavte vlastnosti na kartě Parametry služby SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Na **připojení správci** kartu pro spuštění balíčku služby SSIS aktivitu, pokud je spuštěný Azure-SSIS IR, **SSISDB** je zvolen jako umístění balíčku a **ruční položky**zaškrtávací políčko na **nastavení** je karta není zaškrtnuto, zobrazí se existující připojení správci v vybraný projekt/balíčku z databáze SSISDB přiřazení hodnot k jejich vlastností. V opačném případě můžete zadat jejich jeden po druhém ruční přiřazení hodnot k jejich vlastností – Ujistěte se prosím, že existují a jsou správně zadány pro spouštění balíčku proběhla úspěšně. 
   
   Pokud jste použili **EncryptSensitiveWithUserKey** úroveň ochrany při vytváření balíčku pomocí SSDT a **systému souborů (balíček)** /**systému souborů (projekt)** je vybrán jako umístění balíčku, budete také muset znovu zadat vlastnosti svého citlivé připojení správce k přiřazení hodnot k jejich v konfiguračních souborech nebo na této kartě. 
   
   Při přiřazení hodnoty k vlastnosti svého správce připojení, můžete přidat dynamický obsah pomocí výrazů, funkce, ADF systémové proměnné a proměnné parametrů kanálu ADF. Alternativně můžete použít tajnými kódy uloženými v vaší službou AZURE jako jejich hodnoty (viz výše).

   ![Nastavte vlastnosti na kartě Správce připojení](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Na **přepíše vlastnost** kartu pro aktivity spuštění balíčku služby SSIS, můžete zadat cesty existujících vlastností do vybraného balíčku jednu po druhé ručně přiřadit hodnoty k nim – Ujistěte se prosím, že existují a jsou správně zadaný pro spouštění balíčku úspěšné, například chcete-li přepsat hodnotu proměnné uživatele, zadejte jeho cesty v následujícím formátu: `\Package.Variables[User::<variable name>].Value`. 
   
   Pokud jste použili **EncryptSensitiveWithUserKey** úroveň ochrany při vytváření balíčku pomocí SSDT a **systému souborů (balíček)** /**systému souborů (projekt)** je vybrán jako umístění balíčku, budete také muset znovu zadat vaše citlivé vlastnosti k přiřazení hodnot k jejich v konfiguračních souborech nebo na této kartě. 
   
   Při přiřazování hodnot vlastností, můžete přidat dynamický obsah pomocí výrazů, funkce, ADF systémové proměnné a proměnné parametrů kanálu ADF.

   ![Nastavte vlastnosti na kartě Vlastnosti přepsání](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Hodnoty přiřazené v konfiguračních souborech a *SSIS parametry* kartu lze přepsat pomocí **připojení správci**/**přepíše vlastnost** karty, zatímco přiřazených na **připojení správci** kartu můžete přepsat také pomocí **přepíše vlastnost** kartu.

8. Chcete-li ověřit konfiguraci kanálu, klikněte na tlačítko **ověřit** na panelu nástrojů. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>** .

9. Publikování kanálu ADF kliknutím **Publikovat vše** tlačítko. 

### <a name="run-the-pipeline"></a>Spuštění kanálu
V tomto kroku aktivujete spuštění kanálu. 

1. Aktivace spuštění kanálu, klikněte na tlačítko **aktivační událost** na panelu nástrojů a klikněte na tlačítko **aktivovat**. 

   ![Aktivovat](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. V okně **Spuštění kanálu** vyberte **Dokončit**. 

### <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Se zobrazí spuštění kanálu a jeho stav spolu s dalšími informacemi (jako je čas začátku spuštění). Pokud chcete zobrazení aktualizovat, klikněte na **Aktualizovat**.

   ![Spuštění kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se pouze jednu aktivitu spustit, protože tento kanál obsahuje pouze jednu aktivitu (aktivita spustit balíčků služby SSIS).

   ![Spuštění aktivit](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Spuštěním následujícího **dotazu** proti databáze SSISDB databáze na serveru služby Azure SQL a ověřte, že balíček provedeny. 

   ```sql
   select * from catalog.executions
   ```

   ![Ověřte spuštění balíčku](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Můžete také získat ID spuštění SSISDB z výstupu aktivity spuštění kanálu a použijte ID komplexnější protokoly spuštění a chybových zpráv v serveru SQL Server Management Studio (SSMS).

   ![Získat ID spuštění.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Naplánovat kanál pomocí triggeru

Můžete také vytvořit naplánovanou aktivační událost pro svůj kanál tak, aby kanál se spouští podle plánu (každou hodinu, každý den atd.). Příklad najdete v tématu [vytvoření datové továrny – uživatelské rozhraní služby Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Spusťte balíček pomocí Powershellu
V této části použijete Azure PowerShell k vytvoření ADF kanálu s aktivitou spuštění balíčku služby SSIS, na kterém běží váš balíček služby SSIS. 

Nainstalujte nejnovější moduly Azure Powershellu podle podrobných pokynů v [instalace a konfigurace Azure Powershellu](/powershell/azure/install-az-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Vytvoření ADF pomocí prostředí Azure-SSIS IR
Můžete použít existující ADF, který už má zřízení Azure-SSIS IR nebo vytvořit nové ADF pomocí podrobných pokynů v Azure-SSIS IR [kurzu: Nasazení balíčků SSIS do Azure pomocí Powershellu](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou spuštění balíčku služby SSIS 
V tomto kroku vytvoříte kanál s aktivitou spuštění balíčku služby SSIS. Spuštění aktivit vašeho balíčku služby SSIS. 

1. Vytvořte soubor JSON s názvem **RunSSISPackagePipeline.json** v **C:\ADF\RunSSISPackage** složku s obsahem, podobně jako v následujícím příkladu:

   > [!IMPORTANT]
   > Před uložením souboru nahraďte názvy objektů, popisy a cesty, vlastnosti a hodnoty parametrů, hesla a další hodnoty proměnných. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "mySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "myAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyDomain",
                       "userName": "MyUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "**********"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyPipelineParameter",
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
                               "secretName": "MySecret"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "abc"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "userName": {
                               "value": {
                                   "value": "@pipeline().parameters.MyUsername",
                                   "type": "Expression"
                               }
                           },
                           "passWord": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyPassword",
                                   "secretVersion": "3a1b74e361bf4ef4a00e47053b872149"
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

2. V prostředí Azure PowerShell, přepněte `C:\ADF\RunSSISPackage` složky.

3. Vytvořte kanál **RunSSISPackagePipeline**, spusťte **Set-AzDataFactoryV2Pipeline** rutiny.

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
Použití **Invoke-AzDataFactoryV2Pipeline** rutiny pro spuštění kanálu. Tato rutina vrací ID spuštění kanálu pro budoucí monitorování.

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

Také můžete monitorovat kanál pomocí webu Azure portal. Podrobné pokyny najdete v tématu [monitorování kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Naplánovat kanál pomocí triggeru
V předchozím kroku jste spustili kanálu na vyžádání. Můžete také vytvořit aktivační událost plánovače pro spuštění kanálu podle plánu (každou hodinu, každý den atd.).

1. Vytvořte soubor JSON s názvem **MyTrigger.json** v **C:\ADF\RunSSISPackage** složka s následujícím obsahem: 

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
2. V **prostředí Azure PowerShell**, přepněte **C:\ADF\RunSSISPackage** složky.
3. Spustit **Set-AzDataFactoryV2Trigger** rutiny, která vytvoří aktivační událost. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Ve výchozím nastavení trigger je v zastaveném stavu. Spusťte trigger spuštěním **Start AzDataFactoryV2Trigger** rutiny. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Potvrďte, že aktivační událost se spouští spuštěním **Get-AzDataFactoryV2Trigger** rutiny. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Spusťte následující příkaz po do příští hodiny. Například pokud je aktuální čas UTC času 15:25, spusťte příkaz v 16: 00 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Spuštěním následujícího dotazu proti databázi SSISDB na serveru služby Azure SQL a ověřte, že balíček provedeny. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Další postup
Najdete v následujícím blogovém příspěvku:
-   [Modernizace a Rozšiřte své pracovní postupy ETL/ELT pomocí služby SSIS aktivit v kanálech ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
