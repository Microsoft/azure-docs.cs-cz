---
title: Změnit nastavení clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Tento článek popisuje nastavení prostředků infrastruktury a zásad upgradu prostředků infrastruktury, které můžete přizpůsobit.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/27/2018
ms.author: aljo
ms.openlocfilehash: ed904f7d4de9406e60de1652cefeb5bb84e5a1d8
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144034"
---
# <a name="customize-service-fabric-cluster-settings"></a>Nastavení clusteru Service Fabric
Tento článek popisuje, jak přizpůsobit různá nastavení prostředků infrastruktury pro cluster Service Fabric. Pro clustery hostovaných v Azure, můžete upravit pomocí nastavení [webu Azure portal](https://portal.azure.com) nebo s použitím šablony Azure Resource Manageru. Pro samostatné clustery upravit nastavení aktualizací ClusterConfig.json souborů a provádění upgradu na konfiguraci v clusteru. 

> [!NOTE]
> Ne všechna nastavení jsou k dispozici na portálu. V případě nastavení tady není k dispozici prostřednictvím portálu pro přizpůsobení pomocí šablony Azure Resource Manageru.
> 

## <a name="description-of-the-different-upgrade-policies"></a>Popis různých zásad upgradu

- **Dynamické** – změny v dynamickou konfiguraci nezpůsobí žádné restartování procesu Service Fabric procesů nebo procesy hostitele vaší služby. 
- **Statické** – změny v konfiguraci statického způsobí, že uzel Service Fabric restartovat, aby bylo možné změny využívat. Služby na uzlech se restartuje.
- **Hodnotu NotAllowed** – tato nastavení nelze změnit. Změna těchto nastavení vyžaduje zničení clusteru a vytvoření nového clusteru. 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Nastavení clusteru pomocí šablon Resource Manageru
Následující kroky ukazují, jak přidat nové nastavení *MaxDiskQuotaInMB* k *diagnostiky* části pomocí Průzkumníka prostředků Azure.

1. Přejděte na https://resources.azure.com.
2. Přejděte ke svému předplatnému tak, že rozbalíte **předplatná** -> **\<vašeho předplatného >** -> **resourceGroups**  ->   **\<Vaše skupina prostředků >** -> **poskytovatelé** -> **Microsoft.ServiceFabric**  ->  **clustery** -> **\<si název clusteru >**
3. V pravém horním rohu, vyberte **čtení a zápisu.**
4. Vyberte **upravit** a aktualizovat `fabricSettings` elementu JSON a přidejte nový prvek:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

V jednom z následujících způsobů pomocí Azure Resource Manageru můžete také upravit nastavení clusteru:

- Použití [webu Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) k exportu a aktualizace šablony správce prostředků.
- Použití [Powershellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) k exportu a aktualizovat šablonu Resource Manageru.
- Použití [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) k exportu a aktualizovat šablonu Resource Manageru.
- Použití Azure RM Powershellu [Set-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Set-AzureRmServiceFabricSetting) a [odebrat AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Remove-AzureRmServiceFabricSetting) příkazů ke změně nastavení přímo.
- Pomocí Azure CLI [az sf cluster nastavení](https://docs.microsoft.com/cli/azure/sf/cluster/setting) příkazů ke změně nastavení přímo.

## <a name="customize-cluster-settings-for-standalone-clusters"></a>Nastavení clusteru pro samostatné clustery
Samostatné clustery jsou nakonfigurovány pomocí souboru ClusterConfig.json. Další informace najdete v tématu [nastavení konfigurace pro samostatný cluster Windows](./service-fabric-cluster-manifest.md).

Přidání, aktualizace nebo odebrání nastavení v `fabricSettings` části [vlastnosti clusteru](./service-fabric-cluster-manifest.md#cluster-properties) v ClusterConfig.json části. 

Například následující kód JSON přidá nové nastavení *MaxDiskQuotaInMB* k *diagnostiky* části `fabricSettings`:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Po nastavení jste upravili v souboru ClusterConfig.json, postupujte podle pokynů v [upgradovat konfiguraci clusteru](./service-fabric-cluster-upgrade-windows-server.md#upgrade-the-cluster-configuration) používat nastavení pro váš cluster. 


Tady je seznam prostředků infrastruktury nastavení, které můžete přizpůsobit, uspořádané podle části.

## <a name="applicationgatewayhttp"></a>Brána ApplicationGateway/Http
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|řetězec, výchozí je "None"|Statická| Nelze ověřit certifikát serveru; úspěšná žádost. Odkazovat na config ServiceCertificateThumbprints čárkami oddělený seznam kryptografických otisků, ze vzdáleného certifikáty, které můžete důvěřovat reverzního proxy serveru. Odkazovat na config ServiceCommonNameAndIssuer subjektu název a vystavitele kryptografický otisk vzdálené certifikáty, které můžete důvěřovat reverzního proxy serveru. Další informace najdete v tématu [reverzního proxy serveru zabezpečené připojení](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, výchozí hodnota je 16384 |Dynamická| Dává velikost bloků dat v bajtech používá ke čtení textu. |
|CrlCheckingFlag|uint, výchozí je 0x40000000 |Dynamická| Příznaky pro ověřování řetězu certifikátů aplikace/služby; třeba kontroly seznamu odvolaných certifikátů 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY nastavení na hodnotu 0 Zakáže seznamu CRL kontrola, zda úplný seznam podporovaných hodnot je popsána pomocí dwFlags CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Čas v sekundách. Výchozí hodnota je 120 |Dynamická|Zadejte časový interval v sekundách.  Poskytuje výchozí časový limit žádosti pro požadavky http jsou zpracovávány v aplikaci bránu http. |
|ForwardClientCertificate|Logická hodnota, výchozí hodnotu FALSE|Dynamická|Když nastavenou na hodnotu false, reverzní proxy server nebude vyžadovat zadání klientského certifikátu. Pokud se žádost o certifikát klienta během metody handshake SSL a předávání kódovanou jako base64 nastavenou na hodnotu true, reverzní proxy řetězec formátu PEM do služby v hlavičce X-klienta Certificate.The služby s názvem převzít požadavek s odpovídající stavový kód až po kontrole data certifikátu. Pokud klient není k dispozici certifikát má hodnotu true, budou reverzní proxy server předat prázdnou hlavičku a nechat službu zpracování případu. Reverzní proxy server bude sloužit jako transparentní vrstvy. Další informace najdete v tématu [nastavení ověřování pomocí certifikátu klienta](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |řetězec, výchozí je "None" |Statická| Určuje typ zabezpečovací přihlašovací údaje pro použití na http aplikace brány koncový bod platné hodnoty jsou "None / X 509. |
|GatewayX509CertificateFindType |řetězec, výchozí je "FindByThumbprint" |Dynamická| Určuje, jak vyhledat certifikát v úložišti určené GatewayX509CertificateStoreName podporované hodnoty: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | řetězec, výchozí hodnota je "" |Dynamická| Hodnota filtru hledání používaná k nalezení certifikát brány aplikace http. Tento certifikát je nakonfigurovaná na koncový bod https a je také použít k ověření identity aplikace v případě potřeby prostřednictvím služeb. Nejdříve; je vyhledán FindValue a pokud, který neexistuje; FindValueSecondary se hledá. |
|GatewayX509CertificateFindValueSecondary | řetězec, výchozí hodnota je "" |Dynamická|Hodnota filtru hledání používaná k nalezení certifikát brány aplikace http. Tento certifikát je nakonfigurovaná na koncový bod https a je také použít k ověření identity aplikace v případě potřeby prostřednictvím služeb. Nejdříve; je vyhledán FindValue a pokud, který neexistuje; FindValueSecondary se hledá.|
|GatewayX509CertificateStoreName |řetězec, výchozí hodnota je "My" |Dynamická| Název úložiště certifikátů X.509, který obsahuje certifikát pro bránu aplikace http. |
|HttpRequestConnectTimeout|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(5)|Dynamická|Zadejte časový interval v sekundách.  Poskytuje časový limit připojení pro požadavky http odeslané z aplikace bránu http.  |
|IgnoreCrlOfflineError|Logická hodnota, výchozí hodnotu TRUE|Dynamická|Určuje, zda ignorovat chybu offline seznamu odvolaných certifikátů pro ověření certifikátu aplikace/služby. |
|IsEnabled |Logická hodnota, výchozí hodnota je false |Statická| Povolí nebo zakáže HttpApplicationGateway. HttpApplicationGateway je ve výchozím nastavení vypnutá. Tato konfigurace se musí nastavit, aby je. |
|NumberOfParallelOperations | Uint, výchozí je 5 000 |Statická|Počet čtení k odeslání do fronty http serveru. Tato volba určuje počet souběžných požadavků, které je možné splnit HttpGateway. |
|RemoveServiceResponseHeaders|řetězec, výchozí je "datum; Server"|Statická|Středníkem nebo čárkou oddělený seznam hlaviček odpovědí, které budou odebrány z odpovědi služby; Před předáním do klienta. Pokud je nastavené na prázdný řetězec. Předejte všechny hlavičky vrácené službou jako-je. tj Nepřepisovat data a serveru |
|ResolveServiceBackoffInterval |Čas v sekundách, výchozí hodnota je 5 |Dynamická|Zadejte časový interval v sekundách.  Poskytuje řešení výchozí regresní interval před opakováním nezdařené operace služby. |
|SecureOnlyMode|Logická hodnota, výchozí hodnotu FALSE|Dynamická| SecureOnlyMode: true: reverzní proxy server pouze přeposílají do služby, které publikovat zabezpečené koncové body. FALSE: reverzní proxy server může předat požadavky na zabezpečení/nezabezpečené koncových bodů. Další informace najdete v tématu [reverzního proxy koncový bod výběru logiky](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Čárkou oddělený seznam kryptografické otisky vzdálené certifikáty, které můžete důvěřovat reverzního proxy serveru. Další informace najdete v tématu [reverzního proxy serveru zabezpečené připojení](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>Brána ApplicationGateway/Http/ServiceCommonNameAndIssuer
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, výchozí hodnota je None|Dynamická| Předmět název a vystavitele kryptografický otisk vzdálené certifikáty, které můžete důvěřovat reverzního proxy serveru. Další informace najdete v tématu [reverzního proxy serveru zabezpečené připojení](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, výchozí je 0|Statická|MinReplicaSetSize pro BackupRestoreService |
|PlacementConstraints|řetězec, výchozí hodnota je ""|Statická|  PlacementConstraints BackupRestore služby |
|SecretEncryptionCertThumbprint|řetězec, výchozí hodnota je ""|Dynamická|Kryptografický otisk certifikátu šifrování s tajným X509 |
|SecretEncryptionCertX509StoreName|řetězec, výchozí hodnota je "My"|   Dynamická|    To znamená certifikát, který chcete použít pro šifrování a dešifrování úložiště certifikátů X.509 název přihlašovacích údajů, který se používá pro šifrování, dešifrování přihlašovacích údajů úložiště používá služba Backup Restore |
|TargetReplicaSetSize|int, výchozí je 0|Statická| TargetReplicaSetSize pro BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|EnableDefaultServicesUpgrade | Logická hodnota, výchozí hodnota je false |Dynamická|Povolte upgrade výchozích služeb během upgradu aplikace. Popisy výchozí služby přepsána po upgradu. |
|FabricUpgradeHealthCheckInterval |Čas v sekundách, výchozí hodnota je 60 |Dynamická|Frekvence stav kontroly během monitorovaných upgrade pro Fabric |
|FabricUpgradeStatusPollInterval |Čas v sekundách, výchozí hodnota je 60 |Dynamická|Frekvence dotazování na stav upgradu prostředků infrastruktury. Tato hodnota určuje frekvence aktualizace pro každé volání GetFabricUpgradeProgress |
|ImageBuilderTimeoutBuffer |Čas v sekundách, výchozí hodnota je 3 |Dynamická|Zadejte časový interval v sekundách. Množství času, aby pro Image Builder pro konkrétní vypršení časového limitu se vraťte do klienta. Pokud tuto vyrovnávací paměť je příliš malý. pak klienta vyprší časový limit než server a získá chybu obecný časový limit. |
|InfrastructureTaskHealthCheckRetryTimeout | Čas v sekundách, výchozí hodnota je 60 |Dynamická|Zadejte časový interval v sekundách. Množství času věnovat opakování nepovedlo kontroly stavu při následné zpracování úlohu infrastruktury. Sledování Kontrola stavu úspěch resetuje tento časovač. |
|InfrastructureTaskHealthCheckStableDuration | Čas v sekundách, výchozí hodnota je 0|Dynamická| Zadejte časový interval v sekundách. Množství času sledovat kontroly stavu po sobě jdoucích předaný před po zpracování úkolu infrastruktury dokončí úspěšně. Sledování selhání kontrolu resetuje tento časovač. |
|InfrastructureTaskHealthCheckWaitDuration |Čas v sekundách, výchozí hodnota je 0|Dynamická| Zadejte časový interval v sekundách. Množství času se má čekat před spuštěním kontroly stavu po následné zpracování úlohu infrastruktury. |
|InfrastructureTaskProcessingInterval | Čas v sekundách, výchozí hodnota je 10 |Dynamická|Zadejte časový interval v sekundách. Interval zpracování používán úkolem infrastruktury zpracování stavového stroje. |
|MaxCommunicationTimeout |Čas v sekundách, výchozí hodnota je 600 |Dynamická|Zadejte časový interval v sekundách. Maximální časový limit pro interní komunikaci mezi ClusterManager a další systémové služby (tj.); Služba pojmenování; "Správce převzetí služeb při selhání a atd.). Tento časový limit by měl být menší než globální MaxOperationTimeout (jak může existovat více komunikaci mezi součástmi systému pro každou operaci klienta). |
|MaxDataMigrationTimeout |Čas v sekundách, výchozí hodnota je 600 |Dynamická|Zadejte časový interval v sekundách. Maximální časový limit pro operace obnovení migrace dat, po úspěšném upgradu prostředků infrastruktury. |
|MaxOperationRetryDelay |Čas v sekundách, výchozí hodnota je 5|Dynamická| Zadejte časový interval v sekundách. Maximální zpoždění interní opakovaných pokusů při výskytu chyby. |
|MaxOperationTimeout |Čas v sekundách, výchozí hodnota je hodnota MaxValue |Dynamická| Zadejte časový interval v sekundách. Maximální globální časový limit pro operace na ClusterManager zpracování interně. |
|MaxTimeoutRetryBuffer | Čas v sekundách, výchozí hodnota je 600 |Dynamická|Zadejte časový interval v sekundách. Časový limit maximální operace při opakování interně z důvodu vypršení časového limitu je <Original Time out>  +  <MaxTimeoutRetryBuffer>. V krocích po MinOperationTimeout se přidá další časový limit. |
|MinOperationTimeout | Čas v sekundách, výchozí hodnota je 60 |Dynamická|Zadejte časový interval v sekundách. Minimální globální časový limit pro operace na ClusterManager zpracování interně. |
|MinReplicaSetSize |Int, výchozí hodnota je 3 |Nepovolené|MinReplicaSetSize pro ClusterManager. |
|PlacementConstraints | řetězec, výchozí hodnota je "" |Nepovolené|PlacementConstraints pro ClusterManager. |
|QuorumLossWaitDuration |Čas v sekundách, výchozí hodnota je hodnota MaxValue |Nepovolené| Zadejte časový interval v sekundách. QuorumLossWaitDuration pro ClusterManager. |
|ReplicaRestartWaitDuration |Čas v sekundách, výchozí hodnota je (60.0 * 30)|Nepovolené|Zadejte časový interval v sekundách. ReplicaRestartWaitDuration pro ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Čas v sekundách, výchozí hodnota je 1200 |Dynamická| Zadejte časový interval v sekundách. Pokud ReplicaSetCheckTimeout nastavená na maximální hodnotu DWORD; pak je přepsáno hodnotou tato konfigurace za účelem vrácení zpět. Hodnota použitá vpřed je nikdy přepsána. |
|SkipRollbackUpdateDefaultService | Logická hodnota, výchozí hodnota je false |Dynamická|SP přeskočí vracení k uloženým standardním aktualizace výchozí služby během vrácení upgradu aplikace. |
|StandByReplicaKeepDuration | Čas v sekundách, výchozí hodnota je (3600.0 * 2)|Nepovolené|Zadejte časový interval v sekundách. StandByReplicaKeepDuration pro ClusterManager. |
|TargetReplicaSetSize |Int, výchozí hodnota je 7 |Nepovolené|TargetReplicaSetSize pro ClusterManager. |
|UpgradeHealthCheckInterval |Čas v sekundách, výchozí hodnota je 60 |Dynamická|Frekvence stavu kontroluje během upgradu monitorovanou aplikaci |
|UpgradeStatusPollInterval |Čas v sekundách, výchozí hodnota je 60 |Dynamická|Frekvence dotazování na stav upgradu aplikace. Tato hodnota určuje frekvence aktualizace pro každé volání GetApplicationUpgradeProgress |

## <a name="common"></a>Společné
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Čas v sekundách, výchozí hodnota je 1 |Dynamická|Zadejte časový interval v sekundách. Interval monitorování výkonu. Nastavení na hodnotu 0 nebo záporná zakáže monitorování. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, výchozí hodnota je None|Dynamická|Určuje, že defragmentace zásad řídí při vyprazdňování uzlů. Pro danou metriku 0 označuje, že SF pokusit o defragmentaci uzly rovnoměrně napříč aktualizačními doménami a doménami selhání; 1 znamená jen, že musí být defragmentovat uzly |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, výchozí hodnota je None|Dynamická|Určuje nastavení metriky, který se má použít pro defragmentaci a ne pro vyrovnávání zatížení. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, výchozí hodnota je None|Dynamická|Určuje počet volné uzly, které jsou potřeba vzít v úvahu clusteru defragmentovat zadáním buď procent v rozsahu [0.0-1.0) nebo počtu uzlů prázdné jako číslo > = 1.0 |

## <a name="diagnostics"></a>Diagnostika
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AppDiagnosticStoreAccessRequiresImpersonation |Logická hodnota, výchozí hodnota je true | Dynamická |Určuje, jestli je potřeba zosobnění, při přístupu k diagnostiky ukládá jménem aplikace. |
|AppEtwTraceDeletionAgeInDays |Int, výchozí hodnota je 3 | Dynamická |Počet dní, po jejichž uplynutí jsme odstranit staré soubory ETL trasování událostí pro Windows trasování aplikací obsahující. |
|ApplicationLogsFormatVersion |int, výchozí je 0 | Dynamická |Verze aplikace protokoluje formátu. Podporované hodnoty jsou 0 a 1. Verze 1 obsahuje více polí ze záznamu události trasování událostí pro Windows než verze 0. |
|ClusterId |Řetězec | Dynamická |Jedinečné id clusteru. Tím se vygeneruje, když se cluster vytvoří. |
|ConsumerInstances |Řetězec | Dynamická |Seznam instancí DCA příjemce. |
|DiskFullSafetySpaceInMB |Int, výchozí hodnota je 1024 | Dynamická |Zbývající místo na disku v MB k ochraně před používá DCA. |
|EnableCircularTraceSession |Logická hodnota, výchozí hodnota je false | Statická |Příznak určuje, zda má být použita cyklické trasování relace. |
|EnableTelemetry |Logická hodnota, výchozí hodnota je true | Dynamická |Tím vytvořím k zapnutí nebo vypnutí telemetrie. |
|MaxDiskQuotaInMB |Int, výchozí hodnota je 65536 | Dynamická |Disková kvóta v MB pro Windows Fabric protokolových souborech. |
|ProducerInstances |Řetězec | Dynamická |Seznam instancí DCA výrobce. |

## <a name="dnsservice"></a>Služba DnsService
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|Logická hodnota, výchozí hodnotu FALSE|Statická|Příznak povolení podpory pro dotazy DNS pro dělené služby. Tato funkce je ve výchozím nastavení vypnuta. Další informace najdete v tématu [služba DNS Service Fabricu.](service-fabric-dnsservice.md)|
|InstanceCount|je int, výchozí hodnota -1|Statická|Výchozí hodnota je -1, což znamená, že služba DnsService běží na všech uzlech. OneBox musí to být nastavena na hodnotu 1, protože služba DnsService používá dobře známý port 53, takže nemůže mít více instancí ve stejném počítači.|
|IsEnabled|Logická hodnota, výchozí hodnotu FALSE|Statická|Povolí nebo zakáže služba DnsService. Služba DnsService je ve výchozím nastavení vypnutá. Tato konfigurace se musí nastavit, aby je. |
|PartitionPrefix|řetězec, výchozí hodnota je "--"|Statická|Určuje předponu řetězce oddílu v dotazy DNS pro dělené služby. Hodnota: <ul><li>By měl být kompatibilní s RFC, protože ji budete součást dotazu DNS.</li><li>Nesmí obsahovat tečku, ".", jak je tečka, dochází ke kolizím s chování příponu DNS.</li><li>Nesmí být delší než 5 znaků.</li><li>Nemůže být prázdný řetězec.</li><li>Pokud je přepsána PartitionPrefix nastavení, pak PartitionSuffix musí přepsat a naopak.</li></ul>Další informace najdete v tématu [služba DNS Service Fabricu.](service-fabric-dnsservice.md).|
|PartitionSuffix|řetězec, výchozí hodnota je ""|Statická|Určuje řetězcovou hodnotu přípony oddílu v dotazy DNS pro dělené služby. Hodnota: <ul><li>By měl být kompatibilní s RFC, protože ji budete součást dotazu DNS.</li><li>Nesmí obsahovat tečku, ".", jak je tečka, dochází ke kolizím s chování příponu DNS.</li><li>Nesmí být delší než 5 znaků.</li><li>Pokud je přepsána PartitionPrefix nastavení, pak PartitionSuffix musí přepsat a naopak.</li></ul>Další informace najdete v tématu [služba DNS Service Fabricu.](service-fabric-dnsservice.md). |

## <a name="fabricclient"></a>FabricClient
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Čas v sekundách, výchozí hodnota je 2 |Dynamická|Zadejte časový interval v sekundách. Interval časového limitu připojení pro každého klienta se pokusí otevřít připojení k bráně.|
|HealthOperationTimeout |Čas v sekundách, výchozí hodnota je 120 |Dynamická|Zadejte časový interval v sekundách. Časový limit pro sestavy zpráva odeslaná do Správce stavu. |
|HealthReportRetrySendInterval |Čas v sekundách, výchozí hodnota je 30 |Dynamická|Zadejte časový interval v sekundách. Interval, ve kterém hlásí, že součást znovu odešle nahromaděné stavových sestav pro správce stavu. |
|HealthReportSendInterval |Čas v sekundách, výchozí hodnota je 30 |Dynamická|Zadejte časový interval v sekundách. Interval, ve kterém hlásí, že komponenta odešle nahromaděné stavových sestav pro správce stavu. |
|KeepAliveIntervalInSeconds |Int, výchozí hodnota je 20 |Statická|Interval, jakou odešle FabricClient přenosu zpráv keep-alive k bráně. Pro 0; keepAlive je zakázaná. Musí být kladná hodnota. |
|MaxFileSenderThreads |Uint, výchozí hodnota je 10 |Statická|Maximální počet souborů, které se přenáší paralelně. |
|NodeAddresses |řetězec, výchozí hodnota je "" |Statická|Kolekce adres (připojovací řetězce) na různých uzlech, které lze použít ke komunikaci se službou názvy. Zpočátku připojení klienta výběrem jedné z adresy náhodně. Pokud je zadán více než jeden řetězec připojení a připojení se nezdaří z důvodu vypršení časového limitu; a komunikace přepínače klienta, která se mají použít adresa dalšího postupně. Zobrazit podrobné informace na sémantiku opakované pokusy opakovat pojmenování adresa služby. |
|PartitionLocationCacheLimit |Int, výchozí hodnota je 100 000 |Statická|Počet oddílů v mezipaměti pro službu rozlišení (nastavena na hodnotu 0 pro žádné omezení). |
|RetryBackoffInterval |Čas v sekundách, výchozí hodnota je 3 |Dynamická|Zadejte časový interval v sekundách. Regresní interval před opakováním operace. |
|ServiceChangePollInterval |Čas v sekundách, výchozí hodnota je 120 |Dynamická|Zadejte časový interval v sekundách. Interval mezi po sobě jdoucích hlasování pro službu se změní z klienta do brány pro zpětná volání oznámení změn registrovanou službu. |

## <a name="fabrichost"></a>Hostitele prostředků infrastruktury
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, výchozí hodnota je 10 |Dynamická|Toto je maximální počet, pro kterou systém bude opakovat neúspěšné aktivace, než se ukončí. |
|ActivationMaxRetryInterval |Čas v sekundách, výchozí hodnota je 300 |Dynamická|Zadejte časový interval v sekundách. Maximální interval opakování pro aktivaci. Při každé průběžné selhání interval opakování se počítá jako Min (ActivationMaxRetryInterval; Průběžný počet selhání * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Čas v sekundách, výchozí hodnota je 5 |Dynamická|Zadejte časový interval v sekundách. Interval omezení rychlosti při každé aktivaci selhání; při selhání každý průběžné aktivace systému bude opakovat akci během aktivace pro až MaxActivationFailureCount. Interval opakování na každou akci je produkt průběžné aktivace se nezdařila a aktivace regresní interval. |
|EnableRestartManagement |Logická hodnota, výchozí hodnota je false |Dynamická|To je umožnit restartování serveru. |
|EnableServiceFabricAutomaticUpdates |Logická hodnota, výchozí hodnota je false |Dynamická|Toto je chcete povolit automatické aktualizace prostředků infrastruktury prostřednictvím Windows Update. |
|EnableServiceFabricBaseUpgrade |Logická hodnota, výchozí hodnota je false |Dynamická|To je umožnit základní aktualizace pro server. |
|StartTimeout |Čas v sekundách, výchozí hodnota je 300 |Dynamická|Zadejte časový interval v sekundách. Časový limit pro spuštění fabricactivationmanager. |
|StopTimeout |Čas v sekundách, výchozí hodnota je 300 |Dynamická|Zadejte časový interval v sekundách. Časový limit pro hostovanou službu Aktivace. deaktivace a upgrade. |

## <a name="fabricnode"></a>FabricNode
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |řetězec, výchozí je "FindByThumbprint" |Dynamická|Určuje, jak vyhledat certifikát v úložišti určené ClientAuthX509StoreName podporované hodnoty: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |řetězec, výchozí hodnota je "" | Dynamická|Hodnota filtru hledání používaná k nalezení certifikát pro roli správce výchozí FabricClient. |
|ClientAuthX509FindValueSecondary |řetězec, výchozí hodnota je "" |Dynamická|Hodnota filtru hledání používaná k nalezení certifikát pro roli správce výchozí FabricClient. |
|ClientAuthX509StoreName |řetězec, výchozí hodnota je "My" |Dynamická|Název úložiště certifikátu X.509, který obsahuje certifikát pro roli správce výchozí FabricClient. |
|ClusterX509FindType |řetězec, výchozí je "FindByThumbprint" |Dynamická|Určuje, jak vyhledat certifikát clusteru v úložišti určené ClusterX509StoreName podporované hodnoty: "FindByThumbprint"; "FindBySubjectName" s "FindBySubjectName"; Pokud existuje více shod použije se ten nejvzdálenější vypršení platnosti. |
|ClusterX509FindValue |řetězec, výchozí hodnota je "" |Dynamická|Hodnota filtru hledání používaná k nalezení certifikát clusteru. |
|ClusterX509FindValueSecondary |řetězec, výchozí hodnota je "" |Dynamická|Hodnota filtru hledání používaná k nalezení certifikát clusteru. |
|ClusterX509StoreName |řetězec, výchozí hodnota je "My" |Dynamická|Název úložiště certifikátů X.509, který obsahuje certifikát clusteru pro zabezpečení komunikace mezi clustery. |
|EndApplicationPortRange |int, výchozí je 0 |Statická|End (ne inkluzivní) porty aplikace spravuje hostování subsystému. Požadováno, pokud EndpointFilteringEnabled true v Hosting. |
|ServerAuthX509FindType |řetězec, výchozí je "FindByThumbprint" |Dynamická|Určuje, jak vyhledat certifikát serveru v úložišti určené ServerAuthX509StoreName podporované hodnoty: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |řetězec, výchozí hodnota je "" |Dynamická|Hodnota filtru hledání používaná k nalezení certifikát serveru. |
|ServerAuthX509FindValueSecondary |řetězec, výchozí hodnota je "" |Dynamická|Hodnota filtru hledání používaná k nalezení certifikát serveru. |
|ServerAuthX509StoreName |řetězec, výchozí hodnota je "My" |Dynamická|Název úložiště certifikátů X.509, který obsahuje certifikát serveru pro službu bezbolestný. |
|StartApplicationPortRange |int, výchozí je 0 |Statická|Počáteční porty aplikace spravuje hostování subsystému. Požadováno, pokud EndpointFilteringEnabled true v Hosting. |
|StateTraceInterval |Čas v sekundách, výchozí hodnota je 300 |Statická|Zadejte časový interval v sekundách. Interval pro stav uzlu na každém uzlu a novějšími uzly na největší FM/FMM trasování. |
|UserRoleClientX509FindType |řetězec, výchozí je "FindByThumbprint" |Dynamická|Určuje, jak vyhledat certifikát v úložišti určené UserRoleClientX509StoreName podporované hodnoty: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |řetězec, výchozí hodnota je "" |Dynamická|Hodnota filtru hledání používaná k nalezení certifikát pro výchozí roli uživatele FabricClient. |
|UserRoleClientX509FindValueSecondary |řetězec, výchozí hodnota je "" |Dynamická|Hodnota filtru hledání používaná k nalezení certifikát pro výchozí roli uživatele FabricClient. |
|UserRoleClientX509StoreName |řetězec, výchozí hodnota je "My" |Dynamická|Název úložiště certifikátu X.509, který obsahuje certifikát pro výchozí roli uživatele FabricClient. |

## <a name="failovermanager"></a>FailoverManager
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|BuildReplicaTimeLimit|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(3600)|Dynamická|Zadejte časový interval v sekundách. Časový limit pro vytvoření stavové repliky; po jejímž uplynutí se iniciovalo sestava stavu upozornění |
|ClusterPauseThreshold|int, výchozí je 1|Dynamická|Pokud počet uzlů v systému přejděte pod tuto hodnotu poté umístění; načíst vyrovnávání; a převzetí služeb při selhání se zastaví. |
|CreateInstanceTimeLimit|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(300)|Dynamická|Zadejte časový interval v sekundách. Časový limit pro vytvoření nestavové instance; po jejímž uplynutí se iniciovalo sestava stavu upozornění |
|ExpectedClusterSize|int, výchozí je 1|Dynamická|Při prvním spuštění clusteru. počká převzetí služeb při selhání pro tuto mnoha uzly k hlášení sami si před zahájením umístění jiných služeb; včetně systémových služeb jako pojmenování. Doba trvání clusteru spustit; zvýšení hodnoty tuto zvyšuje. ale zabraňuje počáteční uzly z Přetěžovaná a také další přesuny, které bude nutné jako více uzlů do režimu online. Tato hodnota by měla obecně nastavena na malá část clusteru počáteční velikost. |
|ExpectedNodeDeactivationDuration|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamická|Zadejte časový interval v sekundách. Toto je očekávané době trvání pro uzel k deaktivaci v dokončení. |
|ExpectedNodeFabricUpgradeDuration|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamická|Zadejte časový interval v sekundách. Toto je očekávané době trvání pro uzel k upgradu při upgradu Windows Fabric. |
|ExpectedReplicaUpgradeDuration|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamická|Zadejte časový interval v sekundách. Toto je očekávané době trvání pro všechny repliky k upgradu na uzlu během upgradu aplikace. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|Logická hodnota, výchozí hodnotu TRUE|Dynamická|Pokud nastavenou na hodnotu true; repliky s cílovou velikost sady replik 1 budou moci přesunout během upgradu. |
|MinReplicaSetSize|Int, výchozí hodnota je 3|Nepovolené|Toto je velikost minimální sady replik pro převzetí služeb při selhání. Pokud klesne pod tuto hodnotu; počet aktivní repliky převzetí služeb při selhání převzetí služeb při selhání, bude taková změny do clusteru, dokud alespoň minimální počet replik se obnoví. |
|PlacementConstraints|řetězec, výchozí hodnota je ""|Nepovolené|Žádné omezení umístění pro repliky Správce převzetí služeb při selhání |
|PlacementTimeLimit|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(600)|Dynamická|Zadejte časový interval v sekundách. Časový limit pro dosažení počtu cílových replik; po jejímž uplynutí se iniciovalo sestava stavu upozornění |
|QuorumLossWaitDuration |Čas v sekundách, výchozí hodnota je hodnota MaxValue |Dynamická|Zadejte časový interval v sekundách. Toto je maximální dobu trvání, pro kterou jsou povoleny oddíl a být ve stavu ztrátě kvora. Pokud oddíl je stále ve ztrátě kvora po tuto dobu trvání; oddíl je obnovena ze ztráty kvora vycházet dolů repliky jako ztracené. Všimněte si, že to může potenciálně dojít ke ztrátě dat. |
|ReconfigurationTimeLimit|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(300)|Dynamická|Zadejte časový interval v sekundách. Časový limit pro změnu konfigurace; po jejímž uplynutí se iniciovalo sestava stavu upozornění |
|ReplicaRestartWaitDuration|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(60.0 * 30)|Nepovolené|Zadejte časový interval v sekundách. Toto je ReplicaRestartWaitDuration pro FMService |
|StandByReplicaKeepDuration|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|Nepovolené|Zadejte časový interval v sekundách. Toto je StandByReplicaKeepDuration pro FMService |
|TargetReplicaSetSize|Int, výchozí hodnota je 7|Nepovolené|Toto je cílový počet převzetí služeb při selhání replik, které bude udržovat Windows Fabric. Větší číslo má za následek vyšší spolehlivost dat převzetí služeb při selhání; s na úkor výkonu malé. |
|UserMaxStandByReplicaCount |int, výchozí je 1 |Dynamická|Výchozí maximální počet replik StandBy, které systém uchovává pro uživatele služby. |
|UserReplicaRestartWaitDuration |Čas v sekundách, výchozí hodnota je 60.0 * 30 |Dynamická|Zadejte časový interval v sekundách. Když repliku trvalý ocitne mimo provoz; Windows Fabric počká na této hodnotě duration repliky a navrhněte zpět před vytvořením nové repliky nahrazení (které by vyžadovaly kopii stavu). |
|UserStandByReplicaKeepDuration |Čas v sekundách, výchozí hodnota je 3600.0 * 24 * 7 |Dynamická|Zadejte časový interval v sekundách. Když repliku trvalý vrátit z dolů stavu; To může mít již bylo nahrazeno. Tento časovač Určuje, jak dlouho převzetí služeb při selhání budete mít pohotovostní repliky před budou zrušeny. |

## <a name="faultanalysisservice"></a>FaultAnalysisService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, výchozí hodnota je 604800 |Statická| To je přibližně jak dlouho se mají uchovávat akce, které jsou ve stavu terminálu. To také závisí na StoredActionCleanupIntervalInSeconds; protože práce, kterou čištění se provádí jenom na tohoto intervalu. 604800 je 7 dní. |
|DataLossCheckPollIntervalInSeconds|int, výchozí je 5|Statická|Toto je doba mezi kontroly, které systém provádí při čekání na ztrátě dat, která se provede. Počet pokusů, které vrátí číslo ztráta dat za interní iterace je DataLossCheckWaitDurationInSeconds/this. |
|DataLossCheckWaitDurationInSeconds|int, výchozí je 25|Statická|Celkové množství času; během několika sekund; bude systém čekat ztráty dat, která se provede. Používá se interně při volání rozhraní api StartPartitionDataLossAsync(). |
|MinReplicaSetSize |int, výchozí je 0 |Statická|MinReplicaSetSize pro FaultAnalysisService. |
|PlacementConstraints | řetězec, výchozí hodnota je ""|Statická| PlacementConstraints pro FaultAnalysisService. |
|QuorumLossWaitDuration | Čas v sekundách, výchozí hodnota je hodnota MaxValue |Statická|Zadejte časový interval v sekundách. QuorumLossWaitDuration pro FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, výchozí je 600|Statická|Tento parametr se používá při ztrátě dat volání rozhraní api. Určuje, jak dlouho bude systém čekat repliky získáte po odebrání repliky vyvolán interně v něm. |
|ReplicaRestartWaitDuration |Čas v sekundách, výchozí hodnota je 60 minut|Statická|Zadejte časový interval v sekundách. ReplicaRestartWaitDuration pro FaultAnalysisService. |
|StandByReplicaKeepDuration| Čas v sekundách, výchozí hodnota je (60*24*7) minut |Statická|Zadejte časový interval v sekundách. StandByReplicaKeepDuration pro FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, výchozí hodnota je 3600 |Statická|To je, jak často se vyčistí úložiště. Pouze akce přechodu k ve stavu terminálu; a dokončení alespoň CompletedActionKeepDurationInSeconds před bude odebrána. |
|StoredChaosEventCleanupIntervalInSeconds | Int, výchozí hodnota je 3600 |Statická|To je, jak často se bude auditovat úložišti pro vyčištění Pokud počet událostí, které je více než 30000; čištění se rozjíždí. |
|TargetReplicaSetSize |int, výchozí je 0 |Statická|NOT_PLATFORM_UNIX_START TargetReplicaSetSize pro FaultAnalysisService. |

## <a name="federation"></a>metadata
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|GlobalTicketLeaseDuration|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(300)|Statická|Zadejte časový interval v sekundách. Uzly v clusteru potřeba, abyste se voličů globální zapůjčení. Voliče odešlete své globální zapůjčení mohly rozšířit v clusteru pro této hodnotě duration. Pokud vyprší doba trvání; potom dojde ke ztrátě zapůjčení. Ztrátou kvora zapůjčení způsobí, že uzel spustit metodu Abandon clusteru; pomocí služeb při selhání pro příjem komunikace s Kvorum uzlů v tomto časovém období.  Tato hodnota musí být upravena v závislosti na velikosti clusteru. |
|LeaseDuration |Čas v sekundách, výchozí hodnota je 30 |Dynamická|Doba, po kterou má platnost zapůjčení mezi uzlem a jeho okolím. |
|LeaseDurationAcrossFaultDomain |Čas v sekundách, výchozí hodnota je 30 |Dynamická|Doba trvání zapůjčení vydrží mezi uzlem a jeho okolím napříč doménami selhání. |

## <a name="filestoreservice"></a>FileStoreService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Logická hodnota, výchozí hodnotu TRUE|Dynamická|Konfigurace k určení, jestli služba úložiště souborů přijímá nahrávání bloků dat na základě souborů, nebo ne během kopírování balíčku aplikace. |
|AnonymousAccessEnabled | Logická hodnota, výchozí hodnota je true |Statická|Povolí nebo zakáže anonymní přístup ke sdíleným složkám FileStoreService. |
|CommonName1Ntlmx509CommonName|řetězec, výchozí hodnota je ""|Statická| Běžný název X509 certifikát používaný k vygenerování HMAC na CommonName1NtlmPasswordSecret při použití ověřování NTLM |
|CommonName1Ntlmx509StoreLocation|řetězec, výchozí je "LocalMachine"|Statická|Umístění úložiště X509 certifikát používaný k vygenerování HMAC na CommonName1NtlmPasswordSecret při použití ověřování NTLM |
|CommonName1Ntlmx509StoreName|řetězec, výchozí je "MY"| Statická|Název úložiště X509 certifikát používaný k vygenerování HMAC na CommonName1NtlmPasswordSecret při použití ověřování NTLM |
|CommonName2Ntlmx509CommonName|řetězec, výchozí hodnota je ""|Statická|Běžný název X509 certifikát používaný k vygenerování HMAC na CommonName2NtlmPasswordSecret při použití ověřování NTLM |
|CommonName2Ntlmx509StoreLocation|řetězec, výchozí je "LocalMachine"| Statická|Umístění úložiště X509 certifikát používaný k vygenerování HMAC na CommonName2NtlmPasswordSecret při použití ověřování NTLM |
|CommonName2Ntlmx509StoreName|řetězec, výchozí je "MY"|Statická| Název úložiště X509 certifikát používaný k vygenerování HMAC na CommonName2NtlmPasswordSecret při použití ověřování NTLM |
|CommonNameNtlmPasswordSecret|SecureString, výchozí hodnota je Common::SecureString("")| Statická|Heslo tajný klíč, který používá jako počáteční hodnota generovaného stejné heslo při použití ověřování NTLM |
|GenerateV1CommonNameAccount| Logická hodnota, výchozí hodnotu TRUE|Statická|Určuje, jestli se má generovat účet s algoritmem generování V1 jméno uživatele. Spouští se s platformou Service Fabric verze 6.1; vždy se vytvoří účet s verze 2. generace. Účet V1 je nezbytné pro upgrade z/do verze, která nepodporují generování V2 (před 6.1).|
|MaxCopyOperationThreads | Uint, výchozí hodnota je 0 |Dynamická| Maximální počet paralelních souborů, který sekundární můžete zkopírovat z primární. '0' == počet jader. |
|MaxFileOperationThreads | Uint, výchozí hodnota je 100 |Statická| Maximální počet paralelních vláken povoleno provádět FileOperations (Copy/Move) v primární. '0' == počet jader. |
|MaxRequestProcessingThreads | Uint, výchozí hodnota je 200 |Statická|Maximální počet paralelních vláken zpracování požadavků v primární povoleno. '0' == počet jader. |
|MaxSecondaryFileCopyFailureThreshold | Uint, výchozí hodnota je 25|Dynamická|Maximální počet opakovaných pokusů kopírování souborů na sekundárním, než se ukončí. |
|MaxStoreOperations | Uint, výchozí hodnota je 4096 |Statická|Maximální počet operací transakce paralelní úložiště povolené pro primární. '0' == počet jader. |
|NamingOperationTimeout |Čas v sekundách, výchozí hodnota je 60 |Dynamická|Zadejte časový interval v sekundách. K provedení operace názvů vypršel časový limit. |
|PrimaryAccountNTLMPasswordSecret | SecureString, výchozí hodnota je prázdná |Statická| Heslo tajný klíč, který používá jako počáteční hodnoty pro generované stejné heslo při použití ověřování NTLM. |
|PrimaryAccountNTLMX509StoreLocation | řetězec, výchozí je "LocalMachine"|Statická| Umístění úložiště X509 certifikát používaný k vygenerování HMAC na PrimaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|PrimaryAccountNTLMX509StoreName | řetězec, výchozí je "MY"|Statická| Název úložiště X509 certifikát používaný k vygenerování HMAC na PrimaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|PrimaryAccountNTLMX509Thumbprint | řetězec, výchozí hodnota je ""|Statická|Kryptografický otisk X509 certifikát používaný k vygenerování HMAC na PrimaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|PrimaryAccountType | řetězec, výchozí hodnota je "" |Statická|Primární AccountType instančního objektu pro seznam ACL FileStoreService sdílí. |
|PrimaryAccountUserName | řetězec, výchozí hodnota je "" |Statická|Primární účet uživatelské jméno instančního objektu pro seznam ACL sdílí FileStoreService. |
|PrimaryAccountUserPassword | SecureString, výchozí hodnota je prázdná |Statická|Primární účet heslo instančního objektu pro seznam ACL sdílí FileStoreService. |
|QueryOperationTimeout | Čas v sekundách, výchozí hodnota je 60 |Dynamická|Zadejte časový interval v sekundách. Časový limit pro provedení operace dotazu. |
|SecondaryAccountNTLMPasswordSecret | SecureString, výchozí hodnota je prázdná |Statická| Heslo tajný klíč, který používá jako počáteční hodnoty pro generované stejné heslo při použití ověřování NTLM. |
|SecondaryAccountNTLMX509StoreLocation | řetězec, výchozí je "LocalMachine" |Statická|Umístění úložiště X509 certifikát používaný k vygenerování HMAC na SecondaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|SecondaryAccountNTLMX509StoreName | řetězec, výchozí je "MY" |Statická|Název úložiště X509 certifikát používaný k vygenerování HMAC na SecondaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|SecondaryAccountNTLMX509Thumbprint | řetězec, výchozí hodnota je ""| Statická|Kryptografický otisk X509 certifikát používaný k vygenerování HMAC na SecondaryAccountNTLMPasswordSecret při použití ověřování NTLM. |
|SecondaryAccountType | řetězec, výchozí hodnota je ""|Statická| Sekundární AccountType instančního objektu pro seznam ACL FileStoreService sdílí. |
|SecondaryAccountUserName | řetězec, výchozí hodnota je ""| Statická|Sekundární účet uživatelské jméno instančního objektu pro seznam ACL sdílí FileStoreService. |
|SecondaryAccountUserPassword | SecureString, výchozí hodnota je prázdná |Statická|Sekundární účet heslo instančního objektu pro seznam ACL sdílí FileStoreService. |

## <a name="healthmanager"></a>HealthManager
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Logická hodnota, výchozí hodnota je false |Statická|Zásady pro vyhodnocení stavu clusteru: Povolit za vyhodnocování stavu typu aplikace. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Logická hodnota, výchozí hodnota je false |Statická|Zásady pro vyhodnocení stavu clusteru: upozornění jsou považována za chyby. |
|MaxPercentUnhealthyApplications | int, výchozí je 0 |Statická|Zásady pro vyhodnocení stavu clusteru: maximální procento aplikací není v pořádku, povolen pro cluster se stavem v pořádku. |
|MaxPercentUnhealthyNodes | int, výchozí je 0 |Statická|Zásady pro vyhodnocení stavu clusteru: maximální procento uzlů, není v pořádku, povolen pro cluster se stavem v pořádku. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|Int, výchozí hodnota je 10|Statická|Vyhodnocení zásad upgradu stav clusteru: maximální procento rozdílových uzlů není v pořádku, povolen pro cluster se stavem v pořádku |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, výchozí je 15|Statická|Vyhodnocení zásad upgradu stav clusteru: maximální procento rozdílových uzlů není v pořádku v upgradovací doméně povolený pro cluster se stavem v pořádku |

## <a name="hosting"></a>Hostování
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Celé číslo, výchozí je 10 |Dynamická|Počet pokusů, které systém opakování se nezdařila aktivace, než se ukončí. |
|ActivationMaxRetryInterval |Čas v sekundách, výchozí hodnota je 300 |Dynamická|Na každé průběžné aktivace se nezdařila systém zopakuje pokus o aktivaci pro až ActivationMaxFailureCount. ActivationMaxRetryInterval Určuje časový interval čekání před opakováním po každé aktivace se nezdařila |
|ActivationRetryBackoffInterval |Čas v sekundách, výchozí hodnota je 5 |Dynamická|Interval omezení rychlosti při každé aktivaci selhání; Na každé průběžné aktivace se nezdařila systém zopakuje pokus o aktivaci pro až MaxActivationFailureCount. Interval opakování na každou akci je produkt průběžné aktivace se nezdařila a aktivace regresní interval. |
|ActivationTimeout| Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(180)|Dynamická| Zadejte časový interval v sekundách. Časový limit pro aktivaci aplikace; deaktivace a upgrade. |
|ApplicationHostCloseTimeout| Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(120)|Dynamická| Zadejte časový interval v sekundách. Když se v svým zjistí Fabric ukončovací aktivované procesy; FabricRuntime zavře všechny repliky v procesu hostitele (hostitele aplikace) daného uživatele. Toto je časový limit pro operaci zavření. |
|ApplicationUpgradeTimeout| Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(360)|Dynamická| Zadejte časový interval v sekundách. Časový limit pro upgrade aplikace. Pokud časový limit je menší než "ActivationTimeout" deployer se nezdaří. |
|ContainerServiceArguments|řetězec, výchozí hodnota je "-H localhost: 2375 -H npipe: / /"|Statická|Service Fabric (SF) spravuje démona dockeru (s výjimkou na klientské počítače s windows jako Win10). Tato konfigurace umožňuje uživateli zadat vlastní argumenty, které by měly být předány démona dockeru, při jeho spuštění. Když zadáte vlastní argumenty, Service Fabric do modulu Dockeru s výjimkou nepředávejte žádné další argumenty '--pidfile "argument. Proto by neměl určovat uživatele '--pidfile "argument jako součást svých argumentů zákazníka. Kromě toho vlastní argumenty se ujistěte, které docker démon naslouchá na kanálu s výchozím názvem ve Windows (nebo Unixovému soketu domény v Linuxu) služba Service Fabric komunikovat s ním.|
|ContainerServiceLogFileMaxSizeInKb|int, výchozí je 32768|Statická|Maximální velikost souboru protokolu vygenerované kontejnery dockeru.  Jenom Windows.|
|ContainerServiceLogFileNamePrefix|řetězec, výchozí je "sfcontainerlogs"|Statická|Předpona názvu souboru pro soubory protokolů generované kontejnery dockeru.  Jenom Windows.|
|ContainerServiceLogFileRetentionCount|Int, výchozí hodnota je 10|Statická|Počet souborů protokolů generovaných kontejnery dockeru se soubory protokolu budou přepsány.  Jenom Windows.|
|CreateFabricRuntimeTimeout|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(120)|Dynamická| Zadejte časový interval v sekundách. Hodnota časového limitu pro synchronizaci FabricCreateRuntime volání |
|DefaultContainerRepositoryAccountName|řetězec, výchozí hodnota je ""|Statická|Výchozí pověření použít místo přihlašovacích údajů zadaných v souboru ApplicationManifest.xml |
|DefaultContainerRepositoryPassword|řetězec, výchozí hodnota je ""|Statická|Výchozí heslo pověření použít místo přihlašovacích údajů zadaných v souboru ApplicationManifest.xml|
|DeploymentMaxFailureCount|Int, výchozí hodnota je 20| Dynamická|Nasazení aplikace se provede pro časy DeploymentMaxFailureCount před selháním nasazení této aplikace na uzlu.| 
|DeploymentMaxRetryInterval| Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(3600)|Dynamická| Zadejte časový interval v sekundách. Maximální interval opakování pro nasazení. Při každé průběžné selhání interval opakování se počítá jako Min (DeploymentMaxRetryInterval; Průběžný počet selhání * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(10)|Dynamická|Zadejte časový interval v sekundách. Regresní interval selhání nasazení. Při každé selhání průběžného nasazování bude systém pokusem o nasazení pro až MaxDeploymentFailureCount. Interval opakování je produkt selhání průběžného nasazování a interval omezení rychlosti nasazení. |
|EnableActivateNoWindow| Logická hodnota, výchozí hodnotu FALSE|Dynamická| Aktivovaný proces se vytvoří na pozadí bez jakékoli konzoly. |
|EnableContainerServiceDebugMode|Logická hodnota, výchozí hodnotu TRUE|Statická|Povolí nebo zakáže protokolování pro kontejnery dockeru.  Jenom Windows.|
|EnableDockerHealthCheckIntegration|Logická hodnota, výchozí hodnotu TRUE|Statická|Umožňuje integraci dockeru HEALTHCHECK událostí s sestavy stavu systému Service Fabric |
|EnableProcessDebugging|Logická hodnota, výchozí hodnotu FALSE|Dynamická| Povolí spouštění hostitele aplikace pod ladicím programem |
|EndpointProviderEnabled| Logická hodnota, výchozí hodnotu FALSE|Statická| Umožňuje správu koncový bod prostředků infrastruktury. Vyžaduje, aby počáteční a koncové rozsahu portů aplikace v FabricNode. |
|FabricContainerAppsEnabled| Logická hodnota, výchozí hodnotu FALSE|Statická| |
|FirewallPolicyEnabled|Logická hodnota, výchozí hodnotu FALSE|Statická| Umožňuje otevřít porty brány firewall pro koncový bod prostředků s explicitní portů zadaných v souboru ServiceManifest |
|GetCodePackageActivationContextTimeout|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(120)|Dynamická|Zadejte časový interval v sekundách. Hodnota časového limitu pro CodePackageActivationContext volání. To neplatí pro služby ad-hoc. |
|IPProviderEnabled|Logická hodnota, výchozí hodnotu FALSE|Statická|Umožňuje pomocí správy IP adres. |
|IsDefaultContainerRepositoryPasswordEncrypted|Logická hodnota, výchozí hodnotu FALSE|Statická|Určuje, zda je nebo není zašifrovaná DefaultContainerRepositoryPassword.|
|LinuxExternalExecutablePath|řetězec, výchozí hodnota je "/ usr/bin /" |Statická|Primární adresář externí spustitelné příkazy na uzlu.|
|NTLMAuthenticationEnabled|Logická hodnota, výchozí hodnotu FALSE|Statická| Povolí podporu pro balíčky kódu, které jsou s jiným uživatelům, aby procesy napříč počítači zabezpečeně komunikovat pomocí protokolu NTLM. |
|NTLMAuthenticationPasswordSecret|SecureString, výchozí hodnota je Common::SecureString("")|Statická|Není že šifrované má, který se používá ke generování hesla pro uživatele, protokol NTLM. Musí být nastavena pokud NTLMAuthenticationEnabled má hodnotu true. Ověřuje modul pro nasazení. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|Časový interval, výchozí hodnota je Common::TimeSpan::FromMinutes(3)|Dynamická|Zadejte časový interval v sekundách. Pravidelné interval, ve které Hosting hledá nové certifikáty pro použití protokolu NTLM FileStoreService konfigurace nastavení pro konkrétní prostředí. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|Časový interval, výchozí hodnota je Common::TimeSpan::FromMinutes(4)|Dynamická| Zadejte časový interval v sekundách. Pro konfiguraci protokolu NTLM uživatele, kteří používají certifikát běžných názvů vypršel časový limit. Uživatelé NTLM je třeba FileStoreService sdílené složky. |
|RegisterCodePackageHostTimeout|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(120)|Dynamická| Zadejte časový interval v sekundách. Hodnota časového limitu pro volání FabricRegisterCodePackageHost synchronizace. To je možné použít pouze s více kódu balíček hostitelů aplikací jako FWP |
|RequestTimeout|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(30)|Dynamická| Zadejte časový interval v sekundách. To představuje časový limit pro komunikaci mezi aplikační hostitel systému a prostředků infrastruktury proces pro různé hostingové souvisejících operací jako je registrace továrny; uživatele Registrace modulu runtime. |
|RunAsPolicyEnabled| Logická hodnota, výchozí hodnotu FALSE|Statická| Povolí spouštění balíčků kódu jako místní uživatel, než je uživatel v rámci které prostředků infrastruktury je proces spuštěn. Chcete-li povolit tyto zásady, které prostředky infrastruktury musí běžet jako systém, nebo jako uživatel, který má SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(120)|Dynamická|Zadejte časový interval v sekundách. Hodnota časového limitu pro synchronizaci volání ServiceFactory registr (Stateless/stavová) |
|ServiceTypeDisableFailureThreshold |Celé číslo, výchozí je 1 |Dynamická|Toto je prahová hodnota pro počet selhání, po jejímž uplynutí je oznámena FailoverManager (převzetí služeb při selhání) zakažte typ služby v tomto uzlu a zkuste jiný uzel pro umístění. |
|ServiceTypeDisableGraceInterval|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(30)|Dynamická|Zadejte časový interval v sekundách. Časový interval, po jejímž uplynutí se dají zakázat typ služby |
|ServiceTypeRegistrationTimeout |Čas v sekundách, výchozí hodnota je 300 |Dynamická|Maximální dobu povolenou pro ServiceType zaregistrovat u prostředků infrastruktury |
|UseContainerServiceArguments|Logická hodnota, výchozí hodnotu TRUE|Statická|Tato konfigurace říká hostování přeskočit předávání argumentů (zadané v konfiguraci ContainerServiceArguments) démona dockeru.|

## <a name="httpgateway"></a>HttpGateway
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, výchozí hodnota je 50 |Statická| Počet čtení k odeslání do fronty http serveru. Tato volba určuje počet souběžných požadavků, které je možné splnit HttpGateway. |
|HttpGatewayHealthReportSendInterval |Čas v sekundách, výchozí hodnota je 30 |Statická|Zadejte časový interval v sekundách. Interval, ve kterém bránu Http odesílá nahromaděné stavu sestav nástroje Health Manager. |
|IsEnabled|Logická hodnota, výchozí hodnota je false |Statická| Povolí nebo zakáže HttpGateway. Ve výchozím nastavení je zakázána HttpGateway. |
|MaxEntityBodySize |Uint, výchozí hodnota je 4194304 |Dynamická|Poskytuje maximální velikost obsahu, která se dají očekávat od požadavku http. Výchozí hodnota je 4MB. Httpgateway se požadavek nezdaří, pokud má tělo velikosti > tuto hodnotu. Velikost bloku dat minimální čtení je 4096 bajtů. Takže to musí být > = 4096. |

## <a name="imagestoreclient"></a>ImageStoreClient
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ClientCopyTimeout | Čas v sekundách, výchozí hodnota je 1 800 |Dynamická| Zadejte časový interval v sekundách. Hodnota časového limitu pro nejvyšší úrovně zkopírujte žádost o službu Store bitových kopií. |
|ClientDefaultTimeout | Čas v sekundách, výchozí hodnota je 180 |Dynamická| Zadejte časový interval v sekundách. Hodnota časového limitu pro všechny požadavky bez nahrávání a stahování (například existuje, odstraňte) ke službě Store bitové kopie. |
|ClientDownloadTimeout | Čas v sekundách, výchozí hodnota je 1 800 |Dynamická| Zadejte časový interval v sekundách. Hodnota časového limitu pro stahování nejvyšší úrovně požadavek na službu Store bitových kopií. |
|ClientListTimeout | Čas v sekundách, výchozí hodnota je 600 |Dynamická|Zadejte časový interval v sekundách. Hodnota časového limitu pro nejvyšší úrovně požadavek na službu Store bitových kopií. |
|ClientUploadTimeout |Čas v sekundách, výchozí hodnota je 1 800 |Dynamická|Zadejte časový interval v sekundách. Hodnota časového limitu pro nahrávání nejvyšší úrovně požadavek na službu Store bitových kopií. |

## <a name="imagestoreservice"></a>ImageStoreService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|Povoleno |Logická hodnota, výchozí hodnota je false |Statická|Příznak povoleno ImageStoreService. Výchozí: false |
|MinReplicaSetSize | Int, výchozí hodnota je 3 |Statická|MinReplicaSetSize pro ImageStoreService. |
|PlacementConstraints | řetězec, výchozí hodnota je "" |Statická| PlacementConstraints pro ImageStoreService. |
|QuorumLossWaitDuration | Čas v sekundách, výchozí hodnota je hodnota MaxValue |Statická| Zadejte časový interval v sekundách. QuorumLossWaitDuration pro ImageStoreService. |
|ReplicaRestartWaitDuration | Čas v sekundách, výchozí hodnota je 60.0 * 30 |Statická|Zadejte časový interval v sekundách. ReplicaRestartWaitDuration pro ImageStoreService. |
|StandByReplicaKeepDuration | Čas v sekundách, výchozí hodnota je 3600.0 * 2 |Statická| Zadejte časový interval v sekundách. StandByReplicaKeepDuration pro ImageStoreService. |
|TargetReplicaSetSize | Int, výchozí hodnota je 7 |Statická|TargetReplicaSetSize pro ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |int, výchozí je 1 |Dynamická|Příznak označující, pokud paměti by měla být automaticky a dynamicky konfigurovat nastavení. Pokud nula a konfiguraci nastavení paměti se používají přímo a nemění podle podmínek systému. Pokud jeden pak nastavení paměti jsou konfigurovány automaticky a může se změnit na základě systému podmínek. |
|MaximumDestagingWriteOutstandingInKB | int, výchozí je 0 |Dynamická|Číslo KB umožňující sdílené protokol pro přechod náskok před vyhrazené protokolu. Použijte hodnotu 0 označující bez omezení.
|SharedLogId |řetězec, výchozí hodnota je "" |Statická|Jedinečný identifikátor guid pro sdílené protokolu kontejneru. Použití "" Pokud používáte výchozí cesta pod kořen dat prostředků infrastruktury. |
|SharedLogPath |řetězec, výchozí hodnota je "" |Statická|Cesta a název souboru do umístění, na sdílené protokolu kontejneru. Použití "" pro použití výchozí cesta pod kořen dat prostředků infrastruktury. |
|SharedLogSizeInMB |Int, výchozí hodnota je 8192 |Statická|Počet MB k přidělení v kontejneru sdílené protokolu. |
|WriteBufferMemoryPoolMaximumInKB | int, výchozí je 0 |Dynamická|Číslo KB umožňující paměti fondu vyrovnávacích pamětí zápisu rozšířit až. Použijte hodnotu 0 označující bez omezení. |
|WriteBufferMemoryPoolMinimumInKB |Int, výchozí hodnota je 8388608 |Dynamická|Číslo KB k začátku přidělení pro fond vyrovnávací paměti zápisu. Použijte hodnotu 0 označující bez omezení, výchozí by měl být konzistentní s SharedLogSizeInMB níže. |

## <a name="management"></a>Správa
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AzureStorageMaxConnections | Int, výchozí je 5 000 |Dynamická|Maximální počet souběžných připojení ke službě azure storage. |
|AzureStorageMaxWorkerThreads | int, výchozí je 25 |Dynamická|Maximální počet pracovních vláken současně. |
|AzureStorageOperationTimeout | Čas v sekundách, výchozí hodnota je 6000 |Dynamická|Zadejte časový interval v sekundách. Časový limit pro dokončení operace xstore. |
|CleanupApplicationPackageOnProvisionSuccess|Logická hodnota, výchozí hodnotu FALSE |Dynamická|Tato konfigurace povolí nebo zakáže automatické čištění balíčku aplikace v úspěšném zřízení. |
|DisableChecksumValidation | Logická hodnota, výchozí hodnota je false |Statická| Tato konfigurace umožňuje povolit nebo zakázat ověření kontrolního součtu během zřizování aplikací. |
|DisableServerSideCopy | Logická hodnota, výchozí hodnota je false |Statická|Tato konfigurace povolí nebo zakáže na straně serveru kopii balíčku aplikace na ImageStore během zřizování aplikací. |
|ImageCachingEnabled | Logická hodnota, výchozí hodnota je true |Statická|Tato konfigurace umožňuje povolit nebo zakázat ukládání do mezipaměti. |
|ImageStoreConnectionString |SecureString |Statická|Připojovací řetězec do kořenového adresáře pro ImageStore. |
|ImageStoreMinimumTransferBPS | Int, výchozí hodnota je 1024 |Dynamická|Minimální přenosovou rychlost mezi clusterem a ImageStore. Tato hodnota se používá k určení časového limitu při přístupu k externí ImageStore. Tuto hodnotu změňte, pouze pokud je latence mezi clusterem a ImageStore vysokou a více času pro cluster můžete stáhnout z externí ImageStore. |

## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, výchozí hodnota je None|Dynamická|Určuje sadu MetricActivityThresholds pro metriky v clusteru. Vyrovnávání bude fungovat, pokud je větší než MetricActivityThresholds maxNodeLoad. Pro defragmentaci metrik definuje množství zatížení stejné nebo nižší které Service Fabric bude předpokládat, že uzel prázdné |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, výchozí hodnota je None|Dynamická|Určuje sadu MetricBalancingThresholds pro metriky v clusteru. Vyrovnávání bude fungovat, pokud je větší než MetricBalancingThresholds maxNodeLoad/minNodeLoad. Defragmentace bude fungovat, pokud je menší než MetricBalancingThresholds maxNodeLoad/minNodeLoad v nejméně jedné FD nebo UD. |

## <a name="namingservice"></a>NamingService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |int, výchozí je 0 |Statická|Maximální počet položek udržovat v mezipaměti popis služby LRU brána pojmenování (nastavena na hodnotu 0 pro žádné omezení). |
|MaxClientConnections |Int, výchozí hodnota je 1000 |Dynamická|Maximální povolený počet připojení klientů na jednu bránu. |
|MaxFileOperationTimeout |Čas v sekundách, výchozí hodnota je 30 |Dynamická|Zadejte časový interval v sekundách. Maximální časový limit povolený pro operaci služby úložiště souborů. Odmítne požadavky zadávání většího časového limitu. |
|MaxIndexedEmptyPartitions |Int, výchozí hodnota je 1000 |Dynamická|Maximální počet prázdné oddíly, které zůstanou indexování v mezipaměti oznámení pro synchronizaci opětovné připojení klientů. Všechny prázdné oddíly vyšší než toto číslo se odebere z indexu ve vzestupném pořadí vyhledávání verze. Opětovné připojení klientů stále můžete synchronizovat a dostávat aktualizace zmeškaných prázdný oddíl. ale protokol synchronizace bude dražší. |
|MaxMessageSize |Int, výchozí hodnota je 4\*1024\*1024 |Statická|Maximální velikost zprávy pro komunikaci s klienty uzlu při použití pojmenování. Zmírnění útoků DOS; Výchozí hodnota je 4MB. |
|MaxNamingServiceHealthReports | Int, výchozí hodnota je 10 |Dynamická|Maximální počet pomalých operací, které ukládají pojmenování služby v jednom okamžiku sestavy není v pořádku. Pokud je 0; všechny operace pomalé se odesílají. |
|MaxOperationTimeout |Čas v sekundách, výchozí hodnota je 600 |Dynamická|Zadejte časový interval v sekundách. Maximální časový limit pro klientské operace povolena. Odmítne požadavky zadávání většího časového limitu. |
|MaxOutstandingNotificationsPerClient |Int, výchozí hodnota je 1000 |Dynamická|Maximální počet nezpracovaných oznámení před registrace klienta je vynuceně ukončeno brány. |
|MinReplicaSetSize | Int, výchozí hodnota je 3 |Nepovolené| Minimální počet replik pojmenování Service vyžadovaných k zápisu do dokončení aktualizace. Pokud jsou repliky míň než toto aktivní v systému spolehlivost systému zakazuje aktualizace Store služba pojmenování dokud repliky se obnoví. Tato hodnota by měla být nikdy víc než TargetReplicaSetSize. |
|PartitionCount |Int, výchozí hodnota je 3 |Nepovolené|Počet oddílů služby pojmenování úložiště vytvořit. Každý oddíl vlastní jeden klíč oddílu, který odpovídá jeho index; takže klíče oddílů [0; PartitionCount) neexistuje. Zvýšení počtu oddílů zvýšení pojmenování Service měřítka, pojmenování Service můžete provést na snížením průměrné velikosti data ukládaná společností jakákoli replika zálohování nastavte; a účtuje se cenou vyšší využití prostředků (od PartitionCount * musí být udržovány ReplicaSetSize služby repliky).|
|PlacementConstraints | řetězec, výchozí hodnota je "" |Nepovolené| Omezení umístění pro službu pojmenování. |
|QuorumLossWaitDuration | Čas v sekundách, výchozí hodnota je hodnota MaxValue |Nepovolené| Zadejte časový interval v sekundách. Při pojmenování Service dostane do ztráty kvora; Tento časovač spustí. Po jeho vypršení převzetí služeb při selhání bude vezměte v úvahu dolů repliky jako ztracené; a pokud se pokusíte obnovit kvora. Ne, která to může způsobit ztrátu dat. |
|RepairInterval | Čas v sekundách, výchozí hodnota je 5 |Statická| Zadejte časový interval v sekundách. Interval, ve kterém se spustí pojmenování opravu nekonzistence mezi autority vlastníka a název vlastníka. |
|ReplicaRestartWaitDuration | Čas v sekundách, výchozí hodnota je (60.0 * 30)|Nepovolené| Zadejte časový interval v sekundách. Když se replika Naming Service ocitne mimo provoz; Tento časovač spustí. Po jeho vypršení převzetí služeb při selhání se začnou nahradit replik, které jsou mimo provoz (ji ještě je nepovažuje ztráty). |
|ServiceDescriptionCacheLimit | int, výchozí je 0 |Statická| Maximální počet položek udržovat v mezipaměti popis služby LRU Naming Store Service (nastavena na hodnotu 0 pro žádné omezení). |
|ServiceNotificationTimeout |Čas v sekundách, výchozí hodnota je 30 |Dynamická|Zadejte časový interval v sekundách. Časový limit při doručování oznámení služby klienta. |
|StandByReplicaKeepDuration | Čas v sekundách, výchozí hodnota je 3600.0 * 2 |Nepovolené| Zadejte časový interval v sekundách. Pokud replika Naming Service vrátit z dolů stavu; To může mít již bylo nahrazeno. Tento časovač Určuje, jak dlouho převzetí služeb při selhání budete mít pohotovostní repliky před budou zrušeny. |
|TargetReplicaSetSize |Int, výchozí hodnota je 7 |Nepovolené|Nastaví počet replik pro každý oddíl úložiště pojmenování Service. Zvýšení počtu sady replik zvyšuje úroveň spolehlivosti informace ve Store služba pojmenování; snížení změn, informace, se ztratí v důsledku selhání uzlů; za cenu zvýšení zatížení Windows Fabric a dobu trvá provádění aktualizací na pojmenování data.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, výchozí hodnota je None|Dynamická|Procento kapacity uzel za název metriky; aby bylo možné zachovat některé volné místo na uzlu pro případ převzetí služeb při selhání použít jako vyrovnávací paměť. |

## <a name="nodecapacities"></a>NodeCapacities
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Statická|Kolekce kapacity uzlů pro různé požadované metriky. |

## <a name="nodedomainids"></a>NodeDomainIds
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Statická|Popisuje domén selhání, do které patří uzlu. Doména selhání je definován pomocí identifikátor URI, který popisuje umístění uzlu v datovém centru.  Domény selhání identifikátory URI jsou ve formátu fd: / fd/následovaný segment cesty identifikátoru URI.|
|UpgradeDomainId |řetězec, výchozí hodnota je "" |Statická|Popisuje upgradovací doména, do které patří uzlu. |

## <a name="nodeproperties"></a>NodeProperties
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Statická|Kolekce párů klíč hodnota řetězce pro vlastnosti uzlu. |

## <a name="paas"></a>Paas
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ClusterId |řetězec, výchozí hodnota je "" |Nepovolené|X509 certifikátu úložiště používané konvencemi prostředků infrastruktury pro ochranu konfigurace. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|Čítače |Řetězec | Dynamická |Čárkami oddělený seznam čítačů výkonu k získání. |
|IsEnabled |Logická hodnota, výchozí hodnota je true | Dynamická |Příznak určuje, zda je povoleno shromažďování čítačů výkonu na místní uzel wsfc. |
|MaxCounterBinaryFileSizeInMB |int, výchozí je 1 | Dynamická |Maximální velikost (v MB) pro každý soubor binární čítače výkonu. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, výchozí hodnota je 10 | Dynamická |Maximální interval (v sekundách) po kterém se vytvoří nový soubor binární čítače výkonu. |
|SamplingIntervalInSeconds |Int, výchozí hodnota je 60 | Dynamická |Interval vzorkování pro shromažďování čítačů výkonu. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | int, výchozí je 0 | Dynamická|Určuje prioritu omezení spřažení: 0: pevné; 1: obnovitelně; záporná: ignorovat. |
|ApplicationCapacityConstraintPriority | int, výchozí je 0 | Dynamická|Určuje prioritu omezení kapacity: 0: pevné; 1: obnovitelně; záporná: ignorovat. |
|AutoDetectAvailableResources|Logická hodnota, výchozí hodnotu TRUE|Statická|Tato konfigurace aktivuje Automatická detekce dostupné prostředky na uzel (procesoru a paměti) když tato konfigurace je nastaveno na true – budeme číst skutečné kapacity a opravte je, pokud uživatel zadaný špatný uzel kapacity nebo nebyl jejich definování vůbec tato konfigurace je nastavený na hodnotu false - budeme  upozornění, že tento uživatel zadaný špatný uzel kapacity; trasování ale nebudeme opravovat. To znamená, tento uživatel chce mít kapacity, zadaný jako > než ve skutečnosti má uzel nebo pokud nejsou definovány; kapacity bude předpokládat, neomezenou kapacitu |
|BalancingDelayAfterNewNode | Čas v sekundách, výchozí hodnota je 120 |Dynamická|Zadejte časový interval v sekundách. Nespouštějte vyrovnávání aktivit během tohoto období po přidání nového uzlu. |
|BalancingDelayAfterNodeDown | Čas v sekundách, výchozí hodnota je 120 |Dynamická|Zadejte časový interval v sekundách. Nespouštějte vyrovnávání aktivit během tohoto období po uzel událost vypnutí. |
|CapacityConstraintPriority | int, výchozí je 0 | Dynamická|Určuje prioritu omezení kapacity: 0: pevné; 1: obnovitelně; záporná: ignorovat. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, výchozí hodnota je 20 | Dynamická|Definuje počet po sobě jdoucích pokusů vydané ResourceBalancer pohybů plb typu jsou odstraněna dříve, než jsou prováděny diagnostiky a jsou emitovány upozornění stavu. Negativní: Žádná varování, protože ho za těchto podmínek. |
|ConstraintFixPartialDelayAfterNewNode | Čas v sekundách, výchozí hodnota je 120 |Dynamická| Zadejte časový interval v sekundách. DDo FaultDomain opravit a narušení omezení UpgradeDomain během tohoto období po přidání nového uzlu. |
|ConstraintFixPartialDelayAfterNodeDown | Čas v sekundách, výchozí hodnota je 120 |Dynamická| Zadejte časový interval v sekundách. To není narušení omezení FaultDomain opravit a UpgradeDomain během tohoto období po uzel událost vypnutí. |
|ConstraintViolationHealthReportLimit | Int, výchozí hodnota je 50 |Dynamická| Definuje počet pokusů, které bychom při tom porušili repliky omezení musí být trvale laťky předtím, než jsou prováděny diagnostiky a jsou emitovány sestav stravu. |
|DetailedConstraintViolationHealthReportLimit | Int, výchozí hodnota je 200 |Dynamická| Definuje počet pokusů, které bychom při tom porušili repliky omezení musí být trvale laťky před Diagnostika se provádí a podrobných stavu, které jsou emitovány sestavy. |
|DetailedDiagnosticsInfoListLimit | int, výchozí je 15 |Dynamická| Definuje počtem diagnostických záznamů (s podrobnými informacemi) za omezení, které zahrnují před zkrácením v diagnostice.|
|DetailedNodeListLimit | int, výchozí je 15 |Dynamická| Definuje počet uzlů na omezení, které zahrnují před zkrácením v sestavách Unplaced repliky. |
|DetailedPartitionListLimit | int, výchozí je 15 |Dynamická| Definuje počet oddílů na diagnostických položku pro omezení, které zahrnují před zkrácením v diagnostice. |
|DetailedVerboseHealthReportLimit | Int, výchozí hodnota je 200 | Dynamická|Definuje počet pokusů, které unplaced replika musí být trvale unplaced předtím, než jsou emitovány stavu podrobné sestavy. |
|FaultDomainConstraintPriority | int, výchozí je 0 |Dynamická| Určuje prioritu omezení domény selhání: 0: pevné; 1: obnovitelně; záporná: ignorovat. |
|GlobalMovementThrottleCountingInterval | Čas v sekundách, výchozí hodnota je 600 |Statická| Zadejte časový interval v sekundách. Označuje délku posledních interval, pro které chcete sledovat jednotlivé domény repliky pohybu (používá se společně s GlobalMovementThrottleThreshold). Můžete nastavit na hodnotu 0 ignorovat, globální omezení úplně se vynechá. |
|GlobalMovementThrottleThreshold | Uint, výchozí hodnota je 1000 |Dynamická| Maximální počet pohybů plb typu ve fázi vyrovnávání v posledních interval indikován GlobalMovementThrottleCountingInterval povoleny. |
|GlobalMovementThrottleThresholdForBalancing | Uint, výchozí hodnota je 0 | Dynamická|Maximální počet pohybů plb typu ve fázi vyrovnávání v posledních interval indikován GlobalMovementThrottleCountingInterval povoleny. Hodnota 0 znamená bez omezení. |
|GlobalMovementThrottleThresholdForPlacement | Uint, výchozí hodnota je 0 |Dynamická| Maximální počet pohybů plb typu povolené ve fázi umístění v posledních intervalu indikován GlobalMovementThrottleCountingInterval.0 znamená bez omezení.|
|GlobalMovementThrottleThresholdPercentage|Double výchozí hodnota je 0|Dynamická|Maximální počet celkový počet pohybů plb typu v posledních interval indikován GlobalMovementThrottleCountingInterval povolena ve fázích vyrovnávání a umístění (vyjádřený jako procentní podíl celkového počtu replik v clusteru). Hodnota 0 znamená bez omezení. Pokud tato a jsou uvedeny GlobalMovementThrottleThreshold; použije konzervativnější limit.|
|GlobalMovementThrottleThresholdPercentageForBalancing|Double výchozí hodnota je 0|Dynamická|Maximální počet pohybů plb typu v posledních interval indikován GlobalMovementThrottleCountingInterval povolena ve fázi vyrovnávání (vyjádřený jako procentní podíl celkového počtu replik v PLB). Hodnota 0 znamená bez omezení. Pokud tato a jsou uvedeny GlobalMovementThrottleThresholdForBalancing; použije konzervativnější limit.|
|InBuildThrottlingAssociatedMetric | řetězec, výchozí hodnota je "" |Statická| Přidružený název metriky pro toto omezení. |
|InBuildThrottlingEnabled | Logická hodnota, výchozí hodnota je false |Dynamická| Určení, zda je povoleno omezení v sestavení. |
|InBuildThrottlingGlobalMaxValue | int, výchozí je 0 |Dynamická|Maximální počet replik ve sestavení povoleno globálně. |
|InterruptBalancingForAllFailoverUnitUpdates | Logická hodnota, výchozí hodnota je false | Dynamická|Určuje, zda by měl jakýkoli druh aktualizace jednotek převzetí služeb při selhání přerušení rychlé nebo pomalé vyrovnávání spustit. Pomocí zadané "false" vyrovnávání spuštění se odešle, pokud FailoverUnit: vytvoření/odstranění; Chybí repliky; změnit umístění primární repliku nebo změněných počet replik. Vyrovnávání spuštění není, přeruší se v ostatních případech – Pokud FailoverUnit: má navíc replik; změnit všechny repliky příznaky; změnit pouze verze oddílu nebo všechny ostatní případy. |
|MinConstraintCheckInterval | Čas v sekundách, výchozí hodnota je 1 |Dynamická| Zadejte časový interval v sekundách. Definuje minimální množství času, které musí uplynout, než dvěma po sobě jdoucích omezení zkontrolujte Zaokrouhlí číslo. |
|MinLoadBalancingInterval | Čas v sekundách, výchozí hodnota je 5 |Dynamická| Zadejte časový interval v sekundách. Definuje minimální množství času, které musí uplynout před dvě po sobě jdoucích vyrovnávání kola. |
|MinPlacementInterval | Čas v sekundách, výchozí hodnota je 1 |Dynamická| Zadejte časový interval v sekundách. Definuje minimální množství času, která musí uplynout před zaokrouhlí dvě po sobě jdoucích umístění. |
|MoveExistingReplicaForPlacement | Logická hodnota, výchozí hodnota je true |Dynamická|Nastavení, která určuje, pokud chcete přesunout existující repliku během umísťování. |
|MovementPerPartitionThrottleCountingInterval | Čas v sekundách, výchozí hodnota je 600 |Statická| Zadejte časový interval v sekundách. Označuje délku posledních interval, pro které chcete sledovat pohybů plb typu replik pro každý oddíl (používá se společně s MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, výchozí hodnota je 50 |Dynamická| Žádné související s vyrovnávání přesun dojde pro oddíl, pokud má dosáhli nebo Přesáhli jste MovementPerFailoverUnitThrottleThreshold v posledních intervalu udávají počet vyrovnávání související pohybů plb typu pro repliky daného oddílu MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Logická hodnota, výchozí hodnota je false |Dynamická| Nastavení, která určuje, zda lze přesunout nadřazené repliky opravit omezení spřažení.|
|PartiallyPlaceServices | Logická hodnota, výchozí hodnota je true |Dynamická| Určuje, pokud všechny repliky služby v clusteru budou umístěny "všechno nebo nic" daný omezený vhodný uzly pro ně.|
|PlaceChildWithoutParent | Logická hodnota, výchozí hodnota je true | Dynamická|Nastavení, která určuje, zda podřízené služby repliky mohou být umístěny, pokud žádná nadřazená replika je v provozu. |
|PlacementConstraintPriority | int, výchozí je 0 | Dynamická|Určuje prioritu omezení umístění: 0: pevné; 1: obnovitelně; záporná: ignorovat. |
|PlacementConstraintValidationCacheSize | Int, výchozí hodnota je 10000 |Dynamická| Omezuje velikost tabulky použitý pro rychlé ověření a ukládání do mezipaměti výrazů omezení umístění. |
|PlacementSearchTimeout | Čas v sekundách, výchozí hodnota je 0,5 |Dynamická| Zadejte časový interval v sekundách. Při umístění služeb; Vyhledejte maximálně této doby před vrácením výsledku. |
|PLBRefreshGap | Čas v sekundách, výchozí hodnota je 1 |Dynamická| Zadejte časový interval v sekundách. Definuje minimální množství času, které musí uplynout, než PLB aktualizuje stav znovu. |
|PreferredLocationConstraintPriority | Int, výchozí hodnota je 2| Dynamická|Určuje prioritu omezení upřednostňované umístění: 0: pevné; 1: obnovitelně; 2: optimalizace; záporná: Ignorovat |
|PreventTransientOvercommit | Logická hodnota, výchozí hodnota je false | Dynamická|Určuje, by měl PLB okamžitě Spolehněte se na prostředky, které se uvolní přesune spuštěné. Ve výchozím nastavení; Přesun navýšení kapacity můžete inicializovat PLB a přesunutí najdete v na stejném uzlu, což může způsobit přechodné přetížit. Nastavení tohoto parametru na hodnotu true, nebude moct tyto typy z overcommits a na vyžádání defragmentace (neboli placementWithMove) bude zakázán. |
|ScaleoutCountConstraintPriority | int, výchozí je 0 |Dynamická| Určuje prioritu omezení počtu horizontálním navýšením kapacity: 0: pevné; 1: obnovitelně; záporná: ignorovat. |
|SwapPrimaryThrottlingAssociatedMetric | řetězec, výchozí hodnota je ""|Statická| Přidružený název metriky pro toto omezení. |
|SwapPrimaryThrottlingEnabled | Logická hodnota, výchozí hodnota je false|Dynamická| Určení, zda je povoleno omezení primárních prohození. |
|SwapPrimaryThrottlingGlobalMaxValue | int, výchozí je 0 |Dynamická| Maximální počet replik primárních prohození povoleno globálně. |
|TraceCRMReasons |Logická hodnota, výchozí hodnota je true |Dynamická|Určuje, zda chcete trasovat důvodů, proč CRM pohybů plb typu vydat provozní události kanálu. |
|UpgradeDomainConstraintPriority | int, výchozí je 1| Dynamická|Určuje prioritu omezení domény upgradu: 0: pevné; 1: obnovitelně; záporná: ignorovat. |
|UseMoveCostReports | Logická hodnota, výchozí hodnota je false | Dynamická|Dává pokyn LB ignorovat elementu náklady na funkci bodování; Výsledný potenciálně velkého počtu přesun lépe vyvážené umístění. |
|UseSeparateSecondaryLoad | Logická hodnota, výchozí hodnota je true | Dynamická|Nastavení, která určuje, zda použít jiné sekundární zatížení. |
|ValidatePlacementConstraint | Logická hodnota, výchozí hodnota je true |Dynamická| Určuje, zda se při aktualizaci služby ServiceDescription ověří PlacementConstraint výraz pro službu. |
|VerboseHealthReportLimit | Int, výchozí hodnota je 20 | Dynamická|Definuje počet pokusů, které replika musí projít unplaced před stavu upozornění je hlášeno pro něj (Pokud je povoleno oznamování podrobný stav). |

## <a name="reconfigurationagent"></a>ReconfigurationAgent
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Čas v sekundách, výchozí hodnota je 900 |Dynamická|Zadejte časový interval v sekundách. Doba trvání pro kterou systém počká, než se ukončuje Obsluha hostitelů, které mají repliky, který se zablokuje a v zavřete během upgradu aplikace.|
|FabricUpgradeMaxReplicaCloseDuration | Čas v sekundách, výchozí hodnota je 900 |Dynamická| Zadejte časový interval v sekundách. Doba trvání pro kterou systém počká, než se ukončuje Obsluha hostitelů, které mají repliky, který se zablokuje a v zavřete během upgradu prostředků infrastruktury. |
|GracefulReplicaShutdownMaxDuration|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(120)|Dynamická|Zadejte časový interval v sekundách. Doba trvání pro kterou systém počká, než se ukončuje Obsluha hostitelů, které mají repliky, který se zablokuje a v zavřete. Pokud tato hodnota nastavena na hodnotu 0, nebudou se repliky pokyny, zavřete.|
|NodeDeactivationMaxReplicaCloseDuration | Čas v sekundách, výchozí hodnota je 900 |Dynamická|Zadejte časový interval v sekundách. Doba trvání pro kterou systém počká, než se ukončuje Obsluha hostitelů, které mají repliky, který se zablokuje a v zavřete během deaktivace uzlu. |
|PeriodicApiSlowTraceInterval | Čas v sekundách, výchozí hodnota je 5 minut |Dynamická| Zadejte časový interval v sekundách. PeriodicApiSlowTraceInterval definuje interval, přes který bude pomalé volání rozhraní API překreslována monitorování rozhraní API. |
|ReplicaChangeRoleFailureRestartThreshold|Int, výchozí hodnota je 10|Dynamická| Celé číslo. Zadejte počet neúspěšných rozhraní API během primární povýšení, po které se použijí automaticky zmírňující opatření (repliky restartování). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, výchozí je 2147483647.|Dynamická| Celé číslo. Zadejte počet neúspěšných rozhraní API během primární povýšení, po jejímž uplynutí bude vydána upozornění sestava stavu.|
|ServiceApiHealthDuration | Čas v sekundách, výchozí hodnota je 30 minut |Dynamická| Zadejte časový interval v sekundách. ServiceApiHealthDuration definuje, jak dlouho do jsme čekat pro rozhraní API služby běžet, než jsme nahlaste to není v pořádku. |
|ServiceReconfigurationApiHealthDuration | Čas v sekundách, výchozí hodnota je 30 |Dynamická| Zadejte časový interval v sekundách. ServiceReconfigurationApiHealthDuration definuje, jak dlouho do jsme čekat pro rozhraní API služby běžet, než vytvoříme sestavy není v pořádku. To platí pro volání rozhraní API, které ovlivňují dostupnost.|

## <a name="replication"></a>Replikace
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|Časový interval, výchozí hodnota je Common::TimeSpan::FromMilliseconds(15)|Statická|Zadejte časový interval v sekundách. Určuje množství času, které Replikátor čeká po přijetí operace před odesláním zpět na potvrzení. Další operace přijatých během tohoto období budou mít jejich potvrzení odeslaných zpět do jedné zprávy -> snížení síťový provoz, ale potenciálně snížení propustnosti replikátoru.|
|MaxCopyQueueSize|uint, výchozí je 1024|Statická|Toto je maximální hodnota definuje počáteční velikost fronty, který udržuje operací replikace. Všimněte si, že musí být mocninou čísla 2. Pokud za běhu zvětšuje, aby tato operace velikost fronty se omezí mezi primárním a sekundárním replikátorů.|
|MaxPrimaryReplicationQueueMemorySize|Uint, výchozí hodnota je 0|Statická|To je maximální hodnota primární replikace fronty v bajtech.|
|MaxPrimaryReplicationQueueSize|uint, výchozí je 1024|Statická|Toto je maximální počet operací, které může existovat ve frontě primární replikace. Všimněte si, že musí být mocninou čísla 2.|
|MaxReplicationMessageSize|uint, výchozí je 52428800|Statická|Maximální velikost zprávy operací replikace. Výchozí hodnota je 50MB.|
|MaxSecondaryReplicationQueueMemorySize|Uint, výchozí hodnota je 0|Statická|To je maximální hodnota fronty sekundární replikace v bajtech.|
|MaxSecondaryReplicationQueueSize|uint, výchozí je 2048|Statická|Toto je maximální počet operací, které může existovat ve frontě replikací sekundární. Všimněte si, že musí být mocninou čísla 2.|
|QueueHealthMonitoringInterval|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(30)|Statická|Zadejte časový interval v sekundách. Tato hodnota určuje časové období, které Replikátor používá ke sledování žádné události stavu upozornění nebo chyby ve frontách operace replikace. Hodnota '0' vypne sledování stavu |
|QueueHealthWarningAtUsagePercent|uint, výchozí je 80|Statická|Tato hodnota určuje využití fronty replikací (v procentech), po jejímž uplynutí vytvoříme upozornění sestavy o využití vysoké fronty. To uděláme za dobu odkladu o QueueHealthMonitoringInterval. Pokud využití fronty klesne pod tuto procentuální hodnotu v intervalu odkladu|
|ReplicatorAddress|řetězec, výchozí je "localhost:0"|Statická|Koncový bod v podobě řetězce-"IP: port" který používá Replikátor Windows Fabric pro navázání připojení s ostatními replikami k posílání a přijímání operace.|
|ReplicatorListenAddress|řetězec, výchozí je "localhost:0"|Statická|Koncový bod v podobě řetězce-"IP: port" který používá Replikátor Windows Fabric přijímat operace z jiné repliky.|
|ReplicatorPublishAddress|řetězec, výchozí je "localhost:0"|Statická|Koncový bod v podobě řetězce-"IP: port" sloužící k odesílání operace do jiné repliky, které Replikátor Windows Fabric.|
|retryInterval|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(5)|Statická|Zadejte časový interval v sekundách. Při operaci, dojde ke ztrátě nebo odmítl tento časovač Určuje, jak často bude Replikátor opakovat operaci odeslání.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|IsEnabled|Logická hodnota, výchozí hodnotu FALSE |Statická|Určuje, zda je služba zapnutá v clusteru nebo ne. |

## <a name="runas"></a>Spustit jako
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|RunAsAccountName |řetězec, výchozí hodnota je "" |Dynamická|Určuje název účtu RunAs. To je potřeba jenom pro účet "DomainUser" nebo "ManagedServiceAccount" typu. Platné hodnoty jsou "doména\uživatel" nebo "user@domain". |
|Typ účtu spustit jako|řetězec, výchozí hodnota je "" |Dynamická|Určuje typ účtu spustit jako. To je potřeba pro všechny RunAs části platné hodnoty jsou "Uživatele DomainUser nebo NetworkService/ManagedServiceAccount/LocalSystem".|
|Spustit_jako_heslo|řetězec, výchozí hodnota je "" |Dynamická|Určuje heslo k účtu RunAs. To je potřeba jenom pro typ účtu "uživatele DomainUser". |

## <a name="runasdca"></a>RunAs_DCA
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|RunAsAccountName |řetězec, výchozí hodnota je "" |Dynamická|Určuje název účtu RunAs. To je potřeba jenom pro účet "DomainUser" nebo "ManagedServiceAccount" typu. Platné hodnoty jsou "doména\uživatel" nebo "user@domain". |
|Typ účtu spustit jako|řetězec, výchozí hodnota je "" |Dynamická|Určuje typ účtu spustit jako. To je potřeba pro všechny RunAs části platné hodnoty jsou "LocalUser/uživatele DomainUser nebo NetworkService/ManagedServiceAccount/LocalSystem". |
|Spustit_jako_heslo|řetězec, výchozí hodnota je "" |Dynamická|Určuje heslo k účtu RunAs. To je potřeba jenom pro typ účtu "uživatele DomainUser". |

## <a name="runasfabric"></a>RunAs_Fabric
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|RunAsAccountName |řetězec, výchozí hodnota je "" |Dynamická|Určuje název účtu RunAs. To je potřeba jenom pro účet "DomainUser" nebo "ManagedServiceAccount" typu. Platné hodnoty jsou "doména\uživatel" nebo "user@domain". |
|Typ účtu spustit jako|řetězec, výchozí hodnota je "" |Dynamická|Určuje typ účtu spustit jako. To je potřeba pro všechny RunAs části platné hodnoty jsou "LocalUser/uživatele DomainUser nebo NetworkService/ManagedServiceAccount/LocalSystem". |
|Spustit_jako_heslo|řetězec, výchozí hodnota je "" |Dynamická|Určuje heslo k účtu RunAs. To je potřeba jenom pro typ účtu "uživatele DomainUser". |

## <a name="runashttpgateway"></a>RunAs_HttpGateway
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|RunAsAccountName |řetězec, výchozí hodnota je "" |Dynamická|Určuje název účtu RunAs. To je potřeba jenom pro účet "DomainUser" nebo "ManagedServiceAccount" typu. Platné hodnoty jsou "doména\uživatel" nebo "user@domain". |
|Typ účtu spustit jako|řetězec, výchozí hodnota je "" |Dynamická|Určuje typ účtu spustit jako. To je potřeba pro všechny RunAs části platné hodnoty jsou "LocalUser/uživatele DomainUser nebo NetworkService/ManagedServiceAccount/LocalSystem". |
|Spustit_jako_heslo|řetězec, výchozí hodnota je "" |Dynamická|Určuje heslo k účtu RunAs. To je potřeba jenom pro typ účtu "uživatele DomainUser". |

## <a name="security"></a>Zabezpečení
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu**| **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|AADClientApplication|řetězec, výchozí hodnota je ""|Statická|Název nativní klientské aplikace nebo Identifikátor představující Fabric klientů |
|AADClusterApplication|řetězec, výchozí hodnota je ""|Statická|Název aplikace webového rozhraní API nebo Identifikátor představující clusteru |
|AADTenantId|řetězec, výchozí hodnota je ""|Statická|ID tenanta (GUID) |
|AdminClientCertThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Kryptografické otisky certifikátů používaných klienty v roli správce. Je seznam čárkami oddělených názvů. |
|AdminClientClaims|řetězec, výchozí hodnota je ""|Dynamická|Všechny možné deklarací, od správce klientů; stejný formát jako ClientClaims; Tento seznam interně přidá do ClientClaims; proto není potřeba přidat také stejné položky ClientClaims. |
|AdminClientIdentities|řetězec, výchozí hodnota je ""|Dynamická|Identity Windows fabric klientů v roli správce použít k autorizaci fabric privilegované operace. Je čárkami oddělený seznam; Každá položka je název účtu domény nebo název skupiny. Pro usnadnění práce; účet, který spouští fabric.exe se automaticky přiřadí role správce; Proto je seskupit ServiceFabricAdministrators. |
|CertificateExpirySafetyMargin|Časový interval, výchozí hodnota je Common::TimeSpan::FromMinutes(43200)|Statická|Zadejte časový interval v sekundách. Bezpečný přístup z více rozpětí pro vypršení platnosti certifikátu; Sestava Stav certifikátu změní z OK upozornění při vypršení platnosti je blíže než to. Výchozí hodnota je 30 dní. |
|CertificateHealthReportingInterval|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(3600 * 8)|Statická|Zadejte časový interval v sekundách. Zadejte interval pro vykazování stavu certifikátu; výchozí na 8 hodin; nastavení na hodnotu 0 zakazuje generování sestav stavu certifikátu |
|ClientCertThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Kryptografické otisky certifikátů používaných klienty komunikovat s clusteru; cluster používá toto povolit příchozí připojení. Je seznam čárkami oddělených názvů. |
|ClientClaimAuthEnabled|Logická hodnota, výchozí hodnotu FALSE|Statická|Označuje, pokud je povoleno ověřování na základě deklarace identity na klientských počítačích; Pokud typ nastavíte hodnotu true implicitně nastaví ClientRoleEnabled. |
|ClientClaims|řetězec, výchozí hodnota je ""|Dynamická|Všechny možné deklarace identity pro připojování k bráně očekává od klientů. Toto je seznam 'OR': ClaimsEntry \| \| ClaimsEntry \| \| ClaimsEntry... "A" seznam se každý ClaimsEntry: typu deklarace identity = ClaimValue & & typu deklarace identity = ClaimValue & & typu deklarace identity = ClaimValue... |
|ClientIdentities|řetězec, výchozí hodnota je ""|Dynamická|Windows identity FabricClient; pojmenování brána používá toto povolit příchozí připojení. Je čárkami oddělený seznam; Každá položka je název účtu domény nebo název skupiny. Pro usnadnění práce; účet, který spouští fabric.exe je automaticky povolen; Proto jsou skupině ServiceFabricAllowedUsers a ServiceFabricAdministrators. |
|ClientRoleEnabled|Logická hodnota, výchozí hodnotu FALSE|Statická|Určuje, zda je povolena klientská role; Pokud je nastavena na hodnotu true; Klienti se přiřadí role na základě jejich identit. Pro V2; Umožňuje to znamená, že klient není v AdminClientCommonNames/AdminClientIdentities může být vykonávána pouze operace určené jen pro čtení. |
|ClusterCertThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Kryptografické otisky certifikátů, moct připojit ke clusteru; seznam čárkami oddělených názvů. |
|ClusterCredentialType|řetězec, výchozí je "None"|Nepovolené|Určuje typ zabezpečovací přihlašovací údaje pro použití za účelem zabezpečení clusteru. Platné hodnoty jsou "None/X509/Windows" |
|ClusterIdentities|řetězec, výchozí hodnota je ""|Dynamická|Identity Windows uzlů clusteru; používá pro autorizaci členství v clusteru. Je čárkami oddělený seznam; Každá položka je název účtu domény nebo název skupiny |
|ClusterSpn|řetězec, výchozí hodnota je ""|Nepovolené|Hlavní název služby clusteru; Když fabric funguje jako jednu doménu uživatele (uživatelský účet gMSA a domény). Je název SPN naslouchacích procesů zapůjčení a moduly pro naslouchání v fabric.exe: federační naslouchacích procesů; naslouchací procesy interní replikace; naslouchací proces služby modulu runtime a pojmenování naslouchací proces brány. To by měla zůstat prázdná při fabric funguje jako účty počítače; v takovém případě připojení na straně služby compute naslouchací proces hlavního názvu služby z naslouchacího procesu přenosu adresy. |
|CrlCheckingFlag|uint, výchozí je 0x40000000|Dynamická|Výchozí příznak ověření řetězu certifikátu; může být přepsána specifické komponenty příznak; například Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ Podle dwFlags CertGetCertificateChain je uvedeno pouze nastavení 0 zakáže seznamu CRL kontrola, zda úplný seznam podporovaných hodnot: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|Časový interval, výchozí hodnota je Common::TimeSpan::FromMinutes(15)|Dynamická|Zadejte časový interval v sekundách. Jak dlouho kontrola CRL je zakázaná pro daný certifikát po zjištění chyby offline; Pokud seznam CRL offline chybu můžete ignorovat. |
|CrlOfflineHealthReportTtl|Časový interval, výchozí hodnota je Common::TimeSpan::FromMinutes(1440)|Dynamická|Zadejte časový interval v sekundách. |
|DisableFirewallRuleForDomainProfile| Logická hodnota, výchozí hodnotu TRUE |Statická| Označuje, pokud by nemělo být povoleno pravidlo brány firewall pro profil domény |
|DisableFirewallRuleForPrivateProfile| Logická hodnota, výchozí hodnotu TRUE |Statická| Označuje, pokud by nemělo být povoleno pravidlo brány firewall pro privátní profil | 
|DisableFirewallRuleForPublicProfile| Logická hodnota, výchozí hodnotu TRUE | Statická|Označuje, pokud by nemělo být povoleno pravidlo brány firewall pro veřejný profil |
|FabricHostSpn| řetězec, výchozí hodnota je "" |Statická| Hlavní název služby z hostitele FabricHost; Když fabric funguje jako jednu doménu uživatele (uživatelský účet gMSA a domény) a hostitele FabricHost běží pod účtem počítače. Je název SPN IPC naslouchacího procesu pro hostitele FabricHost; která ve výchozím nastavení by měla zůstat prázdná od hostitele FabricHost běží pod účtem počítače |
|IgnoreCrlOfflineError|Logická hodnota, výchozí hodnotu FALSE|Dynamická|Jestli se do seznamu odvolaných certifikátů offline chybu ignorovat, když ověří příchozí klientské certifikáty na straně serveru |
|IgnoreSvrCrlOfflineError|Logická hodnota, výchozí hodnotu TRUE|Dynamická|Jestli se do seznamu odvolaných certifikátů offline chybu ignorovat, když na straně klienta ověří příchozí certifikáty serveru; Výchozí nastavení na hodnotu true. Útoky s využitím certifikátů odvolaný serveru vyžadovat porušení zabezpečení DNS. obtížnější než s odvolané klientské certifikáty. |
|ServerAuthCredentialType|řetězec, výchozí je "None"|Statická|Určuje typ pověření zabezpečení má použít k zabezpečení komunikace mezi FabricClient a clusteru. Platné hodnoty jsou "None/X509/Windows" |
|ServerCertThumbprints|řetězec, výchozí hodnota je ""|Dynamická|Kryptografické otisky certifikátů serveru používaného clusterem ke komunikaci s klienty používají klienti k ověření clusteru. Je seznam čárkami oddělených názvů. |
|SettingsX509StoreName| řetězec, výchozí je "MY"| Dynamická|X509 úložiště používané prostředky infrastruktury pro ochranu konfigurace certifikátů |
|UseClusterCertForIpcServerTlsSecurity|Logická hodnota, výchozí hodnotu FALSE|Statická|Jestli se má použít certifikát clusteru k zabezpečení IPC Server TLS přenosu jednotek |
|X509Folder|řetězec, výchozí je /var/lib/waagent|Statická|Složka kde X509 certifikátů a privátních klíčů jsou umístěné |

## <a name="securityadminclientx509names"></a>Zabezpečení/AdminClientX509Names
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, výchozí hodnota je None|Dynamická|Toto je seznam dvojic "Name" a "Hodnota". Každý "Name" je běžný název subjektu nebo DnsName X509 certifikáty oprávnění pro správu klientské operace. Pro daný "název", "Value" je na čárkami oddělený seznam kryptografické otisky certifikátů pro vystavitele, připínání, pokud není prázdná, přímého vystavitele správce klientské certifikáty musí být v seznamu. |

## <a name="securityclientaccess"></a>Zabezpečení/ClientAccess
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ActivateNode |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro aktivaci se uzel. |
|CancelTestCommand |řetězec, výchozí je "Admin" |Dynamická| Zruší konkrétní TestCommand – Pokud je v letu. |
|CodePackageControl |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro restartování balíčky kódu. |
|CreateApplication |řetězec, výchozí je "Admin" | Dynamická|Konfigurace zabezpečení pro vytváření aplikací. |
|CreateComposeDeployment|řetězec, výchozí je "Admin"| Dynamická|Vytvoří nasazení compose popsal soubory compose |
|CreateName |řetězec, výchozí je "Admin" |Dynamická|Konfigurace zabezpečení pro identifikátor URI pojmenování vytvoření. |
|CreateService |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení k tvorbě služeb. |
|CreateServiceFromTemplate |řetězec, výchozí je "Admin" |Dynamická|Konfigurace zabezpečení pro vytvoření služby ze šablony. |
|CreateVolume|řetězec, výchozí je "Admin"|Dynamická|Vytvoří svazek |
|DeactivateNode |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro deaktivace uzlu. |
|DeactivateNodesBatch |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro deaktivaci více uzlů. |
|Odstranění |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro image ukládání operace odstranění klienta. |
|DeleteApplication |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro odstranění aplikace. |
|DeleteComposeDeployment|řetězec, výchozí je "Admin"| Dynamická|Odstraní nasazení compose |
|DeleteName |řetězec, výchozí je "Admin" |Dynamická|Konfigurace zabezpečení pro identifikátor URI pojmenování odstranění. |
|DeleteService |řetězec, výchozí je "Admin" |Dynamická|Konfigurace zabezpečení pro odstranění služby. |
|DeleteVolume|řetězec, výchozí je "Admin"|Dynamická|Odstraní svazku.| 
|EnumerateProperties |řetězec, výchozí hodnota je "správce\|\|uživatele" | Dynamická|Konfigurace zabezpečení pro pojmenování výčet vlastností. |
|EnumerateSubnames |řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Konfigurace zabezpečení pro identifikátor URI pojmenování výčtu. |
|FileContent |řetězec, výchozí je "Admin" |Dynamická| Přenos souborů klienta (externí clusteru) úložiště konfigurace zabezpečení pro bitovou kopii. |
|FileDownload |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro image store klienta soubor ke stažení zahájení (mimo cluster). |
|FinishInfrastructureTask |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro dokončení úlohy infrastruktury. |
|GetChaosReport | řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Načte stav Chaos v daném časovém rozsahu. |
|GetClusterConfiguration | řetězec, výchozí hodnota je "správce\|\|uživatele" | Dynamická|Indukuje GetClusterConfiguration na oddíl. |
|GetClusterConfigurationUpgradeStatus | řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Indukuje GetClusterConfigurationUpgradeStatus na oddíl. |
|GetFabricUpgradeStatus |řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Konfigurace zabezpečení pro dotazování stav upgradu clusteru. |
|GetNodeDeactivationStatus |řetězec, výchozí je "Admin" |Dynamická| Kontrola stavu deaktivace konfiguraci zabezpečení. |
|GetNodeTransitionProgress | řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Konfigurace zabezpečení pro získání průběh na přechod příkaz uzlu. |
|GetPartitionDataLossProgress | řetězec, výchozí hodnota je "správce\|\|uživatele" | Dynamická|Načte průběhu pro volání rozhraní api ke ztrátě dat vyvolat. |
|GetPartitionQuorumLossProgress | řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Načte průběhu pro volání rozhraní api ke ztrátě kvora invoke. |
|GetPartitionRestartProgress | řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Načte průběhu pro volání rozhraní api oddílu restartování. |
|GetServiceDescription |řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Konfigurace zabezpečení pro oznámení služby dlouho dotazování a čtení popis služeb. |
|GetStagingLocation |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro image ukládat pracovní umístění načítání klienta. |
|GetStoreLocation |řetězec, výchozí je "Admin" |Dynamická| Načtení umístění úložiště klienta úložiště konfigurace zabezpečení pro bitovou kopii. |
|GetUpgradeOrchestrationServiceState|řetězec, výchozí je "Admin"| Dynamická|Indukuje GetUpgradeOrchestrationServiceState na oddíl |
|GetUpgradesPendingApproval |řetězec, výchozí je "Admin" |Dynamická| Indukuje GetUpgradesPendingApproval na oddíl. |
|GetUpgradeStatus |řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Konfigurace zabezpečení pro dotazování stav upgradu aplikace. |
|InternalList |řetězec, výchozí je "Admin" | Dynamická|Konfigurace zabezpečení pro image ukládání operace seznam souborů klienta (interní). |
|InvokeContainerApi|řetězec, výchozí je "Admin"|Dynamická|Volání rozhraní API kontejneru |
|InvokeInfrastructureCommand |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro příkazy pro správu infrastruktury úloh. |
|InvokeInfrastructureQuery |řetězec, výchozí hodnota je "správce\|\|uživatele" | Dynamická|Konfigurace zabezpečení pro dotazování infrastrukturních úloh. |
|Seznam |řetězec, výchozí hodnota je "správce\|\|uživatele" | Dynamická|Konfigurace zabezpečení pro image ukládání operace seznam souborů klienta. |
|MoveNextFabricUpgradeDomain |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro obnovení inovace clusteru se výslovně upgradu domény. |
|MoveNextUpgradeDomain |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro obnovení upgrady aplikací s explicitní doménou upgradu. |
|MoveReplicaControl |řetězec, výchozí je "Admin" | Dynamická|Přesuňte repliky. |
|NameExists |řetězec, výchozí hodnota je "správce\|\|uživatele" | Dynamická|Konfigurace zabezpečení pro identifikátor URI pojmenování kontroly existence. |
|NodeControl |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro spuštění; Probíhá zastavování. a restartování uzlů. |
|NodeStateRemoved |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro vytváření sestav stavu uzel odebrán. |
|Ping |řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Konfigurace zabezpečení pro příkazy ping pro zjištění klienta. |
|PredeployPackageToNode |řetězec, výchozí je "Admin" |Dynamická| Před nasazením rozhraní api. |
|PrefixResolveService |řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Konfigurace zabezpečení pro překlad předpony služeb na základě předpisy. |
|PropertyReadBatch |řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Operace čtení konfiguraci zabezpečení pro zásady vytváření názvů vlastností. |
|PropertyWriteBatch |řetězec, výchozí je "Admin" |Dynamická|Operace zápisu konfiguracích zabezpečení pro zásady vytváření názvů vlastností. |
|ProvisionApplicationType |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro zřízení typu aplikace. |
|ProvisionFabric |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro MSI a/nebo clusteru manifestu zřizování. |
|Dotaz |řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Konfigurace zabezpečení pro dotazy. |
|RecoverPartition |řetězec, výchozí je "Admin" | Dynamická|Konfigurace zabezpečení pro obnovení oddílu. |
|RecoverPartitions |řetězec, výchozí je "Admin" | Dynamická|Konfigurace zabezpečení pro obnovení oddíly. |
|RecoverServicePartitions |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro obnovení oddílů služby. |
|RecoverSystemPartitions |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro obnovení oddílů systémové služby. |
|RemoveNodeDeactivations |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro vracení k uloženým standardním deaktivaci ve více uzlech. |
|ReportFabricUpgradeHealth |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro obnovení inovace clusteru se aktuální průběh upgradu. |
|ReportFault |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro hlášení chyb. |
|ReportHealth |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro vytváření sestav stavu. |
|ReportUpgradeHealth |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro obnovení upgrady aplikací s aktuální průběh upgradu. |
|ResetPartitionLoad |řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Konfigurace zabezpečení pro zatížení failoverUnit resetování. |
|ResolveNameOwner |řetězec, výchozí hodnota je "správce\|\|uživatele" | Dynamická|Konfigurace zabezpečení pro identifikátor URI pojmenování vlastníka řešení. |
|ResolvePartition |řetězec, výchozí hodnota je "správce\|\|uživatele" | Dynamická|Konfigurace zabezpečení pro řešení systémových služeb. |
|ResolveService |řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Konfigurace zabezpečení pro řešení služeb na základě předpisy. |
|ResolveSystemService|řetězec, výchozí hodnota je "správce\|\|uživatele"|Dynamická| Konfigurace zabezpečení pro řešení systémových služeb |
|RollbackApplicationUpgrade |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro vrácení zpět upgrady aplikací. |
|RollbackFabricUpgrade |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro vrácení zpět upgrady clusteru. |
|ServiceNotifications |řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Konfigurace zabezpečení pro oznámení služby založené na událostech. |
|SetUpgradeOrchestrationServiceState|řetězec, výchozí je "Admin"| Dynamická|Indukuje SetUpgradeOrchestrationServiceState na oddíl |
|StartApprovedUpgrades |řetězec, výchozí je "Admin" |Dynamická| Indukuje StartApprovedUpgrades na oddíl. |
|StartChaos |řetězec, výchozí je "Admin" |Dynamická| Spustí Chaos – Pokud ještě není spuštěný. |
|StartClusterConfigurationUpgrade |řetězec, výchozí je "Admin" |Dynamická| Indukuje StartClusterConfigurationUpgrade na oddíl. |
|StartInfrastructureTask |řetězec, výchozí je "Admin" | Dynamická|Konfigurace zabezpečení pro spuštění úlohy infrastruktury. |
|StartNodeTransition |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro spuštění uzlu přechodu. |
|StartPartitionDataLoss |řetězec, výchozí je "Admin" |Dynamická| Indukuje ztrátu dat na oddíl. |
|StartPartitionQuorumLoss |řetězec, výchozí je "Admin" |Dynamická| Indukuje ztráty kvora na oddíl. |
|StartPartitionRestart |řetězec, výchozí je "Admin" |Dynamická| Zároveň restartuje některé nebo všechny repliky oddílu. |
|StopChaos |řetězec, výchozí je "Admin" |Dynamická| Chaos - zastaví, pokud byla spuštěna. |
|ToggleVerboseServicePlacementHealthReporting | řetězec, výchozí hodnota je "správce\|\|uživatele" |Dynamická| Konfigurace zabezpečení pro přepínání podrobné ServicePlacement HealthReporting. |
|UnprovisionApplicationType |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro zrušení zřízení typu aplikace. |
|UnprovisionFabric |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro MSI a/nebo clusteru manifestu zrušení zřízení. |
|UnreliableTransportControl |řetězec, výchozí je "Admin" |Dynamická| Nespolehlivém přenosu pro přidávání a odebírání chování. |
|UpdateService |řetězec, výchozí je "Admin" |Dynamická|Konfigurace zabezpečení pro aktualizace služby. |
|UpgradeApplication |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro spuštění nebo by bylo třeba přerušit upgrady aplikací. |
|UpgradeComposeDeployment|řetězec, výchozí je "Admin"| Dynamická|Upgrade nasazení compose |
|UpgradeFabric |řetězec, výchozí je "Admin" |Dynamická| Konfigurace zabezpečení pro spuštění inovace clusteru. |
|Odeslat |řetězec, výchozí je "Admin" | Dynamická|Operace nahrávání klienta úložiště konfigurace zabezpečení pro bitovou kopii. |

## <a name="securityclientcertificateissuerstores"></a>Zabezpečení/ClientCertificateIssuerStores
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, výchozí hodnota je None |Dynamická|X509 úložiště certifikátů vystavitele pro klientské certifikáty; Název = clientIssuerCN; Hodnota = čárkami oddělený seznam úložišť |

## <a name="securityclientx509names"></a>Zabezpečení/ClientX509Names
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, výchozí hodnota je None|Dynamická|Toto je seznam dvojic "Name" a "Hodnota". Každý "Name" je běžný název subjektu nebo DnsName X509 certifikáty, které jsou autorizované pro klientské operace. Pro daný "název", "Value" je na čárkami oddělený seznam kryptografické otisky certifikátů pro vystavitele, připínání, pokud není prázdná, přímého vystavitele klientské certifikáty musí být v seznamu.|

## <a name="securityclustercertificateissuerstores"></a>Zabezpečení/ClusterCertificateIssuerStores
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, výchozí hodnota je None |Dynamická|X509 úložiště certifikátů vystavitele pro certifikáty clusteru; Název = clusterIssuerCN; Hodnota = čárkami oddělený seznam úložišť |

## <a name="securityclusterx509names"></a>Zabezpečení/ClusterX509Names
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, výchozí hodnota je None|Dynamická|Toto je seznam dvojic "Name" a "Hodnota". Každý "Name" je běžný název subjektu nebo DnsName X509 certifikáty oprávnění pro operace clusteru. Pro daný "název", "Value" je na čárkami oddělený seznam kryptografické otisky certifikátů pro vystavitele, připínání, pokud není prázdná, přímého vystavitele certifikátů clusteru musí být v seznamu.|

## <a name="securityservercertificateissuerstores"></a>Zabezpečení/ServerCertificateIssuerStores
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, výchozí hodnota je None |Dynamická|X509 úložiště certifikátů vystavitele pro certifikát serveru; Název = serverIssuerCN; Hodnota = čárkami oddělený seznam úložišť |

## <a name="securityserverx509names"></a>Zabezpečení/ServerX509Names
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, výchozí hodnota je None|Dynamická|Toto je seznam dvojic "Name" a "Hodnota". Každý "Name" je běžný název subjektu nebo DnsName X509 certifikáty, které jsou autorizované pro operace serveru. Pro daný "název", "Value" je na čárkami oddělený seznam kryptografické otisky certifikátů pro vystavitele, připínání, pokud není prázdná, přímého vystavitele certifikátů serveru musí být v seznamu.|

## <a name="setup"></a>Nastavení
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ContainerNetworkName|řetězec, výchozí hodnota je ""| Statická |Název sítě pro použití při vytváření kontejneru sítě.|
|ContainerNetworkSetup|Logická hodnota, výchozí hodnotu FALSE| Statická |Určuje, zda nastavení sítě kontejneru.|
|FabricDataRoot |Řetězec | Nepovolené |Service Fabric data kořenový adresář. Výchozí pro Azure je d:\svcfab |
|FabricLogRoot |Řetězec | Nepovolené |Service fabric protokolu kořenový adresář. Je to, kde jsou umístěny SF protokoly a trasování. |
|NodesToBeRemoved|řetězec, výchozí hodnota je ""| Dynamická |Uzly, které byste měli odebrat jako součást konfigurace upgradu. (Pouze pro samostatné nasazení)|
|ServiceRunAsAccountName |Řetězec | Nepovolené |Název účtu, pod kterým chcete spustit service fabric hostitele. |
|SkipContainerNetworkResetOnReboot|Logická hodnota, výchozí hodnotu FALSE|Nepovoleno|Určuje, zda se mají přeskočit obnovení kontejner sítě při restartování.|
|SkipFirewallConfiguration |Logická hodnota, výchozí hodnota je false | Nepovolené |Určuje, pokud nastavení brány firewall, který je potřeba nastavit v systému nebo ne. To platí jenom v případě, že používáte bránu windows firewall. Pokud používáte brány firewall třetích stran, pak je nutné otevřít porty pro systém a aplikace, aby používaly |

## <a name="tokenvalidationservice"></a>TokenValidationService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|Poskytovatelé |řetězec, výchozí je "DSTS" |Statická|Čárkami oddělený seznam zprostředkovatelů ověření tokenu umožňující (platný zprostředkovatelé jsou: DSTS; AAD). Aktuálně lze kdykoli povolit pouze jednoho poskytovatele. |

## <a name="traceetw"></a>Trasování/trasování událostí pro Windows
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|Úroveň |Int, výchozí hodnota je 4 | Dynamická |Úroveň trasování událostí pro Windows trasování může přijmout hodnoty 1, 2, 3, 4. Podporovaná je nutné zachovat úroveň trasování na 4 |

## <a name="transactionalreplicator"></a>Transakční Replikátor
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Čas v sekundách, výchozí hodnota je 0,015 | Statická | Zadejte časový interval v sekundách. Určuje množství času, které Replikátor čeká po přijetí operace před odesláním zpět na potvrzení. Další operace přijatých během tohoto období budou mít jejich potvrzení odeslaných zpět do jedné zprávy -> snížení síťový provoz, ale potenciálně snížení propustnosti replikátoru. |
|MaxCopyQueueSize |Uint, výchozí hodnota je 16384 | Statická |Toto je maximální hodnota definuje počáteční velikost fronty, který udržuje operací replikace. Všimněte si, že musí být mocninou čísla 2. Pokud za běhu zvětšuje, aby tato operace velikost fronty se omezí mezi primárním a sekundárním replikátorů. |
|MaxPrimaryReplicationQueueMemorySize |Uint, výchozí hodnota je 0 | Statická |To je maximální hodnota primární replikace fronty v bajtech. |
|MaxPrimaryReplicationQueueSize |Uint, výchozí hodnota je 8192 | Statická |Toto je maximální počet operací, které může existovat ve frontě primární replikace. Všimněte si, že musí být mocninou čísla 2. |
|MaxReplicationMessageSize |uint, výchozí je 52428800 | Statická | Maximální velikost zprávy operací replikace. Výchozí hodnota je 50MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, výchozí hodnota je 0 | Statická |To je maximální hodnota fronty sekundární replikace v bajtech. |
|MaxSecondaryReplicationQueueSize |Uint, výchozí hodnota je 16384 | Statická |Toto je maximální počet operací, které může existovat ve frontě replikací sekundární. Všimněte si, že musí být mocninou čísla 2. |
|ReplicatorAddress |řetězec, výchozí je "localhost:0" | Statická | Koncový bod v podobě řetězce-"IP: port" který používá Replikátor Windows Fabric pro navázání připojení s ostatními replikami k posílání a přijímání operace. |

## <a name="transport"></a>Přenos
| **Parametr** | **Povolené hodnoty** |**Zásady upgradu** |**Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(60)|Statická|Zadejte časový interval v sekundách. Vypršení časového limitu pro nastavení připojení pro příchozí a přijímá na straně (včetně vyjednávání zabezpečení v zabezpečeném režimu) |
|FrameHeaderErrorCheckingEnabled|Logická hodnota, výchozí hodnotu TRUE|Statická|Výchozí nastavení pro kontrolu chyb na záhlaví rámce v nezabezpečené režimu. komponenta nastavení přepíše tuto položku. |
|MessageErrorCheckingEnabled|Logická hodnota, výchozí hodnotu FALSE|Statická|Výchozí nastavení pro kontrolu chyb na záhlaví zprávy a text v nezabezpečené režimu. komponenta nastavení přepíše tuto položku. |
|ResolveOption|řetězec, výchozí hodnota je "neurčené"|Statická|Určuje, jak je přeložit plně kvalifikovaný název domény.  Platné hodnoty jsou "Tento parametr zadán/ipv4/ipv6". |
|SendTimeout|Časový interval, výchozí hodnota je Common::TimeSpan::FromSeconds(300)|Dynamická|Zadejte časový interval v sekundách. Odešlete časový limit pro zjišťování zablokované připojení. Sestavy selhání TCP nejsou spolehlivé v některé prostředí. To možná bude potřeba upravit podle dostupnou šířku pásma sítě a objem odchozích dat (\*MaxMessageSize\/\*SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|Autoupgradeenabled – | Logická hodnota, výchozí hodnota je true |Statická| Automatické cyklického dotazování a akce upgradu na základě souboru cílový stav. |
|MinReplicaSetSize |int, výchozí je 0 |Statická |MinReplicaSetSize pro UpgradeOrchestrationService.
|PlacementConstraints | řetězec, výchozí hodnota je "" |Statická| PlacementConstraints pro UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Čas v sekundách, výchozí hodnota je hodnota MaxValue |Statická| Zadejte časový interval v sekundách. QuorumLossWaitDuration pro UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Čas v sekundách, výchozí hodnota je 60 minut|Statická| Zadejte časový interval v sekundách. ReplicaRestartWaitDuration pro UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Čas v sekundách, výchozí hodnota je 60*24*7 minut |Statická| Zadejte časový interval v sekundách. StandByReplicaKeepDuration pro UpgradeOrchestrationService. |
|TargetReplicaSetSize |int, výchozí je 0 |Statická |TargetReplicaSetSize pro UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Logická hodnota, výchozí hodnota je false | Statická|Nastavení provést upgrade kódu vyžadovat schválení správce, než budete pokračovat. |

## <a name="upgradeservice"></a>UpgradeService
| **Parametr** | **Povolené hodnoty** | **Zásady upgradu** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
|BaseUrl | řetězec, výchozí hodnota je "" |Statická|BaseUrl pro UpgradeService. |
|ClusterId | řetězec, výchozí hodnota je "" |Statická|ClusterId pro UpgradeService. |
|CoordinatorType | řetězec, výchozí je "WUTest"|Nepovolené|CoordinatorType pro UpgradeService. |
|MinReplicaSetSize | Int, výchozí hodnota je 2 |Nepovolené| MinReplicaSetSize pro UpgradeService. |
|OnlyBaseUpgrade | Logická hodnota, výchozí hodnota je false |Dynamická|OnlyBaseUpgrade pro UpgradeService. |
|PlacementConstraints |řetězec, výchozí hodnota je "" |Nepovolené|PlacementConstraints upgradu služby. |
|TargetReplicaSetSize | Int, výchozí hodnota je 3 |Nepovolené| TargetReplicaSetSize pro UpgradeService. |
|TestCabFolder | řetězec, výchozí hodnota je "" |Statická| TestCabFolder pro UpgradeService. |
|X509FindType | řetězec, výchozí hodnota je ""|Dynamická| X509FindType pro UpgradeService. |
|X509FindValue | řetězec, výchozí hodnota je "" |Dynamická| X509FindValue pro UpgradeService. |
|X509SecondaryFindValue | řetězec, výchozí hodnota je "" |Dynamická| X509SecondaryFindValue pro UpgradeService. |
|X509StoreLocation | řetězec, výchozí hodnota je "" |Dynamická| X509StoreLocation pro UpgradeService. |
|X509StoreName | řetězec, výchozí hodnota je "My"|Dynamická|X509StoreName pro UpgradeService. |

## <a name="next-steps"></a>Další postup
Přečtěte si tyto články pro další informace o správě clusteru:

[Přidat, Nespotřebujete, odeberte certifikáty z clusteru Azure ](service-fabric-cluster-security-update-certs-azure.md) 

