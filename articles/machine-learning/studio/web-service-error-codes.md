---
title: Kódy chyb Azure Machine Learning REST API | Microsoft Docs
description: Tyto kódy chyb mohou být vráceny při operace na webové služby Azure Machine Learning.
keywords: ''
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 0ba44b2a93bcd542db1350def2d0554c8c44233c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="machine-learning-rest-api-error-codes"></a>Strojového učení kódy chyb rozhraní API REST
 
Následující kódy chyb mohou být vráceny při operace na webové služby Azure Machine Learning.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (kód stavu HTTP 400)
 
Zadán neplatný argument.
 
Tato třída chyb znamená, že argument zadaný někde byl neplatný. To může být přihlašovacích údajů nebo umístění Azure storage na něco předaný webovou službu. Podívejte se prosím na pole "kód" Chyba v části "Podrobnosti" diagnostiky, které konkrétní argument byl neplatný.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| BadParameterValue | Zadaná hodnota parametru nesplňuje pravidla parametru u parametru |
| BadSubscriptionId | Id, který slouží ke stanovení skóre předplatného není ta, která je součástí prostředku |
| BadVersionCall | Byl předán parametr neplatné verze během volání rozhraní API: {0}. Zkontrolujte stránku nápovědy rozhraní API pro předávání správnou verzi a zkuste to znovu. |
| BatchJobInputsNotSpecified | Požadované input(s) nebyly zadaný u žádosti: {0}. Přesvědčte se, zda je zadán veškerá vstupní data a zkuste to znovu. |
| BatchJobInputsTooManySpecified | Žádost zadat další vstupy než definované ve službě. Seznam přijatý input(s): {0}. Přesvědčte se, zda je správně zadán veškerá vstupní data a zkuste to znovu. |
| BlobNameTooLong | Cestu k úložišti objektů blob v Azure zadaná pro výstup diagnostiky je příliš dlouhá: {0}. Zkrácení cesty a zkuste to znovu. |
| BlobNotFound | Nelze získat přístup k zadaný objekt blob systému Azure - {0}.  Chybová zpráva Azure: {1}. |
| ContainerIsEmpty | Nebyly poskytnuty žádné název kontejneru úložiště Azure. Zadejte název platné kontejneru a zkuste to znovu. |
| ContainerSegmentInvalid | Název kontejneru neplatný. Zadejte název platné kontejneru a zkuste to znovu. |
| ContainerValidationFailed | Objekt BLOB kontejneru ověření se nezdařilo s touto chybou: {0}. |
| DataTypeNotSupported | Nepodporovaný datový typ zadaný. Zadejte platný datový typ (typy) a zkuste to znovu. |
| DuplicateInputInBatchCall | Dávková žádost je neplatný. Nelze zadat jednotlivé a vícenásobné vstup ve stejnou dobu. Odeberte jeden z těchto položek z požadavku a zkuste to znovu. |
| ExpiryTimeInThePast | Zadaný čas vypršení platnosti je v minulosti: {0}. Zadejte budoucí vypršení platnosti čas v UTC a zkuste to znovu. Nikdy nevyprší, nastavte hodnotu doby vypršení platnosti na hodnotu NULL. |
| IncompleteSettings | Nastavení diagnostiky jsou neúplné. |
| InputBlobRelativeLocationInvalid | K dispozici žádný název objektu blob úložiště Azure. Zadejte platný objekt blob název a akci opakujte. |
| InvalidBlob | Specifikace neplatný objekt blob pro objekt blob: {0}. Ověřte, že připojovací řetězec / relativní cestu nebo specifikace token SAS je správný a zkuste to znovu. |
| InvalidBlobConnectionString | Z připojovacího řetězce zadaného pro jeden z objektů BLOB vstupu a výstupu neplatné: {0}. To opravte a zkuste to znovu. |
| InvalidBlobExtension | Odkaz na objekt blob: {0} má neplatný nebo chybějící souboru rozšíření. Podporovaný přípony souborů pro tento typ výstupu jsou: "{1}". |
| InvalidInputNames | Neplatná služba vstupní názvy zadané v žádosti: {0}. Mapování vstupní data na vstupy správné služby a akci opakujte. |
| InvalidOutputOverrideName | Název přepsání neplatný výstup: {0}. Služba nemá do uzlu výstup s tímto názvem. Předejte prosím název uzlu správný výstup přepsat (platí malá a velká písmena). |
| InvalidQueryParameter | Neplatný parametr dotazu '{0}'. {1} |
| MissingInputBlobInformation | Chybí informace o objektu blob úložiště Azure. Zadejte platný připojovací řetězec a relativní cestu nebo identifikátor URI a zkuste to znovu. |
| MissingJobId | Zadané Id žádná úloha. Úlohu Id je vrácena, pokud úloha byla odeslána poprvé. Ověřte správnost Id úlohy a zkuste to znovu. |
| MissingKeys | Zadáno žádné klíče nebo jeden z primární nebo sekundární klíč není k dispozici. |
| MissingModelPackage | Žádné Id balíčku modelu nebo zadaný balíček modelu. Zadejte platný model balíček Id nebo modelu balíčku a zkuste to znovu. |
| MissingOutputOverrideSpecification | V požadavku chybí specifikace objektů blob pro výstup přepsání {0}. Zadejte platný objekt blob umístění s požadavkem, nebo odeberte výstupu, pokud se požaduje přepsání není umístění. |
| MissingRequestInput | Webová služba očekává vstup, ale nebyl poskytnut žádný vstup. Ujistěte se, že jsou k dispozici platné vstupní hodnoty podle publikované vstupních portů v modelu a zkuste to znovu. |
| MissingRequiredGlobalParameters | Ne všechny požadované parametry webové služby poskytuje. Ověřte správnost parametry očekává u modulů a akci opakujte. |
| MissingRequiredOutputOverrides | Při volání metody koncový bod šifrované služby, které je nutné předat výstup přepsání pro všechny služby výstupy. Chybí přepsání v tuto chvíli pro tyto výstupy: {0} |
| MissingWebServiceGroupId | Zadané Id žádná skupina webové služby. Zadejte Id platný webové služby skupiny a zkuste to znovu. |
| MissingWebServiceId | Zadané Id žádné webové služby. Zadejte platný webovou službu Id a zkuste to znovu. |
| MissingWebServicePackage | Žádný balíček služby web poskytuje. Zadejte balíček platný webové služby a zkuste to znovu. |
| MissingWorkspaceId | Žádné pracovní prostor pro zadané Id. Zadejte platné Id pracovního prostoru a zkuste to znovu. |
| ModelConfigurationInvalid | Neplatný model konfigurace v balíčku modelu. Zkontrolujte konfiguraci modelu obsahuje definici koncové výstup, – std chyba koncovému bodu, a – std koncový bod a zkuste to znovu. |
| ModelPackageIdInvalid | Neplatný model balíček ID. Ověřte správnost Id balíčku modelu a zkuste to znovu. |
| RequestBodyInvalid | Žádné poskytuje textu žádosti nebo Chyba při deserializaci textu požadavku. |
| RequestIsEmpty | Není zadaný žádný požadavek. Zadejte platnou žádost a zkuste to znovu. |
| UnexpectedParameter | Neočekávané parametry zadané. Ověřte všechny názvy parametrů jsou správně zadané, pouze očekávané parametrů a zkuste to znovu. |
| Neznámé chyby | Neznámá chyba |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Nelze změnit požadavky souběžných požadavků pro webovou službu {0}. |
| WebServiceIdInvalid | Zadané id neplatný webové služby. Id webové služby musí být platný identifikátor guid. |
| WebServiceTooManyConcurrentRequestRequirement | Nelze nastavit požadavek na počtu souběžných požadavků na více než {0}. |
| WebServiceTypeInvalid | Zadaný typ neplatný webové služby. Ověřte, zda že je správný typ platný webové služby a akci opakujte. Typy platný webové služby: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (kód stavu HTTP 400)
 
