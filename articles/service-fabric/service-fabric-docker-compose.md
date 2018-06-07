---
title: Azure Service Fabric Docker Compose nasazení Preview
description: Azure Service Fabric přijme Docker Compose formátu, aby bylo snazší orchestraci existující kontejnery pomocí Service Fabric. Tato podpora je aktuálně ve verzi preview.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 43845a55589be9550e64b4a491b7d3675fb22e8c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641777"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Podpora nasazení docker Compose v Azure Service Fabric (Preview)

Používá docker [docker-compose.yml](https://docs.docker.com/compose) souboru k definování více kontejneru aplikace. Chcete-li snadno pro zákazníky obeznámeni s Docker orchestraci existující aplikace typu kontejner na Azure Service Fabric, jsme zahrnuli preview podporu pro Docker Compose nasazení nativně samotné platformy. Service Fabric může přijmout verze 3 nebo novější z `docker-compose.yml` soubory. 

Protože tato podpora je ve verzi preview, se podporuje jenom podmnožinu vytvářené direktivy. Například upgrady aplikací nejsou podporované. Můžete však vždy odebrat a nasadit aplikace místo jejich aktualizace.

Pokud chcete použít tuto verzi preview, vytvořte cluster pomocí verzi 5.7 nebo větší modulu runtime Service Fabric prostřednictvím portálu Azure společně s odpovídající SDK. 

> [!NOTE]
> Tato funkce je ve verzi preview a není podporována v produkčním prostředí.
> Následující příklady jsou založené na modulu runtime verze 6.0 a verze 2.8 sady SDK.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Nasadit soubor Docker Compose v Service Fabric

Následující příkaz třeba vytvoří aplikace Service Fabric (s názvem `fabric:/TestContainerApp`), které můžete sledovat a spravovat podobně jako všechny ostatní aplikace Service Fabric. Můžete použít název zadané aplikace pro dotazy na stav.
Service Fabric rozpozná "DeploymentName" jako identifikátor nové nasazení.

### <a name="use-powershell"></a>Použití prostředí PowerShell

Vytvoření nasazení Service Fabric tvoří ze soubor docker-compose.yml spuštěním následujícího příkazu v prostředí PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` a `RegistryPassword` naleznete v kontejneru registru uživatelské jméno a heslo. Po dokončení nasazení, jeho stav můžete zkontrolovat pomocí následujícího příkazu:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Pokud chcete odstranit nasazení vytvářené pomocí prostředí PowerShell, použijte následující příkaz:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Chcete-li začít s upgradem vytvářené nasazení pomocí prostředí PowerShell, použijte následující příkaz:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Po upgradu byla přijata, může být průběh upgradu sledován pomocí následujícího příkazu:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Používání Azure Service Fabric rozhraní příkazového řádku (sfctl)

Alternativně můžete tyto příkazy Service Fabric rozhraní příkazového řádku:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Po vytvoření nasazení, jeho stav můžete zkontrolovat pomocí následujícího příkazu:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Pokud chcete odstranit nasazení vytvářené, použijte následující příkaz:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Chcete-li začít s upgradem vytvářené nasazení, použijte následující příkaz:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Po upgradu byla přijata, může být průběh upgradu sledován pomocí následujícího příkazu:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Podporované vytvářené direktivy

Tato verze preview podporuje podmnožinu možnosti konfigurace z formátu verze 3 vytvářené, včetně následujících primitiv:

* Služby > nasazení > repliky
* Služby > nasazení > umístění > omezení
* Služby > nasazení > prostředky > omezení
    * -procesoru-sdílené složky
    * -paměti
    * -paměti-swap
* Služby > příkazy
* Služby > prostředí
* Služby > porty
* Služby > bitové kopie
* Služby > izolace (jenom pro Windows)
* Služby > protokolování > ovladačů
* Služby > protokolování > ovladače > Možnosti
* Svazek & nasazení > svazku

Nastavení clusteru pro vynucení omezení prostředků, jak je popsáno v [Service Fabric prostředků zásad správného řízení](service-fabric-resource-governance.md). Všechny ostatní Docker Compose direktivy nejsou podporovány pro tuto verzi preview.

## <a name="servicednsname-computation"></a>Výpočet ServiceDnsName

Pokud název služby, který určíte v souboru Compose je platný plně kvalifikovaný název domény (to znamená, obsahuje tečku [.]), je název DNS registrovaných Service Fabric `<ServiceName>` (včetně tečky). Pokud ne, každý segment cesty v názvu aplikace se změní na název domény v názvu služby DNS s první segment cesty stal popisek domény nejvyšší úrovně.

Například, pokud je zadaný název aplikace `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` by být registrovaný název DNS.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Vytvořit nasazení (instance definice) a modelu aplikace Service Fabric (definice typu)

Soubor docker-compose.yml popisuje sadu nasadit kontejnery, včetně jejich vlastnosti a konfigurace.
Například soubor může obsahovat proměnné prostředí a porty. Parametry nasazení, jako je například umístění omezení, omezení prostředků a názvy DNS, můžete zadat také v soubor docker-compose.yml.

[Model aplikace Service Fabric](service-fabric-application-model.md) používá služby typy a typy aplikací, kde může mít velký počet instancí aplikace stejného typu. Například můžete mít jedna instance aplikace na zákazníka. Tento model na základě typu podporuje více verzí stejného typu aplikace, které je registrované s modulem runtime.

Například zákazník A může mít aplikaci vytvořit instanci typu 1.0 AppTypeA a zákazník B může mít jiné aplikace vytvořena instance stejného typu a verzi. Definování typů aplikací v manifestů aplikace a můžete zadat parametry název a nasazení aplikace při vytváření aplikace.

I když tento model nabízí flexibilitu, jsme také plánování pro podporu nasazení jednodušší, založený na instancích modelu, kde typy jsou implicitní ze souboru manifestu. V tomto modelu každá aplikace získá svůj vlastní nezávislé manifestu. Přidáním podpory pro docker-compose.yml, který je ve formátu nasazení na základě instance jsme náhledu tohoto úsilí.

## <a name="next-steps"></a>Další postup

* Přečíst na [model aplikace Service Fabric](service-fabric-application-model.md)
* [Začínáme s rozhraním příkazového řádku Service Fabric](service-fabric-cli.md)
