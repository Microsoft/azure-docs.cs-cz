---
title: Konsorcium Hyperledger Fabric ve službě Azure Kubernetes Service (AKS)
description: Jak nasadit a nakonfigurovat síť konsorcia Hyperledger Fabric ve službě Azure Kubernetes Service
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476436"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Konsorcium Hyperledger Fabric ve službě Azure Kubernetes Service (AKS)

K nasazení a konfiguraci sítě konsorcia Hyperledger Fabric v Azure můžete použít šablonu Hyperledger Fabric (HLF) ve službě Azure Kubernetes Service (AKS).

Po přečtení tohoto článku:

- Získejte pracovní znalosti o Hyperledger Fabric a různých komponentách, které tvoří stavební kameny blockchainové sítě Hyperledger Fabric.
- Zjistěte, jak nasadit a nakonfigurovat konsorcium Hyperledger Fabric ve službě Azure Kubernetes pro vaše produkční scénáře.

## <a name="hyperledger-fabric-consortium-architecture"></a>Architektura konsorcia Hyperledger Fabric

Chcete-li vytvořit síť Hyperledger Fabric v Azure, musíte nasadit službu objednávání a organizaci s uzly rovnocenných trhů. Různé základní součásti, které jsou vytvořeny jako součást nasazení šablony jsou:

- **Uzly objednatele**: Uzel, který je zodpovědný za objednávání transakcí v hlavní knize. Spolu s dalšími uzly tvoří objednané uzly objednávkovou službu sítě Hyperledger Fabric.

- **Partnerské uzly**: Uzel, který primárně hostuje hlavní knihy a inteligentní smlouvy, tyto základní prvky sítě.

- **Fabric CA**: Fabric CA je certifikační autorita (CA) pro hyperledger fabric. Certifikační autorita fabric umožňuje inicializovat a spustit serverový proces, který je hostitelem certifikační autority. Umožňuje spravovat identity a certifikáty. Každý cluster AKS nasazený jako součást šablony bude mít ve výchozím nastavení pod certifikační autority fabric.

- **CouchDB nebo LevelDB**: Světová stavová databáze pro partnerské uzly mohou být uloženy buď v LevelDB nebo CouchDB. LevelDB je výchozí stav databáze vložené do uzlu partnera a ukládá data kódu řetězce jako jednoduché dvojice klíč-hodnota a podporuje pouze klíčové, klíčové rozsah a složené klíčové dotazy. CouchDB je volitelná databáze alternativního stavu, která podporuje bohaté dotazy při datech chaincode jsou modelovány jako JSON.

Šablona na nasazení stočí různé prostředky Azure ve vašem předplatném. Různé nasazené prostředky Azure jsou:

- **Cluster AKS**: Cluster Azure Kubernetes, který je nakonfigurován podle vstupních parametrů poskytnutých zákazníkem. Cluster AKS má různé pody nakonfigurované pro spuštění síťových komponent Hyperledger Fabric. Různé vytvořené pody jsou:

  - **Nástroje pro textilie**: Nástroj fabric je zodpovědný za konfiguraci součástí Hyperledger Fabric.
  - **Moduly Pro objednavatelé/peerové moduly**: Uzly sítě HLF.
  - **Proxy**server : Proxy pod NGNIX, jehož prostřednictvím mohou klientské aplikace komunikovat s clusterem AKS.
  - **Fabric CA**: Pod, který spouští fabric CA.
- **PostgreSQL**: Instance PostgreSQL je nasazena k udržování identit y FABRIC CA.

- **Trezor klíčů Azure**: Instance trezoru klíčů se nasadí k uložení přihlašovacích údajů certifikační autority fabric a kořenových certifikátů poskytnutých zákazníkem, který se používá v případě opakování nasazení šablony, to je pro zpracování mechaniky šablony.
- **Disk Azure Managed**: Disk Azure Managed je pro trvalé úložiště pro databázi stavu hlavního počítače a peerového uzlu.
- **Veřejná IP adresa**: Veřejný koncový bod IP clusteru AKS nasazený pro propojení s clusterem.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Nastavení sítě Hyperledger Fabric Blockchain

Chcete-li začít, budete potřebovat předplatné Azure, které podporuje nasazení několika virtuálních počítačů a standardní účty úložiště. Pokud nemáte předplatné Azure, můžete [si vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/).

Nastavení sítě Hyperledger Fabric Blockchain pomocí následujících kroků:

