---
title: Odkaz na síť Azure Service Fabric Maven
description: Obsahuje referenční informace o tom, jak používat modul plug-in Maven pro Service Fabric síť.
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.custom: devx-track-java
ms.openlocfilehash: dd42752dc7ef2c7686073b7a2d780fe9356425c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625579"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Modul plug-in Maven pro Service Fabric sítě

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

## <a name="prerequisites"></a>Předpoklady

- Java SDK
- Maven
- Rozhraní příkazového řádku Azure s rozšířením sítě
- Service Fabric CLI

## <a name="goals"></a>Cíle

### `azure-sfmesh:init`
- Vytvoří `servicefabric` složku, která obsahuje `appresources` složku, která má `application.yaml` soubor. 

### `azure-sfmesh:addservice`
- Vytvoří složku ve `servicefabric` složce s názvem služby a vytvoří soubor YAML služby. 

### `azure-sfmesh:addnetwork`
- Vygeneruje `network` YAML s uvedeným názvem sítě ve `appresources` složce. 

### `azure-sfmesh:addgateway`
- Vygeneruje `gateway` YAML s uvedeným názvem brány ve `appresources` složce. 

#### `azure-sfmesh:addvolume`
- Vygeneruje `volume` YAML s uvedeným názvem svazku ve `appresources` složce.

### `azure-sfmesh:addsecret`
- Vygeneruje `secret` YAML se zadaným tajným názvem ve `appresources` složce. 

### `azure-sfmesh:addsecretvalue`
- Vygeneruje `secretvalue` YAML se zadaným tajným klíčem a názvem tajné hodnoty ve `appresources` složce. 

### `azure-sfmesh:deploy`
- Sloučí yamls ze `servicefabric` složky a vytvoří Azure Resource Manager JSON šablony v aktuální složce.
- Nasadí všechny prostředky do prostředí sítě Azure Service Fabric. 

### `azure-sfmesh:deploytocluster`
- Vytvoří složku ( `meshDeploy` ), která obsahuje JSON pro nasazení vygenerované z yamls, které platí pro Service Fabric clustery.
- Nasadí všechny prostředky do clusteru Service Fabric.
 

## <a name="usage"></a>Využití

Pokud chcete použít modul plug-in Maven v aplikaci Maven Java, přidejte do souboru pom.xml následující fragment kódu:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Společná konfigurace

Modul plug-in Maven aktuálně nepodporuje společné konfigurace modulů plug-in Maven pro Azure.

## <a name="how-to"></a>Postupy

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Inicializovat projekt Maven pro síť Azure Service Fabric
Spuštěním následujícího příkazu vytvořte soubor YAML aplikačních prostředků.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Vytvoří složku s názvem `servicefabric->appresources` v kořenové složce obsahující YAML aplikace s názvem `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Přidání prostředku do aplikace

#### <a name="add-a-new-network-to-your-application"></a>Přidání nové sítě do aplikace
Spuštěním následujícího příkazu vytvořte YAML síťového prostředku. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Vytvoří síťovou YAML ve složce `servicefabric->appresources` s názvem. `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Přidání nové služby do aplikace
Spuštěním následujícího příkazu vytvořte službu YAML. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Vytvoří službu YAML ve složce `servicefabric->helloworldservice` s názvem `service_helloworldservice` , která odkazuje `helloworldservicenetwork` & `helloworldserver` aplikace.
- Služba by naslouchala na portu 8080
- Služba by používala ***helloworldserver: nejnovější*** jako image kontejneru.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Přidání nového prostředku brány do aplikace
Spuštěním následujícího příkazu vytvořte prostředek brány YAML. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Vytvoří novou YAML brány ve složce `servicefabric->appresources` s názvem. `gateway_helloworldgateway`
- Odkazuje `helloworldservicelistener` jako naslouchací proces služby, který naslouchá volání z této brány. Také odkazuje `helloworldservice` jako na službu jako na `helloworldservicenetwork` síť a `helloworldserver` jako na aplikaci. 
- Naslouchá požadavkům na portu 80.

#### <a name="add-a-new-volume-to-your-application"></a>Přidání nového svazku do aplikace
Spuštěním následujícího příkazu vytvořte prostředek svazku YAML. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Vytvoří svazek YAML ve složce `servicefabric->appresources` s názvem. `volume_vol1`
- Nastaví vlastnosti požadovaných parametrů, `volumeAccountKey` a výše. `volumeShareName`
- Další informace o tom, jak odkazovat na tento vytvořený svazek, najdete v následujícím článku [nasazení aplikace pomocí svazku souborů Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md) .

#### <a name="add-a-new-secret-resource-to-your-application"></a>Přidání nového tajného prostředku do aplikace
Spuštěním následujícího příkazu vytvořte tajný prostředek YAML. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Vytvoří tajný YAML ve složce `servicefabric->appresources` s názvem. `secret_secret1`
- Další informace o tom, jak odkazovat na tento vytvořený tajný klíč, najdete v následujících tématu [Správa tajných klíčů](service-fabric-mesh-howto-manage-secrets.md) .

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Přidání nového prostředku secretvalue do aplikace
Spuštěním následujícího příkazu vytvořte secretvalue prostředku YAML. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Vytvořit YAML secretvalue ve složce `servicefabric->appresources` s názvem `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Aplikaci spustíte místně.

V nápovědě k cíli `azure-sfmesh:deploytocluster` můžete spustit aplikaci místně pomocí příkazu níže:

```cmd
mvn azure-sfmesh:deploytocluster
```

Ve výchozím nastavení tento cíl nasadí prostředky do místního clusteru. Pokud nasazujete do místního clusteru, předpokládá se, že máte spuštěný místní Cluster Service Fabric. Místní Service Fabric cluster pro prostředky je aktuálně podporován pouze v [systému Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Vytvoří z yamls JSON, které platí pro Service Fabric clustery.
- Nasadí do koncového bodu clusteru.

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Nasazení aplikace do sítě Azure Service Fabric

S `azure-sfmesh:deploy` využitím cíle můžete nasadit do prostředí Service Fabric sítě spuštěním příkazu níže:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Vytvoří skupinu prostředků s názvem, `todoapprg` Pokud neexistuje.
- Vytvoří Azure Resource Manager JSON šablony sloučením YAMLs. 
- Nasadí kód JSON do prostředí sítě Azure Service Fabric.
