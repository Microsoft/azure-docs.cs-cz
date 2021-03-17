---
title: Azure Service Fabric Docker Compose Deployment Preview
description: Azure Service Fabric přijímá formát Docker Compose, který usnadňuje orchestraci stávajících kontejnerů pomocí Service Fabric. Tato podpora je aktuálně ve verzi Preview.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84691284"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Podpora nasazení Docker Compose ve službě Azure Service Fabric (Preview)

Docker používá soubor [Docker-Compose. yml](https://docs.docker.com/compose) pro definování aplikací využívajících více kontejnerů. Abychom zákazníkům, kteří znají Docker, usnadnili orchestraci stávajících aplikací kontejneru v Azure Service Fabric, zahrnuli jsme podporu Preview pro nasazení Docker Compose nativně v této platformě. Service Fabric může přijmout verzi 3 a pozdější `docker-compose.yml` soubory. 

Vzhledem k tomu, že je tato podpora ve verzi Preview, je podporovaná jenom podmnožina direktiv pro vytváření.

Chcete-li použít tuto verzi Preview, vytvořte cluster s verzí 5,7 nebo vyšší Service Fabric modulu runtime prostřednictvím Azure Portal spolu s odpovídající sadou SDK. 

> [!NOTE]
> Tato funkce je ve verzi Preview a není podporovaná v produkčním prostředí.
> Níže uvedené příklady vycházejí z modulu runtime verze 6,0 a sady SDK verze 2,8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Nasazení souboru Docker Compose v Service Fabric

Následující příkazy vytvoří aplikaci Service Fabric (s názvem `fabric:/TestContainerApp` ), kterou můžete monitorovat a spravovat stejně jako jakoukoli jinou aplikaci Service Fabric. Pro dotazy na stav můžete použít zadaný název aplikace.
Service Fabric rozpoznává "Deployment" jako identifikátor nasazení vytváření.

### <a name="use-powershell"></a>Použití prostředí PowerShell

Spuštěním následujícího příkazu v PowerShellu vytvořte Service Fabric vytvoření nasazení ze souboru Docker-Compose. yml:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` a `RegistryPassword` Podívejte se na uživatelské jméno a heslo registru kontejneru. Po dokončení nasazení můžete zjistit jeho stav pomocí následujícího příkazu:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

K odstranění nasazení vytváření pomocí prostředí PowerShell použijte následující příkaz:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Pokud chcete zahájit upgrade nasazení prostřednictvím PowerShellu, použijte následující příkaz:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Pokud chcete vrátit zpět upgrade nasazení pomocí prostředí PowerShell, použijte následující příkaz:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Po přijetí upgradu může být průběh upgradu sledován pomocí následujícího příkazu:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Použití rozhraní příkazového řádku Azure Service Fabric (sfctl)

Případně můžete použít následující příkaz Service Fabric CLI:

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Po vytvoření nasazení můžete zjistit jeho stav pomocí následujícího příkazu:

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Chcete-li odstranit nasazení vytváření, použijte následující příkaz:

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Chcete-li zahájit upgrade nasazení, použijte následující příkaz:

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Pokud chcete vrátit zpět upgrade nasazení psaní, použijte následující příkaz:

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Po přijetí upgradu může být průběh upgradu sledován pomocí následujícího příkazu:

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Podporované direktivy pro vytváření

Tato verze Preview podporuje podmnožinu možností konfigurace z formátu sestavení verze 3, včetně následujících primitivních hodnot:

* Služby > nasazení > replik
* Služby > nasazovat > omezení umístění >
* Služby > nasazení > prostředků > omezení
    * -CPU – sdílené složky
    * – paměť
    * – swap paměti
* Služby > příkazy
* Služby > prostředí
* Služby > porty
* > – obrázek služby
* Služby > izolaci (jenom pro Windows)
* Služby > Logging > Driver
* Služby > protokolování > ovladačů > možnosti
* Svazek & nasazení > svazku

Nastavte cluster pro vynucování omezení prostředků, jak je popsáno v tématu [Service Fabric zásad správného řízení prostředků](service-fabric-resource-governance.md). Všechny ostatní direktivy Docker Compose nejsou pro tuto verzi Preview podporované.

### <a name="ports-section"></a>Oddíl porty

V části porty zadejte protokol HTTP nebo https, který bude použit pro naslouchací proces služby Service Fabric. Tím se zajistí, že je protokol koncového bodu ve službě pojmenování správně publikovaný, aby bylo možné přesměrovat žádosti reverzního proxy serveru:
* Pokud chcete směrovat na nezabezpečené služby Service Fabric psaní, zadejte **/http**. Například- **"80:80/http"**.
* Pokud chcete směrovat na zabezpečené Service Fabric služby vytváření, zadejte **/https**. Například- **"443:443/HTTPS"**.

> [!NOTE]
> Syntaxe oddílu/http a/HTTPS ports je specifická pro Service Fabric k registraci správné Service Fabric adresy URL naslouchacího procesu.  Pokud je syntaxe dopravení souboru Docker ověřována prostřednictvím kódu programu, může dojít k chybě ověření.

## <a name="servicednsname-computation"></a>Výpočet ServiceDnsName

Pokud název služby, který zadáte ve vytvořeném souboru, je plně kvalifikovaný název domény (tj. obsahuje tečku [.]), název DNS zaregistrovaný Service Fabric je `<ServiceName>` (včetně tečky). V takovém případě se každý segment cesty v názvu aplikace stane názvem domény v názvu DNS služby, přičemž první segment cesty se stane popiskem domény nejvyšší úrovně.

Například pokud je zadaný název aplikace `fabric:/SampleApp/MyComposeApp` , bude `<ServiceName>.MyComposeApp.SampleApp` registrovaný název DNS.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Sestavování nasazení (definice instance) versus Service Fabric aplikačního modelu (definice typu)

Soubor Docker-Compose. yml popisuje nasazovatelné sady kontejnerů, včetně jejich vlastností a konfigurací.
Soubor může například obsahovat proměnné prostředí a porty. V souboru Docker-Compose. yml můžete také zadat parametry nasazení, jako například omezení umístění, omezení prostředků a názvy DNS.

[Model aplikace Service Fabric](service-fabric-application-model.md) používá typy služeb a typy aplikací, kde můžete mít mnoho instancí aplikace stejného typu. Například můžete mít jednu instanci aplikace na zákazníka. Tento model založený na typu podporuje více verzí stejného typu aplikace, který je zaregistrován s modulem runtime.

Například zákazník A může mít vytvořenou aplikaci s typem 1,0 AppTypeA a zákazníkem B může mít vytvořenou instanci jiné aplikace se stejným typem a verzí. V manifestech aplikace definujete typy aplikací a při vytváření aplikace zadáte název aplikace a parametry nasazení.

I když tento model nabízí flexibilitu, plánujeme také podporu jednoduššího modelu nasazení založeného na instancích, kde jsou typy implicitní ze souboru manifestu. V tomto modelu každá aplikace získá svůj vlastní nezávislý manifest. Toto úsilí si vyhledáme přidáním podpory pro Docker-Compose. yml, což je formát nasazení na základě instance.

## <a name="next-steps"></a>Další kroky

* Přečtěte si článek [Service Fabric aplikační model](service-fabric-application-model.md)
* [Začínáme s rozhraním příkazového řádku Service Fabric](service-fabric-cli.md)
