---
title: Řešení potíží s Azure Data Factory | Microsoft Docs
description: Přečtěte si, jak řešit potíže s externími ovládacími prvky v Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 922ec6c4b579a657e7ee5e872148f8126ce175e2
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822280"
---
# <a name="troubleshoot-azure-data-factory"></a>Řešení potíží se službou Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro aktivity externích řízení v Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Aktivita konektoru a kopírování

Problémy s konektorem, jako je chyba při použití aktivity kopírování, najdete v tématu [řešení potíží s Azure Data Factory konektory](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>Kód chyby: 3200

- **Zpráva**: Chyba 403.

- **Příčina**: `The Databricks access token has expired.`

- **Doporučení**: ve výchozím nastavení je přístupový token Azure Databricks platný po dobu 90 dnů. Vytvořte nový token a aktualizujte propojenou službu.

### <a name="error-code-3201"></a>Kód chyby: 3201

- **Zpráva**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Příčina**: `Bad authoring: Notebook path not specified correctly.`

- **Doporučení**: zadejte cestu k poznámkovému bloku v aktivitě datacihly.

<br/> 

- **Zpráva**: `Cluster... does not exist.`

- **Příčina**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Doporučení**: Ověřte, zda existuje cluster datacihly.

<br/> 

- **Zpráva**: `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Příčina**: `Bad authoring.`

- **Doporučení**: Zadejte buď absolutní cesty pro schémata adresování pracovních prostorů, nebo `dbfs:/folder/subfolder/foo.py` soubory uložené v systému souborů DFS (datacihly).

<br/> 

- **Zpráva**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Příčina**: `Bad authoring.`

- **Doporučení**: Ověřte [definici propojené služby](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Zpráva**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Příčina**: `Bad authoring.`

- **Doporučení**: Ověřte [definici propojené služby](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Zpráva**: `Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **Příčina**: `Bad authoring.`

- **Doporučení**: Přečtěte si chybovou zprávu.

<br/>

### <a name="error-code-3202"></a>Kód chyby: 3202

- **Zpráva**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **Příčina**: `Too many Databricks runs in an hour.`

- **Doporučení**: Ověřte všechny kanály, které používají tento pracovní prostor datacihly pro svou rychlost vytváření úloh. Pokud kanály spouštějí příliš mnoho datacihlů, migrují se některé kanály do nového pracovního prostoru.

<br/> 

- **Zpráva**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Příčina**: `Authoring error: No value provided for the parameter.`

- **Doporučení**: Zkontrolujte kód JSON kanálu a ujistěte se, že všechny parametry v poznámkovém bloku baseParameters určují neprázdnou hodnotu.

<br/> 

- **Zpráva**: `User: ` SimpleUserContext {userId =..., název = user@company.com , OrgID =...}` is not authorized to access cluster.`

- **Příčina**: uživatel, který vygeneroval přístupový token, nemá oprávnění k přístupu ke clusteru datacihlům, který je zadaný v propojené službě.

- **Doporučení**: Ujistěte se, že uživatel má v pracovním prostoru požadovaná oprávnění.

### <a name="error-code-3203"></a>Kód chyby: 3203

- **Zpráva**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Příčina**: cluster byl ukončen. U interaktivních clusterů se může jednat o případ časování.

- **Doporučení**: Chcete-li se této chybě vyhnout, použijte clustery úloh.

### <a name="error-code-3204"></a>Kód chyby: 3204

- **Zpráva**: `Job execution failed.`

- **Příčina**: chybové zprávy indikují různé problémy, jako je neočekávaný stav clusteru nebo konkrétní aktivita. Často se nezobrazí žádná chybová zpráva.

- **Doporučení**: není k dispozici

### <a name="error-code-3208"></a>Kód chyby: 3208

- **Zpráva**: `An error occurred while sending the request.`

- **Příčina**: síťové připojení ke službě datacihly bylo přerušeno.

- **Doporučení**: Pokud používáte prostředí Integration runtime v místním prostředí, ujistěte se, že síťové připojení je spolehlivé z uzlů Integration runtime. Pokud používáte Azure Integration runtime, obvykle to funguje.
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Následující tabulka platí pro U-SQL.
 
### <a name="error-code-2709"></a>Kód chyby: 2709

- **Zpráva**: `The access token is from the wrong tenant.`

- **Příčina**: nesprávný tenant Azure Active Directory (Azure AD).

- **Doporučení**: nesprávný tenant Azure Active Directory (Azure AD).

<br/>

- **Zpráva**: `We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **Příčina**: Tato chyba je způsobená omezováním Data Lake Analytics.

- **Doporučení**: Snižte počet odeslaných úloh, které se mají data Lake Analytics. Buď změňte triggery Data Factory a nastavení souběžnosti u aktivit, nebo zvyšte omezení Data Lake Analytics.

<br/> 

- **Zpráva**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Příčina**: Tato chyba je způsobená omezováním Data Lake Analytics.

- **Doporučení**: Snižte počet odeslaných úloh, které se mají data Lake Analytics. Buď změňte triggery Data Factory a nastavení souběžnosti u aktivit, nebo zvyšte omezení Data Lake Analytics.

### <a name="error-code-2705"></a>Kód chyby: 2705

- **Zpráva**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Příčina**: objekt služby nebo certifikát nemá přístup k souboru v úložišti.

- **Doporučení**: Ověřte, že instanční objekt nebo certifikát, který uživatel poskytuje pro úlohy Data Lake Analytics, má přístup k účtu Data Lake Analytics a výchozí instanci Data Lake Storage z kořenové složky.

### <a name="error-code-2711"></a>Kód chyby: 2711

- **Zpráva**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Příčina**: objekt služby nebo certifikát nemá přístup k souboru v úložišti.

- **Doporučení**: Ověřte, že instanční objekt nebo certifikát, který uživatel poskytuje pro úlohy Data Lake Analytics, má přístup k účtu Data Lake Analytics a výchozí instanci Data Lake Storage z kořenové složky.

<br/> 

- **Zpráva**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Příčina**: cesta k souboru U-SQL je chybná nebo přihlašovací údaje propojené služby nemají přístup.

- **Doporučení**: Ověřte cestu a přihlašovací údaje, které jsou k dispozici v propojené službě.

### <a name="error-code-2704"></a>Kód chyby: 2704

- **Zpráva**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Příčina**: objekt služby nebo certifikát nemá přístup k souboru v úložišti.

- **Doporučení**: Ověřte, že instanční objekt nebo certifikát, který uživatel poskytuje pro úlohy Data Lake Analytics, má přístup k účtu Data Lake Analytics a výchozí instanci Data Lake Storage z kořenové složky.

### <a name="error-code-2707"></a>Kód chyby: 2707

- **Zpráva**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **Příčina**: účet Data Lake Analytics v propojené službě je nesprávný.

- **Doporučení**: Ověřte, zda je zadán správný účet.

### <a name="error-code-2703"></a>Kód chyby: 2703

- **Zpráva**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **Příčina**: Chyba je z Data Lake Analytics.

- **Doporučení**: úloha byla odeslána do data Lake Analytics a do skriptu došlo k chybě. Prozkoumejte v Data Lake Analytics. Na portálu přejdete na účet Data Lake Analytics a vyhledáte úlohu pomocí ID spuštění Data Factory aktivity (nepoužívejte ID běhu kanálu). Úloha obsahuje další informace o této chybě a pomůže vám při odstraňování potíží.

   Pokud řešení není jasné, obraťte se na tým podpory Data Lake Analytics a poskytněte mu adresu URL (Universal Resource Locator), která zahrnuje název vašeho účtu a ID úlohy.
 
## <a name="azure-functions"></a>Azure Functions

### <a name="error-code-3602"></a>Kód chyby: 3602

- **Zpráva**: `Invalid HttpMethod: '%method;'.`

- **Příčina**: aktivita funkce Azure nepodporuje HttpMethod zadanou v datové části aktivity.

- **Doporučení**: podporované HttpMethods jsou: PUT, post, Get, DELETE, Options, Head a Trace.

### <a name="error-code-3603"></a>Kód chyby: 3603

- **Zpráva**: `Response Content is not a valid JObject.`

- **Příčina**: funkce Azure, která byla volána, nevrátila v odpovědi datovou část JSON. Aktivita služby Azure Functions v Azure Data Factory (ADF) podporuje pouze obsah odpovědi JSON.

- **Doporučení**: aktualizujte funkci Azure Functions, aby vracela platnou datovou část JSON, jako je funkce jazyka C#, která může vracet `(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>Kód chyby: 3606

- **Zpráva**: aktivita funkce Azure postrádá klíč funkce.

- **Příčina**: definice aktivity funkce Azure není dokončená.

- **Doporučení**: Ověřte, jestli má vstupní definice JSON aktivity funkce Azure vlastnost s názvem `functionKey` .

### <a name="error-code-3607"></a>Kód chyby: 3607

- **Zpráva**: `Azure function activity missing function name.`

- **Příčina**: definice aktivity funkce Azure není dokončená.

- **Doporučení**: Ověřte, jestli má vstupní definice JSON aktivity funkce Azure vlastnost s názvem `functionName` .

### <a name="error-code-3608"></a>Kód chyby: 3608

- **Zpráva**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Příčina**: Podrobnosti funkce Azure v definici aktivity můžou být nesprávné.

- **Doporučení**: Opravte podrobnosti o funkci Azure a zkuste to znovu.

### <a name="error-code-3609"></a>Kód chyby: 3609

- **Zpráva**: `Azure function activity missing functionAppUrl.`

- **Příčina**: definice aktivity funkce Azure není dokončená.

- **Doporučení**: Ověřte, jestli má vstupní definice JSON aktivity funkce Azure vlastnost s názvem `functionAppUrl` .

### <a name="error-code-3610"></a>Kód chyby: 3610

- **Zpráva**: `There was an error while calling endpoint.`

- **Příčina**: adresa URL funkce může být nesprávná.

- **Doporučení**: Ověřte správnost hodnoty pro `functionAppUrl` ve formátu JSON aktivity a zkuste to znovu.

### <a name="error-code-3611"></a>Kód chyby: 3611

- **Zpráva**: `Azure function activity missing Method in JSON.`

- **Příčina**: definice aktivity funkce Azure není dokončená.

- **Doporučení**: Ověřte, jestli má vstupní definice JSON aktivity funkce Azure vlastnost s názvem `method` .

### <a name="error-code-3612"></a>Kód chyby: 3612

- **Zpráva**: `Azure function activity missing LinkedService definition in JSON.`

- **Příčina**: definice aktivity funkce Azure není dokončená.

- **Doporučení**: Ověřte, zda vstupní definice JSON aktivity funkce Azure obsahuje propojené Podrobnosti služby.

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>Kód chyby: 4101

- **Zpráva**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Příčina**: chybný formát nebo chybí definice vlastnosti `%propertyName;` .

- **Doporučení**: Ověřte, jestli je u aktivity `%activityName;` definovaná Tato vlastnost `%propertyName;` se správnými daty.

### <a name="error-code-4110"></a>Kód chyby: 4110

- **Zpráva**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Příčina**: definice aktivity AzureMLExecutePipeline není dokončená.

- **Doporučení**: Ověřte, že definice JSON vstupní AzureMLExecutePipeline aktivity má správně propojené Podrobnosti služby.

### <a name="error-code-4111"></a>Kód chyby: 4111

- **Zpráva**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Příčina**: Nesprávná definice aktivity.

- **Doporučení**: Ověřte, že definice JSON vstupní AzureMLExecutePipeline aktivity má správně propojené Podrobnosti služby.

### <a name="error-code-4112"></a>Kód chyby: 4112

- **Zpráva**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Příčina**: chybný formát nebo chybějící definice vlastnosti% PropertyName;.

- **Doporučení**: Ověřte, jestli je u propojené služby `%propertyName;` definovaná vlastnost se správnými daty.

### <a name="error-code-4121"></a>Kód chyby: 4121

- **Zpráva**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Příčina**: vypršela platnost přihlašovacích údajů použitých pro přístup k Azure Machine Learning.

- **Doporučení**: Ověřte, jestli jsou přihlašovací údaje platné, a zkuste to znovu.

### <a name="error-code-4122"></a>Kód chyby: 4122

- **Zpráva**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Příčina**: přihlašovací údaje zadané v propojené službě Azure Machine Learning jsou neplatné nebo nemají oprávnění k této operaci.

- **Doporučení**: Ověřte, že přihlašovací údaje v propojené službě jsou platné a mají oprávnění pro přístup k Azure Machine Learning.

### <a name="error-code-4123"></a>Kód chyby: 4123

- **Zpráva**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Příčina**: vlastnosti aktivity, například, `pipelineParameters` jsou pro kanál Azure Machine Learning (ml) neplatné.

- **Doporučení**: Ověřte, že hodnota vlastností aktivity odpovídá očekávané datové části publikovaného kanálu Azure ml zadaného v propojené službě.

### <a name="error-code-4124"></a>Kód chyby: 4124

- **Zpráva**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Příčina**: publikovaný koncový bod kanálu Azure ml neexistuje.

- **Doporučení**: Ověřte, že v Azure Machine Learning existuje publikovaný koncový bod kanálu Azure Machine Learning, který je zadaný v propojené službě.

### <a name="error-code-4125"></a>Kód chyby: 4125

- **Zpráva**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Příčina**: v Azure Machine Learning dojde k chybě serveru.

- **Doporučení**: zkuste to znovu později. Pokud potíže potrvají, obraťte se na tým Azure Machine Learning.

### <a name="error-code-4126"></a>Kód chyby: 4126

- **Zpráva**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Příčina**: spuštění kanálu Azure ml selhalo.

- **Doporučení**: Zkontrolujte Azure Machine Learning pro další protokoly chyb a pak opravte kanál ml.

## <a name="common"></a>Společné

### <a name="error-code-2103"></a>Kód chyby: 2103

- **Zpráva**: `Please provide value for the required property '%propertyName;'.`

- **Příčina**: nebyla zadána požadovaná hodnota pro vlastnost.

- **Doporučení**: zadejte hodnotu ze zprávy a zkuste to znovu.

### <a name="error-code-2104"></a>Kód chyby: 2104

- **Zpráva**: `The type of the property '%propertyName;' is incorrect.`

- **Příčina**: poskytnutý typ vlastnosti není správný.

- **Doporučení**: Opravte typ vlastnosti a zkuste to znovu.

### <a name="error-code-2105"></a>Kód chyby: 2105

- **Zpráva**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Příčina**: hodnota vlastnosti je neplatná nebo není v očekávaném formátu.

- **Doporučení**: Přečtěte si dokumentaci k vlastnosti a ověřte, zda zadaná hodnota obsahuje správný formát a typ.

### <a name="error-code-2106"></a>Kód chyby: 2106

- **Zpráva**: `The storage connection string is invalid. %errorMessage;`

- **Příčina**: připojovací řetězec pro úložiště je neplatný nebo má nesprávný formát.

- **Doporučení**: otevřete Azure Portal a vyhledejte úložiště, pak zkopírujte a vložte připojovací řetězec do propojené služby a zkuste to znovu.

### <a name="error-code-2108"></a>Kód chyby: 2108

- **Zpráva**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Příčina**: požadavek se nezdařil z důvodu základního problému, například připojení k síti, selhání služby DNS, ověřování certifikátu serveru nebo vypršení časového limitu.

- **Doporučení**: k ověření žádosti použijte Fiddler/Poster.

### <a name="error-code-2110"></a>Kód chyby: 2110

- **Zpráva**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Příčina**: propojená služba zadaná v aktivitě je nesprávná.

- **Doporučení**: Ověřte, že typ propojené služby je jedním z podporovaných typů aktivity. Například typ propojené služby pro aktivity HDI může být HDInsight nebo HDInsightOnDemand.

### <a name="error-code-2111"></a>Kód chyby: 2111

- **Zpráva**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Příčina**: typ poskytnuté vlastnosti není správný.

- **Doporučení**: Opravte typ vlastnosti a zkuste to znovu.

### <a name="error-code-2112"></a>Kód chyby: 2112

- **Zpráva**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Příčina**: typ cloudu není podporovaný nebo ho nebylo možné určit pro úložiště z EndpointSuffix.

- **Doporučení**: použijte úložiště v jiném cloudu a zkuste to znovu.

### <a name="error-code-2128"></a>Kód chyby: 2128

- **Zpráva**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Příčina**: připojení k síti, selhání služby DNS, ověřování certifikátu serveru nebo časový limit.

- **Doporučení**: Ověřte, že koncový bod, který se snažíte dosáhnout, reaguje na požadavky. Můžete použít nástroje jako Fiddler/post.

## <a name="custom"></a>Vlastní

Následující tabulka se vztahuje na Azure Batch.
 
### <a name="error-code-2500"></a>Kód chyby: 2500

- **Zpráva**: `Hit unexpected exception and execution failed.`

- **Příčina**: `Can't launch command, or the program returned an error code.`

- **Doporučení**: Ujistěte se, že spustitelný soubor existuje. Pokud se program spustil, ověřte, že se do účtu úložiště nahrály *stdout.txt* a *stderr.txt* . Je dobrým zvykem zahrnout do kódu protokoly pro ladění.

### <a name="error-code-2501"></a>Kód chyby: 2501

- **Zpráva**: `Cannot access user batch account; please check batch account settings.`

- **Příčina**: nesprávný klíč pro přístup k dávce nebo název fondu.

- **Doporučení**: Ověřte název fondu a přístupový klíč Batch v propojené službě.

### <a name="error-code-2502"></a>Kód chyby: 2502

- **Zpráva**: `Cannot access user storage account; please check storage account settings.`

- **Příčina**: nesprávný název účtu úložiště nebo přístupový klíč.

- **Doporučení**: Ověřte název účtu úložiště a přístupový klíč v propojené službě.

### <a name="error-code-2504"></a>Kód chyby: 2504

- **Zpráva**: `Operation returned an invalid status code 'BadRequest'.`

- **Příčina**: v rámci vlastní aktivity je moc velký počet souborů `folderPath` . Celková velikost `resourceFiles` nemůže být delší než 32 768 znaků.

- **Doporučení**: Odstraňte nepotřebné soubory nebo je zip a přidejte příkaz k extrakci.
   
   Například použijte `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>Kód chyby: 2505

- **Zpráva**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Příčina**: vlastní aktivity podporují jenom účty úložiště, které používají přístupový klíč.

- **Doporučení**: Přečtěte si popis chyby.

### <a name="error-code-2507"></a>Kód chyby: 2507

- **Zpráva**: `The folder path does not exist or is empty: ...`

- **Příčina**: v zadaném umístění nejsou žádné soubory v účtu úložiště.

- **Doporučení**: cesta ke složce musí obsahovat spustitelné soubory, které chcete spustit.

### <a name="error-code-2508"></a>Kód chyby: 2508

- **Zpráva**: `There are duplicate files in the resource folder.`

- **Příčina**: více souborů se stejným názvem jsou v různých podsložkách FolderPath.

- **Doporučení**: vlastní aktivity sloučí strukturu složek pod FolderPath. Pokud potřebujete zachovat strukturu složek, soubory zip a extrahujte je v Azure Batch pomocí příkazu Rozbalit.
   
   Například použijte `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>Kód chyby: 2509

- **Zpráva**: `Batch url ... is invalid; it must be in Uri format.`

- **Příčina**: adresy URL dávky musí být podobné `https://mybatchaccount.eastus.batch.azure.com`

- **Doporučení**: Přečtěte si popis chyby.

### <a name="error-code-2510"></a>Kód chyby: 2510

- **Zpráva**: `An error occurred while sending the request.`

- **Příčina**: adresa URL dávky není platná.

- **Doporučení**: Ověřte adresu URL dávky.
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-206"></a>Kód chyby: 206

- **Zpráva**: `The batch ID for Spark job is invalid. Please retry your job.`

- **Příčina**: došlo k vnitřnímu problému se službou, která způsobila tuto chybu.

- **Doporučení**: Tento problém může být přechodný. Po nějaké době zkuste úlohu zopakovat.

### <a name="error-code-207"></a>Kód chyby: 207

- **Zpráva**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI.`

- **Příčina**: při pokusu o zjištění oblasti z primárního účtu úložiště došlo k vnitřní chybě.

- **Doporučení**: zkuste jiné úložiště. 

### <a name="error-code-208"></a>Kód chyby: 208

- **Zpráva**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again.`

- **Příčina**: při pokusu o čtení instančního objektu nebo při vytváření instancí ověřování MSI došlo k vnitřní chybě.

- **Doporučení**: Zvažte poskytnutí instančního objektu, který má oprávnění k vytvoření clusteru HDInsight v zadaném předplatném, a zkuste to znovu. Ověřte, že je [správně nastavená Správa identit](../hdinsight/hdinsight-managed-identities.md).

### <a name="error-code-2300"></a>Kód chyby: 2300

- **Zpráva**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **Příčina**: chybová zpráva obsahuje zprávu podobnou této `The remote name could not be resolved.` . Poskytnutý identifikátor URI clusteru může být neplatný.

- **Doporučení**: Ověřte, že se cluster neodstranil a že je zadaný identifikátor URI správný. Po otevření identifikátoru URI v prohlížeči by se mělo zobrazit uživatelské rozhraní Ambari. Pokud je cluster ve virtuální síti, identifikátor URI by měl být privátním identifikátorem URI. Pokud ho chcete otevřít, použijte virtuální počítač (VM), který je součástí stejné virtuální sítě.

   Další informace najdete v tématu [přímé připojení k Apache Hadoop službám](../hdinsight/hdinsight-plan-virtual-network-deployment.md#directly-connect-to-apache-hadoop-services).
 
 </br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou této zprávě `A task was canceled.` , vypršel časový limit pro odeslání úlohy.

- **Doporučení**: Tento problém může být buď obecným připojením HDInsight, nebo připojením k síti. Nejdřív potvrďte, že je uživatelské rozhraní HDInsight Ambari dostupné z libovolného prohlížeče. Potom zkontrolujte, zda jsou vaše přihlašovací údaje stále platné.
   
   Pokud používáte integrovaný modul runtime (IR) s místním hostováním, proveďte tento krok z virtuálního počítače nebo počítače, kde je nainstalováno prostředí IR pro místní hostování. Pak zkuste úlohu odeslat z Data Factory znovu.

   Další informace najdete v tématu [webové uživatelské rozhraní pro Ambari](../hdinsight/hdinsight-hadoop-manage-ambari.md#ambari-web-ui).

 </br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou `User admin is locked out in Ambari` nebo `Unauthorized: Ambari user name or password is incorrect` , přihlašovací údaje pro HDInsight jsou nesprávné nebo vypršela jejich platnost.

- **Doporučení**: Opravte přihlašovací údaje a znovu nasaďte propojenou službu. Nejdřív ověřte, že přihlašovací údaje fungují v HDInsight tím, že otevřete identifikátor URI clusteru v jakémkoli prohlížeči a zkusíte se přihlásit. Pokud přihlašovací údaje nefungují, můžete je resetovat z Azure Portal.

   V případě clusteru ESP obnovte heslo pomocí [samoobslužného resetování hesla](../active-directory/user-help/active-directory-passwords-update-your-own-password.md).

 </br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou `502 - Web server received an invalid response while acting as a gateway or proxy server` této, služba HDInsight tuto chybu vrátí.

- **Doporučení**: při vypnutí procesu serveru Ambari často dochází k chybě 502. Služby Ambari můžete restartovat restartováním hlavního uzlu.

    1. Připojte se k jednomu z vašich uzlů v HDInsight pomocí SSH.
    1. Identifikujte aktivního hostitele hlavního uzlu spuštěním `ping headnodehost` .
    1. Připojte se k aktivnímu hlavnímu uzlu jako server Ambari umístěný na aktivním hlavním uzlu pomocí SSH. 
    1. Restartujte aktivní hlavní uzel.

       Další informace najdete v dokumentaci k řešení problémů Azure HDInsight. Například:

       * [Chyba 502 uživatelského rozhraní Ambari](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [RpcTimeoutException pro server služby Apache Spark Thrift](../hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception.md)
       * [Řešení chyb chybných bran v Application Gateway](../application-gateway/application-gateway-troubleshooting-502.md).

 </br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou `Unable to service the submit job request as templeton service is busy with too many submit job requests` nebo, je `Queue root.joblauncher already has 500 applications, cannot accept submission of application` příliš mnoho úloh odesíláno do HDInsight současně.

- **Doporučení**: Omezte počet souběžných úloh odeslaných do HDInsight. Pokud jsou úlohy odesílány stejnou aktivitou, přečtěte si téma Data Factory souběžnosti aktivity. Změna aktivačních událostí, aby souběžné běhy byly rozloženy v průběhu času.

   Informace o tom, jak Chyba naznačuje, najdete v [dokumentaci k HDInsight](../hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors.md) `templeton.parallellism.job.submit` .

### <a name="error-code-2301"></a>Kód chyby: 2301

- **Zpráva**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Příčina**: cluster HDInsight nebo služba má problémy.

- **Doporučení**: k této chybě dochází, když ADF neobdrží odpověď z clusteru HDInsight při pokusu o vystavení stavu spuštěné úlohy. Tento problém může být v samotném clusteru nebo služba HDInsight může mít výpadek.

   Další pomoc najdete v dokumentaci pro řešení potíží s HDInsight na adrese https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide nebo kontaktujte podporu.

### <a name="error-code-2302"></a>Kód chyby: 2302

- **Zpráva**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Příčina**: úloha byla odeslána do clusteru HDI a v ní došlo k chybě.

- **Doporučení**: 

 1. Ověřte uživatelské rozhraní Ambari:
    1. Ujistěte se, že všechny služby jsou pořád spuštěné.
    1. V uživatelském rozhraní Ambari v řídicím panelu se podívejte na část výstrahy.
       1. Další informace o výstrahách a řešeních výstrah najdete v tématu [Správa a monitorování clusteru](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Zkontrolujte paměť vlákna. Pokud je paměť PŘÍZe vysoká, zpracování úloh může být zpožděno. Pokud nemáte dostatek prostředků pro přizpůsobení aplikace nebo úlohy Sparku, naplánujte horizontální navýšení kapacity clusteru, abyste zajistili, že cluster má dostatek paměti a jader. 
 1. Spusťte ukázkovou úlohu testování.
    1. Pokud spustíte stejnou úlohu v back-endu HDInsight, ověřte, že je úspěšná. Příklady spuštění ukázek najdete v tématu [spuštění příkladů MapReduce obsažených v HDInsight](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) . 
 1. Pokud se úloha ve službě HDInsight stále nezdařila, podívejte se do protokolů a informací o aplikacích, které vám poskytnou podporu:
    1. Ověřte, zda byla úloha odeslána do PŘÍZe. Pokud se úloha neodeslala do příze, použijte `--master yarn` .
    1. Pokud aplikace dokončila provádění, shromážděte čas spuštění a čas ukončení aplikace PŘÍZe. Pokud aplikace nedokončila provádění, shromážděte čas spuštění/čas spuštění.
    1. Kontrolovat a shromažďovat protokol aplikace pomocí `yarn logs -applicationId <Insert_Your_Application_ID>` .
    1. Zaškrtněte a Shromážděte protokoly příz Správce prostředků v `/var/log/hadoop-yarn/yarn` adresáři.
    1. Pokud tyto kroky nestačí k vyřešení problému, obraťte se na tým Azure HDInsight pro podporu a poskytněte výše uvedené protokoly a časová razítka.

### <a name="error-code-2303"></a>Kód chyby: 2303

- **Zpráva**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Příčina**: úloha byla odeslána do clusteru HDI a v ní došlo k chybě.

- **Doporučení**: 

 1. Ověřte uživatelské rozhraní Ambari:
    1. Ujistěte se, že všechny služby jsou pořád spuštěné.
    1. V uživatelském rozhraní Ambari v řídicím panelu se podívejte na část výstrahy.
       1. Další informace o výstrahách a řešeních výstrah najdete v tématu [Správa a monitorování clusteru](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Zkontrolujte paměť vlákna. Pokud je paměť PŘÍZe vysoká, zpracování úloh může být zpožděno. Pokud nemáte dostatek prostředků pro přizpůsobení aplikace nebo úlohy Sparku, naplánujte horizontální navýšení kapacity clusteru, abyste zajistili, že cluster má dostatek paměti a jader. 
 1. Spusťte ukázkovou úlohu testování.
    1. Pokud spustíte stejnou úlohu v back-endu HDInsight, ověřte, že je úspěšná. Příklady spuštění ukázek najdete v tématu [spuštění příkladů MapReduce obsažených v HDInsight](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) . 
 1. Pokud se úloha ve službě HDInsight stále nezdařila, podívejte se do protokolů a informací o aplikacích, které vám poskytnou podporu:
    1. Ověřte, zda byla úloha odeslána do PŘÍZe. Pokud se úloha neodeslala do příze, použijte `--master yarn` .
    1. Pokud aplikace dokončila provádění, shromážděte čas spuštění a čas ukončení aplikace PŘÍZe. Pokud aplikace nedokončila provádění, shromážděte čas spuštění/čas spuštění.
    1. Kontrolovat a shromažďovat protokol aplikace pomocí `yarn logs -applicationId <Insert_Your_Application_ID>` .
    1. Zaškrtněte a Shromážděte protokoly příz Správce prostředků v `/var/log/hadoop-yarn/yarn` adresáři.
    1. Pokud tyto kroky nestačí k vyřešení problému, obraťte se na tým Azure HDInsight pro podporu a poskytněte výše uvedené protokoly a časová razítka.

### <a name="error-code-2304"></a>Kód chyby: 2304

- **Zpráva**: `MSI authentication is not supported on storages for HDI activities.`

- **Příčina**: propojené služby úložiště, které se používají v aktivitě propojené služby HDInsight (HDI) nebo HDI, se konfigurují s ověřováním pomocí MSI, které se nepodporuje.

- **Doporučení**: Poskytněte úplné připojovací řetězce pro účty úložiště použité v aktivitě propojené služby HDI nebo HDI.

### <a name="error-code-2305"></a>Kód chyby: 2305

- **Zpráva**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Příčina**: informace o připojení pro cluster HDI nejsou správné, zadaný uživatel nemá oprávnění k provedení požadované akce nebo služba HDInsight obsahuje problémy, které odpovídají požadavkům z ADF.

- **Doporučení**: Ověřte, zda jsou informace o uživateli správné a že uživatelské rozhraní Ambari pro cluster HDI lze otevřít v prohlížeči z virtuálního počítače, ve kterém je NAINSTALOVÁNo infračervené prostředí (pro prostředí IR v místním prostředí), nebo je lze otevřít z libovolného počítače (pro Azure IR).

### <a name="error-code-2306"></a>Kód chyby: 2306

- **Zpráva**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Příčina**: zadaný kód JSON pro akci skriptu je neplatný.

- **Doporučení**: chybová zpráva by měla pomáhat při identifikaci problému. Opravte konfiguraci JSON a zkuste to znovu.

   Další informace najdete [v propojené službě na vyžádání Azure HDInsight](./compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .

### <a name="error-code-2310"></a>Kód chyby: 2310

- **Zpráva**: `Failed to submit Spark job. Error: '%message;'`

- **Příčina**: služba ADF se pokusila vytvořit dávku v clusteru Spark pomocí rozhraní Livy API (Livy/Batch), ale přijala chybu.

- **Doporučení**: Pokud chcete problém vyřešit, postupujte podle chybové zprávy. Pokud není k dispozici dostatek informací, aby je bylo možné vyřešit, obraťte se na tým HDI a poskytněte mu ID dávky a ID úlohy, které lze nalézt v části výstup spuštění aktivit na stránce monitorování ADF. Chcete-li dále řešit problémy, shromážděte úplný protokol úlohy Batch.

   Další informace o tom, jak shromáždit úplný protokol, najdete v tématu [získání úplného protokolu dávkové úlohy](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2312"></a>Kód chyby: 2312

- **Zpráva**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Příčina**: Úloha selhala v clusteru HDInsight Spark.

- **Doporučení**: pomocí odkazů na výstupu spuštění aktivit na stránce monitorování ADF můžete řešit potíže s spuštěním v clusteru HDInsight Spark. Pokud potřebujete další pomoc, obraťte se na tým podpory HDInsight.

   Další informace o tom, jak shromáždit úplný protokol, najdete v tématu [získání úplného protokolu dávkové úlohy](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2313"></a>Kód chyby: 2313

- **Zpráva**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Příčina**: dávka se odstranila v clusteru HDInsight Spark.

- **Doporučení**: řešení potíží se dávkami v clusteru HDInsight Spark. Pokud potřebujete další pomoc, obraťte se na podporu služby HDInsight. 

   Další informace o tom, jak shromáždit úplný protokol, najdete v tématu [získání úplného protokolu úlohy služby Batch](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)a sdílení úplného protokolu s podporou služby HDInsight pro další pomoc.

### <a name="error-code-2328"></a>Kód chyby: 2328

- **Zpráva**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Příčina**: chybová zpráva by měla zobrazovat podrobnosti o tom, co se pokazilo.

- **Doporučení**: chybová zpráva by měla pomoct při odstraňování problému.

### <a name="error-code-2329"></a>Kód chyby: 2329

- **Zpráva**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Příčina**: chybová zpráva by měla zobrazovat podrobnosti o tom, co se pokazilo.

- **Doporučení**: chybová zpráva by měla pomoct při odstraňování problému.

### <a name="error-code-2331"></a>Kód chyby: 2331

- **Zpráva**: `The file path should not be null or empty.`

- **Příčina**: Zadaná cesta k souboru je prázdná.

- **Doporučení**: zadejte cestu k souboru, který existuje.

### <a name="error-code-2340"></a>Kód chyby: 2340

- **Zpráva**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Příčina**: propojená služba HDInsightOnDemand nepodporuje spouštění prostřednictvím SelfHosted IR.

- **Doporučení**: vyberte Azure IR a zkuste to znovu.

### <a name="error-code-2341"></a>Kód chyby: 2341

- **Zpráva**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Příčina**: Zadaná adresa URL není ve správném formátu.

- **Doporučení**: Opravte adresu URL clusteru a zkuste to znovu.

### <a name="error-code-2342"></a>Kód chyby: 2342

- **Zpráva**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Příčina**: buď jsou zadané přihlašovací údaje pro cluster chybné, nebo došlo k potížím s konfigurací sítě nebo se jedná o problém s připojením, nebo má IR problémy s připojením ke clusteru.

- **Doporučení**: 
    1. Otevřete uživatelské rozhraní Ambari clusteru HDInsight v prohlížeči a ověřte, jestli jsou přihlašovací údaje správné.
    1. Pokud je cluster v Virtual Network (VNet) a používá se místní prostředí IR, adresa URL HDI musí být privátní adresa URL v virtuální sítě a měla by obsahovat znak-int, který je uvedený za názvem clusteru.
    
       Například změňte `https://mycluster.azurehdinsight.net/` na `https://mycluster-int.azurehdinsight.net/` . Všimněte si `-int` `mycluster` , ale před `.azurehdinsight.net`
    1. Pokud je cluster ve virtuální síti, používá se v místním prostředí IR a privátní adresa URL se použila a připojení se ještě nezdařilo, virtuální počítač, na kterém je tento IR nainstalovaný, měl problémy s připojením k HDI. 
    
       Připojte se k virtuálnímu počítači, kde je nainstalováno INFRAČERVENé prostředí, a otevřete uživatelské rozhraní Ambari v prohlížeči. Pro cluster použijte soukromou adresu URL. Toto připojení by mělo fungovat z prohlížeče. Pokud ne, požádejte o další pomoc tým podpory HDInsight.
    1. Pokud se nepoužívá místní prostředí IR, cluster HDI by měl být přístupný veřejně. Otevřete uživatelské rozhraní Ambari v prohlížeči a ověřte, že se otevře. Pokud se v clusteru nebo službách nacházejí nějaké problémy, požádejte o pomoc tým podpory HDInsight.

       Adresa URL clusteru HDI, která se používá v propojené službě ADF, musí být přístupná pro službu ADF IR (v místním prostředí nebo Azure), aby se testovací připojení předávalo a aby běžely v práci. Tento stav lze ověřit otevřením adresy URL z prohlížeče buď z virtuálního počítače, nebo z libovolného veřejného počítače.

### <a name="error-code-2343"></a>Kód chyby: 2343

- **Zpráva**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Příčina**: uživatelské jméno nebo heslo je prázdné.

- **Doporučení**: zadejte správné přihlašovací údaje pro připojení k HDI a zkuste to znovu.

### <a name="error-code-2345"></a>Kód chyby: 2345

- **Zpráva**: `Failed to read the content of the hive script. Error: '%message;'`

- **Příčina**: soubor skriptu neexistuje nebo se ADF nepodařilo připojit k umístění skriptu.

- **Doporučení**: Ověřte, že skript existuje a že přidružená propojená služba obsahuje správná pověření pro připojení.

### <a name="error-code-2346"></a>Kód chyby: 2346

- **Zpráva**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Příčina**: služba ADF se pokusila o navázání připojení rozhraní ODBC (Open Database Connectivity) ke clusteru HDI a selhala s chybou.

- **Doporučení**: 

   1. Ověřte, že jste správně nastavili připojení JDBC (ODBC/Java Database Connectivity).
      1. Pokud pro JDBC používáte stejnou virtuální síť, můžete toto připojení získat z těchto důvodů:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Pokud chcete mít jistotu, že máte správnou JDBC sadu, přečtěte si téma [dotazování Apache Hive prostřednictvím ovladače JDBC v HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. V případě Open Database (ODB) si přečtěte článek [kurz: Apache Hive dotazů s rozhraním ODBC a prostředím PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) , abyste měli jistotu, že máte správnou instalaci. 
   1. Ověřte, že jsou aktivní a funguje Hiveserver2, metastore podregistru a Hiveserver2 Interactive. 
   1. Podívejte se na uživatelské rozhraní Ambari (UI):
      1. Ujistěte se, že všechny služby jsou pořád spuštěné.
      1. V uživatelském rozhraní Ambari se podívejte na část výstraha na řídicím panelu.
         1. Další informace o výstrahách a řešeních výstrah najdete v tématu [Správa a monitorování clusteru ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Pokud tyto kroky nestačí k vyřešení problému, obraťte se na tým Azure HDInsight.

### <a name="error-code-2347"></a>Kód chyby: 2347

- **Zpráva**: `Hive execution through ODBC failed with error message '%message;'.`

- **Příčina**: ADF odeslal skript podregistru pro provedení do clusteru HDI prostřednictvím připojení ODBC a skript se nezdařil na HDI.

- **Doporučení**: 

   1. Ověřte, že jste správně nastavili připojení JDBC (ODBC/Java Database Connectivity).
      1. Pokud pro JDBC používáte stejnou virtuální síť, můžete toto připojení získat z těchto důvodů:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Pokud chcete mít jistotu, že máte správnou JDBC sadu, přečtěte si téma [dotazování Apache Hive prostřednictvím ovladače JDBC v HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. V případě Open Database (ODB) si přečtěte článek [kurz: Apache Hive dotazů s rozhraním ODBC a prostředím PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) , abyste měli jistotu, že máte správnou instalaci. 
   1. Ověřte, že jsou aktivní a funguje Hiveserver2, metastore podregistru a Hiveserver2 Interactive. 
   1. Podívejte se na uživatelské rozhraní Ambari (UI):
      1. Ujistěte se, že všechny služby jsou pořád spuštěné.
      1. V uživatelském rozhraní Ambari se podívejte na část výstraha na řídicím panelu.
         1. Další informace o výstrahách a řešeních výstrah najdete v tématu [Správa a monitorování clusteru ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Pokud tyto kroky nestačí k vyřešení problému, obraťte se na tým Azure HDInsight.

### <a name="error-code-2348"></a>Kód chyby: 2348

- **Zpráva**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Příčina**: vlastnosti propojené služby úložiště nejsou správně nastavené.

- **Doporučení**: v hlavní propojené službě úložiště pro aktivity HDI se podporují jenom úplné připojovací řetězce. Ověřte, že nepoužíváte autorizaci a aplikace MSI.

### <a name="error-code-2350"></a>Kód chyby: 2350

- **Zpráva**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Příčina**: zadané přihlašovací údaje pro připojení k úložišti, kde se mají soubory umístit, jsou nesprávné nebo soubory zde neexistují.

- **Doporučení**: k této chybě dochází, když se ADF připraví pro aktivity HDI a pokusí se zkopírovat soubory do hlavního úložiště před odesláním úlohy do HDI. Ověřte, zda soubory existují v zadaném umístění a zda je připojení úložiště správné. Jako aktivity HDI ADF nepodporují ověřování MSI u účtů úložiště souvisejících s aktivitami HDI, ověřte, že tyto propojené služby mají úplné klíče nebo používají Azure Key Vault.

### <a name="error-code-2351"></a>Kód chyby: 2351

- **Zpráva**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Příčina**: soubor na zadané cestě neexistuje.

- **Doporučení**: Ověřte, zda soubor skutečně existuje a zda propojená služba s informacemi o připojení odkazující na tento soubor má správné přihlašovací údaje.

### <a name="error-code-2352"></a>Kód chyby: 2352

- **Zpráva**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Příčina**: vlastnosti propojené služby File Storage nejsou nastaveny správně.

- **Doporučení**: Ověřte, že jsou správně nakonfigurované vlastnosti propojené služby File Storage.

### <a name="error-code-2353"></a>Kód chyby: 2353

- **Zpráva**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Příčina**: vlastnosti propojené služby úložiště skriptu nejsou správně nastavené.

- **Doporučení**: Ověřte, že jsou správně nakonfigurované vlastnosti propojené služby úložiště skriptů.

### <a name="error-code-2354"></a>Kód chyby: 2354

- **Zpráva**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Příčina**: typ propojené služby úložiště není touto aktivitou podporován.

- **Doporučení**: Ověřte, zda vybraná propojená služba obsahuje jeden z podporovaných typů aktivity. Aktivity HDI podporují propojené služby AzureBlobStorage a AzureBlobFSStorage.

   Další informace najdete v článku [porovnání možností úložiště pro použití s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-compare-storage-options.md) .

### <a name="error-code-2355"></a>Kód chyby: 2355

- **Zpráva**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Příčina**: zadaná hodnota pro `commandEnvironment` není správná.

- **Doporučení**: Ověřte, zda je zadaná hodnota podobná této:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Ověřte také, že se všechny proměnné v seznamu zobrazí pouze jednou.

### <a name="error-code-2356"></a>Kód chyby: 2356

- **Zpráva**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **Příčina**: zadaná hodnota pro `commandEnvironment` není správná.

- **Doporučení**: Ověřte, zda je zadaná hodnota podobná této:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Ověřte také, že se všechny proměnné v seznamu zobrazí pouze jednou.

### <a name="error-code-2357"></a>Kód chyby: 2357

- **Zpráva**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Příčina**: zadané přihlašovací údaje nejsou správné.

- **Doporučení**: Ověřte, zda jsou informace o připojení v adls Gen 1 propojené se službou a ověřte, zda je test připojení úspěšný.

### <a name="error-code-2358"></a>Kód chyby: 2358

- **Zpráva**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Příčina**: zadaná hodnota pro požadovanou vlastnost `TimeToLive` má neplatný formát. 

- **Doporučení**: Aktualizujte hodnotu na Navrhovaný rozsah a zkuste to znovu.

### <a name="error-code-2359"></a>Kód chyby: 2359

- **Zpráva**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Příčina**: zadaná hodnota pro vlastnost `roles` je neplatná.

- **Doporučení**: Aktualizujte hodnotu tak, aby byla jedním z návrhů, a zkuste to znovu.

### <a name="error-code-2360"></a>Kód chyby: 2360

- **Zpráva**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Příčina**: zadaný připojovací řetězec pro `HCatalogLinkedService` je neplatný.

- **Doporučení**: Aktualizujte hodnotu na správný připojovací řetězec Azure SQL a zkuste to znovu.

### <a name="error-code-2361"></a>Kód chyby: 2361

- **Zpráva**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Příčina**: Vytvoření clusteru se nezdařilo a služba ADF se nedostala zpět do služby HDInsight.

- **Doporučení**: otevřete Azure Portal a pokuste se najít prostředek HDI se zadaným názvem a pak zkontrolujte stav zřizování. Pokud potřebujete další pomoc, obraťte se na tým podpory HDInsight.

### <a name="error-code-2362"></a>Kód chyby: 2362

- **Zpráva**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Příčina**: zadané dodatečné úložiště nebylo úložištěm objektů BLOB v Azure.

- **Doporučení**: Poskytněte účet Azure Blob Storage jako dodatečné úložiště pro propojenou službu HDInsight na vyžádání.

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>Při použití propojené služby ADF pomocí clusteru HDInsight ESP došlo k chybě SSL.

- **Zpráva**: `Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **Příčina**: problém je pravděpodobně v souvislosti s úložištěm vztahů důvěryhodnosti systému.

- **Řešení**: můžete přejít do cesty **Microsoft Integration Runtime\4.0\Shared\ODBC DRIVERS\MICROSOFT podregistru ODBC Driver\lib** a otevřít DriverConfiguration64.exe pro změnu nastavení.

    ![Zrušit kontrolu použití úložiště důvěryhodnosti systému](./media/connector-troubleshoot-guide/system-trust-store-setting.png)

## <a name="web-activity"></a>Aktivita webu

### <a name="error-code-2128"></a>Kód chyby: 2128

- **Zpráva**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Příčina**: k tomuto problému dochází buď v důsledku připojení k síti, selhání DNS, ověření certifikátu serveru nebo vypršení časového limitu.

- **Doporučení**: Ověřte, že koncový bod, který se snažíte dosáhnout, reaguje na požadavky. Můžete použít nástroje jako **Fiddler/post**.

### <a name="error-code-2108"></a>Kód chyby: 2108

- **Zpráva**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Příčina**: požadavek se nezdařil z důvodu základního problému, například připojení k síti, selhání DNS, ověření certifikátu serveru nebo vypršení časového limitu.

- **Doporučení**: k ověření žádosti použijte Fiddler/Poster.

#### <a name="more-details"></a>Další podrobnosti
Použití **Fiddler** k vytvoření relace HTTP monitorované webové aplikace:

1. Stáhněte, nainstalujte a otevřete [Fiddler](https://www.telerik.com/download/fiddler).

1. Pokud vaše webová aplikace používá protokol HTTPS, použijte   >  **možnost nástroje Fiddler možnosti**  >  **https**.

   1. Na kartě HTTPS vyberte obě **zachytávání https připojení** i **dešifrování přenosu HTTPS**.

      ![Fiddler možnosti](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Pokud vaše aplikace používá certifikáty TLS/SSL, přidejte do svého zařízení certifikát Fiddler.

   Přejít na: **nástroje**  >  **Fiddler možnosti**  >  **https**  >  **Akce**  >  **exportovat kořenový certifikát do počítače**.

1. Vypněte zachytávání pomocí přechodu na   >  **přenos** souborů. Nebo stiskněte klávesu **F12**.

1. Vymažte mezipaměť prohlížeče, aby se odstranily všechny položky v mezipaměti, a je nutné je znovu stáhnout.

1. Vytvořit požadavek:

1. Vyberte kartu **Autor** .

   1. Nastavte metodu HTTP a adresu URL.
 
   1. V případě potřeby přidejte záhlaví a text žádosti.

   1. Vyberte **Execute** (Provést).

1. Zapněte znovu zachytávání provozu a dokončete problematickou transakci na stránce.

1. Přejít na: **soubor**  >  **Uložit**  >  **všechny relace**.

Další informace najdete v tématu [Začínáme s Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="general"></a>Obecné

### <a name="activity-stuck-issue"></a>Problém zablokování aktivity

Když zjistíte, že aktivita běží mnohem déle než normální běhy, ale zlomek žádný pokrok, může se stát zablokovat. Můžete to zkusit zrušit a potom se pokusit znovu zjistit, jestli pomáhá. Pokud se jedná o aktivitu kopírování, můžete získat informace o monitorování výkonu a odstraňování potíží [při odstraňování potíží s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md). Pokud se jedná o tok dat, přečtěte si příručku o výkonu a Průvodci optimalizací [datových toků](concepts-data-flow-performance.md) .

### <a name="payload-is-too-large"></a>Datová část je moc velká.

**Chybová zpráva:**`The payload including configurations on activity/dataSet/linked service is too large. Please check if you have settings with very large value and try to reduce its size.`

**Příčina:** Datová část každého spuštění aktivit zahrnuje konfiguraci aktivity, přidružené datové sady a konfigurace propojených služeb, pokud existují, a malou část vlastností systému generovaných na typ aktivity. Limit takové velikosti datové části je 896 KB, jak je uvedeno v části [omezení Data Factory](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) .

**Doporučení:** Toto omezení je pravděpodobně způsobeno tím, že předáváte jednu nebo více velkých hodnot parametrů buď z nadřazeného výstupu aktivity, nebo z externího, zejména Pokud předáváte skutečná data napříč aktivitami v toku řízení. Ověřte, jestli můžete zmenšit velikost velkých hodnot parametrů, nebo vyladit logiku kanálu, aby nedocházelo k předávání těchto hodnot napříč aktivitami, a místo toho ji prozpracujte uvnitř aktivity.

## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

* [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
* [Videa Azure](https://azure.microsoft.com/resources/videos/index/)
* [Stránka s otázkou Microsoft Q&](/answers/topics/azure-data-factory.html)
