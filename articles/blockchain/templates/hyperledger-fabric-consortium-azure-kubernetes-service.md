---
title: Nasazení konsorcia prostředků infrastruktury pro hlavní knihu v Azure Kubernetes Service
description: Jak nasadit a nakonfigurovat síť konsorcia prostředků infrastruktury pro hlavní knihu ve službě Azure Kubernetes
ms.date: 01/08/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 1ab5b9fadfbb0f1c9c1cdf25ee319c7775a593ed
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060312"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Nasazení konsorcia prostředků infrastruktury pro hlavní knihu v Azure Kubernetes Service

Pomocí šablony prostředků infrastruktury hlavní knihy v Azure Kubernetes Service (AKS) můžete nasadit a nakonfigurovat síť konsorcia prostředků infrastruktury pro hlavní knihu v Azure.

Po přečtení tohoto článku:

- Mít praktické znalosti o prostředcích infrastruktury hlavní knihy a komponent, které tvoří stavební kameny blockchain sítě infrastruktury pro hlavní knihu.
- Zjistěte, jak nasadit a nakonfigurovat síť konsorcia prostředků infrastruktury pro hlavní knihu v Azure Kubernetes Service pro produkční scénáře.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Výběr řešení Azure blockchain

Než se rozhodnete použít šablonu řešení, porovnejte svůj scénář s běžnými případy použití dostupných možností Azure blockchain:

Možnost | Model služby | Běžný případ použití
-------|---------------|-----------------
Šablony řešení | IaaS | Šablony řešení jsou Azure Resource Manager šablony, pomocí kterých můžete zřídit plně nakonfigurovanou topologii sítě blockchain. Šablony nasazují a konfigurují Microsoft Azure COMPUTE, sítě a služby úložiště pro typ sítě blockchain. Šablony řešení jsou poskytovány bez smlouvy o úrovni služeb. Pro podporu použijte [Microsoft Q&](/answers/topics/azure-blockchain-workbench.html) .
[Služba Azure Blockchain](../service/overview.md) | PaaS | Služba Azure blockchain ve verzi Preview zjednodušuje vytváření, správu a řízení sítí konsorcia blockchain. Využijte Azure blockchain Service pro řešení, která vyžadují PaaS, správu konsorcia nebo ochranu osobních údajů smluv a transakcí.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS a PaaS | Azure blockchain Workbench Preview je kolekce služeb a schopností Azure, které vám pomůžou vytvářet a nasazovat aplikace blockchain pro sdílení obchodních procesů a dat s jinými organizacemi. Pomocí Azure blockchain Workbench můžete vytvořit prototypy řešení blockchain nebo zkoušku konceptu pro aplikaci blockchain. Azure blockchain Workbench se poskytuje bez smlouvy o úrovni služeb. Pro podporu použijte [Microsoft Q&](/answers/topics/azure-blockchain-workbench.html) .

## <a name="hyperledger-fabric-consortium-architecture"></a>Architektura konsorcia infrastruktury pro hlavní kniha

Chcete-li vytvořit síť prostředků infrastruktury hlavní knihy v Azure, je nutné nasadit službu řazení a organizaci s partnerskými uzly. Pomocí šablony řešení hlavní kniha prostředků infrastruktury v Azure Kubernetes můžete vytvářet uzly objednávek nebo partnerské uzly. Je nutné nasadit šablonu pro každý uzel, který chcete vytvořit.

Základní komponenty, které jsou vytvořeny jako součást nasazení šablony, jsou:

- **Uzly**: uzel, který je zodpovědný za řazení transakce v hlavní knize. Společně s ostatními uzly tvoří seřazené uzly službu řazení pro síť prostředků infrastruktury hlavní knihy.

- **Partnerské uzly**: uzel, který primárně hostuje hlavní knihy a inteligentní kontrakty, které jsou základními prvky sítě.

- **CA infrastruktury Fabric**: certifikační autorita (CA) pro prostředky infrastruktury hlavní knihy. Certifikační autorita prostředků infrastruktury umožňuje inicializovat a spustit proces serveru, který je hostitelem certifikační autority. Umožňuje správu identit a certifikátů. Každý cluster AKS nasazený jako součást šablony bude mít ve výchozím nastavení certifikační autoritu infrastruktury pod.

