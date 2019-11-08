---
title: Řešení potíží s Azure Data Factory | Microsoft Docs
description: Přečtěte si, jak řešit potíže s externími ovládacími prvky v Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 0026aa377a58f6b766a400860692a35440deb962
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748354"
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

- **Zpráva**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Příčina**: objekt služby nebo certifikát nemá přístup k souboru v úložišti.

- **Doporučení**: Ujistěte se, že instanční objekt nebo certifikát, který uživatel poskytuje pro úlohy Data Lake Analytics, má přístup k účtu Data Lake Analytics a výchozí instanci Data Lake Storage z kořenové složky.


### <a name="error-code--2711"></a>Kód chyby: 2711

- **Zpráva**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Příčina**: objekt služby nebo certifikát nemá přístup k souboru v úložišti.

- **Doporučení**: Ujistěte se, že instanční objekt nebo certifikát, který uživatel poskytuje pro úlohy Data Lake Analytics, má přístup k účtu Data Lake Analytics a výchozí instanci Data Lake Storage z kořenové složky.

<br/>  

- **Zpráva**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Příčina**: cesta k souboru U-SQL je chybná nebo přihlašovací údaje propojené služby nemají přístup.

- **Doporučení**: Ověřte cestu a přihlašovací údaje, které jsou k dispozici v propojené službě.


### <a name="error-code--2704"></a>Kód chyby: 2704

- **Zpráva**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

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

- **Zpráva**: `Invalid HttpMethod: '%method;'.`

- **Příčina**: metoda HTTP zadaná v datové části aktivity není podporována aktivitou funkce Azure Functions.

- **Doporučení**: podporované metody HTTP jsou PUT, post, Get, DELETE, Options, Head a Trace.


### <a name="error-code--3603"></a>Kód chyby: 3603

- **Zpráva**: `Response Content is not a valid JObject.`

- **Příčina**: volaná funkce Azure nevrátila v odpovědi datovou část JSON. Aktivita Azure Functions ADF podporuje jenom obsah odpovědi JSON.

- **Doporučení**: aktualizujte funkci Azure Functions, aby vrátila platnou datovou část JSON, např. C# funkce může vracet (ActionResult) New OkObjectResult ("{\"Id\":\"123\"});


### <a name="error-code--3606"></a>Kód chyby: 3606

- **Zpráva**: aktivita funkce Azure postrádá klíč funkce.

- **Příčina**: definice aktivity funkce Azure není dokončená.

- **Doporučení**: Zkontrolujte, zda definice JSON vstupní AzureFunction aktivity obsahuje vlastnost s názvem functionKey.


### <a name="error-code--3607"></a>Kód chyby: 3607

- **Zpráva**: `Azure function activity missing function name.`

- **Příčina**: definice aktivity funkce Azure není dokončená.

- **Doporučení**: Zkontrolujte, zda definice JSON vstupní AzureFunction aktivity obsahuje vlastnost s názvem "název funkce".


### <a name="error-code--3608"></a>Kód chyby: 3608

- **Zpráva**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

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

- **Doporučení**: Zkontrolujte prosím, že definice JSON vstupní AzureFunction aktivity obsahuje vlastnost s názvem Method.


### <a name="error-code--3612"></a>Kód chyby: 3612

- **Zpráva**: `Azure function activity missing LinkedService definition in JSON.`

- **Příčina**: definice aktivity funkce Azure není dokončená.

- **Doporučení**: Zkontrolujte, zda definice JSON vstupní AzureFunction aktivity obsahuje propojené služby.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Kód chyby: 4101

- **Zpráva**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Příčina**: chybný formát nebo chybějící definice vlastnosti% PropertyName;.

- **Doporučení**: Zkontrolujte prosím, jestli aktivita% Activity; má definovanou vlastnost% PropertyName; se správnými daty.


### <a name="error-code--4110"></a>Kód chyby: 4110

- **Zpráva**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Příčina**: definice aktivity AzureMLExecutePipeline není dokončena.

