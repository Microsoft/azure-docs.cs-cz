---
title: Instalace aplikace do samostatného clusteru
description: V tomto kurzu se dozvíte, jak nainstalovat aplikaci do samostatného Service Fabricho clusteru.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: ae946321b34f12c816a717db4a3d07f57feefe52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96485356"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Kurz: Nasazení aplikace do samostatného clusteru Service Fabric

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „jakýkoli operační systém a cloud“. V této sérii kurzů vytvoříte samostatný cluster hostovaný na AWS a nasadíte do něj aplikaci.

Tento kurz je třetí částí série.  Service Fabric samostatné clustery nabízejí možnost zvolit si vlastní prostředí a vytvořit cluster jako součást libovolného operačního systému, jakéhokoli cloudu s přístupem k Service Fabric. V tomto kurzu se dozvíte, jak vytvořit infrastrukturu AWS potřebnou k hostování tohoto samostatného clusteru.

V tomto článku se naučíte:

> [!div class="checklist"]
> * Stažení ukázkové aplikace
> * Nasazení do clusteru

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* [Nainstalujte Visual Studio 2019](https://www.visualstudio.com/) a nainstalujte úlohy vývoje pro vývoj a vývoj pro **Azure** a vývoj **webů** .
* [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové hlasovací aplikace

V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Nasazení aplikace do clusteru Service Fabric

Jakmile je aplikace stažená, můžete ji do clusteru nasadit přímo ze sady Visual Studio.

1. Otevřete sadu Visual Studio.

2. Vybrat **soubor**  >  **otevřen**

3. Přejděte do složky, do které jste naklonovali úložiště git, a vyberte Voting.sln.

4. V Průzkumníku řešení klikněte pravým tlačítkem na projekt aplikace `Voting` a zvolte **Publikovat**.

5. Vyberte rozevírací seznam pro **Koncový bod připojení** a zadejte veřejný název DNS jednoho z uzlů v clusteru.  Například, `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. V Azure se plně kvalifikovaný název domény (FQDN) neposkytuje automaticky, ale dá se snadno [nastavit na stránce s přehledem virtuálního počítače.](../virtual-machines/create-fqdn.md)

6. Otevřete váš preferovaný prohlížeč a zadejte adresu clusteru (koncový bod připojení, tato aplikace se nasadí na port 8080 – například ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Odpověď rozhraní API z clusteru](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak nasadit aplikaci do clusteru:

> [!div class="checklist"]
> * Stažení ukázkové aplikace
> * Nasazení do clusteru

Přejděte ke čtvrté části série a vyčistěte cluster.

> [!div class="nextstepaction"]
> [Vyčištění prostředků](service-fabric-tutorial-standalone-clean-up.md)