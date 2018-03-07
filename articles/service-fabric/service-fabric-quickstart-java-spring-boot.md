---
title: "Nasazení aplikace Spring Boot do Azure Service Fabric | Dokumentace Microsoftu"
description: "V tomto rychlém startu nasadíte aplikaci Spring Boot pro Azure Service Fabric s využitím ukázkové aplikace Spring Boot."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: ab860b8525bcb77d3ab35d3f649532713c661b61
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-azure"></a>Rychlý start: Nasazení aplikace Spring Boot v Javě do Azure
Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu mikroslužeb a kontejnerů. 

Tento rychlý start ukazuje, jak nasadit aplikaci Spring Boot do Service Fabric. Tento rychlý start využívá ukázku [Getting Started](https://spring.io/guides/gs/spring-boot/) z webu Spring. Pomocí známých nástrojů příkazového řádku vás tento rychlý start provede nasazením ukázky Spring Boot jako aplikace Service Fabric. Po dokončení budete mít funkční aplikaci Spring Boot Getting Started v Service Fabric. 

![Snímek obrazovky aplikace](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

V tomto rychlém startu se naučíte:

> [!div class="checklist"]
> * Nasazení aplikace Spring Boot do služby Service Fabric
> * Nasazení aplikace do místního clusteru 
> * Nasazení aplikace do clusteru v Azure
> * Škálování aplikace na více instancí napříč několika uzly
> * Převzetí služeb při selhání vaší služby bez omezení dostupnosti

## <a name="prerequisites"></a>Požadavky
K provedení kroků v tomto kurzu Rychlý start je potřeba:
1. [Nainstalovat sadu Service Fabric SDK a rozhraní příkazového řádku (CLI) Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods).
2. [Nainstalovat Git](https://git-scm.com/).
3. [Nainstalovat Yeomana](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables).
4. [Nastavit prostředí Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development).

## <a name="download-the-sample"></a>Stažení ukázky
V příkazovém okně naklonujte spuštěním následujícího příkazu ukázkovou aplikaci Spring Boot Getting Started do místního počítače.
```
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>Zabalení aplikace Spring Boot 
1. Uvnitř naklonovaného adresáře `gs-spring-boot` spusťte příkaz `yo azuresfguest`. 

2. Pro jednotlivé výzvy zadejte následující informace. 

    ![Položky Yeomanu](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. Ve složce `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` vytvořte soubor nazvaný `entryPoint.sh`. Do souboru přidejte následující: 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

V této fázi jste vytvořili aplikaci Service Fabric pro ukázku Spring Boot Getting Started, kterou můžete nasadit do Service Fabric.

## <a name="run-the-application-locally"></a>Místní spuštění aplikace
1. Spusťte místní cluster spuštěním následujícího příkazu:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Spuštění místního clusteru nějakou dobu trvá. Pokud chcete potvrdit, že je cluster plně funkční, přejděte do nástroje Service Fabric Explorer na adrese **http://localhost:19080**. Pět uzlů v pořádku značí, že je místní cluster zprovozněný. 
    
    ![Místní cluster v pořádku](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. Přejděte do složky `gs-spring-boot/SpringServiceFabric`.
3. Spusťte následující příkaz pro připojení k místnímu clusteru. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. Spusťte skript `install.sh`. 

    ```bash
    ./install.sh
    ```

5. Otevřete oblíbený webový prohlížeč a přejděte do aplikace na adrese **http://localhost:8080**. 

    ![Front-end aplikace – místní prostředí](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
Teď máte přístup k aplikaci Spring Boot, která je nasazená do clusteru Service Fabric.  

## <a name="deploy-the-application-to-azure"></a>Nasazení aplikace v Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Nastavení clusteru Azure Service Fabric
Pokud chcete nasadit aplikaci do clusteru v Azure, vytvořte si vlastní cluster.

Party Clustery jsou bezplatné, časově omezené clustery Service Fabric hostované v Azure. Jsou provozované týmem Service Fabric a kdokoli na nich může nasazovat aplikace a seznamovat se s platformou. Pokud chcete získat přístup k Party Clusteru, [postupujte podle těchto pokynů](http://aka.ms/tryservicefabric). 

K provádění operací správy na zabezpečeném Party Clusteru můžete použít Service Fabric Explorer, rozhraní příkazového řádku nebo PowerShell. Pokud chcete použít Service Fabric Explorer, budete muset z webu Party Clusteru stáhnout soubor PFX a importovat certifikát do svého úložiště certifikátů (Windows nebo Mac) nebo do samotného prohlížeče (Ubuntu). K certifikátům podepsaným svým držitelem z Party Clusteru není žádné heslo. 

Pokud chcete provádět operace správy pomocí PowerShellu nebo rozhraní příkazového řádku, budete potřebovat soubor PFX (PowerShell) nebo PEM (rozhraní příkazového řádku). Pokud chcete převést soubor PFX na soubor PEM, spusťte následující příkaz:  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

Informace o vytvoření vlastního clusteru najdete v tématu věnovaném [vytvoření clusteru Service Fabric v Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

> [!Note]
> Služba Spring Bot je nakonfigurovaná k naslouchání příchozímu provozu na portu 8080. Ujistěte se, že je ve vašem clusteru tento port otevřený. Pokud používáte Party Cluster, je tento port otevřený.
>

### <a name="deploy-the-application-using-cli"></a>Nasazení aplikace pomocí rozhraní příkazového řádku
Když jsou teď aplikace i cluster připravené, můžete aplikaci nasadit do clusteru přímo z příkazového řádku.

1. Přejděte do složky `gs-spring-boot/SpringServiceFabric`.
2. Spusťte následující příkaz pro připojení ke clusteru Azure. 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    Pokud je tento cluster zabezpečený pomocí certifikátu podepsaného svým držitelem, spustíte tento příkaz: 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Spusťte skript `install.sh`. 

    ```bash
    ./install.sh
    ```

4. Otevřete oblíbený webový prohlížeč a přejděte do aplikace na adrese **http://\<ConnectionIPOrUrl>:8080**. 

    ![Front-end aplikace – místní prostředí](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
Teď máte přístup k aplikaci Spring Boot, která je nasazená do clusteru Service Fabric.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Škálování aplikací a služeb v clusteru
Služby je možné škálovat napříč clusterem a vyřešit tak změny v jejich zatížení. Služby se škálují změnou počtu instancí spuštěných v clusteru. Služby můžete škálovat několika způsoby – můžete použít skripty nebo příkazy v Service Fabric CLI (sfctl). V tomto příkladu používáme Service Fabric Explorer.

Nástroj Service Fabric Explorer běží na všech clusterech Service Fabric a je přístupný z prohlížeče po přechodu na port HTTP pro správu clusteru (19080), například `http://localhost:19080`.

Pokud chcete škálovat webovou front-end službu, proveďte následující kroky:

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `http://localhost:19080`.
2. Ve stromovém zobrazení klikněte na tři tečky vedle uzlu **fabric:/SpringServiceFabric/SpringGettingStarted** a zvolte **Škálovat službu**.

    ![Service Fabric Explorer – škálování služby](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Nyní můžete škálovat počet instancí služby.

3. Změňte počet na **3** a klikněte na **Škálovat službu**.

    Jako alternativu ke škálování služby pomocí příkazového řádku můžete použít následující postup.

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint http://localhost:19080

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. Ve stromovém zobrazení klikněte na uzel **fabric:/SpringServiceFabric/SpringGettingStarted** a rozbalte uzel oddílu (reprezentovaný identifikátorem GUID).

    ![Service Fabric Explorer – dokončení škálování služby](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    Služba má tři instance a stromové zobrazení ukazuje, na kterých uzlech jsou tyto instance spuštěné.

Touto jednoduchou úlohou správy se zvýšilo množství prostředků, které má naše služba Spring k dispozici pro zpracování uživatelské zátěže. Je důležité si uvědomit, že pro spolehlivý provoz služby nepotřebujete více jejích instancí. Pokud služba selže, Service Fabric zajistí v clusteru spuštění nové instance služby.

## <a name="fail-over-services-in-a-cluster"></a>Převzetí služeb při selhání v clusteru 
Jako ukázku převzetí služeb při selhání simulujeme restartování uzlu pomocí Service Fabric Exploreru. Zkontrolujte, že je spuštěná jenom jedna instance vaší služby. 

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `http://localhost:19080`.
2. Klikněte na tři tečky vedle uzlu, na kterém je spuštěná instance vaší služby, a restartujte uzel. 

    ![Service Fabric Explorer – restartování uzlu](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. Instance vaší služby se přesune na jiný uzel a vaše aplikace nebude mít žádný výpadek. 

    ![Service Fabric Explorer – úspěšné restartování uzlu](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste se naučili:

> [!div class="checklist"]
> * Nasazení aplikace Spring Boot do služby Service Fabric
> * Nasazení aplikace do místního clusteru 
> * Nasazení aplikace do clusteru v Azure
> * Škálování aplikace na více instancí napříč několika uzly
> * Převzetí služeb při selhání vaší služby bez omezení dostupnosti

* Další informace o [sestavení mikroslužeb Java s využitím programovacích modelů Service Fabric](service-fabric-quickstart-java-reliable-services.md)
* Další informace o [nastavení průběžné integrace a nasazování pomocí Jenkinse](service-fabric-cicd-your-linux-applications-with-jenkins.md)
* Podívejte se na další [ukázky v Javě](https://github.com/Azure-Samples/service-fabric-java-getting-started).