- [Nasazení organizace objednateč/druhá strana](#deploy-the-ordererpeer-organization)
- [Sestavení konsorcia](#build-the-consortium)
- [Spustit nativní operace HLF](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Nasazení organizace objednateč/druhá strana

Pokud chcete začít s nasazením síťových komponent HLF, přejděte na [portál Azure](https://portal.azure.com). Vyberte **vytvořit prostředek > blockchain** > hledání **hyperledger fabric na Azure Kubernetes Service**.

1. Výběrem **možnosti vytvořit** spustíte nasazení šablony. **Zobrazí se vytvoření struktury Hyperledger Fabric ve službě Azure Kubernetes.**

    ![Infrastruktura Hyperledger u šablony služby Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Zadejte podrobnosti o projektu na stránce **Základy.**

    ![Infrastruktura Hyperledger u šablony služby Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Zadejte následující podrobnosti:
    - **Předplatné**: Zvolte název předplatného, do kterého chcete nasadit síťové součásti HLF.
    - **Skupina prostředků**: Buď vytvořte novou skupinu prostředků, nebo zvolte existující prázdnou skupinu prostředků, skupina prostředků bude obsahovat všechny prostředky nasazené jako součást šablony.
    - **Oblast**: Zvolte oblast Azure, kde chcete nasadit cluster Azure Kubernetes pro součásti HLF. Šablona je k dispozici ve všech oblastech, kde je k dispozici AKS: Ujistěte se, že zvolte oblast, kde vaše předplatné nedosahuje limitu kvóty virtuálního počítače.
    - **Předpona prostředků**: Předpona pro pojmenování prostředků, které jsou nasazeny. Předpona zdroje musí být kratší než šest znaků a kombinace znaků musí obsahovat čísla i písmena.
4. Vyberte kartu **Nastavení prostředků infrastruktury** a definujte síťové součásti HLF, které budou nasazeny.

    ![Infrastruktura Hyperledger u šablony služby Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Zadejte následující podrobnosti:
    - **Název organizace**: Název organizace Fabric, který je vyžadován pro různé operace roviny dat. Název organizace musí být jedinečný pro nasazení. 
    - **Síťová komponenta Fabric**: Zvolte buď objednávací službu nebo uzly peer na základě síťové součásti Blockchain, kterou chcete nastavit.
    - **Počet uzlů** – následující jsou dva typy uzlů:
        - Objednaná služba - vyberte počet uzlů, které mají být zajištěny odolností proti chybám v síti. Pouze 3,5 a 7 jsou podporované pořadí uzlu počet.
        - Partnerské uzly - můžete si vybrat 1-10 uzlů na základě vašeho požadavku.
    - **Peer uzel světové databáze stavu**: Vyberte si mezi LevelDB a CoucbDB. Toto pole se zobrazí, když uživatel zvolí uzel partnerské sítě v rozevíracím seznamu síťové součásti prostředků infrastruktury.
    - **Uživatelské jméno prostředků infrastruktury**: Zadejte uživatelské jméno, které se používá pro ověřování certifikační autority prostředků infrastruktury.
    - **Heslo certifikační autority prostředků infrastruktury**: Zadejte heslo pro ověřování certifikační autority prostředků infrastruktury.
    - **Potvrdit heslo**: Potvrďte heslo certifikační autority prostředků infrastruktury.
    - **Certifikáty**: Pokud chcete k inicializaci certifikačního systému fabrice použít vlastní kořenové certifikáty, zvolte Možnost Odeslat kořenový certifikát pro certifikační autoritu prostředků fabric, jinak certifikační autorita aplikace Fabric vytvoří certifikáty podepsané svým držitelem.
    - **Kořenový certifikát**: Nahrajte kořenový certifikát (veřejný klíč), se kterým je třeba inicializovat certifikační autoritu prostředků infrastruktury. Certifikáty formátu .pem jsou podporovány, certifikáty by měly být platné v časovém pásmu UTC.
    - **Soukromý klíč kořenového certifikátu**: Nahrajte soukromý klíč kořenového certifikátu. Pokud máte certifikát .pem, který má veřejný i soukromý klíč v kombinaci, nahrajte jej i zde.


6. Vyberte kartu **Nastavení clusteru AKS** a definujte konfiguraci clusteru Azure Kubernetes, která je základní infrastrukturou, na které se budou nastavovat síťové součásti Fabric.

    ![Infrastruktura Hyperledger u šablony služby Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Zadejte následující podrobnosti:
    - **Název clusteru Kubernetes**: Název vytvořeného clusteru AKS. Toto pole je předem vyplněno na základě poskytnuté předpony zdroje, můžete ji v případě potřeby změnit.
    - **Kubernetes verze**: Verze Kubernetes, které budou nasazeny v clusteru. Na základě oblasti vybrané na kartě **Základy** se podporované verze, které jsou k dispozici, mohou změnit.
    - **Předpona DNS**: Předpona názvu DNS (Domain Name System) pro cluster AKS. Dns se při správě kontejnerů po vytvoření clusteru připojíte k rozhraní API Kubernetes.
    - **Velikost uzlu**: Velikost uzlu Kubernetes, můžete si vybrat ze seznamu skladové jednotky virtuálních her (SKU) dostupné v Azure. Pro optimální výkon doporučujeme Standard DS3 v2.
    - **Počet uzlů**: Počet počet uzlů Kubernetes, které mají být nasazeny v clusteru. Doporučujeme zachovat počet uzlů alespoň stejné nebo větší než počet uzlů HLF zadaný v nastavení prostředků prostředků.
    - **ID instančního objektu**služby : Zadejte ID klienta existujícího instančního objektu nebo vytvořte nový, který je vyžadován pro ověřování AKS. Postup vytvoření [instančního objektu .](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)
    - **Tajný klíč klienta instančního objektu**: Zadejte tajný klíč klienta instančního objektu uvedeného v ID instančního objektu klienta.
    - **Potvrdit tajný klíč klienta**: Potvrďte tajný klíč klienta poskytnutý v tajném klíči klienta hlavního klienta služby.
    - **Povolit monitorování kontejnerů**: Zvolte povolení monitorování AKS, které umožňuje protokolům AKS nabízení do zadaného pracovního prostoru Log Analytics.
    - **Pracovní prostor Log Analytics**: Pracovní prostor analýzy protokolů bude naplněn výchozím pracovním prostorem, který se vytvoří, pokud je monitorování povoleno.

8. Po zadání všech výše uvedených podrobností vyberte **Zkontrolovat a vytvořit** kartu. Kontrola a vytvoření aktivuje ověření pro hodnoty, které jste zadali.
9. Jakmile ověření projde, můžete vybrat **vytvořit**.
Nasazení obvykle trvá 10-12 minut, se může lišit v závislosti na velikosti a počtu uzlů AKS zadaných.
10. Po úspěšném nasazení budete upozorněni prostřednictvím oznámení Azure v pravém horním rohu.
11. Vyberte **Přejít do skupiny prostředků** a zkontrolujte všechny prostředky vytvořené jako součást nasazení šablony. Všechny názvy prostředků budou začínat předponou uvedenou v nastavení **Základy.**

## <a name="build-the-consortium"></a>Sestavení konsorcia

Chcete-li vytvořit příspěvek blockchainového konsorcia, který nasazuje službu řazení a uzly, musíte provést níže uvedené kroky v pořadí. **Vytvořte síťový** skript (byn.sh), který vám pomůže s nastavením konsorcia, vytvořením kanálu a instalací chaincode.

> [!NOTE]
> Sestavení sítě (byn) skript za předpokladu, je výhradně pro použití pro demo/devtest scénáře. Pro nastavení výrobní třídy doporučujeme použít nativní HLF API.

Všechny příkazy ke spuštění byn skript lze provést prostřednictvím Azure Bash rozhraní příkazového řádku (CLI). Do webové verze Azure shellu se můžete přihlásit prostřednictvím ![Infrastruktura Hyperledger u šablony služby Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) v pravém horním rohu portálu Azure. Na příkazovém řádku zadejte bash a zadejte přepnout na bash CLI.

Další informace najdete v [tématu Prostředí Azure.](https://docs.microsoft.com/azure/cloud-shell/overview)

![Infrastruktura Hyperledger u šablony služby Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Stáhněte si soubor byn.sh a fabric-admin.yaml.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Nastavte níže proměnné prostředí v prostředí Azure CLI Bash**:

Nastavení informací o kanálech a organizačních informací objednate

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Nastavení informací o partnerské organizaci

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Vytvořte jednu sdílenou složku Azure a sdílejte různé veřejné certifikáty mezi partnerskými a objednatevanými organizacemi.

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**Příkazy pro správu kanálů**

Přejít na řádovou organizaci Cluster AKS a příkaz vydání pro vytvoření nového kanálu

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Příkazy pro správu konsorcia**

Spusťte níže uvedené příkazy v daném pořadí přidat peer organizace v kanálu a konsorciu.

1. Přejděte do clusteru AKS peer organizace a nahrajte jeho poskytování služeb členů (MSP) na úložiště souborů Azure.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Přejděte do clusteru AKS organizace a přidejte partnerskou organizaci v kanálu a konsorciu.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Vraťte se do peer organizace a vydat příkaz pro připojení partnerských uzlů v kanálu.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Podobně chcete-li přidat další partnerské organizace v kanálu, aktualizujte peer aks proměnné prostředí podle požadované partnerské organizace a proveďte kroky 1 až 3.

**Příkazy pro správu kódu řetězce**

Proveďte níže uvedený příkaz k provedení operace související s řetězovým kódem. Tyto příkazy provádět všechny operace na demo chaincode. Tento demo chaincode má dvě proměnné "a" a "b". Při inkonkresi chaincode je "a" inicializováno s 1000 a "b" inicializováno s 2000. Při každém vyvolání kódu řetězu se 10 jednotek přenese z "a" na "b". Operace dotazu na chaincode zobrazuje světstav proměnné "a".

Proveďte následující příkazy spuštěné v clusteru AKS partnerské organizace.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Příkazy operace chaincode**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Spustit nativní operace HLF

Chcete-li zákazníkům pomoci začít s prováděním nativních příkazů Hyperledger v síti HLF v AKS. Ukázková aplikace je k dispozici, který používá fabric NodeJS SDK k provedení operací HLF. Příkazy jsou k dispozici k vytvoření nové identity uživatele a instalaci vlastního řetězového kódu.

### <a name="before-you-begin"></a>Než začnete

Postupujte podle následujících příkazů pro počáteční nastavení aplikace:

- Stažení souborů aplikací
- Generování profilu připojení a profilu správce
- Importovat identitu správce uživatele

Po dokončení počátečního nastavení můžete pomocí sady SDK dosáhnout níže uvedených operací:

- Generování identity uživatele
- Operace chaincode

Výše uvedené příkazy lze provést z Azure Cloud Shell.

### <a name="download-application-files"></a>Stažení souborů aplikací

První nastavení pro spuštění aplikace je stáhnout všechny soubory aplikace ve složce.

**Vytvořte složku aplikace a zadejte do ní**:

```bash
mkdir app
cd app
```
Spustit níže příkaz ke stažení všech požadovaných souborů a balíčků:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Tento příkaz trvá nějakou dobu načíst všechny balíčky. Po úspěšném spuštění příkazu `node_modules` můžete zobrazit složku v aktuálním adresáři. Všechny požadované balíčky jsou `node_modules` načteny ve složce.

### <a name="generate-connection-profile-and-admin-profile"></a>Generování profilu připojení a profilu správce

Vytvoření `profile` adresáře `app` uvnitř složky

```bash
cd app
mkdir ./profile
```
Nastavení těchto proměnných prostředí v cloudovém prostředí Azure

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Spustit pod příkazem pro generování profilu připojení a profilu správce organizace

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Vytvoří profil připojení a `profile` správce organizace ve složce `<orgname>-ccp.json` `<orgname>-admin.json` profilu s názvem a příslušně.

Podobně vygenerujte profil připojení a profil správce pro každou organizaci objednatela a partnera.


### <a name="import-admin-user-identity"></a>Importovat identitu správce uživatele

Posledním krokem je import identity správce organizace v peněžence.

```bash
npm run importAdmin -- -o <orgName>

```
Výše uvedený příkaz provede importAdmin.js importovat identitu uživatele správce do peněženky. Skript čte identitu správce z `<orgname>-admin.json` profilu správce a importuje ji do peněženky pro provádění operací HLF.

Skripty používají k ukládání identit peněženku systému souborů. Vytvoří peněženku podle cesty určené v poli ".wallet" v profilu připojení. Ve výchozím nastavení je pole ".wallet" inicializováno pomocí `<orgname>`, což znamená, že složka s názvem `<orgname>` je vytvořena v aktuálním adresáři pro uložení identit. Pokud chcete vytvořit peněženku na jiné cestě, upravte pole ".wallet" v profilu připojení před spuštěním zaregistrovat správce a další operace HLF.

Podobně importujte identitu uživatele správce pro každou organizaci.

Další podrobnosti o argumentech předanejch v příkazu naleznete v nápovědě příkazu.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Generování identity uživatele

Spouštějte níže uvedené příkazy v daném pořadí pro generování nových uživatelských identit pro organizaci HLF.

> [!NOTE]
> Před zahájením s kroky generování identity uživatele, ujistěte se, že počáteční nastavení aplikace je hotovo.

Nastavení pod proměnnými prostředí v prostředí Azure Cloud Shell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Registrace a registrace nového uživatele

Chcete-li zaregistrovat a zaregistrovat nového uživatele, proveďte níže uvedený příkaz, který provede registerUser.js. To šetří generované identity uživatele v peněžence.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> Identita uživatele správce se používá k vydání příkazu registrace pro nového uživatele. Proto je povinné mít identitu správce uživatele v peněžence před provedením tohoto příkazu. V opačném případě se tento příkaz nezdaří.

Další podrobnosti o argumentech předaná příkazem naleznete v nápovědě příkazu

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Operace chaincode


> [!NOTE]
> Před zahájením jakékoli operace chaincode se ujistěte, že je provedeno počáteční nastavení aplikace.

Nastavte níže proměnné prostředí specifické pro chaincode v prostředí Azure Cloud:

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

Níže uvedené operace řetězového kódu lze provádět:

- Instalace kódu řetězu
- Vytvoření instance řetězového kódu
- Vyvolat chaincode
- Kód řetězce dotazu

### <a name="install-chaincode"></a>Instalace kódu řetězu

Spusťte pod příkazem pro instalaci chaincode na peer organizace.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Nainstaluje chaincode na všechny partnerské uzly `ORGNAME` organizace nastavené v proměnné prostředí. Pokud jsou ve vašem kanálu dvě nebo více partnerských organizací a chcete nainstalovat kód řetězu na všechny z nich, spouštějte příkazy samostatně pro každou partnerskou organizaci.

Postupujte podle pokynů:

- Nastavit `ORGNAME` `<peerOrg1Name>` a `installCC` vydat příkaz.
- Nastavit `ORGNAME` `<peerOrg2Name>` a `installCC` vydat příkaz.

  Spusťte jej pro každou partnerskou organizaci.

Další podrobnosti o argumentech předanejch v příkazu naleznete v nápovědě příkazu.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Vytvoření instance řetězového kódu

Spusťte pod příkazem k vytvoření instance chaincode na druhé straně.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Předat název funkce instance a čárka oddělený `<instantiateFunc>` seznam `<instantiateFuncArgs>` argumentů v a příslušně. Například v [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go), chcete-li vytvořit `<instantiateFunc>` `"Init"` instanci kódu chaincode nastaveného na a `<instantiateFuncArgs>` prázdný řetězec `""`.

> [!NOTE]
> Spusťte příkaz pro jednou z jedné partnerské organizace v kanálu.
> Jakmile je transakce úspěšně odeslána objednatli, objednatce distribuuje tuto transakci všem partnerským organizacím v kanálu. Proto chaincode je vytvořena instance na všech uzlech peer na všech partnerských organizací v kanálu.

Další podrobnosti o argumentech předaná příkazem naleznete v nápovědě příkazu

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Vyvolat chaincode

Spusťte příkaz níže a vyvoláte funkci chaincode:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Pass invoke název funkce a čárka `<invokeFunction>` oddělený `<invokeFuncArgs>` seznam argumentů v a příslušně. Pokračování s fabcar chaincode příklad, vyvolat initLedger `"initLedger"` `<invokeFuncArgs>` funkce `""`nastavena `<invokeFunction>` na a na .

> [!NOTE]
> Spusťte příkaz pro jednou z jedné partnerské organizace v kanálu.
> Jakmile je transakce úspěšně odeslána objednatli, objednatce distribuuje tuto transakci všem partnerským organizacím v kanálu. Proto je světový stav aktualizován na všech uzlech peer všech partnerských organizací v kanálu.

Další podrobnosti o argumentech předaná příkazem naleznete v nápovědě příkazu

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Kód řetězce dotazu

Spustit pod příkazem pro dotaz chaincode:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Pass název funkce dotazu a čárka `<queryFunction>` `<queryFuncArgs>` oddělený seznam argumentů v a příslušně. Opět `fabcar` platí, že s chaincode jako odkaz, dotaz `<queryFunction>` `"queryAllCars"` na `<queryArgs>` `""`všechny vozy ve světě stavu nastavena na a .

Další podrobnosti o argumentech předaná příkazem naleznete v nápovědě příkazu

```bash
npm run queryCC -- -h

```
