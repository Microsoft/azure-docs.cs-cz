---
title: "Vytvoření clusteru služby Azure Service Fabric ze šablony | Microsoft Docs"
description: "Tento článek popisuje postup nastavení zabezpečení clusteru Service Fabric v Azure pomocí Azure Resource Manager, Azure Key Vault a Azure Active Directory (Azure AD) pro ověřování klientů."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: chackdan
ms.openlocfilehash: e8e5513df5ab412857403382e1940da27c85274a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Vytvořit cluster Service Fabric pomocí Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Tento podrobný průvodce vás provede procesem nastavení zabezpečení clusteru Azure Service Fabric v Azure pomocí Azure Resource Manager. Jsme na vědomí, že článek je dlouhá. Nicméně pokud jste již důkladně obeznámení s obsahem, je nutné pečlivě postupujte podle následujících kroků.

Průvodce zahrnuje následující postupy:

* Klíčové koncepty, které je nutné znát vypnout před nasazením service fabric cluster.
* Vytvoření clusteru s podporou v Azure pomocí modulů správce prostředků infrastruktury služby.
* Nastavení služby Azure Active Directory (Azure AD) pro ověřování uživatelů provádění operací správy v clusteru.
* Vytváření vlastní šablony Azure Resource Manageru pro váš cluster a jeho nasazení.

## <a name="key-concepts-to-be-aware-of"></a>Klíčové koncepty, které mít na paměti
Ve službě Azure Service fabric vyžaduje, abyste použili x509 certifikát k zabezpečení vašeho clusteru a její koncové body. Ve službě Service Fabric se k ověřování a šifrování pro zabezpečení různých aspektů clusteru a jeho aplikací využívají certifikáty. Pro klientský přístup nebo provádění operací správy v clusteru, včetně nasazení, upgrade a odstranění aplikací, služeb a data, která obsahují můžete použít certifikáty nebo přihlašovacích údajů Azure Active Directory. Použití služby Azure Active Directory je vysoce podporovali vzhledem k tomu, který je jediným způsobem, jak zabránit sdílení certifikátů u klientů.  Další informace o tom, jak certifikáty se používají v Service Fabric najdete v tématu [scénáře zabezpečení clusteru Service Fabric][service-fabric-cluster-security].

Service Fabric používá certifikáty X.509 k zabezpečení clusteru a poskytují funkce zabezpečení aplikací. Používáte [Key Vault] [ key-vault-get-started] na správu certifikátů clusterů Service Fabric v Azure. 


### <a name="cluster-and-server-certificate-required"></a>Cluster a server certifikátu (povinné)
Tyto certifikáty (jeden primární a volitelně sekundární) jsou nutná k zabezpečení clusteru a zabránit neoprávněnému přístupu k němu. Poskytuje zabezpečení clusteru dvěma způsoby:

* **Ověřování clusteru:** ověřuje komunikaci mezi uzly pro cluster federaci. Pouze uzly, které můžete prokázání své identity s tímto certifikátem může připojit ke clusteru.
* **Ověření serveru:** ověřuje koncové body správy clusteru klient pro správu, tak, aby klient správy zná je rozhovoru s skutečné clusteru a ne 'man uprostřed'. Tento certifikát také poskytuje protokolem SSL pro rozhraní API pro správu protokolu HTTPS a pro Service Fabric Explorer přes protokol HTTPS.

Poskytovat tyto účely, certifikát musí splňovat následující požadavky:

* Certifikát musí obsahovat privátní klíč. Tyto certifikáty obvykle mají rozšíření .pfx nebo .pem  
* Certifikát se musí vytvořit pro výměnu klíčů, což je exportovat do souboru Personal Information Exchange (.pfx).
* **Název předmětu certifikátu musí odpovídat domény, který používáte pro přístup ke clusteru Service Fabric**. Toto porovnání se vyžaduje k zajištění protokolem SSL pro koncový bod správy protokolu HTTPS a Service Fabric Explorer clusteru. Nelze získat certifikát SSL od certifikační autority (CA) pro *. cloudapp.azure.com domény. Pro svůj cluster musíte získat název vlastní domény. Pokud požádáte o certifikát od certifikační autority, musí název subjektu certifikátu odpovídat názvu vlastní domény, který používáte pro svůj cluster.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Nastavení Azure Active Directory pro ověřování klientů (volitelné, avšak doporučené)

