---
title: Kódy chyb rozhraní REST API – Azure Machine Learning Studio | Dokumentace Microsoftu
description: Tyto kódy mohou být vráceny operace webové služby Azure Machine Learning.
keywords: ''
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: becc8368ebd9b6096a56e2603944384aa6b50d29
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091726"
---
# <a name="machine-learning-studio-rest-api-error-codes"></a>Ve službě Machine Learning Studio REST API chybové kódy
 
Následující kódy chyb mohou být vráceny operace webové služby Azure Machine Learning Studio.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (stavový kód HTTP 400)
 
Neplatný argument uvedený.
 
Tato třída chyby znamená, že poskytuje někde argument byl neplatný. To může být přihlašovací údaje nebo umístění služby Azure storage na něco předaný k webové službě. Podívejte se prosím na pole "kód" chyby v části "details" pro diagnostiku, která konkrétní argument byl neplatný.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| BadParameterValue | Zadaná hodnota parametru nesplňuje podmínky pravidla parametru u parametru |
| BadSubscriptionId | Id, které slouží ke stanovení skóre pro předplatné není ta, která je k dispozici v prostředku |
| BadVersionCall | Byl předán parametr neplatné verzi během volání rozhraní API: {0}. Zaškrtněte na stránce nápovědy rozhraní API pro předávání správnou verzi a zkuste to znovu. |
| BatchJobInputsNotSpecified | Zadání následujících nezbytných nahráním není zadané v požadavku: {0}. Zkontrolujte, zda je zadán veškerá vstupní data a zkuste to znovu. |
| BatchJobInputsTooManySpecified | Žádost zadat více vstupů, než je definováno ve službě. Seznam přijatelných nahráním: {0}. Zkontrolujte, zda je správně zadána veškerá vstupní data a zkuste to znovu. |
| BlobNameTooLong | Cesta k úložišti objektů blob v Azure k dispozici pro diagnostický výstup je příliš dlouhá: {0}. Zkraťte cestu a zkuste to znovu. |
| BlobNotFound | Nelze získat přístup k zadané Azure blob - {0}.  Chybová zpráva Azure: {1}. |
| ContainerIsEmpty | Nebyl poskytnut žádný název kontejneru úložiště Azure. Zadejte platný kontejner název a zkuste to znovu. |
| ContainerSegmentInvalid | Neplatný název kontejneru. Zadejte platný kontejner název a zkuste to znovu. |
| ContainerValidationFailed | Objekt BLOB ověření kontejneru se nepovedlo kvůli této chybě: {0}. |
| DataTypeNotSupported | Nepodporovaný datový typ, k dispozici. Zadejte platné datové typy a zkuste to znovu. |
| DuplicateInputInBatchCall | Dávkový požadavek je neplatný. Nelze zadat jednu i více vstup ve stejnou dobu. Odeberte jeden z těchto položek z požadavku a zkuste to znovu. |
| ExpiryTimeInThePast | Čas vypršení platnosti, které jsou k dispozici je v minulosti: {0}. Zadejte budoucí platnosti ve standardu UTC a zkuste to znovu. Bez vypršení platnosti, nastavte čas vypršení platnosti na hodnotu NULL. |
| IncompleteSettings | Nastavení diagnostiky nejsou úplné. |
| InputBlobRelativeLocationInvalid | K dispozici žádný název objektu blob úložiště Azure. Zadejte platný objekt blob název a zkuste to znovu. |
| InvalidBlob | Neplatný objekt blob specifikace pro objekt blob: {0}. Ověřte, že připojovací řetězec nebo relativní cestu nebo specifikace token SAS je správný a zkuste to znovu. |
| InvalidBlobConnectionString | Z připojovacího řetězce zadaného pro jednu vstupní a výstupní objekty BLOB neplatné: {0}. Opravit to a zkuste to znovu. |
| InvalidBlobExtension | Odkaz na objekt blob: {0} má soubor je neplatný nebo chybějící rozšíření. Podporované přípony souborů pro tento typ výstupu jsou: "{1}". |
| InvalidInputNames | Vstupní názvy v požadavku je zadaná neplatná služba: {0}. Namapovat vstupní data do správné služby vstupů a zkuste to znovu. |
| InvalidOutputOverrideName | Neplatný výstupní název potlačení: {0}. Služba nemá uzlu výstup s tímto názvem. Předejte prosím správný název uzlu přepsat (platí rozlišování velikosti písmen). |
| InvalidQueryParameter | Neplatný parametr dotazu "{0}". {1} |
| MissingInputBlobInformation | Chybí informace o objektu blob úložiště Azure. Zadejte platný připojovací řetězec a relativní cestu nebo identifikátor URI a zkuste to znovu. |
| MissingJobId | Zadané Id žádná úloha. Úlohy Id je vrácena, pokud úloha byla odeslána poprvé. Ověřte správnost Id úlohy a zkuste to znovu. |
| MissingKeys | K dispozici žádné klíče nebo jeden z primární nebo sekundární klíč není k dispozici. |
| MissingModelPackage | Žádné Id modelu balíčku nebo modelu balíček k dispozici. Zadejte platný model balíček Id nebo model balíček a zkuste to znovu. |
| MissingOutputOverrideSpecification | V požadavku chybí specifikace objektů blob pro výstup přepsání {0}. Zadejte platný objekt blob umístění k požadavku, nebo odeberte výstup, pokud přepsání není umístění. |
| MissingRequestInput | Webová služba očekává vstup, ale nebyl poskytnut žádný vstup. Ujistěte se, že jsou k dispozici platné vstupy závislosti na publikovaných vstupní porty v modelu a zkuste to znovu. |
| MissingRequiredGlobalParameters | Všechny požadované parametry webové služby k dispozici. Ověřte správnost parametrů očekává u modulů a zkuste to znovu. |
| MissingRequiredOutputOverrides | Při volání koncového bodu šifrované služby je povinná a zajistěte tak předání výstup přepsání pro všechny služby výstupy. Chybějící přepsání v tuto chvíli pro tyto výstupy: {0} |
| MissingWebServiceGroupId | Zadané Id skupiny bez webových služeb. Zadejte Id platnou webovou službu skupiny a zkuste to znovu. |
| MissingWebServiceId | Zadané Id žádná webová služba. Zadejte platnou webovou službu Id a zkuste to znovu. |
| MissingWebServicePackage | Žádné webového balíčku služby k dispozici. Zadejte balíček nástroje platnou webovou službu a zkuste to znovu. |
| MissingWorkspaceId | Zadané Id žádný pracovní prostor. Zadejte platné Id pracovního prostoru a zkuste to znovu. |
| ModelConfigurationInvalid | Neplatný model konfigurace v balíčku modelu. Zkontrolujte konfiguraci modelu obsahuje definici koncových bodů výstupu, std chyba koncového bodu, a std výstupní koncový bod a zkuste to znovu. |
| ModelPackageIdInvalid | Balíček modelu je neplatný identifikátor. Ověřte správnost Id balíčku modelu a zkuste to znovu. |
| RequestBodyInvalid | Žádný text žádosti k dispozici nebo Chyba při deserializaci textu požadavku. |
| RequestIsEmpty | Není k dispozici žádný požadavek. Zadejte platnou žádost a zkuste to znovu. |
| UnexpectedParameter | Neočekávané parametry zadané. Ověřte všechny názvy parametrů jsou zadány správně, pouze očekávané parametry jsou předány a zkuste to znovu. |
| Neznámé chyby | Neznámá chyba |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Nelze změnit požadavky souběžných požadavků pro {0} webové služby. |
| WebServiceIdInvalid | Zadané id neplatný webové služby. Id webové služby musí být platný identifikátor guid. |
| WebServiceTooManyConcurrentRequestRequirement | Nelze nastavit požadavek souběžný požadavek na víc než {0}. |
| WebServiceTypeInvalid | Zadaný typ neplatný webové služby. Ověřte že správnost tohoto typu platnou webovou službu a zkuste to znovu. Typy služeb platnou webovou: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (stavový kód HTTP 400)
 
