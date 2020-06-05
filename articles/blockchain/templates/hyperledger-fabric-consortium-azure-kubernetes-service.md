---
title: Hlavní kniha prostředků infrastruktury pro službu Azure Kubernetes (AKS)
description: Jak nasadit a nakonfigurovat síť sdružení prostředků infrastruktury pro hlavní knihu ve službě Azure Kubernetes
ms.date: 06/04/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: 98d89905c89156d05fd61389693ad8d5765ba9e1
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434339"
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

- **Azure Key trezor**: instance trezoru klíčů je nasazená za účelem uložení přihlašovacích údajů certifikační autority infrastruktury a kořenových certifikátů poskytovaných zákazníkem, který se používá v případě opakování nasazení šablony pro zpracování mechanismu šablony.
- **Spravovaný disk Azure**: Azure Managed disk pro hlavní knihu a databázi státních stavů partnerského uzlu jsou pro trvalé úložiště.
- **Veřejná IP adresa**: koncový bod veřejné IP adresy clusteru AKS nasazený pro propojení s clusterem.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Nastavení sítě blockchain infrastruktury pro hlavní knihu

Začněte tím, že budete potřebovat předplatné Azure, které může podporovat nasazení několika virtuálních počítačů a standardních účtů úložiště. Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/).

Nastavte blockchain síť prostředků infrastruktury hlavní knihy pomocí následujících kroků:

