---
title: 'Vytvoření první aplikace Azure Service Fabric v systému Linux pomocí jazyka C #'
description: Naučte se vytvářet a nasazovat Service Fabric aplikace pomocí C# a .NET Core 2,0.
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: 556d8dee4efd492fa98755f1ffd1cdc1c9887856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "82193645"
---
# <a name="create-your-first-azure-service-fabric-application"></a>Vytvoření první aplikace Azure Service Fabric
> [!div class="op_single_selector"]
> * [Java – Linux (Preview)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux (Preview)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric poskytuje sady SDK pro vytváření služeb v Linuxu pomocí .NET Core a Javy. V tomto kurzu si projdeme postup vytvoření aplikace pro Linux a vytvoření služby pomocí jazyka C# na .NET Core 2.0.

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že máte [v Linuxu nastavené vývojové prostředí](service-fabric-get-started-linux.md). Pokud používáte Mac OS X, můžete k [nastavení linuxového prostředí ve virtuálním počítači použít Vagrant](service-fabric-get-started-mac.md).

Budete také chtít nainstalovat rozhraní příkazového [řádku Service Fabric](service-fabric-cli.md)

### <a name="install-and-set-up-the-generators-for-c"></a>Instalace a nastavení generátorů pro jazyk C#
Service Fabric nabízí nástroje pro generování uživatelského rozhraní, které vám pomůžou vytvářet aplikace Service Fabric z terminálu pomocí generátorů šablon Yeoman. Podle těchto pokynů nastavte generátory šablon Service Fabric Yeoman pro jazyk C#:

1. Instalace nodejs a NPM na počítači

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. Nainstalujte na svém počítači generátor šablon [Yeoman](https://yeoman.io/) z npm

   ```bash
   npm install -g yo
   ```
3. Instalace generátoru aplikací v jazyce C# Service Fabric Yeoman z NPM

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>Vytvoření aplikace
Aplikace Service Fabric může obsahovat jednu nebo víc služeb, z nichž každá má určitou roli při poskytování funkcí aplikace. Generátor Service Fabric [Yeoman](https://yeoman.io/) pro jazyk C#, který jste nainstalovali v posledním kroku, vám usnadní vytvoření první služby a případná další rozšíření později. Pomocí generátoru Yeoman vytvoříme aplikaci s jedinou službou.

1. V terminálu zadejte následující příkaz, který zahájí sestavování základní kostry aplikace: `yo azuresfcsharp`
2. Pojmenujte svoji aplikaci.
3. Vyberte typ první služby a pojmenujte ji. Pro účely tohoto kurzu zvolíme službu Reliable Actor.

   ![Generátor Service Fabric Yeoman pro jazyk C#][sf-yeoman]

> [!NOTE]
> Další informace o možnostech najdete v tématu [Přehled programovacího modelu Service Fabric](service-fabric-choose-framework.md).
>
>

## <a name="build-the-application"></a>Sestavení aplikace
Šablony generátoru Service Fabric Yeoman zahrnují skript sestavení, který můžete použít k sestavení aplikace z terminálu (po přejití do složky aplikace).

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>Nasazení aplikace

Jakmile je aplikace sestavená, můžete ji nasadit do místního clusteru.

1. Připojte se k místnímu clusteru služby Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Spuštěním instalačního skriptu, který je součástí šablony, zkopírujte balíček aplikace do úložiště imagí clusteru, zaregistrujte typ aplikace a vytvořte její instanci.

    ```bash
    ./install.sh
    ```

Nasazení sestavené aplikace je stejné jako u všech ostatních aplikací Service Fabric. Podrobné pokyny najdete v dokumentaci s popisem [správy aplikace Service Fabric pomocí Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md).

Parametry těchto příkazů najdete v generovaných manifestech uvnitř balíčku aplikace.

Jakmile je aplikace nasazená, otevřete prohlížeč a přejděte k nástroji [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) na adrese `http://localhost:19080/Explorer`. Pak rozbalte uzel **Aplikace** a všimněte si, že už obsahuje položku pro váš typ aplikace a další položku pro první instanci tohoto typu.

> [!IMPORTANT]
> Pokud chcete nasadit aplikaci do clusteru zabezpečeného Linux v Azure, musíte nakonfigurovat certifikát pro ověření aplikace pomocí modulu runtime Service Fabric. To umožňuje, aby vaše služby Reliable Services komunikovaly se základními rozhraními API Service Fabric runtime. Další informace najdete v tématu [Konfigurace aplikace Reliable Services pro spouštění v clusterech se systémem Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Spuštění klienta testování a převzetí služeb při selhání
Projekty Actor samy o sobě nedělají nic. Vyžadují, aby jim jiná služba nebo klient posílali zprávy. Šablona actor zahrnuje jednoduchý testovací skript, který můžete použít k interakci se službou actor.

1. Spusťte skript pomocí pomocného sledovacího programu a prohlédněte si výstup služby actor.

   V případě systému MAC OS X je nutné zkopírovat složku myactorsvcTestClient do stejného umístění uvnitř kontejneru spuštěním následujících dalších příkazů.
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. V Service Fabric Exploreru vyhledejte uzel, který je hostitelem primární repliky pro službu actor. Na snímku níže je to uzel 3.

    ![Vyhledání primární repliky v Service Fabric Exploreru][sfx-primary]
3. Klikněte na uzel, který jste našli v předchozím kroku, a potom v nabídce Akce vyberte **Deaktivovat (restartovat)**. Tato akce restartuje jeden uzel v místním clusteru a vynutí převzetí služeb při selhání jednou ze sekundárních replik spuštěných v jiném uzlu. Při provádění této akce věnujte pozornost výstupu z klienta testování a všimněte si, že se čítač bez ohledu na převzetí služeb při selhání pořád postupně zvyšuje.

## <a name="adding-more-services-to-an-existing-application"></a>Přidání více služeb do stávající aplikace

Pokud chcete přidat další službu do aplikace již vytvořené pomocí `yo`, proveďte následující kroky:
1. Změňte adresář na kořenovou složku stávající aplikace.  Například `cd ~/YeomanSamples/MyApplication`, pokud `MyApplication` je aplikace vytvořená pomocí Yeomanu.
2. Spuštěním příkazu `yo azuresfcsharp:AddService`

## <a name="next-steps"></a>Další kroky

* [Komunikace s clustery Service Fabric pomocí rozhraní příkazového řádku Service Fabric](service-fabric-cli.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)
* [Začínáme se Service Fabric CLI](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