Zadaný argument neplatný uživatel.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| InputMismatchError | Vstupní data neodpovídá schématu vstupního portu. |
| InputParseError | Analýza vstupu vektoru se nezdařilo.  Ověřte, že vstupní Vektor nemá správný počet sloupců a datové typy.  Další podrobnosti: {0}. |
| MissingRequiredGlobalParameters | Webová služba očekává parametry nebyly nalezeny. Ověřte, že všechny požadované parametry očekává webové služby jsou správné a zkuste to znovu. |
| UnexpectedParameter | Ověřte očekávání webová služba pouze požadované parametry jsou předány a zkuste to znovu. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (stavový kód HTTP 400)
 
Požadavek je neplatný v aktuálním kontextu.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| CannotStartJob | Úlohu nelze spustit, protože je v {0} stavu. |
| IncompatibleModel | Model je kompatibilní s verzí požadavku. Žádost o verze podporuje pouze modely výstup jednoho objektu datatable. |
| MultipleInputsNotAllowed | Model není povoleno více vstupů. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (stavový kód HTTP 400)
 
Spouštění modulu došlo k chybě interní knihovny.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (stavový kód HTTP 400)
 
Spouštění modulu došlo k chybě.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (stavový kód HTTP 400)
 
Neplatný webový balíček služby. Zkontrolujte správnost balíčku webové služby k dispozici a zkuste to znovu.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| FormatError | Balíček webové služby je poškozený. Podrobnosti: {0} |
| RuntimesError | Graf balíček webové služby je neplatný. Podrobnosti: {0} |
| ValidationError | Graf balíček webové služby je neplatný. Podrobnosti: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Neoprávněné (stavový kód HTTP 401)
 
