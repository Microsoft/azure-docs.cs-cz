---
title: Řešení potíží s Azure Data Factory
description: Přečtěte si, jak řešit potíže s externími ovládacími prvky v Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 66590e40ff2440a166310ec3d57026f5a2cdbd1d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676848"
---
# <a name="troubleshoot-azure-data-factory"></a>Řešení potíží s Azure Data Factory

Tento článek popisuje běžné metody řešení potíží pro aktivity externích řízení v Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Aktivita konektoru a kopírování

V případě problémů s konektorem, například Chyba při použití aktivity kopírování, najdete informace v tématu [řešení potíží s Azure Data Factory konektory](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Kód chyby: 3200

- **Zpráva**: Chyba 403.

- **Příčina**: `The Databricks access token has expired.`

- **Doporučení**: ve výchozím nastavení je přístupový token Azure Databricks platný po dobu 90 dnů. Vytvořte nový token a aktualizujte propojenou službu.


### <a name="error-code--3201"></a>Kód chyby: 3201

- **Zpráva**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Příčina**: `Bad authoring: Notebook path not specified correctly.`

- **Doporučení**: zadejte cestu k poznámkovému bloku v aktivitě datacihly.

<br/>

- **Zpráva**: `Cluster   ... does not exist.`

- **Příčina**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Doporučení**: Ověřte, zda existuje cluster datacihly.

<br/>

- **Zpráva**: `Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **Příčina**: `Bad authoring.`

- **Doporučení**: Zadejte buď absolutní cesty pro schémata adresování pracovních prostorů, nebo `dbfs:/folder/subfolder/foo.py` pro soubory uložené v systému souborů datacihly.

<br/>

- **Zpráva**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Příčina**: `Bad authoring.`

- **Doporučení**: Ověřte [definici propojené služby](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Zpráva**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Příčina**: `Bad authoring.`

- **Doporučení**: Ověřte [definici propojené služby](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Zpráva**: `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Příčina**: `Bad authoring.`

- **Doporučení**: Přečtěte si chybovou zprávu. 

<br/>

### <a name="error-code--3202"></a>Kód chyby: 3202

- **Zpráva**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Příčina**: `Too many Databricks runs in an hour.`

- **Doporučení**: Ověřte všechny kanály, které používají tento pracovní prostor datacihly pro svou rychlost vytváření úloh.  Pokud kanály spouštějí příliš mnoho datacihlů, migrují se některé kanály do nového pracovního prostoru.

<br/>

- **Zpráva**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Příčina**: `Authoring error: No value provided for the parameter.`

- **Doporučení**: Zkontrolujte kód JSON kanálu a ujistěte se, že všechny parametry v poznámkovém bloku baseParameters určují neprázdnou hodnotu.

<br/>

- **Zpráva**: `User: `SimpleUserContext {userId =..., name =user@company.com, OrgID =...}` is not   authorized to access cluster.`

- **Příčina**: uživatel, který vygeneroval přístupový token, nemá oprávnění k přístupu ke clusteru datacihlům, který je zadaný v propojené službě.

- **Doporučení**: Ujistěte se, že uživatel má v pracovním prostoru požadovaná oprávnění.


### <a name="error-code--3203"></a>Kód chyby: 3203

- **Zpráva**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Příčina**: cluster byl ukončen. U interaktivních clusterů to může být spor.

- **Doporučení**: nejlepší způsob, jak se vyhnout této chybě, je použití clusterů úloh.


### <a name="error-code--3204"></a>Kód chyby: 3204

- **Zpráva**: `Job execution failed.`

- **Příčina**: chybové zprávy indikují různé problémy, jako je neočekávaný stav clusteru nebo konkrétní aktivita. Nezobrazuje se vám většinou žádná chybová zpráva. 

- **Doporučení**: není k dispozici


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Následující tabulka platí pro U-SQL.


### <a name="error-code--2709"></a>Kód chyby: 2709

- **Zpráva**: `The access token is from the wrong tenant.`

- **Příčina**: nesprávný tenant Azure Active Directory (Azure AD).

- **Doporučení**: nesprávný tenant Azure Active Directory (Azure AD).

<br/>

- **Zpráva**: `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Příčina**: Tato chyba je způsobená omezováním Data Lake Analytics.

- **Doporučení**: Snižte počet odeslaných úloh, které se mají Data Lake Analytics změnou Data Factory triggerů a nastavení souběžnosti u aktivit. Nebo zvyšte limity Data Lake Analytics.

<br/>

- **Zpráva**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Příčina**: Tato chyba je způsobená omezováním Data Lake Analytics. 

- **Doporučení**: Snižte počet odeslaných úloh, které se mají Data Lake Analytics změnou Data Factory triggerů a nastavení souběžnosti u aktivit. Nebo zvyšte limity Data Lake Analytics.


### <a name="error-code--2705"></a>Kód chyby: 2705

- **Zpráva**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Příčina**: objekt služby nebo certifikát nemá přístup k souboru v úložišti.

- **Doporučení**: Ujistěte se, že instanční objekt nebo certifikát, který uživatel poskytuje pro úlohy Data Lake Analytics, má přístup k účtu Data Lake Analytics a výchozí instanci Data Lake Storage z kořenové složky.


### <a name="error-code--2711"></a>Kód chyby: 2711

- **Zpráva**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Příčina**: objekt služby nebo certifikát nemá přístup k souboru v úložišti.

- **Doporučení**: Ujistěte se, že instanční objekt nebo certifikát, který uživatel poskytuje pro úlohy Data Lake Analytics, má přístup k účtu Data Lake Analytics a výchozí instanci Data Lake Storage z kořenové složky.

<br/>

- **Zpráva**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Příčina**: cesta k souboru U-SQL je chybná nebo přihlašovací údaje propojené služby nemají přístup.

- **Doporučení**: Ověřte cestu a přihlašovací údaje, které jsou k dispozici v propojené službě.


### <a name="error-code--2704"></a>Kód chyby: 2704

- **Zpráva**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Příčina**: objekt služby nebo certifikát nemá přístup k souboru v úložišti.

- **Doporučení**: Ujistěte se, že instanční objekt nebo certifikát, který uživatel poskytuje pro úlohy Data Lake Analytics, má přístup k účtu Data Lake Analytics a výchozí instanci Data Lake Storage z kořenové složky.


### <a name="error-code--2707"></a>Kód chyby: 2707

- **Zpráva**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Příčina**: účet Data Lake Analytics v propojené službě je nesprávný.

- **Doporučení**: Ověřte, zda je zadán správný účet.


### <a name="error-code--2703"></a>Kód chyby: 2703

- **Zpráva**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Příčina**: Chyba je z Data Lake Analytics. 

- **Doporučení**: Chyba jako v příkladu znamená, že úloha byla odeslána do data Lake Analytics a skript se nezdařil. Prozkoumejte v Data Lake Analytics. Na portálu přejdete na účet Data Lake Analytics a vyhledáte úlohu pomocí ID spuštění Data Factory aktivity (ne ID spuštění kanálu). Úloha obsahuje další informace o této chybě a pomůže vám při odstraňování potíží. Pokud řešení není jasné, obraťte se na tým podpory Data Lake Analytics a zadejte adresu URL úlohy, která zahrnuje název vašeho účtu a ID úlohy.



## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>Kód chyby: 3602

- **Zpráva**: `Invalid HttpMethod: {method}.`

- **Příčina**: metoda HTTP zadaná v datové části aktivity není podporována aktivitou funkce Azure Functions. 

- **Doporučení**: podporované metody HTTP jsou PUT, post, Get, DELETE, Options, Head a Trace.


### <a name="error-code--3603"></a>Kód chyby: 3603

- **Zpráva**: `Response content is not a valid JObject.`

- **Příčina**: funkce Azure, která byla volána, nevrátila v odpovědi datovou část JSON. Aktivita funkce Azure v Data Factory podporuje jenom obsah odpovědi JSON. 

- **Doporučení**: aktualizujte funkci Azure functions a vraťte platnou datovou část JSON. C# Funkce může například vracet `(ActionResult)new<OkObjectResult("{`\"ID\":\"123\"`}");`.


### <a name="error-code--3606"></a>Kód chyby: 3606

- **Zpráva**: `Azure function activity missing function key.`

- **Příčina**: definice aktivity funkce Azure není dokončená. 

- **Doporučení**: Zkontrolujte, zda definice JSON vstupní AzureFunction aktivity obsahuje vlastnost s názvem functionKey.


### <a name="error-code--3607"></a>Kód chyby: 3607

- **Zpráva**: `Azure function activity missing function name.`

- **Příčina**: definice aktivity funkce Azure není dokončená. 

- **Doporučení**: Zkontrolujte, zda definice JSON vstupní AzureFunction aktivity obsahuje vlastnost s názvem "název funkce".


### <a name="error-code--3608"></a>Kód chyby: 3608

- **Zpráva**: `Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **Příčina**: Podrobnosti funkce Azure v definici aktivit můžou být nesprávné. 

- **Doporučení**: Opravte Podrobnosti funkce Azure a zkuste to znovu.


### <a name="error-code--3609"></a>Kód chyby: 3609

- **Zpráva**: `Azure function activity missing functionAppUrl.` 

- **Příčina**: definice aktivity funkce Azure není dokončená. 

- **Doporučení**: Zkontrolujte, zda definice JSON vstupní AzureFunction aktivity obsahuje vlastnost s názvem functionAppUrl.


### <a name="error-code--3610"></a>Kód chyby: 3610

- **Zpráva**: `There was an error while calling endpoint.`

- **Příčina**: adresa URL funkce může být nesprávná.

- **Doporučení**: Ujistěte se prosím, že hodnota pro ' functionAppUrl ' v kódu JSON aktivity je správná, a zkuste to znovu.


### <a name="error-code--3611"></a>Kód chyby: 3611

- **Zpráva**: `Azure function activity missing Method in JSON.` 

- **Příčina**: definice aktivity funkce Azure není dokončená.

- **Doporučení**: Ověřte, zda definice JSON vstupní AzureFunction aktivity obsahuje vlastnost s názvem Method.


### <a name="error-code--3612"></a>Kód chyby: 3612

- **Zpráva**: `Azure function activity missing LinkedService definition in JSON.`

- **Příčina**: definice aktivity funkce Azure možná není dokončená.

- **Doporučení**: Zkontrolujte, zda definice JSON vstupní AzureFunction aktivity obsahuje propojené služby.


## <a name="azure-machine-learning"></a>Azure Machine Learning


### <a name="error-code--4101"></a>Kód chyby: 4101

- **Zpráva**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Příčina**: chybný formát nebo chybí definice vlastnosti.

- **Doporučení**: Zkontrolujte prosím, jestli je aktivita definovaná se správnými daty.


### <a name="error-code--4110"></a>Kód chyby: 4110

- **Zpráva**: u aktivity AzureMLExecutePipeline chybí definice LinkedService ve formátu JSON.

- **Příčina**: definice aktivity AzureMLExecutePipeline není dokončena.

- **Doporučení**: Zkontrolujte prosím, jestli vstupní definice JSON pro aktivitu AzureMLExecutePipeline obsahuje propojené informace o službě.


### <a name="error-code--4111"></a>Kód chyby: 4111

- **Zpráva**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Příčina**: Nesprávná definice aktivity.

- **Doporučení**: Zkontrolujte prosím, jestli vstupní definice JSON pro aktivitu AzureMLExecutePipeline neobsahuje správné podrobnosti propojené služby.


### <a name="error-code--4112"></a>Kód chyby: 4112

- **Zpráva**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Příčina**: chybný formát nebo chybí definice vlastnosti.

- **Doporučení**: Zkontrolujte, zda definice propojené služby obsahuje správná data.


### <a name="error-code--4121"></a>Kód chyby: 4121

- **Zpráva**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Příčina**: vypršela platnost přihlašovacích údajů použitých pro přístup ke službě Azure ml.

- **Doporučení**: Ověřte, že přihlašovací údaje jsou platné, a zkuste to znovu.


### <a name="error-code--4122"></a>Kód chyby: 4122

- **Zpráva**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Příčina**: přihlašovací údaje zadané v propojené službě AzureML jsou neplatné nebo nemají oprávnění k této operaci.

- **Doporučení**: Ověřte prosím, že přihlašovací údaje v propojené službě jsou platné a mají oprávnění pro přístup ke službě AzureML.


### <a name="error-code--4123"></a>Kód chyby: 4123

- **Zpráva**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Příčina**: `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Doporučení**: Zkontrolujte hodnoty vlastností aktivity, aby odpovídaly očekávané datové části publikovaného kanálu Azure ml zadaného v propojené službě.


### <a name="error-code--4124"></a>Kód chyby: 4124

- **Zpráva**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Příčina**: publikovaný koncový bod kanálu Azure ml neexistuje.

- **Doporučení**: Ověřte, že publikovaný koncový bod kanálu Azure ml zadaný v propojené službě existují ve službě Azure ml.


### <a name="error-code--4125"></a>Kód chyby: 4125

- **Zpráva**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Příčina**: Chyba serveru ve službě Azure ml.

- **Doporučení**: zkuste to prosím znovu později. Pokud problém trvá, obraťte se na tým služby Azure ML.


### <a name="error-code--4126"></a>Kód chyby: 4126

- **Zpráva**: `AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **Příčina**: selhalo spuštění kanálu AzureML.

- **Doporučení**: Zaregistrujte se prosím v AzureMLService, kde najdete další protokoly chyb a OPRAVÍTE kanál ml.


## <a name="custom"></a>Vlastní

Následující tabulka se vztahuje na Azure Batch.


### <a name="error-code--2500"></a>Kód chyby: 2500

- **Zpráva**: `Hit unexpected exception and execution failed.`

- **Příčina**: `Can't launch command, or the program returned an error code.`

- **Doporučení**: Ujistěte se, že spustitelný soubor existuje. Pokud se program spustil, ujistěte se, že se do účtu úložiště nahrály *STDOUT. txt* a *stderr. txt* . Je dobrým zvykem generovat protokoly Copious v kódu pro ladění.


### <a name="error-code--2501"></a>Kód chyby: 2501

- **Zpráva**: `Cannot access user batch account; please check batch account settings.`

- **Příčina**: nesprávný klíč pro přístup k dávce nebo název fondu.

- **Doporučení**: Ověřte název fondu a přístupový klíč Batch v propojené službě.


### <a name="error-code--2502"></a>Kód chyby: 2502

- **Zpráva**: `Cannot access user storage account; please check storage account settings.`

- **Příčina**: nesprávný název účtu úložiště nebo přístupový klíč.

- **Doporučení**: Ověřte název účtu úložiště a přístupový klíč v propojené službě.


### <a name="error-code--2504"></a>Kód chyby: 2504

- **Zpráva**: `Operation returned an invalid status code 'BadRequest'.` 

- **Příčina**: v FolderPath vlastní aktivity je moc velký počet souborů. Celková velikost resourceFiles nemůže být delší než 32 768 znaků.

- **Doporučení**: Odstraňte nepotřebné soubory. Nebo je zip a přidejte příkaz k extrakci, který je rozbalí. Použijte například `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Kód chyby: 2505

- **Zpráva**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Příčina**: vlastní aktivity podporují jenom účty úložiště, které používají přístupový klíč.

- **Doporučení**: Přečtěte si popis chyby.


### <a name="error-code--2507"></a>Kód chyby: 2507

- **Zpráva**: `The folder path does not exist or is empty: ....`

- **Příčina**: v zadaném umístění nejsou žádné soubory v účtu úložiště.

- **Doporučení**: cesta ke složce musí obsahovat spustitelné soubory, které chcete spustit.


### <a name="error-code--2508"></a>Kód chyby: 2508

- **Zpráva**: `There are duplicate files in the resource folder.`

- **Příčina**: více souborů se stejným názvem jsou v různých podsložkách FolderPath.

- **Doporučení**: vlastní aktivity sloučí strukturu složek pod FolderPath.  Pokud potřebujete zachovat strukturu složek, soubory zip a extrahujte je v Azure Batch pomocí příkazu Rozbalit. Použijte například `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Kód chyby: 2509

- **Zpráva**: `Batch   url ... is invalid; it must be in Uri format.` 

- **Příčina**: adresy URL dávky musí být podobné `https://mybatchaccount.eastus.batch.azure.com`

- **Doporučení**: Přečtěte si popis chyby.


### <a name="error-code--2510"></a>Kód chyby: 2510

- **Zpráva**: `An   error occurred while sending the request.`

- **Příčina**: adresa URL dávky není platná. 

- **Doporučení**: Ověřte adresu URL dávky.


## <a name="hdinsight"></a>HDInsight

Následující tabulka se vztahuje na datové proudy Spark, podregistr, MapReduce, prasete a Hadoop.


### <a name="error-code--2300"></a>Kód chyby: 2300

- **Zpráva**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Příčina**: zadaný identifikátor URI clusteru je neplatný. 

- **Doporučení**: Ujistěte se, že se cluster neodstranil a že je zadaný identifikátor URI správný. Po otevření identifikátoru URI v prohlížeči by se mělo zobrazit uživatelské rozhraní Ambari. Pokud je cluster ve virtuální síti, identifikátor URI by měl být privátním identifikátorem URI. Pokud ho chcete otevřít, použijte virtuální počítač, který je součástí stejné virtuální sítě. Další informace najdete v tématu [přímé připojení k Apache Hadoop službám](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Zpráva**: `Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **Příčina**: vypršel časový limit pro odeslání úlohy. 

- **Doporučení**: Tento problém může být buď obecným připojením HDInsight, nebo připojením k síti. Nejdřív potvrďte, že je uživatelské rozhraní HDInsight Ambari dostupné z libovolného prohlížeče. Potvrďte, že vaše přihlašovací údaje jsou pořád platné. Pokud používáte integrovaný modul runtime (IR) s místním hostováním, ujistěte se, že provedete tento postup z virtuálního počítače nebo počítače, ve kterém je nainstalováno prostředí IR pro místní hostování. Pak zkuste úlohu odeslat z Data Factory znovu. Pokud se to nepodaří, obraťte se na tým Data Factory pro podporu.


- **Zpráva**: `Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **Příčina**: přihlašovací údaje pro HDInsight jsou nesprávné nebo vypršela jejich platnost.

- **Doporučení**: Opravte přihlašovací údaje a znovu nasaďte propojenou službu. Nejdřív ověřte, že přihlašovací údaje fungují v HDInsight tím, že otevřete identifikátor URI clusteru v jakémkoli prohlížeči a zkusíte se přihlásit. Pokud přihlašovací údaje nefungují, můžete je resetovat z Azure Portal.

<br/>

- **Zpráva**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Příčina**: Tato chyba je ze služby HDInsight.

- **Doporučení**: Tato chyba je z clusteru HDInsight. Další informace najdete v tématu [Chyba Ambari uživatelského rozhraní 502](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 chyby připojení k serveru spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 chyby při připojování k serveru Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)a řešení chyb chybná [Brána v Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Zpráva**: `Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **Příčina**: do HDInsight se odesílá příliš mnoho úloh současně.

- **Doporučení**: Zvažte omezení počtu souběžných úloh odeslaných do HDInsight. Pokud jsou úlohy odesílány stejnou aktivitou, přečtěte si téma Data Factory souběžnosti aktivity. Změna aktivačních událostí, aby souběžné běhy byly rozloženy v průběhu času. V dokumentaci k HDInsight si můžete `templeton.parallellism.job.submit` upravit, jak naznačuje chyba.


### <a name="error-code--2303"></a>Kód chyby: 2303

- **Zpráva**: `Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Příčina**: úloha byla odeslána do HDInsight a selhala v HDInsight.

- **Doporučení**: úloha byla úspěšně odeslána do HDInsight. Nepovedlo se v clusteru. Buď otevřete úlohu a protokoly v uživatelském rozhraní HDInsight Ambari, nebo soubor otevřete z úložiště, jak naznačuje chybová zpráva. V souboru se zobrazí podrobnosti o chybě.


### <a name="error-code--2310"></a>Kód chyby: 2310

- **Zpráva**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Příčina**: zadaný identifikátor URI clusteru je neplatný. 

- **Doporučení**: Ujistěte se, že se cluster neodstranil a že je zadaný identifikátor URI správný. Po otevření identifikátoru URI v prohlížeči by se mělo zobrazit uživatelské rozhraní Ambari. Pokud je cluster ve virtuální síti, identifikátor URI by měl být privátním identifikátorem URI. Pokud ho chcete otevřít, použijte virtuální počítač, který je součástí stejné virtuální sítě. Další informace najdete v tématu [přímé připojení k Apache Hadoop službám](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Zpráva**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Příčina**: Tato chyba je ze služby HDInsight.

- **Doporučení**: Tato chyba je z clusteru HDInsight. Další informace najdete v tématu [Chyba Ambari uživatelského rozhraní 502](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 chyby připojení k serveru spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 chyby při připojování k serveru Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)a řešení chyb chybná [Brána v Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Zpráva**: `java.lang.NullPointerException`

- **Příčina**: k této chybě dojde, když se úloha odešle do clusteru Spark. 

- **Doporučení**: Tato výjimka pochází ze služby HDInsight. Skrývá skutečný problém. Požádejte o podporu tým HDInsight. Zadejte název clusteru a rozsah doby běhu aktivity.


### <a name="error-code--2347"></a>Kód chyby: 2347

- **Zpráva**: `Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Příčina**: úloha byla odeslána do HDInsight a selhala v HDInsight.

- **Doporučení**: úloha byla úspěšně odeslána do HDInsight. Nepovedlo se v clusteru. Buď otevřete úlohu a protokoly v uživatelském rozhraní HDInsight Ambari, nebo soubor otevřete z úložiště, jak naznačuje chybová zpráva. V souboru se zobrazí podrobnosti o chybě.


### <a name="error-code--2328"></a>Kód chyby: 2328

- **Zpráva**: `Internal server error occurred while processing the request. Please retry the request or contact support. `

- **Příčina**: k této chybě dochází v HDInsight na vyžádání.

- **Doporučení**: k této chybě dojde ze služby HDInsight, když se zřizování HDInsight nepovede. Kontaktujte tým HDInsight a zadejte název clusteru na vyžádání.



## <a name="web-activity"></a>Aktivita webu

### <a name="error-code--2108"></a>Kód chyby: 2108

- **Zpráva**: `Invalid HttpMethod: '...'.`

- **Příčina**: Webová aktivita nepodporuje metodu HTTP zadanou v datové části aktivity.

- **Doporučení**: podporované metody HTTP jsou PUT, post, get a DELETE.

<br/>

- **Zpráva**: `Invalid Server Error 500.`

- **Příčina**: vnitřní chyba koncového bodu.

- **Doporučení**: k ověření funkčnosti na adrese URL použijte Fiddler nebo post.

<br/>

- **Zpráva**: `Unauthorized 401.`

- **Příčina**: u žádosti chybí platné ověření.

- **Doporučení**: platnost tokenu možná vypršela. Zadejte platnou metodu ověřování. Použijte Fiddler nebo post ke kontrole funkčnosti na adrese URL.

<br/>

- **Zpráva**: `Forbidden 403.`

- **Příčina**: chybí požadovaná oprávnění.

- **Doporučení**: Ověřte oprávnění uživatelů u dostupného prostředku. Použijte Fiddler nebo post ke kontrole funkčnosti na adrese URL.

<br/>

- **Zpráva**: `Bad Request 400.`

- **Příčina**: neplatný požadavek HTTP.

- **Doporučení**: Ověřte adresu URL, sloveso a text požadavku. K ověření žádosti použijte Fiddler nebo post.

<br/>

- **Zpráva**: `Not found 404.` 

- **Příčina**: prostředek se nenašel.   

- **Doporučení**: k ověření žádosti použijte Fiddler nebo post.

<br/>

- **Zpráva**: `Service unavailable.`

- **Příčina**: služba není k dispozici.

- **Doporučení**: k ověření žádosti použijte Fiddler nebo post.

<br/>

- **Zpráva**: `Unsupported Media Type.`

- **Příčina**: typ obsahu se neshoduje s textem webové aktivity.

- **Doporučení**: Určete typ obsahu, který odpovídá formátu datové části. K ověření žádosti použijte Fiddler nebo post.

<br/>

- **Zpráva**: `The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **Příčina**: prostředek není k dispozici. 

- **Doporučení**: ke kontrole koncového bodu použijte Fiddler nebo post.

<br/>

- **Zpráva**: `The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **Příčina**: v žádosti byla zadána nesprávná metoda webové aktivity.

- **Doporučení**: ke kontrole koncového bodu použijte Fiddler nebo post.

<br/>

- **Zpráva**: `invalid_payload`

- **Příčina**: tělo aktivity webu je nesprávné.

- **Doporučení**: ke kontrole koncového bodu použijte Fiddler nebo post.


### <a name="error-code--2208"></a>Kód chyby: 2208

- **Zpráva**: `Invoking Web Activity failed with HttpStatusCode - {0}.`

- **Příčina**: cílová služba vrátila stav selhání.

- **Doporučení**: k ověření žádosti použijte Fiddler/Poster.


### <a name="error-code--2308"></a>Kód chyby: 2308

- **Zpráva**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Příčina**: Tato chyba může mít několik příčin, jako je připojení k síti, selhání služby DNS, ověřování certifikátu serveru nebo časový limit.

- **Doporučení**: k ověření žádosti použijte Fiddler/Poster.


Použití Fiddler k vytvoření relace HTTP monitorované webové aplikace:

1. Stáhněte, nainstalujte a otevřete [Fiddler](https://www.telerik.com/download/fiddler).

1. Pokud vaše webová aplikace používá protokol HTTPS, můžete přejít na **nástroje** > **Možnosti Fiddler** > **https**. Vyberte **zachytit protokol HTTPS připojení** a **dešifrování přenosu HTTPS**. 
   
   ![Fiddler možnosti](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Pokud vaše aplikace používá certifikáty SSL, přidejte do svého zařízení certifikát Fiddler. V **nabídce nástroje** > **Možnosti Fiddler** > **https** > **Akce** > **exportovat kořenový certifikát do plochy**.

1. Vypněte zachytávání tak, že v souboru **zachytíte > zachytávání provozu**. Nebo stiskněte klávesu **F12**.

1. Vymažte mezipaměť prohlížeče, aby se odstranily všechny položky v mezipaměti, a je nutné je znovu stáhnout.

1. Vytvořit požadavek: 

   a. Vyberte kartu **Autor** .

   b. Nastavte metodu HTTP a adresu URL.

   c. Pokud potřebujete, přidejte záhlaví a text žádosti.

   d. Vyberte **Provést**.

9. Zapněte znovu zachytávání provozu a dokončete problematickou transakci na stránce.

10. Přejít na **soubor** > **Uložit** > **všechny relace**.

Další informace najdete v tématu [Začínáme s Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

*  [Blog věnovaný Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa k Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)



