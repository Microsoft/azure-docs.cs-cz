---
title: Změna nastavení clusteru Azure Service Fabric
description: Tento článek popisuje nastavení prostředků infrastruktury a zásady upgradu prostředků, které můžete přizpůsobit.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: 8ca40791e625f1ea5904c4e2516e3f211ba551cf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477891"
---
# <a name="customize-service-fabric-cluster-settings"></a>Přizpůsobení nastavení clusteru Service Fabric
Tento článek popisuje různá nastavení prostředků infrastruktury pro cluster Service Fabric, které můžete přizpůsobit. U clusterů hostovaných v Azure můžete nastavení přizpůsobit na [webu Azure Portal](https://portal.azure.com) nebo pomocí šablony Azure Resource Manager. Další informace najdete [v tématu Upgrade konfigurace clusteru Azure](service-fabric-cluster-config-upgrade-azure.md). U samostatných clusterů můžete přizpůsobit nastavení aktualizací souboru *ClusterConfig.json* a provedením upgradu konfigurace v clusteru. Další informace naleznete [v tématu Upgrade konfigurace samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md).

Existují tři různé zásady upgradu:

- **Dynamické** – změny dynamické konfigurace nezpůsobí žádné procesy restartování procesů Service Fabric nebo procesy hostitele služby. 
- **Statické** – změny statické konfigurace způsobí restartování uzlu Service Fabric, aby se akce spotřebovává. Služby na uzlech budou restartovány.
- **NotAllowed** – Tato nastavení nelze změnit. Změna těchto nastavení vyžaduje zničení clusteru a vytvoření nového clusteru. 

Následuje seznam nastavení prostředků fabric, které můžete přizpůsobit, uspořádané podle oddílu.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|řetězec, výchozí hodnota je "Žádný"|Statická| Tím se neověřuje certifikát serveru. požadavek úspěšný. Odkazovat na konfiguraci ServiceCertificateThumbprints pro čárka-oddělený seznam kryptografických otisků vzdálených certifikátů, které může důvěřovat reverzní proxy server. Odkazovat na config ServiceCommonNameAndIssuer pro název subjektu a vydavatel kryptografický otisk vzdálených certifikátů, které může reverzní proxy server důvěřovat. Další informace naleznete v [tématu Reverzní proxy zabezpečené připojení](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, výchozí hodnota je 16384 |Dynamická| Udává velikost pro blok v bajtů slouží ke čtení těla. |
|Příznak CrlChecking|uint, výchozí hodnota je 0x40000000 |Dynamická| Příznaky pro ověření řetězu certifikátů aplikace/služby; Například kontrola CRL 0x1000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x8000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Nastavení na 0 zakáže kontrolu CRL Úplný seznam podporovaných hodnot je dokumentován dwFlags certGetCertificateChain:https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|Výchozí httprequesttimeout |Čas v sekundách. výchozí hodnota je 120 |Dynamická|Určete časový rozsah v sekundách.  Poskytuje výchozí časový limit požadavku pro požadavky http zpracovávané v bráně aplikace http. |
|ForwardClientCertificate|bool, výchozí hodnota je NEPRAVDA|Dynamická|Pokud je nastavena na false, reverzní proxy server nebude požadovat o klientský certifikát. Pokud je nastavena na hodnotu true, reverzní proxy server bude požadovat klientský certifikát během handshake SSL a předat base64 kódovaný formát pem řetězec do služby v záhlaví s názvem X-Client-Certificate.The služba může selhat požadavek s příslušným stavovým kódem po kontrole dat certifikátu. Pokud je to pravda a klient nepředstavuje certifikát, reverzní proxy server předá prázdnou hlavičku a nechá službu zpracovávat případ. Reverzní proxy server bude fungovat jako průhledná vrstva. Další informace naleznete v [tématu Nastavení ověřování klientských certifikátů](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |řetězec, výchozí hodnota je "Žádný" |Statická| Označuje typ pověření zabezpečení, které mají být použity v koncovém bodě brány aplikace http Platné hodnoty jsou None/X509. |
|GatewayX509CertificateFindType |řetězec, výchozí je "FindByThumbprint" |Dynamická| Označuje, jak hledat certifikát v úložišti určeném podporovanou hodnotou GatewayX509CertificateStoreName: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | řetězec, výchozí hodnota je "" |Dynamická| Hodnota vyhledávacího filtru použitá k vyhledání certifikátu http brány aplikace. Tento certifikát je nakonfigurovaný v koncovém bodě https a v případě potřeby služby ho také můžou použít k ověření identity aplikace. FindValue je vyhledán jako první; a pokud to neexistuje; FindValueSecondary je vyhledán. |
|GatewayX509CertificateFindValueSekundární | řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru použitá k vyhledání certifikátu http brány aplikace. Tento certifikát je nakonfigurovaný v koncovém bodě https a v případě potřeby služby ho také můžou použít k ověření identity aplikace. FindValue je vyhledán jako první; a pokud to neexistuje; FindValueSecondary je vyhledán.|
|Název_úložiště GatewayX509CertificateStoreName |řetězec, výchozí hodnota je "My" |Dynamická| Název úložiště certifikátů X.509, které obsahuje certifikát pro bránu aplikace http. |
|HttpRequestConnectTimeout|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(5)|Dynamická|Určete časový rozsah v sekundách.  Poskytuje časový čas připojení pro požadavky http odesílané z brány aplikace http.  |
|Chyba IgnorCrlOfflineError|bool, výchozí hodnota je TRUE|Dynamická|Určuje, zda má být chyba crl offline ignorována pro ověření certifikátu aplikace/služby. |
|IsEnabled |Bool, výchozí hodnota je false |Statická| Povolí/zakáže httpapplicationgateway. HttpApplicationGateway je ve výchozím nastavení zakázána a tuto konfiguraci je třeba nastavit tak, aby byla povolena. |
|NumberOfParallelOperations | Uint, výchozí hodnota je 5000 |Statická|Počet čtení, která mají být odeslána do fronty serveru HTTP. To řídí počet souběžných požadavků, které mohou být splněny httpgateway. |
|RemoveServiceResponseHeaders|řetězec, výchozí hodnota je "Datum; Server"|Statická|Středník/ čárka-oddělený seznam hlaviček odpovědí, které budou odebrány z odpovědi služby; před předáním klientovi. Pokud je nastavena na prázdný řetězec; předat všechny hlavičky vrácené službou jako-je. Tj Nepřepisuje datum a server |
|ResolveServiceBackoffInterval |Čas v sekundách, výchozí hodnota je 5 |Dynamická|Určete časový rozsah v sekundách.  Poskytuje výchozí interval back-off před opakováním operace služby řešení selhání. |
|Režim SecureOnlyMode|bool, výchozí hodnota je NEPRAVDA|Dynamická| SecureOnlyMode: true: Reverzní proxy bude předávat pouze služby, které publikují zabezpečené koncové body. false: Reverzní proxy server může předávat požadavky na zabezpečené/nezabezpečené koncové body. Další informace naleznete v [tématu Reverse proxy logika výběru koncového bodu](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Seznam kryptografických otisků vzdálených certifikátů oddělených čárkami, kterým může reverzní proxy server důvěřovat. Další informace naleznete v [tématu Reverzní proxy zabezpečené připojení](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|X509NameMap, výchozí hodnota je Žádný|Dynamická| Otisk názvu subjektu a vystavitního objektu vzdálených certifikátů, kterým může reverzní proxy server důvěřovat. Další informace naleznete v [tématu Reverzní proxy zabezpečené připojení](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>Služba BackupRestoreService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, výchozí hodnota je 0|Statická|MinReplicaSetSize pro Službu BackupRestoreService |
|Omezení umístění|řetězec, výchozí hodnota je ""|Statická|    Služba PlacementConstraints for BackupRestore |
|Tajný encryptioncertthumbprint|řetězec, výchozí hodnota je ""|Dynamická|Kryptografický šifrovací certifikát X509 |
|SecretEncryptionCertX509Název úložiště|řetězec, výchozí hodnota je "My"|    Dynamická|    Označuje certifikát, který má být použit pro šifrování a dešifrování creds Název úložiště certifikátů X.509, který se používá k šifrování přihlašovacích údajů úložiště dešifrování používaných službou Obnovení zálohování |
|Cílová replikaSetSize|int, výchozí hodnota je 0|Statická| TargetReplicaSetSize pro Službu BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Povolitzásady řazení vlastních upgradů | Bool, výchozí hodnota je false |Dynamická|Určuje, zda jsou povoleny zásady řazení vlastního upgradu. Používá se k provedení dvoufázového upgradu, který tuto funkci umožňuje. Service Fabric 6.5 přidává podporu pro určení zásad řazení pro upgradovací domény během upgradu clusteru nebo aplikace. Podporované zásady jsou numerické, lexikografické, reverzní numerické a reverzní lexikografické. Výchozí hodnota je Numeric. Aby bylo možné tuto funkci používat, musí být nastavení manifestu clusteru ClusterManager/ AllowCustomUpgradeSortPolicies nastaveno na hodnotu True jako druhý krok upgradu konfigurace po dokončení upgradu kódu SF 6.5. Je důležité, aby se to stalo ve dvou fázích, jinak může být upgrade kódu zmatený ohledně pořadí upgradu během prvního upgradu.|
|Povolitvýchozí službyUpgrade | Bool, výchozí hodnota je false |Dynamická|Povolte upgrade výchozích služeb během upgradu aplikace. Výchozí popisy služeb by byly po upgradu přepsány. |
|FabricUpgradeHealthCheckInterval |Čas v sekundách, výchozí hodnota je 60 |Dynamická|Četnost kontroly stavu během sledovaného upgradu prostředků Fabric |
|FabricUpgradeStatusPollInterval |Čas v sekundách, výchozí hodnota je 60 |Dynamická|Frekvence dotazování pro stav upgradu prostředků. Tato hodnota určuje rychlost aktualizace pro jakékoli volání GetFabricUpgradeProgress |
|Vyrovnávací paměť ImageBuilderTimeoutBuffer |Čas v sekundách, výchozí hodnota je 3 |Dynamická|Určete časový rozsah v sekundách. Doba, která má umožnit, aby se chyby časového doby konkrétního tvůrce obrázků vrátily klientovi. Pokud je tato vyrovnávací paměť příliš malá; potom klient časový mat před serverem a získá obecnou chybu časového času. |
|InfrastructureTaskHealthCheckRetryTimeout | Čas v sekundách, výchozí hodnota je 60 |Dynamická|Určete časový rozsah v sekundách. Doba, po kterou je třeba strávit opakováním neúspěšných kontrol stavu při následném zpracování úlohy infrastruktury. Při sledování předané kontroly stavu se tento časovač resetuje. |
|InfrastructureTaskHealthCheckStableDuration | Čas v sekundách, výchozí hodnota je 0|Dynamická| Určete časový rozsah v sekundách. Doba sledování po sobě jdoucích prošlých kontrol stavu před úspěšným dokončením úlohy infrastruktury po zpracování. Při sledování neúspěšné kontroly stavu se tento časovač resetuje. |
|InfrastructureTaskHealthCheckWaitDuration |Čas v sekundách, výchozí hodnota je 0|Dynamická| Určete časový rozsah v sekundách. Doba čekání před zahájením kontrolstavu po zpracování úlohy infrastruktury. |
|Interval zpracování úloh y InfrastructureTaskProcessingInterval | Čas v sekundách, výchozí hodnota je 10 |Dynamická|Určete časový rozsah v sekundách. Interval zpracování používaný stavovým počítačem zpracování úloh infrastruktury. |
|MaxCommunicationTimeout |Čas v sekundách, výchozí hodnota je 600 |Dynamická|Určete časový rozsah v sekundách. Maximální časový limit pro interní komunikaci mezi ClusterManager a dalšími systémovými službami (tj. Služba pojmenování; správce služeb při selhání apod.). Tento časový limit by měl být menší než globální MaxOperationTimeout (jako může být více komunikace mezi součástmi systému pro každou operaci klienta). |
|MaxDataMigrationTimeout |Čas v sekundách, výchozí hodnota je 600 |Dynamická|Určete časový rozsah v sekundách. Maximální časový limit pro operace obnovení migrace dat po dokončení upgradu prostředků prostředků. |
|MaxOperationRetryDelay |Čas v sekundách, výchozí hodnota je 5|Dynamická| Určete časový rozsah v sekundách. Maximální zpoždění pro interní opakování při selhání. |
|Maximální doba provozu |Čas v sekundách, výchozí hodnota je MaxValue |Dynamická| Určete časový rozsah v sekundách. Maximální globální časový limit pro operace interního zpracování na ClusterManager. |
|MaxTimeoutRetryBuffer | Čas v sekundách, výchozí hodnota je 600 |Dynamická|Určete časový rozsah v sekundách. Maximální časový limit operace při interní opakování z `<Original Time out> + <MaxTimeoutRetryBuffer>`důvodu časového limitu je . Další časový čas je přidán v přírůstcích MinOperationTimeout. |
|Časový režim minoperace | Čas v sekundách, výchozí hodnota je 60 |Dynamická|Určete časový rozsah v sekundách. Minimální globální časový čas pro operace interního zpracování na ClusterManager. |
|MinReplicaSetSize |Int, výchozí hodnota je 3 |Není povoleno|MinReplicaSetSize pro ClusterManager. |
|Omezení umístění | řetězec, výchozí hodnota je "" |Není povoleno|PlacementConstraints pro ClusterManager. |
|QuorumLossWaitDuration |Čas v sekundách, výchozí hodnota je MaxValue |Není povoleno| Určete časový rozsah v sekundách. QuorumLossWaitDuration pro ClusterManager. |
|ReplicaRestartWaitDuration |Čas v sekundách, výchozí hodnota \* je (60,0 30)|Není povoleno|Určete časový rozsah v sekundách. ReplicaRestartWaitDuration pro ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Čas v sekundách, výchozí hodnota je 1200 |Dynamická| Určete časový rozsah v sekundách. Pokud ReplicaSetCheckTimeout je nastavena na maximální hodnotu DWORD; pak je přepsána hodnotou této konfigurace pro účely vrácení zpět. Hodnota použitá pro převrácení není nikdy přepsána. |
|SkipRollbackUpdateDefaultService | Bool, výchozí hodnota je false |Dynamická|Cm přeskočí vrácení aktualizovaných výchozích služeb během vrácení upgradu aplikace. |
|Doba trvání pohotovostního režimu | Čas v sekundách, výchozí hodnota je \* (3600.0 2)|Není povoleno|Určete časový rozsah v sekundách. Doba trvání standbyReplicaKeepDuration pro clustermanager. |
|Cílová replikaSetSize |Int, výchozí hodnota je 7 |Není povoleno|TargetReplicaSetSize pro ClusterManager. |
|UpgradeHealthCheckInterval |Čas v sekundách, výchozí hodnota je 60 |Dynamická|Četnost kontrol stavu během upgradů monitorovaných aplikací |
|UpgradeStavInterval |Čas v sekundách, výchozí hodnota je 60 |Dynamická|Frekvence dotazování pro stav upgradu aplikace. Tato hodnota určuje rychlost aktualizace pro jakékoli volání GetApplicationUpgradeProgress. |
|Požadavek úplného klienta | Bool, výchozí hodnota je false |Dynamická| Kompletní požadavek klienta při přijetí CM. |

## <a name="common"></a>Společné

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Interval perfMonitor |Čas v sekundách, výchozí hodnota je 1 |Dynamická|Určete časový rozsah v sekundách. Interval sledování výkonu. Nastavení na hodnotu 0 nebo zápornou hodnotu zakáže monitorování. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentaceEmptyNodeDistributionPolicy
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|KeyIntegerValueMap, výchozí hodnota je Žádný|Dynamická|Určuje, že defragmentace zásad následuje při vyprazdňování uzlů. Pro danou metriku 0 označuje, že SF by se měl pokusit defragmentovat uzly rovnoměrně mezi UD a FD; 1 označuje pouze to, že uzly musí být defragmentovány. |

## <a name="defragmentationmetrics"></a>DefragmentaceMetrické
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|KeyBoolValueMap, výchozí hodnota je Žádný|Dynamická|Určuje sadu metrik, které by měly být použity pro defragmentaci a nikoli pro vyrovnávání zatížení. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentaceMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|KeyDoubleValueMap, výchozí hodnota je Žádný|Dynamická|Určuje počet volných uzlů, které jsou potřeba zvážit defragmentaci clusteru zadáním procenta v rozsahu [0,0 - 1,0] nebo počtu prázdných uzlů jako číslo >= 1,0 |

## <a name="diagnostics"></a>Diagnostika

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Bool, výchozí hodnota je pravda | Dynamická | Vylučte požadavky HTTP, které nemají vliv na stav clusteru z auditování. V současné době; jsou vyloučeny pouze požadavky typu "GET"; ale to se může změnit. |
|AppDiagnosticStoreAccessVyžaduje zosobnění |Bool, výchozí hodnota je pravda | Dynamická |Zda je vyžadováno zosobnění při přístupu k diagnostickým úložištím jménem aplikace. |
|AppEtwTraceDeletionAgeInDays |Int, výchozí hodnota je 3 | Dynamická |Počet dní, po jejichž uplynutí odstraníme staré ETL soubory obsahující trasování aplikace ETW. |
|ApplicationLogsFormatVersion |Int, výchozí hodnota je 0 | Dynamická |Verze pro formát protokolů aplikací. Podporované hodnoty jsou 0 a 1. Verze 1 obsahuje více polí ze záznamu události ETW než verze 0. |
|AuditovathttpRequests |Bool, výchozí hodnota je false | Dynamická | Zapněte nebo vypněte auditování protokolu HTTP. Účelem auditování je zobrazit aktivity, které byly provedeny proti clusteru; včetně toho, kdo žádost inicioval. Všimněte si, že se jedná o nejlepší pokus protokolování; a může dojít ke ztrátě stopování. Požadavky HTTP s ověřováním "Uživatel" se nezaznamenávají. |
|Zachytithttptelemetretrie|Bool, výchozí hodnota je pravda | Dynamická | Zapněte nebo vypněte telemetrii HTTP. Účelem telemetrie je pro Service Fabric, aby bylo možné zachytit telemetrická data pomoci plánovat budoucí práci a identifikovat problémové oblasti. Telemetrie nezaznamenává žádné osobní údaje ani tělo žádosti. Telemetrie zachytí všechny požadavky HTTP, pokud není nakonfigurováno jinak. |
|ClusterId |Řetězec | Dynamická |Jedinečné id clusteru. To je generováno při vytvoření clusteru. |
|Instance spotřebitele |Řetězec | Dynamická |Seznam instancí příjemce DCA. |
|DiskFullSafetyinmb |Int, výchozí hodnota je 1024 | Dynamická |Zbývající místo na disku v MB chránit před použitím DCA. |
|EnableCircularTraceSession |Bool, výchozí hodnota je false | Statická |Příznak označuje, zda mají být použity cyklické relace trasování. |
|EnablePlatformEventsFileSink |Bool, výchozí hodnota je false | Statická |Povolení nebo zakázání událostí platformy zapisovaných na disk |
|EnableTelemetry |Bool, výchozí hodnota je pravda | Dynamická |To se chystá povolit nebo zakázat telemetrii. |
|ChybyPouzeHttpTelemetrie | Bool, výchozí hodnota je false | Dynamická | Pokud je povoleno zachycení telemetrie HTTP; zachytit pouze neúspěšné požadavky. To pomáhá snížit počet událostí generovaných pro telemetrii. |
|HttpTelemetryCapturePercentage | int, výchozí hodnota je 50 | Dynamická | Pokud je povoleno zachycení telemetrie HTTP; zachytit pouze náhodné procento požadavků. To pomáhá snížit počet událostí generovaných pro telemetrii. |
|MaxDiskQuotaInMB |Int, výchozí hodnota je 65536 | Dynamická |Disková kvóta v MB pro soubory protokolu programu Windows Fabric. |
|Instance výrobce |Řetězec | Dynamická |Seznam instancí výrobců DCA. |

## <a name="dnsservice"></a>Služba DnsService
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|bool, výchozí hodnota je NEPRAVDA|Statická|Příznak umožňující podporu pro dotazy DNS pro dělené služby. Funkce je ve výchozím nastavení vypnuta. Další informace naleznete v [tématu Service Fabric DNS Service Service.](service-fabric-dnsservice.md)|
|Počet instancí|int, výchozí hodnota je -1|Statická|Výchozí hodnota je -1, což znamená, že služba DnsService je spuštěna na každém uzlu. OneBox potřebuje, aby to bylo nastaveno na 1, protože DnsService používá dobře známý port 53, takže nemůže mít více instancí ve stejném počítači.|
|IsEnabled|bool, výchozí hodnota je NEPRAVDA|Statická|Povolí/zakáže službu DnsService. Služba DnsService je ve výchozím nastavení zakázána a tuto konfiguraci je třeba nastavit tak, aby byla povolena. |
|Předběžné nastavení oddílu|řetězec, výchozí hodnota je "--"|Statická|Řídí hodnotu řetězce předpony oddílu v dotazech DNS pro dělené služby. Hodnota : <ul><li>By měla být kompatibilní s rfc, protože bude součástí dotazu DNS.</li><li>By neměl obsahovat tečku ,.', jako tečka narušuje chování přípony DNS.</li><li>By neměla být delší než 5 znaků.</li><li>Nemůže se nesmije prázdný řetězec.</li><li>Pokud je přepsáno nastavení PartitionPrefix, musí být přepsána přípona PartitionSuffix a naopak.</li></ul>Další informace naleznete v [tématu Service Fabric DNS Service.](service-fabric-dnsservice.md).|
|PartitionSuffix|řetězec, výchozí hodnota je ""|Statická|Řídí hodnotu řetězce přípony oddílu v dotazech DNS pro dělené služby. Hodnota : <ul><li>By měla být kompatibilní s rfc, protože bude součástí dotazu DNS.</li><li>By neměl obsahovat tečku ,.', jako tečka narušuje chování přípony DNS.</li><li>By neměla být delší než 5 znaků.</li><li>Pokud je přepsáno nastavení PartitionPrefix, musí být přepsána přípona PartitionSuffix a naopak.</li></ul>Další informace naleznete v [tématu Service Fabric DNS Service.](service-fabric-dnsservice.md). |

## <a name="eventstoreservice"></a>Služba EventStoreService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, výchozí hodnota je 0|Statická|Služba MinReplicaSetSize pro eventstore |
|Omezení umístění|řetězec, výchozí hodnota je ""|Statická|    Služba PlacementConstraints pro EventStore |
|Cílová replikaSetSize|int, výchozí hodnota je 0|Statická| Služba TargetReplicaSetSize pro eventstore |

## <a name="fabricclient"></a>FabricClient

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Čas v sekundách, výchozí hodnota je 2 |Dynamická|Určete časový rozsah v sekundách. Časový limit připojení pro každý pokus klienta o otevření připojení k bráně.|
|Časovod operace stavu |Čas v sekundách, výchozí hodnota je 120 |Dynamická|Určete časový rozsah v sekundách. Časový čas pro zprávu sestavy odeslané Správci zdraví. |
|HealthReportRetrySendInterval |Čas v sekundách, výchozí hodnota je 30, minimum je 1 |Dynamická|Určete časový rozsah v sekundách. Interval, ve kterém součást vykazování znovu odešle akumulované sestavy stavu správci stavu. |
|Interval HealthReportSendInterval |Čas v sekundách, výchozí hodnota je 30 |Dynamická|Určete časový rozsah v sekundách. Interval, ve kterém komponenta vykazování odesílá akumulované zprávy o stavu správci stavu. |
|KeepAliveIntervalInSeconds |Int, výchozí hodnota je 20 |Statická|Interval, ve kterém fabricClient přenosu odesílá zprávy keep-alive do brány. Pro 0; keepAlive je zakázán. Musí to být kladná hodnota. |
|MaxFileSenderVlákna |Uint, výchozí hodnota je 10 |Statická|Maximální počet souborů, které jsou přenášeny paralelně. |
|Adresy node |řetězec, výchozí hodnota je "" |Statická|Kolekce adres (připojovacířetězce) na různých uzlech, které lze použít ke komunikaci se službou pojmenování. Zpočátku klient připojí výběr jedné z adres náhodně. Pokud je zadán více než jeden připojovací řetězec a připojení se nezdaří z důvodu chyby komunikace nebo časového limitu; klient přepne na použití další adresy postupně. Podrobnosti o sémantice opakovaných pokusů naleznete v části Opakování adresy pojmenování. |
|PartitionLocationCacheLimit |Int, výchozí hodnota je 100000 |Statická|Počet oddílů uložených v mezipaměti pro rozlišení služby (nastaveno na hodnotu 0 bez omezení). |
|Interval RetryBackoffInterval |Čas v sekundách, výchozí hodnota je 3 |Dynamická|Určete časový rozsah v sekundách. Interval back-off před opakováním operace. |
|ServiceChangePollInterval |Čas v sekundách, výchozí hodnota je 120 |Dynamická|Určete časový rozsah v sekundách. Interval mezi po sobě jdoucími dotazováními pro službu se změní z klienta na bránu pro registrovaná oznámení o změně služby zpětná volání. |

## <a name="fabrichost"></a>FabricHost

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Počet selhání aktivace Max |Int, výchozí hodnota je 10 |Dynamická|Toto je maximální počet, pro který systém bude opakovat neúspěšnou aktivaci před vzdání. |
|AktivaceMaxRetryInterval |Čas v sekundách, výchozí hodnota je 300 |Dynamická|Určete časový rozsah v sekundách. Maximální interval opakování pro aktivaci. Při každé nepřetržité poruše se interval opakování vypočítá jako Min( ActivationMaxRetryInterval; Nepřetržitý počet selhání * ActivationRetryBackoffInterval). |
|AktivaceInterval zpětného vypnutí |Čas v sekundách, výchozí hodnota je 5 |Dynamická|Určete časový rozsah v sekundách. Interval backoff při každé chybě aktivace;Při každé selhání nepřetržité aktivace systém zopakování aktivace pro až MaxActivationFailureCount. Interval opakování při každém pokusu je produktem selhání nepřetržité aktivace a intervalu zpětného vypnutí aktivace. |
|EnableRestartManagement |Bool, výchozí hodnota je false |Dynamická|Toto je povolit restartování serveru. |
|EnableServiceFabricAutomatickéUpdates |Bool, výchozí hodnota je false |Dynamická|To to umožňuje automatické aktualizace prostředků fabric prostřednictvím služby Windows Update. |
|EnableServiceFabricBaseUpgrade |Bool, výchozí hodnota je false |Dynamická|Toto je povolit základní aktualizace pro server. |
|FailureReportingExpeditedReportingIntervalEnabled | Bool, výchozí hodnota je pravda | Statická | Umožňuje rychlejší nahrávání v DCA, když FabricHost je v režimu hlášení selhání. |
|FailureReportingTimeout | TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(60) | Statická |Určete časový rozsah v sekundách. Časový čas pro hlášení selhání DCA v případě, že FabricHost narazí na selhání spuštění v rané fázi. | 
|RunDCAonStartupSelhání | Bool, výchozí hodnota je pravda | Statická |Určuje, zda se má spustit dca nahrát protokoly při řešení problémů se spuštěním v FabricHost. | 
|StartTimeout |Čas v sekundách, výchozí hodnota je 300 |Dynamická|Určete časový rozsah v sekundách. Časový čas pro fabricactivationmanager spuštění. |
|StopTimeout |Čas v sekundách, výchozí hodnota je 300 |Dynamická|Určete časový rozsah v sekundách. Časový čas pro aktivaci hostované služby; deaktivace a upgrade. |

## <a name="fabricnode"></a>Látková uzel

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|KlientAuthX509FindTyp |řetězec, výchozí je "FindByThumbprint" |Dynamická|Označuje, jak hledat certifikát v úložišti určeném clientAuthX509StoreName supported hodnota: FindByThumbprint; FindBySubjectName. |
|KlientAuthX509FindValue |řetězec, výchozí hodnota je "" | Dynamická|Hodnota filtru hledání použitá k vyhledání certifikátu pro výchozí roli správce FabricClient. |
|KlientAuthX509FindValueSekundární |řetězec, výchozí hodnota je "" |Dynamická|Hodnota filtru hledání použitá k vyhledání certifikátu pro výchozí roli správce FabricClient. |
|KlientAuthX509Název úložiště |řetězec, výchozí hodnota je "My" |Dynamická|Název úložiště certifikátů X.509, které obsahuje certifikát pro výchozí roli správce FabricClient. |
|ClusterX509Typ hledání |řetězec, výchozí je "FindByThumbprint" |Dynamická|Označuje, jak hledat certifikát clusteru v úložišti určeném hodnotami podporovanými názvem ClusterX509StoreName: "FindByThumbprint"; "findbysubjectname" s "findbysubjectname"; pokud existuje více shod; ten s nejvzdálenějším vypršením. |
|ClusterX509FindValue |řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru používaná k vyhledání certifikátu clusteru. |
|ClusterX509FindValueSekundární |řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru používaná k vyhledání certifikátu clusteru. |
|ClusterX509Název úložiště |řetězec, výchozí hodnota je "My" |Dynamická|Název úložiště certifikátů X.509, které obsahuje certifikát clusteru pro zabezpečení komunikace v rámci clusteru. |
|EndApplicationPortRange |Int, výchozí hodnota je 0 |Statická|Ukončovací (bez zahrnutí) portů aplikace spravovaných hostitelským subsystémem. Povinné, pokud EndpointFilteringEnabled platí v hostování. |
|ServerAuthX509Typ find |řetězec, výchozí je "FindByThumbprint" |Dynamická|Označuje, jak hledat certifikát serveru v úložišti určeném serverem ServerAuthX509StoreName Supported value: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru použitá k vyhledání certifikátu serveru. |
|ServerAuthX509FindValueSekundární |řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru použitá k vyhledání certifikátu serveru. |
|ServerAuthX509Název úložiště |řetězec, výchozí hodnota je "My" |Dynamická|Název úložiště certifikátů X.509, které obsahuje certifikát serveru pro službu entrée. |
|StartApplicationPortRange |Int, výchozí hodnota je 0 |Statická|Spuštění aplikačních portů spravovaných hostováním podsystému. Povinné, pokud EndpointFilteringEnabled platí v hostování. |
|Interval Sledování stavu |Čas v sekundách, výchozí hodnota je 300 |Statická|Určete časový rozsah v sekundách. Interval pro sledování stavu uzlu na každém uzlu a nahoru uzly na FM/FMM. |
|UserRoleClientX509FindType |řetězec, výchozí je "FindByThumbprint" |Dynamická|Označuje, jak hledat certifikát v úložišti určeném podporovanou hodnotou UserRoleClientX509StoreName: FindByThumbprint; FindBySubjectName. |
|UživatelRoleClientX509FindValue |řetězec, výchozí hodnota je "" |Dynamická|Hodnota filtru hledání použitá k vyhledání certifikátu pro výchozí roli uživatele FabricClient. |
|UživatelRoleClientX509FindValueSekundární |řetězec, výchozí hodnota je "" |Dynamická|Hodnota filtru hledání použitá k vyhledání certifikátu pro výchozí roli uživatele FabricClient. |
|UserRoleClientX509StoreName |řetězec, výchozí hodnota je "My" |Dynamická|Název úložiště certifikátů X.509, které obsahuje certifikát pro výchozí roli uživatele FabricClient. |

## <a name="failovermanager"></a>Správce převzetí služeb při selhání

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Bool, výchozí hodnota je FALSE |Dynamická|Příznak označující, zda je povoleno odebrat stav uzlu pro uzel osiva |
|BuildReplicaTimeLimit|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(3600)|Dynamická|Určete časový rozsah v sekundách. Časový limit pro vytváření stavové repliky; po kterém bude zahájena varovná zpráva o zdravotním stavu |
|ClusterPauseThreshold|int, výchozí hodnota je 1|Dynamická|Pokud počet uzlů v systému jít pod tuto hodnotu pak umístění; vyrovnávání zatížení; a převzetí služeb při selhání je zastaveno. |
|Vytvořit limit instancetime|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(300)|Dynamická|Určete časový rozsah v sekundách. Časový limit pro vytvoření instance bez stavů; po kterém bude zahájena varovná zpráva o zdravotním stavu |
|Očekávaná velikost clusteru|int, výchozí hodnota je 1|Dynamická|Při prvním spuštění clusteru; FM bude čekat na tolik uzlů, aby se hlásili, než začne umisťovat další služby; včetně systémových služeb, jako je pojmenování. Zvýšení této hodnoty zvyšuje čas potřebný ke spuštění clusteru; ale zabraňuje časné uzly z stává přetížené a také další pohyby, které budou nezbytné jako více uzlů přejdete do režimu online. Tato hodnota by měla být obecně nastavena na nějaký malý zlomek počáteční velikostclusteru. |
|Očekávaná doba trvání dedeaktivace|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(60.0 \* 30)|Dynamická|Určete časový rozsah v sekundách. Toto je očekávaná doba trvání uzlu k dokončení deaktivace. |
|Doba trvání_upgradu_nodeFabric|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(60.0 \* 30)|Dynamická|Určete časový rozsah v sekundách. Toto je očekávaná doba trvání uzlu, který má být upgradován během upgradu prostředků windows fabric. |
|ExpectedReplicaUpgradeDuration|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(60.0 \* 30)|Dynamická|Určete časový rozsah v sekundách. Toto je očekávaná doba trvání pro všechny repliky, které mají být upgradovány na uzlu během upgradu aplikace. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, výchozí hodnota je TRUE|Dynamická|Je-li nastavena na hodnotu true; repliky s cílovou sadou replik velikosti 1 se budou moci během inovace přesunout. |
|MinReplicaSetSize|int, výchozí hodnota je 3|Není povoleno|Toto je minimální velikost sady replik pro FM. Pokud počet aktivních replik FM klesne pod tuto hodnotu; FM odmítne změny v clusteru, dokud nebude obnoven alespoň min počet replik |
|Omezení umístění|řetězec, výchozí hodnota je ""|Není povoleno|Všechna omezení umístění pro repliky správce převzetí služeb při selhání |
|PlacementTimeLimit|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(600)|Dynamická|Určete časový rozsah v sekundách. Časový limit pro dosažení počtu cílových replik; po kterém bude zahájena varovná zpráva o zdravotním stavu |
|QuorumLossWaitDuration |Čas v sekundách, výchozí hodnota je MaxValue |Dynamická|Určete časový rozsah v sekundách. Toto je maximální doba trvání, po kterou povolíme, aby byl oddíl ve stavu ztráty kvora. Pokud je oddíl stále ve ztrátě kvora po této době; oddíl je získán ze ztráty kvora tím, že považuje dolů repliky jako ztracené. Všimněte si, že to může potenciálně vzniknout ztrátu dat. |
|ReconfigurationTimeLimit|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(300)|Dynamická|Určete časový rozsah v sekundách. Časový limit pro změnu konfigurace; po kterém bude zahájena varovná zpráva o zdravotním stavu |
|ReplicaRestartWaitDuration|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(60.0 \* 30)|Není povoleno|Určete časový rozsah v sekundách. Toto je ReplikaRestartWaitDuration pro službu FMService. |
| SeedNodeQuorumAdditionalBufferNodes | int, výchozí hodnota je 0 | Dynamická | Pufr semen uzly, které je třeba nahoru (spolu s kvorem semenných uzlů) FM by měl umožnit maximální (totalNumSeedNodes - (seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)) uzly sekly jít dolů. |
|Doba trvání pohotovostního režimu|Timespan, výchozí hodnota je Common::TimeSpan::FromSeconds(3600.0 \* 24 \* 7)|Není povoleno|Určete časový rozsah v sekundách. Toto je doba spánku Pro FMService. |
|Cílová replikaSetSize|int, výchozí hodnota je 7|Není povoleno|Toto je cílový počet replik FM, které bude udržovat windows fabric. Vyšší počet má za následek vyšší spolehlivost dat FM; s malým výkonem kompromisu. |
|Uživatel MaxStandByReplicaCount |Int, výchozí hodnota je 1 |Dynamická|Výchozí maximální počet replik v pohotovostním režimu, který systém uchovává pro uživatelské služby. |
|Doba trvání UserReplicaRestartWaitDuration |Čas v sekundách, výchozí \* hodnota je 60,0 30 |Dynamická|Určete časový rozsah v sekundách. Když se zobrazí trvalá replika; Windows Fabric čeká na tuto dobu trvání repliky vrátit nahoru před vytvořením nové náhradní repliky (což by vyžadovalo kopii stavu). |
|Doba trvání uživatelského standbyrepliky |Čas v sekundách, výchozí hodnota \* je \* 3600,0 24 7 |Dynamická|Určete časový rozsah v sekundách. Když trvalé repliky vrátit ze stavu dolů; je možné, že již byla nahrazena. Tento časovač určuje, jak dlouho fm bude udržovat pohotovostní repliku před jeho vyhozením. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|DokončenoActionKeepDurationVSekundách | Int, výchozí hodnota je 604800 |Statická| To je přibližně jak dlouho zachovat akce, které jsou ve stavu terminálu. To také závisí na StoredActionCleanupIntervalInSeconds; protože práce na vyčištění se provádí pouze v tomto intervalu. 604800 je 7 dní. |
|DataLossCheckPollIntervalVsekundách|int, výchozí hodnota je 5|Statická|Toto je doba mezi kontrolami, které systém provádí při čekání na ztrátu dat. Počet, kolikrát bude číslo ztráty dat zkontrolováno na interní iteraci, je DataLossCheckWaitDurationInSeconds/this. |
|DataLossCheckWaitDurationInSeconds|int, výchozí hodnota je 25|Statická|Celková doba; v sekundách; systém bude čekat na ztrátu dat. To se používá interně při Volání rozhraní API StartPartitionDataLossAsync(). |
|MinReplicaSetSize |Int, výchozí hodnota je 0 |Statická|MinReplicaSetSize pro FaultAnalysisService. |
|Omezení umístění | řetězec, výchozí hodnota je ""|Statická| PlacementConstraints pro FaultAnalysisService. |
|QuorumLossWaitDuration | Čas v sekundách, výchozí hodnota je MaxValue |Statická|Určete časový rozsah v sekundách. QuorumLossWaitDuration pro FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, výchozí hodnota je 600|Statická|Tento parametr se používá při volání rozhraní API ztráty dat. Určuje, jak dlouho bude systém čekat, až se replika vyloučí poté, co je na ní interně vyvolána replika. |
|ReplicaRestartWaitDuration |Čas v sekundách, výchozí hodnota je 60 minut|Statická|Určete časový rozsah v sekundách. ReplikaRestartWaitDuration pro FaultAnalysisService. |
|Doba trvání pohotovostního režimu| Čas v sekundách, výchozí hodnota je (60*24*7) minut |Statická|Určete časový rozsah v sekundách. StandByReplicaKeepDuration pro FaultAnalysisService. |
|Uložená akceCleanupIntervalInSeconds | Int, výchozí hodnota je 3600 |Statická|To je, jak často bude obchod vyčištěn. Pouze akce v terminálním stavu; a že dokončena alespoň CompletedActionKeepDurationInSeconds před budou odebrány. |
|UloženoChaosEventCleanupIntervalInSeconds | Int, výchozí hodnota je 3600 |Statická|To je, jak často bude úložiště auditováno pro vyčištění; je-li počet událostí vyšší než 30000; Úklid začne působit. |
|Cílová replikaSetSize |Int, výchozí hodnota je 0 |Statická|NOT_PLATFORM_UNIX_START TargetReplicaSetSize pro FaultAnalysisService. |

## <a name="federation"></a>metadata

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Doba trvání zapůjčení |Čas v sekundách, výchozí hodnota je 30 |Dynamická|Doba trvání zapůjčení mezi uzlovým a jeho sousedy. |
|LeaseDurationAcrossFaultDomain |Čas v sekundách, výchozí hodnota je 30 |Dynamická|Doba trvání zapůjčení mezi uzlovým a jeho sousedy napříč doménami selhání. |

## <a name="filestoreservice"></a>Služba FileStoreService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Bool, výchozí hodnota je PRAVDA|Dynamická|Konfigurovat a zjistit, zda služba úložiště souborů přijímá nahrávání souborů na základě bloku dat nebo ne během kopírování balíčku aplikace. |
|AnonymousAccessEnabled | Bool, výchozí hodnota je pravda |Statická|Povolit nebo zakázat anonymní přístup ke sdíleným položkám FileStoreService. |
|Společný název1Ntlmx509CommonName|řetězec, výchozí hodnota je ""|Statická| Běžný název certifikátu X509, který se používá ke generování hmac na CommonName1NtlmPasswordSecret při použití ověřování NTLM |
|Společný název1Ntlmx509Umístění úložiště|řetězec, výchozí hodnota je "LocalMachine"|Statická|Umístění úložiště certifikátu X509 používaného ke generování hmac na CommonName1NtlmPasswordSecret při použití ověřování NTLM |
|Společný název1Ntlmx509Název úložiště|řetězec, výchozí hodnota je "MY"| Statická|Název úložiště certifikátu X509 použitého ke generování hmac na CommonName1NtlmPasswordSecret při použití ověřování NTLM |
|CommonName2Ntlmx509CommonName|řetězec, výchozí hodnota je ""|Statická|Běžný název certifikátu X509, který se používá ke generování hmac na CommonName2NtlmPasswordSecret při použití ověřování NTLM |
|SpolečnéNázev2Ntlmx509Umístění úložiště|řetězec, výchozí hodnota je "LocalMachine"| Statická|Umístění úložiště certifikátu X509 používaného ke generování hmac na CommonName2NtlmPasswordSecret při použití ověřování NTLM |
|CommonName2Ntlmx509Název úložiště|řetězec, výchozí hodnota je "MY"|Statická| Název úložiště certifikátu X509 použitého ke generování hmac na CommonName2NtlmPasswordSecret při použití ověřování NTLM |
|CommonNameNtlmPasswordSecret|SecureString, default is Common::SecureString("")| Statická|Tajný klíč hesla, který se používal jako osivo pro generování stejného hesla při použití ověřování NTLM |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |TimeSpan, výchozí hodnota je Common::TimeSpan::FromMinutes(5)|Dynamická|Určete časový rozsah v sekundách. Časový interval mezi kontrolou místa na disku pro hlášení události stavu, když je disk blízko místa. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |TimeSpan, výchozí hodnota je Common::TimeSpan::FromMinutes(15)|Dynamická|Určete časový rozsah v sekundách. Časový interval mezi kontrolou místa na disku pro hlášení události stavu, pokud je na disku dostatek místa. |
|EnableImageStoreHealthReporting |bool, výchozí hodnota je TRUE    |Statická|Konfigurujte, chcete-li zjistit, zda má služba úložiště souborů vykazovat svůj stav. |
|FreeDiskSpaceNotificationInKB|int64, výchozí hodnota\*je 25 1024 |Dynamická|Velikost volného místa na disku, pod kterým může dojít k upozornění na stav. Minimální hodnota této konfigurace a freediskspacenotificationthresholdpercentage konfigurace se používají k určení odeslání upozornění stavu. |
|FreeDiskSpaceNotificationThresholdPercentage|double, výchozí hodnota je 0,02 |Dynamická|Procento volného místa na disku, pod kterým může dojít k upozornění na stav. Minimální hodnota této konfigurace a konfigurace FreeDiskSpaceNotificationInMB se používá k určení odesílání upozornění na stav. |
|GenerateV1CommonNameAccount| bool, výchozí hodnota je TRUE|Statická|Určuje, zda má být generován účet s algoritmem generování uživatelského jména V1. Počínaje Service Fabric verze 6.1; účet s generováním v2 je vždy vytvořen. Účet V1 je nezbytný pro upgrady z/na verze, které nepodporují generování V2 (před verzí 6.1).|
|MaxCopyOperationThreads | Uint, výchozí hodnota je 0 |Dynamická| Maximální počet paralelních souborů, které sekundární může kopírovat z primární. '0' == počet jader. |
|Vlákna MaxFileOperationThreads | Uint, výchozí hodnota je 100 |Statická| Maximální počet paralelních vláken povoleno provádět FileOperations (Kopírovat/Přesunout) v primární. '0' == počet jader. |
|MaxRequestProcessingVlákna | Uint, výchozí hodnota je 200 |Statická|Maximální počet paralelních vláken povoleno zpracování požadavků v primární. '0' == počet jader. |
|MaxSecondaryFileCopyFailureThreshold | Uint, výchozí hodnota je 25|Dynamická|Maximální počet opakovaných pokusů o kopírování souborů na sekundární před vzdání. |
|MaxStoreOperations | Uint, výchozí hodnota je 4096 |Statická|Maximální počet operací paralelní transakce úložiště povoleno na primární. '0' == počet jader. |
|PojmenováníOperaceTimeout |Čas v sekundách, výchozí hodnota je 60 |Dynamická|Určete časový rozsah v sekundách. Časový čas pro provádění operace pojmenování. |
|Primární AccountNTLMPasswordSecret | SecureString, výchozí nastavení je prázdné |Statická| Tajný klíč hesla, který se používá jako osivo pro generování stejného hesla při použití ověřování NTLM. |
|PrimárníaccountNTLMX509Umístění úložiště | řetězec, výchozí hodnota je "LocalMachine"|Statická| Umístění úložiště certifikátu X509 používaného ke generování HMAC na primárním účtuNTLMPasswordSecret při použití ověřování NTLM. |
|Primární AccountNTLMX509Název úložiště | řetězec, výchozí hodnota je "MY"|Statická| Název úložiště certifikátu X509, který se používá ke generování hmac na PrimárníAccountNTLMPasswordSecret při použití ověřování NTLM. |
|Primární accountNTLMX509Otisk palce | řetězec, výchozí hodnota je ""|Statická|Kryptografický otisk certifikátu X509, který se používá ke generování hmac na PrimárníaccountNTLMPasswordSecret při použití ověřování NTLM. |
|Typ primárního účtu | řetězec, výchozí hodnota je "" |Statická|Primární AccountType hlavního povinného acl FileStoreService sdílené složky. |
|Primární název uživatele account | řetězec, výchozí hodnota je "" |Statická|Primární účet Uživatelské jméno objektu zabezpečení acl filestoreservice sdílené složky. |
|PrimárníaccountUserPassword | SecureString, výchozí nastavení je prázdné |Statická|Heslo primárního účtu objektu zabezpečení acl sdílené složky FileStoreService. |
|QueryOperationTimeout | Čas v sekundách, výchozí hodnota je 60 |Dynamická|Určete časový rozsah v sekundách. Časový limit pro provedení operace dotazu. |
|Sekundární accountNTLMPasswordSecret | SecureString, výchozí nastavení je prázdné |Statická| Tajný klíč hesla, který se používá jako osivo pro generování stejného hesla při použití ověřování NTLM. |
|Sekundární accountNTLMX509Umístění úložiště | řetězec, výchozí hodnota je "LocalMachine" |Statická|Umístění úložiště certifikátu X509 používaného ke generování HMAC na secondaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|Sekundární accountNTLMX509Název úložiště | řetězec, výchozí hodnota je "MY" |Statická|Název úložiště certifikátu X509, který se používá ke generování hmac na SecondaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|Sekundární accountNTLMX509Otisk palce | řetězec, výchozí hodnota je ""| Statická|Kryptografický otisk certifikátu X509, který se používá ke generování hmac na SecondaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|Sekundární typ účtu | řetězec, výchozí hodnota je ""|Statická| Sekundární AccountType hlavního povinného acl FileStoreService sdílené složky. |
|Sekundární účetUserName | řetězec, výchozí hodnota je ""| Statická|Sekundární účet Uživatelské jméno objektu zabezpečení acl FileStoreService sdílí. |
|Sekundární accountuserpassword | SecureString, výchozí nastavení je prázdné |Statická|Sekundární heslo účtu hlavního povinného acl FileStoreService sdílené složky. |
|SekundárníFileCopyRetryDelayMilliseconds|uint, výchozí hodnota je 500|Dynamická|Zpoždění opakování kopírování souboru (v milisekundách).|
|useChunkContentInTransportMessage|bool, výchozí hodnota je TRUE|Dynamická|Příznak pro použití nové verze protokolu upload zavedené v verzi 6.4. Tato verze protokolu používá přenos prostředků infrastruktury služeb k nahrávání souborů do úložiště obrázků, které poskytuje lepší výkon než protokol SMB používaný v předchozích verzích. |

## <a name="healthmanager"></a>HealthManager

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Bool, výchozí hodnota je false |Statická|Zásady hodnocení stavu clusteru: povolit pro hodnocení stavu typu aplikace. |
|MaxSuggestedNumberOfEntityHealthReports|Int, výchozí hodnota je 100 |Dynamická|Maximální počet zpráv o stavu, které může mít entita před vyvolání obavy o logiku vykazování stavu sledovacího zařízení. Každá entita stavu má mít relativně malý počet zdravotních zpráv. Pokud počet sestavy přejde nad toto číslo; mohou existovat problémy s implementací hlídacího psa. Entita s příliš velkým početem sestav je při vyhodnocení entity označena prostřednictvím sestavy stavu upozornění. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Zvážit WarningAsChyba |Bool, výchozí hodnota je false |Statická|Zásady vyhodnocení stavu clusteru: upozornění jsou považovány za chyby. |
|MaxPercentUnhealthyApplications | Int, výchozí hodnota je 0 |Statická|Zásady vyhodnocení stavu clusteru: maximální procento aplikací není v pořádku povoleno pro clusteru být v pořádku. |
|MaxPercentUnhealthyNodes | Int, výchozí hodnota je 0 |Statická|Zásady vyhodnocení stavu clusteru: maximální procento uzlů není v pořádku povoleno pro clusteru být v pořádku. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, výchozí hodnota je 10|Statická|Zásady vyhodnocení stavu upgradu clusteru: maximální procento uzlů rozdílových není v pořádku povoleno pro cluster uzdravit |
|MaxPercentUpgradeDomainDeltaUnunhealthyNodes|int, výchozí hodnota je 15|Statická|Zásady vyhodnocení stavu upgradu clusteru: maximální procento rozdílu uzlů není v pořádku v upgradovací doméně povolené pro v pořádku clusteru |

## <a name="hosting"></a>Hostování

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Počet selhání aktivace Max |Celé číslo, výchozí hodnota je 10 |Dynamická|Počet neúspěšných pokusů o obnovení systému před vzdáním se aktivace |
|AktivaceMaxRetryInterval |Čas v sekundách, výchozí hodnota je 300 |Dynamická|Při každé selhání nepřetržité aktivace systém znovu pokusí aktivaci až do ActivationMaxFailureCount. ActivationMaxRetryInterval určuje interval čekání před opakováním po každém selhání aktivace |
|AktivaceInterval zpětného vypnutí |Čas v sekundách, výchozí hodnota je 5 |Dynamická|Interval backoff při každé chybě aktivace; Při každé selhání nepřetržité aktivace systém znovu zpokouší aktivaci až do maxactivationFailureCount. Interval opakování při každém pokusu je produktem selhání nepřetržité aktivace a intervalu zpětného vypnutí aktivace. |
|AktivaceČasový čas| TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(180)|Dynamická| Určete časový rozsah v sekundách. Časový čas pro aktivaci aplikace; deaktivace a upgrade. |
|AplikaceHostCloseTimeout| TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(120)|Dynamická| Určete časový rozsah v sekundách. Když fabric exit je detekován v samostatně aktivované procesy; FabricRuntime zavře všechny repliky v procesu hostitele uživatele (hostitele aplikace). Toto je časový čas pro operaci zavření. |
| CnsNetworkPluginCnmUrlPort | wstring, výchozí hodnota je L"48080" | Statická | Port url rozhraní API Azure |
| CnsNetworkPluginCnsUrlPort | wstring, výchozí hodnota je L"10090" | Statická | Port url Azure cns |
|ContainerServiceArguments|řetězec, výchozí hodnota je "-H localhost:2375 -H npipe://"|Statická|Service Fabric (SF) spravuje docker daemon (s výjimkou na počítačích s Windows klienta, jako je Win10). Tato konfigurace umožňuje uživateli zadat vlastní argumenty, které by měly být předány daemon dockeru při jeho spuštění. Pokud jsou zadány vlastní argumenty, Service Fabric nepředají žádný jiný argument modulu Docker s výjimkou '--pidfile' argument. Uživatelé by proto neměli zadávat argument '--pidfile' jako součást svých argumentů zákazníka. Vlastní argumenty by také měly zajistit, aby daemon dockeru naslouchá výchozímu kanálu názvů v systému Windows (nebo unixovém soketu domény v systému Linux), aby s ním service fabric mohl komunikovat.|
|ContainerServiceLogFileMaxSizeInKb|int, výchozí hodnota je 32768|Statická|Maximální velikost souboru protokolu generovaného kontejnery dockeru.  Pouze pro Windows.|
|ContainerImageDownloadTimeout|int, počet sekund, výchozí hodnota je 1200 (20 min)|Dynamická|Počet sekund před stažením obrázku časový min.|
|ContainerImagesToskip|řetězec, názvy obrázků oddělené svislým znakem čáry, výchozí hodnota je ""|Statická|Název jednoho nebo více iobrazů kontejneru, které by neměly být odstraněny.  Používá se s parametrem PruneContainerImages.|
|ContainerServiceLogFileNamePrefix|řetězec, výchozí je "sfcontainerlogs"|Statická|Předpona názvu souboru pro soubory protokolu generované kontejnery dockeru.  Pouze pro Windows.|
|ContainerServiceLogFileRetentionCount|int, výchozí hodnota je 10|Statická|Počet souborů protokolu generovaných kontejnery dockeru před přepsáním souborů protokolu.  Pouze pro Windows.|
|Vytvořit časový čas běhu vytvořit|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(120)|Dynamická| Určete časový rozsah v sekundách. Hodnota časového času pro volání sync FabricCreateRuntime |
|DefaultContainerRepositoryAccountName|řetězec, výchozí hodnota je ""|Statická|Výchozí pověření použitá místo pověření zadaná v applicationManifest.xml |
|DefaultContainerRepositoryPassword|řetězec, výchozí hodnota je ""|Statická|Výchozí pověření hesla použitá místo pověření zadaná v applicationManifest.xml|
|DefaultContainerRepositoryPasswordType|řetězec, výchozí hodnota je ""|Statická|Pokud není prázdný řetězec, hodnota může být "Šifrované" nebo "SecretsStoreRef".|
|DefaultDnsSearchSuffixEmpty|bool, výchozí hodnota je NEPRAVDA|Statická|Ve výchozím nastavení je název služby připojen k názvu SF DNS pro kontejnerové služby. Tato funkce zastaví toto chování tak, aby nic není připojen k názvu SF DNS ve výchozím nastavení v cestě rozlišení.|
|Počet selhání deploymentmax|int, výchozí hodnota je 20| Dynamická|Nasazení aplikace bude opakováno pro DeploymentMaxFailureCount časy před selháním nasazení této aplikace v uzlu.| 
|NasazeníMaxRetryInterval| TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(3600)|Dynamická| Určete časový rozsah v sekundách. Maximální interval opakování pro nasazení. Při každé nepřetržité poruše se interval opakování vypočítá jako Min( DeploymentMaxRetryInterval; Počet trvalých selhání * DeploymentRetryBackoffInterval) |
|NasazeníRetryBackoffInterval| TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(10)|Dynamická|Určete časový rozsah v sekundách. Interval back-off pro selhání nasazení. Při každém selhání průběžného nasazení systém u soudí nasazení až do maximálního počtu selhání nasazení. Interval opakování je produktem selhání průběžného nasazení a intervalu backoff nasazení. |
|Zakázat kontejnery|bool, výchozí hodnota je NEPRAVDA|Statická|Config pro zakázání kontejnerů - používá se místo DisableContainerServiceStartOnContainerActivatorOpen, která je zkrácená konfigurace |
|ZakázatPožadavekNatolitry|bool, výchozí hodnota je NEPRAVDA |Dynamická| Ve výchozím nastavení SF komunikuje s DD (docker dameon) s časovým limitem 'DockerRequestTimeout' pro každý požadavek http odeslané na něj. Pokud DD nereaguje v tomto časovém období; SF znovu odešle požadavek, pokud operace nejvyšší úrovně stále zbývá čas.  S hyperv kontejnerem; DD někdy trvat mnohem více času, aby se kontejner nebo deaktivovat. V takových případech DD požadavek časový čas z hlediska SF a SF opakování operace. Někdy se zdá, že to přidává větší tlak na DD. Tato konfigurace umožňuje zakázat tento pokus a čekat na odpověď DD. |
|DnsServerListTwoIps | Bool, výchozí hodnota je FALSE | Statická | Tento příznaky přidá místní dns server dvakrát pomoci zmírnit občasné řešení problémů. |
| DoNotInjectLocalDnsServer | bool, výchozí hodnota je NEPRAVDA | Statická | Zabraňuje runtime vstřikovat místní IP jako DNS server pro kontejnery. |
|EnableActivateNoWindow| bool, výchozí hodnota je NEPRAVDA|Dynamická| Aktivovaný proces je vytvořen na pozadí bez jakékoli konzoly. |
|EnableContainerServiceDebugMode|bool, výchozí hodnota je TRUE|Statická|Povolit nebo zakázat protokolování pro kontejnery dockeru.  Pouze pro Windows.|
|EnableDockerHealthCheckIntegration|bool, výchozí hodnota je TRUE|Statická|Umožňuje integraci událostí dockeru HEALTHCHECK se sestavou stavu systému Service Fabric |
|EnableProcessDebugging|bool, výchozí hodnota je NEPRAVDA|Dynamická| Umožňuje spouštění hostitelů aplikací v ladicím programu. |
|EndpointProviderEnabled| bool, výchozí hodnota je NEPRAVDA|Statická| Umožňuje správu prostředků koncového bodu podle prostředků Fabric. Vyžaduje specifikaci rozsahu počátečního a koncového portu aplikace v aplikaci FabricNode. |
|FabricContainerAppsPovoleno| bool, výchozí hodnota je NEPRAVDA|Statická| |
|Technologie FirewallPolicyEnabled|bool, výchozí hodnota je NEPRAVDA|Statická| Povolí otevírání portů brány firewall pro prostředky koncového bodu s explicitními porty určenými v servicemanifestu. |
|GetCodePackageActivationContextTimeout|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(120)|Dynamická|Určete časový rozsah v sekundách. Hodnota časového času pro volání CodePackageActivationContext. To se nevztahuje na služby ad hoc. |
|GovernOnlyMainMemoryForProcesses|bool, výchozí hodnota je NEPRAVDA|Statická|Výchozí chování zásad správného řízení prostředků je umístit limit zadaný v MemoryInMB na množství celkové paměti (RAM + swap), který používá proces. Je-li limit překročen; proces obdrží výjimku OutOfMemory. Pokud je tento parametr nastaven na hodnotu true; limit bude použit pouze na velikost paměti RAM, kterou bude proces používat. Pokud je tento limit překročen; a pokud je toto nastavení pravdivé; pak Operační systém zamění hlavní paměť na disk. |
|Povolení ipprovideru|bool, výchozí hodnota je NEPRAVDA|Statická|Umožňuje správu IP adres. |
|IsDefaultContainerRepositoryPasswordEncrypted|bool, výchozí hodnota je NEPRAVDA|Statická|Zda defaultContainerRepositoryPassword je šifrována nebo ne.|
|LinuxExternalExecutablePath|řetězec, výchozí hodnota je "/usr/bin/" |Statická|Primární adresář externích spustitelných příkazů v uzlu.|
|Ověřování NTLM povoleno|bool, výchozí hodnota je NEPRAVDA|Statická| Umožňuje podporu pro použití NTLM balíčky kódu, které jsou spuštěny jako ostatní uživatelé tak, aby procesy napříč počítači můžete bezpečně komunikovat. |
|NTLMAuthenticationPasswordSecret|SecureString, default is Common::SecureString("")|Statická|Je šifrovaný má, který se používá ke generování hesla pro uživatele NTLM. Musí být nastavena, pokud je ntlmauthenticationEnabled true. Ověřeno nasazovačem. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, výchozí hodnota je Common::TimeSpan::FromMinutes(3)|Dynamická|Určete časový rozsah v sekundách. Nastavení specifická pro prostředí Periodický interval, ve kterém hostování prohledá nové certifikáty, které mají být použity pro konfiguraci NTLM služby FileStoreService. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, výchozí hodnota je Common::TimeSpan::FromMinutes(4)|Dynamická| Určete časový rozsah v sekundách. Časový čas pro konfiguraci uživatelů NTLM pomocí běžných názvů certifikátů. Uživatelé NTLM jsou potřebné pro sdílené složky FileStoreService. |
|Obrázky prunecontainer|bool, výchozí hodnota je NEPRAVDA|Dynamická| Odeberte nepoužívané image kontejneru aplikace z uzlů. Pokud ApplicationType je unregistered z clusteru Service Fabric, image kontejneru, které byly použity v této aplikaci budou odebrány na uzly, kde byl stažen Service Fabric. Prořezávání se spustí každou hodinu, takže může trvat až jednu hodinu (plus čas prořezávat obrázek) pro obrazy, které mají být odebrány z clusteru.<br>Service Fabric nikdy nestáhne ani neodebere obrázky, které nesouvisejí s aplikací.  Nesouvisející obrázky, které byly staženy ručně nebo jinak, musí být explicitně odebrány.<br>Obrázky, které by neměly být odstraněny lze zadat v ContainerImagesToSkip parametr.| 
|RegisterCodePackageHostTimeout|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(120)|Dynamická| Určete časový rozsah v sekundách. Hodnota časového času pro volání synchronizace FabricRegisterCodePackageHost. To platí pouze pro hostitele vícekódových balíčků, jako je FWP |
|RequestTimeout|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(30)|Dynamická| Určete časový rozsah v sekundách. To představuje časový čas pro komunikaci mezi hostitelem aplikace uživatele a fabric proces pro různé operace související s hostováním, jako je registrace z výroby; registrace za běhu. |
|Zásady runas povoleny| bool, výchozí hodnota je NEPRAVDA|Statická| Umožňuje spouštění balíčků kódu jako místního uživatele než uživatele, pod kterým je spuštěn proces infrastruktury. Chcete-li povolit tuto zásadu Fabric musí být spuštěn jako SYSTEM nebo jako uživatel, který má SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(120)|Dynamická|Určete časový rozsah v sekundách. Hodnota časového času pro volání registru synchronizace(Bezstavové/Stavové)ServiceFactory |
|ServiceTypeDisableFailureThreshold |Celé číslo, výchozí hodnota je 1 |Dynamická|Toto je prahová hodnota pro počet selhání, po kterém FailoverManager (FM) je upozorněn zakázat typ služby v tomto uzlu a zkuste jiný uzel pro umístění. |
|ServiceTypeDisableGraceInterval|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(30)|Dynamická|Určete časový rozsah v sekundách. Časový interval, po kterém lze typ služby zakázat |
|ServiceTypeRegistrationTimeout |Čas v sekundách, výchozí hodnota je 300 |Dynamická|Maximální povolená doba pro ServiceType, které mají být registrovány s fabric |
|UseContainerServiceArguments|bool, výchozí hodnota je TRUE|Statická|Tento config říká hosting přeskočit předávání argumentů (zadaný v konfiguraci ContainerServiceArguments) docker daemon.|

## <a name="httpgateway"></a>HttpGateway

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Aktivní posluchači |Uint, výchozí hodnota je 50 |Statická| Počet čtení, která mají být odeslána do fronty serveru HTTP. To řídí počet souběžných požadavků, které mohou být splněny httpgateway. |
|HttpGatewayHealthReportSendInterval |Čas v sekundách, výchozí hodnota je 30 |Statická|Určete časový rozsah v sekundách. Interval, ve kterém brána Http gateway odesílá akumulované sestavy stavu správci stavu. |
|HttpstrictTransportSecurityHeader|řetězec,výchozí hodnota je ""|Dynamická| Zadejte hodnotu hlavičky HTTP Strict Transport Security, která má být zahrnuta do každé odpovědi odeslané službou HttpGateway. Při nastavení na prázdný řetězec; tato hlavička nebude zahrnuta do odpovědi brány.|
|IsEnabled|Bool, výchozí hodnota je false |Statická| Povolí/zakáže bránu HttpGateway. HttpGateway je ve výchozím nastavení zakázána. |
|Velikost MaxEntityBodySize |Uint, výchozí hodnota je 4194304 |Dynamická|Udává maximální velikost těla, které lze očekávat od požadavku http. Výchozí hodnota je 4 MB. Httpgateway se nezdaří požadavek, pokud má tělo velikosti > tuto hodnotu. Minimální velikost bloku čtení je 4096 bajtů. Takže to musí být >= 4096. |

## <a name="imagestoreservice"></a>Služba ImageStoreService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Povoleno |Bool, výchozí hodnota je false |Statická|Příznak Enabled pro službu ImageStoreService. Výchozí: false |
|MinReplicaSetSize | Int, výchozí hodnota je 3 |Statická|MinReplicaSetSize pro ImageStoreService. |
|Omezení umístění | řetězec, výchozí hodnota je "" |Statická| PlacementConstraints pro ImageStoreService. |
|QuorumLossWaitDuration | Čas v sekundách, výchozí hodnota je MaxValue |Statická| Určete časový rozsah v sekundách. QuorumLossWaitDuration pro ImageStoreService. |
|ReplicaRestartWaitDuration | Čas v sekundách, výchozí \* hodnota je 60,0 30 |Statická|Určete časový rozsah v sekundách. ReplikaRestartWaitDuration pro ImageStoreService. |
|Doba trvání pohotovostního režimu | Čas v sekundách, výchozí hodnota \* je 3600,0 2 |Statická| Určete časový rozsah v sekundách. StandByReplicaKeepDuration pro ImageStoreService. |
|Cílová replikaSetSize | Int, výchozí hodnota je 7 |Statická|TargetReplicaSetSize pro ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Automatická konfigurace paměti |Int, výchozí hodnota je 1 |Dynamická|Příznak, který označuje, zda nastavení paměti by měla být automaticky a dynamicky nakonfigurována. Pokud nula pak nastavení konfigurace paměti jsou použity přímo a nemění v závislosti na stavu systému. Pokud jeden pak nastavení paměti jsou konfigurovány automaticky a může změnit v závislosti na stavu systému. |
|MaximumDestagingWriteOutstandingInKB | Int, výchozí hodnota je 0 |Dynamická|Počet KB povolit sdílené protokolu předem před vyhrazené protokolu. Použijte 0 k označení žádného omezení.
|SharedLogId |řetězec, výchozí hodnota je "" |Statická|Jedinečný identifikátor GUID pro sdílený kontejner protokolu. Použijte "" pokud používáte výchozí cestu pod kořenem dat prostředků infrastruktury. |
|SharedLogPath |řetězec, výchozí hodnota je "" |Statická|Cesta a název souboru do umístění umístit sdílený kontejner protokolu. Použijte "" pro použití výchozí cesty pod kořenem dat prostředků infrastruktury. |
|SharedLogSizeInMB |Int, výchozí hodnota je 8192 |Statická|Počet MB přidělit v kontejneru sdíleného protokolu. |
|SharedLogThrottleLimitInPercentUsed|int, výchozí hodnota je 0 | Statická | Procento použití sdíleného protokolu, které vyvolá omezení. Hodnota by měla být mezi 0 a 100. Hodnota 0 znamená použití výchozí procentuální hodnoty. Hodnota 100 znamená žádné omezení vůbec. Hodnota mezi 1 a 99 určuje procento využití protokolu, nad kterou dojde k omezení; Například pokud sdílený protokol je 10 GB a hodnota je 90 pak omezení dojde, jakmile 9GB je používán. Doporučujeme použít výchozí hodnotu.|
|WriteBufferMemoryPoolMaximumInKB | Int, výchozí hodnota je 0 |Dynamická|Počet KB povolit fondu paměti vyrovnávací paměti zápisu vyrůstat. Použijte 0 k označení žádného omezení. |
|ZápisBufferMemoryPoolminimumInKB |Int, výchozí hodnota je 8388608 |Dynamická|Počet KB zpočátku přidělit pro fond vyrovnávací paměti zápisu. Použijte 0 k označení žádné omezení Výchozí by měla být konzistentní s SharedLogSizeInMB níže. |

## <a name="managedidentitytokenservice"></a>Služba ManagedIdentityTokenService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|IsEnabled|bool, výchozí hodnota je NEPRAVDA|Statická|Příznak řízení stavu a stavu služby Token spravované identity v clusteru;to je předpokladem pro použití funkce spravované identity aplikací Service Fabric.|

## <a name="management"></a>Správa

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|AutomatickéUnprovisionInterval|TimeSpan, výchozí hodnota je Common::TimeSpan::FromMinutes(5)|Dynamická|Určete časový rozsah v sekundách. Interval vyčištění pro povolení pro zrušení registrace typu aplikace během automatického čištění typu aplikace.|
|AzureStorageMaxConnections | Int, výchozí hodnota je 5000 |Dynamická|Maximální počet souběžných připojení k úložišti Azure. |
|AzureStorageMaxWorkerVlákna | Int, výchozí hodnota je 25 |Dynamická|Maximální počet pracovních podprocesů paralelně. |
|AzureStorageOperationTimeout | Čas v sekundách, výchozí hodnota je 6000 |Dynamická|Určete časový rozsah v sekundách. Časový čas pro dokončení operace xstore. |
|CleanupApplicationPackageOnProvisionSuccess|bool, výchozí hodnota je NEPRAVDA |Dynamická|Povolí nebo zakáže automatické vyčištění balíčku aplikace při úspěšném zřizování. |
|Vyčištění Nepoužívanétypy aplikací|Bool, výchozí hodnota je FALSE |Dynamická|Tato konfigurace, pokud je povolena, umožňuje automaticky zrušit registraci nepoužívaných verzí typu aplikace, které přeskočí nejnovější tři nepoužívané verze, čímž se ořízne místo na disku obsazené úložištěm bitových kopií. Automatické vyčištění se spustí na konci úspěšného zřízení pro tento konkrétní typ aplikace a také běží pravidelně jednou denně pro všechny typy aplikací. Počet nepoužívaných verzí přeskočit je konfigurovatelný pomocí parametru "MaxUnusedAppTypeVersionsToKeep". |
|Zakázat ověřování kontrolního součtu | Bool, výchozí hodnota je false |Statická| Tato konfigurace nám umožňuje povolit nebo zakázat ověření kontrolního součtu během zřizování aplikací. |
|ZakázatserverSideCopy | Bool, výchozí hodnota je false |Statická|Tato konfigurace umožňuje nebo zakáže kopii balíčku aplikace na straně serveru na ImageStore během zřizování aplikací. |
|ImageCachingEnabled | Bool, výchozí hodnota je pravda |Statická|Tato konfigurace nám umožňuje povolit nebo zakázat ukládání do mezipaměti. |
|Řetězec připojení úložiště bitové kopie |Securestring |Statická|Připojovací řetězec ke kořenovému adresáři pro úložiště bitové kopie. |
|ImageStoreMinimumTransferBPS | Int, výchozí hodnota je 1024 |Dynamická|Minimální přenosová rychlost mezi clusterem a ImageStore. Tato hodnota se používá k určení časového času při přístupu k externí ImageStore. Změňte tuto hodnotu pouze v případě, že latence mezi clusterem a ImageStore je vysoká, aby více času pro cluster ke stažení z externího ImageStore. |
|MaxUnusedAppTypeVersionsToKeep | Int, výchozí hodnota je 3 |Dynamická|Tato konfigurace definuje počet nepoužívaných verzí typu aplikace, které mají být přeskočeny pro vyčištění. Tento parametr je použitelný pouze v případě, že je povolen parametr CleanupUnusedApplicationTypes. |


## <a name="metricactivitythresholds"></a>Metrické hodnoty aktivity
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|KeyIntegerValueMap, výchozí hodnota je Žádný|Dynamická|Určuje sadu MetricActivityThresholds pro metriky v clusteru. Vyrovnávání bude fungovat, pokud je maxNodeLoad větší než MetricActivityThresholds. Pro defrag metriky definuje množství zatížení, které se rovná nebo pod kterou Service Fabric bude považovat uzel prázdný |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|KeyDoubleValueMap, výchozí hodnota je Žádný|Dynamická|Určuje sadu MetricBalancingThresholds pro metriky v clusteru. Vyrovnávání bude fungovat, pokud je hodnota maxNodeLoad/minNodeLoad větší než hodnota MetricBalancingThresholds. Defragmentace bude fungovat, pokud maxNodeLoad/minNodeLoad alespoň v jednom FD nebo UD je menší než MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|KeyDoubleValueMap, výchozí hodnota je Žádný|Dynamická|Určuje část zatížení, která se při výměně drží s replikou Trvá hodnotu mezi 0 (zatížení se nelepí s replikou) a 1 (načtení hole s replikou - výchozí) |

## <a name="namingservice"></a>Pojmenování

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, výchozí hodnota je 0 |Statická|Maximální počet položek uchovávaných v mezipaměti popisu služby LRU v pojmenovávací bráně (nastavený na hodnotu 0 bez omezení). |
|Maximální počet připojení klientů |Int, výchozí hodnota je 1000 |Dynamická|Maximální povolený počet klientských připojení na bránu. |
|MaxFileOperationTimeout |Čas v sekundách, výchozí hodnota je 30 |Dynamická|Určete časový rozsah v sekundách. Maximální povolený časový limit pro operaci služby úložiště souborů. Požadavky určující větší časový čas budou odmítnuty. |
|MaxIndexedEmptyOddíly |Int, výchozí hodnota je 1000 |Dynamická|Maximální počet prázdných oddílů, které zůstanou indexovány v mezipaměti oznámení pro synchronizaci opětovného připojení klientů. Všechny prázdné oddíly nad tímto číslem budou odebrány z indexu ve vzestupném pořadí vyhledávací verze. Znovupřipojující klienti mohou stále synchronizovat a přijímat zmeškané aktualizace prázdných oddílů. ale synchronizační protokol se stává dražší. |
|Maximální velikost zprávy |Int, výchozí\*hodnota je\*4 1024 1024 |Statická|Maximální velikost zprávy pro komunikaci klientského uzlu při použití pojmenování. DOS útok zmírnění; výchozí hodnota je 4 MB. |
|MaxNamingServiceHealthReports | Int, výchozí hodnota je 10 |Dynamická|Maximální počet pomalých operací, které služba Naming Store hlásí není v pořádku najednou. Pokud 0; všechny pomalé operace jsou odeslány. |
|Maximální doba provozu |Čas v sekundách, výchozí hodnota je 600 |Dynamická|Určete časový rozsah v sekundách. Maximální povolený časový limit pro operace klienta. Požadavky určující větší časový čas budou odmítnuty. |
|MaxOutstandingNotificationsPerClient |Int, výchozí hodnota je 1000 |Dynamická|Maximální počet nevyřízených oznámení před registrací klienta je násilně uzavřen bránou. |
|MinReplicaSetSize | Int, výchozí hodnota je 3 |Není povoleno| Minimální počet replik služby pojmenování, do kterých je třeba zapisovat, aby bylo možné dokončit aktualizaci. Pokud je v systému méně replik, než je aktivní, systém spolehlivosti odepře aktualizace úložiště služeb pojmenování, dokud nebudou repliky obnoveny. Tato hodnota by nikdy neměla být větší než TargetReplicaSetSize. |
|PartitionCount |Int, výchozí hodnota je 3 |Není povoleno|Počet oddílů úložiště služby pojmenování, které mají být vytvořeny. Každý oddíl vlastní jeden klíč oddílu, který odpovídá jeho indexu; takže klíče oddílů [0; PartitionCount] existují. Zvýšení počtu oddílů služby pojmenování zvyšuje měřítko, ve které může služba pojmenování provádět, snížením průměrného množství dat, která jsou uložena v libovolné podpůrné sadě replik. za cenu zvýšenévyužití prostředků (protože partitionCount*ReplicaSetSize repliky služby musí být udržovány).|
|Omezení umístění | řetězec, výchozí hodnota je "" |Není povoleno| Omezení umístění pro službu pojmenování. |
|QuorumLossWaitDuration | Čas v sekundách, výchozí hodnota je MaxValue |Není povoleno| Určete časový rozsah v sekundách. Když se služba pojmenování dostane do ztráty kvora; tento časovač se spustí. Když vyprší FM bude považovat dolů repliky jako ztracené; a pokus o obnovení kvora. Ne, že by to mohlo vést ke ztrátě dat. |
|Interval opravy | Čas v sekundách, výchozí hodnota je 5 |Statická| Určete časový rozsah v sekundách. Interval, ve kterém bude zahájena oprava nekonzistence názvů mezi vlastníkem autority a vlastníkem názvu. |
|ReplicaRestartWaitDuration | Čas v sekundách, výchozí hodnota je (60.0 * 30)|Není povoleno| Určete časový rozsah v sekundách. Když replika služby pojmenování přejde dolů; tento časovač se spustí. Když vyprší FM začne nahrazovat repliky, které jsou dole (to ještě nepovažuje za ztracené). |
|ServiceDescriptionCacheLimit | Int, výchozí hodnota je 0 |Statická| Maximální počet položek uchovávaných v mezipaměti popisu služby LRU ve službě Naming Store (nastavený na hodnotu 0 pro žádné omezení). |
|ServiceNotificationTimeout |Čas v sekundách, výchozí hodnota je 30 |Dynamická|Určete časový rozsah v sekundách. Časový čas použitý při doručování oznámení služby klientovi. |
|Doba trvání pohotovostního režimu | Čas v sekundách, výchozí hodnota je 3600,0 * 2 |Není povoleno| Určete časový rozsah v sekundách. Když se replika služby pojmenování vrátí ze stavu dolů; je možné, že již byla nahrazena. Tento časovač určuje, jak dlouho fm bude udržovat pohotovostní repliku před jeho vyhozením. |
|Cílová replikaSetSize |Int, výchozí hodnota je 7 |Není povoleno|Počet sad replik pro každý oddíl úložiště služby pojmenování. Zvýšení počtu sad replik zvyšuje úroveň spolehlivosti informací v úložišti pojmenování služby; snížení změny, že informace budou ztraceny v důsledku selhání uzlu; náklady na zvýšené zatížení windows fabric a množství času potřebný k provedení aktualizace dat pojmenování.|

## <a name="nodebufferpercentage"></a>Procento nodeBuffer
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|KeyDoubleValueMap, výchozí hodnota je Žádný|Dynamická|Procento kapacity uzlu na název metriky; slouží jako vyrovnávací paměť, aby bylo možné zachovat některé volné místo na uzlu pro případ převzetí služeb při selhání. |

## <a name="nodecapacities"></a>NodeKapacity

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup |NodeCapacityCollectionMap |Statická|Kolekce kapacit uzlů pro různé metriky. |

## <a name="nodedomainids"></a>Id nodeDomainIds

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup |Kolekce NodeFaultDomainIdCollection |Statická|Popisuje domény selhání, do kterých uzel patří. Doména selhání je definována pomocí identifikátoru URI, který popisuje umístění uzlu v datovém centru.  Identifikátory URI domény selhání jsou formátu fd:/fd/ následované segmentem cesty URI.|
|UpgradeDomainId |řetězec, výchozí hodnota je "" |Statická|Popisuje upgradovací doménu, do které uzel patří. |

## <a name="nodeproperties"></a>Vlastnosti uzlu

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup |Soubor NodePropertyCollectionMap |Statická|Kolekce párů klíč hodnota řetězce pro vlastnosti uzlu. |

## <a name="paas"></a>Paas (Paas)

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|ClusterId |řetězec, výchozí hodnota je "" |Není povoleno|Úložiště certifikátů X509 používaných látkou pro ochranu konfigurace. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Counters |Řetězec | Dynamická |Seznam čítačů výkonu oddělených čárkami, které mají být shromažďovány. |
|IsEnabled |Bool, výchozí hodnota je pravda | Dynamická |Příznak označuje, zda je povolena kolekce čítače výkonu v místním uzlu. |
|MaxCounterBinaryFileSizeInMB |Int, výchozí hodnota je 1 | Dynamická |Maximální velikost (v MB) pro každý binární soubor čítače výkonu. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, výchozí hodnota je 10 | Dynamická |Maximální interval (v sekundách), po kterém je vytvořen nový binární soubor čítače výkonu. |
|SamplingIntervalInSeconds |Int, výchozí hodnota je 60 | Dynamická |Interval vzorkování pro sběrčích výkonu. |

## <a name="placementandloadbalancing"></a>UmístěníALoadBalancing

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, výchozí hodnota je 0 | Dynamická|Určuje prioritu omezení spřažení: 0: Pevný; 1: měkké; negativní: Ignorovat. |
|ApplicationCapacityConstraintPriority | Int, výchozí hodnota je 0 | Dynamická|Určuje prioritu omezení kapacity: 0: Hard; 1: měkké; negativní: Ignorovat. |
|AutoDetectAvailableResources|bool, výchozí hodnota je TRUE|Statická|Tato konfigurace spustí automatickou detekci dostupných prostředků na uzlu (CPU a Memory) Když je tato konfigurace nastavena na hodnotu true - přečteme skutečné kapacity a opravíme je, pokud uživatel zadal chybné nodové kapacity nebo je vůbec nedefinoval Pokud je tato konfigurace nastavena na false - budeme sledovat upozornění, že uživatel zadal chybné nodové kapacity; ale nebudeme je opravovat; což znamená, že uživatel chce mít kapacity určené jako > než uzel skutečně má, nebo pokud kapacity jsou nedefinované; bude předpokládat neomezenou kapacitu |
|BalancingDelayAfterNewNode | Čas v sekundách, výchozí hodnota je 120 |Dynamická|Určete časový rozsah v sekundách. Nezačínejte vyvažování aktivit v tomto období po přidání nového uzlu. |
|BalancingDelayAfterNodeDown | Čas v sekundách, výchozí hodnota je 120 |Dynamická|Určete časový rozsah v sekundách. Nezačínejte vyvažovat aktivity v tomto období po události dolů uzlu. |
|CapacityConstraintPriority | Int, výchozí hodnota je 0 | Dynamická|Určuje prioritu omezení kapacity: 0: Hard; 1: měkké; negativní: Ignorovat. |
|Po sobě jdoucíDroppedMovementsHealthReportLimit | Int, výchozí hodnota je 20 | Dynamická|Definuje počet po sobě jdoucích časů, které ResourceBalancer vydané pohyby jsou vynechány před diagnostiky jsou prováděny a jsou vydávána zdravotní upozornění. Negativní: Za této podmínky nejsou vyzařována žádná upozornění. |
|ConstraintFixIn-ZpoceníAfterNewNode | Čas v sekundách, výchozí hodnota je 120 |Dynamická| Určete časový rozsah v sekundách. DDo not Fix FaultDomain and UpgradeDomain omezení porušení v tomto období po přidání nového uzlu. |
|ConstraintFixIn -Zdyskaní po uplynutí nosné | Čas v sekundách, výchozí hodnota je 120 |Dynamická| Určete časový rozsah v sekundách. Neopravujte porušení omezení domény faultdomain a upgradedomény během této doby po události dolů uzlu. |
|Limit ConstraintViolationHealthReportLimit | Int, výchozí hodnota je 50 |Dynamická| Definuje, kolikrát omezení porušení repliky musí být trvale unfixed před diagnostiky jsou prováděny a zprávy o stavu jsou vydávány. |
|Limit DetailedConstraintConstraintHealthReportLimit | Int, výchozí hodnota je 200 |Dynamická| Definuje, kolikrát omezení porušení repliky musí být trvale unfixed před diagnostiky jsou prováděny a podrobné zprávy o stavu jsou vydávány. |
|Podrobné diagnostické údajeInfoListLimit | Int, výchozí hodnota je 15 |Dynamická| Definuje počet diagnostických položek (s podrobnými informacemi) na omezení, které mají být zahrnuty před zkrácením v diagnostice.|
|PodrobnéNodeListLimit | Int, výchozí hodnota je 15 |Dynamická| Definuje počet uzlů na omezení, které mají být zahrnuty před zkrácením v sestavách Neumístěné repliky. |
|Podrobný limit partitionlist | Int, výchozí hodnota je 15 |Dynamická| Definuje počet oddílů na diagnostickou položku pro omezení, které má být zahrnuto před zkrácením v diagnostice. |
|PodrobnéVerboseHealthReportLimit | Int, výchozí hodnota je 200 | Dynamická|Definuje, kolikrát neumístěná replika musí být trvale neumístěna před vyzařováním podrobných zpráv o stavu. |
|EnforceUserServiceMetricKapacity|bool, výchozí hodnota je NEPRAVDA | Statická |Umožňuje ochranu služeb tkaniny. Všechny uživatelské služby jsou pod jedním objektem/skupinou úloh a jsou omezeny na zadané množství prostředků. To musí být statické (vyžaduje restartování FabricHost) jako vytvoření/ odebrání objektu úlohy uživatele a nastavení limitů v provádí při otevření Fabric Host. |
|Priorita omezení domény chyby | Int, výchozí hodnota je 0 |Dynamická| Určuje prioritu omezení domény selhání: 0: Pevný; 1: měkké; negativní: Ignorovat. |
|GlobalMovementThrottleCountingInterval | Čas v sekundách, výchozí hodnota je 600 |Statická| Určete časový rozsah v sekundách. Uveďte délku minulého intervalu, pro který chcete sledovat pohyby repliky domény (používá se společně s GlobalMovementThrottleThreshold). Lze nastavit na 0 ignorovat globální omezení úplně. |
|Prahová hodnota škrticí klapky GlobalMovement | Uint, výchozí hodnota je 1000 |Dynamická| Maximální počet pohybů povolených ve vyvažovací fázi v minulém intervalu označený globalmovementthrottlecountinginterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, výchozí hodnota je 0 | Dynamická|Maximální počet pohybů povolených ve vyvažovací fázi v minulém intervalu označený globalmovementthrottlecountinginterval. 0 označuje žádné omezení. |
|GlobalMovementThrottleThresholdforPlacement | Uint, výchozí hodnota je 0 |Dynamická| Maximální počet pohybů povolených ve fázi umístění v minulém intervalu označeném globalMovementThrottleCountingInterval.0 označuje žádné omezení.|
|GlobalMovementThrottleThresholdProcento|double, výchozí hodnota je 0|Dynamická|Maximální počet celkových pohybů povolených ve fázích vyvažování a umístění (vyjádřeno v procentech celkového počtu replik v clusteru) v minulém intervalu označeném GlobalMovementThrottleCountingInterval. 0 označuje žádné omezení. Pokud jsou zadány obě tyto a GlobalMovementThrottleThreshold; pak se použije konzervativnější limit.|
|GlobalMovementThrottleThresholdProcentoProvyvyvy|double, výchozí hodnota je 0|Dynamická|Maximální počet pohybů povolených ve vyvažovací fázi (vyjádřeno v procentech celkového počtu replik v PLB) v minulém intervalu označeném GlobalMovementThrottleCountingInterval. 0 označuje žádné omezení. Pokud jsou zadány jak toto a GlobalMovementThrottleThresholdForBalancing; pak se použije konzervativnější limit.|
|InBuildThrottlingPřidružená metrika | řetězec, výchozí hodnota je "" |Statická| Přidružený název metriky pro toto omezení. |
|InBuildThrottlingPovoleno | Bool, výchozí hodnota je false |Dynamická| Zjistěte, zda je povoleno omezení v sestavení. |
|Hodnota InBuildThrottlingGlobalMaxValue | Int, výchozí hodnota je 0 |Dynamická|Maximální počet replik v sestavení povolen globálně. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, výchozí hodnota je false | Dynamická|Určuje, zda by měl jakýkoli typ aktualizace jednotky s podporou převzetí služeb při selhání přerušit rychlé nebo pomalé vyvažování. Při zadaném "false" vyvažování spuštění bude přerušeno, pokud FailoverUnit: je vytvořen/odstraněn; obsahuje chybějící repliky; změněno umístění primární repliky nebo změněný počet replik. Vyvažování spuštění nebude přerušeno v jiných případech - pokud FailoverUnit: má další repliky; změněna všechna příznak repliky; změnila pouze verzi oddílu nebo jakýkoli jiný případ. |
|MinConstraintCheckInterval | Čas v sekundách, výchozí hodnota je 1 |Dynamická| Určete časový rozsah v sekundách. Definuje minimální dobu, která musí uplynout před dvěma po sobě jdoucími kontrolními zaokrouchy omezení. |
|Interval minLoadBalancingInterval | Čas v sekundách, výchozí hodnota je 5 |Dynamická| Určete časový rozsah v sekundách. Definuje minimální dobu, která musí uplynout před dvěma po sobě jdoucími vyvažovacími zaokrouchy. |
|Interval minplacementu | Čas v sekundách, výchozí hodnota je 1 |Dynamická| Určete časový rozsah v sekundách. Definuje minimální dobu, která musí uplynout před dvěma po sobě jdoucími zaokrouch umístění. |
|MoveExistingReplicaForPlacement | Bool, výchozí hodnota je pravda |Dynamická|Nastavení, které určuje, zda má být během umístění přesunutí existující repliky. |
|Interval intervalu s počítace škrtů v oddílech | Čas v sekundách, výchozí hodnota je 600 |Statická| Určete časový rozsah v sekundách. Uveďte délku minulého intervalu, pro který chcete sledovat pohyby repliky pro každý oddíl (používá se spolu s MovementPerPartitionThrottleThreshold). |
|Hodnota omezení oddílu MovementPerPartitionThrottleThreshold | Uint, výchozí hodnota je 50 |Dynamická| Žádné vyvažování související pohyb dojde pro oddíl, pokud počet vyrovnávací související pohyby pro repliky tohoto oddílu dosáhl nebo překročil MovementPerFailoverUnitThrottleThreshold v minulém intervalu označené MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, výchozí hodnota je false |Dynamická| Nastavení, které určuje, zda nadřazené repliky lze přesunout opravit omezení spřažení.|
|Služby Částečné umístění | Bool, výchozí hodnota je pravda |Dynamická| Určuje, zda všechny repliky služby v clusteru budou umístěny "všechny nebo nic" vzhledem k omezené vhodné uzly pro ně.|
|PlaceChildWithoutParent | Bool, výchozí hodnota je pravda | Dynamická|Nastavení, které určuje, zda může být umístěna replika podřízené služby, pokud není žádná nadřazená replika. |
|Priorita omezení umístění | Int, výchozí hodnota je 0 | Dynamická|Určuje prioritu omezení umístění: 0: Pevný; 1: měkké; negativní: Ignorovat. |
|Velikost mezipaměti_ověření_přístupu k_omezení | Int, výchozí hodnota je 10000 |Dynamická| Omezuje velikost tabulky použité pro rychlé ověření a ukládání výrazů omezení umístění do mezipaměti. |
|UmístěníSearchTimeout | Čas v sekundách, výchozí hodnota je 0,5 |Dynamická| Určete časový rozsah v sekundách. Při umísťování služeb; hledat maximálně tak dlouho před vrácením výsledku. |
|REFRESHBRefreshgap | Čas v sekundách, výchozí hodnota je 1 |Dynamická| Určete časový rozsah v sekundách. Definuje minimální dobu, která musí uplynout, než plb znovu aktualizuje stav. |
|Priorita omezení služby PreferredLocation | Int, výchozí hodnota je 2| Dynamická|Určuje prioritu omezení upřednostňované polohy: 0: Pevný; 1: měkké; 2: Optimalizace; negativní: Ignorovat |
|Priorita PreferredPrimaryDomainsConstraintPriority| Int, výchozí hodnota je 1 | Dynamická| Určuje prioritu upřednostňovaného omezení primární domény: 0: Pevný; 1: měkké; negativní: Ignorovat |
|Preferovatupgradované dudy|bool,výchozí hodnota je NEPRAVDA|Dynamická|Zapíná a vypíná logiku, která upřednostňuje přechod na již upgradované ud. Počínaje sF 7.0 se výchozí hodnota tohoto parametru změní z TRUE na FALSE.|
|Zabránit transientovercommit | Bool, výchozí hodnota je false | Dynamická|Určuje, zda by měl PLB okamžitě počítat s prostředky, které budou uvolněny iniciované tahy. Ve výchozím nastavení; PLB můžete zahájit přesunout ven a přesunout na stejném uzlu, který může vytvořit přechodné overcommit. Nastavení tohoto parametru na hodnotu true zabrání tomu, aby tyto druhy overcommits a defrag na vyžádání (aka placementWithMove) budou zakázány. |
|Priorita omezení horizontálního navýšení kapacity | Int, výchozí hodnota je 0 |Dynamická| Určuje prioritu omezení počtu horizontálních navýšení kapacit: 0: Pevný; 1: měkké; negativní: Ignorovat. |
|SubclusteringEnabled|Bool, výchozí hodnota je FALSE | Dynamická |Potvrzení podskupin při výpočtu směrodatné odchylky pro vyrovnávání |
|Zásady subclusteringReporting| Int, výchozí hodnota je 1 |Dynamická|Definuje, jak a zda jsou odesílány sestavy stavu podclustering: 0: Nehlásit; 1: Varování; 2: OK |
|SwapPrimaryThrottlingPřidružená metrika | řetězec, výchozí hodnota je ""|Statická| Přidružený název metriky pro toto omezení. |
|Funkce SwapPrimaryThrottlingPovoleno | Bool, výchozí hodnota je false|Dynamická| Zjistěte, zda je povoleno omezení primárního závěje. |
|Hodnota SwapPrimaryThrottlingGlobalMaxValue | Int, výchozí hodnota je 0 |Dynamická| Maximální počet swapprimární repliky povolena globálně. |
|TraceCRMReasons |Bool, výchozí hodnota je pravda |Dynamická|Určuje, zda mají být sledovány důvody přesunů vydaných protokolem CRM do kanálu provozních událostí. |
|Priorita omezení domény upgradu | Int, výchozí hodnota je 1| Dynamická|Určuje prioritu omezení domény upgradu: 0: Pevný; 1: měkké; negativní: Ignorovat. |
|UseMoveCostReports | Bool, výchozí hodnota je false | Dynamická|Instruuje LB ignorovat prvek nákladů funkce bodování; což vede k potenciálně velkému počtu pohybů pro vyváženější umístění. |
|Použít samostatný sekundární náklad | Bool, výchozí hodnota je pravda | Dynamická|Nastavení, které určuje, zda by mělo být pro sekundární repliky použito samostatné zatížení. |
|Použít separateSecondaryMoveCost | Bool, výchozí hodnota je false | Dynamická|Nastavení, které určuje, zda mají být pro sekundární repliky použity samostatné náklady na přesun. |
|ValidatePlacementConstraint | Bool, výchozí hodnota je pravda |Dynamická| Určuje, zda je výraz PlacementConstraint pro službu ověřen při aktualizaci servicedescription služby. |
|Ověřit PrimaryPlacementConstraintOnPromote| Bool, výchozí hodnota je PRAVDA |Dynamická|Určuje, zda je výraz PlacementConstraint pro službu vyhodnocen pro primární předvolbu při převzetí služeb při selhání. |
|VerboseHealthReportLimit | Int, výchozí hodnota je 20 | Dynamická|Definuje, kolikrát replika musí přejít neumístěný před upozornění stavu je hlášena pro něj (pokud podrobné sestavy stavu je povolena). |
|NodeLoadsOperationalTracingEnabled | Bool, výchozí hodnota je pravda |Dynamická|Konfigurace, která umožňuje sledování provozních strukturálních trasování Node Load v úložišti událostí. |
|NodeLoadsOperationalTracingInterval | TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(20) | Dynamická|Určete časový rozsah v sekundách. Interval, se kterým chcete sledovat zatížení uzlu do úložiště událostí pro každou doménu služby. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Čas v sekundách, výchozí hodnota je 900 |Dynamická|Určete časový rozsah v sekundách. Doba, po kterou bude systém čekat před ukončením hostitelů služby, kteří mají repliky, které se během upgradu aplikace uvíznou na blízku.|
|FabricUpgradeMaxReplicaCloseDuration | Čas v sekundách, výchozí hodnota je 900 |Dynamická| Určete časový rozsah v sekundách. Doba, po kterou bude systém čekat před ukončením hostitelů služby, kteří mají repliky, které se během upgradu prostředků infrastruktury uvíznou v blízkosti. |
|Saná délka_období|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(120)|Dynamická|Určete časový rozsah v sekundách. Doba, po kterou bude systém čekat před ukončením hostitelů služby, kteří mají repliky, které jsou těsně zablokované. Pokud je tato hodnota nastavena na hodnotu 0, repliky nebudou instruovány k uzavření.|
|Doba trvání nodedeactivationMaxReplicaCloseDuration | Čas v sekundách, výchozí hodnota je 900 |Dynamická|Určete časový rozsah v sekundách. Doba, po kterou bude systém čekat před ukončením hostitelů služby, kteří mají repliky, které jsou těsně zablokované během deaktivace uzlu. |
|PeriodaApiSlowTraceInterval | Čas v sekundách, výchozí hodnota je 5 minut |Dynamická| Určete časový rozsah v sekundách. PeriodicApiSlowTraceInterval definuje interval, přes který pomalé volání rozhraní API bude retraced monitorování rozhraní API. |
|ReplicaChangeRoleFailureRestartThreshold|int, výchozí hodnota je 10|Dynamická| Celé číslo. Zadejte počet selhání rozhraní API během primární propagace, po které bude použita akce automatického zmírnění (restartování repliky). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, výchozí hodnota je 2147483647|Dynamická| Celé číslo. Zadejte počet selhání rozhraní API během primární propagace, po které bude aktivována zpráva o stavu upozornění.|
|ServiceApiHealthDuration | Čas v sekundách, výchozí hodnota je 30 minut |Dynamická| Určete časový rozsah v sekundách. ServiceApiHealthDuration definuje, jak dlouho budeme čekat na spuštění rozhraní API služby, než ohlásíme, že není v pořádku. |
|ServiceReconfigurationApiHealthDuration | Čas v sekundách, výchozí hodnota je 30 |Dynamická| Určete časový rozsah v sekundách. ServiceReconfigurationApiHealthDuration definuje, jak dlouho budeme čekat na spuštění rozhraní API služby, než ohlásíme není v pořádku. To platí pro volání rozhraní API, které mají vliv na dostupnost.|

## <a name="replication"></a>Replikace
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu**| **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Interval potvrzení dávky|TimeSpan, výchozí hodnota je Common::TimeSpan::FromMilliseconds(15)|Statická|Určete časový rozsah v sekundách. Určuje dobu, po kterou replikátor čeká po přijetí operace před odesláním potvrzení zpět. Ostatní operace přijaté během tohoto časového období budou mít jejich potvrzení odeslána zpět v jedné > zprávy snížení síťového provozu, ale potenciálně snížení propustnost replikátoru.|
|Velikost maxcopyfronty|uint, výchozí hodnota je 1024|Statická|Toto je maximální hodnota definuje počáteční velikost fronty, která udržuje operace replikace. Všimněte si, že musí být mocninu 2. Pokud během běhu fronty zvětší tuto velikost operace bude omezena mezi primární a sekundární replikátory.|
|MaxPrimaryReplicationQueueMemorySize|uint, výchozí hodnota je 0|Statická|Toto je maximální hodnota fronty primární replikace v bajtech.|
|Velikost maxprimárníreplikační fronty|uint, výchozí hodnota je 1024|Statická|Toto je maximální počet operací, které by mohly existovat v primární replikační frontě. Všimněte si, že musí být mocninu 2.|
|MaxReplicationMessageSize|uint, výchozí hodnota je 52428800|Statická|Maximální velikost zprávy operací replikace. Výchozí hodnota je 50 MB.|
|Velikost paměti MemoryMemorySize sekundární chodů replikace|uint, výchozí hodnota je 0|Statická|Toto je maximální hodnota fronty sekundární replikace v bajtech.|
|Velikost fronty maxsekundární replikace|uint, výchozí hodnota je 2048|Statická|Toto je maximální počet operací, které by mohly existovat v sekundární replikační frontě. Všimněte si, že musí být mocninu 2.|
|Interval queueHealthMonitoringinterval|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(30)|Statická|Určete časový rozsah v sekundách. Tato hodnota určuje časové období používané replikátorem ke sledování všech událostí stavu upozornění nebo chyby ve frontách operací replikace. Hodnota 0 zakáže monitorování stavu. |
|QueueHealthWarningAtUsagePercent|uint, výchozí hodnota je 80|Statická|Tato hodnota určuje využití fronty replikace (v procentech), po kterém hlásíme upozornění na vysoké využití fronty. Činíme tak po intervalu odkladu QueueHealthMonitoringInterval. Pokud využití fronty klesne pod toto procento v intervalu odkladu|
|Adresa replikátoru|řetězec, výchozí je "localhost:0"|Statická|Koncový bod ve formě řetězce -'IP:Port', který používá replikátor prostředků systému Windows k navázání připojení s jinými replikami za účelem odesílání a přijímání operací.|
|Adresa ReplicatorListen|řetězec, výchozí je "localhost:0"|Statická|Koncový bod ve formě řetězce -'IP:Port', který používá replikátor prostředků systému Windows pro příjem operací z jiných replik.|
|Adresa Publishr Replicator|řetězec, výchozí je "localhost:0"|Statická|Koncový bod ve formě řetězce -'IP:Port', který používá replikátor prostředků systému Windows k odesílání operací do jiných replik.|
|Interval Retry|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(5)|Statická|Určete časový rozsah v sekundách. Pokud je operace ztracena nebo odmítnuta, tento časovač určuje, jak často bude replikátor opakovat odeslání operace.|

## <a name="resourcemonitorservice"></a>Služba ResourceMonitorService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu**| **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|IsEnabled|bool, výchozí hodnota je NEPRAVDA |Statická|Určuje, zda je služba v clusteru povolena či nikoli. |

## <a name="runas"></a>Runas

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|RunasNázev |řetězec, výchozí hodnota je "" |Dynamická|Označuje název účtu RunAs. To je potřeba pouze pro typ účtu "DomainUser" nebo "ManagedServiceAccount". Platné hodnoty jsou "doména\uživatel" nebo "user@domain". |
|Typ účtu Runas|řetězec, výchozí hodnota je "" |Dynamická|Označuje typ účtu RunAs. To je potřeba pro všechny sekce RunAs Platné hodnoty jsou "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunasPassword|řetězec, výchozí hodnota je "" |Dynamická|Označuje heslo účtu RunAs. To je potřeba pouze pro typ účtu "DomainUser". |

## <a name="runas_dca"></a>RunAs_DCA

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|RunasNázev |řetězec, výchozí hodnota je "" |Dynamická|Označuje název účtu RunAs. To je potřeba pouze pro typ účtu "DomainUser" nebo "ManagedServiceAccount". Platné hodnoty jsou "doména\uživatel" nebo "user@domain". |
|Typ účtu Runas|řetězec, výchozí hodnota je "" |Dynamická|Označuje typ účtu RunAs. To je potřeba pro všechny sekce RunAs Platné hodnoty jsou "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunasPassword|řetězec, výchozí hodnota je "" |Dynamická|Označuje heslo účtu RunAs. To je potřeba pouze pro typ účtu "DomainUser". |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|RunasNázev |řetězec, výchozí hodnota je "" |Dynamická|Označuje název účtu RunAs. To je potřeba pouze pro typ účtu "DomainUser" nebo "ManagedServiceAccount". Platné hodnoty jsou "doména\uživatel" nebo "user@domain". |
|Typ účtu Runas|řetězec, výchozí hodnota je "" |Dynamická|Označuje typ účtu RunAs. To je potřeba pro všechny sekce RunAs Platné hodnoty jsou "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunasPassword|řetězec, výchozí hodnota je "" |Dynamická|Označuje heslo účtu RunAs. To je potřeba pouze pro typ účtu "DomainUser". |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|RunasNázev |řetězec, výchozí hodnota je "" |Dynamická|Označuje název účtu RunAs. To je potřeba pouze pro typ účtu "DomainUser" nebo "ManagedServiceAccount". Platné hodnoty jsou "doména\uživatel" nebo "user@domain". |
|Typ účtu Runas|řetězec, výchozí hodnota je "" |Dynamická|Označuje typ účtu RunAs. To je potřeba pro všechny sekce RunAs Platné hodnoty jsou "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunasPassword|řetězec, výchozí hodnota je "" |Dynamická|Označuje heslo účtu RunAs. To je potřeba pouze pro typ účtu "DomainUser". |

## <a name="security"></a>Zabezpečení
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Formát koncového bodu AADCert|řetězec, výchozí hodnota je ""|Statická|Formát koncového bodu certifikátu AAD, výchozí Azure Commercial, určený pro\/nevýchozí{0}prostředí, jako je Azure Government "https: /login.microsoftonline.us/ /federationmetadata/2007-06/federationmetadata.xml" |
|Aplikace AADClient|řetězec, výchozí hodnota je ""|Statická|Název aplikace nativního klienta nebo ID představující klienty infrastruktury |
|Aplikace AADCluster|řetězec, výchozí hodnota je ""|Statická|Název aplikace webového rozhraní API nebo ID představující cluster |
|Koncový bod aplikace AADLogin|řetězec, výchozí hodnota je ""|Statická|Koncový bod přihlášení AAD, výchozí Azure Commercial, určený pro nevýchozí\/prostředí, jako je Azure Government "https: /login.microsoftonline.us" |
|AADTenantId|řetězec, výchozí hodnota je ""|Statická|ID klienta (GUID) |
|AcceptExpiredPinnedClusterCertificate|bool, výchozí hodnota je NEPRAVDA|Dynamická|Příznak označující, zda mají být přijaty certifikáty clusteru, jejichž platnost vypršela, deklarované kryptografickým otiskem Platí pouze pro clusterové certifikáty. tak, aby cluster zůstal naživu. |
|ThumbClientCertThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Kryptografické otisky certifikátů používaných klienty v roli správce. Jedná se o seznam názvů oddělených čárkami. |
|Formát koncového bodu adtokenu|řetězec, výchozí hodnota je ""|Statická|Koncový bod tokenu AAD, výchozí Azure Commercial, určený pro nevýchozí prostředí, jako je Azure Government "https:\//login.microsoftonline.us/{0}" |
|Deklarace klienta admin|řetězec, výchozí hodnota je ""|Dynamická|Všechny možné nároky očekávané od administrátorů; ve stejném formátu jako ClientClaims; tento seznam interně získá přidán do ClientClaims; takže není třeba také přidávat stejné položky ClientClaims. |
|Identity klienta admin|řetězec, výchozí hodnota je ""|Dynamická|Identity systému Windows klientů infrastruktury v roli správce; slouží k autorizaci privilegovaných operací tkaniny. Jedná se o seznam oddělený čárkami; každá položka je název účtu domény nebo název skupiny. Pro pohodlí; účet, který spouští fabric.exe je automaticky přiřazena role správce; tak je skupina ServiceFabricAdministrators. |
|Složka AppRunAsAccountGroupX509|řetězec, výchozí hodnota je /home/sfuser/sfusercerts |Statická|Složka, ve které jsou umístěny certifikáty AppRunAsAccountGroup X509 a soukromé klíče |
|CertifikátExpirySafetyMargin|TimeSpan, výchozí hodnota je Common::TimeSpan::FromMinutes(43200)|Statická|Určete časový rozsah v sekundách. Bezpečnostní rezerva pro vypršení platnosti certifikátu; Stav sestavy stavu certifikátu se změní z OK na Upozornění, pokud je vypršení platnosti blíže než toto. Výchozí hodnota je 30 dní. |
|CertificateHealthReportingInterval|TimeSpan, výchozí je common::TimeSpan::FromSeconds (3600 * 8)|Statická|Určete časový rozsah v sekundách. Zadejte interval pro vykazování stavu certifikátu; výchozí hodnota 8 hodin; nastavení na 0 zakáže vykazování stavu certifikátu |
|ClientCertThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Kryptografické otisky certifikátů používaných klienty k rozhovoru s clusterem; cluster používá toto autorizovat příchozí připojení. Jedná se o seznam názvů oddělených čárkami. |
|ClientClaimAuthEnabled|bool, výchozí hodnota je NEPRAVDA|Statická|Označuje, zda je u klientů povoleno ověřování na základě deklarace; nastavení této true implicitně nastaví ClientRoleEnabled. |
|Klientské nároky|řetězec, výchozí hodnota je ""|Dynamická|Všechny možné nároky očekávané od klientů pro připojení k bráně. Toto je seznam OR: \| \| ClaimsEntry \| \| ClaimsEntry ClaimsEntry ... každý ClaimEntry je seznam "AND": ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue...ClaimValue ... |
|Klientské identity|řetězec, výchozí hodnota je ""|Dynamická|Identity systému Windows fabricclient; pojmenování brány používá k autorizaci příchozích připojení. Jedná se o seznam oddělený čárkami; každá položka je název účtu domény nebo název skupiny. Pro pohodlí; účet, který spouští fabric.exe je automaticky povolena; tak jsou skupiny ServiceFabricAllowedUsers a ServiceFabricAdministrators. |
|ClientRoleEnabled|bool, výchozí hodnota je NEPRAVDA|Statická|Označuje, zda je povolena role klienta. je-li nastavena na hodnotu true; klientům jsou přiřazeny role na základě jejich identit. Pro V2; povolení znamená, že klient, který není v AdminClientCommonNames/AdminClientIdentities, může provádět pouze operace jen pro čtení. |
|ClusterCertThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Kryptografické otisky certifikátů, které se mohou připojit ke clusteru; seznam názvů oddělených čárkami. |
|Typ pověření clusteru|řetězec, výchozí hodnota je "Žádný"|Není povoleno|Označuje typ pověření zabezpečení, která mají být použita k zabezpečení clusteru. Platné hodnoty jsou "None/X509/Windows" |
|Identity clusteru|řetězec, výchozí hodnota je ""|Dynamická|Identity systému Windows uzlů clusteru; používá se pro autorizaci členství v clusteru. Jedná se o seznam oddělený čárkami; každá položka je název účtu domény nebo název skupiny |
|ClusterSpn|řetězec, výchozí hodnota je ""|Není povoleno|Název hlavního serveru služby clusteru; když fabric běží jako jeden uživatel domény (gMSA/doménový uživatelský účet). Je SPN zapůjčení posluchačů a posluchačů v fabric.exe: naslouchací procesy federace; interní naslouchací procesy replikace; naslouchací proces služby runtime a naslouchací proces pojmenování brány. To by mělo zůstat prázdné při fabric běží jako účty počítače; v takovém případě připojení straně výpočetní naslouchací proces SPN z adresy přenosu posluchače. |
|Příznak CrlChecking|uint, výchozí hodnota je 0x40000000|Dynamická|Výchozí příznak ověření řetězu certifikátu; může být přepsán příznakem specifickým pro daný komponentu; Například Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x4000000CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x8000000CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Nastavení na 0 zakáže crl kontrola Úplný seznam podporovaných hodnot je dokumentován dwFlags certGetChain:https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, výchozí hodnota je Common::TimeSpan::FromMinutes(15)|Dynamická|Určete časový rozsah v sekundách. Jak dlouho je kontrola seznamu odvolaných certifikátů pro daný certifikát zakázána po výskytu chyby offline. pokud lze chybu offline seznamu odvolaných od volaných v souladu s čl. |
|CrlOfflineHealthReportTtl|TimeSpan, výchozí hodnota je Common::TimeSpan::FromMinutes(1440)|Dynamická|Určete časový rozsah v sekundách. |
|Zakázat profil domény FirewallRuleForDomainProfile| bool, výchozí hodnota je TRUE |Statická| Označuje, zda by pro profil domény nemělo být povoleno pravidlo brány firewall. |
|Zakázat zásadu firewallrulepro soukromý profil| bool, výchozí hodnota je TRUE |Statická| Označuje, zda by pravidlo brány firewall nemělo být povoleno pro soukromý profil. | 
|Zakázat zásadu firewallrulepro veřejný profil| bool, výchozí hodnota je TRUE | Statická|Označuje, zda by pravidlo brány firewall nemělo být povoleno pro veřejný profil. |
| EnforceLinuxMinTlsVersion | bool, výchozí hodnota je NEPRAVDA | Statická | Je-li nastavena na hodnotu true; podporována je pouze TLS verze 1.2+.  Pokud false; podporuje starší verze TLS. Platí pouze pro Linux |
| EnforcePrevalidationOnSecurityChanges | bool, výchozí hodnota je NEPRAVDA| Dynamická | Příznak řízení chování upgradu clusteru při zjišťování změn jeho nastavení zabezpečení. Pokud je nastavena na hodnotu "true", upgrade clusteru se pokusí zajistit, že alespoň jeden z certifikátů odpovídajících některému z pravidel prezentace může projít odpovídajícím ověřovacím pravidlem. Předběžné ověření je provedeno před použitím nového nastavení na libovolný uzel, ale běží pouze na uzlu hostujícím primární repliku služby Cluster Manager v době zahájení inovace. Výchozí hodnota je aktuálně nastavena na hodnotu false. počínaje verzí 7.1 bude nastavení nastaveno na hodnotu "true" pro nové clustery Azure Service Fabric.|
|FabricHostSpn| řetězec, výchozí hodnota je "" |Statická| Hlavní název služby FabricHost; když fabric běží jako jeden uživatel domény (gMSA/doménový uživatelský účet) a FabricHost běží pod účet počítače. Jedná se o hlavní název spn naslouchací proces IPC pro FabricHost; který ve výchozím nastavení by měl být ponechán prázdný, protože FabricHost běží pod účtem počítače |
|Chyba IgnorCrlOfflineError|bool, výchozí hodnota je NEPRAVDA|Dynamická|Zda ignorovat chybu offline seznamu odvolaných certifikátů, pokud ověří příchozí klientské certifikáty na straně serveru |
|Chyba ignoroceSvrCrlOfflineError|bool, výchozí hodnota je TRUE|Dynamická|Určuje, zda má být chyba offline seznamu odvolaných certifikátů ignorována, pokud ověří příchozí certifikáty serveru. výchozí hodnota true. Útoky se odvolanými serverovými certifikáty vyžadují ohrožení služby DNS. těžší než u odvolaných klientských certifikátů. |
|ServerAuthTyp pověření|řetězec, výchozí hodnota je "Žádný"|Statická|Označuje typ pověření zabezpečení, která mají být použita k zabezpečení komunikace mezi klientem FabricClient a clusterem. Platné hodnoty jsou "None/X509/Windows" |
|ServerCertThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Kryptografické otisky serverových certifikátů používaných clusterem k rozhovoru s klienty; klienti používají k ověření clusteru. Jedná se o seznam názvů oddělených čárkami. |
|SettingsX509Název_obchodu| řetězec, výchozí hodnota je "MY"| Dynamická|Úložiště certifikátů X509 používané látkou pro ochranu konfigurace |
|UseClusterCertForIpcServerTlsSecurity|bool, výchozí hodnota je NEPRAVDA|Statická|Zda použít certifikát clusteru k zabezpečení přenosové jednotky TLS serveru IPC Server |
|Složka X509|řetězec, výchozí hodnota je /var/lib/waagent|Statická|Složka, ve které jsou umístěny certifikáty X509 a soukromé klíče |
|TLS1_2_CipherList| řetězec| Statická|Pokud je nastavena na neprázdný řetězec; přepíše podporovaný seznam šifraů pro TLS1.2 a níže. Viz dokumentace "openssl-ciphers" pro načtení podporovaného seznamu šifrovacích a formát seznamu Příklad silného šifrovacího seznamu pro TLS1.2: "ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES-128-GCM-SHA256:ECDHE-ECDSA-AES256-CBC-SHA384:ECDHE-ECDSA-AES128-CBC-SHA256:ECDHE-RSA-AES256-CBC-SHA384:ECDHE-RSA-AES128-CBC-SHA256" Platí pouze pro Linux. |

## <a name="securityadminclientx509names"></a>Bezpečnostní/adminClientX509Názvy

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|X509NameMap, výchozí hodnota je Žádný|Dynamická|Toto je seznam dvojice "Název" a "Hodnota". Každý "Název" je předmětu běžný název nebo DnsName certifikátů X509 autorizovaných pro operace klienta správce. Pro daný "Název", "Hodnota" je čárka samostatný seznam kryptografických otisků certifikátu pro připnutí vystavitele, pokud není prázdný, přímý vystavitel klientských certifikátů správce musí být v seznamu. |

## <a name="securityclientaccess"></a>Zabezpečení/Přístup klienta

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Aktivovatuzel |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro aktivaci uzlu. |
|CancelTestCommand |řetězec, výchozí je "Admin" |Dynamická| Zruší konkrétní TestCommand - pokud je v letu. |
|CodePackageControl |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro restartování balíčků kódu. |
|Vytvořit aplikaci |řetězec, výchozí je "Admin" | Dynamická|Konfigurace zabezpečení pro vytvoření aplikace. |
|CreateComposeDeployment|řetězec, výchozí je "Admin"| Dynamická|Vytvoří nasazení komponování popsané vytvářením souborů. |
|CreateGatewayResource|řetězec, výchozí je "Admin"| Dynamická|Vytvoření prostředku brány |
|CreateName |řetězec, výchozí je "Admin" |Dynamická|Konfigurace zabezpečení pro vytvoření identifikátoru URI pro pojmenování. |
|Vytvořit síť|řetězec, výchozí je "Admin" |Dynamická|Vytvoří síť kontejnerů. |
|Vytvořit službu |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro vytvoření služby. |
|CreateServiceFromTemplate |řetězec, výchozí je "Admin" |Dynamická|Konfigurace zabezpečení pro vytvoření služby ze šablony. |
|Vytvořitsvazek|řetězec, výchozí je "Admin"|Dynamická|Vytvoří svazek |
|Deaktivovatuzel |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro deaktivaci uzlu. |
|DeaktivovatnodesBatch |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro deaktivaci více uzlů. |
|Odstranění |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro operaci odstranění klienta úložiště bitových kopie. |
|Odstranit aplikaci |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro odstranění aplikace. |
|OdstranitcomposeDeployment|řetězec, výchozí je "Admin"| Dynamická|Odstraní nasazení komponování. |
|Odstranit prostředek brány|řetězec, výchozí je "Admin"| Dynamická|Odstraní prostředek brány. |
|Odstranitnázev |řetězec, výchozí je "Admin" |Dynamická|Konfigurace zabezpečení pro odstranění identifikátoru URI pojmenování. |
|Odstranit síť|řetězec, výchozí je "Admin" |Dynamická|Odstraní síť kontejnerů. |
|Odstranit službu |řetězec, výchozí je "Admin" |Dynamická|Konfigurace zabezpečení pro odstranění služby. |
|Odstranit svazek|řetězec, výchozí je "Admin"|Dynamická|Odstraní svazek.| 
|Výčet vlastností |řetězec, výchozí je\|\|"Admin User" | Dynamická|Konfigurace zabezpečení pro výčet vlastnosti Naming. |
|VýčetPodnázvů |řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro výčet identifikátoru URI pojmenování. |
|Obsah souboru |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro přenos souborů klienta úložiště bitových obrázků (externí do clusteru). |
|SouborKestažení |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro zahájení stahování souboru klienta úložiště bitových obrázků (externí clusteru). |
|Úkol dokončení infrastruktury |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro dokončení úloh infrastruktury. |
|GetChaosReport | řetězec, výchozí je\|\|"Admin User" |Dynamická| Načte stav Chaos v daném časovém rozsahu. |
|GetClusterConfiguration | řetězec, výchozí je\|\|"Admin User" | Dynamická|Indukuje GetClusterConfiguration na oddílu. |
|GetClusterConfigurationUpgradeStatus | řetězec, výchozí je\|\|"Admin User" |Dynamická| Indukuje GetClusterConfigurationUpgradeStatus v oddílu. |
|GetFabricUpgradeStatus |řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro stav upgradu clusteru dotazování. |
|Velikost_složky GetFolderSize |řetězec, výchozí je "Admin" |Dynamická|Konfigurace zabezpečení pro získání velikosti složky FileStoreService |
|GetNodeDeactivationStatus |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro kontrolu stavu deaktivace. |
|GetNodeTransitionProgress | řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro získání průběhu na příkaz přechodu uzlu. |
|GetPartitionDataLossProgress | řetězec, výchozí je\|\|"Admin User" | Dynamická|Načte průběh pro volání vyvolat ztrátu dat rozhraní API volání. |
|GetPartitionQuorumLossProgress | řetězec, výchozí je\|\|"Admin User" |Dynamická| Načte průběh pro volání volání rozhraní API ztráty kvora. |
|GetPartitionRestartProgress | řetězec, výchozí je\|\|"Admin User" |Dynamická| Načte průběh pro volání rozhraní API restartování oddílu. |
|GetSecrets|řetězec, výchozí je "Admin"|Dynamická|Získat tajné hodnoty |
|Popis služby GetService |řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro oznámení služby s dlouhým dotazem a popisy služby čtení. |
|GetStagingUmístění |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro načítání umístění klienta úložiště bitových míst úložiště bitových kopie. |
|GetStoreLocation |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro načítání umístění úložiště klienta úložiště bitových kopie. |
|GetUpgradeOrchestrationServiceState|řetězec, výchozí je "Admin"| Dynamická|Indukuje GetUpgradeOrchestraationServiceState na oddílu |
|GetUpgradesPendingApproval GetUpgradeSPending Approval GetUpgradesPending Approval GetUpgrade |řetězec, výchozí je "Admin" |Dynamická| Indukuje GetUpgradesPendingApproval na oddíl. |
|GetUpgradeStatus |řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro stav upgradu aplikace dotazování. |
|Interní seznam |řetězec, výchozí je "Admin" | Dynamická|Konfigurace zabezpečení pro operaci seznamu souborů klienta úložiště bitových obrázků (interní). |
|InvokeContainerApi|řetězec,výchozí hodnota je "Admin"|Dynamická|Vyvolat rozhraní API kontejneru |
|InvokeInfrastructureCommand |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro příkazy správy úloh infrastruktury. |
|InvokeInfrastructureQuery |řetězec, výchozí je\|\|"Admin User" | Dynamická|Konfigurace zabezpečení pro dotazování úloh infrastruktury. |
|Seznam |řetězec, výchozí je\|\|"Admin User" | Dynamická|Konfigurace zabezpečení pro operaci seznamu souborů klienta úložiště bitových kopie. |
|MoveNextFabricUpgradeDomain |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro obnovení upgradů clusteru s explicitní inovační doménou. |
|MoveNextUpgradeDomain |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro obnovení upgradů aplikací s explicitní doménou upgradu. |
|MoveReplicaControl |řetězec, výchozí je "Admin" | Dynamická|Přesunout repliku. |
|NázevExists |řetězec, výchozí je\|\|"Admin User" | Dynamická|Konfigurace zabezpečení pro kontroly existence identifikátoru URI pojmenování. |
|NodeControl |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro spuštění; zastavení; a restartování uzlů. |
|NodeStateRemoved |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro hlášení stavu uzlu odebrána. |
|Ping |řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro příkazy ping klienta. |
|PredeployPackageToNode |řetězec, výchozí je "Admin" |Dynamická| Rozhraní API před nasazením. |
|PrefixResolveService |řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro řešení předpony služby založené na stížnostech. |
|VlastnostReadBatch |řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro operace čtení vlastností Naming. |
|PropertyWriteBatch |řetězec, výchozí je "Admin" |Dynamická|Konfigurace zabezpečení pro operace zápisu vlastností Naming. |
|ProvisionApplicationType |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro zřizování typu aplikace. |
|ProvisionFabric |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro msi nebo manifest clusteru zřizování. |
|Dotaz |řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro dotazy. |
|RecoverPartition |řetězec, výchozí je "Admin" | Dynamická|Konfigurace zabezpečení pro obnovení oddílu. |
|RecoverPartitions |řetězec, výchozí je "Admin" | Dynamická|Konfigurace zabezpečení pro obnovení oddílů. |
|RecoverServicePartitions |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro obnovení oddílů služby. |
|RecoverSystemPartitions |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro obnovení oddílů systémových služeb. |
|RemoveNodeDeactivations |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro vrácení deaktivace na více uzlech. |
|ReportFabricUpgradeHealth |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro obnovení upgradů clusteru s aktuálním průběhem upgradu. |
|Chyba report |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro hlášení chyby. |
|ReportHealth |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro hlášení stavu. |
|Stav upgradu sestavy |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro obnovení upgradů aplikací s aktuálním průběhem upgradu. |
|ResetPartitionLoad |řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro resetování zatížení pro převzetí služeb při selháníUnit. |
|ResolveNameOwner |řetězec, výchozí je\|\|"Admin User" | Dynamická|Konfigurace zabezpečení pro překlad vlastníka identifikátoru URI pojmenování. |
|Vyřešit oddíl |řetězec, výchozí je\|\|"Admin User" | Dynamická|Konfigurace zabezpečení pro řešení systémových služeb. |
|ResolveService |řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro řešení služeb založené na stížnostech. |
|Služba ResolveSystemService|řetězec, výchozí je\|\|"Admin User"|Dynamická| Konfigurace zabezpečení pro řešení systémových služeb |
|Upgrade aplikace vrácení zpět |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro vrácení inovací aplikací zpět. |
|Kumulativní fabricupgrade |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro vrácení inovací clusteru zpět. |
|Oznámení služby |řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro oznámení služby založené na událostech. |
|SetUpgradeOrchestrationServiceState|řetězec, výchozí je "Admin"| Dynamická|Indukuje SetUpgradeOrchestraationServiceState na oddílu |
|SpustitApprovedUpgrades |řetězec, výchozí je "Admin" |Dynamická| Indukuje StartApprovedUpgrads na oddílu. |
|StartChaos |řetězec, výchozí je "Admin" |Dynamická| Spustí Chaos - pokud ještě není spuštěn. |
|StartClusterConfigurationUpgrade |řetězec, výchozí je "Admin" |Dynamická| Indukuje StartClusterConfigurationUpgrade na oddílu. |
|Úkol StartInfrastructure |řetězec, výchozí je "Admin" | Dynamická|Konfigurace zabezpečení pro spuštění úloh infrastruktury. |
|StartNodeTransition |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro spuštění přechodu uzlu. |
|StartPartitionDataLoss |řetězec, výchozí je "Admin" |Dynamická| Vyvolá ztrátu dat v oddílu. |
|StartPartitionQuorumLoss |řetězec, výchozí je "Admin" |Dynamická| Indukuje ztrátu kvora v oddílu. |
|StartPartitionRestart |řetězec, výchozí je "Admin" |Dynamická| Současně restartuje některé nebo všechny repliky oddílu. |
|StopChaos |řetězec, výchozí je "Admin" |Dynamická| Zastaví Chaos - pokud byl spuštěn. |
|ToggleVerboseServicePlacementHealthReporting | řetězec, výchozí je\|\|"Admin User" |Dynamická| Konfigurace zabezpečení pro přepínání verbose ServicePlacement HealthReporting. |
|UnprovisionApplicationType |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro zrušení zřízení typu aplikace. |
|UnprovisionFabric |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro zrušení zřízení msi nebo manifestu clusteru. |
|NespolehlivéTransportControl |řetězec, výchozí je "Admin" |Dynamická| Nespolehlivé Přenos pro přidání a odebrání chování. |
|Služba UpdateService |řetězec, výchozí je "Admin" |Dynamická|Konfigurace zabezpečení pro aktualizace služby. |
|Aplikace upgradu |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro spuštění nebo přerušení upgradů aplikací. |
|UpgradeComposeDeployment|řetězec, výchozí je "Admin"| Dynamická|Upgraduje nasazení komponování. |
|Upgrade Fabric |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro spuštění upgradů clusteru. |
|Odeslat |řetězec, výchozí je "Admin" | Dynamická|Konfigurace zabezpečení pro operaci nahrávání klienta úložiště bitových obrázků. |

## <a name="securityclientcertificateissuerstores"></a>Úložiště zabezpečení/klientského certifikátu

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|IssuerStoreKeyValueMap, výchozí hodnota je Žádný |Dynamická|Úložiště certifikátů vystavitele X509 pro klientské certifikáty; Název = clientIssuerCN; Hodnota = seznam obchodů oddělených čárkami |

## <a name="securityclientx509names"></a>Zabezpečení/ClientX509Názvy

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|X509NameMap, výchozí hodnota je Žádný|Dynamická|Toto je seznam dvojice "Název" a "Hodnota". Každý "Název" je předmětu běžný název nebo DnsName certifikátů X509 autorizovaných pro klientské operace. Pro daný "Název", "Hodnota" je čárka samostatný seznam kryptografických otisků certifikátu pro připnutí vystavitele, pokud není prázdný, přímý vystavitel klientských certifikátů musí být v seznamu.|

## <a name="securityclustercertificateissuerstores"></a>Úložiště zabezpečení/clusteruCertificateIssuerStores

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|IssuerStoreKeyValueMap, výchozí hodnota je Žádný |Dynamická|Úložiště certifikátů vystavittele X509 pro certifikáty clusteru; Název = clusterIssuerCN; Hodnota = seznam obchodů oddělených čárkami |

## <a name="securityclusterx509names"></a>Zabezpečení/ClusterX509Názvy

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|X509NameMap, výchozí hodnota je Žádný|Dynamická|Toto je seznam dvojice "Název" a "Hodnota". Každý "Název" je předmětu běžný název nebo DnsName certifikátů X509 autorizovaných pro operace clusteru. Pro daný "Název","Hodnota" je čárka samostatný seznam kryptografických otisků certifikátu pro připnutí vystavittele, pokud není prázdný, přímý vystavittel clusterových certifikátů musí být v seznamu.|

## <a name="securityservercertificateissuerstores"></a>Úložiště zabezpečení/serverových certifikátů

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|IssuerStoreKeyValueMap, výchozí hodnota je Žádný |Dynamická|Úložiště certifikátů vystavitena X509 pro serverové certifikáty; Název = serverIssuerCN; Hodnota = seznam obchodů oddělených čárkami |

## <a name="securityserverx509names"></a>Bezpečnostní/ServerX509Názvy

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup|X509NameMap, výchozí hodnota je Žádný|Dynamická|Toto je seznam dvojice "Název" a "Hodnota". Každý "Název" je předmětu běžný název nebo DnsName certifikátů X509 autorizovaných pro serverové operace. Pro daný "Název", "Hodnota" je čárka samostatný seznam kryptografických otisků certifikátu pro připnutí vystavittele, pokud není prázdný, přímý vystavittel certifikátů serveru musí být v seznamu.|

## <a name="setup"></a>Nastavení

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|ContainerNetworkName|řetězec, výchozí hodnota je ""| Statická |Název sítě, který se má použít při nastavování sítě kontejnerů.|
|ContainerNetworkSetup|bool, výchozí je FALSE (Linux) a výchozí je TRUE (Windows)| Statická |Určuje, zda se má nastavit síť kontejnerů.|
|FabricDataRoot |Řetězec | Není povoleno |Kořenový adresář dat service fabric. Výchozí pro Azure je d:\svcfab |
|FabricLogRoot |Řetězec | Není povoleno |Kořenový adresář protokolu infrastruktury služby. To to je místo, kde sf protokoly a stopy jsou umístěny. |
|UzlyToBeRemoved|řetězec, výchozí hodnota je ""| Dynamická |Uzly, které by měly být odebrány jako součást upgradu konfigurace. (Pouze pro samostatná nasazení)|
|Název_účtu ServiceRunAs |Řetězec | Není povoleno |Název účtu, pod kterým chcete spustit hostitelskou službu prostředků infrastruktury. |
|SkipContainerNetworkResetonReboot|bool, výchozí hodnota je NEPRAVDA|Není povoleno.|Určuje, zda má být při restartování přenecháno resetování sítě kontejnerů.|
|Přeskočit konfiguraci brány firewall |Bool, výchozí hodnota je false | Není povoleno |Určuje, zda je třeba nastavení brány firewall nastavit systémem nebo ne. To platí pouze v případě, že používáte bránu firewall systému Windows. Pokud používáte brány firewall třetích stran, musíte otevřít porty pro systém a aplikace, které chcete používat |

## <a name="tokenvalidationservice"></a>Služba TokenValidationService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Poskytovatelé |řetězec, výchozí hodnota je "DSTS" |Statická|Seznam poskytovatelů ověření tokenů oddělený chod čárek, který chcete povolit (platnými zprostředkovateli jsou: DSTS; AAD). V současné době lze povolit pouze jednoho zprostředkovatele. |

## <a name="traceetw"></a>Trasování/Etw

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Úroveň |Int, výchozí hodnota je 4 | Dynamická |Úroveň trace etw může mít hodnoty 1, 2, 3, 4. Chcete-li být podporovány, musíte udržovat úroveň trasování na 4 |

## <a name="transactionalreplicator"></a>Transakční replikátor

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Interval potvrzení dávky | Čas v sekundách, výchozí hodnota je 0,015 | Statická | Určete časový rozsah v sekundách. Určuje dobu, po kterou replikátor čeká po přijetí operace před odesláním potvrzení zpět. Ostatní operace přijaté během tohoto časového období budou mít jejich potvrzení odeslána zpět v jedné > zprávy snížení síťového provozu, ale potenciálně snížení propustnost replikátoru. |
|Velikost maxcopyfronty |Uint, výchozí hodnota je 16384 | Statická |Toto je maximální hodnota definuje počáteční velikost fronty, která udržuje operace replikace. Všimněte si, že musí být mocninu 2. Pokud během běhu fronty zvětší tuto velikost operace bude omezena mezi primární a sekundární replikátory. |
|MaxPrimaryReplicationQueueMemorySize |Uint, výchozí hodnota je 0 | Statická |Toto je maximální hodnota fronty primární replikace v bajtech. |
|Velikost maxprimárníreplikační fronty |Uint, výchozí hodnota je 8192 | Statická |Toto je maximální počet operací, které by mohly existovat v primární replikační frontě. Všimněte si, že musí být mocninu 2. |
|MaxReplicationMessageSize |Uint, výchozí hodnota je 52428800 | Statická | Maximální velikost zprávy operací replikace. Výchozí hodnota je 50 MB. |
|Velikost paměti MemoryMemorySize sekundární chodů replikace |Uint, výchozí hodnota je 0 | Statická |Toto je maximální hodnota fronty sekundární replikace v bajtech. |
|Velikost fronty maxsekundární replikace |Uint, výchozí hodnota je 16384 | Statická |Toto je maximální počet operací, které by mohly existovat v sekundární replikační frontě. Všimněte si, že musí být mocninu 2. |
|Adresa replikátoru |řetězec, výchozí je "localhost:0" | Statická | Koncový bod ve formě řetězce -'IP:Port', který používá replikátor prostředků systému Windows k navázání připojení s jinými replikami za účelem odesílání a přijímání operací. |

## <a name="transport"></a>Přenos
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu** |**Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(60)|Statická|Určete časový rozsah v sekundách. Časový limit pro nastavení připojení na příchozí i přijímající straně (včetně vyjednávání zabezpečení v zabezpečeném režimu) |
|Kontrola errorerrorerrorsu rámečkupovoleno|bool, výchozí hodnota je TRUE|Statická|Výchozí nastavení pro kontrolu chyb v záhlaví rámce v nezabezpečeném režimu; nastavení součásti to toto přepíše. |
|Kontrola chyby v chyběPovoleno|bool,výchozí hodnota je NEPRAVDA|Statická|Výchozí nastavení pro kontrolu chyb v záhlaví a textu zprávy v nezabezpečeném režimu; nastavení součásti to toto přepíše. |
|Vyřešit možnost|řetězec, výchozí hodnota je "neurčeno"|Statická|Určuje, jak je vyřešen a jak je fQDN vyřešen.  Platné hodnoty jsou "nespecifikované/ipv4/ipv6". |
|SendTimeout|TimeSpan, výchozí hodnota je Common::TimeSpan::FromSeconds(300)|Dynamická|Určete časový rozsah v sekundách. Odeslat časový limit pro detekci zablokované připojení. Zprávy o selhání protokolu TCP nejsou v některých prostředích spolehlivé. To může být nutné upravit podle dostupné šířky pásma\*sítě\/\*a velikosti odchozích dat (MaxMessageSize SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestraationService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Možnost automatického upgradu | Bool, výchozí hodnota je pravda |Statická| Automatické dotazování a upgrade akce na základě souboru stavu cíle. |
|Funkce Automatické inovaceIniktumA|Bool, výchozí hodnota je FALSE|Statická|Automatické dotazování, zřizování a instalace akce upgradu kódu na základě souboru stavu cíle.|
|GoalStateExpirationReminderInDays|int, výchozí hodnota je 30|Statická|Nastaví počet zbývajících dnů, po kterých má být zobrazena připomínka stavu cíle.|
|MinReplicaSetSize |Int, výchozí hodnota je 0 |Statická |MinReplicaSetSize pro UpgradeOrchestrationService.|
|Omezení umístění | řetězec, výchozí hodnota je "" |Statická| PlacementConstraints pro UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Čas v sekundách, výchozí hodnota je MaxValue |Statická| Určete časový rozsah v sekundách. QuorumLossWaitDuration pro UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Čas v sekundách, výchozí hodnota je 60 minut|Statická| Určete časový rozsah v sekundách. ReplikaRestartWaitDuration pro UpgradeOrchestrationService. |
|Doba trvání pohotovostního režimu | Čas v sekundách, výchozí hodnota je 60*24*7 minut |Statická| Určete časový rozsah v sekundách. Doba spánku Pro upgradeOrchestraationService. |
|Cílová replikaSetSize |Int, výchozí hodnota je 0 |Statická |TargetReplicaSetSize pro UpgradeOrchestrationService. |
|UpgradeApprovalApprovalRequired | Bool, výchozí hodnota je false | Statická|Nastavení, aby upgrade kódu vyžadoval schválení správce mne, než budete pokračovat. |

## <a name="upgradeservice"></a>Služba UpgradeService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Základní adresa Url | řetězec, výchozí hodnota je "" |Statická|BaseUrl pro UpgradeService. |
|ClusterId | řetězec, výchozí hodnota je "" |Statická|ClusterId pro UpgradeService. |
|Typ koordinátora | řetězec, výchozí hodnota je "WUTest"|Není povoleno|Typ koordinátora pro upgradeservice. |
|MinReplicaSetSize | Int, výchozí hodnota je 2 |Není povoleno| MinReplicaSetSize pro UpgradeService. |
|OnlyBaseUpgrade | Bool, výchozí hodnota je false |Dynamická|OnlyBaseUpgrade pro UpgradeService. |
|Omezení umístění |řetězec, výchozí hodnota je "" |Není povoleno|PlacementConstraints pro upgrade služby. |
|PollIntervalInSeconds|Timespan, výchozí hodnota je Common::TimeSpan::FromSeconds(60) |Dynamická|Určete časový rozsah v sekundách. Interval mezi upgradeservice dotazování pro operace správy ARM. |
|Cílová replikaSetSize | Int, výchozí hodnota je 3 |Není povoleno| TargetReplicaSetSize pro UpgradeService. |
|Složky TestCab | řetězec, výchozí hodnota je "" |Statická| TestCabFolder pro UpgradeService. |
|X509Typ hledání | řetězec, výchozí hodnota je ""|Dynamická| X509FindType pro UpgradeService. |
|Hodnota X509FindValue | řetězec, výchozí hodnota je "" |Dynamická| X509FindValue pro UpgradeService. |
|X509SekundárníFindValue | řetězec, výchozí hodnota je "" |Dynamická| X509SecondaryFindValue pro upgradeservice. |
|X509Umístění store | řetězec, výchozí hodnota je "" |Dynamická| X509StoreLocation pro UpgradeService. |
|X509Název_obchodu | řetězec, výchozí hodnota je "My"|Dynamická|X509StoreName pro UpgradeService. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricKapacity
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
|Propertygroup| UserServiceMetricCapacitiesMap, výchozí hodnota je Žádný | Statická | Kolekce omezení prostředků uživatelských služeb musí být statická, protože ovlivňuje logiku automatického zjišťování. |

## <a name="next-steps"></a>Další kroky
Další informace najdete [v tématu Upgrade konfigurace clusteru Azure](service-fabric-cluster-config-upgrade-azure.md) a [upgrade konfigurace samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md).
