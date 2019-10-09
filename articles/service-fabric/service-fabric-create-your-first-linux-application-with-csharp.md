---
title: Vytvoření první aplikace Azure Service Fabric v systému Linux pomocí C#| Microsoft Docs
description: Naučte se vytvářet a nasazovat Service Fabric aplikace s C# využitím a .net Core 2,0.
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: atsenthi
ms.openlocfilehash: 8c34f50e8eb9d9793c146c38aca47691b04049c0
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167307"
---
# <a name="create-your-first-azure-service-fabric-application"></a>Vytvoření první aplikace Service Fabric v Azure
> [!div class="op_single_selector"]
> * [Java – Linux (Preview)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C#– Linux (Preview)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric poskytuje sady SDK pro vytváření služeb na platformě Linux v rozhraní .NET Core i Java. V tomto kurzu se podíváme na postup vytvoření aplikace pro Linux a vytvoření služby pomocí C# .net Core 2,0.

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že jste [nastavili vývojové prostředí pro Linux](service-fabric-get-started-linux.md). Pokud používáte Mac OS X, můžete [na virtuálním počítači pomocí Vagrant nastavit prostředí pro jedno okno se systémem Linux](service-fabric-get-started-mac.md).

Budete také chtít nainstalovat rozhraní příkazového [řádku Service Fabric](service-fabric-cli.md)

### <a name="install-and-set-up-the-generators-for-c"></a>Instalace a nastavení generátorů proC#
Service Fabric poskytuje nástroje pro generování uživatelského rozhraní, které vám pomůžou vytvářet Service Fabric aplikace z terminálu pomocí generátorů šablon Yeoman. Pomocí těchto kroků můžete nastavit generátory šablon Service Fabric Yeoman pro C#:

1. Instalace NodeJS a NPM na váš počítač

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. Nainstalujte na svém počítači generátor šablon [Yeoman](https://yeoman.io/) z npm

   ```bash
   npm install -g yo
   ```
3. Instalace generátoru aplikace C# Service Fabric YEOMAN z npm

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>Vytvoření aplikace
Service Fabric aplikace může obsahovat jednu nebo více služeb, z nichž každá má určitou roli při poskytování funkcí aplikace. Generátor Service Fabric [Yeoman](https://yeoman.io/) pro C#, který jste nainstalovali v posledním kroku, usnadňuje vytvoření první služby a pozdější přidání. Pojďme pomocí Yeoman vytvořit aplikaci s jednou službou.

1. V terminálu zadejte následující příkaz, kterým začnete sestavovat generování uživatelského rozhraní: `yo azuresfcsharp`
2. Pojmenujte svoji aplikaci.
3. Vyberte typ první služby a pojmenujte ji. Pro účely tohoto kurzu zvolíme službu Reliable actor.

   ![Service Fabric generátor Yeoman proC#][sf-yeoman]

> [!NOTE]
> Další informace o možnostech najdete v tématu [Přehled programovacího modelu Service Fabric](service-fabric-choose-framework.md).
>
>

## <a name="build-the-application"></a>Sestavení aplikace
Šablony Service Fabric Yeoman obsahují skript sestavení, který můžete použít k sestavení aplikace z terminálu (po přechodu do složky aplikace).

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>Nasazení aplikace

Jakmile je aplikace sestavená, můžete ji nasadit do místního clusteru.

1. Připojte se k místnímu clusteru Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Spuštěním instalačního skriptu, který je součástí šablony, zkopírujte balíček aplikace do úložiště imagí clusteru, zaregistrujte typ aplikace a vytvořte instanci aplikace.

    ```bash
    ./install.sh
    ```

Nasazení sestavené aplikace je stejné jako u jakékoli jiné aplikace Service Fabric. Podrobné pokyny najdete v dokumentaci ke [správě Service Fabric aplikace pomocí Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md) .

Parametry pro tyto příkazy lze nalézt v generovaných manifestech uvnitř balíčku aplikace.

Jakmile je aplikace nasazená, otevřete prohlížeč a přejděte na [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) na [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Pak rozbalte uzel **aplikace** a Všimněte si, že teď existuje položka pro typ aplikace a druhá pro první instanci tohoto typu.

> [!IMPORTANT]
> Pokud chcete nasadit aplikaci do clusteru zabezpečeného Linux v Azure, musíte nakonfigurovat certifikát pro ověření aplikace pomocí modulu runtime Service Fabric. To umožňuje, aby vaše služby Reliable Services komunikovaly se základními rozhraními API Service Fabric runtime. Další informace najdete v tématu [Konfigurace aplikace Reliable Services pro spouštění v clusterech se systémem Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Spuštění testovacího klienta a provedení převzetí služeb při selhání
Projekty objektu actor samy o sobě nedělají žádnou z nich. Vyžadují jinou službu nebo klienta k posílání zpráv. Šablona objektu actor obsahuje jednoduchý testovací skript, který můžete použít k interakci se službou objektu actor.

1. Spusťte skript pomocí sledovacího nástroje a zobrazte výstup služby objektu actor.

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
2. V Service Fabric Explorer vyhledejte uzel, který je hostitelem primární repliky pro službu objektu actor. Na následujícím snímku obrazovky je uzel 3.

    ![Hledání primární repliky v Service Fabric Explorer][sfx-primary]
3. Klikněte na uzel, který jste našli v předchozím kroku, a pak v nabídce Akce vyberte **deaktivovat (restartovat)** . Tato akce restartuje jeden uzel v místním clusteru a vynutí převzetí služeb při selhání sekundární replikou běžící na jiném uzlu. Při provádění této akce věnujte pozornost výstupu z testovacího klienta a Všimněte si, že se čítač stále zvyšuje i bez ohledu na převzetí služeb při selhání.

## <a name="adding-more-services-to-an-existing-application"></a>Přidání dalších služeb do existující aplikace

Chcete-li přidat další službu do aplikace již vytvořené pomocí `yo`, proveďte následující kroky:
1. Změňte adresář na kořen existující aplikace.  Například `cd ~/YeomanSamples/MyApplication`, pokud `MyApplication` je aplikace vytvořená pomocí Yeoman.
2. Spustit @no__t – 0

## <a name="next-steps"></a>Další kroky

* [Interakce s Service Fabric clustery pomocí rozhraní příkazového řádku Service Fabric](service-fabric-cli.md)
* Další informace o [možnostech podpory Service Fabric](service-fabric-support.md)
* [Začínáme s Service Fabric CLI](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