- **CouchDB nebo LevelDB**: databáze státních stavů pro rovnocenné uzly. LevelDB je výchozí databáze stavu vložená v partnerském uzlu. Ukládá chaincode data jako jednoduché páry klíč/hodnota a podporuje pouze dotazy na klíč, rozsah klíčů a složené klíče. CouchDB je volitelná alternativní databáze stavu, která podporuje formátované dotazy, když jsou datové hodnoty chaincode modelované jako JSON.

Šablona v nasazení roztočí různé prostředky Azure v rámci vašeho předplatného. Nasazené prostředky Azure jsou:

- **Cluster AKS**: cluster služby Azure Kubernetes, který je nakonfigurovaný podle vstupních parametrů poskytovaných zákazníkem. Cluster AKS má různé lusky nakonfigurované pro spouštění síťových komponent prostředků infrastruktury hlavní knihy. Vytvořené lusky jsou:

  - **Fabric Tools**: nástroje, které jsou zodpovědné za konfiguraci komponent technologie Fabric v hlavní knize.
  - **Lusky/protějšky**: uzly sítě prostředků infrastruktury hlavní knihy.
  - **Proxy**: proxy server ngnix pod tím, přes který mohou klientské aplikace komunikovat s clusterem AKS.
  - **CA infrastruktury Fabric**: na pod, kde je SPUŠTĚNÁ certifikační autorita infrastruktury.
- **PostgreSQL**: instance databáze, která udržuje identity certifikační autority infrastruktury.

- **Trezor klíčů**: Instance služby Azure Key Vault nasazená za účelem uložení přihlašovacích údajů certifikační autority prostředků infrastruktury a kořenových certifikátů poskytovaných zákazníkem. Trezor se používá v případě opakování nasazení šablony pro zpracování mechanismu šablony.
- **Managed disk**: instance služby Azure Managed disks, která poskytuje trvalé úložiště pro hlavní knihu a databázi světového stavu partnerského uzlu.
- **Veřejná IP adresa**: koncový bod clusteru AKS byl nasazen pro komunikaci s clusterem.

## <a name="deploy-the-orderer-and-peer-organization"></a>Nasazení pořadí a partnerské organizace

Začněte tím, že budete potřebovat předplatné Azure, které může podporovat nasazení několika virtuálních počítačů a standardních účtů úložiště. Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/).

