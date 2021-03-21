---
title: Změnit nastavení clusteru Azure Service Fabric
description: Tento článek popisuje nastavení prostředků infrastruktury a zásady upgradu prostředků infrastruktury, které můžete přizpůsobit.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: 78d83faea802862d3cd6d1b1a9cf9f1016245065
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232047"
---
# <a name="customize-service-fabric-cluster-settings"></a>Přizpůsobení nastavení clusteru Service Fabric
Tento článek popisuje různá nastavení prostředků infrastruktury pro váš Service Fabric cluster, který můžete přizpůsobit. Pro clustery hostované v Azure můžete nastavení přizpůsobit prostřednictvím [Azure Portal](https://portal.azure.com) nebo pomocí Azure Resource Manager šablony. Další informace najdete v tématu [Upgrade konfigurace clusteru Azure](service-fabric-cluster-config-upgrade-azure.md). Pro samostatné clustery přizpůsobíte nastavení tím, že aktualizujete *ClusterConfig.jsna* soubor a provádíte upgrade konfigurace v clusteru. Další informace najdete v tématu [Upgrade konfigurace samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md).

Existují tři různé zásady upgradu:

- **Dynamické** – změny dynamické konfigurace nezpůsobí restartování žádného procesu Service Fabricch procesů ani procesů hostování služby. 
- **Statické** – změny statické konfigurace způsobí, že se uzel Service Fabric restartuje, aby se změna projevila. Služby na uzlech budou restartovány.
- **Nelůjelné** – tato nastavení nelze upravovat. Změna těchto nastavení vyžaduje, aby se cluster zničil a vytvořil nový cluster. 

Následuje seznam nastavení prostředků infrastruktury, která lze přizpůsobit, Uspořádáno podle části.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/http

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|řetězec, výchozí hodnota je None.|Static| Tím se certifikát serveru neověřuje. požadavek byl úspěšný. V části config ServiceCertificateThumbprints najdete čárkami oddělený seznam kryptografických otisků vzdálených certifikátů, které může reverzní proxy důvěřovat. V tématu config ServiceCommonNameAndIssuer najdete název předmětu a kryptografický otisk vystavitele vzdálených certifikátů, které může reverzní proxy důvěřovat. Další informace najdete v tématu [zabezpečené připojení k reverznímu proxy serveru](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, výchozí hodnota je 16384 |Dynamická| Udává velikost bloku v bajtech použitých ke čtení těla. |
|CrlCheckingFlag|uint, Default je 0x40000000 |Dynamická| Příznaky pro ověření řetězu certifikátů aplikací nebo služeb; například kontrola CRL 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY nastavení na hodnotu 0 zakáže kontrolu seznamu CRL úplný seznam podporovaných hodnot je dokumentován dwFlags CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Čas v sekundách. Výchozí hodnota je 120. |Dynamická|Zadejte časový interval v sekundách.  Poskytuje výchozí časový limit žádosti pro požadavky HTTP zpracovávané ve službě HTTP App Gateway. |
|ForwardClientCertificate|logická hodnota, výchozí hodnota je FALSE.|Dynamická|Při nastavení na hodnotu false nebude reverzní proxy vyžadovat klientský certifikát. Při nastavení na hodnotu true bude reverzní proxy požadovat certifikát klienta během metody handshake TLS a přesměrovat řetězec formátu PEM kódovaný na kódování Base64 do služby v hlavičce s názvem X-Client-Certificate. služba může po kontrole dat certifikátu selhat požadavek s příslušným stavovým kódem. Pokud je to pravda a klient neprezentuje certifikát, reverzní proxy přepošle prázdné záhlaví a umožní, aby služba zavedla případ. Reverzní proxy bude fungovat jako průhledná vrstva. Další informace najdete v tématu [nastavení ověřování klientských certifikátů](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |řetězec, výchozí hodnota je None. |Static| Určuje typ přihlašovacích údajů zabezpečení, které se mají použít u koncových bodů služby http App Gateway. platné hodnoty jsou None/x509. |
|GatewayX509CertificateFindType |řetězec, výchozí hodnota je "FindByThumbprint" |Dynamická| Určuje, jak vyhledat certifikát v úložišti zadaném hodnotou GatewayX509CertificateStoreName Supported: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | řetězec, výchozí hodnota je "" |Dynamická| Hodnota vyhledávacího filtru, která se používá k vyhledání certifikátu služby http App Gateway Tento certifikát je nakonfigurovaný na koncovém bodu https a dá se taky použít k ověření identity aplikace, pokud to vyžadují služby. Je vyhledána FindValue jako první; a pokud neexistuje, FindValueSecondary je vyhledán. |
|GatewayX509CertificateFindValueSecondary | řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru, která se používá k vyhledání certifikátu služby http App Gateway Tento certifikát je nakonfigurovaný na koncovém bodu https a dá se taky použít k ověření identity aplikace, pokud to vyžadují služby. Je vyhledána FindValue jako první; a pokud neexistuje, FindValueSecondary je vyhledán.|
|GatewayX509CertificateStoreName |řetězec, výchozí hodnota je my |Dynamická| Název úložiště certifikátů X. 509, které obsahuje certifikát pro bránu http App Gateway. |
|HttpRequestConnectTimeout|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (5)|Dynamická|Zadejte časový interval v sekundách.  Poskytuje časový limit připojení pro požadavky HTTP odesílané z služby http App Gateway.  |
|IgnoreCrlOfflineError|logická hodnota, výchozí hodnota je TRUE.|Dynamická|Určuje, zda má být pro ověřování certifikátu aplikace/služby ignorována chyba režimu CRL. |
|IsEnabled |Logická hodnota, výchozí hodnota je false. |Static| Povolí nebo zakáže HttpApplicationGateway. HttpApplicationGateway je ve výchozím nastavení zakázaná a tato konfigurace musí být nastavená tak, aby se povolila. |
|NumberOfParallelOperations | Uint, výchozí hodnota je 5000 |Static|Počet operací čtení, které se mají odeslat do fronty serveru http Tento ovládací prvek řídí počet souběžných požadavků, které může HttpGateway splnit. |
|RemoveServiceResponseHeaders|řetězec, výchozí hodnota je "datum; WebServer|Static|Středník nebo čárkami oddělený seznam hlaviček odpovědí, které budou odebrány z odpovědi na službu; před předáním klientovi. Pokud je toto nastaveno na prázdný řetězec; Předejte všechny hlavičky vrácené službou tak, jak jsou. t Nepřepisovat datum a Server |
|ResolveServiceBackoffInterval |Čas v sekundách, výchozí hodnota je 5. |Dynamická|Zadejte časový interval v sekundách.  Nastaví výchozí Back-off interval před opakováním operace neúspěšného překladu služby. |
|SecureOnlyMode|logická hodnota, výchozí hodnota je FALSE.|Dynamická| SecureOnlyMode: true: reverzní proxy bude předáván pouze službám, které publikují zabezpečené koncové body. false: reverzní proxy může přesměrovat požadavky do zabezpečených nebo nezabezpečených koncových bodů. Další informace najdete v tématu [logika výběru koncového bodu proxy](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Čárkami oddělený seznam kryptografických otisků vzdálených certifikátů, které může reverzní proxy důvěřovat. Další informace najdete v tématu [zabezpečené připojení k reverznímu proxy serveru](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/http/ServiceCommonNameAndIssuer

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, výchozí hodnota je None|Dynamická| Název subjektu a kryptografický otisk vystavitele vzdálených certifikátů, které může reverzní proxy důvěřovat. Další informace najdete v tématu [zabezpečené připojení k reverznímu proxy serveru](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, výchozí hodnota je 0|Static|MinReplicaSetSize pro BackupRestoreService |
|PlacementConstraints|řetězec, výchozí hodnota je ""|Static|    Služba PlacementConstraints for BackupRestore |
|SecretEncryptionCertThumbprint|řetězec, výchozí hodnota je ""|Dynamická|Kryptografický otisk certifikátu šifrovaného šifrovacího klíče x509 |
|SecretEncryptionCertX509StoreName|řetězec, doporučená hodnota je my (žádná výchozí) |    Dynamická|    Určuje certifikát, který se má použít pro šifrování a dešifrování přihlašovacích údajů s názvem úložiště certifikátů X. 509, který se používá k šifrování šifrovacích přihlašovacích údajů, které používá služba obnovení záloh. |
|TargetReplicaSetSize|int, výchozí hodnota je 0|Static| TargetReplicaSetSize pro BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | Logická hodnota, výchozí hodnota je false. |Dynamická|Zda jsou povoleny vlastní zásady pro řazení upgradu. Tato funkce se používá k provedení 2 fáze upgradu. Service Fabric 6,5 přidává podporu pro zadání zásad řazení pro domény upgradu během upgradů clusteru nebo aplikací. Podporované zásady jsou numeric, lexicographical, ReverseNumeric a ReverseLexicographical. Výchozí hodnota je číslo. Aby bylo možné tuto funkci používat, nastavení manifestu clusteru ClusterManager/AllowCustomUpgradeSortPolicies musí být nastavené na hodnotu true jako druhý krok upgradu konfigurace po dokončení upgradu kódu SF 6,5. Je důležité, aby se provedlo ve dvou fázích, jinak se upgrade kódu může zaměňovat o pořadí upgradu během prvního upgradu.|
|EnableDefaultServicesUpgrade | Logická hodnota, výchozí hodnota je false. |Dynamická|Povolit upgrade výchozích služeb během upgradu aplikace. Výchozí popisy služeb by se po upgradu přepsaly. |
|FabricUpgradeHealthCheckInterval |Čas v sekundách, výchozí hodnota je 60. |Dynamická|Frekvence kontroly stavu během monitorovaného upgradu prostředků infrastruktury |
|FabricUpgradeStatusPollInterval |Čas v sekundách, výchozí hodnota je 60. |Dynamická|Frekvence cyklického dotazování na stav upgradu prostředků infrastruktury. Tato hodnota určuje rychlost aktualizace pro libovolný GetFabricUpgradeProgress hovor. |
|ImageBuilderTimeoutBuffer |Čas v sekundách, výchozí hodnota je 3. |Dynamická|Zadejte časový interval v sekundách. Doba, po kterou se má v případě, že se má v případě, že se mají v nástroji pro image s časovým limitem Pokud je tato vyrovnávací paměť příliš malá; pak časový limit klienta vyprší před serverem a získá obecnou chybu časového limitu. |
|InfrastructureTaskHealthCheckRetryTimeout | Čas v sekundách, výchozí hodnota je 60. |Dynamická|Zadejte časový interval v sekundách. Doba, po kterou se má při následném zpracování úlohy infrastruktury vyhradit neúspěšné kontroly stavu Při sledování předané kontroly stavu dojde k resetování tohoto časovače. |
|InfrastructureTaskHealthCheckStableDuration | Čas v sekundách, výchozí hodnota je 0.|Dynamická| Zadejte časový interval v sekundách. Doba, po kterou se po úspěšném dokončení následného zpracování úlohy infrastruktury dopustí úspěšné kontroly stavu Při pozorování neúspěšné kontroly stavu dojde k resetování tohoto časovače. |
|InfrastructureTaskHealthCheckWaitDuration |Čas v sekundách, výchozí hodnota je 0.|Dynamická| Zadejte časový interval v sekundách. Doba, po kterou se má čekat před spuštěním kontrol stavu po následném zpracování úlohy infrastruktury |
|InfrastructureTaskProcessingInterval | Čas v sekundách, výchozí hodnota je 10. |Dynamická|Zadejte časový interval v sekundách. Interval zpracování, který využíval Stavový počítač pro zpracování úloh infrastruktury. |
|MaxCommunicationTimeout |Čas v sekundách, výchozí hodnota je 600. |Dynamická|Zadejte časový interval v sekundách. Maximální časový limit pro interní komunikaci mezi ClusterManager a dalšími systémovými službami (tj.) Naming Service; Správce převzetí služeb při selhání a atd.). Tento časový limit by měl být menší než globální MaxOperationTimeout (protože může existovat více komunikací mezi součástmi systému pro každou operaci klienta). |
|MaxDataMigrationTimeout |Čas v sekundách, výchozí hodnota je 600. |Dynamická|Zadejte časový interval v sekundách. Maximální časový limit pro operace obnovení migrace dat po uskutečnění upgradu prostředků infrastruktury |
|MaxOperationRetryDelay |Čas v sekundách, výchozí hodnota je 5.|Dynamická| Zadejte časový interval v sekundách. Maximální zpoždění pro interní opakování při zjištění selhání |
|MaxOperationTimeout |Čas v sekundách, výchozí hodnota je MaxValue. |Dynamická| Zadejte časový interval v sekundách. Maximální globální časový limit pro operace interního zpracování v ClusterManager. |
|MaxTimeoutRetryBuffer | Čas v sekundách, výchozí hodnota je 600. |Dynamická|Zadejte časový interval v sekundách. Maximální časový limit operace při interním pokusu o opakování z důvodu vypršení časového limitu `<Original Time out> + <MaxTimeoutRetryBuffer>` . Další časový limit je přidaný v přírůstcích MinOperationTimeout. |
|MinOperationTimeout | Čas v sekundách, výchozí hodnota je 60. |Dynamická|Zadejte časový interval v sekundách. Minimální globální časový limit pro operace interního zpracování v ClusterManager. |
|MinReplicaSetSize |Int, výchozí hodnota je 3 |Nepovolené|MinReplicaSetSize pro ClusterManager. |
|PlacementConstraints | řetězec, výchozí hodnota je "" |Nepovolené|PlacementConstraints pro ClusterManager. |
|QuorumLossWaitDuration |Čas v sekundách, výchozí hodnota je MaxValue. |Nepovolené| Zadejte časový interval v sekundách. QuorumLossWaitDuration pro ClusterManager. |
|ReplicaRestartWaitDuration |Čas v sekundách, výchozí hodnota je (60,0 \* 30)|Nepovolené|Zadejte časový interval v sekundách. ReplicaRestartWaitDuration pro ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Čas v sekundách, výchozí hodnota je 1200. |Dynamická| Zadejte časový interval v sekundách. Pokud je ReplicaSetCheckTimeout nastaveno na maximální hodnotu DWORD; pak je přepsána hodnotou této konfigurace pro účely vrácení zpět. Hodnota použitá pro přeposílání není nikdy přepsána. |
|SkipRollbackUpdateDefaultService | Logická hodnota, výchozí hodnota je false. |Dynamická|CM při vrácení aktualizace upgradu aplikace přeskočí původní výchozí služby. |
|StandByReplicaKeepDuration | Čas v sekundách, výchozí hodnota je (3600,0 \* 2)|Nepovolené|Zadejte časový interval v sekundách. StandByReplicaKeepDuration pro ClusterManager. |
|TargetReplicaSetSize |Int, výchozí hodnota je 7 |Nepovolené|TargetReplicaSetSize pro ClusterManager. |
|UpgradeHealthCheckInterval |Čas v sekundách, výchozí hodnota je 60. |Dynamická|Frekvence kontroly stavu během monitorovaných upgradů aplikace |
|UpgradeStatusPollInterval |Čas v sekundách, výchozí hodnota je 60. |Dynamická|Frekvence cyklického dotazování na stav upgradu aplikace. Tato hodnota určuje rychlost aktualizace pro libovolný GetApplicationUpgradeProgress hovor. |
|CompleteClientRequest | Logická hodnota, výchozí hodnota je false. |Dynamická| Dokončete požadavek klienta, když ho akceptuje CM. |

## <a name="common"></a>Společné

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Čas v sekundách, výchozí hodnota je 1. |Dynamická|Zadejte časový interval v sekundách. Interval sledování výkonu Nastavení na hodnotu 0 nebo záporná hodnota zakáže monitorování. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, výchozí hodnota je None|Dynamická|Určuje, že defragmentace zásad následuje po vyprázdnění uzlů. U dané metriky 0 znamená, že SF by se měl pokusit defragmentovat uzly rovnoměrně napříč UDs a doménami selhání; 1 znamená, že uzly musí být defragmentované. |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, výchozí hodnota je None|Dynamická|Určuje sadu metrik, která se má použít pro defragmentaci, a ne pro vyrovnávání zatížení. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, výchozí hodnota je None|Dynamická|Určuje počet bezplatných uzlů, které jsou potřeba k tomu, aby bylo možné cluster defragmentovat, a to zadáním procenta v rozsahu [0,0-1,0] nebo počtu prázdných uzlů jako číslo >= 1,0. |

## <a name="diagnostics"></a>Diagnostika

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Logická hodnota, výchozí hodnota je true. | Dynamická | Vylučte požadavky HTTP, které nemají vliv na stav clusteru z auditování. Aktuálně jsou vyloučeny pouze požadavky typu "GET"; ale tato změna se může změnit. |
|AppDiagnosticStoreAccessRequiresImpersonation |Logická hodnota, výchozí hodnota je true. | Dynamická |Bez ohledu na to, jestli je při přístupu k diagnostickým úložištím jménem aplikace vyžadováno zosobnění. |
|AppEtwTraceDeletionAgeInDays |Int, výchozí hodnota je 3 | Dynamická |Počet dní, po které odstraníme staré soubory ETL, které obsahují trasování ETW aplikace |
|ApplicationLogsFormatVersion |Int, výchozí hodnota je 0 | Dynamická |Verze pro formát protokolů aplikací Podporovány jsou hodnoty 0 a 1. Verze 1 obsahuje více polí z záznamu události ETW než verze 0. |
|AuditHttpRequests |Logická hodnota, výchozí hodnota je false. | Dynamická | Zapnout nebo vypnout auditování protokolu HTTP. Účelem auditování je zobrazit aktivity, které byly provedeny v clusteru. včetně toho, kdo žádost inicioval. Všimněte si, že se jedná o osvědčené protokolování; a mohlo by dojít ke ztrátě trasování. Požadavky HTTP s ověřováním "uživatel" nejsou zaznamenávány. |
|CaptureHttpTelemetry|Logická hodnota, výchozí hodnota je true. | Dynamická | Zapnutí nebo vypnutí telemetrie HTTP Účelem telemetrie je Service Fabric, aby bylo možné zachytit data telemetrie, abyste mohli naplánovat budoucí práci a identifikovat problematické oblasti. Telemetrie nezaznamenává žádná osobní data ani tělo žádosti. Telemetrie zachycuje všechny požadavky HTTP, pokud není nakonfigurované jinak. |
|ClusterId |Řetězec | Dynamická |Jedinečné ID clusteru Tato funkce je generována při vytvoření clusteru. |
|ConsumerInstances |Řetězec | Dynamická |Seznam instancí příjemce DCA |
|DiskFullSafetySpaceInMB |Int, výchozí hodnota je 1024 | Dynamická |Zbývající místo na disku v MB pro ochranu před použitím DCA. |
|EnableCircularTraceSession |Logická hodnota, výchozí hodnota je false. | Static |Příznak označuje, zda by měly být použity cyklické relace trasování. |
|EnablePlatformEventsFileSink |Logická hodnota, výchozí hodnota je false. | Static |Povolit nebo zakázat zapsané události platformy na disk |
|EnableTelemetry |Logická hodnota, výchozí hodnota je true. | Dynamická |Tím se povolí nebo zakáže telemetrie. |
|FailuresOnlyHttpTelemetry | Logická hodnota, výchozí hodnota je false. | Dynamická | Pokud je povoleno zachycení telemetrie HTTP; zachytávání pouze neúspěšných žádostí To vám umožní snížit počet událostí vygenerovaných pro telemetrii. |
|HttpTelemetryCapturePercentage | int, výchozí hodnota je 50 | Dynamická | Pokud je povoleno zachycení telemetrie HTTP; zachytit jenom náhodné procento požadavků. To vám umožní snížit počet událostí vygenerovaných pro telemetrii. |
|MaxDiskQuotaInMB |Int, výchozí hodnota je 65536 | Dynamická |Disková kvóta v MB pro soubory protokolu prostředků infrastruktury Windows a Linux. |
|ProducerInstances |Řetězec | Dynamická |Seznam instancí výrobců DCA. |

## <a name="dnsservice"></a>Služba DNSservice
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|logická hodnota, výchozí hodnota je FALSE.|Static|Příznak pro povolení podpory pro dotazy DNS pro dělené služby. Tato funkce je ve výchozím nastavení vypnutá. Další informace najdete v tématu [Service Fabric služby DNS.](service-fabric-dnsservice.md)|
|InstanceCount|int, výchozí hodnota je-1.|Static|Výchozí hodnota je-1, což znamená, že služba DNSservice běží na všech uzlech. OneBox musí být nastavené na hodnotu 1, protože služba DNSservice používá dobře známý port 53, takže nemůže mít ve stejném počítači více instancí.|
|IsEnabled|logická hodnota, výchozí hodnota je FALSE.|Static|Povolí nebo zakáže služba DNSservice. Služba DNSservice je ve výchozím nastavení zakázaná a tato konfigurace musí být nastavená tak, aby se povolila. |
|PartitionPrefix|řetězec, výchozí hodnota je "--"|Static|Určuje řetězcovou hodnotu předpony oddílu v dotazech DNS pro dělené služby. Hodnota: <ul><li>By měl být kompatibilní se specifikací RFC, protože bude součástí dotazu DNS.</li><li>Nesmí obsahovat tečku (.), protože tečka má vliv na chování přípony DNS.</li><li>By neměl být delší než 5 znaků.</li><li>Nemůže být prázdný řetězec.</li><li>Pokud je nastavení PartitionPrefix přepsáno, pak PartitionSuffix musí být přepsáno a naopak.</li></ul>Další informace najdete v tématu [Service Fabric služby DNS.](service-fabric-dnsservice.md)|
|PartitionSuffix|řetězec, výchozí hodnota je ""|Static|Určuje hodnotu řetězce přípony oddílu v dotazech DNS pro dělené služby. Hodnota: <ul><li>By měl být kompatibilní se specifikací RFC, protože bude součástí dotazu DNS.</li><li>Nesmí obsahovat tečku (.), protože tečka má vliv na chování přípony DNS.</li><li>By neměl být delší než 5 znaků.</li><li>Pokud je nastavení PartitionPrefix přepsáno, pak PartitionSuffix musí být přepsáno a naopak.</li></ul>Další informace najdete v tématu [Service Fabric služby DNS.](service-fabric-dnsservice.md) |
|RetryTransientFabricErrors|Logická hodnota, výchozí hodnota je true.|Static|Nastavení řídí možnosti opakování při volání rozhraní Service Fabric API z služba DNSservice. Pokud je tato možnost povolená, opakuje se až třikrát, pokud dojde k přechodné chybě.|

## <a name="eventstoreservice"></a>EventStoreService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, výchozí hodnota je 0|Static|Služba MinReplicaSetSize for Eventstoru |
|PlacementConstraints|řetězec, výchozí hodnota je ""|Static|    Služba PlacementConstraints for Eventstoru |
|TargetReplicaSetSize|int, výchozí hodnota je 0|Static| Služba TargetReplicaSetSize for Eventstoru |

## <a name="fabricclient"></a>FabricClient

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Čas v sekundách, výchozí hodnota je 2 |Dynamická|Zadejte časový interval v sekundách. Interval časového limitu připojení pro pokaždé, když se klient pokusí otevřít připojení k bráně.|
|HealthOperationTimeout |Čas v sekundách, výchozí hodnota je 120. |Dynamická|Zadejte časový interval v sekundách. Časový limit pro zprávu sestavy odeslanou správci stavu. |
|HealthReportRetrySendInterval |Čas v sekundách, výchozí hodnota je 30, minimum je 1. |Dynamická|Zadejte časový interval v sekundách. Interval, ve kterém komponenta pro vytváření sestav znovu odesílá shromážděné zprávy o stavu správci stavu. |
|HealthReportSendInterval |Čas v sekundách, výchozí hodnota je 30. |Dynamická|Zadejte časový interval v sekundách. Interval, ve kterém komponenta pro vytváření sestav odesílá shromážděné zprávy o stavu správci stavu. |
|KeepAliveIntervalInSeconds |Int, výchozí hodnota je 20 |Static|Interval, ve kterém přenos FabricClient odesílá do brány zprávy Keep-Alive. Pro 0; Udržení naživu je zakázané. Musí být kladná hodnota. |
|MaxFileSenderThreads |Uint, výchozí hodnota je 10. |Static|Maximální počet souborů, které jsou přenášeny paralelně. |
|NodeAddresses |řetězec, výchozí hodnota je "" |Static|Kolekce adres (připojovacích řetězců) na různých uzlech, které lze použít ke komunikaci s Naming Service. Zpočátku se klient připojí a vybere jednu z adres náhodně. Je-li zadán více než jeden připojovací řetězec a připojení selže z důvodu chyby komunikace nebo vypršení časového limitu; Klient přepne na použití další adresy postupně. Podrobnosti o sémantikě opakování najdete v části Naming Service adresy pro opakování. |
|PartitionLocationCacheLimit |Int, výchozí hodnota je 100000 |Static|Počet oddílů v mezipaměti pro účely překladu služby (nastavte na 0 bez omezení). |
|RetryBackoffInterval |Čas v sekundách, výchozí hodnota je 3. |Dynamická|Zadejte časový interval v sekundách. Back-off interval před opakováním operace. |
|ServiceChangePollInterval |Čas v sekundách, výchozí hodnota je 120. |Dynamická|Zadejte časový interval v sekundách. Interval mezi po sobě jdoucími dotazy na změny služby od klienta k bráně pro zpětná volání oznámení změn služby. |

## <a name="fabrichost"></a>Hostitele fabrichost vrátilo

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, výchozí hodnota je 10. |Dynamická|Toto je maximální počet, který systém před tím, než se pokusí o aktivaci, selže. |
|ActivationMaxRetryInterval |Čas v sekundách, výchozí hodnota je 300. |Dynamická|Zadejte časový interval v sekundách. Maximální interval opakování pro aktivaci Při každé nepřetržité chybě se interval opakování počítá jako min (ActivationMaxRetryInterval; Počet nepřetržitých selhání * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Čas v sekundách, výchozí hodnota je 5. |Dynamická|Zadejte časový interval v sekundách. Omezení rychlosti interval při každém selhání aktivace; při každé chybě průběžné aktivace provede systém aktivaci znovu pro MaxActivationFailureCount. Interval opakování při každém pokusu je produktem selhání průběžné aktivace a interval back-v aktivace. |
|EnableRestartManagement |Logická hodnota, výchozí hodnota je false. |Dynamická|Tím se povolí restart serveru. |
|EnableServiceFabricAutomaticUpdates |Logická hodnota, výchozí hodnota je false. |Dynamická|Slouží k povolení automatické aktualizace prostředků infrastruktury prostřednictvím web Windows Update. |
|EnableServiceFabricBaseUpgrade |Logická hodnota, výchozí hodnota je false. |Dynamická|Slouží k povolení základní aktualizace pro server. |
|FailureReportingExpeditedReportingIntervalEnabled | Logická hodnota, výchozí hodnota je true. | Static | Umožňuje rychlejší nahrávání rychlostí v DCA, pokud je hostitele fabrichost vrátilo v režimu generování sestav selhání. |
|FailureReportingTimeout | Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (60) | Static |Zadejte časový interval v sekundách. Časový limit pro zasílání zpráv o chybách DCA v případě, že hostitele fabrichost vrátilo narazí na počáteční fázi selhání při spuštění. | 
|RunDCAOnStartupFailure | Logická hodnota, výchozí hodnota je true. | Static |Určuje, zda se má spustit DCA pro nahrání protokolů v případě problémů se spouštěním v hostitele fabrichost vrátilo. | 
|Čas spuštění |Čas v sekundách, výchozí hodnota je 300. |Dynamická|Zadejte časový interval v sekundách. Vypršel časový limit pro fabricactivationmanager spuštění. |
|StopTimeout |Čas v sekundách, výchozí hodnota je 300. |Dynamická|Zadejte časový interval v sekundách. Časový limit pro aktivaci hostované služby; deaktivace a upgrade. |

## <a name="fabricnode"></a>FabricNode

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |řetězec, výchozí hodnota je "FindByThumbprint" |Dynamická|Určuje, jak vyhledat certifikát v úložišti zadaném hodnotou ClientAuthX509StoreName Supported: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |řetězec, výchozí hodnota je "" | Dynamická|Hodnota vyhledávacího filtru, která se používá k vyhledání certifikátu pro výchozí roli správce FabricClient. |
|ClientAuthX509FindValueSecondary |řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru, která se používá k vyhledání certifikátu pro výchozí roli správce FabricClient. |
|ClientAuthX509StoreName |řetězec, výchozí hodnota je my |Dynamická|Název úložiště certifikátů X. 509, které obsahuje certifikát pro výchozí roli správce FabricClient. |
|ClusterX509FindType |řetězec, výchozí hodnota je "FindByThumbprint" |Dynamická|Určuje, jak se má vyhledat certifikát clusteru v úložišti zadaném pomocí ClusterX509StoreName podporovaných hodnot: "FindByThumbprint"; "FindBySubjectName" s "FindBySubjectName"; Pokud existuje více shod; použije se tu, která má nejdálení vypršení platnosti. |
|ClusterX509FindValue |řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru, která se používá k vyhledání certifikátu clusteru |
|ClusterX509FindValueSecondary |řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru, která se používá k vyhledání certifikátu clusteru |
|ClusterX509StoreName |řetězec, výchozí hodnota je my |Dynamická|Název úložiště certifikátů X. 509, které obsahuje certifikát clusteru pro zabezpečení komunikace uvnitř clusteru. |
|EndApplicationPortRange |Int, výchozí hodnota je 0 |Static|Konec (No včetně) portů aplikace, které spravuje hostující podsystém. Požadováno v případě, že EndpointFilteringEnabled má hodnotu true v hostování. |
|ServerAuthX509FindType |řetězec, výchozí hodnota je "FindByThumbprint" |Dynamická|Určuje, jak vyhledat certifikát serveru v úložišti určeném hodnotou ServerAuthX509StoreName Supported: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru, která se používá k vyhledání certifikátu serveru |
|ServerAuthX509FindValueSecondary |řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru, která se používá k vyhledání certifikátu serveru |
|ServerAuthX509StoreName |řetězec, výchozí hodnota je my |Dynamická|Název úložiště certifikátů X. 509, které obsahuje certifikát serveru pro službu bezbolestný. |
|StartApplicationPortRange |Int, výchozí hodnota je 0 |Static|Začátek portů aplikace, které spravuje hostující podsystém. Požadováno v případě, že EndpointFilteringEnabled má hodnotu true v hostování. |
|StateTraceInterval |Čas v sekundách, výchozí hodnota je 300. |Static|Zadejte časový interval v sekundách. Interval pro trasování stavu uzlu v každém uzlu a v uzlech na FMM na FM/. |
|UserRoleClientX509FindType |řetězec, výchozí hodnota je "FindByThumbprint" |Dynamická|Určuje, jak vyhledat certifikát v úložišti zadaném hodnotou UserRoleClientX509StoreName Supported: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru, která se používá k vyhledání certifikátu pro výchozí roli uživatele FabricClient |
|UserRoleClientX509FindValueSecondary |řetězec, výchozí hodnota je "" |Dynamická|Hodnota vyhledávacího filtru, která se používá k vyhledání certifikátu pro výchozí roli uživatele FabricClient |
|UserRoleClientX509StoreName |řetězec, výchozí hodnota je my |Dynamická|Název úložiště certifikátů X. 509, které obsahuje certifikát pro výchozí roli uživatele FabricClient. |

## <a name="failovermanager"></a>FailoverManager

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Logická hodnota, výchozí hodnota je FALSE. |Dynamická|Příznak označující, zda je povoleno odebrat stav uzlu pro počáteční uzel |
|BuildReplicaTimeLimit|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (3600)|Dynamická|Zadejte časový interval v sekundách. Časový limit pro vytvoření stavové repliky; po které se bude iniciovat zpráva o stavu upozornění |
|ClusterPauseThreshold|int, výchozí hodnota je 1.|Dynamická|Pokud počet uzlů v systému přechází pod tuto hodnotu, pak na umístění; Vyrovnávání zatížení; a převzetí služeb při selhání se zastavilo. |
|CreateInstanceTimeLimit|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (300)|Dynamická|Zadejte časový interval v sekundách. Časový limit pro vytvoření bezstavové instance; po které se bude iniciovat zpráva o stavu upozornění |
|ExpectedClusterSize|int, výchozí hodnota je 1.|Dynamická|Při počátečním spuštění clusteru; FM bude čekat na to, než se před zahájením umístění dalších služeb zahlásí tento počet uzlů. včetně systémových služeb, jako je pojmenování. Zvýšení hodnoty zvyšuje čas potřebný ke spuštění clusteru. ale zabrání nadměrnému zatížení uzlů a také dalším přesunům, které budou nezbytné k tomu, aby další uzly byly online. Tato hodnota by měla být obecně nastavená na malý zlomek počáteční velikosti clusteru. |
|ExpectedNodeDeactivationDuration|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (60.0 \* 30)|Dynamická|Zadejte časový interval v sekundách. Toto je očekávaná doba, po kterou se uzel dokončí deaktivací. |
|ExpectedNodeFabricUpgradeDuration|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (60.0 \* 30)|Dynamická|Zadejte časový interval v sekundách. Toto je očekávaná doba trvání uzlu, který se má upgradovat během Windows Fabric upgradu. |
|ExpectedReplicaUpgradeDuration|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (60.0 \* 30)|Dynamická|Zadejte časový interval v sekundách. Toto je očekávaná doba trvání pro všechny repliky, které se mají upgradovat na uzlu během upgradu aplikace. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|logická hodnota, výchozí hodnota je TRUE.|Dynamická|Pokud je nastaveno na true; repliky s cílovou sadou replik s velikostí 1 se budou moct během upgradu přesunout. |
|MinReplicaSetSize|int, výchozí hodnota je 3|Nepovolené|Toto je minimální velikost sady replik pro FM. Pokud počet aktivních replik FM klesne pod tuto hodnotu; v případě, že dojde k obnovení nejméně minimálního počtu replik, bude FM zamítnout změny do clusteru. |
|PlacementConstraints|řetězec, výchozí hodnota je ""|Nepovolené|Všechna omezení umístění pro repliky správce převzetí služeb při selhání |
|PlacementTimeLimit|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (600)|Dynamická|Zadejte časový interval v sekundách. Časový limit pro dosažení počtu cílových replik; po které se bude iniciovat zpráva o stavu upozornění |
|QuorumLossWaitDuration |Čas v sekundách, výchozí hodnota je MaxValue. |Dynamická|Zadejte časový interval v sekundách. Toto je maximální doba, po kterou je možné, aby oddíl byl ve stavu ztráty kvora. Pokud je oddíl stále ve ztrátě kvora po uplynutí této doby; oddíl se obnoví ze ztráty kvora tím, že se posuzuje o ztracené repliky. Všimněte si, že to může způsobit ztrátu dat. |
|ReconfigurationTimeLimit|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (300)|Dynamická|Zadejte časový interval v sekundách. Časový limit pro rekonfiguraci; po které se bude iniciovat zpráva o stavu upozornění |
|ReplicaRestartWaitDuration|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (60.0 \* 30)|Nepovolené|Zadejte časový interval v sekundách. Toto je ReplicaRestartWaitDuration pro FMService. |
| SeedNodeQuorumAdditionalBufferNodes | int, výchozí hodnota je 0 | Dynamická | Vyrovnávací paměť počátečních uzlů, které musí být (společně s kvorem uzlů počátečních), by mělo umožňovat, aby bylo možné přejít maximálně o (totalNumSeedNodes-(seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)) uzlů. |
|StandByReplicaKeepDuration|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (3600.0 \* 24 \* 7)|Nepovolené|Zadejte časový interval v sekundách. Toto je StandByReplicaKeepDuration pro FMService. |
|TargetReplicaSetSize|int, výchozí hodnota je 7|Nepovolené|Toto je cílový počet replik FM, které se Windows Fabric zachovají. Vyšší hodnota má za následek vyšší spolehlivost dat FM; s malými kompromisy ve výkonu. |
|UserMaxStandByReplicaCount |Int, výchozí hodnota je 1. |Dynamická|Výchozí maximální počet replik v pohotovostním stavu, které systém udržuje pro uživatelské služby. |
|UserReplicaRestartWaitDuration |Čas v sekundách, výchozí hodnota je 60,0 \* 30. |Dynamická|Zadejte časový interval v sekundách. Dojde-li k výpadku trvalé repliky; Windows Fabric čeká na tuto dobu, než se replika před vytvořením nových náhradních replik (která by vyžadovala kopii stavu). |
|UserStandByReplicaKeepDuration |Čas v sekundách, výchozí hodnota je 3600,0 \* 24 \* 7. |Dynamická|Zadejte časový interval v sekundách. Když se trvalá replika vrátí ze stavu mimo provoz; je možné, že již byla nahrazena. Tento časovač určuje, jak dlouho bude FM uchovávat pohotovostní repliku před zahozením. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, výchozí hodnota je 604800 |Static| To je přibližně doba, po kterou je možné zachovat akce, které jsou ve stavu terminálu. To závisí také na StoredActionCleanupIntervalInSeconds; vzhledem k tomu, že práce na čištění je provedena pouze v tomto intervalu. 604800 je 7 dní. |
|DataLossCheckPollIntervalInSeconds|int, výchozí hodnota je 5|Static|Jedná se o čas mezi kontrolami, které systém provádí při čekání na ztrátu dat. Počet, kolikrát bude číslo ztráty dat kontrolováno na interní iteraci, je DataLossCheckWaitDurationInSeconds/this. |
|DataLossCheckWaitDurationInSeconds|int, výchozí hodnota je 25.|Static|Celkové množství času; v sekundách; systém bude čekat na ztrátu dat. Tato metoda se používá interně při volání rozhraní API StartPartitionDataLossAsync (). |
|MinReplicaSetSize |Int, výchozí hodnota je 0 |Static|MinReplicaSetSize pro FaultAnalysisService. |
|PlacementConstraints | řetězec, výchozí hodnota je ""|Static| PlacementConstraints pro FaultAnalysisService. |
|QuorumLossWaitDuration | Čas v sekundách, výchozí hodnota je MaxValue. |Static|Zadejte časový interval v sekundách. QuorumLossWaitDuration pro FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, výchozí hodnota je 600|Static|Tento parametr se používá v případě, že je voláno rozhraní API pro ztrátu dat. Určuje, jak dlouho bude systém čekat na vyřazení repliky po jejím interním vyvolání odebrání repliky. |
|ReplicaRestartWaitDuration |Čas v sekundách, výchozí hodnota je 60 minut.|Static|Zadejte časový interval v sekundách. ReplicaRestartWaitDuration pro FaultAnalysisService. |
|StandByReplicaKeepDuration| Čas v sekundách, výchozí hodnota je (60 *24* 7) min. |Static|Zadejte časový interval v sekundách. StandByReplicaKeepDuration pro FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, výchozí hodnota je 3600 |Static|To je postup, jak často se bude obchod vyčistit. Pouze akce ve stavu terminálu; a které byly dokončeny alespoň před CompletedActionKeepDurationInSeconds, budou odebrány. |
|StoredChaosEventCleanupIntervalInSeconds | Int, výchozí hodnota je 3600 |Static|To je postup, jak často bude úložiště auditováno pro vyčištění. Pokud je počet událostí vyšší než 30000; Vyčištění bude vykázat. |
|TargetReplicaSetSize |Int, výchozí hodnota je 0 |Static|NOT_PLATFORM_UNIX_START TargetReplicaSetSize pro FaultAnalysisService. |

## <a name="federation"></a>metadata

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|LeaseDuration |Čas v sekundách, výchozí hodnota je 30. |Dynamická|Doba, po kterou zapůjčení trvá mezi uzlem a jeho sousedními okruhy. |
|LeaseDurationAcrossFaultDomain |Čas v sekundách, výchozí hodnota je 30. |Dynamická|Doba, po kterou zapůjčení trvá mezi uzlem a jeho sousedními uzly napříč doménami selhání. |

## <a name="filestoreservice"></a>FileStoreService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Logická hodnota, výchozí hodnota je TRUE.|Dynamická|Konfigurace k určení, zda služba File Store akceptuje nahrávání souborů na základě bloku nebo ne během kopírování balíčku aplikace. |
|AnonymousAccessEnabled | Logická hodnota, výchozí hodnota je true. |Static|Povolí nebo zakáže anonymní přístup ke sdíleným složkám FileStoreService. |
|CommonName1Ntlmx509CommonName|řetězec, výchozí hodnota je ""|Static| Běžný název certifikátu x509, který se používá k vygenerování HMAC u CommonName1NtlmPasswordSecret při použití ověřování NTLM |
|CommonName1Ntlmx509StoreLocation|řetězec, výchozí hodnota je "LocalMachine"|Static|Umístění úložiště certifikátu x509 používaného k vygenerování HMAC u CommonName1NtlmPasswordSecret při použití ověřování NTLM |
|CommonName1Ntlmx509StoreName|řetězec, výchozí hodnota je MY| Static|Název úložiště certifikátu x509, který se používá k vygenerování HMAC u CommonName1NtlmPasswordSecret při použití ověřování NTLM |
|CommonName2Ntlmx509CommonName|řetězec, výchozí hodnota je ""|Static|Běžný název certifikátu x509, který se používá k vygenerování HMAC u CommonName2NtlmPasswordSecret při použití ověřování NTLM |
|CommonName2Ntlmx509StoreLocation|řetězec, výchozí hodnota je "LocalMachine"| Static|Umístění úložiště certifikátu x509 používaného k vygenerování HMAC u CommonName2NtlmPasswordSecret při použití ověřování NTLM |
|CommonName2Ntlmx509StoreName|řetězec, výchozí hodnota je MY|Static| Název úložiště certifikátu x509, který se používá k vygenerování HMAC u CommonName2NtlmPasswordSecret při použití ověřování NTLM |
|CommonNameNtlmPasswordSecret|SecureString, výchozí nastavení je běžné:: SecureString ("")| Static|Tajný klíč hesla, který se používá jako základ pro generování stejného hesla při použití ověřování NTLM |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |Časový interval, výchozí hodnota je common:: TimeSpan:: FromMinutes (5)|Dynamická|Zadejte časový interval v sekundách. Časový interval mezi kontrolou místa na disku pro hlášení stavu události, když se na disku blíží volné místo. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |Časový interval, výchozí hodnota je common:: TimeSpan:: FromMinutes (15)|Dynamická|Zadejte časový interval v sekundách. Časový interval mezi kontrolou místa na disku pro hlášení stavu události, když je na disku dostatek místa. |
|EnableImageStoreHealthReporting |logická hodnota, výchozí hodnota je TRUE.    |Static|Konfigurace k určení, zda by služba úložiště souborů měla hlásit svůj stav. |
|FreeDiskSpaceNotificationSizeInKB|Int64, výchozí hodnota je 25 \* 1024 |Dynamická|Velikost volného místa na disku, pod kterým může dojít k upozornění na stav. Minimální hodnota této konfigurace a FreeDiskSpaceNotificationThresholdPercentage config slouží k určení odeslání upozornění na stav. |
|FreeDiskSpaceNotificationThresholdPercentage|Double, výchozí hodnota je 0,02. |Dynamická|Procento volného místa na disku, na kterém se může objevit upozornění na stav. Minimální hodnota této konfigurace a FreeDiskSpaceNotificationInMB config slouží k určení odesílání upozornění na stav. |
|GenerateV1CommonNameAccount| logická hodnota, výchozí hodnota je TRUE.|Static|Určuje, jestli se má vygenerovat účet s algoritmem generace uživatelského jména v1. Počínaje verzí Service Fabric 6,1; je vždy vytvořen účet se generováním v2. Účet v1 je nutný pro upgrady z/na verze, které nepodporují generování v2 (před 6,1).|
|MaxCopyOperationThreads | Uint, výchozí hodnota je 0 |Dynamická| Maximální počet paralelních souborů, které sekundární může kopírovat z primárního. 0 = = počet jader. |
|MaxFileOperationThreads | Uint, výchozí hodnota je 100 |Static| Maximální počet paralelních vláken, která mohou v primárních operacích provádět operace operací (kopírování a přesun). 0 = = počet jader. |
|MaxRequestProcessingThreads | Uint, výchozí hodnota je 200 |Static|Maximální počet paralelních vláken, která mohou zpracovávat požadavky v primárním vlákně. 0 = = počet jader. |
|MaxSecondaryFileCopyFailureThreshold | Uint, výchozí hodnota je 25|Dynamická|Maximální počet opakovaných pokusů o kopírování souborů v sekundárním souboru před tím, než bude proveden. |
|MaxStoreOperations | Uint, výchozí hodnota je 4096 |Static|Maximální počet paralelních operací transakcí úložiště, které jsou povoleny na primárním počítači. 0 = = počet jader. |
|NamingOperationTimeout |Čas v sekundách, výchozí hodnota je 60. |Dynamická|Zadejte časový interval v sekundách. Časový limit pro provedení operace pojmenování. |
|PrimaryAccountNTLMPasswordSecret | SecureString, výchozí hodnota je prázdná. |Static| Tajný klíč hesla, který se používá jako základ pro generování stejného hesla při použití ověřování NTLM. |
|PrimaryAccountNTLMX509StoreLocation | řetězec, výchozí hodnota je "LocalMachine"|Static| Umístění úložiště certifikátu x509 používaného k vygenerování HMAC ve PrimaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|PrimaryAccountNTLMX509StoreName | řetězec, výchozí hodnota je MY|Static| Název úložiště certifikátu x509, který se používá k vygenerování HMAC u PrimaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|PrimaryAccountNTLMX509Thumbprint | řetězec, výchozí hodnota je ""|Static|Kryptografický otisk certifikátu x509, který se používá k vygenerování HMAC u PrimaryAccountNTLMPasswordSecret při použití ověřování NTLM |
|PrimaryAccountType | řetězec, výchozí hodnota je "" |Static|Primární AccountType objektu zabezpečení pro sdílení seznamu FileStoreService. |
|PrimaryAccountUserName | řetězec, výchozí hodnota je "" |Static|Primární uživatelské jméno účtu objektu zabezpečení pro sdílené složky FileStoreService. |
|PrimaryAccountUserPassword | SecureString, výchozí hodnota je prázdná. |Static|Primární heslo účtu objektu zabezpečení k seznamu ACL pro sdílené složky FileStoreService. |
|QueryOperationTimeout | Čas v sekundách, výchozí hodnota je 60. |Dynamická|Zadejte časový interval v sekundách. Časový limit pro provedení operace dotazu. |
|SecondaryAccountNTLMPasswordSecret | SecureString, výchozí hodnota je prázdná. |Static| Tajný klíč hesla, který se používá jako základ pro generování stejného hesla při použití ověřování NTLM. |
|SecondaryAccountNTLMX509StoreLocation | řetězec, výchozí hodnota je "LocalMachine" |Static|Umístění úložiště certifikátu x509 používaného k vygenerování HMAC ve SecondaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|SecondaryAccountNTLMX509StoreName | řetězec, výchozí hodnota je MY |Static|Název úložiště certifikátu x509, který se používá k vygenerování HMAC u SecondaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|SecondaryAccountNTLMX509Thumbprint | řetězec, výchozí hodnota je ""| Static|Kryptografický otisk certifikátu x509, který se používá k vygenerování HMAC u SecondaryAccountNTLMPasswordSecret při použití ověřování NTLM |
|SecondaryAccountType | řetězec, výchozí hodnota je ""|Static| Sekundární AccountType objektu zabezpečení k řízení přístupu ke sdíleným složkám FileStoreService. |
|SecondaryAccountUserName | řetězec, výchozí hodnota je ""| Static|Uživatelské jméno sekundárního účtu objektu zabezpečení pro sdílené složky FileStoreService. |
|SecondaryAccountUserPassword | SecureString, výchozí hodnota je prázdná. |Static|Heslo sekundárního účtu objektu zabezpečení pro sdílené složky FileStoreService. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, výchozí hodnota je 500|Dynamická|Zpoždění při kopírování souboru (v milisekundách)|
|UseChunkContentInTransportMessage|logická hodnota, výchozí hodnota je TRUE.|Dynamická|Příznak pro použití nové verze protokolu nahrání představeného v v 6.4 Tato verze protokolu pomocí Service Fabric transportu odesílá soubory do úložiště imagí, což poskytuje lepší výkon než protokol SMB používaný v předchozích verzích. |

## <a name="healthmanager"></a>HealthManager

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Logická hodnota, výchozí hodnota je false. |Static|Zásady hodnocení stavu clusteru: Povolit pro vyhodnocení stavu podle typu aplikace. |
|MaxSuggestedNumberOfEntityHealthReports|Int, výchozí hodnota je 100 |Dynamická|Maximální počet zpráv o stavu, které může entita mít, než vyvolává obavy o logice vytváření sestav o stavu sledovacího zařízení. Každá entita o stavu by měla mít relativně malý počet sestav o stavu. Pokud počet sestav překročí toto číslo, mohou nastat problémy s implementací sledovacího zařízení. Entita s příliš mnoha sestavami je označena prostřednictvím sestavy stavu upozornění, když je entita vyhodnocena. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Logická hodnota, výchozí hodnota je false. |Static|Zásady hodnocení stavu clusteru: upozornění jsou považována za chyby. |
|MaxPercentUnhealthyApplications | Int, výchozí hodnota je 0 |Static|Zásady hodnocení stavu clusteru: maximální procento aplikací, které nejsou v pořádku, povolené pro cluster v dobrém stavu. |
|MaxPercentUnhealthyNodes | Int, výchozí hodnota je 0 |Static|Zásady hodnocení stavu clusteru: maximální procento uzlů, které nejsou v pořádku, povolené pro cluster v pořádku. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, výchozí hodnota je 10.|Static|Zásady hodnocení stavu upgradu clusteru: maximální procento rozdílových uzlů, které nemají stav v pořádku, aby byl cluster v pořádku |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, výchozí hodnota je 15.|Static|Zásady hodnocení stavu upgradu clusteru: maximální procento rozdílu uzlů, které nejsou v pořádku, v upgradovací doméně povolené pro cluster v dobrém stavu |

## <a name="hosting"></a>Hostování

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Celé číslo, výchozí hodnota je 10. |Dynamická|Počet neúspěšných pokusů o aktivaci systému před tím, než se zahájí |
|ActivationMaxRetryInterval |Čas v sekundách, výchozí hodnota je 300. |Dynamická|Při každé chybě průběžné aktivace se systém znovu pokusí o aktivaci až do ActivationMaxFailureCount. ActivationMaxRetryInterval určuje časový interval čekání před opakováním při každé aktivaci. |
|ActivationRetryBackoffInterval |Čas v sekundách, výchozí hodnota je 5. |Dynamická|Omezení rychlosti interval při každém selhání aktivace; Při každé chybě průběžné aktivace se systém znovu pokusí o aktivaci až do MaxActivationFailureCount. Interval opakování při každém pokusu je produktem selhání průběžné aktivace a interval back-v aktivace. |
|ActivationTimeout| Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (180)|Dynamická| Zadejte časový interval v sekundách. Časový limit pro aktivaci aplikace; deaktivace a upgrade. |
|ApplicationHostCloseTimeout| Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (120)|Dynamická| Zadejte časový interval v sekundách. Když se v automaticky aktivovaných procesech zjistí ukončení prostředků infrastruktury; FabricRuntime zavře všechny repliky v procesu hostitele (ApplicationHost) daného uživatele. Toto je časový limit pro operaci Zavřít. |
| CnsNetworkPluginCnmUrlPort | wstring, výchozí hodnota je L "48080" | Static | Port URL rozhraní API služby Azure CNM |
| CnsNetworkPluginCnsUrlPort | wstring, výchozí hodnota je L "10090" | Static | Port adresy URL služby Azure CNS |
|ContainerServiceArguments|řetězec, výchozí hodnota je "-H localhost: 2375-H npipe://"|Static|Service Fabric (SF) spravuje démona Docker (s výjimkou klientských počítačů s Windows, jako je Win10). Tato konfigurace umožňuje uživateli zadat vlastní argumenty, které by měly být předány do Docker démona při spuštění. Pokud jsou zadány vlastní argumenty, Service Fabric Nepředávat žádný jiný argument stroji Docker s výjimkou argumentu--PidFile. Proto by uživatelé neměli jako součást svých argumentů zákazníků zadávat argument--PidFile. Vlastní argumenty by taky měly zajišťovat, že démon Docker naslouchá na výchozím kanálu názvů v systému Windows (nebo v případě, že se nachází na platformě Linux v systému Linux), aby Service Fabric mohl komunikovat s ním.|
|ContainerServiceLogFileMaxSizeInKb|int, výchozí hodnota je 32768|Static|Maximální velikost souboru protokolu generovaných kontejnery Docker.  Pouze Windows.|
|ContainerImageDownloadTimeout|int, počet sekund, výchozí hodnota je 1200 (20 minut)|Dynamická|Počet sekund před časovým limitem stahování imagí.|
|ContainerImagesToSkip|řetězec, názvy obrázků oddělené znakem svislé čáry, výchozí hodnota je ""|Static|Název jednoho nebo více imagí kontejneru, které by neměly být odstraněny.  Používá se s parametrem PruneContainerImages.|
|ContainerServiceLogFileNamePrefix|řetězec, výchozí hodnota je "sfcontainerlogs"|Static|Předpona názvu souboru pro soubory protokolu generované kontejnery Docker.  Pouze Windows.|
|ContainerServiceLogFileRetentionCount|int, výchozí hodnota je 10.|Static|Počet souborů protokolu generovaných kontejnery Docker před přepsáním souborů protokolu.  Pouze Windows.|
|CreateFabricRuntimeTimeout|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (120)|Dynamická| Zadejte časový interval v sekundách. Hodnota časového limitu pro FabricCreateRuntime volání Sync |
|DefaultContainerRepositoryAccountName|řetězec, výchozí hodnota je ""|Static|Místo přihlašovacích údajů zadaných v ApplicationManifest.xml se použily výchozí přihlašovací údaje. |
|DefaultContainerRepositoryPassword|řetězec, výchozí hodnota je ""|Static|Místo přihlašovacích údajů zadaných v ApplicationManifest.xml se použily výchozí přihlašovací údaje hesla.|
|DefaultContainerRepositoryPasswordType|řetězec, výchozí hodnota je ""|Static|Pokud není prázdný řetězec, hodnota může být "Encrypted" nebo "SecretsStoreRef".|
|DefaultDnsSearchSuffixEmpty|logická hodnota, výchozí hodnota je FALSE.|Static|Ve výchozím nastavení se název služby připojuje k názvu SF DNS pro služby kontejneru. Tato funkce zastaví toto chování, takže ve výchozím nastavení není v cestách pro překlad žádné připojení k názvu DNS SF.|
|DeploymentMaxFailureCount|int, výchozí hodnota je 20| Dynamická|Nasazení aplikace se bude opakovat po DeploymentMaxFailureCount dobu, než dojde k selhání nasazení této aplikace na uzlu.| 
|DeploymentMaxRetryInterval| Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (3600)|Dynamická| Zadejte časový interval v sekundách. Maximální interval opakování pro nasazení Při každé nepřetržité chybě se interval opakování počítá jako min (DeploymentMaxRetryInterval; Počet nepřetržitých selhání * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (10)|Dynamická|Zadejte časový interval v sekundách. Záložní interval pro selhání nasazení. Při každém selhání průběžného nasazování systém zopakuje nasazení až do MaxDeploymentFailureCount. Interval opakování je produktem selhání průběžného nasazování a interval omezení rychlosti nasazení. |
|DisableContainers|logická hodnota, výchozí hodnota je FALSE.|Static|Konfigurace pro zákaz použití kontejnerů místo DisableContainerServiceStartOnContainerActivatorOpen, což je nepoužívané konfigurace |
|DisableDockerRequestRetry|logická hodnota, výchozí hodnota je FALSE. |Dynamická| Ve výchozím nastavení SF komunikuje pomocí příkazu DD (Docker Dameon) s časovým limitem "DockerRequestTimeout" pro každý odeslaný požadavek HTTP. Pokud DD nereaguje v rámci tohoto časového období; SF znovu odešle požadavek, pokud stále zbývá operace na nejvyšší úrovni.  S kontejnerem HyperV; DD někdy vybere mnohem více času, aby kontejner mohl vyvolat nebo deaktivovat. V takových případech DD žádosti vyprší od SF perspektivy a SF opakuje operaci. Někdy se zdá, že přidá větší tlak na DD. Tato konfigurace umožňuje zakázat tento pokus o opakování a počkat na odpověď DD. |
|DnsServerListTwoIps | Logická hodnota, výchozí hodnota je FALSE. | Static | Tyto příznaky přidávají místní server DNS dvakrát, aby bylo možné zmírnit občasné řešení problémů. |
| DockerTerminateOnLastHandleClosed | logická hodnota, výchozí hodnota je FALSE. | Static | Ve výchozím nastavení, pokud hostitele fabrichost vrátilo spravuje dockerd (na základě: SkipDockerProcessManagement = = false), toto nastavení nakonfiguruje, co se stane, když dojde k chybě hostitele fabrichost vrátilo nebo dockerd. Pokud je `true` Tato možnost nastavena na hodnotu v případě, že dojde k vynucenému ukončení všech spuštěných kontejnerů, bude klientovi HCS nuceně ukončen. Pokud je sada nastavena na kontejnery, budou dál fungovat `false` . Poznámka: předchozí až 8,0 Toto chování bylo neúmyslně ekvivalentem `false` . Výchozím nastavením `true` tady je to, co se ve výchozím nastavení stane, že se při restartování těchto procesů projeví pro naši logiku vyčištění. |
| DoNotInjectLocalDnsServer | logická hodnota, výchozí hodnota je FALSE. | Static | Zabraňuje modulu runtime vložit místní IP adresu jako server DNS pro kontejnery. |
|EnableActivateNoWindow| logická hodnota, výchozí hodnota je FALSE.|Dynamická| Aktivovaný proces se vytvoří na pozadí bez konzoly. |
|EnableContainerServiceDebugMode|logická hodnota, výchozí hodnota je TRUE.|Static|Povolí nebo zakáže protokolování kontejnerů Docker.  Pouze Windows.|
|EnableDockerHealthCheckIntegration|logická hodnota, výchozí hodnota je TRUE.|Static|Umožňuje integraci událostí Docker HEALTHCHECK se sestavou stavu systému Service Fabric. |
|EnableProcessDebugging|logická hodnota, výchozí hodnota je FALSE.|Dynamická| Povoluje spouštění hostitelů aplikace v rámci ladicího programu. |
|EndpointProviderEnabled| logická hodnota, výchozí hodnota je FALSE.|Static| Umožňuje správu prostředků koncového bodu podle prostředků infrastruktury. Vyžaduje určení rozsahu portů počáteční a koncové aplikace v FabricNode. |
|FabricContainerAppsEnabled| logická hodnota, výchozí hodnota je FALSE.|Static| |
|FirewallPolicyEnabled|logická hodnota, výchozí hodnota je FALSE.|Static| Umožňuje otevřít porty brány firewall pro prostředky koncového bodu s explicitními porty zadanými v ServiceManifest. |
|GetCodePackageActivationContextTimeout|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (120)|Dynamická|Zadejte časový interval v sekundách. Hodnota časového limitu pro volání CodePackageActivationContext. Neplatí to pro ad hoc služby. |
|GovernOnlyMainMemoryForProcesses|logická hodnota, výchozí hodnota je FALSE.|Static|Výchozím chováním zásad správného řízení prostředků je vložení omezení určeného v MemoryInMB na množství paměti (RAM + swap), které proces používá. Pokud je limit překročen, proces dostane výjimku OutOfMemory. Pokud je tento parametr nastaven na hodnotu true; limit se použije jenom na velikost paměti RAM, kterou bude proces používat. Pokud je tento limit překročen, a pokud je toto nastavení pravdivé; pak operační systém zahodí hlavní paměť na disk. |
|IPProviderEnabled|logická hodnota, výchozí hodnota je FALSE.|Static|Umožňuje správu IP adres. |
|IsDefaultContainerRepositoryPasswordEncrypted|logická hodnota, výchozí hodnota je FALSE.|Static|Určuje, zda je DefaultContainerRepositoryPassword zašifrováno.|
|LinuxExternalExecutablePath|řetězec, výchozí hodnota je "/usr/bin/" |Static|Primární adresář externích spustitelných příkazů na uzlu.|
|NTLMAuthenticationEnabled|logická hodnota, výchozí hodnota je FALSE.|Static| Povoluje podporu pro použití protokolu NTLM balíčky kódu, které jsou spuštěny jako jiní uživatelé, aby procesy napříč počítači mohly komunikovat bezpečně. |
|NTLMAuthenticationPasswordSecret|SecureString, výchozí nastavení je běžné:: SecureString ("")|Static|Je šifrovaný, který slouží ke generování hesla pro uživatele protokolu NTLM. Je nutné nastavit, pokud má NTLMAuthenticationEnabled hodnotu true. Ověřil Nástroj pro nasazení. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|Časový interval, výchozí hodnota je common:: TimeSpan:: FromMinutes (3)|Dynamická|Zadejte časový interval v sekundách. Nastavení specifické pro prostředí – pravidelný interval, při kterém hostování vyhledává nové certifikáty, které se mají použít pro FileStoreService konfiguraci NTLM. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|Časový interval, výchozí hodnota je common:: TimeSpan:: FromMinutes (4)|Dynamická| Zadejte časový interval v sekundách. Časový limit pro konfiguraci uživatelů NTLM pomocí společných názvů certifikátů. Uživatelé protokolu NTLM jsou potřeba pro sdílené složky FileStoreService. |
|PruneContainerImages|logická hodnota, výchozí hodnota je FALSE.|Dynamická| Odebere nepoužívané image kontejneru aplikace z uzlů. Pokud je typu ApplicationType odregistrována v clusteru Service Fabric, bitové kopie kontejneru, které byly použity touto aplikací, budou odebrány na uzlech, které byly staženy pomocí Service Fabric. Vyřazování se spouští každou hodinu, takže může trvat až jednu hodinu (a navíc dobu vyřazení obrázku) pro image, které se mají z clusteru odebrat.<br>Service Fabric nikdy nebude stahovat ani odebírat obrázky, které nesouvisí s aplikací.  Nesouvisející obrázky, které byly staženy ručně nebo jinak, je nutné odebrat explicitně.<br>Obrázky, které by neměly být odstraněny, lze zadat v parametru ContainerImagesToSkip.| 
|RegisterCodePackageHostTimeout|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (120)|Dynamická| Zadejte časový interval v sekundách. Hodnota časového limitu pro FabricRegisterCodePackageHost synchronizační hovor. To platí jenom pro hostitele aplikací balíčku s více kódy, jako je FWP. |
|RequestTimeout|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (30)|Dynamická| Zadejte časový interval v sekundách. To představuje časový limit pro komunikaci mezi hostitelem aplikace uživatele a procesem prostředků infrastruktury pro různé hostující operace, jako je registrace do továrního umístění. Registrace modulu runtime. |
|RunAsPolicyEnabled| logická hodnota, výchozí hodnota je FALSE.|Static| Povoluje spouštění balíčků kódu jako místní uživatel jiný než uživatel, pod kterým je spuštěn proces Fabric. Aby bylo možné povolit tuto zásadu Fabric, musí být spuštěná jako systém nebo uživatel, který má SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (120)|Dynamická|Zadejte časový interval v sekundách. Hodnota časového limitu pro ServiceFactory volání synchronizace (stav bez stavu/stav) |
|ServiceTypeDisableFailureThreshold |Celé číslo, výchozí hodnota je 1. |Dynamická|Toto je prahová hodnota počtu selhání, po kterém se FailoverManager (FM) upozorní na zakázání typu služby v tomto uzlu a pro umístění zkuste použít jiný uzel. |
|ServiceTypeDisableGraceInterval|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (30)|Dynamická|Zadejte časový interval v sekundách. Časový interval, po kterém může být typ služby zakázán |
|ServiceTypeRegistrationTimeout |Čas v sekundách, výchozí hodnota je 300. |Dynamická|Maximální doba, po kterou je ServiceType zaregistrovaný do prostředků infrastruktury |
|UseContainerServiceArguments|logická hodnota, výchozí hodnota je TRUE.|Static|Tato konfigurace oznamuje hostování, aby vynechala předávání argumentů (určených v konfiguračním ContainerServiceArguments) do Docker démon.|

## <a name="httpgateway"></a>HttpGateway

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, výchozí hodnota je 50 |Static| Počet operací čtení, které se mají odeslat do fronty serveru http Tento ovládací prvek řídí počet souběžných požadavků, které může HttpGateway splnit. |
|HttpGatewayHealthReportSendInterval |Čas v sekundách, výchozí hodnota je 30. |Static|Zadejte časový interval v sekundách. Interval, ve kterém brána protokolu HTTP odesílá shromážděné zprávy o stavu správci stavu. |
|HttpStrictTransportSecurityHeader|řetězec, výchozí hodnota je ""|Dynamická| Zadejte hodnotu hlavičky zabezpečení přenosu HTTP Strict, která se má zahrnout do každé odpovědi odeslané HttpGateway. Při nastavení na prázdný řetězec; Tato hlavička nebude obsažena v odpovědi brány.|
|IsEnabled|Logická hodnota, výchozí hodnota je false. |Static| Povolí nebo zakáže HttpGateway. HttpGateway je ve výchozím nastavení zakázaná. |
|MaxEntityBodySize |Uint, výchozí hodnota je 4194304 |Dynamická|Dává maximální velikost textu, který se dá očekávat od požadavku HTTP. Výchozí hodnota je 4 MB. Httpgateway selže požadavek, pokud má tělo velikosti > této hodnotě. Minimální velikost bloku pro čtení je 4096 bajtů. Proto musí být >= 4096. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|Povoleno |Logická hodnota, výchozí hodnota je false. |Static|Příznak Enabled pro ImageStoreService. Výchozí: false |
|MinReplicaSetSize | Int, výchozí hodnota je 3 |Static|MinReplicaSetSize pro ImageStoreService. |
|PlacementConstraints | řetězec, výchozí hodnota je "" |Static| PlacementConstraints pro ImageStoreService. |
|QuorumLossWaitDuration | Čas v sekundách, výchozí hodnota je MaxValue. |Static| Zadejte časový interval v sekundách. QuorumLossWaitDuration pro ImageStoreService. |
|ReplicaRestartWaitDuration | Čas v sekundách, výchozí hodnota je 60,0 \* 30. |Static|Zadejte časový interval v sekundách. ReplicaRestartWaitDuration pro ImageStoreService. |
|StandByReplicaKeepDuration | Čas v sekundách, výchozí hodnota je 3600,0 \* 2 |Static| Zadejte časový interval v sekundách. StandByReplicaKeepDuration pro ImageStoreService. |
|TargetReplicaSetSize | Int, výchozí hodnota je 7 |Static|TargetReplicaSetSize pro ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, výchozí hodnota je 1. |Dynamická|Příznak, který označuje, jestli se má nastavení paměti automaticky a dynamicky nakonfigurovat. Pokud je nula, nastavení konfigurace paměti se používá přímo a nemění se v závislosti na systémových podmínkách. Pokud je nastavení paměti nakonfigurované automaticky a může se změnit v závislosti na systémových podmínkách. |
|MaximumDestagingWriteOutstandingInKB | Int, výchozí hodnota je 0 |Dynamická|Počet KB, které umožní sdílenému protokolu předem vyhradit vyhrazený protokol. Hodnotu 0 použijte k označení bez omezení.
|SharedLogId |řetězec, výchozí hodnota je "" |Static|Jedinečný identifikátor GUID pro sdílený kontejner protokolu Použijte "", pokud se používá výchozí cesta v části Fabric data root. |
|SharedLogPath |řetězec, výchozí hodnota je "" |Static|Cesta a název souboru, kam se má umístit sdílený kontejner protokolu. Pro použití výchozí cesty v části Fabric data root použijte "". |
|SharedLogSizeInMB |Int, výchozí hodnota je 8192 |Static|Počet MB, který se má přidělit v kontejneru sdíleného protokolu. |
|SharedLogThrottleLimitInPercentUsed|int, výchozí hodnota je 0 | Static | Procentuální podíl využití sdíleného protokolu, který bude omezovat. Hodnota by měla být mezi 0 a 100. Hodnota 0 znamená použití výchozí procentuální hodnoty. Hodnota 100 nezahrnuje žádné omezení. Hodnota v rozsahu 1 až 99 určuje procento využití protokolu, které se omezuje; Pokud je například sdílený protokol 10 GB a hodnota je 90, po použití 9 GB dojde k omezení. Doporučuje se použít výchozí hodnotu.|
|WriteBufferMemoryPoolMaximumInKB | Int, výchozí hodnota je 0 |Dynamická|Počet KB, který umožňuje, aby fond paměti vyrovnávací paměti pro zápis vzrostl. Hodnotu 0 použijte k označení bez omezení. |
|WriteBufferMemoryPoolMinimumInKB |Int, výchozí hodnota je 8388608 |Dynamická|Počet KB, které se mají zpočátku přidělit fondu paměti pro vyrovnávací paměť zápisu. Hodnota 0 značí, že výchozí omezení nesmí odpovídat SharedLogSizeInMB níže. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|IsEnabled|logická hodnota, výchozí hodnota je FALSE.|Static|Příznak řízení přítomnosti a stavu služby spravovaného tokenu identity v clusteru, je předpokladem pro použití funkce spravované identity Service Fabric aplikací.|

## <a name="management"></a>Správa

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|Časový interval, výchozí hodnota je common:: TimeSpan:: FromMinutes (5)|Dynamická|Zadejte časový interval v sekundách. Interval čištění, který je povolen pro zrušení registrace typu aplikace během automatického čištění typu aplikace.|
|AzureStorageMaxConnections | Int, výchozí hodnota je 5000 |Dynamická|Maximální počet souběžných připojení ke službě Azure Storage. |
|AzureStorageMaxWorkerThreads | Int, výchozí hodnota je 25. |Dynamická|Maximální počet paralelně spuštěných pracovních vláken. |
|AzureStorageOperationTimeout | Čas v sekundách, výchozí hodnota je 6000. |Dynamická|Zadejte časový interval v sekundách. Vypršel časový limit pro dokončení operace xstore. |
|CleanupApplicationPackageOnProvisionSuccess|logická hodnota, výchozí hodnota je true. |Dynamická|Povolí nebo zakáže automatické čištění balíčku aplikace při úspěšném zřízení.
|CleanupUnusedApplicationTypes|Logická hodnota, výchozí hodnota je FALSE. |Dynamická|Tato konfigurace, pokud je povolená, umožňuje automaticky odregistrovat nepoužívané verze typu aplikace, které přeskočí nejnovější tři nepoužívané verze, a tím vystřihuje místo na disku obsazené úložištěm imagí. Automatické čištění se spustí na konci úspěšného zřízení pro daný typ aplikace a také bude pravidelně spouštět jednou denně pro všechny typy aplikací. Počet nepoužívaných verzí, které se mají přeskočit, se dá konfigurovat pomocí parametru "MaxUnusedAppTypeVersionsToKeep". <br/> *Osvědčeným postupem je použití `true` .*
|DisableChecksumValidation | Logická hodnota, výchozí hodnota je false. |Static| Tato konfigurace umožňuje povolit nebo zakázat ověření kontrolního součtu během zřizování aplikace. |
|DisableServerSideCopy | Logická hodnota, výchozí hodnota je false. |Static|Tato konfigurace povolí nebo zakáže kopii balíčku aplikace na straně serveru na úložiště bitových kopií během zřizování aplikace. |
|ImageCachingEnabled | Logická hodnota, výchozí hodnota je true. |Static|Tato konfigurace nám umožňuje povolit nebo zakázat ukládání do mezipaměti. |
|ImageStoreConnectionString |SecureString |Static|Připojovací řetězec do kořenového adresáře pro úložiště bitových kopií. |
|ImageStoreMinimumTransferBPS | Int, výchozí hodnota je 1024 |Dynamická|Minimální přenosová rychlost mezi clusterem a úložiště bitových kopií. Tato hodnota se používá k určení časového limitu při přístupu k externímu úložiště bitových kopií. Tuto hodnotu změňte jenom v případě, že latence mezi clusterem a úložiště bitových kopií je vysoká, aby se cluster mohl stáhnout z externího úložiště bitových kopií. |
|MaxUnusedAppTypeVersionsToKeep | Int, výchozí hodnota je 3 |Dynamická|Tato konfigurace definuje počet nepoužitých verzí typu aplikace, které mají být vynechány pro vyčištění. Tento parametr je použitelný pouze v případě, že je povolen parametr CleanupUnusedApplicationTypes. <br/>*Obecný osvědčený postup je použít výchozí ( `3` ). Hodnoty menší než 1 jsou neplatné.*|


## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, výchozí hodnota je None|Dynamická|Určuje sadu MetricActivityThresholds pro metriky v clusteru. Vyrovnávání bude fungovat, pokud je maxNodeLoad větší než MetricActivityThresholds. Pro metriky Defrag definuje množství zátěže, které se rovná nebo je nižší, než Service Fabric bude brát v úvahu, že uzel je prázdný. |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, výchozí hodnota je None|Dynamická|Určuje sadu MetricBalancingThresholds pro metriky v clusteru. Vyrovnávání bude fungovat, pokud je maxNodeLoad/minNodeLoad větší než MetricBalancingThresholds. Defragmentace bude fungovat, pokud je maxNodeLoad/minNodeLoad alespoň v jednom FD nebo UD menší než MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, výchozí hodnota je None|Dynamická|Určuje část zatížení, která se na základě repliky používá, pokud je zaměněna hodnota mezi 0 (načtení nefunguje s replikou) a 1 (načíst hole s výchozí replikou). |

## <a name="namingservice"></a>NamingService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, výchozí hodnota je 0 |Static|Maximální počet položek udržovaných v mezipaměti popisů služby LRU v bráně pro pojmenovávání (nastavený na 0 bez omezení). |
|MaxClientConnections |Int, výchozí hodnota je 1000 |Dynamická|Maximální povolený počet připojení klientů na bránu. |
|MaxFileOperationTimeout |Čas v sekundách, výchozí hodnota je 30. |Dynamická|Zadejte časový interval v sekundách. Maximální časový limit povolený pro operaci služby File Store. Požadavky, které určují větší časový limit, se odmítnou. |
|MaxIndexedEmptyPartitions |Int, výchozí hodnota je 1000 |Dynamická|Maximální počet prázdných oddílů, které zůstanou indexované v mezipaměti oznámení pro synchronizaci klientů. Všechny prázdné oddíly nad tímto číslem budou odebrány z indexu ve vzestupném pořadí verzí vyhledávání. Opětovné připojení klientů může stále synchronizovat a přijímat chybějící prázdné aktualizace oddílů. ale synchronizační protokol se bude levnější. |
|MaxMessageSize |Int, výchozí hodnota je 4 \* 1024 \* 1024 |Static|Maximální velikost zprávy pro komunikaci klientského uzlu při použití názvů DOS – zmírnění útoků; Výchozí hodnota je 4 MB. |
|MaxNamingServiceHealthReports | Int, výchozí hodnota je 10. |Dynamická|Maximální počet pomalých operací, které pojmenovává nestavové operace služby Store Pokud 0; odesílají se všechny pomalé operace. |
|MaxOperationTimeout |Čas v sekundách, výchozí hodnota je 600. |Dynamická|Zadejte časový interval v sekundách. Maximální časový limit povolený pro klientské operace. Požadavky, které určují větší časový limit, se odmítnou. |
|MaxOutstandingNotificationsPerClient |Int, výchozí hodnota je 1000 |Dynamická|Maximální počet nevyřízených oznámení před vynuceným zavřením registrace klienta bránou. |
|MinReplicaSetSize | Int, výchozí hodnota je 3 |Nepovolené| Minimální počet replik Naming Service potřebných k zápisu do k dokončení aktualizace. Pokud je v systému méně replik, než je aktivní, systém pro spolehlivost odmítne aktualizace služby Naming Service Store, dokud nebudou repliky obnoveny. Tato hodnota by nikdy neměla být větší než TargetReplicaSetSize. |
|PartitionCount |Int, výchozí hodnota je 3 |Nepovolené|Počet oddílů úložiště Naming Service, které se mají vytvořit. Každý oddíl vlastní jeden klíč oddílu, který odpovídá jeho indexu. Takže klíče oddílů [0; PartitionCount] existuje. Zvýšení počtu Naming Service oddílů zvyšuje měřítko, které může Naming Service provádět na základě snížení průměrného množství dat, která uchovává jakákoli záložní sada replik. za cenu zvýšeného využití prostředků (vzhledem k tomu, že repliky služby PartitionCount * ReplicaSetSize musí být zachovány).|
|PlacementConstraints | řetězec, výchozí hodnota je "" |Nepovolené| Omezení umístění pro Naming Service. |
|QuorumLossWaitDuration | Čas v sekundách, výchozí hodnota je MaxValue. |Nepovolené| Zadejte časový interval v sekundách. Když se Naming Service dostane do ztráty kvora; Tento časovač se spustí. Po vypršení jeho platnosti bude FM považovat za ztracené repliky. a pokuste se o obnovení kvora. To může mít za následek ztrátu dat. |
|RepairInterval | Čas v sekundách, výchozí hodnota je 5. |Static| Zadejte časový interval v sekundách. Interval, ve kterém se spustí oprava nekonzistence názvů mezi vlastníkem úřadu a vlastníkem jména. |
|ReplicaRestartWaitDuration | Čas v sekundách, výchozí hodnota je (60,0 × 30)|Nepovolené| Zadejte časový interval v sekundách. Když se replika Naming Service neukončí; Tento časovač se spustí. Po vypršení platnosti FM začnou nahrazené repliky (ještě nepovažují za ztracené). |
|ServiceDescriptionCacheLimit | Int, výchozí hodnota je 0 |Static| Maximální počet položek udržovaných v mezipaměti popisů služby LRU ve službě úložiště názvů (nastavené na 0 bez omezení). |
|ServiceNotificationTimeout |Čas v sekundách, výchozí hodnota je 30. |Dynamická|Zadejte časový interval v sekundách. Časový limit, který se použije při doručování oznámení služby klientovi. |
|StandByReplicaKeepDuration | Čas v sekundách, výchozí hodnota je 3600,0 × 2 |Nepovolené| Zadejte časový interval v sekundách. Když se replika Naming Service vrátí ze stavu dole; je možné, že již byla nahrazena. Tento časovač určuje, jak dlouho bude FM uchovávat pohotovostní repliku před zahozením. |
|TargetReplicaSetSize |Int, výchozí hodnota je 7 |Nepovolené|Počet sad replik pro každý oddíl úložiště Naming Service. Zvýšení počtu sad replik zvyšuje úroveň spolehlivosti pro informace v úložišti Naming Service. snížení změny, ke kterým dojde v důsledku selhání uzlu. za cenu zvýšeného zatížení na Windows Fabric a množství času potřebného k provedení aktualizací dat pro pojmenování.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, výchozí hodnota je None|Dynamická|Procento kapacity uzlu na název metriky; používá se jako vyrovnávací paměť, aby bylo na uzlu pro případ převzetí služeb při selhání zachováno nějaké volné místo. |

## <a name="nodecapacities"></a>NodeCapacities

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Static|Kolekce kapacit uzlů pro různé metriky. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Static|Popisuje domény selhání, ke kterým uzel patří. Doména selhání je definována pomocí identifikátoru URI, který popisuje umístění uzlu v datovém centru.  Identifikátory URI domény selhání mají formát FD:/FD/následovaný segmentem cesty identifikátoru URI.|
|UpgradeDomainId |řetězec, výchozí hodnota je "" |Static|Popisuje doménu upgradu, do které uzel patří. |

## <a name="nodeproperties"></a>NodeProperties

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Static|Kolekce párů klíč-hodnota řetězce pro vlastnosti uzlu. |

## <a name="paas"></a>PaaS

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ClusterId |řetězec, výchozí hodnota je "" |Nepovolené|Úložiště certifikátů x509 používané prostředky infrastruktury pro ochranu konfigurací. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|Counters |Řetězec | Dynamická |Čárkami oddělený seznam čítačů výkonu ke shromáždění. |
|IsEnabled |Logická hodnota, výchozí hodnota je true. | Dynamická |Příznak označuje, zda je povolena kolekce čítačů výkonu v místním uzlu. |
|MaxCounterBinaryFileSizeInMB |Int, výchozí hodnota je 1. | Dynamická |Maximální velikost (v MB) pro každý binární soubor čítače výkonu. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, výchozí hodnota je 10. | Dynamická |Maximální interval (v sekundách), po kterém se vytvoří nový binární soubor čítače výkonu. |
|SamplingIntervalInSeconds |Int, výchozí hodnota je 60 | Dynamická |Interval vzorkování pro shromažďované čítače výkonu. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, výchozí hodnota je 0 | Dynamická|Určuje prioritu omezení spřažení: 0: tvrdý; 1: soft; negativní: ignoruje se. |
|ApplicationCapacityConstraintPriority | Int, výchozí hodnota je 0 | Dynamická|Určuje prioritu omezení kapacity: 0: tvrdý; 1: soft; negativní: ignoruje se. |
|AutoDetectAvailableResources|logická hodnota, výchozí hodnota je TRUE.|Static|Tato konfigurace aktivuje automatickou detekci dostupných prostředků v uzlu (procesor a paměť), pokud je tato konfigurace nastavená na hodnotu true. Pokud uživatel zadal chybné kapacity uzlů nebo pokud je tato konfigurace nastavená na false, načtou se skutečné kapacity a opraví. Pokud je tato konfigurace nastavená na false, budeme sledovat upozornění, že uživatel zadal chybné kapacity uzlů. ale nebudeme je opravovat. To znamená, že uživatel chce mít zadané kapacity jako >, než má uzel skutečně má, nebo pokud nejsou definovány kapacity; bude předpokládat neomezenou kapacitu. |
|BalancingDelayAfterNewNode | Čas v sekundách, výchozí hodnota je 120. |Dynamická|Zadejte časový interval v sekundách. Po přidání nového uzlu nespouštějte vyrovnávání aktivit v tomto období. |
|BalancingDelayAfterNodeDown | Čas v sekundách, výchozí hodnota je 120. |Dynamická|Zadejte časový interval v sekundách. Nepovolujte vyvážení aktivit v rámci této doby po událostech uzlu. |
|BlockNodeInUpgradeConstraintPriority | Int, výchozí hodnota je-1. |Dynamická|Určuje prioritu omezení kapacity: 0: tvrdý; 1: soft; negativní: ignorovat  |
|CapacityConstraintPriority | Int, výchozí hodnota je 0 | Dynamická|Určuje prioritu omezení kapacity: 0: tvrdý; 1: soft; negativní: ignoruje se. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, výchozí hodnota je 20 | Dynamická|Definuje počet po sobě jdoucích pohybů vydaných ResourceBalancer před provedením diagnostiky a vygenerování upozornění na stav. Záporné: za touto podmínkou nejsou vygenerována žádná upozornění. |
|ConstraintFixPartialDelayAfterNewNode | Čas v sekundách, výchozí hodnota je 120. |Dynamická| Zadejte časový interval v sekundách. Po přidání nového uzlu DDo v tomto období neopravit FaultDomain a porušení omezení UpgradeDomain. |
|ConstraintFixPartialDelayAfterNodeDown | Čas v sekundách, výchozí hodnota je 120. |Dynamická| Zadejte časový interval v sekundách. Neopravujte porušení omezení FaultDomain a UpgradeDomain v rámci této doby za událostí uzlu. |
|ConstraintViolationHealthReportLimit | Int, výchozí hodnota je 50 |Dynamická| Definuje počet porušujících omezení repliky musí být před provedením diagnostiky trvale neopravené a budou vygenerovány sestavy o stavu. |
|DetailedConstraintViolationHealthReportLimit | Int, výchozí hodnota je 200 |Dynamická| Definuje počet porušujících omezení repliky musí být před provedením diagnostiky trvale neopravené a budou vygenerovány podrobné sestavy o stavu. |
|DetailedDiagnosticsInfoListLimit | Int, výchozí hodnota je 15. |Dynamická| Definuje počet diagnostických položek (s podrobnými informacemi) na omezení, které chcete zahrnout před zkrácením diagnostiky.|
|DetailedNodeListLimit | Int, výchozí hodnota je 15. |Dynamická| Definuje počet uzlů na omezení, které mají být zahrnuty před zkrácením v sestavách neumístěných replik. |
|DetailedPartitionListLimit | Int, výchozí hodnota je 15. |Dynamická| Definuje počet oddílů na položku diagnostiky pro omezení, které chcete zahrnout před zkrácením diagnostiky. |
|DetailedVerboseHealthReportLimit | Int, výchozí hodnota je 200 | Dynamická|Definuje počet, kolikrát musí být Neumístěná replika trvale Neumístěná před vygenerováním podrobných sestav o stavu. |
|EnforceUserServiceMetricCapacities|logická hodnota, výchozí hodnota je FALSE. | Static |Povolí ochranu služeb prostředků infrastruktury. Všechny uživatelské služby jsou v rámci jednoho objektu úlohy/CGROUP a jsou omezené na zadané množství prostředků. Tato hodnota musí být statická (vyžaduje restart hostitele fabrichost vrátilo) jako vytvoření nebo odebrání objektu uživatelské úlohy a nastavení omezení provedené během otevřeného hostitele prostředků infrastruktury. |
|FaultDomainConstraintPriority | Int, výchozí hodnota je 0 |Dynamická| Určuje prioritu omezení domény selhání: 0: tvrdý; 1: soft; negativní: ignoruje se. |
|GlobalMovementThrottleCountingInterval | Čas v sekundách, výchozí hodnota je 600. |Static| Zadejte časový interval v sekundách. Uveďte délku minulého intervalu, pro který se má sledovat pohyb repliky domény (používá se společně s GlobalMovementThrottleThreshold). Můžete nastavit na 0, aby bylo globální omezení zcela ignorováno. |
|GlobalMovementThrottleThreshold | Uint, výchozí hodnota je 1000 |Dynamická| Maximální počet pohybů povolený ve fázi vyrovnávání v minulém intervalu, který ukazuje GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, výchozí hodnota je 0 | Dynamická|Maximální počet pohybů povolený ve fázi vyrovnávání v minulém intervalu, který ukazuje GlobalMovementThrottleCountingInterval. 0 označuje bez omezení. |
|GlobalMovementThrottleThresholdForPlacement | Uint, výchozí hodnota je 0 |Dynamická| Maximální počet pohybů povolený ve fázi umístění v minulém intervalu uvedeném GlobalMovementThrottleCountingInterval. 0 znamená bez omezení.|
|GlobalMovementThrottleThresholdPercentage|Double, výchozí hodnota je 0.|Dynamická|Maximální počet povolených přesunů v rámci fází vyrovnávání a umístění (vyjádřený jako procento celkového počtu replik v clusteru) v minulém intervalu určeném hodnotou GlobalMovementThrottleCountingInterval 0 označuje bez omezení. Pokud je zadána tato i GlobalMovementThrottleThreshold; pak se použije větší konzervativní limit.|
|GlobalMovementThrottleThresholdPercentageForBalancing|Double, výchozí hodnota je 0.|Dynamická|Maximální počet pohybů povolený ve fázi vyrovnání (vyjádřený jako procento celkového počtu replik v PLB) v minulém intervalu, který je určen GlobalMovementThrottleCountingInterval. 0 označuje bez omezení. Pokud je zadána tato i GlobalMovementThrottleThresholdForBalancing; pak se použije větší konzervativní limit.|
|InBuildThrottlingAssociatedMetric | řetězec, výchozí hodnota je "" |Static| Název přidružené metriky pro toto omezení |
|InBuildThrottlingEnabled | Logická hodnota, výchozí hodnota je false. |Dynamická| Určete, zda je povoleno omezení v rámci sestavení. |
|InBuildThrottlingGlobalMaxValue | Int, výchozí hodnota je 0 |Dynamická|Maximální počet replik v rámci sestavení povolených globálně. |
|InterruptBalancingForAllFailoverUnitUpdates | Logická hodnota, výchozí hodnota je false. | Dynamická|Určuje, jestli má nějaký typ aktualizace jednotky převzetí služeb při selhání přerušit rychlé nebo pomalé vyrovnávání zatížení. Při vytvoření nebo odstranění zadaného příkazu pro vyrovnávání zatížení bude přerušena hodnota FailoverUnit: chybí repliky. změnili jste umístění primární repliky nebo změnili počet replik. Vyrovnávání zatížení nebude přerušeno v jiných případech – Pokud FailoverUnit: má nadbytečné repliky; změnili jste příznak libovolné repliky; změnila se pouze verze oddílu nebo jakýkoli jiný případ. |
|MinConstraintCheckInterval | Čas v sekundách, výchozí hodnota je 1. |Dynamická| Zadejte časový interval v sekundách. Definuje minimální dobu, která musí uplynout před tím, než se dvě po sobě jdoucí kontrolní omezení zaokrouhlí. |
|MinLoadBalancingInterval | Čas v sekundách, výchozí hodnota je 5. |Dynamická| Zadejte časový interval v sekundách. Definuje minimální dobu, která musí uplynout před tím, než se zaokrouhlí dvě po sobě jdoucí vyrovnávání. |
|MinPlacementInterval | Čas v sekundách, výchozí hodnota je 1. |Dynamická| Zadejte časový interval v sekundách. Definuje minimální dobu, která musí uplynout před dvěma po sobě jdoucími umístěními. |
|MoveExistingReplicaForPlacement | Logická hodnota, výchozí hodnota je true. |Dynamická|Nastavení, které určuje, zda se má během umísťování přesunout existující replika. |
|MovementPerPartitionThrottleCountingInterval | Čas v sekundách, výchozí hodnota je 600. |Static| Zadejte časový interval v sekundách. Označuje délku minulého intervalu, pro který se má sledovat pohyb repliky pro každý oddíl (používá se společně s MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, výchozí hodnota je 50 |Dynamická| Pro oddíl se neobjeví žádný pohyb související s vyrovnáváním, pokud počet ovlivněných pohybů pro repliky tohoto oddílu dosáhl nebo překročil MovementPerFailoverUnitThrottleThreshold v minulém intervalu, který je určen MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Logická hodnota, výchozí hodnota je false. |Dynamická| Nastavení, které určuje, jestli je možné přesunout nadřazené repliky a opravit omezení spřažení.|
|PartiallyPlaceServices | Logická hodnota, výchozí hodnota je true. |Dynamická| Určuje, zda budou všechny repliky služby v clusteru umístěny na hodnotu "vše" nebo "nic", na které jsou přiděleny omezené vhodné uzly.|
|PlaceChildWithoutParent | Logická hodnota, výchozí hodnota je true. | Dynamická|Nastavení, které určuje, zda lze repliku podřízené služby umístit, pokud není nastavena žádná nadřazená replika. |
|PlacementConstraintPriority | Int, výchozí hodnota je 0 | Dynamická|Určuje prioritu omezení umístění: 0: tvrdý; 1: soft; negativní: ignoruje se. |
|PlacementConstraintValidationCacheSize | Int, výchozí hodnota je 10000 |Dynamická| Omezuje velikost tabulky používané pro rychlé ověřování a ukládání výrazů omezení umístění do mezipaměti. |
|PlacementSearchTimeout | Čas v sekundách, výchozí hodnota je 0,5. |Dynamická| Zadejte časový interval v sekundách. Při umísťování služeb; předtím, než vrátíte výsledek, hledejte na příliš dlouhou dobu. |
|PLBRefreshGap | Čas v sekundách, výchozí hodnota je 1. |Dynamická| Zadejte časový interval v sekundách. Definuje minimální dobu, která musí uplynout před tím, než PLB obnoví stav. |
|PreferredLocationConstraintPriority | Int, výchozí hodnota je 2| Dynamická|Určuje prioritu upřednostňovaného omezení umístění: 0: tvrdý; 1: soft; 2: optimalizace; negativní: ignorovat |
|PreferredPrimaryDomainsConstraintPriority| Int, výchozí hodnota je 1. | Dynamická| Určuje prioritu upřednostňovanosti omezení primární domény: 0: pevný; 1: soft; negativní: ignorovat |
|PreferUpgradedUDs|logická hodnota, výchozí hodnota je FALSE.|Dynamická|Zapne a vypne logiku, která preferuje přesun na již upgradovanou UDs. Počínaje hodnotou SF 7,0 je výchozí hodnota pro tento parametr změněna z hodnoty TRUE na FALSE.|
|PreventTransientOvercommit | Logická hodnota, výchozí hodnota je false. | Dynamická|Určuje, že se má PLB okamžitě počítat s prostředky, které budou uvolněny pomocí iniciované přesunutí. Ve výchozím nastavení. PLB může iniciovat přesun a přesunout se na stejný uzel, který může vytvořit přechodný přepisování. Nastavením tohoto parametru na hodnotu true zabráníte tomu, aby se tyto typy nadtvrzování a příkazu defrag na vyžádání (neboli placementWithMove) zakázaly. |
|ScaleoutCountConstraintPriority | Int, výchozí hodnota je 0 |Dynamická| Určuje prioritu omezení počtu navýšení kapacity: 0: tvrdý; 1: soft; negativní: ignoruje se. |
|SubclusteringEnabled|Logická hodnota, výchozí hodnota je FALSE. | Dynamická |Při výpočtu směrodatné odchylky pro vyvážení potvrdit subclustery |
|SubclusteringReportingPolicy| Int, výchozí hodnota je 1. |Dynamická|Definuje, jak a kdy se budou odesílat sestavy o stavu subclusterů: 0: nesestavovat; 1: upozornění; 2: OK |
|SwapPrimaryThrottlingAssociatedMetric | řetězec, výchozí hodnota je ""|Static| Název přidružené metriky pro toto omezení |
|SwapPrimaryThrottlingEnabled | Logická hodnota, výchozí hodnota je false.|Dynamická| Určete, zda je povoleno omezení swap-Primary. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, výchozí hodnota je 0 |Dynamická| Maximální počet přehozených primárních replik je povolen globálně. |
|TraceCRMReasons |Logická hodnota, výchozí hodnota je true. |Dynamická|Určuje, jestli se mají sledovat důvody pro pohyby vydaných aplikací CRM do kanálu provozních událostí. |
|UpgradeDomainConstraintPriority | Int, výchozí hodnota je 1.| Dynamická|Určuje prioritu omezení domény upgradu: 0: tvrdý; 1: soft; negativní: ignoruje se. |
|UseMoveCostReports | Logická hodnota, výchozí hodnota je false. | Dynamická|Dá pokyn k ignorování nákladového prvku funkce bodování. Výsledkem je potenciálně velký počet přesunů pro lepší vyvážené umístění. |
|UseSeparateSecondaryLoad | Logická hodnota, výchozí hodnota je true. | Dynamická|Nastavení, které určuje, zda má být pro sekundární repliky použito samostatné zatížení. |
|UseSeparateSecondaryMoveCost | Logická hodnota, výchozí hodnota je false. | Dynamická|Nastavení, které určuje, zda mají být pro sekundární repliky použity samostatné náklady na přesun. |
|ValidatePlacementConstraint | Logická hodnota, výchozí hodnota je true. |Dynamická| Určuje, jestli se má PlacementConstraint výraz pro službu ověřit, když se aktualizuje ServiceDescription služby. |
|ValidatePrimaryPlacementConstraintOnPromote| Logická hodnota, výchozí hodnota je TRUE. |Dynamická|Určuje, jestli se má pro převzetí služeb při selhání vyhodnotit výraz PlacementConstraint pro službu pro primární preference. |
|VerboseHealthReportLimit | Int, výchozí hodnota je 20 | Dynamická|Definuje počet, kolikrát musí být replika Neumístěná předtím, než se pro ni nahlásí upozornění na stav (Pokud je zapnuté podrobné hlášení stavu). |
|NodeLoadsOperationalTracingEnabled | Logická hodnota, výchozí hodnota je true. |Dynamická|Konfigurace, která umožňuje zatížení uzlu provozovat provozní strukturální trasování v úložišti událostí. |
|NodeLoadsOperationalTracingInterval | Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (20) | Dynamická|Zadejte časový interval v sekundách. Interval, pomocí kterého se má sledovat načtení uzlu do úložiště událostí pro každou doménu služby. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Čas v sekundách, výchozí hodnota je 900. |Dynamická|Zadejte časový interval v sekundách. Doba, po kterou systém počká, než se ukončí hostitelé služby s replikami, které jsou zablokované v ukončení během upgradu aplikace.|
|FabricUpgradeMaxReplicaCloseDuration | Čas v sekundách, výchozí hodnota je 900. |Dynamická| Zadejte časový interval v sekundách. Doba, po kterou systém počká, než se ukončí hostitelé služby s replikami, které jsou zablokované během upgradu prostředků infrastruktury v blízkosti. |
|GracefulReplicaShutdownMaxDuration|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (120)|Dynamická|Zadejte časový interval v sekundách. Doba, po kterou systém počká, než se ukončí hostitelé služby s replikami, které jsou zablokované v Zavřít. Pokud je tato hodnota nastavená na 0, repliky se nedají pokyn zavřít.|
|NodeDeactivationMaxReplicaCloseDuration | Čas v sekundách, výchozí hodnota je 900. |Dynamická|Zadejte časový interval v sekundách. Doba, po kterou systém počká, než se ukončí hostitelé služby s replikami, které jsou zablokované v zavření během deaktivace uzlu. |
|PeriodicApiSlowTraceInterval | Čas v sekundách, výchozí hodnota je 5 minut. |Dynamická| Zadejte časový interval v sekundách. PeriodicApiSlowTraceInterval definuje interval, za který se budou přesledovat pomalá volání rozhraní API monitorováním rozhraní API. |
|ReplicaChangeRoleFailureRestartThreshold|int, výchozí hodnota je 10.|Dynamická| Čísla. Zadejte počet selhání rozhraní API při primární promoakci, po kterém se použije akce automatického zmírnění (restart repliky). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, výchozí hodnota je 2147483647|Dynamická| Čísla. Zadejte počet selhání rozhraní API při primární promoakci, po kterém bude vyvolána zpráva o stavu upozornění.|
|ServiceApiHealthDuration | Čas v sekundách, výchozí hodnota je 30 minut. |Dynamická| Zadejte časový interval v sekundách. ServiceApiHealthDuration definuje, jak dlouho čekáme na spuštění rozhraní API služby předtím, než pošleme zprávu, že není v pořádku. |
|ServiceReconfigurationApiHealthDuration | Čas v sekundách, výchozí hodnota je 30. |Dynamická| Zadejte časový interval v sekundách. ServiceReconfigurationApiHealthDuration definuje, jak dlouho čekáme na spuštění rozhraní API služby předtím, než nahlásíme, že není v pořádku. To platí pro volání rozhraní API, která mají vliv na dostupnost.|

## <a name="replication"></a>Replikace
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|Časový interval, výchozí hodnota je common:: TimeSpan:: FromMilliseconds (15)|Static|Zadejte časový interval v sekundách. Určuje dobu, po kterou Replikátor po přijetí operace počká, než pošle zpět potvrzení. Jiné operace přijaté během tohoto časového období budou mít potvrzení, které se vrátí v rámci jedné zprávy – > snižují síťový provoz, ale můžou snížit propustnost replikátoru.|
|MaxCopyQueueSize|uint, výchozí hodnota je 1024|Static|Toto je maximální hodnota definující počáteční velikost fronty, která udržuje operace replikace. Všimněte si, že musí být mocninou 2. Pokud během běhu bude fronta růst s touto operací velikosti, bude omezena mezi primárními a sekundárními replikami.|
|MaxPrimaryReplicationQueueMemorySize|uint, výchozí hodnota je 0|Static|Toto je maximální hodnota primární fronty replikace v bajtech.|
|MaxPrimaryReplicationQueueSize|uint, výchozí hodnota je 1024|Static|Toto je maximální počet operací, které mohou existovat ve frontě primární replikace. Všimněte si, že musí být mocninou 2.|
|MaxReplicationMessageSize|uint, výchozí hodnota je 52428800|Static|Maximální velikost zprávy operací replikace. Výchozí hodnota je 50 MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, výchozí hodnota je 0|Static|Toto je maximální hodnota fronty sekundární replikace v bajtech.|
|MaxSecondaryReplicationQueueSize|uint, výchozí hodnota je 2048|Static|Toto je maximální počet operací, které mohou existovat v sekundární frontě replikace. Všimněte si, že musí být mocninou 2.|
|QueueHealthMonitoringInterval|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (30)|Static|Zadejte časový interval v sekundách. Tato hodnota určuje časové období, které Replikátor využíval k monitorování všech událostí stavu upozornění/chyby ve frontách operací replikace. Hodnota 0 zakáže sledování stavu. |
|QueueHealthWarningAtUsagePercent|uint, výchozí hodnota je 80|Static|Tato hodnota určuje využití fronty replikace (v procentech) po tom, co oznamuje upozornění na vysoké využití fronty. Provedeme to po uplynutí časového limitu QueueHealthMonitoringInterval. Pokud využití fronty v intervalu odkladu klesne pod toto procento,|
|ReplicatorAddress|řetězec, výchozí hodnota je "localhost: 0"|Static|Koncový bod ve formě řetězce "IP: port", který používá Replikátor Windows Fabric k navázání připojení k ostatním replikám za účelem odesílání/přijímání operací.|
|ReplicatorListenAddress|řetězec, výchozí hodnota je "localhost: 0"|Static|Koncový bod ve formě řetězce "IP: port", který používá Replikátor Windows Fabric k přijímání operací z jiných replik.|
|ReplicatorPublishAddress|řetězec, výchozí hodnota je "localhost: 0"|Static|Koncový bod ve formě řetězce "IP: port", který používá Replikátor Windows Fabric k odesílání operací do jiných replik.|
|RetryInterval|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (5)|Static|Zadejte časový interval v sekundách. Když dojde ke ztrátě nebo zamítnutí operace, určíte, jak často se Replikátor bude pokoušet odeslat operaci.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|IsEnabled|logická hodnota, výchozí hodnota je FALSE. |Static|Řídí, jestli je služba v clusteru povolená, nebo ne. |

## <a name="runas"></a>RunAs

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|RunAsAccountName |řetězec, výchozí hodnota je "" |Dynamická|Označuje název účtu RunAs. To je potřeba jenom pro typ účtu "DomainUser" nebo "ManagedServiceAccount". Platné hodnoty jsou "doména \ uživatel" nebo " user@domain ". |
|Seznamu|řetězec, výchozí hodnota je "" |Dynamická|Označuje typ účtu RunAs. To je potřeba pro všechny platné hodnoty oddílu RunAs jsou "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|řetězec, výchozí hodnota je "" |Dynamická|Označuje heslo účtu RunAs. To je potřeba jenom pro typ účtu "DomainUser". |

## <a name="runas_dca"></a>RunAs_DCA

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|RunAsAccountName |řetězec, výchozí hodnota je "" |Dynamická|Označuje název účtu RunAs. To je potřeba jenom pro typ účtu "DomainUser" nebo "ManagedServiceAccount". Platné hodnoty jsou "doména \ uživatel" nebo " user@domain ". |
|Seznamu|řetězec, výchozí hodnota je "" |Dynamická|Označuje typ účtu RunAs. To je potřeba pro všechny platné hodnoty oddílu RunAs jsou "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|řetězec, výchozí hodnota je "" |Dynamická|Označuje heslo účtu RunAs. To je potřeba jenom pro typ účtu "DomainUser". |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|RunAsAccountName |řetězec, výchozí hodnota je "" |Dynamická|Označuje název účtu RunAs. To je potřeba jenom pro typ účtu "DomainUser" nebo "ManagedServiceAccount". Platné hodnoty jsou "doména \ uživatel" nebo " user@domain ". |
|Seznamu|řetězec, výchozí hodnota je "" |Dynamická|Označuje typ účtu RunAs. To je potřeba pro všechny platné hodnoty oddílu RunAs jsou "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|řetězec, výchozí hodnota je "" |Dynamická|Označuje heslo účtu RunAs. To je potřeba jenom pro typ účtu "DomainUser". |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|RunAsAccountName |řetězec, výchozí hodnota je "" |Dynamická|Označuje název účtu RunAs. To je potřeba jenom pro typ účtu "DomainUser" nebo "ManagedServiceAccount". Platné hodnoty jsou "doména \ uživatel" nebo " user@domain ". |
|Seznamu|řetězec, výchozí hodnota je "" |Dynamická|Označuje typ účtu RunAs. To je potřeba pro všechny platné hodnoty oddílu RunAs jsou "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|řetězec, výchozí hodnota je "" |Dynamická|Označuje heslo účtu RunAs. To je potřeba jenom pro typ účtu "DomainUser". |

## <a name="security"></a>Zabezpečení
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|řetězec, výchozí hodnota je ""|Static|Formát koncového bodu certifikátu AAD, výchozí komerční služba Azure určená pro jiné než výchozí prostředí, například Azure Government "https: \/ /Login.microsoftonline.us/ {0} /federationmetadata/2007-06/federationmetadata.xml" |
|AADClientApplication|řetězec, výchozí hodnota je ""|Static|Název nebo ID nativní klientské aplikace představující klienty topologie Fabric |
|AADClusterApplication|řetězec, výchozí hodnota je ""|Static|Název nebo ID aplikace webového rozhraní API představující cluster |
|AADLoginEndpoint|řetězec, výchozí hodnota je ""|Static|Koncový bod přihlášení AAD, výchozí komerční Azure, zadaný pro jiné než výchozí prostředí, například Azure Government "https: \/ /Login.microsoftonline.us" |
|AADTenantId|řetězec, výchozí hodnota je ""|Static|ID tenanta (GUID) |
|AcceptExpiredPinnedClusterCertificate|logická hodnota, výchozí hodnota je FALSE.|Dynamická|Příznak označující, zda se mají přijímat certifikáty clusteru s vypršenou platností deklarované kryptografickým otiskem platí pouze pro certifikáty clusteru. Takže zachováte cluster jako aktivní. |
|AdminClientCertThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Kryptografické otisky certifikátů používaných klienty v roli správce Je to seznam názvů oddělených čárkami. |
|AADTokenEndpointFormat|řetězec, výchozí hodnota je ""|Static|Koncový bod tokenu AAD, výchozí komerční Azure, zadaný pro jiné než výchozí prostředí, například Azure Government "https: \/ /Login.microsoftonline.us/ {0} " |
|AdminClientClaims|řetězec, výchozí hodnota je ""|Dynamická|Od klientů pro správu jsou očekávány všechny možné deklarace identity. stejný formát jako ClientClaims; v tomto seznamu se interně přidají do ClientClaims; Proto není nutné přidávat stejné položky do ClientClaims. |
|AdminClientIdentities|řetězec, výchozí hodnota je ""|Dynamická|Identity klientů prostředků infrastruktury Windows v roli správce; slouží k autorizaci privilegovaných operací prostředků infrastruktury. Je to seznam oddělený čárkami. Každá položka je název účtu domény nebo název skupiny. Pro pohodlí; účet, na kterém je spuštěný fabric.exe, je automaticky přiřazená role správce. Takže je skupina ServiceFabricAdministrators. |
|AppRunAsAccountGroupX509Folder|řetězec, výchozí hodnota je/Home/sfuser/sfusercerts |Static|Složka, ve které se nachází certifikáty AppRunAsAccountGroup x509 a soukromé klíče |
|CertificateExpirySafetyMargin|Časový interval, výchozí hodnota je common:: TimeSpan:: FromMinutes (43200)|Static|Zadejte časový interval v sekundách. Bezpečnostní okraj pro vypršení platnosti certifikátu; Zpráva o stavu certifikátu se změní z OK na varování, pokud je vypršení platnosti menší než toto. Výchozí hodnota je 30 dní. |
|CertificateHealthReportingInterval|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (3600 * 8)|Static|Zadejte časový interval v sekundách. Zadejte interval pro vytváření sestav stavu certifikátů; Výchozí hodnota je 8 hodin; nastavením na hodnotu 0 zakážete vytváření sestav stavu certifikátů. |
|ClientCertThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Kryptografické otisky certifikátů používaných klienty ke komunikaci s clusterem; cluster používá toto autorizaci příchozího připojení. Je to seznam názvů oddělených čárkami. |
|ClientClaimAuthEnabled|logická hodnota, výchozí hodnota je FALSE.|Static|Určuje, zda je na klientech povoleno ověřování na základě deklarace identity. nastavení této hodnoty true implicitně nastaví ClientRoleEnabled. |
|ClientClaims|řetězec, výchozí hodnota je ""|Dynamická|Od klientů se pro připojení k bráně očekávají všechny možné deklarace identity. Tento seznam: ClaimsEntry \| \| ClaimsEntry \| \| ClaimsEntry... Každý ClaimsEntry je a seznam: ClaimType = ClaimValue && ClaimType = ClaimValue && ClaimType = ClaimValue... |
|ClientIdentities|řetězec, výchozí hodnota je ""|Dynamická|Identity Windows FabricClient; k autorizaci příchozích připojení se používá pojmenování brány. Je to seznam oddělený čárkami. Každá položka je název účtu domény nebo název skupiny. Pro pohodlí; účet, který spouští fabric.exe, je automaticky povolen. Takže jsou skupiny ServiceFabricAllowedUsers a ServiceFabricAdministrators. |
|ClientRoleEnabled|logická hodnota, výchozí hodnota je FALSE.|Static|Indikuje, jestli je role klienta povolená; Při nastavení na hodnotu true; Klienti jsou přiřazeni role na základě jejich identit. Pro v2; Povolením této možnosti znamená, že klient, který není v AdminClientCommonNames/AdminClientIdentities, může spouštět pouze operace jen pro čtení. |
|ClusterCertThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Kryptografické otisky certifikátů s povoleným připojením ke clusteru; seznam názvů oddělených čárkami. |
|ClusterCredentialType|řetězec, výchozí hodnota je None.|Nepovolené|Určuje typ přihlašovacích údajů zabezpečení, které se mají použít k zabezpečení clusteru. Platné hodnoty jsou None/x509/Windows. |
|ClusterIdentities|řetězec, výchozí hodnota je ""|Dynamická|Identity Windows uzlů clusteru; používá se pro autorizaci členství v clusteru. Je to seznam oddělený čárkami. Každá položka je název účtu domény nebo název skupiny. |
|ClusterSpn|řetězec, výchozí hodnota je ""|Nepovolené|Hlavní název služby clusteru; Když se prostředky infrastruktury spouštějí jako uživatel s jedním doménou (účet uživatele gMSA/doména). Je hlavní název služby pro naslouchací procesy zapůjčení a naslouchací procesy v fabric.exe: federační naslouchací procesy; interní naslouchací procesy replikace; naslouchací proces služby runtime a naslouchací proces pro pojmenovávání brány. Při spuštění prostředků infrastruktury jako účtů počítačů by toto mělo zůstat prázdné. v takovém případě připojení hlavního názvu služby (SPN) služby COMPUTE COMPUTE z přenosové adresy naslouchacího procesu. |
|CrlCheckingFlag|uint, Default je 0x40000000|Dynamická|Výchozí příznak ověření řetězu certifikátů; může být přepsán příznakem specifickým pro součást; například Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY nastavení na hodnotu 0 zakáže kontrolu seznamu CRL úplný seznam podporovaných hodnot je dokumentován dwFlags CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|Časový interval, výchozí hodnota je common:: TimeSpan:: FromMinutes (15)|Dynamická|Zadejte časový interval v sekundách. Jak dlouho je kontrola seznamu CRL pro daný certifikát vypnuta po zjištění chyby v režimu offline; Pokud je offline chyba seznamu CRL, můžete ji ignorovat. |
|CrlOfflineHealthReportTtl|Časový interval, výchozí hodnota je common:: TimeSpan:: FromMinutes (1440)|Dynamická|Zadejte časový interval v sekundách. |
|DisableFirewallRuleForDomainProfile| logická hodnota, výchozí hodnota je TRUE. |Static| Indikuje, jestli se nemá Povolit pravidlo brány firewall pro profil domény. |
|DisableFirewallRuleForPrivateProfile| logická hodnota, výchozí hodnota je TRUE. |Static| Indikuje, jestli se nemá Povolit pravidlo brány firewall pro privátní profil. | 
|DisableFirewallRuleForPublicProfile| logická hodnota, výchozí hodnota je TRUE. | Static|Indikuje, jestli se nemá Povolit pravidlo brány firewall pro veřejný profil. |
| EnforceLinuxMinTlsVersion | logická hodnota, výchozí hodnota je FALSE. | Static | Pokud je nastaveno na true; podporuje se jenom TLS verze 1.2 +.  Pokud je hodnota false; Podpora dřívějších verzí TLS. Platí jenom pro Linux. |
| EnforcePrevalidationOnSecurityChanges | logická hodnota, výchozí hodnota je FALSE.| Dynamická | Příznak řízení chování upgradu clusteru při zjištění změn jeho nastavení zabezpečení Pokud je nastavená hodnota true, upgrade clusteru se pokusí zajistit, aby aspoň jeden z certifikátů, které odpovídají některým pravidlům prezentace, mohl předat odpovídající ověřovací pravidlo. Před použitím nového nastavení na libovolný uzel se provede předběžné ověření, ale spustí se jenom v uzlu hostujícím primární repliku služby Správce clusterů v době zahájení upgradu. Výchozí hodnota je nastavená na false; od verze 7,1 bude nastavení pro nové clustery Azure Service Fabric nastaveno na hodnotu true.|
|FabricHostSpn| řetězec, výchozí hodnota je "" |Static| Hlavní název služby pro hostitele fabrichost vrátilo; Když se Fabric spustí jako uživatel s jedním doménou (účet uživatele gMSA/doména) a hostitele fabrichost vrátilo se spustí pod účtem počítače. Je hlavní název služby (SPN) naslouchacího procesu IPC pro hostitele fabrichost vrátilo; které by ve výchozím nastavení mělo zůstat prázdné, protože hostitele fabrichost vrátilo běží pod účtem počítače |
|IgnoreCrlOfflineError|logická hodnota, výchozí hodnota je FALSE.|Dynamická|Bez ohledu na to, jestli se má při ověřování příchozích klientských certifikátů na straně serveru ignorovat chyba režimu CRL |
|IgnoreSvrCrlOfflineError|logická hodnota, výchozí hodnota je TRUE.|Dynamická|Bez ohledu na to, jestli se má ignorovat chyba offline v seznamu CRL, když klientská strana ověří příchozí certifikáty serveru Výchozí hodnota je true. Útoky s odvolanými certifikáty serveru vyžadují narušení služby DNS; těžší než u odvolaných klientských certifikátů. |
|ServerAuthCredentialType|řetězec, výchozí hodnota je None.|Static|Určuje typ přihlašovacích údajů zabezpečení, které se mají použít, aby se zabezpečila komunikace mezi FabricClient a clusterem. Platné hodnoty jsou None/x509/Windows. |
|ServerCertThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Kryptografické otisky certifikátů serveru, které cluster používá ke komunikaci s klienty; klienti používají tuto metodu k ověření clusteru. Je to seznam názvů oddělených čárkami. |
|SettingsX509StoreName| řetězec, výchozí hodnota je MY| Dynamická|Úložiště certifikátů x509 používané prostředky infrastruktury ke konfiguraci ochrany |
|UseClusterCertForIpcServerTlsSecurity|logická hodnota, výchozí hodnota je FALSE.|Static|Určuje, jestli se má k zabezpečení přenosové jednotky TLS serveru IPC použít certifikát clusteru. |
|X509Folder|řetězec, výchozí hodnota je/var/lib/waagent|Static|Složka, ve které se nachází certifikáty x509 a soukromé klíče |
|TLS1_2_CipherList| řetězec| Static|Pokud je nastaveno na neprázdný řetězec; přepíše seznam podporovaných šifr pro TLS 1.2 a nižší. V dokumentaci "OpenSSL-šifry" najdete seznam podporovaného šifrování a příklad formátu seznamu silné šifry pro TLS 1.2: "ECDH-ECDSA-AES256-GCM-SHA384: ECDH-ECDSA-AES128-GCM-SHA256: ECDH-RSA-AES256-GCM-SHA384: ECDH – RSA-AES-128-GCM-SHA256: ECDH-ECDSA-AES256-CBC-SHA384: ECDH-ECDSA-AES128-CBC-SHA256: ECDH-RSA-AES256-CBC-SHA384: ECDH-RSA-AES128-CBC-SHA256" platí jenom pro Linux. |

## <a name="securityadminclientx509names"></a>Zabezpečení/AdminClientX509Names

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, výchozí hodnota je None|Dynamická|Toto je seznam dvojic "název" a "hodnota". Každé "název" je subjektem běžný název nebo DnsName certifikátů x509 autorizovaných pro operace klienta správce. Pro dané "název", "value" je čárkami oddělený seznam kryptografických otisků certifikátů pro připnutí vystavitele, pokud není prázdný, musí být přímým vystavitelem klientských certifikátů správce v seznamu. |

## <a name="securityclientaccess"></a>Zabezpečení/ClientAccess

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ActivateNode |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro aktivaci uzlu |
|CancelTestCommand |řetězec, výchozí hodnota je admin. |Dynamická| Zruší konkrétní TestCommand – Pokud se nachází v letu. |
|CodePackageControl |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro restartování balíčků kódu. |
|CreateApplication |řetězec, výchozí hodnota je admin. | Dynamická|Konfigurace zabezpečení pro vytváření aplikací. |
|CreateComposeDeployment|řetězec, výchozí hodnota je admin.| Dynamická|Vytvoří nasazení typu popsané pomocí souborů pro vytváření. |
|CreateGatewayResource|řetězec, výchozí hodnota je admin.| Dynamická|Vytvoření prostředku brány |
|Vytvořit |řetězec, výchozí hodnota je admin. |Dynamická|Konfigurace zabezpečení pro vytváření názvů identifikátorů URI. |
|CreateNetwork|řetězec, výchozí hodnota je admin. |Dynamická|Vytvoří síť kontejneru. |
|Operace CreateService |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro vytvoření služby. |
|CreateServiceFromTemplate |řetězec, výchozí hodnota je admin. |Dynamická|Konfigurace zabezpečení pro vytvoření služby ze šablony |
|CreateVolume|řetězec, výchozí hodnota je admin.|Dynamická|Vytvoří svazek. |
|Operace deactivatenode |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro deaktivaci uzlu. |
|DeactivateNodesBatch |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro deaktivaci více uzlů. |
|Odstranit |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro operaci odstranění klienta úložiště imagí. |
|DeleteApplication |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro odstranění aplikace |
|DeleteComposeDeployment|řetězec, výchozí hodnota je admin.| Dynamická|Odstraní nasazení vytváření. |
|DeleteGatewayResource|řetězec, výchozí hodnota je admin.| Dynamická|Odstraní prostředek brány. |
|Odstranit |řetězec, výchozí hodnota je admin. |Dynamická|Konfigurace zabezpečení pro odstranění názvů identifikátorů URI. |
|DeleteNetwork|řetězec, výchozí hodnota je admin. |Dynamická|Odstraní síť kontejneru. |
|DeleteService |řetězec, výchozí hodnota je admin. |Dynamická|Konfigurace zabezpečení pro odstranění služby. |
|DeleteVolume|řetězec, výchozí hodnota je admin.|Dynamická|Odstraní svazek.| 
|EnumerateProperties |řetězec, výchozí hodnota je "admin \| \| User" | Dynamická|Konfigurace zabezpečení pro vytváření výčtu vlastností |
|EnumerateSubnames |řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro vytváření názvů výčtu identifikátorů URI. |
|FileContent |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro přenos souborů klienta úložiště imagí (externí do clusteru). |
|Stažení |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro spuštění stahování souboru klienta úložiště imagí (externí do clusteru) |
|FinishInfrastructureTask |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro dokončení úloh infrastruktury |
|GetChaosReport | řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Načte stav chaos v daném časovém rozsahu. |
|GetClusterConfiguration | řetězec, výchozí hodnota je "admin \| \| User" | Dynamická|VyGetClusterConfiguration na oddíl. |
|GetClusterConfigurationUpgradeStatus | řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| VyGetClusterConfigurationUpgradeStatus na oddíl. |
|GetFabricUpgradeStatus |řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro dotazování na stav upgradu clusteru. |
|GetFolderSize |řetězec, výchozí hodnota je admin. |Dynamická|Konfigurace zabezpečení pro získání velikosti složky v FileStoreService |
|GetNodeDeactivationStatus |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro kontrolu stavu deaktivace. |
|GetNodeTransitionProgress | řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro získání průběhu příkazu přechodu uzlu. |
|GetPartitionDataLossProgress | řetězec, výchozí hodnota je "admin \| \| User" | Dynamická|Načte průběh volání rozhraní API pro ztrátu dat. |
|GetPartitionQuorumLossProgress | řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Načte průběh volání rozhraní API ztráty kvora. |
|GetPartitionRestartProgress | řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Načte průběh volání rozhraní API pro restartování oddílu. |
|Gettajných kódů|řetězec, výchozí hodnota je admin.|Dynamická|Získat tajné hodnoty |
|Getservicedescription |řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro zasílání oznámení služby Long-cyklické dotazování a čtení popisů služeb. |
|GetStagingLocation |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro načtení pracovního umístění klienta úložiště imagí. |
|GetStoreLocation |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro načítání umístění úložiště klienta úložiště imagí. |
|GetUpgradeOrchestrationServiceState|řetězec, výchozí hodnota je admin.| Dynamická|VyGetUpgradeOrchestrationServiceState na oddíl. |
|GetUpgradesPendingApproval |řetězec, výchozí hodnota je admin. |Dynamická| VyGetUpgradesPendingApproval na oddíl. |
|GetUpgradeStatus |řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro dotazování na stav upgradu aplikace. |
|InternalList |řetězec, výchozí hodnota je admin. | Dynamická|Konfigurace zabezpečení pro operaci seznamu klientských souborů úložiště imagí (interní). |
|InvokeContainerApi|řetězec, výchozí hodnota je admin.|Dynamická|Vyvolat rozhraní API kontejneru |
|InvokeInfrastructureCommand |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro příkazy správy úloh infrastruktury |
|InvokeInfrastructureQuery |řetězec, výchozí hodnota je "admin \| \| User" | Dynamická|Konfigurace zabezpečení pro dotazování na úlohy infrastruktury. |
|Seznam |řetězec, výchozí hodnota je "admin \| \| User" | Dynamická|Konfigurace zabezpečení pro operaci seznamu klientských souborů úložiště imagí |
|MoveNextFabricUpgradeDomain |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro obnovení upgradu clusteru s explicitní upgradovací doménou |
|MoveNextUpgradeDomain |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro obnovení upgradu aplikace s explicitní upgradovací doménou |
|MoveReplicaControl |řetězec, výchozí hodnota je admin. | Dynamická|Přesuňte repliku. |
|NameExists |řetězec, výchozí hodnota je "admin \| \| User" | Dynamická|Konfigurace zabezpečení pro kontrolu existence identifikátorů URI pojmenování. |
|NodeControl |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro spuštění; zastaví a restartování uzlů. |
|Operace nodestateremoved |řetězec, výchozí hodnota je admin. |Dynamická| Odstranila se konfigurace zabezpečení pro stav uzlu vytváření sestav. |
|Ping |řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro klientské příkazy pro ověřování. |
|PredeployPackageToNode |řetězec, výchozí hodnota je admin. |Dynamická| Rozhraní API pro předinstalaci |
|PrefixResolveService |řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro rozlišení předpony služby na základě stížnosti. |
|PropertyReadBatch |řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro operace čtení vlastností pojmenování |
|PropertyWriteBatch |řetězec, výchozí hodnota je admin. |Dynamická|Konfigurace zabezpečení pro operace zápisu vlastností pojmenování. |
|ProvisionApplicationType |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro zřizování typu aplikace. |
|ProvisionFabric |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro clustery MSI a/nebo zřizování manifestu clusteru. |
|Dotaz |řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro dotazy. |
|RecoverPartition |řetězec, výchozí hodnota je admin. | Dynamická|Konfigurace zabezpečení pro obnovování oddílu. |
|Operace recoverpartitions |řetězec, výchozí hodnota je admin. | Dynamická|Konfigurace zabezpečení pro obnovování oddílů. |
|RecoverServicePartitions |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro obnovování oddílů služby. |
|Operace recoversystempartitions |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro obnovování oddílů systémových služeb. |
|RemoveNodeDeactivations |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro vrácení deaktivace na více uzlech. |
|ReportFabricUpgradeHealth |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro obnovení upgradu clusteru s aktuálním průběhem upgradu. |
|ReportFault |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro chybu generování sestav. |
|ReportHealth |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro vytváření sestav stavu. |
|ReportUpgradeHealth |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro obnovení upgradu aplikací s aktuálním průběhem upgradu. |
|ResetPartitionLoad |řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro obnovení zatížení pro failoverUnit. |
|ResolveNameOwner |řetězec, výchozí hodnota je "admin \| \| User" | Dynamická|Konfigurace zabezpečení pro překlad vlastníka identifikátoru URI pro pojmenování. |
|ResolvePartition |řetězec, výchozí hodnota je "admin \| \| User" | Dynamická|Konfigurace zabezpečení pro překlad systémových služeb. |
|ResolveService |řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro řešení služby na základě stížností. |
|ResolveSystemService|řetězec, výchozí hodnota je "admin \| \| User"|Dynamická| Konfigurace zabezpečení pro překlad systémových služeb |
|RollbackApplicationUpgrade |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro vrácení zpět upgradů aplikací. |
|RollbackFabricUpgrade |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro vracení zpět upgradů clusteru. |
|ServiceNotifications |řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro oznámení služby na základě událostí. |
|SetUpgradeOrchestrationServiceState|řetězec, výchozí hodnota je admin.| Dynamická|VySetUpgradeOrchestrationServiceState na oddíl. |
|StartApprovedUpgrades |řetězec, výchozí hodnota je admin. |Dynamická| VyStartApprovedUpgrades na oddíl. |
|StartChaos |řetězec, výchozí hodnota je admin. |Dynamická| Spustí chaos – Pokud ještě není spuštěný. |
|StartClusterConfigurationUpgrade |řetězec, výchozí hodnota je admin. |Dynamická| VyStartClusterConfigurationUpgrade na oddíl. |
|StartInfrastructureTask |řetězec, výchozí hodnota je admin. | Dynamická|Konfigurace zabezpečení pro spouštění úloh infrastruktury |
|StartNodeTransition |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro spuštění přechodu uzlu. |
|StartPartitionDataLoss |řetězec, výchozí hodnota je admin. |Dynamická| Vychází ze ztráty dat na oddílu. |
|StartPartitionQuorumLoss |řetězec, výchozí hodnota je admin. |Dynamická| Vychází ze ztráty kvora v oddílu. |
|StartPartitionRestart |řetězec, výchozí hodnota je admin. |Dynamická| Současně restartuje některé nebo všechny repliky oddílu. |
|StopChaos |řetězec, výchozí hodnota je admin. |Dynamická| Zastaví chaos – Pokud bylo spuštěno. |
|ToggleVerboseServicePlacementHealthReporting | řetězec, výchozí hodnota je "admin \| \| User" |Dynamická| Konfigurace zabezpečení pro přepnutí podrobného ServicePlacementho HealthReporting. |
|UnprovisionApplicationType |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro zrušení zřízení typu aplikace |
|UnprovisionFabric |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro odzřizování MSI nebo manifestu clusteru. |
|UnreliableTransportControl |řetězec, výchozí hodnota je admin. |Dynamická| Nespolehlivý přenos pro přidání a odebrání chování |
|Operace updateservice |řetězec, výchozí hodnota je admin. |Dynamická|Konfigurace zabezpečení pro aktualizace služby. |
|UpgradeApplication |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro spuštění nebo přerušení upgradu aplikace. |
|UpgradeComposeDeployment|řetězec, výchozí hodnota je admin.| Dynamická|Upgraduje nasazení vytváření. |
|UpgradeFabric |řetězec, výchozí hodnota je admin. |Dynamická| Konfigurace zabezpečení pro spouštění upgradů clusteru. |
|Nahrávání |řetězec, výchozí hodnota je admin. | Dynamická|Konfigurace zabezpečení pro operaci nahrání klienta úložiště imagí |

## <a name="securityclientcertificateissuerstores"></a>Zabezpečení/ClientCertificateIssuerStores

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, výchozí hodnota je None |Dynamická|Úložiště certifikátů vystavitele x509 pro klientské certifikáty; Název = clientIssuerCN; Value = seznam obchodů oddělených čárkami |

## <a name="securityclientx509names"></a>Zabezpečení/ClientX509Names

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, výchozí hodnota je None|Dynamická|Toto je seznam dvojic "název" a "hodnota". Každé "název" je subjektem běžný název nebo DnsName certifikátů x509 autorizovaných pro klientské operace. Pro dané "název", "value" je čárkami oddělený seznam kryptografických otisků certifikátů pro připnutí vystavitele, pokud není prázdný, musí být na seznamu přímo Vystavitel klientských certifikátů.|

## <a name="securityclustercertificateissuerstores"></a>Zabezpečení/ClusterCertificateIssuerStores

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, výchozí hodnota je None |Dynamická|Úložiště certifikátů vystavitele x509 pro certifikáty clusteru; Název = clusterIssuerCN; Value = seznam obchodů oddělených čárkami |

## <a name="securityclusterx509names"></a>Zabezpečení/ClusterX509Names

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, výchozí hodnota je None|Dynamická|Toto je seznam dvojic "název" a "hodnota". Každé "název" je subjektem běžný název nebo DnsName certifikátů x509 autorizovaných pro operace clusteru. Pro dané "název", "value" je čárkami oddělený seznam kryptografických otisků certifikátů pro připnutí vystavitele, pokud není prázdný, musí být přímým vystavitelem certifikátů clusteru v seznamu.|

## <a name="securityservercertificateissuerstores"></a>Zabezpečení/ServerCertificateIssuerStores

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, výchozí hodnota je None |Dynamická|Úložiště certifikátů vystavitele x509 pro certifikáty serveru; Název = serverIssuerCN; Value = seznam obchodů oddělených čárkami |

## <a name="securityserverx509names"></a>Zabezpečení/ServerX509Names

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, výchozí hodnota je None|Dynamická|Toto je seznam dvojic "název" a "hodnota". Každé "název" je subjektem běžný název nebo DnsName certifikátů x509 autorizovaných pro operace serveru. Pro dané "název", "value" je čárkami oddělený seznam kryptografických otisků certifikátů pro připnutí vystavitele, pokud není prázdný, musí být přímým vystavitelem certifikátů serveru v seznamu.|

## <a name="setup"></a>Nastavení

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ContainerNetworkName|řetězec, výchozí hodnota je ""| Static |Název sítě, který se má použít při nastavování sítě kontejneru|
|ContainerNetworkSetup|bool, výchozí hodnota je FALSE (Linux) a výchozí hodnota je TRUE (Windows).| Static |Určuje, zda se má nastavit síť kontejneru.|
|FabricDataRoot |Řetězec | Nepovolené |Service Fabric adresář data root. Výchozí hodnota pro Azure je d:\svcfab |
|FabricLogRoot |Řetězec | Nepovolené |Kořenový adresář protokolu Service Fabric. To je místo, kde jsou umístěny záznamy a záznamy SF. |
|NodesToBeRemoved|řetězec, výchozí hodnota je ""| Dynamická |Uzly, které by měly být odebrány jako součást upgradu konfigurace. (Jenom pro samostatná nasazení)|
|ServiceRunAsAccountName |Řetězec | Nepovolené |Název účtu, pod kterým se má spustit služba hostitele prostředků infrastruktury |
|SkipContainerNetworkResetOnReboot|logická hodnota, výchozí hodnota je FALSE.|NotAllowed|Bez ohledu na to, jestli se při restartování má přeskočit resetování sítě kontejneru|
|SkipFirewallConfiguration |Logická hodnota, výchozí hodnota je false. | Nepovolené |Určuje, jestli musí být nastavení brány firewall nastavené systémem, nebo ne. To platí jenom v případě, že používáte bránu Windows Firewall. Pokud používáte brány firewall třetích stran, musíte otevřít porty pro systém a aplikace, které chcete použít. |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|Zprostředkovatelé |řetězec, výchozí hodnota je "DSTS" |Static|Seznam zprostředkovatelů ověřování tokenů oddělených čárkou, které se mají povolit (platné zprostředkovatele jsou: DSTS; AAD). V současné době může být povolen pouze jeden zprostředkovatel. |

## <a name="traceetw"></a>Trasování/trasování událostí pro Windows

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|Level |Int, výchozí hodnota je 4. | Dynamická |Trasovat úroveň trasování událostí pro Windows může přibírat hodnoty 1, 2, 3, 4. Aby byla podpora podporovaná, musíte udržet úroveň trasování 4. |

## <a name="transactionalreplicator"></a>TransactionalReplicator

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Čas v sekundách, výchozí hodnota je 0,015. | Static | Zadejte časový interval v sekundách. Určuje dobu, po kterou Replikátor po přijetí operace počká, než pošle zpět potvrzení. Jiné operace přijaté během tohoto časového období budou mít potvrzení, které se vrátí v rámci jedné zprávy – > snižují síťový provoz, ale můžou snížit propustnost replikátoru. |
|MaxCopyQueueSize |Uint, výchozí hodnota je 16384 | Static |Toto je maximální hodnota definující počáteční velikost fronty, která udržuje operace replikace. Všimněte si, že musí být mocninou 2. Pokud během běhu bude fronta růst s touto operací velikosti, bude omezena mezi primárními a sekundárními replikami. |
|MaxPrimaryReplicationQueueMemorySize |Uint, výchozí hodnota je 0 | Static |Toto je maximální hodnota primární fronty replikace v bajtech. |
|MaxPrimaryReplicationQueueSize |Uint, výchozí hodnota je 8192 | Static |Toto je maximální počet operací, které mohou existovat ve frontě primární replikace. Všimněte si, že musí být mocninou 2. |
|MaxReplicationMessageSize |Uint, výchozí hodnota je 52428800 | Static | Maximální velikost zprávy operací replikace. Výchozí hodnota je 50 MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, výchozí hodnota je 0 | Static |Toto je maximální hodnota fronty sekundární replikace v bajtech. |
|MaxSecondaryReplicationQueueSize |Uint, výchozí hodnota je 16384 | Static |Toto je maximální počet operací, které mohou existovat v sekundární frontě replikace. Všimněte si, že musí být mocninou 2. |
|ReplicatorAddress |řetězec, výchozí hodnota je "localhost: 0" | Static | Koncový bod ve formě řetězce "IP: port", který používá Replikátor Windows Fabric k navázání připojení k ostatním replikám za účelem odesílání/přijímání operací. |

## <a name="transport"></a>Přenos
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu** |**Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (60)|Static|Zadejte časový interval v sekundách. Časový limit pro nastavení připojení na straně příjmu (včetně vyjednávání zabezpečení v zabezpečeném režimu) |
|FrameHeaderErrorCheckingEnabled|logická hodnota, výchozí hodnota je TRUE.|Static|Výchozí nastavení pro kontrolu chyb v hlavičce snímku v nezabezpečeném režimu; nastavení součásti přepisuje toto. |
|MessageErrorCheckingEnabled|logická hodnota, výchozí hodnota je TRUE.|Static|Výchozí nastavení pro kontrolu chyb v záhlaví a textu zprávy v nezabezpečeném režimu; nastavení součásti přepisuje toto. |
|ResolveOption|řetězec, výchozí hodnota je "Neurčeno".|Static|Určuje, jak se přeloží plně kvalifikovaný název domény.  Platné hodnoty jsou "Neurčeno/IPv4/IPv6". |
|SendTimeout|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (300)|Dynamická|Zadejte časový interval v sekundách. Vypršel časový limit pro detekci zablokování připojení. Zprávy o chybách TCP nejsou v některých prostředí spolehlivé. To může být potřeba upravit podle dostupné šířky pásma sítě a velikosti odchozích dat ( \* MaxMessageSize \/ \* SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Logická hodnota, výchozí hodnota je true. |Static| Automatická akce cyklického dotazování a upgradu na základě souboru s cílovým stavem. |
|AutoupgradeInstallEnabled|Logická hodnota, výchozí hodnota je FALSE.|Static|Automatické dotazování, zřizování a instalaci akce upgradu kódu na základě souboru s cílovým stavem.|
|GoalStateExpirationReminderInDays|int, výchozí hodnota je 30.|Static|Nastaví počet zbývajících dnů, po kterých se má zobrazit připomenutí stavu cíle.|
|MinReplicaSetSize |Int, výchozí hodnota je 0 |Static |MinReplicaSetSize pro UpgradeOrchestrationService.|
|PlacementConstraints | řetězec, výchozí hodnota je "" |Static| PlacementConstraints pro UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Čas v sekundách, výchozí hodnota je MaxValue. |Static| Zadejte časový interval v sekundách. QuorumLossWaitDuration pro UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Čas v sekundách, výchozí hodnota je 60 minut.|Static| Zadejte časový interval v sekundách. ReplicaRestartWaitDuration pro UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Čas v sekundách, výchozí hodnota je 60 *24* 7 minut. |Static| Zadejte časový interval v sekundách. StandByReplicaKeepDuration pro UpgradeOrchestrationService. |
|TargetReplicaSetSize |Int, výchozí hodnota je 0 |Static |TargetReplicaSetSize pro UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Logická hodnota, výchozí hodnota je false. | Static|Nastavení pro provedení upgradu kódu vyžaduje schválení správcem, než budete pokračovat. |

## <a name="upgradeservice"></a>UpgradeService

| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|BaseUrl | řetězec, výchozí hodnota je "" |Static|BaseUrl pro UpgradeService. |
|ClusterId | řetězec, výchozí hodnota je "" |Static|ClusterId pro UpgradeService. |
|CoordinatorType | řetězec, výchozí hodnota je "WUTest"|Nepovolené|CoordinatorType pro UpgradeService. |
|MinReplicaSetSize | Int, výchozí hodnota je 2 |Nepovolené| MinReplicaSetSize pro UpgradeService. |
|OnlyBaseUpgrade | Logická hodnota, výchozí hodnota je false. |Dynamická|OnlyBaseUpgrade pro UpgradeService. |
|PlacementConstraints |řetězec, výchozí hodnota je "" |Nepovolené|PlacementConstraints pro upgrade služby. |
|PollIntervalInSeconds|Časový interval, výchozí hodnota je common:: TimeSpan:: FromSeconds (60) |Dynamická|Zadejte časový interval v sekundách. Interval mezi UpgradeService dotazem na operace správy ARM |
|TargetReplicaSetSize | Int, výchozí hodnota je 3 |Nepovolené| TargetReplicaSetSize pro UpgradeService. |
|TestCabFolder | řetězec, výchozí hodnota je "" |Static| TestCabFolder pro UpgradeService. |
|X509FindType | řetězec, výchozí hodnota je ""|Dynamická| X509FindType pro UpgradeService. |
|X509FindValue | řetězec, výchozí hodnota je "" |Dynamická| X509FindValue pro UpgradeService. |
|X509SecondaryFindValue | řetězec, výchozí hodnota je "" |Dynamická| X509SecondaryFindValue pro UpgradeService. |
|X509StoreLocation | řetězec, výchozí hodnota je "" |Dynamická| X509StoreLocation pro UpgradeService. |
|X509StoreName | řetězec, výchozí hodnota je my|Dynamická|X509StoreName pro UpgradeService. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacities
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup| UserServiceMetricCapacitiesMap, výchozí hodnota je None | Static | Kolekce omezení zásad správného řízení prostředků uživatelských služeb musí být statická, protože má vliv na logiku pro automatickou detekci. |

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu [Upgrade konfigurace clusteru Azure](service-fabric-cluster-config-upgrade-azure.md) a [Upgrade konfigurace samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md).
