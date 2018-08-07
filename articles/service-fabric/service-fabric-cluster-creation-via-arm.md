---
title: Vytvoření clusteru Azure Service Fabric ze šablony | Dokumentace Microsoftu
description: Tento článek popisuje, jak nastavit zabezpečení clusteru Service Fabric v Azure pomocí Azure Resource Manageru, Azure Key Vault a Azure Active Directory (Azure AD) pro ověřování klientů.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: aljo
ms.openlocfilehash: 780420c3ff69eb7da6e7c73b973164ed47c2c047
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525480"
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Vytvoření clusteru Service Fabric pomocí Azure Resource Manageru 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Tento podrobný návod vás provede nastavením zabezpečeného clusteru Azure Service Fabric v Azure pomocí Azure Resource Manageru. Jsme na vědomí, že je dlouhý článek. Nicméně pokud jste už znají s obsahem, je potřeba pečlivě postupujte podle jednotlivých kroků.

Průvodce zahrnuje následující postupy:

* Klíčové koncepty nástroje, které potřebujete znát před nasazením clusteru Service Fabric.
* Vytvoření clusteru v Azure s použitím modulů služby Správce prostředků infrastruktury.
* Nastavení Azure Active Directory (Azure AD) za účelem ověřování totožnosti uživatelů, kteří provádějí operace správy v clusteru.
* Vlastní šablonu Azure Resource Manageru pro váš cluster a jeho nasazení.

## <a name="key-concepts-to-be-aware-of"></a>Klíčové koncepty, je potřeba vědět
V Azure, který zmocňuje Service Fabric pomocí x x509 certifikátů k zabezpečení vašeho clusteru a její koncové body. Ve službě Service Fabric se k ověřování a šifrování pro zabezpečení různých aspektů clusteru a jeho aplikací využívají certifikáty. Pro klienta přístup nebo provádění operací správy v clusteru, včetně nasazení, upgrade a odstraňování aplikací, služeb a dat, které obsahují můžete použít certifikáty nebo přihlašovacích údajů Azure Active Directory. Použití služby Azure Active Directory je důrazně doporučujeme, protože to je jediný způsob, jak zabránit ve sdílení certifikátů u klientů.  Další informace o použití certifikátů ve službě Service Fabric najdete v tématu [scénáře zabezpečení clusteru Service Fabric][service-fabric-cluster-security].

Service Fabric pomocí certifikátů X.509 zabezpečení clusteru a poskytuje funkce pro zabezpečení aplikací. Použijete [služby Key Vault] [ key-vault-get-started] ke správě certifikátů pro clustery Service Fabric v Azure. 


### <a name="cluster-and-server-certificate-required"></a>Certifikát clusteru a serverem (povinné)
Tyto certifikáty (jeden primární a volitelně sekundární) je potřeba zabezpečit cluster a zabránit neoprávněnému přístupu k němu. Poskytuje zabezpečení clusteru dvěma způsoby:

* **Ověřování clusteru:** ověřuje komunikace mezi uzly clusteru federace. Pouze uzly, které se mohou prokázat svoji identitu pomocí tohoto certifikátu, můžete připojit ke clusteru.
* **Ověření serveru:** koncových bodů správy clusteru pro správu klienta, se ověří tak, aby klient správy ví se mluví skutečným clusterem a nikoli "muže in the middle". Tento certifikát také poskytuje zabezpečení SSL pro rozhraní API pro správu protokolu HTTPS a pro Service Fabric Explorer přes protokol HTTPS.

Chcete-li mají tyto účely, certifikát musí splňovat následující požadavky:

* Certifikát musí obsahovat privátní klíč. Obvykle mají tyto certifikáty PFX rozšíření nebo .pem  
* Certifikát musí být vytvořen pro výměnu klíčů, které je možné exportovat do souboru Personal Information Exchange (.pfx).
* **Název subjektu certifikátu musí odpovídat domény, který používáte pro přístup ke clusteru Service Fabric**. Tato shoda se vyžaduje kvůli zajištění SSL pro koncový bod HTTPS management a Service Fabric Explorer clusteru. Nelze získat certifikát SSL od certifikační autority (CA) pro *. cloudapp.azure.com domény. Pro svůj cluster musíte získat název vlastní domény. Pokud požádáte o certifikát od certifikační autority, musí název subjektu certifikátu odpovídat názvu vlastní domény, který používáte pro svůj cluster.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Nastavení Azure Active Directory pro ověřování klientů (volitelné, ale doporučené)

