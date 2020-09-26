---
title: 'ML Studio (klasický): REST API kódy chyb – Azure'
description: Tyto kódy chyb by mohly být vráceny operací na Azure Machine Learning webové služby.
keywords: ''
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: c48c59db2d9b830367276d39a82bc7fc8fdc34d3
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91357246"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Kódy chyb REST API Azure Machine Learning Studio (Classic)

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


Následující chybové kódy by mohly být vráceny operací na webové službě Azure Machine Learning Studio (Classic).
 
## <a name="badargument-http-status-code-400"></a>BadArgument (kód stavu HTTP 400)
 
Byl zadán neplatný argument.
 
Tato třída chyb znamená, že argument zadaný někde je neplatný. Může se jednat o přihlašovací údaje nebo umístění úložiště Azure pro objekt, který se předává do webové služby. Pokud chcete diagnostikovat, který konkrétní argument byl neplatný, podívejte se prosím do pole kód chyby v části Podrobnosti.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| BadParameterValue | Zadaná hodnota parametru nesplňuje pravidlo parametru pro parametr. |
| BadSubscriptionId | ID předplatného, které se používá k vyhodnocení, není ten přítomný v prostředku. |
| BadVersionCall | Během volání rozhraní API byl předán neplatný parametr verze: {0} . Na stránce s nápovědu k rozhraní API vyhledejte správnou verzi a zkuste to znovu. |
| BatchJobInputsNotSpecified | Následující požadované vstupy nebyly pro požadavek zadány: {0} . Ujistěte se prosím, že jsou zadaná všechna vstupní data, a zkuste to znovu. |
| BatchJobInputsTooManySpecified | Požadavek zadal více vstupů, než je definováno ve službě. Seznam přijatého vstupu (ů): {0} . Ujistěte se prosím, že jsou správně zadaná všechna vstupní data, a zkuste to znovu. |
| BlobNameTooLong | Cesta k úložišti objektů BLOB v Azure, která se poskytuje pro výstup diagnostiky, je moc dlouhá: {0} . Zkraťte cestu a zkuste to znovu. |
| BlobNotFound | Nejde získat přístup k poskytnutému objektu blob Azure – {0} .  Chybová zpráva Azure: {1} . |
| ContainerIsEmpty | Nezadal se žádný název kontejneru úložiště Azure. Zadejte platný název kontejneru a akci opakujte. |
| ContainerSegmentInvalid | Neplatný název kontejneru Zadejte platný název kontejneru a akci opakujte. |
| ContainerValidationFailed | Ověření kontejneru objektů BLOB se nezdařilo s touto chybou: {0} . |
| DataTypeNotSupported | Byl zadán nepodporovaný datový typ. Zadejte platné datové typy a akci opakujte. |
| DuplicateInputInBatchCall | Požadavek dávky je neplatný. Současně nelze současně zadat současně jeden i více vstupu. Odeberte jednu z těchto položek z požadavku a zkuste to znovu. |
| ExpiryTimeInThePast | Čas vypršení platnosti je uvedený v minulosti: {0} . Zadejte čas vypršení platnosti ve standardu UTC a zkuste to znovu. Aby nikdy nevypršela platnost, nastavte čas vypršení platnosti na NULL. |
| IncompleteSettings | Nastavení diagnostiky jsou neúplná. |
| InputBlobRelativeLocationInvalid | Nezadal se žádný název objektu BLOB služby Azure Storage. Zadejte platný název objektu BLOB a zkuste to znovu. |
| InvalidBlob | Neplatná specifikace objektu BLOB pro objekt BLOB: {0} . Ověřte, zda je připojovací řetězec/relativní cesta nebo specifikace tokenu SAS správné, a akci opakujte. |
| InvalidBlobConnectionString | Připojovací řetězec zadaný pro jeden z vstupních/výstupních objektů BLOB není platný: {0} . Opravte prosím tento problém a zkuste to znovu. |
| InvalidBlobExtension | Odkaz na objekt BLOB: {0} má neplatnou nebo chybějící příponu souboru. Podporované přípony souborů pro tento typ výstupu jsou: " {1} ". |
| InvalidInputNames | V požadavku byly zadány neplatné názvy vstupu služby: {0} . Namapujte prosím vstupní data na správné vstupy služby a zkuste to znovu. |
| InvalidOutputOverrideName | Neplatný název přepisu výstupu: {0} . Služba nemá výstupní uzel s tímto názvem. Předejte prosím správný název výstupního uzlu, který se má přepsat (platí pro rozlišování velkých a malých písmen). |
| InvalidQueryParameter | Neplatný parametr dotazu {0} . {1} |
| MissingInputBlobInformation | Chybí informace o objektu BLOB služby Azure Storage. Zadejte platný připojovací řetězec a relativní cestu nebo identifikátor URI a zkuste to znovu. |
| MissingJobId | Nebylo zadáno ID úlohy. ID úlohy se vrátí při prvním odeslání úlohy. Ověřte správnost ID úlohy a zkuste to znovu. |
| MissingKeys | Nejsou zadané žádné klíče ani jeden z primárních nebo sekundárních klíčů. |
| MissingModelPackage | Nezadal se žádný identifikátor balíčku modelu nebo balíček modelu. Zadejte platný identifikátor balíčku modelu nebo balíček modelu a zkuste to znovu. |
| MissingOutputOverrideSpecification | V požadavku chybí specifikace objektu BLOB pro přepsání výstupu {0} . Zadejte prosím platné umístění objektu BLOB s požadavkem, nebo odeberte specifikaci výstupu, pokud není potřeba žádné přepsání umístění. |
| MissingRequestInput | Webová služba očekává vstup, ale nebyl zadán žádný vstup. Zajistěte, aby byly na základě publikovaných vstupních portů v modelu zajištěny platné vstupy, a zkuste to znovu. |
| MissingRequiredGlobalParameters | Nebyly zadány všechny požadované parametry webové služby. Ověřte, jestli jsou parametry očekávané pro tyto moduly správné, a zkuste to znovu. |
| MissingRequiredOutputOverrides | Při volání šifrovaného koncového bodu služby je povinné předat přepsání výstupu pro všechny výstupy služby. V tuto chvíli pro tyto výstupy chybí přepsání: {0} |
| MissingWebServiceGroupId | Nebylo zadáno ID skupiny webových služeb. Zadejte platné ID skupiny webových služeb a zkuste to znovu. |
| MissingWebServiceId | Nebylo zadáno ID webové služby. Zadejte platné ID webové služby a zkuste to znovu. |
| MissingWebServicePackage | Nezadal se žádný balíček webové služby. Zadejte platný balíček webové služby a zkuste to znovu. |
| MissingWorkspaceId | Nebylo zadáno ID pracovního prostoru. Zadejte platné ID pracovního prostoru a zkuste to znovu. |
| ModelConfigurationInvalid | Neplatná konfigurace modelu v balíčku modelu. Zajistěte, aby konfigurace modelu obsahovala definice výstupních koncových bodů, standardní koncový bod chyby a standardní koncový bod, a zkuste to znovu. |
| ModelPackageIdInvalid | Neplatné ID balíčku modelu. Zkontrolujte, jestli je ID balíčku modelu správné, a zkuste to znovu. |
| RequestBodyInvalid | Při deserializaci textu žádosti nebyl zadán text žádosti nebo došlo k chybě. |
| RequestIsEmpty | Nebyl zadán žádný požadavek. Zadejte platný požadavek a akci opakujte. |
| UnexpectedParameter | Byly zadány neočekávané parametry. Ověřte, že všechny názvy parametrů jsou zadány správně, jsou předány pouze očekávané parametry a akci opakujte. |
| UnknownError | Neznámou chybu. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | U webové služby nelze změnit požadavky souběžných požadavků {0} . |
| WebServiceIdInvalid | Bylo zadáno neplatné ID webové služby. ID webové služby by mělo být platný identifikátor GUID. |
| WebServiceTooManyConcurrentRequestRequirement | U souběžných požadavků na požadavek nelze nastavit více než {0} . |
| WebServiceTypeInvalid | Poskytnutý typ webové služby je neplatný. Ověřte správnost platného typu webové služby a zkuste to znovu. Platné typy webových služeb: {0} . |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (kód stavu HTTP 400)
 
