---
title: Poradce při potížích s Azure Data Factory | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit potíže s externími aktivitami řízení v Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: e284060893e00ed7459edd0d1a03075c813dc5b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065381"
---
# <a name="troubleshoot-azure-data-factory"></a>Poradce při potížích s Azure Data Factory

Tento článek zkoumá běžné metody řešení potíží pro aktivity externího řízení v Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Aktivita konektoru a kopírování

Problémy s konektorem, například při výskytu chyby pomocí aktivity kopírování, najdete [v tématu Poradce při potížích s konektory Azure Data Factory .](connector-troubleshoot-guide.md)
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Kód chyby: 3200

- **Zpráva**: Chyba 403.

- **Příčina**:`The Databricks access token has expired.`

- **Doporučení:** Ve výchozím nastavení je přístupový token Azure Databricks platný po dobu 90 dnů. Vytvořte nový token a aktualizujte propojenou službu.


### <a name="error-code--3201"></a>Kód chyby: 3201

- **Zpráva**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Příčina**:`Bad authoring: Notebook path not specified correctly.`

- **Doporučení:** Zadejte cestu poznámkového bloku v aktivitě Databricks.

<br/>  

- **Zpráva**:`Cluster... does not exist.`

- **Příčina**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Doporučení**: Ověřte, zda existuje cluster Databricks.

<br/>  

- **Zpráva**:`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Příčina**:`Bad authoring.`

- **Doporučení:** Zadejte absolutní cesty pro schémata adresování pracovního prostoru nebo `dbfs:/folder/subfolder/foo.py` pro soubory uložené v systému souborů Databricks.

<br/>  

- **Zpráva**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Příčina**:`Bad authoring.`

- **Doporučení**: Ověřte [definici propojené služby](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Zpráva**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Příčina**:`Bad authoring.`

- **Doporučení**: Ověřte [definici propojené služby](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Zpráva**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Příčina**:`Bad authoring.`

- **Doporučení**: Podívejte se na chybovou zprávu.

<br/>

### <a name="error-code--3202"></a>Kód chyby: 3202

- **Zpráva**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Příčina**:`Too many Databricks runs in an hour.`

- **Doporučení:** Zkontrolujte všechny kanály, které používají tento pracovní prostor Databricks pro jejich míru vytváření pracovních míst.  Pokud kanály spustily příliš mnoho databricků, běží v agregátně, migrujte některé kanály do nového pracovního prostoru.

<br/>  

- **Zpráva**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Příčina**:`Authoring error: No value provided for the parameter.`

- **Doporučení:** Zkontrolujte kanál JSON a ujistěte se, že všechny parametry v poznámkovém bloku baseParameters určují neprázdnou hodnotu.

<br/>  

- **Zpráva** `User: `: SimpleUserContext{userId=...,user@company.comname= , orgId=...}` is not   authorized to access cluster.`

- **Příčina**: Uživateli, který vygeneroval přístupový token, není povolen přístup k clusteru Databricks určenému v propojené službě.

- **Doporučení:** Ujistěte se, že uživatel má požadovaná oprávnění v pracovním prostoru.


### <a name="error-code--3203"></a>Kód chyby: 3203

- **Zpráva**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Příčina**: Cluster byl ukončen. Pro interaktivní clustery to může být spor.

- **Doporučení**: Nejlepší způsob, jak se této chybě vyhnout, je použít clustery úloh.


### <a name="error-code--3204"></a>Kód chyby: 3204

- **Zpráva**:`Job execution failed.`

- **Příčina**: Chybové zprávy označují různé problémy, například neočekávaný stav clusteru nebo určitou aktivitu. Nejčastěji se nezobrazí žádná chybová zpráva.

- **Doporučení**: Není k mů e pojem
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Následující tabulka se vztahuje na U-SQL.
      
### <a name="error-code--2709"></a>Kód chyby: 2709

- **Zpráva**:`The access token is from the wrong tenant.`

- **Příčina:** Nesprávný klient Azure Active Directory (Azure AD).

- **Doporučení:** Nesprávný klient Azure Active Directory (Azure AD).

<br/>

- **Zpráva**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Příčina**: Tato chyba je způsobena omezením v analýze datového jezera.

- **Doporučení**: Snižte počet odeslaných úloh do služby Data Lake Analytics změnou aktivačních událostí datové továrny a nastavení souběžnosti s aktivitami. Nebo zvyšte limity pro Data Lake Analytics.

<br/>  

- **Zpráva**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Příčina**: Tato chyba je způsobena omezením v analýze datového jezera.

- **Doporučení**: Snižte počet odeslaných úloh do služby Data Lake Analytics změnou aktivačních událostí datové továrny a nastavení souběžnosti s aktivitami. Nebo zvyšte limity pro Data Lake Analytics.


### <a name="error-code--2705"></a>Kód chyby: 2705

- **Zpráva**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Příčina**: Instanční objekt nebo certifikát nemají přístup k souboru v úložišti.

- **Doporučení:** Ujistěte se, že instanční objekt nebo certifikát, který uživatel poskytuje pro úlohy Data Lake Analytics, má přístup k účtu Data Lake Analytics a výchozí instanci Úložiště datového jezera z kořenové složky.


### <a name="error-code--2711"></a>Kód chyby: 2711

- **Zpráva**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Příčina**: Instanční objekt nebo certifikát nemají přístup k souboru v úložišti.

- **Doporučení:** Ujistěte se, že instanční objekt nebo certifikát, který uživatel poskytuje pro úlohy Data Lake Analytics, má přístup k účtu Data Lake Analytics a výchozí instanci Úložiště datového jezera z kořenové složky.

<br/>  

- **Zpráva**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Příčina**: Cesta k souboru U-SQL je chybná nebo pověření propojené služby nemají přístup.

- **Doporučení:** Ověřte cestu a pověření poskytnutá v propojené službě.


### <a name="error-code--2704"></a>Kód chyby: 2704

- **Zpráva**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Příčina**: Instanční objekt nebo certifikát nemají přístup k souboru v úložišti.

- **Doporučení:** Ujistěte se, že instanční objekt nebo certifikát, který uživatel poskytuje pro úlohy Data Lake Analytics, má přístup k účtu Data Lake Analytics a výchozí instanci Úložiště datového jezera z kořenové složky.


### <a name="error-code--2707"></a>Kód chyby: 2707

- **Zpráva**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Příčina**: Účet Data Lake Analytics v propojené službě je chybný.

- **Doporučení**: Ověřte, zda je k dispozici správný účet.


### <a name="error-code--2703"></a>Kód chyby: 2703

- **Zpráva**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Příčina**: Chyba je z Data Lake Analytics.

- **Doporučení**: Chyba, jako je příklad, znamená, že úloha byla odeslána do služby Data Lake Analytics a tamní skript se nezdařil. Prozkoumejte v data lake analytics. Na portálu přejděte na účet Data Lake Analytics a vyhledejte úlohu pomocí ID spuštění aktivity datové továrny (ne ID spuštění kanálu). Úloha tam poskytuje další informace o chybě a pomůže vám řešit potíže. Pokud řešení není jasné, obraťte se na tým podpory Data Lake Analytics a zadejte adresu URL úlohy, která obsahuje název vašeho účtu a ID úlohy.
          

## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>Kód chyby: 3602

- **Zpráva**:`Invalid HttpMethod: '%method;'.`

- **Příčina**: Metoda Http zadaná v datové části aktivity není podporována aktivitou funkce Azure.

- **Doporučení:** Http metody, které jsou podporovány jsou PUT, POST, GET, DELETE, MOŽNOSTI, HEAD a TRACE.


### <a name="error-code--3603"></a>Kód chyby: 3603

- **Zpráva**:`Response Content is not a valid JObject.`

- **Příčina**: Azure funkce, která byla volána nevrátil a JSON datové části v odpovědi. Aktivita funkce ADF Azure podporuje pouze obsah odpovědí JSON.

- **Doporučení:** Aktualizace funkce Azure pro vrácení platné datové části JSON, například funkce Jazyka C# může\"vrátit\"\"(ActionResult)new OkObjectResult("{ Id : 123\"}");


### <a name="error-code--3606"></a>Kód chyby: 3606

- **Zpráva**: Funkce Azure aktivity chybí funkční klávesa.

- **Příčina**: Definice aktivity funkce Azure není úplná.

- **Doporučení:** Zkontrolujte vstupní AzureFunction activity JSON definice má vlastnost s názvem "functionKey".


### <a name="error-code--3607"></a>Kód chyby: 3607

- **Zpráva**:`Azure function activity missing function name.`

- **Příčina**: Definice aktivity funkce Azure není úplná.

- **Doporučení:** Zkontrolujte vstupní AzureFunction activity JSON definice má vlastnost s názvem "functionName".


### <a name="error-code--3608"></a>Kód chyby: 3608

- **Zpráva**:`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Příčina**: Podrobnosti funkce Azure v definici aktivity mohou být nesprávné.

