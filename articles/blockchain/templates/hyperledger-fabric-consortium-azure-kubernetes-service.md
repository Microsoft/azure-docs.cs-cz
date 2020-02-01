---
title: Hlavní kniha prostředků infrastruktury pro službu Azure Kubernetes (AKS)
description: Jak nasadit a nakonfigurovat síť sdružení prostředků infrastruktury pro hlavní knihu ve službě Azure Kubernetes
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 93f78f2c8bc32a2012e5635e0daec10b8c51d167
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901626"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Hlavní kniha prostředků infrastruktury pro službu Azure Kubernetes (AKS)

K nasazení a konfiguraci sítě konsorcia prostředků infrastruktury v Azure můžete použít šablonu HLF (hlavní kniha prostředků infrastruktury) na šabloně Azure Kubernetes Service (AKS).

Po přečtení tohoto článku:

- Získejte pracovní znalosti prostředků infrastruktury hlavní knihy a různých komponent, které tvoří stavební kameny blockchain sítě hlavní knihy.
- Naučte se, jak nasadit a nakonfigurovat konsorcium prostředků infrastruktury hlavní knihy ve službě Azure Kubernetes pro produkční scénáře.

## <a name="hyperledger-fabric-consortium-architecture"></a>Architektura konsorcia infrastruktury pro hlavní kniha

Pokud chcete vytvořit síť prostředků infrastruktury hlavní knihy v Azure, je potřeba nasadit službu řazení a organizaci s partnerskými uzly. Různé základní komponenty, které jsou vytvořeny jako součást nasazení šablony, jsou:

- **Uzly**: uzel, který je zodpovědný za řazení transakce v hlavní knize. Společně s ostatními uzly tvoří seřazené uzly službu řazení pro síť prostředků infrastruktury hlavní knihy.

- **Partnerské uzly**: uzel, který primárně hostuje hlavní knihy a inteligentní kontrakty, tyto základní prvky sítě.

- **CA Fabric**: Fabric je certifikační autorita (CA) pro prostředky infrastruktury hlavní knihy. Certifikační autorita prostředků infrastruktury umožňuje inicializovat a spustit proces serveru, který je hostitelem certifikační autority. Umožňuje správu identit a certifikátů. Každý cluster AKS nasazený jako součást šablony bude mít ve výchozím nastavení certifikační autoritu infrastruktury pod.

- **CouchDB nebo LevelDB**: databáze státních stavů pro rovnocenné uzly může být uložena v LevelDB nebo CouchDB. LevelDB je výchozí databáze stavu vložená v partnerském uzlu a ukládá data chaincode jako jednoduché páry klíč-hodnota a podporuje jenom klíč, rozsah klíčů a dotazy složeného klíče. CouchDB je volitelná alternativní databáze stavu, která podporuje formátované dotazy, když jsou datové hodnoty chaincode modelované jako JSON.

Šablona v nasazení roztočí různé prostředky Azure v rámci vašeho předplatného. Mezi nasazené různé prostředky Azure patří:

- **Cluster AKS**: cluster Azure Kubernetes, který je nakonfigurovaný podle vstupních parametrů poskytovaných zákazníkem. Cluster AKS má různé lusky nakonfigurované pro spouštění síťových komponent prostředků infrastruktury hlavní knihy. Vytvoří se různé lusky:

  - **Fabric Tools**: nástroj Fabric zodpovídá za konfiguraci komponent prostředků infrastruktury hlavní knihy.
  - **Lusky/protějšky**: uzly sítě HLF.
  - **Proxy**: proxy server ngnix pod tím, přes který mohou klientské aplikace používat rozhraní s clusterem AKS.
  - **CA infrastruktury Fabric**: na pod, kde je SPUŠTĚNÁ certifikační autorita infrastruktury.
- **PostgreSQL**: instance PostgreSQL je nasazená za účelem zachování identit certifikační autority infrastruktury.

- **Azure Key trezor**: instance trezoru klíčů je nasazená za účelem uložení přihlašovacích údajů certifikační autority infrastruktury a kořenových certifikátů poskytovaných zákazníkem, který se používá v případě opakování nasazení šablony. je to zpracování mechanismu šablony.
- **Spravovaný disk Azure**: Azure Managed disk pro hlavní knihu a databázi státních stavů partnerského uzlu jsou pro trvalé úložiště.
- **Veřejná IP adresa**: koncový bod veřejné IP adresy clusteru AKS nasazený pro propojení s clusterem.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Nastavení sítě blockchain infrastruktury pro hlavní knihu