- **Doporučení**: Zkontrolujte prosím, jestli vstupní definice JSON pro aktivitu AzureMLExecutePipeline obsahuje propojené informace o službě.


### <a name="error-code--4111"></a>Kód chyby: 4111

- **Zpráva**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Příčina**: Nesprávná definice aktivity.

- **Doporučení**: Zkontrolujte prosím, jestli vstupní definice JSON pro aktivitu AzureMLExecutePipeline neobsahuje správné podrobnosti propojené služby.


### <a name="error-code--4112"></a>Kód chyby: 4112

- **Zpráva**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Příčina**: chybný formát nebo chybějící definice vlastnosti% PropertyName;.

- **Doporučení**: Zkontrolujte, jestli je u propojené služby definovaná vlastnost% PropertyName; se správnými daty.


### <a name="error-code--4121"></a>Kód chyby: 4121

- **Zpráva**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Příčina**: vypršela platnost přihlašovacích údajů použitých pro přístup ke službě Azure ml.

- **Doporučení**: Ověřte, že přihlašovací údaje jsou platné, a zkuste to znovu.


### <a name="error-code--4122"></a>Kód chyby: 4122

- **Zpráva**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Příčina**: přihlašovací údaje zadané v propojené službě Azure ml jsou neplatné nebo nemají oprávnění k této operaci.

- **Doporučení**: Ověřte, že přihlašovací údaje v propojené službě jsou platné a mají oprávnění pro přístup ke službě Azure ml.


### <a name="error-code--4123"></a>Kód chyby: 4123

- **Zpráva**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Příčina**: `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Doporučení**: Zkontrolujte hodnoty vlastností aktivity, aby odpovídaly očekávané datové části publikovaného kanálu Azure ml zadaného v propojené službě.


### <a name="error-code--4124"></a>Kód chyby: 4124

- **Zpráva**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Příčina**: publikovaný koncový bod kanálu Azure ml neexistuje.

- **Doporučení**: Ověřte, že publikovaný koncový bod kanálu Azure ml zadaný v propojené službě existují ve službě Azure ml.


### <a name="error-code--4125"></a>Kód chyby: 4125

- **Zpráva**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Příčina**: Chyba serveru ve službě Azure ml.

- **Doporučení**: zkuste to prosím znovu později. Pokud problém trvá, obraťte se na tým služby Azure ML.


### <a name="error-code--4126"></a>Kód chyby: 4126

- **Zpráva**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure ML Service for more error logs.`

- **Příčina**: selhalo spuštění kanálu Azure ml.

- **Doporučení**: Zkontrolujte prosím službu Azure ml, kde najdete další protokoly chyb a opravte kanál ml.



## <a name="common"></a>Společné

### <a name="error-code--2103"></a>Kód chyby: 2103

- **Zpráva**: `Please provide value for the required property '%propertyName;'.`

- **Příčina**: hodnota vlastnosti nebyla poskytnuta, ale je vyžadována ve scénáři.

- **Doporučení**: zadejte hodnotu ze zprávy a zkuste to znovu.


### <a name="error-code--2104"></a>Kód chyby: 2104

- **Zpráva**: `The type of the property '%propertyName;' is incorrect.`

- **Příčina**: typ poskytnuté vlastnosti není podle očekávání.

- **Doporučení**: Opravte typ vlastnosti a zkuste to znovu.


### <a name="error-code--2105"></a>Kód chyby: 2105

- **Zpráva**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Příčina**: hodnota vlastnosti je neplatná nebo nemá očekávaný formát.

- **Doporučení**: vyhledejte dokumentaci pro danou vlastnost a ujistěte se, že zadaná hodnota má očekávaný formát a typ.


### <a name="error-code--2106"></a>Kód chyby: 2106

- **Zpráva**: `The storage connection string is invalid. %errorMessage;`

- **Příčina**: připojovací řetězec pro úložiště je neplatný nebo má nesprávný formát.