- **Doporučení:** Opravte podrobnosti funkce azure a opakujte akci.


### <a name="error-code--3609"></a>Kód chyby: 3609

- **Zpráva**:`Azure function activity missing functionAppUrl.`

- **Příčina**: Definice aktivity funkce Azure není úplná.

- **Doporučení:** Zkontrolujte vstupní AzureFunction activity JSON definice má vlastnost s názvem "functionAppUrl".


### <a name="error-code--3610"></a>Kód chyby: 3610

- **Zpráva**:`There was an error while calling endpoint.`

- **Příčina**: Adresa URL funkce může být nesprávná.

- **Doporučení:** Ujistěte se, že hodnota 'functionAppUrl' v aktivitě JSON je správná a zkuste to znovu.


### <a name="error-code--3611"></a>Kód chyby: 3611

- **Zpráva**:`Azure function activity missing Method in JSON.`

- **Příčina**: Definice aktivity funkce Azure není úplná.

- **Doporučení:** Zkontrolujte vstupní AzureFunction activity JSON definice má vlastnost s názvem "metoda".


### <a name="error-code--3612"></a>Kód chyby: 3612

- **Zpráva**:`Azure function activity missing LinkedService definition in JSON.`

- **Příčina**: Definice aktivity funkce Azure není úplná.

- **Doporučení:** Zkontrolujte vstupní AzureFunction aktivity JSON definice má propojené podrobnosti o službě.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Kód chyby: 4101

- **Zpráva**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Příčina**: Chybný formát nebo chybějící definice vlastnosti %propertyName;.

- **Doporučení:** Zkontrolujte, zda aktivita %activityName;' má vlastnost %propertyName;' definovanou se správnými daty.


### <a name="error-code--4110"></a>Kód chyby: 4110

