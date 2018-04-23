---
title: Vytvoření aplikace Azure Service Fabric typu kontejner v Linuxu | Dokumentace Microsoftu
description: V tomto rychlém startu vytvoříte svou první aplikaci typu kontejner pro Linux na platformě Azure Service Fabric.  Sestavíte image Dockeru s vaší aplikací, nahrajete image do registru kontejneru a sestavíte a nasadíte aplikaci Service Fabric typu kontejner.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 65f048d67ef5f250691700a382e781814c57e8a8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Rychlý start: Nasazení aplikace Azure Service Fabric typu kontejner pro Linux v Azure
Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu škálovatelných a spolehlivých mikroslužeb a kontejnerů. 

Tento rychlý start ukazuje, jak nasadit kontejnery Linuxu do clusteru Service Fabric. Jakmile budete hotovi, budete mít hlasovací aplikaci skládající se z webového front-endu v Pythonu a back-endu Redis spuštěného v clusteru Service Fabric. Zjistíte také, jak převzít služby při selhání aplikace a jak škálovat aplikaci v clusteru.

![Webová stránka hlasovací aplikace][quickstartpic]

V tomto rychlém startu ke spouštění příkazů Service Fabric CLI použijete prostředí Bash ve službě Azure Cloud Shell. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud spouštíte Cloud Shell poprvé, zobrazí se výzva k nastavení sdílené složky `clouddrive`. Můžete přijmout výchozí hodnoty nebo připojit existující sdílenou složku. Další informace najdete v tématu [Nastavení sdílené složky `clouddrive`](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share).

## <a name="get-the-application-package"></a>Získání balíčku aplikace
K nasazení kontejnerů do Service Fabric potřebujete sadu souborů manifestu (definice aplikace), které popisují jednotlivé kontejnery a aplikaci.

Ve službě Cloud Shell pomocí gitu naklonujte kopii definice aplikace a pak přejděte do adresáře `Voting` ve vašem klonu. 

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Vytvoření clusteru Service Fabric
Pokud chcete nasadit aplikaci do Azure, potřebujete cluster Service Fabric, ve kterém bude aplikace spuštěná. Party Clustery nabízí snadný způsob, jak rychle vytvořit cluster Service Fabric. Party Clustery jsou bezplatné, časově omezené clustery Service Fabric hostované v Azure a provozované týmem Service Fabric. Party Clustery můžete použít k nasazování aplikací a seznámení se s platformou. Cluster k zajištění zabezpečení mezi uzly a mezi klientem a uzlem využívá jeden certifikát podepsaný svým držitelem.

