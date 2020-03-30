---
title: Nastavení příkazového příkazu k síti Azure Service Fabric Mesh
description: Service Fabric Mesh Rozhraní příkazového řádku (CLI) je nutné nasadit a spravovat prostředky místně a v Azure Service Fabric Mesh. Zde je návod, jak nastavit.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 0fb65ceeabca9331130083f8ec5b3fe8acce13b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259185"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku služby Service Fabric Mesh
Service Fabric Mesh Rozhraní příkazového řádku (CLI) je nutné nasadit a spravovat prostředky místně a v Azure Service Fabric Mesh. Zde je návod, jak nastavit.

Existují tři typy příkazových příkazových odlohovených pokynů, které lze použít a jsou shrnuty v následující tabulce.

| Modul CLI | Cílové prostředí |  Popis | 
|---|---|---|
| az síť | Síť prostředků infrastruktury služby Azure | Primární příkazové příkazové příkazové příkazové příkazy, které vám umožní nasadit aplikace a spravovat prostředky v prostředí Azure Service Fabric Mesh. 
| sfctl | Místní clustery | Správce cli service fabric, který umožňuje nasazení a testování prostředků Service Fabric proti místním clusterům.  
| Maven CLI | Místní clustery & síť Azure Service Fabric Mesh | Obálka kolem `az mesh` `sfctl` a která umožňuje vývojářům Java používat známé prostředí příkazového řádku pro místní a Azure vývojové prostředí.  

Ve verzi Preview je rozhraní příkazového řádku služby Azure Service Fabric Mesh jako rozšíření Azure CLI. Můžete ho nainstalovat do Azure Cloud Shellu nebo do místní instalace Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instalace příkazového příkazového příkazu k síti Azure Service Fabric Mesh
1. Je nutné nainstalovat Azure CLI verze 2.0.67 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete rozhraní příkazového řádku nainstalovat nebo upgradovat na nejnovější verzi, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

2. Nainstalujte rozšiřující modul rozšíření Azure Service Fabric Mesh CLI pomocí následujícího příkazu. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Aktualizujte existující modul Příkazpříkaz ové rozhraní Azure Service Fabric Mesh CLI pomocí následujícího příkazu.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Instalace cli service fabric (sfctl) 

Postupujte podle pokynů [na nastavení stavu příkazu KOnT služby](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli). Modul **sfctl** lze použít pro nasazení aplikací založených na modelu prostředků proti clusterům Service Fabric v místním počítači. 

## <a name="install-the-maven-cli"></a>Instalace cli Maven 

Chcete-li používat příkazcli Maven, je třeba do počítače nainstalovat následující: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh CLI (az mesh) – cílit na síť Azure Service Fabric Mesh 
* SFCTL (sfctl) - Chcete-li cílit na místní clustery 

Maven CLI pro service fabric je stále ve verzi preview. 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Přečtěte si referenční část [Maven CLI,](service-fabric-mesh-reference-maven.md) kde se dozvíte o podrobném použití.

## <a name="next-steps"></a>Další kroky

Můžete také nastavit [vývojové prostředí ve Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Najděte odpovědi na [běžné dotazy a problémy](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