Byl zadán neplatný argument uživatele.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| InputMismatchError | Vstupní data neodpovídají schématu vstupního portu. |
| InputParseError | Nepovedlo se analyzovat vstupní vektor.  Ověřte, že vstupní vektor má správný počet sloupců a datových typů.  Další podrobnosti: {0} . |
| MissingRequiredGlobalParameters | Chybí parametry, které webová služba očekává. Ověřte správnost všech požadovaných parametrů, které očekává webová služba, a zkuste to znovu. |
| UnexpectedParameter | Ověřte, zda jsou předány pouze požadované parametry očekávané webovou službou, a akci opakujte. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (kód stavu HTTP 400)
 
Požadavek není v aktuálním kontextu platný.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| CannotStartJob | Úlohu nelze spustit, protože je ve {0} stavu. |
| IncompatibleModel | Model není kompatibilní s verzí požadavku. Verze žádosti podporuje pouze jeden výstupní model Single DataTable. |
| MultipleInputsNotAllowed | Model nepovoluje více vstupů. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (kód stavu HTTP 400)
 
Při provádění modulu došlo k vnitřní chybě knihovny.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (kód stavu HTTP 400)
 
Při provádění modulu došlo k chybě.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (kód stavu HTTP 400)
 
Neplatný balíček webové služby Ověřte, jestli je zadaný balíček webové služby správný, a zkuste to znovu.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| FormatError | Balíček webové služby je poškozen. Zobrazí {0} |
| RuntimesError | Graf balíčku webové služby je neplatný. Zobrazí {0} |
| ValidationError | Graf balíčku webové služby je neplatný. Zobrazí {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Neautorizováno (kód stavu HTTP 401)
 
Požadavek nemá oprávnění k přístupu k prostředku.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| AdminRequestUnauthorized | Neautorizováno |
| ManagementRequestUnauthorized | Neautorizováno |
| ScoreRequestUnauthorized | Zadali jste neplatné přihlašovací údaje. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (kód stavu HTTP 404)
 
Prostředek se nenašel.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| ModelPackageNotFound | Balíček modelu se nenašel. Ověřte, jestli je ID balíčku modelu správné, a zkuste to znovu. |
| WebServiceIdNotFoundInWorkspace | Webová služba v tomto pracovním prostoru se nenašla. Došlo k neshodě mezi webServiceId a ID pracovního prostoru. Ověřte, jestli je poskytnutá webová služba součástí pracovního prostoru, a zkuste to znovu. |
| WebServiceNotFound | Webová služba se nenašla. Ověřte správnost ID webové služby a zkuste to znovu. |
| WorkspaceNotFound | Pracovní prostor nebyl nalezen. Ověřte, jestli je ID pracovního prostoru správné, a zkuste to znovu. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (kód stavu HTTP 408)
 
Operaci nelze dokončit v povoleném čase.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| RequestCanceled | Požadavek byl zrušen klientem. |
| ScoreRequestTimeout | Vypršel časový limit žádosti o spuštění. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (kód stavu HTTP 409)
 
Prostředek už existuje.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Neplatný název výstupního parametru. Zkuste použít modul editoru metadat k přejmenování sloupců a zkuste to znovu. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (kód stavu HTTP 413)
 
Model překročil přidělenou kvótu paměti.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| OutOfMemoryLimit | Model využil více paměti, než bylo pro něj vhodné. Maximální povolená paměť pro model je {0} MB. Zkontrolujte prosím, jestli máte v modelu problémy. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (kód stavu HTTP 500)
 
Při provádění došlo k vnitřní chybě.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | V procesu kontejneru došlo k chybě. Chyba systému |
| ContainerProcessTerminatedWithUnknownError | V procesu kontejneru došlo k chybě s neznámou chybou. |
| ContainerValidationFailed | Ověření kontejneru objektů BLOB se nezdařilo s touto chybou: {0} . |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Nebyly zadány žádné argumenty. Ověřte, zda jsou předány platné argumenty, a akci opakujte. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Port ID = {0} má nepodporovaný datový typ: {1} . |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Nepovedlo se vygenerovat Swagger, podrobnosti: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Neznámý kód stavu úlohy {0} . |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, podrobnosti: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (kód stavu HTTP 500)
 
Při provádění došlo k vnitřní chybě. Nedostatek paměti systému. Zkuste to prosím znovu.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (kód stavu HTTP 500)
 
Neplatný balíček modelu Ověřte, jestli je zadaný balíček modelu správný, a zkuste to znovu.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (kód stavu HTTP 500)
 
Neplatný balíček webové služby Ověřte, jestli je zadaný webový balíček správný, a zkuste to znovu.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| ModuleError | Graf balíčku webové služby je neplatný. Zobrazí {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (kód stavu HTTP 503)
 
Požadavek nelze provést, protože jsou kontejnery inicializovány.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (kód stavu HTTP 503)
 
Služba je dočasně nedostupná.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| NoMoreResources | Pro požadavek nejsou k dispozici žádné prostředky. |
| RequestThrottled | Požadavek byl pro {0} koncový bod omezen. Maximální souběžnost pro koncový bod je {1} . |
| TooManyConcurrentRequests | Bylo odesláno příliš mnoho souběžných požadavků. |
| TooManyHostsBeingInitialized | Současně je inicializováno příliš mnoho hostitelů. Zvažte omezení/opakování pokusu. |
| TooManyHostsBeingInitializedPerModel | Současně je inicializováno příliš mnoho hostitelů. Zvažte omezení/opakování pokusu. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (kód stavu HTTP 504)
 
Operaci nelze dokončit v povoleném čase.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| BackendInitializationTimeout | Inicializace webové služby se nedala dokončit v povoleném čase. |
| BackendScoreTimeout | Spuštění požadavku webové služby nebylo v povoleném čase dokončeno. |
 