Azure AD umožňuje spravovat přístup uživatelů k aplikacím organizace (označované jako klienty). Aplikace se dál dělí na ty s webové přihlášení uživatelského rozhraní a ty prostředí nativního klienta. V tomto článku předpokládáme, že jste již vytvořili klienta. Pokud máte není, spusťte načtením [postup získání klienta Azure Active Directory][active-directory-howto-tenant].

Cluster Service Fabric nabízí několik vstupní body k jeho funkce správy, včetně webové [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] a [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. V důsledku toho můžete vytvořit dvě aplikace Azure AD k řízení přístupu do clusteru, jednu webovou aplikaci a jeden nativní aplikace.

Další informace o tom, jak nastavit později v dokumentu.

### <a name="application-certificates-optional"></a>Certifikáty aplikace (volitelné)
Libovolný počet dalších certifikátů lze nainstalovat na clusteru pro aplikace z bezpečnostních důvodů. Před vytvořením clusteru, vezměte v úvahu scénáře zabezpečení aplikací, které vyžadují certifikát nainstalovaný na uzlech, například:

* Šifrování a dešifrování hodnot konfigurace aplikace.
* Šifrování dat mezi uzly během replikace.

Koncept vytváření zabezpečené clusterů je stejný, ať už jsou Linux nebo Windows clusterů. 

### <a name="client-authentication-certificates-optional"></a>Certifikáty pro ověřování klientů (volitelné)
Libovolný počet dalších certifikátů lze zadat pro uživatele nebo správce operací klienta. Ve výchozím nastavení tento certifikát clusteru má oprávnění správce klienta. Tyto další klientské certifikáty by se neměly instalovat do clusteru, pouze musí být zadány jako povolené v konfiguraci clusteru, ale potřebují nainstalovat na klientské počítače, připojte se ke clusteru a provádět všechny správy operace.


## <a name="prerequisites"></a>Požadavky 
Koncept vytváření zabezpečené clusterů je stejný, ať už jsou Linux nebo Windows clusterů. Tato příručka popisuje použití prostředí azure powershell nebo rozhraní příkazového řádku azure k vytvoření nových clusterů. Požadavky jsou buď 

-  [Prostředí Azure PowerShell 4.1 a vyšší] [ azure-powershell] nebo [rozhraní příkazového řádku Azure 2.0 a vyšší][azure-CLI].
-  můžete najít podrobnosti o na topologie Fabric modulů služby zde - [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) a [az SF rozhraní příkazového řádku modulu](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Nasazení clusteru pomocí modulu service fabric RM

V tomto dokumentu jsme byste použili service fabric RM prostředí powershell a rozhraní příkazového řádku modulu pro nasazení clusteru, prostředí powershell nebo rozhraní příkazového řádku příkaz modulu umožňuje pro více scénářů. Projděte dejte nám každý z nich. Vybrat v situaci, že si myslíte, že nejlepší vyhovuje vašim potřebám. 

- Vytvoření nového clusteru – použití systému vygenerovat certifikát podepsaný sám sebou
    - Použít výchozí šablonu clusteru
    - použít šablonu, kterou již máte
- Vytvoření nového clusteru – používá certifikát, které už vlastníte
    - Použít výchozí šablonu clusteru
    - použít šablonu, kterou již máte

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Vytvořit nový cluster - použití systému vygenerovat certifikát podepsaný sám sebou

Použijte následující příkaz k vytvoření clusteru, pokud máte má systém vygenerovat certifikát podepsaný sám sebou a použít ho k zabezpečení vašeho clusteru. Tento příkaz nastaví primární clusteru certifikát, který se používá pro zabezpečení clusteru a nastavit přístup správce k provádění operací správy pomocí tohoto certifikátu.

### <a name="login-in-to-azure"></a>přihlášení do Azure.

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Výchozí 5 šablonu nodetype 1 uzel, který se dodává v modulu nastavení clusteru

Použijte následující příkaz k vytvoření clusteru rychle zadáním minimální parametry

Je k dispozici na šablonu, která je použita [ukázky šablony služby azure service fabric: šablony systému windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) a [Ubuntu šablony](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Příkazy níže funguje pro vytváření clusterů Windows a Linux, stačí k určení operačního systému odpovídajícím způsobem. PowerShell / CLI příkazy také výstupy certifikátu v zadané CertificateOutputFolder ale zajistit certifikát složky, které jsou již vytvořeny. Příkaz přijímá v dalších parametrů jako virtuální počítač SKU také.

```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser –Location $resourceGroupLocation

```

```CLI

declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"



az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Použít vlastní šablonu, kterou už máte 

Pokud potřebujete vytvořit vlastní šablonu, aby vyhovovala vašim potřebám, důrazně doporučujeme začínat jednu z šablon, které jsou dostupné na [ukázky šablony služby azure service fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Postupujte podle pokynů a vysvětlení k [upravte si svou šablonu clusteru] [ customize-your-cluster-template] části níže.

Pokud již máte vlastní šablony a poté se ujistěte se, zda Překontrolujte, který související s všechny tři certifikát jsou takto pojmenované parametry v šabloně a soubor parametrů a hodnoty mají hodnotu null následujícím způsobem.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Zde je ekvivalentní příkaz rozhraní příkazového řádku o stejnou akci. Změňte hodnoty v příkazech declare odpovídající hodnoty. Rozhraní příkazového řádku podporuje všech dalších parametrů, které podporuje výše uvedeném příkazu prostředí PowerShell.

```CLI

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Vytvoření nového clusteru – pomocí certifikátu jste koupili od certifikační Autority nebo už máte.

Použijte následující příkaz k vytvoření clusteru, pokud máte certifikát, který chcete použít k zabezpečení vašeho clusteru v nástroji.

Pokud je certifikát podepsaný certifikační Autoritou, která se ukončí používá pro jiné účely také, pak doporučujeme zadat skupinu prostředků odlišné speciálně pro váš trezor klíčů. Doporučujeme převést trezoru klíčů do vlastní skupiny prostředků. Tato akce umožňuje odstranit výpočetního prostředí a úložiště skupiny prostředků, včetně skupinu prostředků, která obsahuje daný cluster Service Fabric bez ztráty klíče a tajné klíče. **Skupinu prostředků, která obsahuje váš trezor klíčů _musí být ve stejné oblasti_ jako cluster, který se používá.**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>Výchozí 5 šablonu nodetype 1 uzel, který se dodává v modulu
Je k dispozici na šablonu, která je použita [ukázek azure: šablony systému windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) a [Ubuntu šablony](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

```PowerShell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser

```

#### <a name="use-the-custom-template-that-you-have"></a>Použít vlastní šablonu, kterou máte 
Pokud potřebujete vytvořit vlastní šablonu, aby vyhovovala vašim potřebám, důrazně doporučujeme začínat jednu z šablon, které jsou dostupné na [ukázky šablony služby azure service fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Postupujte podle pokynů a vysvětlení k [upravte si svou šablonu clusteru] [ customize-your-cluster-template] části níže.

Pokud již máte vlastní šablony a poté se ujistěte se, zda Překontrolujte, který související s všechny tři certifikát jsou takto pojmenované parametry v šabloně a soubor parametrů a hodnoty mají hodnotu null následujícím způsobem.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword

```

Zde je ekvivalentní příkaz rozhraní příkazového řádku o stejnou akci. Změňte hodnoty v příkazech declare odpovídající hodnoty.

```CLI

declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-key-vault"></a>Použijte ukazatel na tajný klíč, který jste již odeslali do trezoru klíčů

Chcete-li použít existující trezor klíčů, můžete _ji musíte povolit pro nasazení_ umožňující poskytovatele výpočetních prostředků z něj získat certifikáty a nainstalujte ji na uzlech clusteru:

```PowerShell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment


$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroup -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```
Zde je ekvivalentní příkaz rozhraní příkazového řádku o stejnou akci. Změňte hodnoty v příkazech declare odpovídající hodnoty.

```CLI
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 

```

<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Nastavení Azure Active Directory pro ověřování klientů

Azure AD umožňuje spravovat přístup uživatelů k aplikacím organizace (označované jako klienty). Aplikace se dál dělí na ty s webové přihlášení uživatelského rozhraní a ty prostředí nativního klienta. V tomto článku předpokládáme, že jste již vytvořili klienta. Pokud máte není, spusťte načtením [postup získání klienta Azure Active Directory][active-directory-howto-tenant].

Cluster Service Fabric nabízí několik vstupní body k jeho funkce správy, včetně webové [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] a [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. V důsledku toho můžete vytvořit dvě aplikace Azure AD k řízení přístupu do clusteru, jednu webovou aplikaci a jeden nativní aplikace.

Pro zjednodušení některé kroky v konfiguraci Azure AD se cluster Service Fabric, jsme vytvořili sadu skriptů prostředí Windows PowerShell.

> [!NOTE]
> Následující kroky musí dokončit před vytvořením clusteru. Skripty očekává koncové body a názvy clusterů, hodnoty by měla být plánované a není hodnoty, že jste již vytvořili.

1. [Stáhněte si skripty] [ sf-aad-ps-script-download] do vašeho počítače.
2. Klikněte pravým tlačítkem na soubor zip, vyberte **vlastnosti**, vyberte **Odblokovat** zaškrtněte políčko a potom klikněte na **použít**.
3. Rozbalte soubor zip.
4. Spustit `SetupApplications.ps1`a zadejte TenantId, název clusteru a WebApplicationReplyUrl jako parametry. Příklad:

```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'

```

Vaše ID Tenanta můžete najít spuštěním příkazu Powershellu `Get-AzureSubscription`. Provádění tento příkaz zobrazí TenantId pro každé předplatné.

Název clusteru se používá jako předpona aplikace Azure AD, které jsou vytvořené pomocí skriptu. Nemusí se přesně shodovat s názvem skutečné clusteru. Je určena pouze k usnadňují mapování na cluster Service Fabric, který se používá s artefakty Azure AD.

WebApplicationReplyUrl je výchozí koncový bod, který vrátí Azure AD pro uživatele po jejich dokončení přihlašování. Nastavte tento koncový bod jako koncový bod Service Fabric Explorer pro váš cluster, který ve výchozím nastavení je:

https://&lt;cluster_domain&gt;:19080/Explorer

Zobrazí se výzva k přihlášení k účtu, který má oprávnění správce pro tenanta Azure AD. Po přihlášení vytvoří skript web a nativní aplikace představují cluster Service Fabric. Pokud se podíváte na klienta aplikace v [portál Azure][azure-portal], měli byste vidět dvě nové položky:

   * *ClusterName*\_Cluster
   * *Název clusteru*\_klienta

Skript vypíše JSON vyžadují šablony Azure Resource Manageru při vytvoření clusteru v další části, takže je vhodné ponechat otevřené okno prostředí PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Vytvoření šablony správce prostředků clusteru Service Fabric
Tato část je pro uživatele, kteří mají vlastní vytvářet šablony Resource Manageru clusteru Service Fabric. Jakmile máte šablonu, můžete stále přejděte zpět a použít powershell nebo rozhraní příkazového řádku moduly nasazení. 

Jsou k dispozici v šablonách Resource Manageru ukázkový [ukázek Azure na Githubu](https://github.com/Azure-Samples/service-fabric-cluster-templates). Tyto šablony slouží jako výchozí bod pro šablonu clusteru.

### <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru
Tato příručka používá [5 uzlu clusteru zabezpečené] [ service-fabric-secure-cluster-5-node-1-nodetype] příklad šablony a parametry šablony. Stáhněte si `azuredeploy.json` a `azuredeploy.parameters.json` do počítače a otevřete oba soubory ve svém oblíbeném textovém editoru.

### <a name="add-certificates"></a>Přidat certifikáty
Přidejte certifikáty do šablony správce prostředků clusteru pod položkou trezoru klíčů, který obsahuje klíče certifikátu. Přidejte tyto klíče trezoru parametry a hodnoty v souboru parametrů šablony Resource Manageru (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Přidejte všechny certifikáty do osProfile sady škálování virtuálního počítače
Každý certifikát, který je nainstalován v clusteru musí být nakonfigurován v části osProfile prostředku sada škálování (Microsoft.Compute/virtualMachineScaleSets). Tato akce Nastaví zprostředkovatele prostředků k instalaci certifikátu na virtuálních počítačích. Tato instalace zahrnuje certifikát clusteru a všechny certifikáty zabezpečení aplikací, které budete používat pro vaše aplikace:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurace certifikátu clusteru Service Fabric
Certifikát pro ověření clusteru musí být konfigurované v obou Service Fabric prostředek clusteru (Microsoft.ServiceFabric/clusters) a nastaví rozšíření Service Fabric pro škálování virtuálních počítačů v prostředku sady škálování virtuálního počítače. Toto uspořádání umožňuje poskytovateli prostředků Service Fabric ho nakonfigurovat pro použití pro ověření clusteru a ověření serveru pro správu koncové body.

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Přidáte že prostředek nastavit informace o certifikátu škálování virtuálních počítačů:
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Přidání informací o certifikátu pro prostředek clusteru Service Fabric:
```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Přidat konfiguraci Azure AD použít Azure AD pro klientský přístup

Konfigurace Azure AD s přidáte do šablony správce prostředků clusteru pod položkou trezoru klíčů, který obsahuje klíče certifikátu. Přidejte tyto parametry Azure AD a hodnoty v souboru parametrů šablony Resource Manageru (azuredeploy.parameters.json).

```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a name="populate-the-parameter-file-with-the-values"></a>Naplnění soubor parametrů s hodnotami.
Nakonec použijte hodnoty výstup z trezoru klíčů a Azure AD PowerShell příkazy k naplnění souboru parametrů:

Pokud máte v plánu služby Azure service fabric, moduly Powershellu RM, pak nemusíte naplnit informace o clusteru certifikátu, pokud je chcete systému a generovat vlastní podepsané certifikátu pro zabezpečení clusteru můžete použít, pouze je ponechat jako hodnotu null. 

> [!NOTE]
> Pro moduly RM vyzvednutí a naplnit tyto hodnoty prázdný parametr názvy parametrů mnohem odpovídat názvům níže
>

```json
        "clusterCertificateThumbprint": {
            "value": ""
        },
        "clusterCertificateUrlValue": {
            "value": ""
        },
        "sourceVaultvalue": {
            "value": ""
        },
```

Pokud používáte aplikace certifikátů nebo použití existující cluster, který jste odeslali do trezoru klíčů, budete muset získat tyto informace a přidejte do ní 

Moduly RM nemají možnost vygenerovat konfiguraci Azure AD pro vás. takže pokud máte v plánu používat Azure AD pro klientský přístup, je třeba přidejte do ní.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

### <a name="test-your-template"></a>Testování šablony  
Použijte následující příkaz prostředí PowerShell k testování šablony správce prostředků se soubor s parametry:

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

V případě, že potížím a získat zprávy jako nesrozumitelné, potom použijte "-Debug" jako možnost.

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Následující diagram znázorňuje, kde trezoru klíčů a konfigurace Azure AD zapadnou do vaší šablony Resource Manageru.

![Mapa závislostí Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>Vytvoření clusteru pomocí šablony prostředků Azure. 

Teď můžete nasadit cluster pomocí postupu uvedeného výše v dokumentu, nebo pokud máte hodnoty v souboru parametrů vyplněný, pak nyní jste připraveni k vytvoření clusteru pomocí [nasazení šablony Azure resource] [ resource-group-template-deploy] přímo.

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

V případě, že potížím a získat zprávy jako nesrozumitelné, potom použijte "-Debug" jako možnost.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Přiřadit uživatele k rolím
Po vytvoření aplikace, které chcete představují clusteru přiřadit uživatelům role nepodporuje Service Fabric: jen pro čtení a správce. Můžete přiřadit role pomocí [portál Azure][azure-portal].

1. Na portálu Azure vyberte svého klienta v pravém horním rohu.

    ![Kliknutím na tlačítko klienta][select-tenant-button]
2. Vyberte **Azure Active Directory** na levé kartě a poté vyberte možnost "podnikové aplikace".
3. Vyberte "Všechny aplikace" a potom najděte a vyberte webové aplikaci, která má název, jako je `myTestCluster_Cluster`.
4. Klikněte **uživatelů a skupin** kartě.

    ![Karta uživatelů a skupin][users-and-groups-tab]
5. Klikněte **přidat uživatele** tlačítko na nové stránce, vyberte uživatele a role, které chcete přiřadit a pak klikněte na tlačítko **vyberte** tlačítko v dolní části stránky.

    ![Přiřazení uživatelů na stránku rolí][assign-users-to-roles-page]
6. Klikněte **přiřadit** tlačítko v dolní části stránky.

    ![Přidat potvrzení přiřazení][assign-users-to-roles-confirm]

> [!NOTE]
> Další informace o rolích v Service Fabric najdete v tématu [řízení přístupu na základě rolí pro Service Fabric klienty](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Poradce při potížích nastavení služby Azure Active Directory
Nastavení služby Azure AD a jeho použití, může být náročné, takže tady jsou některé ukazatele na co můžete dělat k ladění problém.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer zobrazí výzva k výběru certifikátu
#### <a name="problem"></a>Problém
Po přihlášení úspěšně do služby Azure AD v Service Fabric Exploreru, prohlížeč vrací na domovskou stránku, ale zobrazí se výzva k výběru certifikátu.

![SFX dialogového okna certifikátu][sfx-select-certificate-dialog]

#### <a name="reason"></a>Důvod
Uživatel není přiřazena role v clusteru aplikace Azure AD. Ověření Azure AD se proto nezdaří na cluster Service Fabric. Service Fabric Explorer spadne zpět na ověřování pomocí certifikátu.

#### <a name="solution"></a>Řešení
Postupujte podle pokynů pro nastavení služby Azure AD a přiřadit role uživatele. Také doporučujeme zapnout "Přiřazení uživatelských požadovaná pro přístup k aplikaci" jako `SetupApplications.ps1` nepodporuje.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Připojení pomocí prostředí PowerShell selže s chybou: "zadané přihlašovací údaje jsou neplatné."
#### <a name="problem"></a>Problém
Použijete-li se připojit ke clusteru pomocí "Azureactivedirectory selhala" režim zabezpečení, po přihlášení úspěšně do služby Azure AD PowerShell, připojení se nezdaří s chybou: "zadané přihlašovací údaje jsou neplatné."

#### <a name="solution"></a>Řešení
Toto řešení je stejný jako předchozí.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer vrátí chybu při přihlášení: "AADSTS50011"
#### <a name="problem"></a>Problém
Při pokusu o přihlášení k Azure AD v Service Fabric Exploreru stránce vrátí chybu: "AADSTS50011: adresu &lt;url&gt; neodpovídá adresy odpovědět, nakonfigurované pro aplikaci: &lt;guid&gt;."

![Adresa pro odpověď SFX neodpovídá.][sfx-reply-address-not-match]

#### <a name="reason"></a>Důvod
Aplikace clusteru (web), která představuje Service Fabric Explorer pokus o ověření služby Azure AD a jako součást požadavku poskytuje návratové adresy URL přesměrování. Adresa URL není uvedena v aplikaci Azure AD, ale **adresa URL odpovědi** seznamu.

#### <a name="solution"></a>Řešení
Vyberte "Registrace aplikace" na stránce AAD, vyberte svou aplikaci clusteru a pak vyberte **adresy URL odpovědí** tlačítko. Na stránce "Adresy URL odpovědí" přidejte do seznamu adresu URL z Service Fabric Explorer nebo nahradit jedné z položek v seznamu. Po dokončení, uložte změnu.

![Adresa url odpovědi pro webové aplikace][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Připojení ke clusteru pomocí ověřování Azure AD pomocí prostředí PowerShell
Pokud chcete připojit cluster Service Fabric, použijte v následujícím příkladu příkaz prostředí PowerShell:

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Další informace o rutině Connect-ServiceFabricCluster najdete v tématu [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Můžete opakovaně použít stejnou klienta Azure AD ve více clusterů?
Ano. Ale nezapomeňte přidat adresu URL z Service Fabric Explorer aplikace clusteru (web). Service Fabric Exploreru, jinak nebude fungovat.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Proč stále potřebuji certifikát serveru je zapnuto Azure AD?
FabricClient a FabricGateway provést vzájemné ověření. Během ověřování Azure AD integrace Azure AD poskytuje identitu klienta k serveru a certifikát serveru se používá k ověření identity serveru. Další informace o certifikátech Service Fabric najdete v tématu [certifikáty X.509 a Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Další postup
V tuto chvíli máte zabezpečené cluster s poskytnete správu ověřování Azure Active Directory. Dále [připojit ke clusteru](service-fabric-connect-to-secure-cluster.md) a zjistěte, jak [spravovat tajné klíče aplikace](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