Zadán neplatný uživatel argument.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| InputMismatchError | Vstupních dat neodpovídá schématu vstupní port. |
| InputParseError | Analýza vstupu vektoru se nezdařilo.  Ověřte, zda že vstupní vektoru má správný počet sloupců a datové typy.  Další podrobnosti: {0}. |
| MissingRequiredGlobalParameters | Chybí parametry očekává webovou službou. Ověřte, zda jsou všechny požadované parametry očekává webovou službou správné a zkuste to znovu. |
| UnexpectedParameter | Ověřte jsou předávány pouze požadované parametry očekávanou webovou službu a zkuste to znovu. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (kód stavu HTTP 400)
 
Žádost je neplatný v aktuálním kontextu.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| CannotStartJob | Úlohu nelze spustit, protože je ve stavu {0}. |
| IncompatibleModel | Model je kompatibilní s verzí požadavku. Verze žádosti podporuje pouze jeden element datatable výstup modely. |
| MultipleInputsNotAllowed | Model nepovoluje více vstupů. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (kód stavu HTTP 400)
 
Spouštění modulu došlo k chybě interní knihovna.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (kód stavu HTTP 400)
 
Spouštění modulu došlo k chybě.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (kód stavu HTTP 400)
 
Balíček neplatný webové služby. Ověřte správnost zadané balíček webové služby a akci opakujte.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| FormatError | Balíček webové služby je poškozený. Podrobnosti: {0} |
| RuntimesError | Graf balíček webové služby je neplatný. Podrobnosti: {0} |
| ValidationError | Graf balíček webové služby je neplatný. Podrobnosti: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Neoprávněné (kód stavu HTTP 401)
 