- **Zpráva**:`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Příčina**: Definice aktivity AzureMLExecutePipeline není dokončena.

- **Doporučení:** Zkontrolujte, zda má vstupní definice JSON aktivity AzureMLExecutePipeline propojené podrobnosti o službě.


### <a name="error-code--4111"></a>Kód chyby: 4111

- **Zpráva**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Příčina:** Nesprávná definice aktivity.

- **Doporučení:** Zkontrolujte, zda má vstupní definice JSON aktivity AzureMLExecutePipeline správné podrobnosti o propojené službě.


### <a name="error-code--4112"></a>Kód chyby: 4112

- **Zpráva**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Příčina**: Chybný formát nebo chybějící definice vlastnosti %propertyName;.

- **Doporučení:** Zkontrolujte, zda má propojená služba vlastnost %propertyName;' definovanou se správnými daty.


### <a name="error-code--4121"></a>Kód chyby: 4121

- **Zpráva**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Příčina**: Platnost pověření používaného pro přístup k Azure Machine Learning vypršela.

- **Doporučení:** Ověřte, zda je pověření platné a opakujte akci.


### <a name="error-code--4122"></a>Kód chyby: 4122

- **Zpráva**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Příčina**: Pověření poskytnutá v propojené službě Azure Machine Learning je neplatná nebo nemá oprávnění k operaci.

- **Doporučení:** Ověřte, zda jsou přihlašovací údaje v propojené službě platné a mají oprávnění k přístupu k Azure Machine Learning.


### <a name="error-code--4123"></a>Kód chyby: 4123

- **Zpráva**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Příčina**: Vlastnosti aktivity, jako je pipelineParameters jsou neplatné pro kanál Azure ML.

- **Doporučení:** Zkontrolujte hodnotu vlastností aktivity tak, aby odpovídala očekávané datové části publikovaného kanálu Azure ML určeného v propojené službě.


### <a name="error-code--4124"></a>Kód chyby: 4124

- **Zpráva**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Příčina**: Publikovaný koncový bod kanálu Azure ML neexistuje.

- **Doporučení:** Ověřte, zda publikovaný koncový bod kanálu Azure Machine Learning zadaný v propojené službě existuje v Azure Machine Learning.


### <a name="error-code--4125"></a>Kód chyby: 4125

- **Zpráva**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Příčina**: Chyba serveru v Azure Machine Learning.

- **Doporučení**: Opakujte akci později. Pokud problém přetrvává, obraťte se na tým Azure Machine Learning.


### <a name="error-code--4126"></a>Kód chyby: 4126

- **Zpráva**:`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Příčina:** Spuštění kanálu Azure ML se nezdařilo.

- **Doporučení:** Zkontrolujte, zda v Azure Machine Learning další protokoly chyb a opravit kanál ML.



## <a name="common"></a>Společné

### <a name="error-code--2103"></a>Kód chyby: 2103

- **Zpráva**:`Please provide value for the required property '%propertyName;'.`

- **Příčina**: Hodnota vlastnosti nebyla poskytnuta, ale je požadováno ve scénáři.

- **Doporučení**: Zadejte hodnotu ze zprávy a akci opakujte.


### <a name="error-code--2104"></a>Kód chyby: 2104

- **Zpráva**:`The type of the property '%propertyName;' is incorrect.`

- **Příčina**: Typ zadané vlastnosti není podle očekávání.

- **Doporučení:** Opravte typ zařízení a akci opakujte.


### <a name="error-code--2105"></a>Kód chyby: 2105

- **Zpráva**:`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Příčina**: Hodnota vlastnosti je neplatná nebo nemá očekávaný formát.

- **Doporučení:** Vyhledejte dokumentaci k nemovitosti a ujistěte se, že zadaná hodnota má očekávaný formát a typ.


### <a name="error-code--2106"></a>Kód chyby: 2106

- **Zpráva**:`The storage connection string is invalid. %errorMessage;`

- **Příčina**: Připojovací řetězec pro úložiště je neplatný nebo má nesprávný formát.

- **Doporučení:** Přejděte na portál Azure, vyhledejte úložiště, zkopírujte připojovací řetězec a vložte do propojené služby a zkuste to znovu.


### <a name="error-code--2108"></a>Kód chyby: 2108

- **Zpráva**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Příčina**: Požadavek se nezdařil z důvodu základního problému, jako je připojení k síti, selhání DNS, ověření certifikátu serveru nebo časový výtok.

- **Doporučení**: Žádost ověřte pomocí Fiddler/Postman.


### <a name="error-code--2110"></a>Kód chyby: 2110

- **Zpráva**:`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Příčina**: Propojená služba zadaná v aktivitě byla chybná.

- **Doporučení:** Ujistěte se, že typ propojené služby je jedním z podporovaných typů aktivity. Například pro aktivity HDI může být typ propojené služby HDInsight nebo HDInsightOnDemand.


### <a name="error-code--2111"></a>Kód chyby: 2111

- **Zpráva**:`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Příčina**: Typ zadané vlastnosti není podle očekávání.

- **Doporučení:** Opravte typ zařízení a akci opakujte.


### <a name="error-code--2112"></a>Kód chyby: 2112

- **Zpráva**:`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Příčina**: Typ cloudu není podporován nebo nelze určit pro úložiště z koncového pole Suffix.

- **Doporučení:** Použijte úložiště v jiném cloudu a zkuste to znovu.