Začněte tím, že budete potřebovat předplatné Azure, které může podporovat nasazení několika virtuálních počítačů a standardních účtů úložiště. Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/).

Nastavte blockchain síť prostředků infrastruktury hlavní knihy pomocí následujících kroků:

- [Nasazení organizace v řádu nebo v partnerském vztahu](#deploy-the-ordererpeer-organization)
- [Sestavení konsorcia](#build-the-consortium)
- [Spouštění nativních operací HLF](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Nasazení organizace v řádu nebo v partnerském vztahu

Pokud chcete začít s nasazením síťových součástí HLF, přejděte na [Azure Portal](https://portal.azure.com). Vyberte **vytvořit prostředek > Blockchain** > Hledat v prostředcích **infrastruktury hlavní knihy ve službě Azure Kubernetes**.

1. Vyberte **vytvořit** a spusťte tak nasazení šablony. Zobrazí se zobrazení **vytvoření prostředků infrastruktury hlavní knihy ve službě Azure Kubernetes** .

    ![Šablona prostředků infrastruktury hlavní knihy v Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Na stránce **základy** zadejte podrobnosti projektu.

    ![Šablona prostředků infrastruktury hlavní knihy v Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Zadejte následující podrobnosti:
    - **Předplatné**: vyberte název předplatného, ve kterém chcete nasadit součásti sítě HLF.
    - **Skupina prostředků**: buď vytvořte novou skupinu prostředků, nebo vyberte existující prázdnou skupinu prostředků, skupina prostředků bude obsahovat všechny prostředky nasazené v rámci šablony.
    - **Oblast**: Vyberte oblast Azure, ve které chcete nasadit cluster Azure Kubernetes pro součásti HLF. Tato šablona je dostupná ve všech oblastech, kde je AKS k dispozici, zajistěte si výběr oblasti, ve které vaše předplatné nedosahuje limitu kvóty virtuálních počítačů.
    - **Předpona prostředku**: předpona pro pojmenování nasazených prostředků. Předpona prostředku musí být kratší než šest znaků a kombinace znaků musí zahrnovat číslice i písmena.
4. Vyberte kartu **nastavení prostředků infrastruktury** a definujte součásti sítě HLF, které budou nasazeny.

    ![Šablona prostředků infrastruktury hlavní knihy v Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Zadejte následující podrobnosti:
    - **Název organizace**: název organizace prostředků infrastruktury, která se vyžaduje pro různé operace roviny dat.
    - **Součást sítě prostředků infrastruktury**: vyberte buď řazení služby nebo partnerské uzly na základě součásti sítě blockchain, kterou chcete nastavit.
    - **Počet uzlů** – následující dva typy uzlů:
        - Služba objednávání – vyberte počet uzlů pro zajištění odolnosti proti chybám v síti. Podporovaným počtem uzlů pro pořadí je jenom 3, 5 a 7.
        - Partnerské uzly – na základě vašeho požadavku můžete vybrat 1-10 uzlů.
    - **Databáze stavu partnerského uzlu na světě**: Vyberte mezi LevelDB a CoucbDB. Toto pole se zobrazí, když uživatel zvolí uzel peer v rozevíracím seznamu součást sítě Fabric.
    - **Uživatelské jméno prostředků infrastruktury**: zadejte uživatelské jméno, které se používá pro ověřování certifikační autority infrastruktury.
    - **Heslo certifikační autority prostředků infrastruktury**: zadejte heslo pro ověřování certifikační autority infrastruktury.
    - **Potvrzení hesla**: potvrďte heslo certifikační autority infrastruktury.
    - **Certifikáty**: Pokud chcete k inicializaci CA prostředků infrastruktury použít vlastní kořenové certifikáty, pak zvolte možnost nahrát kořenový certifikát pro certifikační autoritu infrastruktury, jinak ve výchozím nastavení certifikační autorita infrastruktury vytvoří certifikáty podepsané svým držitelem.
    - **Kořenový certifikát**: Nahrajte kořenový certifikát (veřejný klíč), ve kterém se musí inicializovat certifikační autorita infrastruktury. Certifikáty ve formátu. pem jsou podporovány, certifikáty by měly být platné v časovém pásmu UTC.
    - **Privátní klíč kořenového certifikátu**: Nahrajte privátní klíč kořenového certifikátu. Pokud máte certifikát. pem, který má kombinaci veřejného i privátního klíče, nahrajte ho i tady.


6. Výběrem karty **Nastavení clusteru AKS** definujte konfiguraci clusteru Azure Kubernetes, která je základní infrastrukturou, na které se budou nastavovat síťové součásti prostředků infrastruktury.

    ![Šablona prostředků infrastruktury hlavní knihy v Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Zadejte následující podrobnosti:
    - **Kubernetes clusteru název**: název clusteru AKS, který se vytvoří. Toto pole je předem vyplněné na základě zadané předpony prostředku, můžete v případě potřeby změnit.
    - **Verze Kubernetes**: verze Kubernetes, která bude nasazena v clusteru. V závislosti na oblasti vybrané na kartě **základy** se můžou dostupné podporované verze změnit.
    - **Předpona DNS**: Předpona názvu DNS (Domain Name System) pro cluster AKS. Pomocí DNS se připojíte k rozhraní Kubernetes API při správě kontejnerů po vytvoření clusteru.
    - **Velikost uzlu**: velikost uzlu Kubernetes můžete vybrat ze seznamu skladových jednotek virtuálních počítačů (SKU) dostupných v Azure. Pro zajištění optimálního výkonu doporučujeme standardní DS3 v2.
    - **Počet uzlů**: počet uzlů Kubernetes, které mají být nasazeny v clusteru. Doporučujeme, aby byl tento počet uzlů minimálně rovný nebo větší než počet uzlů HLF zadaných v nastavení prostředků infrastruktury.
    - **ID klienta instančního objektu**: Zadejte ID klienta existujícího instančního objektu nebo vytvořte nový, který se vyžaduje pro ověřování AKS. Další informace najdete v tématu Postup [Vytvoření instančního objektu](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Tajný kód klienta instančního objektu**: zadejte tajný klíč klienta instančního objektu, který je k dispozici v ID klienta instančního objektu.
    - **Potvrzení tajného klíče klienta**: potvrďte tajný klíč klienta, který je k dispozici v tajnosti klienta instančního objektu
    - **Povolit monitorování kontejnerů**: tuto možnost vyberte, pokud chcete povolit monitorování AKS, což umožňuje, aby protokoly AKS předalo do zadaného pracovního prostoru Log Analytics.
    - **Log Analytics pracovní prostor**: pracovní prostor Log Analytics se naplní výchozím pracovním prostorem, který se vytvoří, pokud je povolené monitorování.

8. Po zadání všech výše uvedených podrobností vyberte možnost **zkontrolovat a vytvořit** kartu. Kontrola a vytvoření aktivuje ověření pro hodnoty, které jste zadali.
9. Po úspěšném ověření můžete vybrat **vytvořit**.
Nasazení obvykle trvá 10-12 minut, se může lišit v závislosti na velikosti a počtu zadaných uzlů AKS.
10. Po úspěšném nasazení budete upozorňováni prostřednictvím oznámení Azure v pravém horním rohu.
11. Vyberte **Přejít do skupiny prostředků** a ověřte, jestli se všechny prostředky vytvořily v rámci nasazení šablony. Všechny názvy prostředků budou začínat předponou poskytnutou v nastavení **základů** .

## <a name="build-the-consortium"></a>Sestavení konsorcia

Chcete-li vytvořit blockchain Consortium po nasazení služby řazení a partnerských uzlů, je nutné provést následující kroky v pořadí. **Sestavte síťový** skript (Byn.sh), který vám pomůže nastavit konsorcium, vytvořit kanál a nainstalovat chaincode.

> [!NOTE]
> Sestavování vašeho síťového skriptu (Byn) je výhradně k použití pro scénáře demo/DevTest. Pro nastavení produkčních stupňů doporučujeme používat nativní rozhraní HLF API.

Všechny příkazy ke spuštění skriptu Byn můžete provádět prostřednictvím rozhraní příkazového řádku (CLI) Azure bash. K webové verzi prostředí Azure Shell se můžete přihlásit prostřednictvím ![Šablona prostředků infrastruktury hlavní knihy v Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) možnost v pravém horním rohu Azure Portal. Do příkazového řádku zadejte bash a přejděte do bash CLI.

Další informace najdete v tématu [Azure Shell](https://docs.microsoft.com/azure/cloud-shell/overview) .

![Šablona prostředků infrastruktury hlavní knihy v Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Stáhněte si soubor byn.sh a Fabric-admin. yaml.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**V prostředí Azure CLI bash shell nastavte níže uvedené proměnné prostředí**:

Nastavení informací o kanálu a informací o organizaci v pořadí

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Nastavit informace o partnerské organizaci

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Vytvořte jednu sdílenou složku Azure pro sdílení různých veřejných certifikátů mezi partnerskými organizacemi a mezi nimi.

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
**Příkazy správy kanálů**

Pokud chcete vytvořit nový kanál, přejít na AKS cluster a příkaz pro změnu pořadí organizace

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Příkazy správy konsorcia**

V uvedeném pořadí proveďte níže uvedené příkazy pro přidání partnerské organizace do kanálu a konsorcia.

1. Přejít na cluster AKS peer Organization a nahrajte jeho poskytování členské služby (MSP) na File Storage Azure.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Přejít na cluster AKS organizace pro objednávky a přidat partnerské organizace v kanálu a v konsorciu

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Pokud se chcete připojit k uzlům rovnocenného uzlu v kanálu, vraťte se k partnerské organizaci a vydání příkazu.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Podobně pro přidání dalších partnerských organizací do kanálu aktualizujte proměnné prostředí peer AKS podle požadované partnerské organizace a proveďte kroky 1 až 3.

**Příkazy správy Chaincode**

Spusťte následující příkaz, který provede operaci související s chaincode. Tyto příkazy provádějí všechny operace v ukázce chaincode. Tato ukázková chaincode má dvě proměnné "a" a "b". Při vytváření instance chaincode je "a" inicializována s 1000 a "b" je inicializována s 2000. Při každém vyvolání chaincode je 10 jednotek převedeno z "a" na "b". Operace dotazu na chaincode zobrazuje světový stav proměnné "a".

Spusťte následující příkazy spuštěné v clusteru AKS organizace partnera.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Příkazy operace Chaincode**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Spouštění nativních operací HLF

Abychom zákazníkům pomohla začít s prováděním nativních příkazů hlavní knihy v HLF síti v AKS. Ukázková aplikace je k dispozici, aby k provádění operací HLF používala NodeJS SDK pro Fabric. K dispozici jsou tyto příkazy pro vytvoření nové identity uživatele a instalaci vlastních chaincode.

### <a name="before-you-begin"></a>Než začnete

Při počátečním nastavení aplikace postupujte podle následujících příkazů:

- Stažení souborů aplikace
- Vygenerovat profil připojení a profil správce
- Importovat identitu uživatele správce

Po dokončení počátečního nastavení můžete pomocí sady SDK dosáhnout níže uvedených operací:

- Generování identity uživatele
- Operace Chaincode

Výše uvedené příkazy lze spustit z Azure Cloud Shell.

### <a name="download-application-files"></a>Stažení souborů aplikace

Prvním nastavením pro spuštěnou aplikaci je stažení všech souborů aplikace do složky.

**Vytvořte složku aplikace a zadejte ji do složky**:

```bash
mkdir app
cd app
```
Spuštěním následujícího příkazu stáhnete všechny požadované soubory a balíčky:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Načtení všech balíčků v tomto příkazu trvá déle. Po úspěšném provedení příkazu uvidíte složku `node_modules` v aktuálním adresáři. Všechny požadované balíčky jsou načteny do složky `node_modules`.

### <a name="generate-connection-profile-and-admin-profile"></a>Vygenerovat profil připojení a profil správce

Vytvoření adresáře `profile` v rámci `app` složky

```bash
cd app
mkdir ./profile
```
Nastavení těchto proměnných prostředí ve službě Azure Cloud Shell

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Spustit níže uvedeným příkazem vygenerujete profil připojení a profil správce organizace.

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Vytvoří profil připojení a správce `profile` organizace ve složce profilu s názvem `<orgname>-ccp.json` a `<orgname>-admin.json`.

Podobně vygenerujte profil připojení a profil správce pro každého z objednávek a partnerských organizací.


### <a name="import-admin-user-identity"></a>Importovat identitu uživatele správce

Posledním krokem je naimportování identity uživatele správce organizace do peněženky.

```bash
npm run importAdmin -- -o <orgName>

```
Výše uvedený příkaz spustí importAdmin. js k importu identity uživatele správce do peněženky. Skript přečte identitu správce z profilu správce `<orgname>-admin.json` a naimportuje je do peněženky pro provádění operací HLF.

Skripty používají k ukládání identit kapesní systém souborů. V profilu připojení se vytvoří kapesní na základě cesty zadané v poli ". peněženka". Ve výchozím nastavení se pole ". peněženka" inicializuje s `<orgname>`, což znamená, že se v aktuálním adresáři vytvoří složka s názvem `<orgname>` pro uložení identit. Pokud chcete vytvořit peněženku na nějaké jiné cestě, před spuštěním možnosti registrovat uživatele správce a jakékoli jiné operace HLF upravte v profilu připojení pole "kapesní".

Podobně importujte identitu uživatele správce pro každou organizaci.

Další informace o argumentech předaných v příkazu najdete v nápovědě k příkazům.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Generování identity uživatele

Vygenerujte nové identity uživatelů pro HLF organizaci pod příkazy provedenými níže v daném pořadí.

> [!NOTE]
> Než začnete s kroky generování identity uživatele, zajistěte, aby se počáteční nastavení aplikace dokončilo.

Nastavení níže uvedených proměnných prostředí ve službě Azure Cloud Shell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Registrace a registrace nového uživatele

Chcete-li zaregistrovat a zapsat nového uživatele, spusťte následující příkaz, který spustí registerUser. js. Uloží vygenerovanou identitu uživatele v kapesním okně.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> Identita uživatele správce se používá k vydání příkazu Register pro nového uživatele. Proto je před provedením tohoto příkazu nutné mít identitu uživatele správce v kapesním prostředí. V opačném případě se tento příkaz nezdaří.

Další podrobnosti o argumentech předaných příkazem najdete v nápovědě k příkazům.

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Operace Chaincode


> [!NOTE]
> Před zahájením jakékoli operace chaincode zajistěte, aby se počáteční nastavení aplikace dokončilo.

V Azure Cloud shellu nastavte pod chaincode konkrétní proměnné prostředí:

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

Níže uvedené operace chaincode lze provést:

- Nainstalovat chaincode
- Vytvoření instance chaincode
- Vyvolat chaincode
- Chaincode dotazu

### <a name="install-chaincode"></a>Nainstalovat chaincode

Spusťte následující příkaz pro instalaci chaincode v partnerské organizaci.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Nainstaluje chaincode do všech partnerských uzlů sady organizace v sadě `ORGNAME` proměnné prostředí. Pokud máte ve vašem kanálu dvě nebo více partnerských organizací a chcete na všechny z nich nainstalovat chaincode, spusťte příkazy samostatně pro každou organizaci v partnerském vztahu.

Postupujte podle následujících kroků:

- Nastavte `ORGNAME` na `<peerOrg1Name>` a vydejte `installCC` příkaz.
- Nastavte `ORGNAME` na `<peerOrg2Name>` a vydejte `installCC` příkaz.

  Proveďte pro každou z partnerských organizací.

Další informace o argumentech předaných v příkazu najdete v nápovědě k příkazům.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Vytvoření instance chaincode

Spuštěním následujícího příkazu vytvořte instanci chaincode na partnerském uzlu.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Název funkce instance a seznam argumentů, které jsou v `<instantiateFunc>` a `<instantiateFuncArgs>` odděleny čárkou. Například v [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)pro vytvoření instance sady chaincode `<instantiateFunc>` `"Init"` a `<instantiateFuncArgs>` k prázdnému řetězci `""`.

> [!NOTE]
> Spusťte příkaz pro jednu z libovolných partnerských organizací v kanálu.
> Po úspěšném odeslání transakce do objednávky bude objednávka distribuovat tuto transakci do všech partnerských organizací v kanálu. Proto je instance chaincode vytvořena na všech partnerských uzlech všech partnerských organizací v kanálu.

Další podrobnosti o argumentech předaných příkazem najdete v nápovědě k příkazům.

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Vyvolat chaincode

Spusťte následující příkaz, který vyvolá funkci chaincode:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Předejte vyvolat název funkce a seznam argumentů oddělených čárkou v `<invokeFunction>` a `<invokeFuncArgs>` v uvedeném pořadí. Pokračování v příkladu fabcar chaincode pro vyvolání sady funkcí initLedger `<invokeFunction>` `"initLedger"` a `<invokeFuncArgs>` na `""`.

> [!NOTE]
> Spusťte příkaz pro jednu z libovolných partnerských organizací v kanálu.
> Po úspěšném odeslání transakce do objednávky bude objednávka distribuovat tuto transakci do všech partnerských organizací v kanálu. Proto je celosvětový stav aktualizován na všech partnerských uzlech všech partnerských organizací v kanálu.

Další podrobnosti o argumentech předaných příkazem najdete v nápovědě k příkazům.

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Chaincode dotazu

Příkaz spustit pod příkazem pro dotaz na chaincode:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Zadejte název funkce dotazu a čárkami oddělený seznam argumentů v `<queryFunction>` a `<queryFuncArgs>`. Znovu se postará o `fabcar` chaincode, aby se dotazoval na všechna vozidla v celém světě nastaveném `<queryFunction>` na `"queryAllCars"` a `<queryArgs>` `""`.

Další podrobnosti o argumentech předaných příkazem najdete v nápovědě k příkazům.

```bash
npm run queryCC -- -h

```