Pokud chcete začít s nasazením síťových komponent infrastruktury pro hlavní knihu, navštivte [Azure Portal](https://portal.azure.com).

1. Vyberte **vytvořit prostředek**  >  **blockchain** a pak vyhledejte **prostředky infrastruktury hlavní knihy ve službě Azure Kubernetes Service (Preview)**.

2. Na kartě **základy** zadejte podrobnosti o projektu.

    ![Snímek obrazovky zobrazující kartu základy](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Zadejte následující podrobnosti:
    - **Předplatné**: vyberte název předplatného, ve kterém chcete nasadit síťové součásti prostředků infrastruktury hlavní knihy.
    - **Skupina prostředků**: buď vytvořte novou skupinu prostředků, nebo vyberte existující prázdnou skupinu prostředků. Skupina prostředků bude obsahovat všechny prostředky nasazené jako součást šablony.
    - **Oblast**: Vyberte oblast Azure, ve které chcete nasadit cluster služby Azure Kubernetes pro součásti prostředků infrastruktury hlavní knihy. Šablona je dostupná ve všech oblastech, kde je AKS k dispozici. Vyberte oblast, ve které se vašemu předplatnému nemůžete zavýšit kvótu virtuálního počítače (VM).
    - **Předpona prostředku**: Zadejte předponu pro pojmenování nasazených prostředků. Musí mít méně než šest znaků a kombinace znaků musí zahrnovat číslice i písmena.
4. Vyberte kartu **nastavení prostředků infrastruktury** a definujte síťové komponenty prostředků infrastruktury hlavní knihy, které budou nasazeny.

    ![Snímek obrazovky zobrazující kartu nastavení prostředků infrastruktury](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Zadejte následující podrobnosti:
    - **Název organizace**: zadejte název organizace prostředků infrastruktury hlavní knihy, která je vyžadována pro různé operace roviny dat. Název organizace musí být pro každé nasazení jedinečný.
    - **Součást sítě prostředků infrastruktury**: vyberte buď **řazení služby** nebo **partnerské uzly**, na základě součásti sítě blockchain, kterou chcete nastavit.
    - **Počet uzlů**: následující dva typy uzlů:
        - **Služba objednávání**: Vyberte počet uzlů pro zajištění odolnosti proti chybám v síti. Počet uzlů podporovaného pořadí je 3, 5 a 7.
        - **Partnerské uzly**: na základě vašeho požadavku můžete vybrat 1 až 10 uzlů.
    - **Databáze stavu partnerského uzlu na světě**: Vyberte mezi LevelDB a CouchDB. Toto pole se zobrazí, pokud v rozevíracím seznamu **součást sítě prostředků infrastruktury** vyberete možnost **rovnocenné uzly** .
    - **Uživatelské jméno certifikační autority infrastruktury**: zadejte uživatelské jméno, které se používá pro ověřování certifikační autority infrastruktury.
    - **Heslo certifikační autority prostředků infrastruktury**: zadejte heslo pro ověřování certifikační autority infrastruktury.
    - **Potvrzení hesla**: potvrďte heslo certifikační autority infrastruktury.
    - **Certifikáty**: Pokud chcete k inicializaci certifikační autority prostředků infrastruktury použít vlastní kořenové certifikáty, pak zvolte možnost **nahrát kořenový certifikát pro Fabric** . V opačném případě certifikační autorita infrastruktury ve výchozím nastavení vytvoří certifikáty podepsané svým držitelem.
    - **Kořenový certifikát**: Nahrajte kořenový certifikát (veřejný klíč), ve kterém je potřeba inicializovat certifikační autoritu infrastruktury. Podporují se certifikáty ve formátu. pem. Certifikáty by měly být platné a v časovém pásmu UTC.
    - **Privátní klíč kořenového certifikátu**: Nahrajte privátní klíč kořenového certifikátu. Pokud máte certifikát. pem s kombinovaným veřejným a soukromým klíčem, nahrajte ho také sem.


6. Výběrem karty **Nastavení clusteru AKS** definujte konfiguraci clusteru služby Azure Kubernetes, která je základní infrastrukturou, na které budou nastavené síťové komponenty prostředků infrastruktury hlavní knihy.

    ![Snímek obrazovky, který zobrazuje kartu Nastavení clusteru A kB S.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Zadejte následující podrobnosti:
    - **Název clusteru Kubernetes**: v případě potřeby změňte název clusteru AKS. Toto pole je předem vyplněné na základě zadané předpony prostředku.
    - **Verze Kubernetes**: vyberte verzi Kubernetes, která bude nasazena v clusteru. V závislosti na oblasti, kterou jste vybrali na kartě **základy** , se můžou dostupné podporované verze změnit.
    - **Předpona DNS**: Zadejte předponu názvu DNS (Domain Name System) pro cluster AKS. Pomocí DNS se připojíte k rozhraní Kubernetes API při správě kontejnerů po vytvoření clusteru.
    - **Velikost uzlu**: velikost uzlu Kubernetes můžete vybrat ze seznamu skladových jednotek virtuálních počítačů (SKU) dostupných v Azure. Pro zajištění optimálního výkonu doporučujeme standardní DS3 v2.
    - **Počet uzlů**: zadejte počet uzlů Kubernetes, které mají být nasazeny v clusteru. Doporučujeme, aby tento počet uzlů byl stejný nebo více než počet uzlů prostředků infrastruktury hlavní knihy zadaný na kartě **nastavení prostředků infrastruktury** .
    - **ID klienta instančního objektu**: Zadejte ID klienta existujícího instančního objektu nebo vytvořte nový. Pro ověřování AKS je vyžadován instanční objekt. Přečtěte si [Postup vytvoření instančního objektu](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal).
    - **Tajný kód klienta instančního objektu**: zadejte tajný klíč klienta instančního objektu, který je k dispozici v ID klienta pro instanční objekt.
    - **Potvrzení tajného klíče klienta**: potvrďte tajný klíč klienta pro instanční objekt.
    - **Povolit monitorování kontejnerů**: tuto možnost vyberte, pokud chcete povolit monitorování AKS, což umožňuje, aby protokoly AKS nahrajte do určeného pracovního prostoru Log Analytics.
    - **Log Analytics pracovní prostor**: Log Analytics pracovní prostor se naplní výchozím pracovním prostorem, který se vytvoří, pokud je povolené monitorování.

8. Vyberte kartu **Kontrola a vytvoření** . Tento krok aktivuje ověření pro hodnoty, které jste zadali.
9. Po úspěšném ověření vyberte **vytvořit**.

    Nasazení obvykle trvá 10 až 12 minut. Čas se může lišit v závislosti na velikosti a počtu zadaných uzlů AKS.
10. Po úspěšném nasazení budete upozorňováni prostřednictvím oznámení Azure v pravém horním rohu.
11. Vyberte **Přejít do skupiny prostředků** a ověřte, jestli se všechny prostředky vytvořily v rámci nasazení šablony. Všechny názvy prostředků budou začínat předponou poskytnutou na kartě **základy** .

## <a name="build-the-consortium"></a>Sestavení konsorcia

Pokud chcete po nasazení služby řazení a partnerských uzlů vytvořit blockchain Consortium, proveďte následující kroky v uvedeném pořadí. Skript prostředků infrastruktury Azure (azhlf) vám pomůže s nastavením konsorcia, vytvořením kanálu a prováděním operací chaincode.

> [!NOTE]
> Byl aktualizován skript Azure azhlf (Fabric-hlavní kniha), který poskytuje více funkcí. Pokud chcete odkazovat na starý skript, přečtěte si [soubor Readme na GitHubu](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Tento skript je kompatibilní s prostředky infrastruktury hlavní knihy ve službě Azure Kubernetes Service Template verze 2.0.0 a novější. Chcete-li zjistit verzi nasazení, postupujte podle kroků v tématu [řešení potíží](#troubleshoot).

> [!NOTE]
> Tento skript je k dispozici pouze v případě demonstračních, vývojových a testovacích scénářů. Kanál a konsorcium, které tento skript vytvoří, má základní zásady infrastruktury hlavní knihy pro zjednodušení scénářů pro ukázky, vývoj a testování. V případě produkčního nastavení doporučujeme, abyste aktualizovali zásady infrastruktury hlavního projektu kanálu/konsorcia v souladu s požadavky vaší organizace na dodržování předpisů pomocí nativních rozhraní API prostředků infrastruktury pro hlavní knihu.


Pomocí rozhraní příkazového řádku (CLI) pro Azure bash se dají spouštět všechny příkazy ke spuštění skriptu infrastruktury Azure. K Azure Cloud Shell se můžete přihlásit pomocí ![ Možnosti šablony prostředků infrastruktury služby Azure Kubernetes v pravém ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) horním rohu Azure Portal. Na příkazovém řádku zadejte `bash` a vyberte klávesu ENTER pro přepnutí na bash CLI nebo vyberte **bash** z panelu nástrojů Cloud Shell.

Další informace najdete v tématu [Azure Cloud Shell](../../cloud-shell/overview.md) .

![Snímek obrazovky, který zobrazuje příkazy v Azure Cloud Shell.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Následující obrázek znázorňuje podrobný proces vytváření konsorcia mezi organizací a rovnocennou organizací v řádu. Následující části obsahují podrobné příkazy k provedení těchto kroků.

![Diagram procesu pro sestavení konsorcia](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Po dokončení počátečního nastavení použijte klientskou aplikaci k dosažení následujících operací:  

- Správa kanálů
- Správa konsorcia
- Správa Chaincode

### <a name="download-client-application-files"></a>Stáhnout klientské soubory aplikace

Prvním instalačním programem je stažení souborů klientské aplikace. Spuštěním následujících příkazů stáhněte všechny požadované soubory a balíčky:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Tyto příkazy naklonují kód klientské aplikace prostředků infrastruktury Azure z veřejného úložiště GitHubu a za ním se načítají všechny závislé balíčky npm. Po úspěšném provedení příkazu uvidíte složku node_modules v aktuálním adresáři. Všechny požadované balíčky jsou načteny do složky node_modules.

### <a name="set-up-environment-variables"></a>Nastavení proměnných prostředí

Všechny proměnné prostředí následují po konvenci vytváření názvů prostředků Azure.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Nastavení proměnných prostředí pro klienta organizace v řádu

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Nastavení proměnných prostředí pro klienta partnerské organizace

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

Na základě počtu partnerských organizací ve vaší konsorcium může být nutné opakovat rovnocenné příkazy a odpovídajícím způsobem nastavit proměnnou prostředí.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Nastavení proměnných prostředí pro účet úložiště Azure

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Pomocí následujících příkazů vytvořte účet úložiště Azure. Pokud už máte účet úložiště Azure, přeskočte tento krok.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Pomocí následujících příkazů vytvořte sdílenou složku v účtu služby Azure Storage. Pokud již sdílenou složku máte, tento krok přeskočte.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

K vygenerování připojovacího řetězce pro sdílenou složku Azure použijte následující příkazy.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Importovat profil připojení organizace, identitu uživatele správce a MSP

Pomocí následujících příkazů načtěte profil připojení organizace, identitu uživatele správce a poskytovatele spravované služby (MSP) z clusteru služby Azure Kubernetes a uložte tyto identity v místním úložišti klientské aplikace. Příkladem místního úložiště je adresář *azhlfTool/Store* .

Pro organizaci v řádu Order:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Pro rovnocennou organizaci:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>Vytvoření kanálu

Z klienta organizace v řádu použijte následující příkaz k vytvoření kanálu, který obsahuje pouze organizaci daného řádu.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Přidání partnerské organizace pro správu konsorcia

>[!NOTE]
> Než začnete s jakoukoliv operací konsorcia, ujistěte se, že jste dokončili počáteční nastavení klientské aplikace.  

V uvedeném pořadí spusťte následující příkazy pro přidání partnerské organizace do kanálu a konsorcia: 

1.  Z klienta partnerské organizace nahrajte na Azure Storage MSP pro partnerský subjekt.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Z klienta organizace v řádu si stáhněte MSP pro partnerských organizací z Azure Storage. Pak vydejte příkaz pro přidání partnerské organizace do kanálu a konsorcia.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Z klienta organizace v řádu nahrajte do Azure Storage profil připojení k tomuto pořadí, aby se partnerské organizace mohla připojit k uzlům pořadí pomocí tohoto profilu připojení.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Z klienta partnerské organizace si stáhněte profil připojení k tomuto pořadí z Azure Storage. Pak spuštěním příkazu přidejte partnerské uzly do kanálu.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Podobně pro přidání dalších partnerských organizací do kanálu aktualizujte proměnné partnerského prostředí podle požadované organizace partnera a opakujte kroky 1 až 4.

### <a name="set-anchor-peers"></a>Nastavení partnerských uzlů pro kotvy

Z klienta partnerské organizace spusťte příkaz pro nastavení partnerských uzlů pro partnerský subjekt v zadaném kanálu.

>[!NOTE]
> Před spuštěním tohoto příkazu se ujistěte, že se v kanálu přidá partnerská organizace pomocí příkazů pro správu konsorcia.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` je čárkou oddělený seznam partnerských uzlů, které se mají nastavit jako kotvicí partner. Příklad:

  - Nastavte `<anchorPeersList>` , jako `"peer1"` kdybyste chtěli nastavit jenom uzel peer1 jako ukotvení partnerského uzlu.
  - Nastavte `<anchorPeersList>` , jak `"peer1" "peer3"` chcete jako kotvové partnery nastavit uzly peer1 i peer3.

## <a name="chaincode-management-commands"></a>Příkazy správy Chaincode

>[!NOTE]
> Než začnete s jakoukoli operací chaincode, ujistěte se, že jste provedli počáteční nastavení klientské aplikace.  

### <a name="set-the-chaincode-specific-environment-variables"></a>Nastavení proměnných prostředí specifických pro chaincode

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Nainstalovat chaincode  

Spusťte následující příkaz, který nainstaluje chaincode v partnerské organizaci.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Příkaz nainstaluje chaincode do všech partnerských uzlů sady rovnocenných organizací v sadě s `ORGNAME` proměnnou prostředí. Pokud máte ve vašem kanálu dvě nebo více partnerských organizací a chcete na všechny z nich nainstalovat chaincode, spusťte tento příkaz samostatně pro každou organizaci partnera.  

Postupujte takto:  

1.  Nastavte `ORGNAME` a `USER_IDENTITY` v závislosti na `peerOrg1` a spusťte `./azhlf chaincode install` příkaz.  
2.  Nastavte `ORGNAME` a `USER_IDENTITY` v závislosti na `peerOrg2` a spusťte `./azhlf chaincode install` příkaz.  

### <a name="instantiate-chaincode"></a>Vytvoření instance chaincode  

Z klientské aplikace partnerského vztahu spusťte následující příkaz pro vytvoření instance chaincode na kanálu.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Předejte název funkce vytváření instance a seznam argumentů oddělených mezerami v `<instantiateFunc>` `<instantiateFuncArgs>` uvedeném pořadí. Například pro vytvoření instance chaincode_example02. přejít chaincode, nastavte `<instantiateFunc>` na `init` a `<instantiateFuncArgs>` na `"a" "2000" "b" "1000"` .

Konfigurační soubor JSON kolekce můžete také předat pomocí `--collections-config` příznaku. Nebo nastavte přechodné argumenty pomocí `-t` příznaku při vytváření instance chaincode používané pro privátní transakce.

Příklad:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

`<collectionConfigJSONFilePath>`Část je cesta k souboru JSON, která obsahuje kolekce definované pro vytváření instancí privátních datových chaincode. Konfigurační soubor JSON pro vzorovou kolekci najdete relativně k adresáři *azhlfTool* v následující cestě: `./samples/chaincode/src/private_marbles/collections_config.json` .
Předat `<transientArgs>` jako platný formát JSON ve formátu řetězce. Řídicí sekvence všech speciálních znaků. Příklad: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Spusťte příkaz jednou ze všech partnerských organizací v kanálu. Po úspěšném odeslání transakce do objednávky tento objednávka distribuuje tuto transakci do všech partnerských organizací v kanálu. Chaincode se pak vytvoří na všech partnerských uzlech všech partnerských organizací v kanálu.  

### <a name="invoke-chaincode"></a>Vyvolat chaincode  

Z klienta partnerské organizace spusťte následující příkaz, který vyvolá funkci chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Předat název vyvolání funkce a seznam argumentů oddělených mezerami v  `<invokeFunction>`   a v  `<invokeFuncArgs>`   uvedeném pořadí. Pokračování s chaincode_example02. přejít chaincode, chcete-li provést operaci vyvolání, nastavte  `<invokeFunction>`   na  `invoke`   a  `<invokeFuncArgs>`   na `"a" "b" "10"` .  

>[!NOTE]
> Spusťte příkaz jednou ze všech partnerských organizací v kanálu. Po úspěšném odeslání transakce do objednávky tento objednávka distribuuje tuto transakci do všech partnerských organizací v kanálu. Stav World se pak aktualizuje na všech partnerských uzlech všech partnerských organizací v kanálu.  


### <a name="query-chaincode"></a>Chaincode dotazu  

Spusťte následující příkaz pro dotaz na chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
Přidávajícím partnerům jsou partnerské uzly, kde je nainstalovaný chaincode a který se volá pro provádění transakcí. Musíte nastavit `<endorsingPeers>` , aby obsahovaly názvy partnerských uzlů z aktuální partnerské organizace. Zobrazí seznam schválených partnerských uzlů pro danou kombinaci chaincode a kanálu oddělené mezerami. Příklad: `-p "peer1" "peer3"`.

Pokud používáte *azhlfTool* k instalaci chaincode, předejte všechny názvy partnerských uzlů jako hodnotu do argumentu pro potvrzení partnerského vztahu. Chaincode je nainstalovaný na všech partnerských uzlech této organizace. 

Předejte název funkce dotazu a seznam argumentů oddělených mezerami v  `<queryFunction>`    `<queryFuncArgs>`   uvedeném pořadí. Opětovné přijetí chaincode_example02. Chcete-li zadat dotaz na hodnotu "a" ve stavu World, nastavte  `<queryFunction>`   na hodnotu  `query` a  `<queryArgs>` na `"a"` .  

## <a name="troubleshoot"></a>Řešení potíží

### <a name="find-deployed-version"></a>Najít nasazenou verzi

Spusťte následující příkazy, abyste našli verzi nasazení šablony. Nastavte proměnné prostředí podle skupiny prostředků, ve které byla šablona nasazena.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Oprava předchozí verze

Pokud máte problémy se spouštěním chaincode na jakémkoli nasazení šablony verze níže v 3.0.0, postupujte podle následujících kroků a opravte své rovnocenné uzly opravou.

Stáhněte si skript nasazení peer.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Spusťte skript pomocí následujícího příkazu, který nahradí parametry pro partnerský uzel.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Počkejte, než se dostanou žádné ze svých partnerských uzlů. Stav partnerských uzlů můžete kdykoli kontrolovat v různých instancích prostředí pomocí následujícího příkazu.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Pokud chcete mít přehled o nabídkách služby blockchain a informacích od týmu Azure blockchain Engineering, přejděte na [blog Azure blockchain](https://azure.microsoft.com/blog/topics/blockchain/).

Pokud chcete poskytnout zpětnou vazbu k produktu nebo požádat o nové funkce, vystavte nebo Hlasujte nápad prostřednictvím [fóra Azure Feedback pro blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Podpora komunity

Spolupracujte s odborníky z Microsoftu a komunitou Azure blockchain:

- [Stránka Microsoft Q&](/answers/topics/azure-blockchain-workbench.html) 
   
  Technická podpora pro šablony Blockchain je omezená na problémy s nasazením.
- [Technická komunita Microsoftu](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