### <a name="error-code--2128"></a>Kód chyby: 2128

- **Zpráva**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Příčina:** Připojení k síti, selhání DNS, ověření certifikátu serveru nebo časový výtok.

- **Doporučení**: Ověřte, zda koncový bod, který se pokoušíte požadavek na požadavek. Můžete použít nástroje, jako je Šumař / Pošťák.



## <a name="custom"></a>Vlastní

Následující tabulka platí pro Azure Batch.
      
### <a name="error-code--2500"></a>Kód chyby: 2500

- **Zpráva**:`Hit unexpected exception and execution failed.`

- **Příčina**:`Can't launch command, or the program returned an error code.`

- **Doporučení**: Ujistěte se, že spustitelný soubor existuje. Pokud byl program spuštěn, ujistěte se, že *stdout.txt* a *stderr.txt* byly nahrány do účtu úložiště. Je vhodné vyzařovat velké protokoly v kódu pro ladění.


### <a name="error-code--2501"></a>Kód chyby: 2501

- **Zpráva**:`Cannot access user batch account; please check batch account settings.`

- **Příčina:** Nesprávný přístupový klíč dávky nebo název fondu.

- **Doporučení:** Ověřte název fondu a klíč batch přístup u propojené služby.


### <a name="error-code--2502"></a>Kód chyby: 2502

- **Zpráva**:`Cannot access user storage account; please check storage account settings.`

- **Příčina:** Nesprávný název účtu úložiště nebo přístupový klíč.

- **Doporučení:** Ověřte název účtu úložiště a přístupový klíč v propojené službě.


### <a name="error-code--2504"></a>Kód chyby: 2504

- **Zpráva**:`Operation returned an invalid status code 'BadRequest'.`

- **Příčina**: Příliš mnoho souborů ve složceCesta vlastní aktivity. Celková velikost resourceFiles nemůže být větší než 32 768 znaků.

- **Doporučení:** Odebrání nepotřebných souborů. Nebo je zip a přidat rozbalit příkaz k jejich extrahování. Použijte například`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Kód chyby: 2505

- **Zpráva**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Příčina**: Vlastní aktivity podporují pouze účty úložiště, které používají přístupový klíč.

- **Doporučení**: Viz popis chyby.


### <a name="error-code--2507"></a>Kód chyby: 2507

- **Zpráva**:`The folder path does not exist or is empty: ...`

- **Příčina:** V účtu úložiště na zadané cestě nejsou žádné soubory.

- **Doporučení:** Cesta ke složce musí obsahovat spustitelné soubory, které chcete spustit.


### <a name="error-code--2508"></a>Kód chyby: 2508

- **Zpráva**:`There are duplicate files in the resource folder.`

- **Příčina**: Více souborů se stejným názvem jsou v různých podsložkách folderPath.

- **Doporučení:** Vlastní aktivity slanou strukturu složek pod folderPath.  Pokud potřebujete zachovat strukturu složek, zip soubory a extrahovat je v Azure Batch pomocí příkazu rozbalit. Použijte například`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Kód chyby: 2509

- **Zpráva**:`Batch   url ... is invalid; it must be in Uri format.`

- **Příčina:** Adresy URL dávek musí být podobné`https://mybatchaccount.eastus.batch.azure.com`

- **Doporučení**: Viz popis chyby.


### <a name="error-code--2510"></a>Kód chyby: 2510

- **Zpráva**:`An   error occurred while sending the request.`

- **Příčina**: Adresa URL dávky je neplatná.

- **Doporučení:** Ověřte dávkovou adresu URL.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Kód chyby: 200

- **Zpráva**:`Unexpected error happened: '%error;'.`

- **Příčina**: Došlo k internímu problému se službou.

- **Doporučení**: Obraťte se na podporu ADF pro další pomoc.


### <a name="error-code--201"></a>Kód chyby: 201

- **Zpráva**:`JobType %jobType; is not found.`

- **Příčina**: Existuje nový typ úlohy, který není podporován adf.

- **Doporučení**: Obraťte se na tým podpory ADF pro další pomoc.


### <a name="error-code--202"></a>Kód chyby: 202

- **Zpráva**:`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Příčina**: Chybová zpráva by měla zobrazit podrobnosti o tom, co se pokazilo.

- **Doporučení:** Chybová zpráva by měla pomoci při řešení problému. Pokud není k dispozici dostatek informací, obraťte se na podporu ADF pro další pomoc.


### <a name="error-code--203"></a>Kód chyby: 203

- **Zpráva**:`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Příčina**: Chybová zpráva by měla zobrazit podrobnosti o tom, co se pokazilo.

- **Doporučení:** Chybová zpráva by měla pomoci při řešení problému. Pokud není k dispozici dostatek informací, obraťte se na podporu ADF pro další pomoc.


### <a name="error-code--204"></a>Kód chyby: 204

- **Zpráva**:`The resumption token is missing for runId '%runId;'.`

- **Příčina**: Došlo k internímu problému se službou.

- **Doporučení**: Obraťte se na podporu ADF pro další pomoc.


### <a name="error-code--205"></a>Kód chyby: 205

- **Zpráva**:`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Příčina**: Při vytváření clusteru HDI na vyžádání došlo k chybě.

- **Doporučení**: Obraťte se na podporu ADF pro další pomoc.


### <a name="error-code--206"></a>Kód chyby: 206

- **Zpráva**:`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Příčina**: Došlo k internímu problému se službou, která to způsobila.