- **Doporučení**: přejděte prosím na Azure Portal, najděte úložiště, zkopírujte připojovací řetězec a vložte ho do propojené služby a zkuste to znovu.


### <a name="error-code--2108"></a>Kód chyby: 2108

- **Zpráva**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Příčina**: požadavek se nezdařil z důvodu základního problému, například připojení k síti, selhání služby DNS, ověřování certifikátu serveru nebo vypršení časového limitu.

- **Doporučení**: k ověření žádosti použijte Fiddler/Poster.


### <a name="error-code--2110"></a>Kód chyby: 2110

- **Zpráva**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Příčina**: propojená služba zadaná v aktivitě je nesprávná.

- **Doporučení**: Ujistěte se prosím, že typ propojené služby je jedním z podporovaných typů aktivity. Například pro aktivity HDI může být typ propojené služby HDInsight nebo HDInsightOnDemand.


### <a name="error-code--2111"></a>Kód chyby: 2111

- **Zpráva**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Příčina**: typ poskytnuté vlastnosti není podle očekávání.

- **Doporučení**: Opravte typ vlastnosti a zkuste to znovu.


### <a name="error-code--2112"></a>Kód chyby: 2112

- **Zpráva**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Příčina**: typ cloudu není podporovaný nebo ho nejde určit pro úložiště z EndpointSuffix.

- **Doporučení**: prosím, použijte úložiště v jiném cloudu a zkuste to znovu.


### <a name="error-code--2128"></a>Kód chyby: 2128

- **Zpráva**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Příčina**: připojení k síti, selhání služby DNS, ověřování certifikátu serveru nebo časový limit.

- **Doporučení**: Ověřte, že koncový bod, který se snažíte dosáhnout, reaguje na požadavky. Můžete použít nástroje jako Fiddler/post.



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

### <a name="error-code--200"></a>Kód chyby: 200

- **Zpráva**: `Unexpected error happened: '%error;'.`

- **Příčina**: došlo k internímu problému se službou.

- **Doporučení**: Pokud potřebujete další pomoc, obraťte se prosím na podporu ADF.


### <a name="error-code--201"></a>Kód chyby: 201

- **Zpráva**: `JobType %jobType; is not found.`

- **Příčina**: k dispozici je nový typ úlohy, který pomocí ADF nepodporuje.

- **Doporučení**: obraťte se prosím na tým podpory ADF a požádejte o další pomoc.


### <a name="error-code--202"></a>Kód chyby: 202

- **Zpráva**: `Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Příčina**: chybová zpráva by měla zobrazovat podrobnosti o tom, co se pokazilo.

- **Doporučení**: chybová zpráva by měla pomoct při odstraňování problému. Pokud k dispozici nejsou žádné informace, obraťte se na podporu ADF, kde najdete další nápovědu.


### <a name="error-code--203"></a>Kód chyby: 203

- **Zpráva**: `Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Příčina**: chybová zpráva by měla zobrazovat podrobnosti o tom, co se pokazilo.

- **Doporučení**: chybová zpráva by měla pomoct při odstraňování problému. Pokud k dispozici nejsou žádné informace, obraťte se na podporu ADF, kde najdete další nápovědu.


### <a name="error-code--204"></a>Kód chyby: 204

- **Zpráva**: `The resumption token is missing for runId '%runId;'.`

- **Příčina**: došlo k internímu problému se službou.

- **Doporučení**: Pokud potřebujete další pomoc, obraťte se prosím na podporu ADF.


### <a name="error-code--205"></a>Kód chyby: 205

- **Zpráva**: `Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Příčina**: při vytváření clusteru HDI na vyžádání došlo k chybě.

- **Doporučení**: Pokud potřebujete další pomoc, obraťte se prosím na podporu ADF.


### <a name="error-code--206"></a>Kód chyby: 206

- **Zpráva**: `The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Příčina**: došlo k vnitřnímu problému se službou, která to způsobila.

