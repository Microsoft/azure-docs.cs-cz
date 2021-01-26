---
title: Vytvoření aplikace typu kontejner pro Linux v Service Fabric v Azure
description: V tomto rychlém startu sestavíte image Dockeru s vaší aplikací, nahrajete image do registru kontejneru a pak nasadíte kontejner do clusteru Service Fabric.
ms.topic: quickstart
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f58b8feda0e88d8a5e7cddaabbc650b0f0ab3973
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788158"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Rychlý start: Nasazení kontejnerů Linuxu do Service Fabric

Azure Service Fabric je platforma distribuovaných systémů pro nasazování a správu škálovatelných a spolehlivých mikroslužeb a kontejnerů.

V tomto rychlém startu se dozvíte, jak nasadit kontejnery Linux do clusteru Service Fabric v Azure. Jakmile budete hotovi, budete mít hlasovací aplikaci skládající se z webového front-endu v Pythonu a back-endu Redis spuštěného v clusteru Service Fabric. Zjistíte také, jak převzít služby při selhání aplikace a jak škálovat aplikaci v clusteru.

![Webová stránka hlasovací aplikace][quickstartpic]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

1. Vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete, pokud nemáte předplatné.

2. Instalace rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli-apt)

3. Instalace [sady Service Fabric SDK a](service-fabric-get-started-linux.md#installation-methods) rozhraní PŘÍKAZového řádku

4. Nainstalovat [Git](https://git-scm.com/)


## <a name="get-the-application-package"></a>Získání balíčku aplikace

K nasazení kontejnerů do Service Fabric potřebujete sadu souborů manifestu (definice aplikace), které popisují jednotlivé kontejnery a aplikaci.

V konzole nástroje pomocí Gitu naklonujte kopii definice aplikace; pak změňte adresář na `Voting` adresář ve vašem klonu.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Vytvoření clusteru Service Fabric

Pokud chcete nasadit aplikaci do Azure, potřebujete cluster Service Fabric, ve kterém bude aplikace spuštěná. Následující příkazy vytvoří cluster s pěti uzly v Azure.  Příkazy také vytvoří certifikát podepsaný svým držitelem, přidá ho do trezoru klíčů a stáhne certifikát místně. Nový certifikát se používá k zabezpečení clusteru při jeho nasazení a slouží k ověřování klientů.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName='containertestcluster' 
ClusterName='containertestcluster' 
Location='eastus' 
Password='q6D7nN%6ck@6' 
Subject='containertestcluster.eastus.cloudapp.azure.com' 
VaultName='containertestvault' 
VmPassword='Mypa$$word!321'
VmUserName='sfadminuser'

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> Webová front-end služba je nakonfigurovaná k naslouchání příchozímu provozu na portu 80. Ve výchozím nastavení je na virtuálních počítačích clusteru a nástroji pro vyrovnávání zatížení Azure otevřený port 80.
>

## <a name="configure-your-environment"></a>Konfigurace prostředí

Service Fabric poskytuje několik nástrojů, pomocí kterých můžete spravovat cluster a jeho aplikace:

- Nástroj v prohlížeči Service Fabric Explorer.
- Rozhraní příkazového řádku (CLI) Service Fabric, které se spouští nad Azure CLI. 
- Příkazy PowerShellu.

V tomto rychlém startu použijete Service Fabric CLI a Service Fabric Explorer (nástroj založený na webu). Chcete-li použít Service Fabric Explorer, je nutné importovat soubor PFX certifikátu do prohlížeče. Ve výchozím nastavení nemá soubor PFX žádné heslo.

Mozilla Firefox je výchozím prohlížečem v Ubuntu 16,04. Pokud chcete certifikát importovat do prohlížeče Firefox, klikněte na tlačítko nabídky v pravém horním rohu prohlížeče a pak klikněte na **Možnosti**. Na stránce **Předvolby** pomocí vyhledávacího pole vyhledejte „certifikáty“. Klikněte na **Zobrazit certifikáty**, vyberte kartu **Osobní**, klikněte na **Importovat** a podle zobrazených výzev importujte certifikát.

   ![Instalace certifikátu v prohlížeči Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Nasazení aplikace Service Fabric

1. Připojte se ke clusteru Service Fabric v Azure pomocí rozhraní příkazového řádku. Koncový bod je koncový bod správy vašeho clusteru. Soubor PEM jste vytvořili v předchozí části. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. Pomocí instalačního skriptu zkopírujte definici hlasovací aplikace do clusteru, zaregistrujte typ aplikace a vytvořte její instanci.  Soubor certifikátu PEM by měl být umístěn ve stejném adresáři jako soubor *install.sh* .

    ```bash
    ./install.sh
    ```

3. Otevřete webový prohlížeč a přejděte na koncový bod Service Fabric Exploreru pro váš cluster. Koncový bod má následující formát:  **https:// \<my-azure-service-fabric-cluster-url> : 19080/Explorer**; například `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer` . </br>

4. Rozbalením uzlu **aplikace** zjistíte, že nyní existuje položka pro typ hlasovací aplikace a instanci, kterou jste vytvořili.

    ![Service Fabric Explorer][sfx]

5. Pokud se chcete připojit ke spuštěnému kontejneru, otevřete webový prohlížeč a přejděte na adresu URL vašeho clusteru, například `http://containertestcluster.eastus.cloudapp.azure.com:80`. V prohlížeči by se měla zobrazit hlasovací aplikace.

    ![Webová stránka hlasovací aplikace][quickstartpic]

> [!NOTE]
> Aplikace Service Fabric můžete nasazovat také pomocí Docker Compose. K nasazení a instalaci aplikace v clusteru pomocí Docker Compose můžete použít například následující příkaz.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Převzetí služeb při selhání kontejneru v clusteru

Service Fabric zajišťuje v případě selhání automatický přesun instancí kontejneru do jiných uzlů clusteru. Kontejnery můžete z uzlu také ručně vyprázdnit a řádně je přesunout do jiných uzlů v clusteru. Service Fabric poskytuje několik způsobů škálování služeb. V následujících krocích použijete Service Fabric Explorer.

Pokud chcete převzít služby při selhání front-end kontejneru, proveďte následující kroky:

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`.
2. Ve stromovém zobrazení klikněte na uzel **fabric:/Voting/azurevotefront** a rozbalte uzel oddílu (reprezentovaný identifikátorem GUID). Všimněte si názvu uzlu ve stromovém zobrazení, které zobrazuje uzly, na kterých je kontejner právě spuštěný – například `_nodetype_1`.
3. Ve stromovém zobrazení rozbalte uzel **Uzly**. Klikněte na tři tečky (...) vedle uzlu, na kterém je kontejner spuštěný.
4. Pokud chcete tento uzel restartovat, zvolte **Restartovat** a potvrďte akci restartování. Restartování způsobí převzetí služeb při selhání kontejneru do jiného uzlu v clusteru.

    ![Zobrazení uzlu v Service Fabric Exploreru][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Škálování aplikací a služeb v clusteru

Služby Service Fabric je možné snadno škálovat napříč clusterem a vyřešit tak jejich zatížení. Služby se škálují změnou počtu instancí spuštěných v clusteru.

Pokud chcete škálovat webovou front-end službu, proveďte následující kroky:

1. Otevřete ve vašem clusteru Service Fabric Explorer – například `https://containertestcluster.eastus.cloudapp.azure.com:19080`.
2. Ve stromovém zobrazení klikněte na tři tečky (...) vedle uzlu **fabric:/Voting/azurevotefront** a zvolte **Škálovat službu**.

    ![Service Fabric Explorer – zahájení škálování služby][containersquickstartscale]

    Teď můžete škálovat počet instancí webové front-end služby.

3. Změňte počet na **2** a klikněte na **Škálovat službu**.
4. Ve stromovém zobrazení klikněte na uzel **fabric:/Voting/azurevotefront** a rozbalte uzel oddílu (reprezentovaný identifikátorem GUID).

    ![Service Fabric Explorer – dokončení škálování služby][containersquickstartscaledone]

    Teď je vidět, že má služba dvě instance. Ve stromovém zobrazení vidíte, na kterých uzlech jsou instance spuštěné.

Touto jednoduchou úlohou správy jste zdvojnásobili prostředky, které má front-end služba k dispozici pro zpracování uživatelské zátěže. Je důležité si uvědomit, že pro spolehlivý provoz služby nepotřebujete více jejích instancí. Pokud služba selže, Service Fabric zajistí v clusteru spuštění nové instance služby.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí odinstalačního skriptu (uninstall.sh), který je součástí šablony, odstraňte instanci aplikace z clusteru a zrušte registraci typu aplikace. Vyčištění instance tímto skriptem nějakou dobu trvá, proto byste instalační skript neměli spouštět ihned po tomto skriptu. Pomocí Service Fabric Exploreru můžete určit, kdy došlo k odebrání instance a zrušení registrace typu aplikace.

```bash
./uninstall.sh
```

Nejjednodušší způsob, jak odstranit cluster a všechny prostředky, které využívá, je odstranit příslušnou skupinu prostředků.

Přihlaste se k Azure a vyberte ID předplatného, se kterým chcete cluster odebrat. Své ID předplatného můžete zjistit po přihlášení k webu Azure Portal. Pomocí [příkazu AZ Group Delete](/cli/azure/group)odstraňte skupinu prostředků a všechny prostředky clusteru.

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

Pokud už jste dokončili práci se svým clusterem, můžete odebrat certifikát ze svého úložiště certifikátů. Například:
- Windows: Použijte [modul snap-in Certifikáty v konzole MMC](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Při přidávání modulu snap-in nezapomeňte vybrat možnost **Můj uživatelský účet**. Přejděte do umístění `Certificates - Current User\Personal\Certificates` a odeberte certifikát.
- Mac: Použijte aplikaci Klíčenka.
- Ubuntu: Postupujte podle kroků, pomocí kterých jste zobrazili certifikáty a odebrali certifikát.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili aplikaci typu kontejner pro Linux do clusteru Service Fabric v Azure, provedli jste převzetí služeb při selhání aplikace a škálovali jste aplikaci v clusteru. Další informace o práci s kontejnery Linuxu v Service Fabric najdete v kurzu věnovaném aplikacím typu kontejner pro Linux.

> [!div class="nextstepaction"]
> [Vytvoření aplikace typu kontejner pro Linux](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
