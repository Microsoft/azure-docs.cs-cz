---
title: Referenční informace k Azure Service Fabric mřížky Mavenu | Dokumentace Microsoftu
description: Obsahuje referenční informace pro používání modulu plug-in Maven pro Service Fabric mřížky
services: service-fabric-mesh
keywords: maven, java, rozhraní příkazového řádku
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.service: service-fabric-mesh
manager: subramar
ms.openlocfilehash: 08e842f5b91bd0ca5f8e8b2a7866f3f9a689ac28
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998992"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Modul plug-in maven pro Service Fabric sítě

## <a name="prerequisites"></a>Požadavky

- Java SDK
- Maven
- Rozhraní příkazového řádku Azure s rozšířením sítě
- Service Fabric CLI

## <a name="goals"></a>Cíle

### `azure-sfmesh:init`
- Vytvoří `servicefabric` složku, která obsahuje `appresources` složky, která má `application.yaml` souboru. 

### `azure-sfmesh:addservice`
- Vytvoří složku uvnitř `servicefabric` složku s názvem služby a vytvoří soubor YAML služby. 

### `azure-sfmesh:addnetwork`
- Generuje `network` YAML s názvem zadaného sítě v `appresources` složky 

### `azure-sfmesh:addgateway`
- Generuje `gateway` YAML s názvem zadaného brány v `appresources` složky 

### `azure-sfmesh:addsecret`
- Generuje `secret` YAML se zadaným názvem tajných kódů v `appresources` složky 

### `azure-sfmesh:addsecretvalue`
- Generuje `secretvalue` YAML s názvem zadaná hodnota tajného kódu a tajných kódů v `appresources` složky 

### `azure-sfmesh:deploy`
- Sloučí yamls z `servicefabric` složku a vytvoří šablonu Azure Resource Manageru JSON v aktuální složce.
- Všechny prostředky nasadí do prostředí Azure Service Fabric mřížky 

### `azure-sfmesh:deploytocluster`
- Vytvoří složku (`meshDeploy`) obsahující nasazení JSONs vygenerovaná yamls, které se dají použít pro clustery Service Fabric
- Všechny prostředky nasadí do clusteru Service Fabric
 

## <a name="usage"></a>Využití

Použití modulu plug-in Maven ve vaší aplikaci Maven Java, přidejte do souboru pom.xml následující fragment kódu:

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

## <a name="common-configuration"></a>Běžnou konfigurací

Modul plug-in Maven aktuálně nepodporuje obvyklé konfigurace moduly plug-in Maven pro Azure.

## <a name="how-to"></a>Postupy

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Inicializovat projekt Maven pro Azure Service Fabric mřížky
Spuštěním následujícího příkazu vytvořte soubor YAML prostředků aplikace.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Vytvoří složku s názvem `servicefabric->appresources` v kořenové složce, která obsahuje aplikaci s názvem YAML `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Přidání prostředků do aplikace

#### <a name="add-a-new-network-to-your-application"></a>Přidat nové sítě pro vaši aplikaci
Spuštěním následujícího příkazu vytvořte yaml prostředků sítě. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.4/22
```

- Ve složce vytvoří síť YAML `servicefabric->appresources` s názvem `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Přidání nové služby do aplikace
Spuštěním následujícího příkazu vytvořte službu yaml. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Vytvoří službu YAML ve složce `servicefabric->helloworldservice` s názvem `service_helloworldservice` , která odkazuje na `helloworldservicenetwork` & `helloworldserver` aplikace
- Služba bude naslouchat na portu 8080
- Služba by pomocí ***helloworldserver:latest*** jako je image kontejneru.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Přidat nový prostředek brány pro vaši aplikaci
Spuštěním následujícího příkazu vytvořte yaml prostředek brány. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Vytvoří novou bránu YAML ve složce `servicefabric->appresources` s názvem `gateway_helloworldgateway`
- Odkazy na `helloworldservicelistener` jako naslouchací proces služby, která naslouchá na volání z této brány. Také odkazuje `helloworldservice` jako služba, `helloworldservicenetwork` jako síť a `helloworldserver` jako aplikace. 
- Čeká na požadavky na portu 80

#### <a name="add-a-new-volume-to-your-application"></a>Přidat nový svazek do vaší aplikace
Spuštěním následujícího příkazu vytvořte yaml prostředku svazku. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Vytvoří ve složce svazku YAML `servicefabric->appresources` s názvem `volume_vol1`
- Nastaví vlastnosti pro požadované parametry `volumeAccountKey`, a `volumeShareName` jak je uvedeno výše
- Další informace o tom, jak odkazovat na toto vytvořili svazek, naleznete na následujícím [nasadit aplikaci pomocí svazku soubory Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Přidat nový prostředek tajného kódu do vaší aplikace
Spuštěním následujícího příkazu vytvořte tajný prostředků yaml. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Ve složce vytvoří skrytou YAML `servicefabric->appresources` s názvem `secret_secret1`
- Další informace o tom, jak odkazovat na tuto vytvořenou tajného kódu, naleznete na následujícím [spravovat tajné kódy](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Přidat nový prostředek secretvalue do vaší aplikace
Spuštěním následujícího příkazu vytvořte yaml prostředků secretvalue. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Ve složce vytvořte secretvalue YAML `servicefabric->appresources` s názvem `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Místní spuštění aplikace

Díky pomoci cílem `azure-sfmesh:deploytocluster`, můžete spustit aplikaci místně pomocí následujícího příkazu:

```cmd
mvn azure-sfmesh:deploytocluster
```

Ve výchozím nastavení tento cíl nasadí prostředků k místnímu clusteru. Pokud provádíte nasazení do místního clusteru, předpokládá se, že máte místní cluster Service Fabric rychle zprovoznit. Aktuálně podporuje jenom na místní cluster Service Fabric pro prostředky [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Vytvoří JSONs z yamls, které se dají použít pro clustery Service Fabric
- Pak nasadí do koncového bodu clusteru

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Nasazení aplikace do Azure Service Fabric mřížky

Díky pomoci cílem `azure-sfmesh:deploy`, můžete nasadit do Service Fabric mřížky prostředí spuštěním následujícího příkazu:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Vytvoří skupinu prostředků s názvem `todoapprg` Pokud neexistuje.
- Sloučení YAMLs vytvoří šablonu Azure Resource Manageru JSON. 
- Nasadí kód JSON do prostředí Azure Service Fabric mřížky.