- **Doporučení**: Může se jedná o přechodný problém. Opakujte úlohu a pokud problém přetrvává, požádejte o další pomoc podporu podavače ADF.


### <a name="error-code--207"></a>Kód chyby: 207

- **Zpráva**:`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Příčina**: Při pokusu o určení oblasti z účtu primárního úložiště došlo k vnitřní chybě.

- **Doporučení:** Zkuste jiné úložiště. V případě, že se nejedná o přijatelné řešení, obraťte se na tým podpory ADF pro další pomoc.


### <a name="error-code--208"></a>Kód chyby: 208

- **Zpráva**:`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Příčina**: Při pokusu o čtení instančního objektu nebo vytvoření instance ověřování MSI došlo k vnitřní chybě.

- **Doporučení:** Zvažte poskytnutí instančního objektu, který má oprávnění k vytvoření clusteru HDInsight v poskytnutém předplatném a akci opakujte. V případě, že se nejedná o přijatelné řešení, obraťte se na tým podpory ADF pro další pomoc.


### <a name="error-code--2300"></a>Kód chyby: 2300

- **Zpráva**:`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Příčina:** Pokud chybová zpráva obsahuje zprávu podobnou "Vzdálený název nelze přeložit.' to může znamenat, že zadaný identifikátor URI clusteru je neplatný.


- **Doporučení**: Ujistěte se, že cluster nebyl odstraněn a že zadaný identifikátor URI je správný. Při otevření identifikátoru URI v prohlížeči byste měli vidět ui Ambari. Pokud je cluster ve virtuální síti, identifikátor URI by měl být privátní identifikátor URI. Chcete-li jej otevřít, použijte virtuální počítač, který je součástí stejné virtuální sítě. Další informace naleznete v [tomto](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services).
                  

<br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou "Úkol byl zrušen.', znamená to, že časový čas odeslání úlohy byl vyhotoven.

- **Doporučení:** Problém může být obecné připojení HDInsight nebo připojení k síti. Nejprve zkontrolujte, zda je rozhraní HDInsight Ambari k dispozici v libovolném prohlížeči. Zkontrolujte, zda jsou vaše přihlašovací údaje stále platné. Pokud používáte integrovaný runtime (IR s vlastním hostitelem), nezapomeňte to provést z virtuálního počítače nebo počítače, kde je nainstalována infračervená infračervená infračervená instalace s vlastním hostitelem. Potom zkuste úlohu odeslat z datové továrny znovu. Pokud se stále nezdaří, obraťte se na tým data factory pro podporu.

<br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou "Správce uživatele je uzamčen v Ambari" nebo "Neautorizováno: Uživatelské jméno nebo heslo Ambari je nesprávné", znamená to, že přihlašovací údaje pro HDInsight jsou nesprávné nebo vypršela jeho platnost.

- **Doporučení**: Opravte pověření a znovu nasaďte propojenou službu. Nejprve se ujistěte, že pověření fungují na HDInsight otevřením identifikátoru URI clusteru v libovolném prohlížeči a pokusem o přihlášení. Pokud přihlašovací údaje nefungují, můžete je obnovit z webu Azure Portal.

<br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou '502 - webový server obdržel neplatnou odpověď při hraní jako brána nebo proxy server', tato chyba je vrácena službou HDInsight.


- **Doporučení:** Prohlédněte si dokumentaci k řešení https://hdinsight.github.io/ambari/ambari-ui-502-error.html https://hdinsight.github.io/spark/spark-thriftserver-errors.htmlpotíží https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502azure HDInsight, například , , .
                  

<br>

- **Příčina**: Pokud chybová zpráva obsahuje zprávu podobnou "Nelze opravit žádost o odeslání úlohy, protože služba templeton je zaneprázdněna příliš mnoha žádostmi o odeslání úloh" nebo "Queue root.joblauncher již má 500 aplikací, nemůže přijmout odeslání žádosti", znamená to, že do hdinsightu je současně odesíláno příliš mnoho úloh.

- **Doporučení:** Zvažte omezení počtu souběžných úloh odeslaných do HDInsight. Odkazovat na souběžnost aktivity data factory, pokud jsou úlohy jsou předkládány stejnou aktivitou. Změňte aktivační události tak, aby souběžné spuštění kanálu byly rozloženy v čase. Naleznete v dokumentaci HDInsight upravit templeton.parallellism.job.submit podle chyby naznačuje.


### <a name="error-code--2301"></a>Kód chyby: 2301

- **Zpráva**:`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Příčina:** Cluster nebo služba HDInsight má problémy.


- **Doporučení:** K této chybě dochází, když adf nezíská odpověď z clusteru HDInsight při pokusu o získání stavu spuštěné úlohy. Může to být příčinou problémů v samotném clusteru nebo služba HDInsight může mít výpadek. Další pomoc naleznete v dokumentaci k řešení potíží s rozhraním HDInsight na adrese https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide.
                


### <a name="error-code--2302"></a>Kód chyby: 2302

- **Zpráva**:`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Příčina**: Úloha byla odeslána do clusteru HDI a selhala tam.

- **Doporučení:** Postupujte podle odkazu protokoly příze v aktivitě spustit výstup a vyhledejte chyby ve výstupu HDI. V případě potřeby kontaktujte tým HDInsight.


### <a name="error-code--2303"></a>Kód chyby: 2303

- **Zpráva**:`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Příčina**: Úloha byla odeslána do clusteru HDI a selhala tam.