- **Doporučení**: může to být přechodný problém. Zkuste prosím úlohu zopakovat. Pokud potíže potrvají, obraťte se na podporu ADF a požádejte o další pomoc.


### <a name="error-code--207"></a>Kód chyby: 207

- **Zpráva**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Příčina**: při pokusu o zjištění oblasti z primárního účtu úložiště došlo k vnitřní chybě.

- **Doporučení**: zkuste jiné úložiště. V případě, že se nejedná o přijatelné řešení, kontaktujte tým podpory ADF a požádejte o další pomoc.


### <a name="error-code--208"></a>Kód chyby: 208

- **Zpráva**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Příčina**: při pokusu o čtení instančního objektu nebo při vytváření instancí ověřování MSI došlo k vnitřní chybě.

- **Doporučení**: zvažte prosím poskytnutí instančního objektu, který má oprávnění k vytvoření clusteru HDInsight v zadaném předplatném, a zkuste to znovu. V případě, že se nejedná o přijatelné řešení, obraťte se na tým podpory ADF a požádejte o další pomoc.


### <a name="error-code--2300"></a>Kód chyby: 2300

- **Zpráva**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou "vzdálenému názvu nelze přeložit". to může znamenat, že zadaný identifikátor URI clusteru je neplatný.


- **Doporučení**: Ujistěte se, že se cluster neodstranil a že je zadaný identifikátor URI správný. Po otevření identifikátoru URI v prohlížeči by se mělo zobrazit uživatelské rozhraní Ambari. Pokud je cluster ve virtuální síti, identifikátor URI by měl být privátním identifikátorem URI. Pokud ho chcete otevřít, použijte virtuální počítač, který je součástí stejné virtuální sítě. Další informace najdete v [tomto](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)tématu.
                  

<br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou "úloha byla zrušena.", znamená to, že odeslání úlohy vypršel časový limit.

- **Doporučení**: Tento problém může být buď obecným připojením HDInsight, nebo připojením k síti. Nejdřív potvrďte, že je uživatelské rozhraní HDInsight Ambari dostupné z libovolného prohlížeče. Potvrďte, že vaše přihlašovací údaje jsou pořád platné. Pokud používáte integrovaný modul runtime (IR) s místním hostováním, ujistěte se, že provedete tento postup z virtuálního počítače nebo počítače, ve kterém je nainstalováno prostředí IR pro místní hostování. Pak zkuste úlohu odeslat z Data Factory znovu. Pokud se to nepodaří, obraťte se na tým Data Factory pro podporu.

<br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou "Správci uživatelů je zamčený v Ambari" nebo "neoprávněné: Ambari uživatelské jméno nebo heslo není správné", znamená to, že přihlašovací údaje pro HDInsight nejsou správné nebo vypršela jejich platnost.

- **Doporučení**: Opravte přihlašovací údaje a znovu nasaďte propojenou službu. Nejdřív ověřte, že přihlašovací údaje fungují v HDInsight tím, že otevřete identifikátor URI clusteru v jakémkoli prohlížeči a zkusíte se přihlásit. Pokud přihlašovací údaje nefungují, můžete je resetovat z Azure Portal.

<br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou 502-webový server obdržel neplatnou odpověď v době, kdy funguje jako brána nebo proxy server, tato chyba je vrácena službou HDInsight.


- **Doporučení**: Prohlédněte si dokumentaci k řešení potíží se službou Azure HDInsight, například https://hdinsight.github.io/ambari/ambari-ui-502-error.htmlhttps://hdinsight.github.io/spark/spark-thriftserver-errors.htmlhttps://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502.
                  

<br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou této žádosti o odeslání žádosti o úlohu, protože služba Templeton je zaneprázdněná s příliš velkým počtem žádostí o odeslání úlohy nebo kořenem fronty. joblauncher už má 500 aplikací, nemůže přijmout odeslání. To znamená, že do HDInsight se současně odesílají příliš mnoho úloh.

