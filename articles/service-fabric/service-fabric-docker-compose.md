---
title: Azure Service Fabric Docker compose náhled nasazení
description: Azure Service Fabric přijímá formát Docker Compose, aby bylo snazší organizovat existující kontejnery pomocí Service Fabric. Tato podpora je v současné době ve verzi Preview.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282455"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Podpora nasazení Docker ukomponování ve službě Azure Fabric (preview)

Docker používá soubor [docker-compose.yml](https://docs.docker.com/compose) pro definování aplikací s více kontejnery. Abychom zákazníkům obeznámeným s Dockerem usnadnili orchestrazování existujících kontejnerových aplikací ve službě Azure Service Fabric, nativně jsme zahrnuli podporu náhledu pro nasazení Docker Compose nativně na platformě. Service Fabric může přijímat verze `docker-compose.yml` 3 a novější soubory. 

Vzhledem k tomu, že tato podpora je ve verzi Preview, je podporována pouze podmnožina direktivy compose.

Chcete-li použít tento náhled, vytvořte cluster s verzí 5.7 nebo vyšší runtime Service Fabric prostřednictvím portálu Azure spolu s odpovídající sadou SDK. 

> [!NOTE]
> Tato funkce je ve verzi Preview a není podporována v produkčním prostředí.
> Níže uvedené příklady jsou založeny na runtime verze 6.0 a SDK verze 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Nasazení souboru Docker Compose na Service Fabric

Následující příkazy vytvořit service fabric `fabric:/TestContainerApp`aplikace (s názvem), které můžete sledovat a spravovat jako všechny ostatní aplikace Service Fabric. Pro dotazy na stav můžete použít zadaný název aplikace.
Service Fabric rozpozná "DeploymentName" jako identifikátor nasazení Compose.

### <a name="use-powershell"></a>Použití prostředí PowerShell

Vytvořte nasazení service fabric compose ze souboru docker-compose.yml spuštěním následujícího příkazu v Prostředí PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName`a `RegistryPassword` podívejte se na uživatelské jméno a heslo registru kontejnerů. Po dokončení nasazení můžete zkontrolovat jeho stav pomocí následujícího příkazu:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Chcete-li odstranit nasazení compose prostřednictvím prostředí PowerShell, použijte následující příkaz:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Chcete-li zahájit upgrade nasazení compose prostřednictvím prostředí PowerShell, použijte následující příkaz:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Chcete-li vrátit upgrade nasazení compose prostřednictvím prostředí PowerShell, použijte následující příkaz:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Po přijetí upgradu lze průběh upgradu sledovat pomocí následujícího příkazu:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Použití příkazového příkazového příkazu Azure Service Fabric (sfctl)

Případně můžete použít následující příkaz příkazu příkazu příkazu příkazu příkazu příkazu Příkaz příkazu Service Fabric:

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Po vytvoření nasazení můžete zkontrolovat jeho stav pomocí následujícího příkazu:

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Chcete-li odstranit nasazení compose, použijte následující příkaz:

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Chcete-li zahájit upgrade nasazení compose, použijte následující příkaz:

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Chcete-li vrátit upgrade nasazení compose, použijte následující příkaz:

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Po přijetí upgradu lze průběh upgradu sledovat pomocí následujícího příkazu:

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Podporované direktivy pro skládání

Tento náhled podporuje podmnožinu možností konfigurace z formátu Compose verze 3, včetně následujících primitiv:

* Služby > nasadit > repliky
* Služby > nasazení > omezení umístění >
* Služby > nasazení limitů > prostředků >
    * -cpu-akcie
    * -paměť
    * -výměna paměti
* Příkazy služby >
* Služby > prostředí
* Porty > služeb
* Image služeb >
* Služby > izolace (pouze pro Windows)
* Ovladače > protokolování služeb >
* > Možnosti > > služby
* Svazek & nasazení > svazku

Nastavte cluster pro vynucení omezení prostředků, jak je popsáno v části [Zásadsprávné řízení prostředků Service Fabric](service-fabric-resource-governance.md). Všechny ostatní direktivy Docker Compose nejsou pro tento náhled podporovány.

### <a name="ports-section"></a>Sekce Porty

Zadejte protokol http nebo https v části Porty, který bude používat naslouchací proces služby Service Fabric. Tím zajistíte, že protokol koncového bodu je publikován správně se službou pojmenování, aby reverzní proxy server předal požadavky:
* Chcete-li směrovat k nezabezpečené služby Service Fabric Compose, zadejte **/http**. Například - **"80:80/http"**.
* Chcete-li směrovat k zabezpečené služby Service Fabric Compose, zadejte **/https**. Například - **"443:443/https"**.

> [!NOTE]
> Syntaxe oddílu /http a /https Ports je specifická pro service fabric pro registraci správné adresy URL naslouchacího procesu Service Fabric.  Pokud je syntaxe souboru komponovaného souboru Dockeru programově ověřena, může to způsobit chybu ověření.

## <a name="servicednsname-computation"></a>ServiceDnsName výpočtu

Pokud je název služby, který zadáte v souboru Compose, plně kvalifikovaný název domény (to znamená, že `<ServiceName>` obsahuje tečku [.]), je název DNS registrovaný service fabric (včetně tečky). Pokud tomu tak není, každý segment cesty v názvu aplikace se stane popiskem domény v názvu DNS služby, přičemž první segment cesty se stane popiskem domény nejvyšší úrovně.

Pokud je `fabric:/SampleApp/MyComposeApp`například zadaný `<ServiceName>.MyComposeApp.SampleApp` název aplikace , bude registrovaný název DNS.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Napsat nasazení (definice instance) versus model aplikace Service Fabric (definice typu)

Soubor docker-compose.yml popisuje nasaditelné sadu kontejnerů, včetně jejich vlastnosti a konfigurace.
Soubor může například obsahovat proměnné prostředí a porty. V souboru docker-compose.yml můžete také určit parametry nasazení, jako jsou omezení umístění, omezení prostředků a názvy DNS.

[Aplikační model Service Fabric](service-fabric-application-model.md) používá typy služeb a typy aplikací, kde můžete mít mnoho instancí aplikace stejného typu. Můžete mít například jednu instanci aplikace na zákazníka. Tento model založený na typu podporuje více verzí stejného typu aplikace, která je registrována s runtime.

Například zákazník A může mít aplikaci instanci s typem 1.0 AppTypeA a zákazník B může mít jinou aplikaci instanci se stejným typem a verzí. Definujete typy aplikací v manifestech aplikace a při vytváření aplikace zadáte název aplikace a parametry nasazení.

Přestože tento model nabízí flexibilitu, plánujeme také podporovat jednodušší model nasazení založený na instanci, kde jsou typy implicitní ze souboru manifestu. V tomto modelu každá aplikace získá svůj vlastní nezávislý manifest. Jsme náhled tohoto úsilí přidáním podpory pro docker-compose.yml, což je formát nasazení založené na instancích.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [modelu aplikace Service Fabric](service-fabric-application-model.md)
* [Začínáme s rozhraním příkazového řádku Service Fabric](service-fabric-cli.md)