- **Doporučení:** Postupujte podle odkazu protokoly příze v aktivitě spustit výstup a vyhledejte chyby ve výstupu HDI. Zkuste to znovu nebo se v případě potřeby obraťte na tým HDInsight.


### <a name="error-code--2304"></a>Kód chyby: 2304

- **Zpráva**:`MSI authentication is not supported on storages for HDI activities.`

- **Příčina:** Služby propojené s úložištěm používané v propojené službě HDI nebo aktivitě HDI jsou konfigurovány s ověřováním MSI, které není podporováno.

- **Doporučení:** Poskytněte úplné připojovací řetězce pro účty úložiště používané v propojené službě HDI nebo aktivitě HDI.


### <a name="error-code--2305"></a>Kód chyby: 2305

- **Zpráva**:`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Příčina**: Informace o připojení pro cluster HDI jsou nesprávné, zadaný uživatel nemá oprávnění k provedení požadované akce nebo služba HDInsight měla problémy s odpovědí na požadavky adf.

- **Doporučení**: Ujistěte se, že informace o uživateli jsou správné. Také ověřte, že umělou instalaci Ambari pro cluster HDI lze otevřít v prohlížeči z virtuálního počítače, kde je infračervený přenos nainstalován v případě samoobslužné infračervené hodu, nebo lze otevřít z libovolného počítače v případě Azure IR.


### <a name="error-code--2306"></a>Kód chyby: 2306

- **Zpráva**:`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Příčina**: Json poskytnutý pro akci skriptu je neplatný.


- **Doporučení**: Chybová zpráva by měla pomoci identifikovat problém. Opravte konfiguraci json a akci opakujte. Další https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service informace naleznete.
                


### <a name="error-code--2310"></a>Kód chyby: 2310

- **Zpráva**:`Failed to submit Spark job. Error: '%message;'`

- **Příčina**: ADF se pokusil vytvořit dávku v clusteru Spark pomocí Livy API (livy/batch), ale obdržel chybu.

- **Doporučení**: Chcete-li problém vyřešit, postupujte podle chybové zprávy. Pokud není dostatek informací, aby si to vyřešit, obraťte se na hdi tým a poskytnout jim id dávky a ID úlohy, které lze nalézt v aktivitě spustit výstup v ADF monitorovací stránku.


### <a name="error-code--2312"></a>Kód chyby: 2312

- **Zpráva**:`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Příčina**: Úloha se nezdařila v clusteru HDInsight Spark.

- **Doporučení:** Postupujte podle odkazů na stránce Výstup spuštění aktivity v adf monitoringu a vyřešte potíže se spuštěním v clusteru HDInsight Spark. Další pomoc vám poskytne tým podpory HDInsight.


### <a name="error-code--2313"></a>Kód chyby: 2313

- **Zpráva**:`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Příčina**: Dávka byla odstraněna v clusteru HDInsight Spark.

- **Doporučení:** Poradce při potížích s dávkami v clusteru HDInsight Spark. Další pomoc vám poskytne podpora HDInsight. 


### <a name="error-code--2328"></a>Kód chyby: 2328

- **Zpráva**:`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Příčina**:`The error message should show the details of what went wrong.`

- **Doporučení:** Chybová zpráva by měla pomoci při řešení problému.


### <a name="error-code--2329"></a>Kód chyby: 2329

- **Zpráva**:`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Příčina**: Chybová zpráva by měla zobrazit podrobnosti o tom, co se pokazilo.

- **Doporučení:** Chybová zpráva by měla pomoci při řešení problému.


### <a name="error-code--2331"></a>Kód chyby: 2331

- **Zpráva**:`The file path should not be null or empty.`

- **Příčina**: Zadaný soubor cesta je prázdný.

- **Doporučení:** Zadejte cestu k existujícímu souboru.


### <a name="error-code--2340"></a>Kód chyby: 2340

- **Zpráva**:`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Příčina**: Propojená služba HDInsightOnDemand nepodporuje spuštění prostřednictvím služby SelfHosted IR.

- **Doporučení:** Vyberte azure ir a zkuste to znovu.


### <a name="error-code--2341"></a>Kód chyby: 2341

- **Zpráva**:`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Příčina**: Zadaná adresa URL není ve správném formátu.

- **Doporučení:** Opravte adresu URL clusteru a akci opakujte.


### <a name="error-code--2342"></a>Kód chyby: 2342