- **Doporučení**: Zvažte omezení počtu souběžných úloh odeslaných do HDInsight. Pokud jsou úlohy odesílány stejnou aktivitou, přečtěte si téma Data Factory souběžnosti aktivity. Změna aktivačních událostí, aby souběžné běhy byly rozloženy v průběhu času. V dokumentaci k HDInsight si můžete upravit Templeton. parallellism. job. Submit, jak naznačuje chyba.


### <a name="error-code--2301"></a>Kód chyby: 2301

- **Zpráva**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Příčina**: cluster HDInsight nebo služba má problémy.


- **Doporučení**: k této chybě dochází, když ADF při pokusu o získání stavu spuštěné úlohy neobdrží odpověď z clusteru HDInsight. Může to způsobovat problémy v samotném clusteru nebo služba HDInsight může mít výpadek. Další pomoc najdete v dokumentaci k řešení potíží s HDInsight na https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide, nebo se obraťte na podporu.
                


### <a name="error-code--2302"></a>Kód chyby: 2302

- **Zpráva**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Příčina**: úloha byla odeslána do clusteru HDI a tam se nezdařila.

- **Doporučení**: postupujte podle odkazu protokoly příze ve výstupu spuštění aktivity a vyhledejte chyby ve výstupu HDI. Pokud potřebujete podporu, obraťte se na tým HDInsight.


### <a name="error-code--2303"></a>Kód chyby: 2303

- **Zpráva**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Příčina**: úloha byla odeslána do clusteru HDI a tam se nezdařila.

- **Doporučení**: postupujte podle odkazu protokoly příze ve výstupu spuštění aktivity a vyhledejte chyby ve výstupu HDI. Zkuste to znovu nebo v případě potřeby kontaktujte tým služby HDInsight.


### <a name="error-code--2304"></a>Kód chyby: 2304

- **Zpráva**: `MSI authentication is not supported on storages for HDI activities.`

- **Příčina**: propojené služby úložiště používané v HDI propojené službě nebo aktivitě HDI jsou nakonfigurované s ověřováním pomocí MSI, které se nepodporuje.

- **Doporučení**: Zadejte prosím úplné připojovací řetězce pro účty úložiště použité v aktivitě propojené služby HDI nebo HDI.


### <a name="error-code--2305"></a>Kód chyby: 2305

- **Zpráva**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Příčina**: informace o připojení pro cluster HDI nejsou správné, zadaný uživatel nemá oprávnění k provedení požadované akce nebo služba HDInsight měla problémy s odezvou na požadavky z ADF.

- **Doporučení**: Ujistěte se prosím, že jsou informace o uživateli správné. Ověřte také, že uživatelské rozhraní Ambari pro cluster HDI lze otevřít v prohlížeči z virtuálního počítače, ve kterém je nainstalováno infračervené vysílání v případě místního prostředí IR, nebo může být otevřeno z libovolného počítače v případě Azure IR.


### <a name="error-code--2306"></a>Kód chyby: 2306

- **Zpráva**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Příčina**: zadaný kód JSON pro akci skriptu je neplatný.


- **Doporučení**: chybová zpráva by měla pomáhat při identifikaci problému. Opravte prosím konfiguraci JSON a zkuste to znovu. Další informace najdete https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service.
                


### <a name="error-code--2310"></a>Kód chyby: 2310

- **Zpráva**: `Failed to submit Spark job. Error: '%message;'`

- **Příčina**: služba ADF se pokusila vytvořit dávku v clusteru Spark pomocí rozhraní Livy API (Livy/Batch), ale přijala chybu.

- **Doporučení**: Pokud chcete problém vyřešit, postupujte prosím podle chybové zprávy. Pokud není dostatek informací, aby je bylo možné vyřešit, obraťte se na tým HDI a poskytněte mu ID dávky a ID úlohy, které najdete v části výstup spuštění aktivit na stránce monitorování ADF.


### <a name="error-code--2312"></a>Kód chyby: 2312

