---
title: Azure Service Fabric Mesh Maven odkaz
description: Obsahuje odkaz na použití modulu plug-in Maven pro service fabric mesh
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.openlocfilehash: bcc3fb7c6c3adce0997d0960c4d98227089b048b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75459019"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven Plugin pro service fabric mesh

## <a name="prerequisites"></a>Požadavky

- Java SDK
- Maven
- Azure CLI s rozšířením sítě
- Service Fabric CLI

## <a name="goals"></a>Cíle

### `azure-sfmesh:init`
- Vytvoří `servicefabric` složku, která `appresources` obsahuje složku, která má `application.yaml` soubor. 

### `azure-sfmesh:addservice`
- Vytvoří složku `servicefabric` uvnitř složky s názvem služby a vytvoří soubor YAML služby. 

### `azure-sfmesh:addnetwork`
- Generuje YAML `network` s poskytnutým názvem sítě `appresources` ve složce 

### `azure-sfmesh:addgateway`
- Generuje YAML `gateway` s názvem zadaný mchy ve složce. `appresources` 

#### `azure-sfmesh:addvolume`
- Generuje YAML `volume` s zadaným názvem `appresources` svazku ve složce.

### `azure-sfmesh:addsecret`
- Generuje YAML `secret` s poskytnutým tajným `appresources` názvem ve složce 

### `azure-sfmesh:addsecretvalue`
- Generuje YAML `secretvalue` s poskytnutým tajným a tajným názvem hodnoty ve složce. `appresources` 

### `azure-sfmesh:deploy`
- Sloučí yamls `servicefabric` ze složky a vytvoří šablonu Azure Resource Manager JSON v aktuální složce.
- Nasazuje všechny prostředky do prostředí Azure Service Fabric Mesh. 

### `azure-sfmesh:deploytocluster`
- Vytvoří složku`meshDeploy`( ), která obsahuje nasazení JSONs generované z yamls, které jsou použitelné pro clustery Service Fabric
- Nasazuje všechny prostředky do clusteru Service Fabric.
 

## <a name="usage"></a>Využití

Chcete-li v aplikaci Maven Java používat plugin Maven, přidejte do souboru pom.xml následující úryvek:

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

Modul plug-in Maven v současné době nepodporuje běžné konfigurace modulů plug-in Maven pro Azure.

## <a name="how-to"></a>Postupy

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Inicializovat projekt Maven pro síť Azure Service Fabric Mesh
Spusťte následující příkaz k vytvoření souboru YAML prostředku aplikace.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Vytvoří složku `servicefabric->appresources` volanou v kořenové složce, která obsahuje aplikaci YAML s názvem`app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Přidání prostředku do aplikace

#### <a name="add-a-new-network-to-your-application"></a>Přidání nové sítě do aplikace
Spusťte níže uvedený příkaz a vytvořte síťový prostředek yaml. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Vytvoří síť YAML `servicefabric->appresources` ve složce s názvem`network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Přidání nové služby do aplikace
Spusťte níže uvedený příkaz a vytvořte servisní yaml. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Vytvoří službu YAML `servicefabric->helloworldservice` `service_helloworldservice` ve složce s názvem, která odkazuje `helloworldservicenetwork` & aplikaci `helloworldserver`
- Služba by poslouchat na portu 8080
- Služba by se pomocí ***helloworldserver: nejnovější,*** jak je to image kontejneru.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Přidání nového prostředku brány do aplikace
Spusťte následující příkaz a vytvořte prostředek brány yaml. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Vytvoří novou bránu YAML ve složce `servicefabric->appresources` s názvem`gateway_helloworldgateway`
- Odkazy `helloworldservicelistener` jako naslouchací proces služby, který naslouchá volání z této brány. Také odkazuje `helloworldservice` jako služba, `helloworldservicenetwork` jako síť `helloworldserver` a jako aplikace. 
- Naslouchá požadavkům na portu 80

#### <a name="add-a-new-volume-to-your-application"></a>Přidání nového svazku do aplikace
Spusťte následující příkaz a vytvořte zdroj svazku yaml. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Vytvoří svazek YAML `servicefabric->appresources` ve složce s názvem`volume_vol1`
- Nastaví vlastnosti pro `volumeAccountKey`požadované `volumeShareName` parametry , a jak je uvedeno výše
- Další informace o tom, jak odkazovat na tento vytvořený svazek, najdete v následujícím, [nasazení aplikace pomocí svazku souborů Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Přidání nového tajného prostředku do aplikace
Spusťte níže uvedený příkaz a vytvořte tajný zdroj yaml. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Vytvoří tajný yaml `servicefabric->appresources` ve složce s názvem`secret_secret1`
- Další informace o tom, jak odkazovat na tento vytvořený tajný klíč, naleznete v následujícím, [Spravovat tajné kódy](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Přidání nového prostředku skreční hodnoty do aplikace
Spusťte níže uvedený příkaz a vytvořte zdroj yaml s utajovnou hodnotou. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Vytvoření tajné hodnoty YAML `servicefabric->appresources` ve složce s názvem`secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Místní spuštění aplikace

Pomocí cíle `azure-sfmesh:deploytocluster`můžete spustit aplikaci místně pomocí následujícího příkazu:

```cmd
mvn azure-sfmesh:deploytocluster
```

Ve výchozím nastavení tento cíl nasazuje prostředky do místního clusteru. Pokud nasazujete do místního clusteru, předpokládá se, že máte místní cluster Service Fabric v provozu. Cluster Local Service Fabric pro prostředky je aktuálně podporován pouze v [systému Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Vytvoří jsony z yamls, které jsou použitelné pro clustery Service Fabric
- Nasazení pak do koncového bodu clusteru

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Nasazení aplikace do sítě Azure Service Fabric Mesh

Pomocí cíle `azure-sfmesh:deploy`můžete nasadit do prostředí Service Fabric Mesh pomocí následujícího příkazu:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Vytvoří skupinu `todoapprg` prostředků s názvem, pokud neexistuje.
- Vytvoří šablonu Azure Resource Manager JSON sloučením YAML. 
- Nasazuje JSON do prostředí Azure Service Fabric Mesh.