Azure AD umožňuje organizacím (označuje se jako tenantů) ke správě přístupu uživatelů k aplikacím. Aplikace se dělí na ty, které mají webové přihlašovacího uživatelského rozhraní a ty, které mají nativní klientské prostředí. V tomto článku předpokládáme, že jste již vytvořili tenanta. Pokud ne, začněte tím, že čtení [získání tenanta služby Azure Active Directory][active-directory-howto-tenant].

Cluster Service Fabric nabízí několik vstupních bodů do jeho funkce správy, včetně webová [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] a [sady Visual Studio] [ service-fabric-manage-application-in-visual-studio]. V důsledku toho můžete vytvořit dvě aplikace Azure AD pro řízení přístupu ke clusteru, jednu webovou aplikaci a jeden nativní aplikace.

Další informace o tom, jak nastavit později v dokumentu.

### <a name="application-certificates-optional"></a>Certifikáty aplikace (volitelné)
Libovolný počet dalších certifikátů můžete nainstalovat na clusteru pro účely zabezpečení aplikace. Před vytvořením clusteru, vezměte v úvahu scénáře zabezpečení aplikací, které vyžadují certifikát nainstalovaný na uzlech, například:

* Šifrování a dešifrování hodnoty konfigurace aplikace.
* Šifrování dat napříč uzly během replikace.

Konceptu vytváření zabezpečených clusterů je stejný, ať už jsou Linux nebo Windows clusterů. 

### <a name="client-authentication-certificates-optional"></a>Certifikáty pro ověřování klientů (volitelné)
U správce nebo uživatel operací klienta můžete zadat libovolný počet dalších certifikátů. Certifikát clusteru ve výchozím nastavení má oprávnění správce klienta. Tyto další klientské certifikáty by se neměly instalovat do clusteru, stačí zadat jako mohou v konfiguraci clusteru, musí však být nainstalovaný na klientského počítače pro připojení ke clusteru a provádět žádné management operace.


## <a name="prerequisites"></a>Požadavky 
Konceptu vytváření zabezpečených clusterů je stejný, ať už jsou Linux nebo Windows clusterů. Tato příručka popisuje použití Azure Powershellu nebo rozhraní příkazového řádku Azure k vytvoření nových clusterů. Požadavky jsou buď:

-  [Prostředí Azure PowerShell 4.1 a vyšší] [ azure-powershell] nebo [Azure CLI 2.0 a vyšší][azure-CLI].
-  Podrobnosti najdete v Service Fabric moduly zde - [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) a [az SF modulu rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Modul Service Fabric RM slouží k nasazení clusteru

V tomto dokumentu budeme používat Service Fabric RM powershellu a umožňuje modulu rozhraní příkazového řádku k nasazení clusteru, Powershellu nebo modul příkazu rozhraní příkazového řádku pro více scénářů. Dejte nám prosím projděte si každý z nich. Výběr scénáře, že máte pocit, že nejlepší bude vyhovovat vašim potřebám. 

- Vytvoření nového clusteru 
    - systém vygeneruje certifikát podepsaný svým držitelem
    - používá certifikát, které už vlastníte

Můžete použít výchozí šablonu clusteru nebo šablonu, která už máte

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Vytvoření nového clusteru – systém vygeneruje certifikát podepsaný svým držitelem

Použijte následující příkaz k vytvoření clusteru, pokud má systém vygenerovat certifikát podepsaný svým držitelem a použít ho k zabezpečení clusteru. Tento příkaz nastaví primární clusteru certifikát, který se používá pro zabezpečení clusteru a jak nastavit přístup správce k provádění operací správy pomocí certifikátu.

### <a name="login-to-azure"></a>Přihlaste se k Azure

```PowerShell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
```

```CLI
azure login
az account set --subscription $subscriptionId
```
#### <a name="use-the-default-5-node-1-node-type-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Šablona výchozí 5 uzlu 1 uzel typu, který se dodává v modulu nastavení clusteru

Použijte následující příkaz k vytvoření clusteru rychle, tak, že zadáte minimálního počtu parametrů

Šablonu, která se použije je k dispozici na [ukázkové šablony Azure Service Fabric: Šablona windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) a [Ubuntu šablony](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Příkazy pod funguje pro vytváření clusterů Windows a Linuxem, stačí k určení operačního systému odpovídajícím způsobem. Příkazy prostředí PowerShell nebo rozhraní příkazového řádku také výstup certifikátu v zadané CertificateOutputFolder; však ujistěte se, že složka certifikát už vytvořili. Příkaz přijímá další parametry jako skladovou Položku virtuálního počítače stejně.

> [!NOTE]
> Níže Powershellu příkaz funguje pouze s Azure Resource Manageru s prostředím PowerShell verze > 6.1. Zkontrolujte aktuální verzi Azure Powershellu pro Resource Manager verze, spusťte následující příkaz prostředí PowerShell "Get-Module AzureRM". Přejděte na tento odkaz k upgradu verze Azure Powershellu pro Resource Manager. https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.3.0
>
>
```PowerShell
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

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
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

#### <a name="use-the-custom-template-that-you-already-have"></a>Použít vlastní šablonu, která už máte 

Pokud je potřeba vytvořit vlastní šablony tak, aby odpovídala vašim potřebám, důrazně doporučujeme začít s některou ze šablon, které jsou k dispozici na [ukázkové šablony Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Postupujte podle pokynů a vysvětlení k [přizpůsobení šablony clusteru] [ customize-your-cluster-template] níže v části.

Pokud již jste vlastní šablony, pak se ujistěte se, že Překontrolujte, že všechny tři certifikát související jsou takto pojmenovaných parametrů v šabloně a soubor parametrů a hodnot mají hodnotu null následujícím způsobem.

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

Toto je ekvivalentní příkazu rozhraní příkazového řádku škol. Změňte hodnoty v příkazech declare na odpovídající hodnoty. CLI podporuje všechny ostatní parametry, které podporuje výše uvedený příkaz Powershellu.

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


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Vytvořit nový cluster - pomocí certifikátu, který jste zakoupili od certifikační Autority nebo už máte

Použijte následující příkaz k vytvoření clusteru, pokud máte certifikát, který chcete použít k zabezpečení clusteru pomocí.

Pokud certifikát podepsaný certifikační Autoritou, která skončíte použití pro jiné účely a, pak doporučujeme zadat do jiné skupiny prostředků pro váš trezor klíčů. Doporučujeme umístit služby key vault do své vlastní skupině prostředků. Tato akce umožňuje odebrání výpočetních prostředků a úložiště skupiny prostředků, včetně skupiny prostředků, která obsahuje cluster Service Fabric, aniž by ztratily klíče a tajné kódy. **Skupina prostředků obsahující trezor klíčů _musí být ve stejné oblasti_ jako cluster, který je používán.**


#### <a name="use-the-default-5-node-1-node-type-template-that-ships-in-the-module"></a>Šablona výchozí 5 uzlu 1 uzel typu, který se dodává v modulu
Šablonu, která se použije je k dispozici na [ukázky v Azure: Šablona Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) a [Ubuntu šablony](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

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

#### <a name="use-the-custom-template-that-you-have"></a>Použít vlastní šablonu, která máte 
Pokud je potřeba vytvořit vlastní šablony tak, aby odpovídala vašim potřebám, důrazně doporučujeme začít s některou ze šablon, které jsou k dispozici na [ukázkové šablony Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Postupujte podle pokynů a vysvětlení k [přizpůsobení šablony clusteru] [ customize-your-cluster-template] níže v části.

Pokud již jste vlastní šablony, pak se ujistěte se, že Překontrolujte, že všechny tři certifikát související jsou takto pojmenovaných parametrů v šabloně a soubor parametrů a hodnot mají hodnotu null následujícím způsobem.

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

Toto je ekvivalentní příkazu rozhraní příkazového řádku škol. Změňte hodnoty v příkazech declare na odpovídající hodnoty.

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

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-key-vault"></a>Použije ukazatel na tajný klíč, který jste již nahráli do služby key vault

Použití existujícího trezoru klíčů, které _musí povolit pro nasazení_ umožňující poskytovateli prostředků compute k získání certifikátů z něj a nainstalujte ho na uzly clusteru:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```
Toto je ekvivalentní příkazu rozhraní příkazového řádku škol. Změňte hodnoty v příkazech declare na odpovídající hodnoty.

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

Azure AD umožňuje organizacím (označuje se jako tenantů) ke správě přístupu uživatelů k aplikacím. Aplikace se dělí na ty, které mají webové přihlašovacího uživatelského rozhraní a ty, které mají nativní klientské prostředí. V tomto článku předpokládáme, že jste již vytvořili tenanta. Pokud ne, začněte tím, že čtení [získání tenanta služby Azure Active Directory][active-directory-howto-tenant].

Cluster Service Fabric nabízí několik vstupních bodů do jeho funkce správy, včetně webová [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] a [sady Visual Studio] [ service-fabric-manage-application-in-visual-studio]. V důsledku toho můžete vytvořit dvě aplikace Azure AD pro řízení přístupu ke clusteru, jednu webovou aplikaci a jeden nativní aplikace.

Pro zjednodušení některé kroky při konfiguraci Azure AD s clusterem Service Fabric, vytvořili jsme sadu skriptů prostředí Windows PowerShell.

> [!NOTE]
> Před vytvořením clusteru, musíte dokončit následující kroky. Vzhledem k tomu, že skripty očekávají názvů clusterů a koncových bodů, hodnoty plánování byste měli využít a ne hodnoty, že jste již vytvořili.

1. [Stáhněte si skripty] [ sf-aad-ps-script-download] k vašemu počítači.
2. Klikněte pravým tlačítkem na soubor zip, vyberte **vlastnosti**, vyberte **Odblokovat** zaškrtněte políčko a potom klikněte na tlačítko **použít**.
3. Rozbalte soubor zip.
4. Spustit `SetupApplications.ps1`a zadejte ID Tenanta, název clusteru a WebApplicationReplyUrl jako parametry. Příklad:

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
```

> [!NOTE]
> Pro národní cloudy (Azure Government, Azure China, Azure Germany), by měly také určit `-Location` parametru.

Vaše ID Tenanta zjistíte spuštěním příkazu Powershellu `Get-AzureSubscription`. Spuštění tohoto příkazu se zobrazí ID Tenanta pro každé předplatné.

Název clusteru je používat jako předpona aplikací v Azure AD, které jsou vytvořeny skriptem. Nemusí přesně odpovídat skutečný název clusteru. Je určena pouze k usnadňují mapovat do clusteru Service Fabric, který se právě používají s artefakty Azure AD.

WebApplicationReplyUrl je výchozí koncový bod, který Azure AD vrací uživatelům po jejich dokončení přihlašování. Nastavte jako koncový bod Service Fabric Exploreru pro váš cluster, který ve výchozím nastavení je tento koncový bod:

https://&lt;cluster_domain&gt;:19080/Explorer

Zobrazí se výzva k přihlášení k účtu, který má oprávnění správce pro tenanta Azure AD. Po přihlášení, skript vytvoří webové a nativní aplikace pro reprezentaci vašeho clusteru Service Fabric. Když se podíváte na aplikace vašeho tenanta v [webu Azure portal][azure-portal], měli byste vidět dvě nové položky:

   * *Název clusteru*\_clusteru
   * *Název clusteru*\_klienta

Skript vypíše soubor JSON potřebný pomocí šablony Azure Resource Manageru při vytvoření clusteru v další části, takže je vhodné ponechat otevřené okno Powershellu.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Vytvoření šablony resource Manageru clusteru Service Fabric
Tato část je pro uživatele, kteří mají na vlastní vytvoření šablony resource Manageru clusteru Service Fabric. Jakmile budete mít šablony, můžete stále nevrátíte zpět a použijte moduly Powershellu nebo rozhraní příkazového řádku k jejímu nasazení. 

Šablony Resource Manageru ukázky jsou k dispozici v [ukázky na Githubu v Azure](https://github.com/Azure-Samples/service-fabric-cluster-templates). Tyto šablony lze použít jako výchozí bod pro šablony clusteru.

> [!NOTE]
> Pro národní cloudy (Azure Government, Azure China, Azure Germany), měli byste také přidat následující `fabricSettings` do šablony ARM: `AADLoginEndpoint`, `AADTokenEndpointFormat` a `AADCertEndpointFormat`.

### <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru
Tato příručka používá [zabezpečeného clusteru s 5 uzly] [ service-fabric-secure-cluster-5-node-1-nodetype] příklad šablony a parametrů šablony. Stáhněte si `azuredeploy.json` a `azuredeploy.parameters.json` do vašeho počítače a otevřete oba soubory ve svém oblíbeném textovém editoru.

### <a name="add-certificates"></a>Přidat certifikáty
Přidejte certifikáty do šablony clusteru resource Manageru odkazem, který obsahuje klíče certifikátů trezor klíčů. Přidejte tyto služby key vault parametry a hodnoty v souboru parametrů šablony Resource Manageru (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Přidat všechny certifikáty do osProfile sady škálování virtuálního počítače
Každý certifikát, který je nainstalován v clusteru musí být nakonfigurovaná v části osProfile prostředku škálovací sady (Microsoft.Compute/virtualMachineScaleSets). Tato akce nastaví poskytovatele prostředků k instalaci certifikátu na virtuálních počítačích. Tato instalace zahrnuje certifikát clusteru a všechny certifikáty zabezpečení aplikací, které chcete použít pro vaše aplikace:

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
Ověřovací certifikát clusteru musí být nakonfigurovány v obou Service Fabric prostředku clusteru (Microsoft.ServiceFabric/clusters) a rozšíření Service Fabric pro virtuální počítač škálovací sady v prostředku virtuálního počítače škálovací sady. Toto uspořádání umožňuje poskytovateli prostředků Service Fabric ji nakonfigurovat pro použití pro ověření clusteru a ověření serveru pro koncové body správy.

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Přidejte informace o certifikátu virtuálního počítače škálovací sady prostředků:
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
                  "commonNames": ["[parameters('certificateCommonName')]"],
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
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Přidat konfiguraci Azure AD, které používají Azure AD pro klientský přístup

Konfigurace služby Azure AD přidejte do šablony Resource Manageru clusteru odkazem, který obsahuje klíče certifikátů trezor klíčů. Přidejte tyto služby Azure AD parametry a hodnoty v souboru parametrů šablony Resource Manageru (azuredeploy.parameters.json).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
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

### <a name="populate-the-parameter-file-with-the-values"></a>Naplnění soubor parametrů s hodnotami
Nakonec použijte výstupní hodnoty ze služby key vault a příkazy Azure AD Powershellu k naplnění souboru parametrů.

Pokud máte v plánu používat moduly Powershellu služby Azure service fabric RM, není potřeba naplnění informací o certifikátu clusteru. Pokud má systém generování podepsané svým certifikátů pro zabezpečení clusteru, ponechat pouze jako hodnota null. 

> [!NOTE]
> Pro moduly RM sbírání a naplňte jimi tyto hodnoty prázdných parametrů názvy parametrů mnohem odpovídají názvům níže

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Pokud používáte certifikáty aplikace nebo používáte existující cluster, který jste odeslali do služby key vault, musíte získat tyto informace a přidejte do ní.

Moduly RM nemají možnost vygenerovat konfiguraci Azure AD pro vás, tak pokud plánujete používat Azure AD pro klientský přístup, je potřeba ho naplnit.

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

### <a name="test-your-template"></a>Test šablony  
Pro testování soubor parametrů šablony Resource Manageru pomocí následujícího příkazu Powershellu:

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

V případě narazíte na problémy a získat zprávy jako nesrozumitelné, pak použijte "-Debug" jako možnost.

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Následující diagram znázorňuje, které je váš trezor klíčů a konfigurace služby Azure AD umístit do šablony Resource Manageru.

![Mapa závislostí Resource Manageru][cluster-security-arm-dependency-map]


## <a name="encrypting-the-disks-attached-to-your-windows-cluster-nodevirtual-machine-instances"></a>Šifruje disky připojené k windows instance uzlu nebo virtuální počítače clusteru

Pro šifrování disků (jednotky operačního systému a další spravované disky), které uzly připojeny, můžeme využít Azure Disk Encryption. Azure Disk Encryption je nová funkce, která vám pomůže [šifrovat disky virtuálních počítačů Windows](service-fabric-enable-azure-disk-encryption-windows.md). Azure Disk Encryption využívá standardní oborový [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkce Windows zajišťuje šifrování svazku operačního systému. Toto řešení je integrovaná s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) umožňují řídit a spravovat šifrování disku klíče a tajné kódy ve vašem předplatném služby key vault. Řešení také zajišťuje, že všechna data na discích virtuálních počítačů šifrování v klidovém stavu ve službě Azure storage. 

## <a name="encrypting-the-disks-attached-to-your-linux-cluster-nodevirtual-machine-instances"></a>Šifrování disků připojených k vaší instance systému Linux clusteru uzlu nebo virtuální počítač

Pro šifrování disků (datový disk a další spravované disky), které uzly připojeny, můžeme využít Azure Disk Encryption. Azure Disk Encryption je nová funkce, která vám pomůže [šifrovat disky virtuálních počítačů Linux](service-fabric-enable-azure-disk-encryption-linux.md). Azure Disk Encryption využívá standardní oborový [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkce Linux zajišťuje šifrování pro datové disky. Toto řešení je integrovaná s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) umožňují řídit a spravovat šifrování disku klíče a tajné kódy ve vašem předplatném služby key vault. Řešení také zajišťuje, že všechna data na discích virtuálních počítačů šifrování v klidovém stavu ve službě Azure storage. 


## <a name="create-the-cluster-using-azure-resource-template"></a>Vytvoření clusteru pomocí šablony Azure resource 

Teď můžete nasadit cluster pomocí postupu uvedeného dříve v dokumentu, nebo pokud máte v souboru parametrů, naplní hodnoty, pak nyní jste připraveni k vytvoření clusteru pomocí [nasazení šablony Azure resource] [ resource-group-template-deploy] přímo.

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

V případě narazíte na problémy a získat zprávy jako nesrozumitelné, pak použijte "-Debug" jako možnost.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Přiřazení uživatelů k rolím
Po vytvoření aplikace, které chcete cluster představují přiřazení uživatele k rolím nepodporuje Service Fabric: jen pro čtení a správce. Pomocí můžete přiřadit role [webu Azure portal][azure-portal].

1. Na webu Azure Portal vyberte v pravém horním rohu vašeho tenanta.

    ![Vyberte tlačítko tenanta][select-tenant-button]
2. Vyberte **Azure Active Directory** na levé kartě a poté vyberte možnost "podnikové aplikace".
3. Vyberte "Všechny aplikace" a pak vyhledejte a vyberte webovou aplikaci, která má název například `myTestCluster_Cluster`.
4. Klikněte na tlačítko **uživatelů a skupin** kartu.

    ![Kartu uživatelé a skupiny][users-and-groups-tab]
5. Klikněte na tlačítko **přidat uživatele** tlačítko Nová stránka, vyberte uživatele a roli chcete přiřadit a potom klikněte na tlačítko **vyberte** tlačítko v dolní části stránky.

    ![Přiřadit uživatele na stránku rolí][assign-users-to-roles-page]
6. Klikněte na tlačítko **přiřadit** tlačítko v dolní části stránky.

    ![Přidat přiřazení potvrzení][assign-users-to-roles-confirm]

> [!NOTE]
> Další informace o rolích v Service Fabric najdete v tématu [řízení přístupu na základě rolí pro Service Fabric klienty](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Poradce při potížích v nastavení Azure Active Directory
Nastavení Azure AD a jeho použití může být náročné, takže tady je několik tipů, na co můžete dělat k ladění těchto potíží.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer vyzve k výběru certifikátu
#### <a name="problem"></a>Problém
Po přihlášení úspěšně do služby Azure AD v Service Fabric Explorer, prohlížeč se vrátí na domovskou stránku, ale zobrazí se výzva k výběru certifikátu.

![Dialogové okno certifikát SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Důvod
Uživatel není přiřazena role v clusteru aplikaci Azure AD. Ověřování Azure AD díky tomu se nezdaří v clusteru Service Fabric. Service Fabric Explorer spadne zpět na ověření certifikátu.

#### <a name="solution"></a>Řešení
Postupujte podle pokynů pro nastavení služby Azure AD a přiřadit role uživatele. Taky doporučujeme zapnout "Přiřazení uživatelů povinné pro přístup k aplikaci," jako `SetupApplications.ps1` nepodporuje.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Připojení pomocí prostředí PowerShell se nezdaří s chybou: "zadané přihlašovací údaje jsou neplatné."
#### <a name="problem"></a>Problém
Při použití prostředí PowerShell pro připojení ke clusteru pomocí "AzureActiveDirectory" režim zabezpečení, po přihlášení úspěšně do služby Azure AD, připojení se nezdaří s chybou: "zadané přihlašovací údaje jsou neplatné."

#### <a name="solution"></a>Řešení
Toto řešení je stejná jako ta předchozí.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer vrátí chybu při přihlášení: "AADSTS50011"
#### <a name="problem"></a>Problém
Při pokusu o přihlášení k Azure AD v Service Fabric Exploreru na stránce vrátí chybu: "AADSTS50011: adresa pro odpovědi &lt;url&gt; neodpovídá adresám pro odpovědi nakonfigurovaným pro aplikaci: &lt;guid&gt;."

![Adresa pro odpovědi SFX neodpovídá.][sfx-reply-address-not-match]

#### <a name="reason"></a>Důvod
Aplikace clusteru (web), který představuje Service Fabric Exploreru pokusí ověřit ve službě Azure AD a jako součást požadavku poskytuje návratová adresa URL přesměrování. Adresa URL není uvedena v aplikaci Azure AD, ale **adresy URL odpovědi** seznamu.

#### <a name="solution"></a>Řešení
Vyberte "Registrace aplikací" na stránce AAD, vyberte svou aplikaci clusteru a pak vyberte **adresy URL odpovědí** tlačítko. Na stránce "Adresy URL odpovědí" adresa URL nástroje Service Fabric Exploreru přidejte do seznamu nebo nahradit jednu z položek v seznamu. Jakmile budete hotovi, uložte změnu.

![Adresa url odpovědi webové aplikace][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Připojení ke clusteru pomocí ověřování Azure AD prostřednictvím prostředí PowerShell
Pro připojení ke clusteru Service Fabric, použijte následující ukázkový příkaz Powershellu:

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Další informace o rutiny Connect-ServiceFabricCluster najdete v tématu [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Můžete znovu použít stejným tenantem Azure AD v několika clusterech?
Ano. Ale nezapomeňte přidat adresu URL nástroje Service Fabric Exploreru pro vaši aplikaci clusteru (web). V opačném případě se Service Fabric Exploreru nefunguje.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Proč musím i certifikát serveru služby Azure AD je povolen?
FabricClient a FabricGateway provedeno vzájemné ověření. Během ověřování Azure AD integrace služby Azure AD poskytuje identitu klienta na server a server certifikát se používá k ověření identity serveru. Další informace o certifikátech pro Service Fabric najdete v tématu [certifikáty X.509 a Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Další postup
V tomto okamžiku máte cluster zabezpečený pomocí ověřování Azure Active Directory poskytuje správu. Dále [připojení k vašemu clusteru](service-fabric-connect-to-secure-cluster.md) a zjistěte, jak [Správa tajných klíčů aplikací](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
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