- **Zpráva**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Příčina**: Úloha selhala v clusteru HDInsight Spark.

- **Doporučení**: při řešení potíží se spouštěním v clusteru HDInsight Spark prosím použijte odkazy na výstupu spuštění aktivit na stránce monitorování ADF. Pokud potřebujete další pomoc, obraťte se prosím na tým podpory HDInsight.


### <a name="error-code--2313"></a>Kód chyby: 2313

- **Zpráva**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Příčina**: dávka se odstranila v clusteru HDInsight Spark.

- **Doporučení**: řešení potíží se dávkami v clusteru HDInsight Spark. Pokud potřebujete další pomoc, obraťte se na podporu služby HDInsight. 


### <a name="error-code--2328"></a>Kód chyby: 2328

- **Zpráva**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Příčina**: `The error message should show the details of what went wrong.`

- **Doporučení**: chybová zpráva by měla pomoct při odstraňování problému.


### <a name="error-code--2329"></a>Kód chyby: 2329

- **Zpráva**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Příčina**: chybová zpráva by měla zobrazovat podrobnosti o tom, co se pokazilo.

- **Doporučení**: chybová zpráva by měla pomoct při odstraňování problému.


### <a name="error-code--2331"></a>Kód chyby: 2331

- **Zpráva**: `The file path should not be null or empty.`

- **Příčina**: Zadaná cesta k souboru je prázdná.

- **Doporučení**: zadejte cestu k souboru, který existuje.


### <a name="error-code--2340"></a>Kód chyby: 2340

- **Zpráva**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Příčina**: propojená služba HDInsightOnDemand nepodporuje spouštění prostřednictvím SelfHosted IR.

- **Doporučení**: vyberte prosím Azure IR a zkuste to znovu.


### <a name="error-code--2341"></a>Kód chyby: 2341

- **Zpráva**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Příčina**: Zadaná adresa URL není ve správném formátu.

- **Doporučení**: Opravte prosím adresu URL clusteru a zkuste to znovu.


### <a name="error-code--2342"></a>Kód chyby: 2342

- **Zpráva**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Příčina**: zadané přihlašovací údaje jsou pro cluster chybné nebo došlo k potížím s konfigurací sítě nebo se jedná o problém s připojením nebo se může stát, že při připojování ke clusteru dojde k problémům.

- **Doporučení**:  
      1. Otevřete uživatelské rozhraní Ambari clusteru HDInsight v prohlížeči a ověřte, jestli jsou přihlašovací údaje správné.
      2. Pokud je cluster ve virtuální síti a používá se místní prostředí IR, adresa URL HDI by měla být privátní adresou URL v virtuální sítě, to znamená, že po názvu clusteru musí obsahovat "-int". Například "https://mycluster.azurehdinsight.net/" by měl být změněn na "https://mycluster-int.azurehdinsight.net/".
      2. Pokud je cluster ve virtuální síti, používá se místní prostředí IR a privátní adresa URL se použila a připojení se pořád nezdařilo, virtuální počítač, ve kterém je nainstalovaná služba IR, má problémy s připojením k HDI. Připojte se k virtuálnímu počítači, kde je nainstalovaný IR, a otevřete uživatelské rozhraní Ambari v prohlížeči. Pro cluster použijte soukromou adresu URL. Toto připojení by mělo fungovat z prohlížeče. Pokud ne, požádejte o další pomoc tým podpory HDInsight.
      3. Pokud se nepoužívá místní prostředí IR, cluster HDI by měl být přístupný veřejně. Otevřete uživatelské rozhraní Ambari v prohlížeči a ujistěte se, že se otevře. Pokud se v clusteru nebo službách nacházejí nějaké problémy, požádejte o pomoc tým podpory HDInsight.
      Obecně platí, že adresa URL clusteru HDI, která se používá v propojené službě ADF, musí být přístupná pro službu ADF IR (v místním prostředí nebo Azure), aby bylo možné test připojení předat a aby fungovala. To se dá snadno ověřit otevřením této adresy URL z prohlížeče buď z virtuálního počítače, nebo z libovolného veřejného počítače.
    