- **Zpráva**:`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Příčina**: Zadaná pověření jsou pro cluster chybná nebo došlo k problému s konfigurací sítě nebo připojením nebo došlo k potížím s připojením ke clusteru.

- **Doporučení**:  
      1. Ověřte správnost přihlašovacích údajů otevřením ui clusteru HDInsight v prohlížeči.
      2. Pokud je cluster ve virtuální síti a používá se infračervená infračervená infračervená z vlastních serverů, adresa URL HDI by měla být privátní adresou URL ve virtuálních sítích, to znamená, že by měla mít za názvem clusteru '-int'. Napříkladhttps://mycluster.azurehdinsight.net/" " byhttps://mycluster-int.azurehdinsight.net/měla být změněna na " ".
      2. Pokud je cluster ve virtuální síti, používá se infračervené zařízení hostované na vlastní uživatele a byla použita privátní adresa URL a připojení se stále nezdařilo, pak virtuální virtuální počítače, kde je infračervený přenos nainstalován, měl problémy s připojením k rozhraní HDI. Připojte se k virtuálnímu virtuálnímu virtuálnímu zařízení, kde je nainstalováninfračervený přenos a otevřete ui Ambari v prohlížeči. Použijte privátní adresu URL clusteru. Toto připojení by mělo fungovat z prohlížeče. Pokud tomu tak není, obraťte se na tým podpory HDInsight pro další pomoc.
      3. Pokud se nepoužívají infračervené nebo infračervené ovládání hostované samostatně, měl by být cluster HDI přístupný veřejně. Otevřete ui Ambari v prohlížeči a ujistěte se, že se otevře. Pokud se s clusterem nebo službami v něm najdou nějaké problémy, požádejte o pomoc tým podpory HDInsight.
      Takže obecně musí být adresa URL clusteru HDI používaná v propojené službě ADF přístupná pro službu ADF IR (vlastní hostované nebo Azure), aby testovací připojení prošlo a aby fungovalo spuštění. To lze snadno ověřit otevřením této adresy URL z prohlížeče z virtuálního počítače nebo z libovolného veřejného počítače.
    


### <a name="error-code--2343"></a>Kód chyby: 2343

- **Zpráva**:`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Příčina**: Uživatelské jméno nebo heslo jsou prázdné.

- **Doporučení:** Zadejte správná pověření pro připojení k rozhraní HDI a akci opakujte.


### <a name="error-code--2345"></a>Kód chyby: 2345

- **Zpráva**:`Failed to read the content of the hive script. Error: '%message;'`

- **Příčina**: Soubor skriptu neexistuje nebo se adf nemůže připojit k umístění skriptu.

- **Doporučení**: Ověřte, zda skript existuje a že přidružená propojená služba má správná pověření pro připojení.


### <a name="error-code--2346"></a>Kód chyby: 2346

- **Zpráva**:`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Příčina**: ADF se pokusil navázat připojení ODBC ke clusteru HDI a s chybou se nezdařilo.

- **Doporučení:** Chybová zpráva a kód chyby by měly pomoci při odstraňování chyb připojení ROZHRANÍ ODBC. V případě, že k vyřešení problému nestačí, obraťte se na tým Azure HDInsight s žádostí o podporu.


### <a name="error-code--2347"></a>Kód chyby: 2347

- **Zpráva**:`Hive execution through ODBC failed with error message '%message;'.`

- **Příčina**: ADF odeslal skript podregistru ke spuštění do clusteru HDI prostřednictvím připojení ODBC a skript selhal na ROZHRANÍ HDI.

- **Doporučení:** Spuštění skriptu podregistru se nezdařilo v clusteru HDI a chybová zpráva a kód chyby by měly pomoci při řešení potíží. V případě, že k vyřešení problému nestačí, obraťte se na tým Azure HDInsight s žádostí o podporu.


### <a name="error-code--2348"></a>Kód chyby: 2348

- **Zpráva**:`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Příčina**: Vlastnosti služby propojené úložiště nejsou správně nastaveny.

- **Doporučení:** V hlavní službě propojené úložiště pro aktivity HDI jsou podporovány pouze úplné připojovací řetězce. Ujistěte se, že nepoužíváte msi auth nebo aplikace.


### <a name="error-code--2350"></a>Kód chyby: 2350

- **Zpráva**:`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Příčina**: Pověření poskytnutá pro připojení k úložišti, kde by měly být soubory umístěny, jsou nesprávná nebo soubory neexistují.

- **Doporučení:** K této chybě dochází, když ADF provede kroky přípravy pro aktivity HDI. Pokusí se zkopírovat soubory do hlavního úložiště před odesláním úlohy hdi. Ujistěte se, že soubory existují v zadaným umístění, připojení úložiště je správné. ADF HDI aktivity nepodporují ověřování MSI na účty úložiště související s aktivitami HDI, takže se ujistěte, že tyto propojené služby mají úplné klíče nebo používají Azure Key Vault.


### <a name="error-code--2351"></a>Kód chyby: 2351

- **Zpráva**:`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Příčina**: Soubor neexistuje v zadané cestě.

- **Doporučení:** Zkontrolujte, zda soubor skutečně existuje, a propojená služba s informacemi o připojení směřujícími na tento soubor má správná pověření.


### <a name="error-code--2352"></a>Kód chyby: 2352

- **Zpráva**:`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Příčina**: Vlastnosti propojené služby úložiště souborů nejsou správně nastaveny.

- **Doporučení:** Ujistěte se, že vlastnosti propojené služby úložiště souborů jsou správně nakonfigurovány.


### <a name="error-code--2353"></a>Kód chyby: 2353

- **Zpráva**:`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Příčina**: Vlastnosti propojené služby úložiště skriptů nejsou správně nastaveny.

- **Doporučení:** Ujistěte se, že vlastnosti služby propojené úložiště skriptů jsou správně nakonfigurovány.


### <a name="error-code--2354"></a>Kód chyby: 2354

- **Zpráva**:`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Příčina**: Typ služby propojené úložiště není aktivitou podporován.

- **Doporučení:** Ujistěte se, že vybraná propojená služba má jeden z podporovaných typů aktivity. Hdi aktivity podporují azureblobstorage a AzureBlobFSStorage propojené služby.


### <a name="error-code--2355"></a>Kód chyby: 2355

- **Zpráva**:`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Příčina**: Za předpokladu, pro commandEnvironment je nesprávná.