Požadavek není autorizovaný k přístupu k prostředku.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| AdminRequestUnauthorized | Neautorizováno |
| ManagementRequestUnauthorized | Neautorizováno |
| ScoreRequestUnauthorized | Zadané neplatné přihlašovací údaje. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (stavový kód HTTP 404)
 
Prostředek se nenašel.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| ModelPackageNotFound | Model balíček se nenašel. Ověřte správnost Id balíčku modelu a zkuste to znovu. |
| WebServiceIdNotFoundInWorkspace | Webové služby v tomto pracovním prostoru nenašla. Došlo k neshodě mezi webServiceId a ID pracovního prostoru. Ověřte, že webová služba k dispozici je součástí pracovního prostoru a zkuste to znovu. |
| WebServiceNotFound | Webová služba nebyla nalezena. Ověřte správnost Id webové služby a zkuste to znovu. |
| WorkspaceNotFound | Pracovní prostor nebyl nalezen. Ověřte správnost Id pracovního prostoru a zkuste to znovu. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (stavový kód HTTP 408)
 
Operaci nešlo dokončit v povoleném čase.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| RequestCanceled | Požadavek byl zrušen klientem. |
| ScoreRequestTimeout | Žádost o spuštění, vypršení časového limitu. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (kód stavu protokolu HTTP 409)
 
Prostředek už existuje.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Název parametru neplatný výstup. Zkuste použít editor modulu metadat přejmenování sloupců a zkuste to znovu. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (stavový kód HTTP 413)
 
Model překročil kvótu paměti přiřazené.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| OutOfMemoryLimit | Model spotřebovávat více paměti, než byla přidělena pro něj. Maximální povolené využití paměti pro model je {0} MB. Zkontrolujte prosím váš model pro problémy. |
 
## <a name="internalerror-http-status-code-500"></a>Vnitřní chyba (kód stavu HTTP 500)
 
Provádění došlo k vnitřní chybě.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | Kontejnerový proces selhal s chybou systému |
| ContainerProcessTerminatedWithUnknownError | Kontejnerový proces, došlo k chybě kvůli neznámé chybě |
| ContainerValidationFailed | Objekt BLOB ověření kontejneru se nepovedlo kvůli této chybě: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Nebyly zadány žádné argumenty. Ověřte, že platné argumenty jsou předány a zkuste to znovu. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Id portu ={0} má nepodporovaný datový typ: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Funkcí výstupu swagger se nepovedlo, podrobnosti: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| Neznámé chyby |  |
| UnknownJobStatusCode | Neznámá úloha stavový kód {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, podrobnosti: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (stavový kód HTTP 500)
 
Provádění došlo k vnitřní chybě. Nedostatek paměti systému. Zkuste to prosím znovu.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (stavový kód HTTP 500)
 
Balíček modelu je neplatný. Zkontrolujte správnost balíčku modelu k dispozici a zkuste to znovu.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (stavový kód HTTP 500)
 
Neplatný webový balíček služby. Ověřte správnost webový balíček, který je k dispozici a zkuste to znovu.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| ModuleError | Graf balíček webové služby je neplatný. Podrobnosti: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (stavový kód HTTP 503)
 
Požadavek nelze provést jako kontejnery jsou během inicializace.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (stavový kód HTTP 503)
 
Služba je dočasně nedostupná.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| NoMoreResources | Žádné prostředky k dispozici pro požadavek. |
| RequestThrottled | Žádost byla omezena pro {0} koncového bodu. Maximální souběžnost pro koncový bod je {1}. |
| TooManyConcurrentRequests | Poslalo příliš mnoho souběžných požadavků. |
| TooManyHostsBeingInitialized | Příliš mnoho hostitelů během inicializace ve stejnou dobu. Vezměte v úvahu omezení využití sítě / opakování. |
| TooManyHostsBeingInitializedPerModel | Příliš mnoho hostitelů během inicializace ve stejnou dobu. Vezměte v úvahu omezení využití sítě / opakování. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (stavový kód HTTP 504)
 
Operaci nešlo dokončit v povoleném čase.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| BackendInitializationTimeout | Inicializace webové služby nelze dokončit v povoleném čase. |
| BackendScoreTimeout | Spuštění webové služby požadavek nešlo dokončit v povoleném čase. |
 