### <a name="error-code--2343"></a>Kód chyby: 2343

- **Zpráva**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Příčina**: buď je uživatelské jméno nebo heslo prázdné.

- **Doporučení**: Poskytněte správné přihlašovací údaje pro připojení k HDI a zkuste to znovu.


### <a name="error-code--2345"></a>Kód chyby: 2345

- **Zpráva**: `Failed to read the content of the hive script. Error: '%message;'`

- **Příčina**: soubor skriptu neexistuje nebo se ADF nepodařilo připojit k umístění skriptu.

- **Doporučení**: Ověřte prosím, jestli tento skript existuje, a přidružená propojená služba má správné přihlašovací údaje pro připojení.


### <a name="error-code--2346"></a>Kód chyby: 2346

- **Zpráva**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Příčina**: služba ADF se pokusila vytvořit připojení ODBC ke clusteru HDI a selhala s chybou.

- **Doporučení**: chybová zpráva a kód chyby by měly pomáhat při řešení chyb připojení rozhraní ODBC. V případě, že k vyřešení problému nestačí, kontaktujte prosím tým Azure HDInsight, který vám poskytne podporu.


### <a name="error-code--2347"></a>Kód chyby: 2347

- **Zpráva**: `Hive execution through ODBC failed with error message '%message;'.`

- **Příčina**: služba ADF odeslala skript podregistru pro provedení do clusteru HDI prostřednictvím připojení ODBC a skript se nezdařil na HDI.

- **Doporučení**: spuštění skriptu podregistru v clusteru HDI se nezdařilo a chybová zpráva a kód chyby by měl pomáhat při řešení potíží. V případě, že k vyřešení problému nestačí, kontaktujte prosím tým Azure HDInsight, který vám poskytne podporu.


### <a name="error-code--2348"></a>Kód chyby: 2348

- **Zpráva**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Příčina**: vlastnosti propojené služby úložiště nejsou správně nastavené.

- **Doporučení**: v hlavní propojené službě úložiště pro aktivity HDI se podporují jenom úplné připojovací řetězce. Ujistěte se prosím, že nepoužíváte ověřování MSI nebo aplikace.


### <a name="error-code--2350"></a>Kód chyby: 2350

- **Zpráva**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Příčina**: zadané přihlašovací údaje pro připojení k úložišti, kde se mají soubory umístit, jsou nesprávné nebo soubory zde neexistují.

- **Doporučení**: k této chybě dochází, když ADF provede přípravu kroků pro aktivity HDI. Před odesláním úlohy do HDI se pokusí zkopírovat soubory do hlavního úložiště. Ujistěte se, že soubory existují v zadaném umístění, připojení úložiště je správné. Aktivity HDI ADF nepodporují ověřování MSI u účtů úložiště souvisejících s aktivitami HDI, takže se ujistěte, že tyto propojené služby mají úplné klíče nebo používají Azure Key Vault.


### <a name="error-code--2351"></a>Kód chyby: 2351

- **Zpráva**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Příčina**: soubor neexistuje v zadané cestě.

- **Doporučení**: Zkontrolujte, jestli soubor skutečně existuje a propojená služba s informacemi o připojení odkazující na tento soubor má správné přihlašovací údaje.


### <a name="error-code--2352"></a>Kód chyby: 2352

- **Zpráva**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Příčina**: vlastnosti propojené služby File Storage nejsou nastaveny správně.

- **Doporučení**: Ujistěte se prosím, že jsou správně nakonfigurované vlastnosti propojené služby File Storage.


### <a name="error-code--2353"></a>Kód chyby: 2353

- **Zpráva**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Příčina**: vlastnosti propojené služby úložiště skriptu nejsou správně nastavené.

- **Doporučení**: Ujistěte se prosím, že jsou správně nakonfigurované vlastnosti propojené služby úložiště skriptů.