- **Doporučení**:  
      Ujistěte se, že zadaný \"hodnota\"je \"podobný: commandEnvironment\" : [ variableName=variableValue ] A každá proměnná se zobrazí v seznamu pouze jednou.
    


### <a name="error-code--2356"></a>Kód chyby: 2356

- **Zpráva**:`The commandEnvironment already contains a variable named '%variableName;'.`

- **Příčina**: Proměnná byla poskytnuta dvakrát v commandEnvironment .

- **Doporučení**:  
      Ujistěte se, že zadaný \"hodnota\"je \"podobný: commandEnvironment\" : [ variableName=variableValue ] A každá proměnná se zobrazí v seznamu pouze jednou.
    


### <a name="error-code--2357"></a>Kód chyby: 2357

- **Zpráva**:`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Příčina**: Zadaný pověření jsou nesprávné.

- **Doporučení:** Ověřte informace o připojení v propojené službě ADLS Gen 1 a ujistěte se, že testovací připojení je úspěšné.


### <a name="error-code--2358"></a>Kód chyby: 2358

- **Zpráva**:`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Příčina**: Zadaný hodnota pro požadovanou vlastnost TimeToLive má neplatný formát. 

- **Doporučení:** Aktualizujte hodnotu, která má být v navrhovaném rozsahu, a akci opakujte.


### <a name="error-code--2359"></a>Kód chyby: 2359

- **Zpráva**:`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Příčina**: Zadaný hodnota vlastnosti 'role' je neplatná.

- **Doporučení:** Aktualizujte hodnotu tak, aby byla jedním z návrhů, a akci opakujte.


### <a name="error-code--2360"></a>Kód chyby: 2360

- **Zpráva**:`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Příčina**: Zadaný připojovací řetězec pro službu HCatalogLinkedService je neplatný.

- **Doporučení:** Aktualizujte hodnotu na správný připojovací řetězec Azure SQL a zkuste to znovu.


### <a name="error-code--2361"></a>Kód chyby: 2361

- **Zpráva**:`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Příčina**: Vytvoření clusteru se nezdařilo a služba ADF nezískala zpět chybu ze služby HDInsight.

- **Doporučení:** Otevřete portál Azure a pokuste se najít prostředek HDI s zadaným názvem a zkontrolujte stav zřizování. Další pomoc vám poskytne tým podpory HDInsight.


### <a name="error-code--2362"></a>Kód chyby: 2362

- **Zpráva**:`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Příčina**: Zapředpokladuté další úložiště není úložiště objektů blob Azure.

- **Doporučení:** Poskytněte účet úložiště objektů Blob Azure jako další úložiště pro hdinsight na vyžádání propojené služby.



## <a name="web-activity"></a>Aktivita webu

### <a name="error-code--2128"></a>Kód chyby: 2128

- **Zpráva**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Příčina:** Připojení k síti, selhání DNS, ověření certifikátu serveru nebo časový výtok.

- **Doporučení**: Ověřte, zda koncový bod, který se pokoušíte požadavek na požadavek. Můžete použít nástroje, jako je Šumař / Pošťák.


### <a name="error-code--2108"></a>Kód chyby: 2108

- **Zpráva**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Příčina**: Požadavek se nezdařil z důvodu základního problému, jako je připojení k síti, selhání DNS, ověření certifikátu serveru nebo časový výtok.

- **Doporučení**: Žádost ověřte pomocí Fiddler/Postman.
<br>


#### <a name="more-details"></a>Další podrobnosti
Použití Fiddleru k vytvoření relace HTTP monitorované webové aplikace:

1. Stáhněte, nainstalujte a otevřete [Šumař](https://www.telerik.com/download/fiddler).

1. Pokud vaše webová aplikace používá protokol HTTPS, přejděte > na **možnosti nástroje** > **šumava****HTTPS**. Vyberte **Zachytávat HTTPS CONNECTs** a **dešifrovat přenosy HTTPS**.

   ![Fiddler možnosti](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Pokud vaše aplikace používá certifikáty TLS/SSL, přidejte do zařízení certifikát Fiddler. Přejděte na **možnosti** > **nástroje fiddler možnosti** > **HTTPS** > **akce** > **exportovat kořenový certifikát na plochu**.

1. Vypněte zachytávání tak, že přejdete na **Soubor** > **Zachytit provoz**. Nebo stiskněte **klávesu F12**.

1. Vymažte mezipaměť prohlížeče, aby byly odebrány všechny položky uložené v mezipaměti a musely být znovu staženy.

1. Vytvoření požadavku:

   1. Vyberte kartu **Skladatel.**

   1. Nastavte metodu protokolu HTTP a adresu URL.
   
   1. V případě potřeby přidejte záhlaví a tělo požadavku.

   1. Vyberte **Provést**.

1. Zapněte zachytávání provozu znovu a dokončete problematickou transakci na stránce.

1. Přejděte na **Soubor** > **Uložit** > **všechny relace**.

Další informace naleznete [v tématu Začínáme s šumavou](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Další kroky

Další nápovědu k řešení potíží naleznete v těchto zdrojích:

*  [Blog data factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Požadavky na funkce datové továrny](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Fórum přetečení zásobníku pro datovou továrnu](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter informace o Data Factory](https://twitter.com/hashtag/DataFactory)


            