Požadavek není autorizovaný k přístupu k prostředku.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| AdminRequestUnauthorized | Neautorizováno |
| ManagementRequestUnauthorized | Neautorizováno |
| ScoreRequestUnauthorized | Zadané neplatné přihlašovací údaje. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (kód stavu HTTP 404)
 
Prostředek se nenašel.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| ModelPackageNotFound | Modelu balíček nebyl nalezen. Ověřte správnost Id balíčku modelu a zkuste to znovu. |
| WebServiceIdNotFoundInWorkspace | Webová služba v rámci tohoto pracovního prostoru nebyl nalezen. Došlo k neshodě mezi webServiceId a ID pracovního prostoru. Ověřte webovou službu, zadaná je součástí pracovního prostoru a zkuste to znovu. |
| WebServiceNotFound | Webové služby nebyl nalezen. Ověřte správnost Id webovou službu a zkuste to znovu. |
| WorkspaceNotFound | Pracovní prostor nebyl nalezen. Ověřte správnost Id pracovního prostoru a akci opakujte. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (kód stavu HTTP 408)
 
Operaci nešlo dokončit v povoleném čase.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| RequestCanceled | Požadavek byl zrušen klientem. |
| ScoreRequestTimeout | Vypršel časový limit provádění požadavku. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (kód stavu HTTP 409)
 
Prostředek již existuje.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Název parametru neplatný výstup. Zkuste použít modul editor metadata přejmenování sloupců a zkuste to znovu. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (kód stavu HTTP 413)
 
Model překročil přidělenou kvótu paměti.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| OutOfMemoryLimit | Model využívat více paměti, než byla přidělena pro ni. Maximální povolená velikost paměti pro model je {0} MB. Zkontrolujte prosím váš model problémů. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (kód stavu HTTP 500)
 
Spuštění došlo k vnitřní chybě.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | Proces kontejneru selhal s chybou systému |
| ContainerProcessTerminatedWithUnknownError | Došlo k chybě procesu kontejneru k neznámé chybě |
| ContainerValidationFailed | Objekt BLOB kontejneru ověření se nezdařilo s touto chybou: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Žádné argumenty. Ověřte, zda platnými argumenty jsou předávány a zkuste to znovu. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Id portu = {0} má nepodporovaný datový typ: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Generování swagger se nezdařila, podrobnosti: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| Neznámé chyby |  |
| UnknownJobStatusCode | Neznámá úloha kódem stavu {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, podrobnosti: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (kód stavu HTTP 500)
 
Spuštění došlo k vnitřní chybě. Systém nemá dostatek paměti. Zkuste to prosím znovu.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (kód stavu HTTP 500)
 
Neplatný model balíček. Ověřte zadaný balíček modelu je správný a zkuste to znovu.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (kód stavu HTTP 500)
 
Balíček neplatný webové služby. Ověřte správnost zadané webového balíčku a zkuste to znovu.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| ModuleError | Graf balíček webové služby je neplatný. Podrobnosti: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (kód stavu HTTP 503)
 
Požadavek nelze provést jako kontejnery není inicializován.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (kód stavu HTTP 503)
 
Služba je dočasně nedostupná.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| NoMoreResources | Pro požadavek k dispozici žádné prostředky. |
| RequestThrottled | Žádost byla omezena pro koncový bod {0}. Maximální souběžnosti pro koncový bod je {1}. |
| TooManyConcurrentRequests | Moc souběžných žádostí, které jsou odeslána. |
| TooManyHostsBeingInitialized | Příliš mnoho hostitelé během inicializace ve stejnou dobu. Vezměte v úvahu omezení / opakování. |
| TooManyHostsBeingInitializedPerModel | Příliš mnoho hostitelé během inicializace ve stejnou dobu. Vezměte v úvahu omezení / opakování. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (stavový kód HTTP 504)
 
Operaci nešlo dokončit v povoleném čase.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| BackendInitializationTimeout | Inicializace webové služby nelze dokončit v povoleném čase. |
| BackendScoreTimeout | Webové služby žádost o spuštění nebylo možné dokončit v povoleném čase. |
 