Přihlaste se a připojte se ke [clusteru s Linuxem](http://aka.ms/tryservicefabric). Stáhněte si certifikát PFX do počítače kliknutím na odkaz **PFX**. Kliknutím na odkaz **ReadMe** zobrazíte heslo certifikátu a pokyny ke konfiguraci různých prostředí pro použití certifikátu. **Úvodní** stránku a stránku **ReadMe** nechte otevřené, protože některé pokyny využijete v následujících krocích. 

> [!Note]
> Každou hodinu je k dispozici omezený počet Party Clusterů. Pokud se vám při pokusu o registraci Party Clusteru zobrazí chyba, můžete chvíli počkat a zkusit to znovu nebo můžete podle kroků v kurzu [Vytvoření clusteru Service Fabric v Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) vytvořit cluster ve svém předplatném. 
> 
>Pokud vytvoříte vlastní cluster, nezapomeňte, že webová front-end služba je nakonfigurovaná k naslouchání příchozímu provozu na portu 80. Ujistěte se, že je ve vašem clusteru tento port otevřený. (Pokud používáte Party Cluster, je tento port otevřený.)
>

## <a name="configure-your-environment"></a>Konfigurace prostředí
Service Fabric poskytuje několik nástrojů, pomocí kterých můžete spravovat cluster a jeho aplikace:

- Nástroj v prohlížeči Service Fabric Explorer.
- Rozhraní příkazového řádku (CLI) Service Fabric, které se spouští nad Azure CLI 2.0.
- Příkazy PowerShellu. 

V tomto rychlém startu použijete Service Fabric CLI ve službě Cloud Shell a Service Fabric Explorer. V následujících částech se dozvíte, jak pomocí těchto nástrojů nainstalovat certifikát potřebný k připojení k zabezpečenému clusteru.

### <a name="configure-certificate-for-the-service-fabric-cli"></a>Konfigurace certifikátu pro Service Fabric CLI
Pokud chcete použít rozhraní příkazového řádku ve službě Cloud Shell, musíte do služby Cloud Shell nahrát soubor PFX certifikátu a pak ho použít k vytvoření souboru PEM.

1. Pokud chcete nahrát certifikát do aktuálního pracovního adresáře ve službě Cloud Shell, přetáhněte soubor PFX certifikátu ze složky na svém počítači, kam se stáhnul, do okna služby Cloud Shell.  

2. K převodu souboru PFX na soubor PEM použijte následující příkaz. (V případě Party Clusterů můžete zkopírovat příkaz specifický pro váš soubor PFX a heslo z pokynů na stránce **ReadMe**.)

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

### <a name="configure-certificate-for-service-fabric-explorer"></a>Konfigurace certifikátu pro Service Fabric Explorer
Pokud chcete použít Service Fabric Explorer, musíte importovat soubor PFX certifikátu, který jste stáhli z webu Party Clusteru, do svého úložiště certifikátů (Windows nebo Mac) nebo do samotného prohlížeče (Ubuntu). Potřebujete heslo privátního klíče PFX, které můžete získat na stránce **ReadMe**.

K importování certifikátu do svého systému použijte jakoukoli metodu, která vám vyhovuje nejvíce. Příklad:

- Windows: Dvakrát klikněte na soubor PFX a podle zobrazených výzev nainstalujte certifikát do svého osobního úložiště `Certificates - Current User\Personal\Certificates`. Alternativně můžete použít příkaz PowerShellu uvedený v pokynech **ReadMe**.
- Mac: Dvakrát klikněte na soubor PFX a podle zobrazených výzev nainstalujte certifikát do své klíčenky.
- Ubuntu: Výchozím prohlížečem v Ubuntu 16.04 je Mozilla Firefox. Pokud chcete certifikát importovat do prohlížeče Firefox, klikněte na tlačítko nabídky v pravém horním rohu prohlížeče a pak klikněte na **Možnosti**. Na stránce **Předvolby** pomocí vyhledávacího pole vyhledejte „certifikáty“. Klikněte na **Zobrazit certifikáty**, vyberte kartu **Osobní**, klikněte na **Importovat** a podle zobrazených výzev importujte certifikát.
 
   ![Instalace certifikátu v prohlížeči Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png) 

## <a name="deploy-the-service-fabric-application"></a>Nasazení aplikace Service Fabric 
1. Pomocí rozhraní příkazového řádku ve službě Cloud Shell se připojte ke clusteru Service Fabric v Azure. Koncový bod je koncový bod správy vašeho clusteru. Soubor PEM jste vytvořili v předchozí části. (V případě Party Clusterů můžete zkopírovat příkaz specifický pro váš soubor PEM a koncový bod správy z pokynů na stránce **ReadMe**.)

    ```bash
    sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
    ```

2. Pomocí instalačního skriptu zkopírujte definici hlasovací aplikace do clusteru, zaregistrujte typ aplikace a vytvořte její instanci.

    ```bash
    ./install.sh
    ```

2. Otevřete webový prohlížeč a přejděte na koncový bod Service Fabric Exploreru pro váš cluster. Koncový bod má následující formát: https://\<adresa_URL_clusteru_Azure_Service_Fabric>:19080/Explorer, například `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. </br>(V případě Party Clusterů můžete koncový bod Service Fabric Exploreru pro váš cluster zjistit na **Úvodní** stránce.) 

3. Rozbalte uzel **Aplikace** a všimněte si, že už obsahuje položku pro typ hlasovací aplikace a instanci, kterou jste vytvořili.

    ![Service Fabric Explorer][sfx]

3. Pokud se chcete připojit ke spuštěnému kontejneru, otevřete webový prohlížeč a přejděte na adresu URL vašeho clusteru, například `http://linh1x87d1d.westus.cloudapp.azure.com:80`. V prohlížeči by se měla zobrazit hlasovací aplikace.

    ![Webová stránka hlasovací aplikace][quickstartpic]


> [!NOTE]
> Aplikace Service Fabric můžete nasazovat také pomocí Docker Compose. K nasazení a instalaci aplikace v clusteru pomocí Docker Compose můžete použít například následující příkaz.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Převzetí služeb při selhání kontejneru v clusteru
Service Fabric zajišťuje v případě selhání automatický přesun instancí kontejneru do jiných uzlů clusteru. Kontejnery můžete z uzlu také ručně vyprázdnit a řádně je přesunout do jiných uzlů v clusteru. Service Fabric poskytuje několik způsobů škálování služeb. V následujících krocích použijete Service Fabric Explorer.

Pokud chcete převzít služby při selhání front-end kontejneru, proveďte následující kroky:

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Ve stromovém zobrazení klikněte na uzel **fabric:/Voting/azurevotefront** a rozbalte uzel oddílu (reprezentovaný identifikátorem GUID). Všimněte si názvu uzlu ve stromovém zobrazení, které zobrazuje uzly, na kterých je kontejner právě spuštěný – například `_nodetype_4`.
3. Ve stromovém zobrazení rozbalte uzel **Uzly**. Klikněte na tři tečky (...) vedle uzlu, na kterém je kontejner spuštěný.
4. Pokud chcete tento uzel restartovat, zvolte **Restartovat** a potvrďte akci restartování. Restartování způsobí převzetí služeb při selhání kontejneru do jiného uzlu v clusteru.

    ![Zobrazení uzlu v Service Fabric Exploreru][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Škálování aplikací a služeb v clusteru
Služby Service Fabric je možné snadno škálovat napříč clusterem a vyřešit tak jejich zatížení. Služby se škálují změnou počtu instancí spuštěných v clusteru.

Pokud chcete škálovat webovou front-end službu, proveďte následující kroky:

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `https://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Ve stromovém zobrazení klikněte na tři tečky (...) vedle uzlu **fabric:/Voting/azurevotefront** a zvolte **Škálovat službu**.

    ![Service Fabric Explorer – zahájení škálování služby][containersquickstartscale]

  Nyní můžete škálovat počet instancí webové front-end služby.

3. Změňte počet na **2** a klikněte na **Škálovat službu**.
4. Ve stromovém zobrazení klikněte na uzel **fabric:/Voting/azurevotefront** a rozbalte uzel oddílu (reprezentovaný identifikátorem GUID).

    ![Service Fabric Explorer – dokončení škálování služby][containersquickstartscaledone]

    Nyní je vidět, že má služba dvě instance. Ve stromovém zobrazení vidíte, na kterých uzlech instance spuštěné.

Touto jednoduchou úlohou správy jste zdvojnásobili prostředky, které má front-end služba k dispozici pro zpracování uživatelské zátěže. Je důležité si uvědomit, že pro spolehlivý provoz služby nepotřebujete více jejích instancí. Pokud služba selže, Service Fabric zajistí v clusteru spuštění nové instance služby.

## <a name="clean-up-resources"></a>Vyčištění prostředků
1. Pomocí odinstalačního skriptu (uninstall.sh), který je součástí šablony, odstraňte instanci aplikace z clusteru a zrušte registraci typu aplikace. Vyčištění instance tímto skriptem nějakou dobu trvá, proto byste instalační skript neměli spouštět ihned po tomto skriptu. Pomocí Service Fabric Exploreru můžete určit, kdy došlo k odebrání instance a zrušení registrace typu aplikace. 

    ```bash
    ./uninstall.sh
    ```

2. Pokud už jste dokončili práci se svým clusterem, můžete odebrat certifikát ze svého úložiště certifikátů. Příklad:
   - Windows: Použijte [modul snap-in Certifikáty v konzole MMC](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Při přidávání modulu snap-in nezapomeňte vybrat možnost **Můj uživatelský účet**. Přejděte do umístění `Certificates - Current User\Personal\Certificates` a odeberte certifikát.
   - Mac: Použijte aplikaci Klíčenka.
   - Ubuntu: Postupujte podle kroků, pomocí kterých jste zobrazili certifikáty a odebrali certifikát.

3. Pokud už nechcete používat Cloud Shell, můžete odstranit přidružený účet úložiště, abyste se vyhnuli dalším poplatkům. Ukončete svou relaci služby Cloud Shell. Na webu Azure Portal klikněte na účet úložiště přidružený ke službě Cloud Shell, v horní části stránky klikněte na **Odstranit** a postupujte podle zobrazených výzev.

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste nasadili aplikaci typu kontejner pro Linux do clusteru Service Fabric v Azure, provedli jste převzetí služeb při selhání aplikace a škálovali jste aplikaci v clusteru. Další informace o práci s kontejnery Linuxu v Service Fabric najdete v kurzu věnovaném aplikacím typu kontejner pro Linux.

> [!div class="nextstepaction"]
> [Vytvoření aplikace typu kontejner pro Linux](./service-fabric-tutorial-create-container-images.md)


[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