### <a name="error-code--2354"></a>Kód chyby: 2354

- **Zpráva**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Příčina**: typ propojené služby úložiště není podporován aktivitou.

- **Doporučení**: Ujistěte se, že vybraná propojená služba má jeden z podporovaných typů aktivity. Aktivity HDI podporují propojené služby AzureBlobStorage a AzureBlobFSStorage.


### <a name="error-code--2355"></a>Kód chyby: 2355

- **Zpráva**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Příčina**: zadaná pro commandEnvironment je nesprávná.

- **Doporučení**:  
      Ujistěte se prosím, že zadaná hodnota je podobná: \"commandEnvironment\": [\"Variable = variableValue\"] a každá proměnná se v seznamu zobrazí jenom jednou.
    


### <a name="error-code--2356"></a>Kód chyby: 2356

- **Zpráva**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **Příčina**: proměnná byla v commandEnvironment zadána dvakrát.

- **Doporučení**:  
      Ujistěte se prosím, že zadaná hodnota je podobná: \"commandEnvironment\": [\"Variable = variableValue\"] a každá proměnná se v seznamu zobrazí jenom jednou.
    


### <a name="error-code--2357"></a>Kód chyby: 2357

- **Zpráva**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Příčina**: zadané přihlašovací údaje nejsou správné.

- **Doporučení**: Ověřte informace o připojení v propojené službě adls Gen 1 a ujistěte se, že je test připojení úspěšný.


### <a name="error-code--2358"></a>Kód chyby: 2358

- **Zpráva**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Příčina**: zadaná hodnota pro povinnou vlastnost ' TimeToLive ' má neplatný formát. 

- **Doporučení**: Aktualizujte hodnotu tak, aby byla v navrhovaném rozsahu, a akci opakujte.


### <a name="error-code--2359"></a>Kód chyby: 2359

- **Zpráva**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Příčina**: zadaná hodnota pro vlastnost Roles je neplatná.

- **Doporučení**: Aktualizujte prosím tuto hodnotu, aby byla jedním z návrhů, a zkuste to znovu.


### <a name="error-code--2360"></a>Kód chyby: 2360

- **Zpráva**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Příčina**: zadaný připojovací řetězec pro HCatalogLinkedService je neplatný.

- **Doporučení**: Aktualizujte prosím hodnotu na správný připojovací řetězec SQL Azure a zkuste to znovu.


### <a name="error-code--2361"></a>Kód chyby: 2361

- **Zpráva**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Příčina**: Vytvoření clusteru se nezdařilo a služba ADF se nedostala zpět do služby HDInsight.

- **Doporučení**: Otevřete portál Azure Portal a pokuste se najít prostředek HDI se zadaným názvem a ověřte stav zřizování. Pokud potřebujete další pomoc, obraťte se na tým podpory HDInsight.


### <a name="error-code--2362"></a>Kód chyby: 2362

- **Zpráva**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Příčina**: zadané dodatečné úložiště nebylo úložištěm objektů BLOB v Azure.

- **Doporučení**: Poskytněte účet Azure Blob Storage jako další úložiště pro propojenou službu HDInsight na vyžádání.



## <a name="web-activity"></a>Aktivita webu

### <a name="error-code--2128"></a>Kód chyby: 2128

- **Zpráva**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Příčina**: připojení k síti, selhání služby DNS, ověřování certifikátu serveru nebo časový limit.

- **Doporučení**: Ověřte, že koncový bod, který se snažíte dosáhnout, reaguje na požadavky. Můžete použít nástroje jako Fiddler/post.


### <a name="error-code--2108"></a>Kód chyby: 2108

- **Zpráva**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Příčina**: požadavek se nezdařil z důvodu základního problému, například připojení k síti, selhání služby DNS, ověřování certifikátu serveru nebo vypršení časového limitu.

- **Doporučení**: k ověření žádosti použijte Fiddler/Poster.
<br>


#### <a name="more-details"></a>Další podrobnosti
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


            