- [Nasazení organizace v řádu nebo v partnerském vztahu](#deploy-the-ordererpeer-organization)
- [Sestavení konsorcia](#build-the-consortium)

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
    - **Název organizace**: název organizace prostředků infrastruktury, která se vyžaduje pro různé operace roviny dat. Název organizace musí být pro každé nasazení jedinečný.
    - **Součást sítě prostředků infrastruktury**: vyberte buď řazení služby nebo partnerské uzly na základě součásti sítě blockchain, kterou chcete nastavit.
    - **Počet uzlů** – následující dva typy uzlů:
        - Služba objednávání – vyberte počet uzlů pro zajištění odolnosti proti chybám v síti. Podporovaným počtem uzlů pro pořadí je jenom 3, 5 a 7.
        - Partnerské uzly – na základě vašeho požadavku můžete vybrat 1-10 uzlů.
    - **Databáze stavu partnerského uzlu na světě**: Vyberte mezi LevelDB a CoucbDB. Toto pole se zobrazí, když uživatel zvolí uzel peer v rozevíracím seznamu součást sítě Fabric.
    - **Uživatelské jméno prostředků infrastruktury**: zadejte uživatelské jméno, které se používá pro ověřování certifikační autority infrastruktury.
    - **Heslo certifikační autority prostředků infrastruktury**: zadejte heslo pro ověřování certifikační autority infrastruktury.
    - **Potvrzení hesla**: potvrďte heslo certifikační autority infrastruktury.
    - **Certifikáty**: Pokud chcete k inicializaci certifikační autority prostředků infrastruktury použít vlastní kořenové certifikáty, zvolte možnost nahrát kořenový certifikát pro certifikační autoritu infrastruktury, jinak ve výchozím nastavení certifikační autorita infrastruktury vytvoří certifikáty podepsané svým držitelem.
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

Chcete-li vytvořit blockchain Consortium po nasazení služby řazení a partnerských uzlů, je nutné provést následující kroky v pořadí. Skript Azure HLF (azhlf), který vám pomůže s nastavením konsorcia, vytváření kanálů a chaincode operací.

> [!NOTE]
> Ve skriptu se nachází aktualizace. Tato aktualizace má poskytnout více funkcí skriptu Azure HLF. Pokud chcete odkazovat na starý skript, [Přečtěte si zde](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Tento skript je kompatibilní s prostředky infrastruktury hlavní knihy ve službě Azure Kubernetes Service Template verze 2.0.0 a vyšší. Chcete-li zjistit verzi nasazení, postupujte podle kroků v tématu [řešení potíží](#troubleshoot).

> [!NOTE]
> K dispozici jste skript Azure HLF (azhlf), který vám pomůžeme jenom o scénářích demo/DevTest. Kanál a konsorcium vytvořené tímto skriptem mají základní zásady HLF, které zjednodušují scénář demo/DevTest. V případě produkčního nastavení doporučujeme aktualizovat zásady kanálu/konsorcia HLF v souladu s požadavky vaší organizace na dodržování předpisů pomocí nativních rozhraní HLF API.


Všechny příkazy ke spuštění skriptu Azure HLF můžete provést prostřednictvím příkazového řádku Azure bash. Rozhraní (CLI). K webové verzi prostředí Azure Shell se můžete přihlásit prostřednictvím  ![Šablona prostředků infrastruktury hlavní knihy v Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) možnost v pravém horním rohu Azure Portal. Do příkazového řádku zadejte bash a přejděte do bash CLI.

Další informace najdete v tématu [Azure Shell](https://docs.microsoft.com/azure/cloud-shell/overview) .

![Šablona prostředků infrastruktury hlavní knihy v Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Následující obrázek ukazuje podrobný postup pro sestavování konsorcia mezi organizací a rovnocennou organizací. Podrobné příkazy pro provedení těchto kroků jsou zachyceny v následujících částech.

![Šablona prostředků infrastruktury hlavní knihy v Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Při počátečním nastavení klientské aplikace postupujte podle následujících příkazů: 

1.  [Stáhnout klientské soubory aplikace](#download-client-application-files)
2.  [Nastavení proměnných prostředí](#setup-environment-variables)
3.  [Importovat profil připojení k organizaci, uživatele s oprávněními správce a MSP](#import-organization-connection-profile-admin-user-identity-and-msp)

Po dokončení počátečního nastavení můžete pomocí klientské aplikace dosáhnout níže uvedených operací:  

- [Příkazy správy kanálů](#channel-management-commands)
- [Příkazy správy konsorcia](#consortium-management-commands)
- [Příkazy správy Chaincode](#chaincode-management-commands)

### <a name="download-client-application-files"></a>Stáhnout klientské soubory aplikace

Prvním instalačním programem je stažení souborů klientské aplikace. Spuštěním následujícího příkazu stáhnete všechny požadované soubory a balíčky:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup

```
Tyto příkazy naklonují kód klientské aplikace Azure HLF z veřejného úložiště GitHub následovaný načtením všech závislých balíčků npm. Po úspěšném provedení příkazu uvidíte složku node_modules v aktuálním adresáři. Všechny požadované balíčky jsou načteny do složky node_modules.


### <a name="setup-environment-variables"></a>Nastavení proměnných prostředí

> [!NOTE]
> Všechny proměnné prostředí následují po konvenci vytváření názvů prostředků Azure.


**Nastavte níže uvedené proměnné prostředí pro klienta organizace v objednávce.**


```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```
**Nastavte níže uvedené proměnné prostředí pro klienta partnerské organizace.**

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> Na základě počtu partnerských organizace ve vaší konsorciu může být nutné opakovat rovnocenné příkazy a odpovídajícím způsobem nastavit proměnnou prostředí.

**Nastavte následující proměnné prostředí pro nastavení Azure Storage účtu.**

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Při vytváření účtu Azure Storage postupujte podle následujících kroků. Pokud už máte vytvořený účet Azure Storage, přeskočte tyto kroky.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Při vytváření sdílené složky v účtu Azure Storage postupujte podle následujících kroků. Pokud už máte vytvořenou sdílenou složku, přeskočte tyto kroky.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Postup generování připojovacího řetězce sdílené složky Azure podle následujících kroků

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>Importovat profil připojení k organizaci, identitu uživatele správce a MSP

Níže uvedené příkazy načítají profil připojení organizace, identitu uživatele správce a MSP z clusteru Azure Kubernetes a ukládají tyto identity v místním úložišti klientské aplikace, tj. v adresáři azhlfTool/Stores.

Pro organizaci v řádu Order:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Pro organizaci v partnerském vztahu:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="channel-management-commands"></a>Příkazy správy kanálů

> [!NOTE]
> Než začnete s jakoukoli operací kanálu, ujistěte se, že je původní nastavení klientské aplikace hotové.  

Následují dva příkazy správy kanálů:

1. [Vytvořit kanál – příkaz](#create-channel-command)
2. [Nastavení příkazu kotvních partnerských uzlů](#setting-anchor-peers-command)


#### <a name="create-channel-command"></a>Vytvořit kanál – příkaz

Z klienta organizace s objednávkou, vystavení příkazu pro vytvoření nového kanálu. Tento příkaz vytvoří kanál, který v něm má pouze organizaci s přířazením.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

#### <a name="setting-anchor-peers-command"></a>Nastavení příkazu kotvních partnerských uzlů
Z klienta partnerské organizace, vystavení níže příkaz pro nastavení partnerských partnerských vztahů pro organizaci partnerského vztahu na zadaném kanálu.

>[!NOTE]
> Před spuštěním tohoto příkazu zajistěte, aby se v kanálu přidala partnerská organizace pomocí příkazů pro správu konsorcia.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY
```

`<anchorPeersList>`je seznam uzlů oddělený mezerou, který se má nastavit jako kotvicí partner. Třeba

  - Nastavte `<anchorPeersList>` jako "peer1", pokud chcete nastavit pouze uzel peer1 jako kotvicího partnera.
  - Nastavte `<anchorPeersList>` jako "peer1" "peer3", pokud chcete jako kotvový partner nastavit uzel peer1 i peer3.

### <a name="consortium-management-commands"></a>Příkazy správy konsorcia

>[!NOTE]
> Před zahájením jakékoli operace konsorcia se ujistěte, že je provedena počáteční instalace klientské aplikace.  

V uvedeném pořadí proveďte následující příkazy, aby se do kanálu a konsorcia přidala organizace typu peer.
1.  Z klienta partnerské organizace nahrajte partnerský subjekt MSP na Azure Storage

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Z klienta organizace pro objednávky Stáhněte si z Azure Storage partnerských organizací MSP a potom vydejte příkaz pro přidání partnerské organizace do kanálu nebo konsorcia.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Z klienta organizace pro objednávky nahrajte profil připojení pro objednávku na Azure Storage tak, aby se partnerské organizace mohla připojit k uzlům s použitím tohoto profilu připojení.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Z klienta rovnocenné organizace stáhněte profil připojení k nástroji pro změnu pořadí z Azure Storage a potom vydejte příkaz pro přidání partnerských uzlů do kanálu.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Podobně pro přidání dalších partnerských organizací do kanálu aktualizujte proměnné partnerského prostředí podle požadované partnerské organizace a proveďte kroky 1 až 4.


### <a name="chaincode-management-commands"></a>Příkazy správy Chaincode

>[!NOTE]
> Před zahájením jakékoli operace chaincode zajistěte, aby byla provedena počáteční instalace klientské aplikace.  

**Nastavení níže určených proměnných prostředí chaincode**

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

Níže uvedené operace chaincode lze provést:  

- [Nainstalovat chaincode](#install-chaincode)  
- [Vytvoření instance chaincode](#instantiate-chaincode)  
- [Vyvolat chaincode](#invoke-chaincode)
- [Chaincode dotazu](#query-chaincode)


### <a name="install-chaincode"></a>Nainstalovat chaincode  

Spusťte následující příkaz pro instalaci chaincode v partnerské organizaci.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Nainstaluje chaincode do všech partnerských uzlů nastavených v proměnné prostředí ORGNAME v partnerském uzlu. Pokud máte ve vašem kanálu dvě nebo více partnerských organizací a chcete na všechny z nich nainstalovat chaincode, spusťte tento příkaz samostatně pro každou organizaci partnera.  

Postupujte podle následujících kroků:  

1.  Nastavte `ORGNAME` a `USER_IDENTITY` jako na peerOrg1 a problémový `./azhlf chaincode install` příkaz.  
2.  Nastavte `ORGNAME` a `USER_IDENTITY` jako na peerOrg2 a problémový `./azhlf chaincode install` příkaz.  

### <a name="instantiate-chaincode"></a>Vytvoření instance chaincode  

Z klientské aplikace peere spusťte pod příkazem vytvoření instance chaincode na kanálu.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```
Název funkce instance a seznam argumentů, které jsou odděleny mezerou, v `<instantiateFunc>` a v `<instantiateFuncArgs>` uvedeném pořadí. Například v chaincode_example02. přejít chaincode, pokud chcete vytvořit instanci chaincode sady `<instantiateFunc>` na `init` a `<instantiateFuncArgs>` na "a" "2000" "b" "1000".

> [!NOTE]
> Spusťte příkaz pro jednu z libovolných partnerských organizací v kanálu. Po úspěšném odeslání transakce do objednávky bude objednávka distribuovat tuto transakci do všech partnerských organizací v kanálu. Proto je instance chaincode vytvořena na všech partnerských uzlech všech partnerských organizací v kanálu.  


### <a name="invoke-chaincode"></a>Vyvolat chaincode  

Z klienta partnerské organizace spusťte následující příkaz, který vyvolá funkci chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Předejte vyvolat název funkce a seznam argumentů oddělených mezerou v v  `<invokeFunction>`    `<invokeFuncArgs>`   uvedeném pořadí. Pokračování s chaincode_example02. přejít chaincode, aby se provedla operace vyvolání sady  `<invokeFunction>`   na  `invoke`   a  `<invokeFuncArgs>`   na "a" b "" 10 ".  

>[!NOTE]
> Spusťte příkaz pro jednu z libovolných partnerských organizací v kanálu. Po úspěšném odeslání transakce do objednávky bude objednávka distribuovat tuto transakci do všech partnerských organizací v kanálu. Proto je celosvětový stav aktualizován na všech partnerských uzlech všech partnerských organizací v kanálu.  


### <a name="query-chaincode"></a>Chaincode dotazu  

Příkaz spustit pod příkazem pro dotaz na chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
Předejte název funkce dotazu a seznam argumentů oddělených mezerami v  `<queryFunction>`    `<queryFuncArgs>`   uvedeném pořadí. Znovu se postará o chaincode_example02. přejít chaincode jako na odkaz a na hodnotu dotazu "a" ve světě nastavenou  `<queryFunction>`   na  `query` a  `<queryArgs>` na "a".  

## <a name="troubleshoot"></a>Řešení potíží

**Ověření verze spuštěné šablony**

Spusťte následující příkazy a vyhledejte verzi nasazení šablony.

Nastavte níže uvedené proměnné prostředí podle skupiny prostředků, ve které byla šablona nasazena.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
Spusťte následující příkaz pro tisk verze šablony.
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Novinky ke službě Azure blockchain News najdete na [blogu Azure blockchain](https://azure.microsoft.com/blog/topics/blockchain/) , abyste měli přehled o nabídkách služeb blockchain a informacích od týmu Azure blockchain Engineering.

Pokud chcete poskytnout zpětnou vazbu k produktu nebo požádat o nové funkce, vystavte nebo Hlasujte nápad prostřednictvím [fóra Azure Feedback pro blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Podpora komunity

Spolupracujte s odborníky z Microsoftu a komunitou Azure blockchain.

- [Microsoft Q&stránku s otázkou pro službu Azure blockchain](https://docs.microsoft.com/answers/topics/azure-blockchain-workbench.html). Technická podpora pro šablony Blockchain je omezená na problémy s nasazením.
- [Technická komunita Microsoftu](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)