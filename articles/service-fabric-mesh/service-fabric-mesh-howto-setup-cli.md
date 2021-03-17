---
title: Nastavení rozhraní příkazového řádku pro síť Azure Service Fabric
description: K nasazení a správě prostředků v místním počítači a v síti Azure Service Fabric je nutná Service Fabric rozhraní příkazového řádku (CLI). Tady je postup, jak ho nastavit.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: ea4a7764cf1ede1cfaf53b1097034c5894660376
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660674"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku služby Service Fabric Mesh
K nasazení a správě prostředků v místním počítači a v síti Azure Service Fabric je nutná Service Fabric rozhraní příkazového řádku (CLI). Tady je postup, jak ho nastavit.

Existují tři typy rozhraní příkazového řádku, které lze použít a které jsou shrnuty v následující tabulce.

| Modul CLI | Cílové prostředí |  Popis | 
|---|---|---|
| AZ Mesh | Síť Azure Service Fabric | Primární rozhraní příkazového řádku, které umožňuje nasazovat aplikace a spravovat prostředky v prostředí sítě Azure Service Fabric. 
| sfctl | Místní clustery | Service Fabric CLI, který umožňuje nasazení a testování prostředků Service Fabric na místních clusterech.  
| Maven CLI | Místní clustery & síť Azure Service Fabric | Obálka kolem `az mesh` a `sfctl` , která vývojářům v jazyce Java umožňuje používat známé prostředí příkazového řádku pro místní a vývojové prostředí Azure.  

Ve verzi Preview je rozhraní příkazového řádku služby Azure Service Fabric Mesh jako rozšíření Azure CLI. Můžete ho nainstalovat do Azure Cloud Shellu nebo do místní instalace Azure CLI. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.67 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instalace rozhraní příkazového řádku pro Azure Service Fabric

Pokud jste to ještě neudělali, nainstalujte modul rozšíření CLI Azure Service Fabric pro rozhraní příkazového řádku pomocí následujícího příkazu: 
 
```azurecli-interactive
az extension add --name mesh
```

Pokud už je nainstalovaná, aktualizujte stávající modul rozhraní příkazového řádku Azure Service Fabric pomocí následujícího příkazu:

```azurecli-interactive
az extension update --name mesh
```

## <a name="install-the-service-fabric-cli-sfctl"></a>Instalace rozhraní příkazového řádku Service Fabric (sfctl) 

Postupujte podle pokynů v tématu [nastavení Service Fabric CLI](../service-fabric/service-fabric-cli.md). Modul **sfctl** se dá použít k nasazení aplikací na základě modelu prostředků v Service Fabric clusterech na místním počítači. 

## <a name="install-the-maven-cli"></a>Instalace rozhraní příkazového řádku Maven 

Aby bylo možné používat rozhraní příkazového řádku Maven, musí být na počítači nainstalovány následující: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure mřížka CLI (AZ mesh) – pro cílení na síť Azure Service Fabric 
* SFCTL (SFCTL) – pro cílení místních clusterů 

Rozhraní příkazového řádku Maven pro Service Fabric je stále ve verzi Preview. 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Další informace o podrobném využití najdete v části referenční dokumentace k rozhraní příkazového [řádku Maven](service-fabric-mesh-reference-maven.md) .

## <a name="next-steps"></a>Další kroky

Můžete také nastavit [vývojové prostředí ve Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Najděte odpovědi na [běžné dotazy a problémy](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
