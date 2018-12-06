---
title: Nastavení rozhraní příkazového řádku služby Azure Service Fabric Mesh | Microsoft Docs
description: Naučte se nastavit rozhraní příkazového řádku služby Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 11/28/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: daeec38863ee7e9dd4e56f2470e5f9459dcc8bc1
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958589"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku služby Service Fabric Mesh
Service Fabric mřížky rozhraní příkazového řádku (CLI) je potřeba nasadit a spravovat prostředky, místně i v Azure Service Fabric mřížky. 

Existují tři typy rozhraní příkazového řádku, který můžete použít a jsou shrnuty v následující tabulce. 

| Rozhraní příkazového řádku modulu | Cílové prostředí |  Popis | 
|---|---|---|
| síť az | Síť Azure Service Fabric | Primární CLI, který umožňuje nasazení aplikací a správě prostředků v prostředí Azure Service Fabric mřížky. 
| sfctl | Místní clusterů | Service Fabric CLI, který umožňuje nasazení a testování prostředky Service Fabric na místní clustery.  
| Maven rozhraní příkazového řádku | Místní clustery & sítě Azure Service Fabric | Obálka kolem 'az sítě' a "sfctl", která umožňuje Java vývojářům používat známé příkazový řádek prostředí pro vývoj pro Azure a místním prostředí.  

Ve verzi Preview je rozhraní příkazového řádku služby Azure Service Fabric Mesh jako rozšíření Azure CLI. Můžete ho nainstalovat do Azure Cloud Shellu nebo do místní instalace Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instalace Azure Service Fabric síť rozhraní příkazového řádku
1. Je nutné nainstalovat Azure CLI verze 2.0.43 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete rozhraní příkazového řádku nainstalovat nebo upgradovat na nejnovější verzi, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

2. Instalace modulu Azure Service Fabric mřížky CLI rozšíření pomocí následujícího příkazu. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Aktualizujte existující modul Azure mřížky příkazového řádku Service Fabric pomocí následujícího příkazu.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Nainstalujte Service Fabric CLI (sfctl) 

Postupujte podle pokynů na [nastavení Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli). **Sfctl** modul lze použít pro nasazení aplikací založených na modelu prostředků, který s clustery Service Fabric na místním počítači. 

## <a name="install-the-maven-cli"></a>Instalace nástroje Maven rozhraní příkazového řádku 

Chcete-li používat rozhraní příkazového řádku Maven následující musí být nainstalovaný na vašem počítači: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](http://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Mřížky rozhraní příkazového řádku Azure (az síť) - do cílové sítě Azure Service Fabric 
* SFCTL (sfctl) - cílit na místní clusterů 

Rozhraní příkazového řádku nástroje Maven pro Service Fabric je stále ve verzi preview. 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Čtení [odkaz na rozhraní příkazového řádku Maven](service-fabric-mesh-reference-maven.md) části a seznamte se s podrobnými informacemi o využití.

## <a name="next-steps"></a>Další postup

Můžete také nastavit [vývojové prostředí ve Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Najděte odpovědi na [běžné dotazy a problémy](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
