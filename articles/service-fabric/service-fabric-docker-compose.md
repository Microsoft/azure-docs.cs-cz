---
title: Azure Service Fabric Docker Compose ve verzi Preview nasazení
description: Azure Service Fabric přijímá Docker Compose formát, aby bylo snazší orchestrujte existující kontejnery pomocí Service Fabric. Tato podpora je aktuálně ve verzi preview.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: twhitney, subramar
ms.openlocfilehash: e4eb26ab91261d1888d3c756d611db1b31801e8f
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720221"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Podpora nasazení docker Compose v Azure Service Fabric (verze Preview)

Používá dockeru [docker-compose.yml](https://docs.docker.com/compose) souboru k definování vícekontejnerových aplikací. Abyste usnadnili snadnou pro zákazníky, kteří znají Docker, orchestraci stávajících aplikací typu kontejner v Azure Service Fabric, jsme rozšířili možnosti ve verzi preview pro Docker Compose nasazení nativně na platformě. Service Fabric může přijmout verze 3 a novější `docker-compose.yml` soubory. 

Protože tato podpora je ve verzi preview, je podporována pouze podmnožina direktiv Compose. Například upgrady aplikací nepodporují. Můžete však vždy odebrat a nasadit místo upgradování aplikace.

Pokud chcete používat tuto verzi preview, vytvořte cluster s verzí 5.7 nebo novější z modulu runtime Service Fabric na webu Azure portal a odpovídající sadu SDK. 

> [!NOTE]
> Tato funkce je ve verzi preview a není podporována v produkčním prostředí.
> Následující příklady jsou založeny na modul runtime verze 6.0 a sady SDK verze 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Nasadit soubor Docker-Compose na platformě Service Fabric

Následující příkazy vytvoří aplikace Service Fabric (s názvem `fabric:/TestContainerApp`), který můžete monitorovat a spravovat podobně jako jakékoli jiné aplikace Service Fabric. Můžete použít zadaný název aplikace pro dotazy na stav.
Service Fabric rozpozná "DeploymentName" jako identifikátor nasazení Compose.

### <a name="use-powershell"></a>Použití prostředí PowerShell

Vytvoření nasazení aplikace Service Fabric tvoří ze souboru docker-compose.yml spuštěním následujícího příkazu v Powershellu:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` a `RegistryPassword` odkazovat na registru kontejneru uživatelské jméno a heslo. Po dokončení nasazení, můžete zkontrolovat její stav pomocí následujícího příkazu:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Pokud chcete odstranit nasazení Compose prostřednictvím prostředí PowerShell, použijte následující příkaz:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Chcete-li začít s upgradem nasazení Compose prostřednictvím prostředí PowerShell, použijte následující příkaz:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

K vrácení zpět nasazení Compose upgrade pomocí Powershellu, použijte následující příkaz:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Po přijetí upgradu může uvidíte průběh upgradu sledovat pomocí následujícího příkazu:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Použití Azure Service Fabric CLI (sfctl)

Alternativně můžete použít následující příkaz rozhraní příkazového řádku Service Fabric:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Po vytvoření nasazení, můžete zkontrolovat její stav pomocí následujícího příkazu:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Pokud chcete odstranit nasazení Compose, použijte následující příkaz:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Pokud chcete spustit upgrade nasazení Compose, použijte následující příkaz:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

K vrácení zpět nasazení Compose upgrade, použijte následující příkaz:

```azurecli
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Po přijetí upgradu může uvidíte průběh upgradu sledovat pomocí následujícího příkazu:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Podporované direktiv Compose

Tato verze preview podporuje jenom některé možnosti konfigurace formát psaní verze 3, včetně následujících primitivních elementů:

* Služby > nasazení > repliky
* Služby > nasazení > umístění > omezení
* Služby > nasazení > prostředky > omezení
    * procesor. sdílené složky
    * – paměť
    * -paměti-swap
* Služby > příkazy
* Služby > prostředí
* Služby > porty
* Služby > obrázku
* Služby > izolace (jenom pro Windows)
* Služby > protokolování > ovladače
* Služby > protokolování > ovladače > Možnosti
* Svazek & nasazení > svazku

Nastavení clusteru pro vynucení omezení prostředků, jak je popsáno v [zásady správného řízení prostředků Service Fabric](service-fabric-resource-governance.md). Všechny ostatní Docker Compose direktivy nejsou podporovány pro tuto verzi preview.

### <a name="ports-section"></a>Část porty

Zadejte protokol http nebo https v sekci portů, který bude používat naslouchací proces služby Service Fabric. Tím se zajistí, že protokol koncového bodu je správně publikována ve službě pojmenování povolit reverzní proxy server pro předávání požadavků:
* Směrovat do nezabezpečených služby Service Fabric tvoří, zadejte **/http**. Například- **"80:80 / http"**.
* Pokud chcete směrovat zabezpečených služeb Service Fabric tvoří, zadejte **/https**. Například- **"443:443 / https"**.

> [!NOTE]
> Syntaxe oddílu /http a porty /https je specifický pro Service Fabric k registraci správnou adresu URL naslouchací proces Service Fabric.  Pokud Docker compose syntaxi souboru se ověří prostřednictvím kódu programu, může to způsobit chybu ověřování.

## <a name="servicednsname-computation"></a>ServiceDnsName výpočtu

Pokud je název služby, který zadáte v souboru psaní použitím plně kvalifikovaného názvu domény (to znamená, že obsahuje tečku [.]), název DNS zaregistrovaný Service Fabric je `<ServiceName>` (včetně tečky). Pokud ne, každý segment cesty v názvu aplikace bude popisek domény v názvu DNS služby s první segment cesty stávají popisek domény nejvyšší úrovně.

Například, pokud je zadaný název aplikace `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` by registrovaného názvu DNS.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Vytvořit nasazení (instance definice) a model aplikace Service Fabric (definice typu)

Soubor docker-compose.yml popisuje nasaditelný sadu kontejnerů, včetně jejich vlastností a konfigurace.
Například soubor může obsahovat proměnné prostředí a porty. Můžete také zadat parametry nasazení, jako je například omezení umístění, limity prostředků a názvy DNS, v souboru docker-compose.yml.

[Aplikačním modelem Service Fabric](service-fabric-application-model.md) service používá typy a typy aplikací, kde může mít mnoho instancí aplikace stejného typu. Například můžete mít jednu instanci aplikace na zákazníka. Tento model založený na typu podporuje více verzí stejného typu aplikace, který je zaregistrován s modulem runtime.

Například zákazník A může mít aplikaci vytvořena instance s typem 1.0 AppTypeA a zákazník B může mít jinou aplikaci vytvořena instance s stejného typu a verzi. Definování typů aplikací v manifesty aplikací a zadejte parametry název a nasazení aplikace při vytváření aplikace.

I když tento model nabízí flexibilitu, plánujeme také podporují model nasazení jednodušší, založený na instancích, ve kterém jsou implicitní typy ze souboru manifestu. V tomto modelu každá aplikace získá svůj vlastní nezávislý manifestu. V předběžné verzi nabízíme snaha přidáním podpory pro docker-compose.yml, který je ve formátu založený na instancích nasazení.

## <a name="next-steps"></a>Další postup

* Přečtěte si [aplikačním modelem Service Fabric](service-fabric-application-model.md)
* [Začínáme s rozhraním příkazového řádku Service Fabric](service-fabric-cli.md)
