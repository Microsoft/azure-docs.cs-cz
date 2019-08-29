---
title: Nasazení virtuálního počítače Azure s využitím Microsoft Docs
description: Naučte se používat k automatickému nasazení a konfiguraci virtuálních počítačů na Microsoft Azure.
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 5cbf53da5a0af0a511350b9f30153e2fefe72dcf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080091"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatizace nasazení virtuálních počítačů Azure pomocí Chefu

Nástroj pro vytváření je skvělým nástrojem pro poskytování automatizace a konfigurace požadovaného stavu.

S nejnovější verzí Cloud API poskytuje oprávnění k bezproblémové integraci s Azure, což vám dává možnost zřizovat a nasazovat stavy konfigurace prostřednictvím jediného příkazu.

V tomto článku jste nastavili své prostředí pro systém kuchařka pro zřizování virtuálních počítačů Azure, Projděte si téma Vytvoření zásady nebo "" a potom tento kuchařka nasadíte na virtuální počítač Azure.

## <a name="chef-basics"></a>Základní informace o pro.
Než začnete, [Přečtěte si základní koncepty systému pro](https://www.chef.io/chef)nasazení.

Následující diagram znázorňuje architekturu s vysokou úrovní.

![][2]

Má tři hlavní součásti architektury: Server pro stavbu, klient pro klientské aplikace (uzel) a pracovní stanice pro příkaz.

Server s nástrojem pro správu je bod správy a existují dvě možnosti pro server s podporou: hostované řešení nebo místní řešení.

Klient systému pro správu systému (uzel) je agent, který je umístěn na serverech, které spravujete.

Pracovní stanice systému na pracovišti, která je názvem pro pracovní stanici správce, kde vytváříte zásady a příkazy pro správu a softwarové balíčky nástrojů pro správu systému.

Obecně se zobrazí _vaše pracovní stanice_ jako umístění, kde provádíte akce a _pracovní stanice_ pro balíček softwaru.
Například můžete stáhnout příkaz nůž v rámci _pracovní stanice_systému příkazového řádku, ale z _pracovní stanice_ spustíte tyto příkazy pro správu infrastruktury.

Nástroj pro nasazení systému používá také koncepty "návody" a "recepty", které jsou efektivně definovány a použity na serverech.

## <a name="preparing-your-workstation"></a>Příprava pracovní stanice

Nejprve vytvořte adresář pro ukládání konfiguračních souborů a návody pro autoritu pro vytváření a přípravu pracovní stanice.

Vytvoření adresáře s názvem C:\Chef.

Stáhněte si a nainstalujte nejnovější verzi [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) do vaší pracovní stanice.

## <a name="configure-azure-service-principal"></a>Konfigurace instančního objektu Azure

V nejjednodušších pojmech a instančního objektu služby Azure je účet služby.   Pomocí instančního objektu nám pomůže vytvořit prostředky Azure z naší pracovní stanice pro službu systému.  Aby bylo možné vytvořit relevantní instanční objekt s požadovanými oprávněními, musíme v prostředí PowerShell spustit následující příkazy:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Poznamenejte si prosím svůj SubscriptionID, TenantID, ClientID a tajný kód klienta (heslo, které jste nastavili výše), budete ho potřebovat později. 

## <a name="setup-chef-server"></a>Nastavení serveru pro instalaci

V tomto průvodci se předpokládá, že se přihlásíte k hostovanému hostiteli.

Pokud ještě nepoužíváte server systému, můžete:

* Zaregistrujte [](https://manage.chef.io/signup)se do hostovaného zprovoznění, což je nejrychlejší způsob, jak začít pracovat s nástrojem.
* Nainstalujte samostatný server systému pro počítače se systémem Linux, a to podle [pokynů k instalaci](https://docs.chef.io/install_server.html) z [docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Vytvoření hostovaného účtu pro prostředí pro hostování

[Zde si](https://manage.chef.io/signup)Zaregistrujte hostovaný účet pro účet.

Během procesu registrace budete požádáni o vytvoření nové organizace.

![][3]

Po vytvoření vaší organizace si stáhněte úvodní sadu.

![][4]

> [!NOTE]
> Pokud se zobrazí výzva s upozorněním, že se vaše klíče resetují, je možné pokračovat, protože ještě není nakonfigurovaná žádná stávající infrastruktura.
>

Tento soubor zip sady Starter obsahuje konfigurační soubory vaší organizace a klíč uživatele v `.chef` adresáři.

Je `organization-validator.pem` nutné se stáhnout samostatně, protože se jedná o privátní klíč a privátní klíče by se neměly ukládat na server systému. V části [Správa](https://manage.chef.io/)v nástroji pro správu webu použijte příkaz "resetovat ověřovací klíč", který poskytuje soubor, který se má stáhnout samostatně. Uložte soubor do c:\chef.

### <a name="configuring-your-chef-workstation"></a>Konfigurace pracovní stanice pro správce

Extrakce obsahu souboru Chef-Starter. zip na c:\chef.

Zkopírujte všechny soubory v rámci\.Chef-starter\chef-repo do adresáře c:\chef.

`organization-validator.pem` Zkopírovat soubor do c:\chef, pokud je uložený v c:\downloads

Váš adresář by teď měl vypadat podobně jako v následujícím příkladu.

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Teď byste měli mít pět souborů a čtyři adresáře (včetně prázdného adresáře pro úložiště pro \ úložiště) v kořenovém adresáři c:\chef.

### <a name="edit-kniferb"></a>Upravit nůž. RB

Soubory PEM obsahují vaši organizaci a privátní klíče pro správu pro komunikaci a soubor nůž. RB obsahuje konfiguraci nůž. Bude nutné upravit soubor nůž. RB.

Otevřete soubor nůž. RB v editoru dle vašeho výběru. Nezměněný soubor by měl vypadat nějak takto:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Do nůž. RB přidejte následující informace:

validation_client_name "myorg-validátor"

validation_key "#{current_dir}/myorg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

nůž [: azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


Tyto řádky zajistí, aby nůž odkazovalo na adresář návody pod c:\chef\cookbooks a také používal instanční objekt Azure, který jste vytvořili během operací Azure.

Váš soubor nůž. RB by teď měl vypadat podobně jako v následujícím příkladu:

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Nainstalovat pracovní stanici systému pro instalaci

Pak [Stáhněte a nainstalujte](https://downloads.chef.io/chef-workstation/) pracovní stanici systému.
Nainstalujte do výchozího umístění pracovní stanici systému pro instalaci. Tato instalace může trvat několik minut.

Na ploše uvidíte "PowerShell PowerShell", což je prostředí načtené pomocí nástroje, které budete potřebovat pro interakci s produkty systému Desktop. Prostředí PowerShell pro SH zpřístupňuje nové příkazy ad hoc, `chef-run` jako jsou i tradiční příkazy rozhraní příkazového `chef`řádku. Podívejte se na vaši nainstalovanou verzi sady nástrojů pro správu systému `chef -v`pomocí nástroje. Verzi pracovní stanice si můžete prohlédnout taky tak, že v aplikaci pro pracovní stanici zaškrtnete "o pracovní stanici pro správce \.

`chef --version`měla by vracet něco jako:

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> Pořadí cesty je důležité. Pokud vaše cesty opscode nejsou ve správném pořadí, budete mít problémy.
>

Než budete pokračovat, restartujte svoji pracovní stanici.

### <a name="install-knife-azure"></a>Nainstalovat nůž – Azure

V tomto kurzu se předpokládá, že používáte Azure Resource Manager k interakci s virtuálním počítačem.

Nainstalujte rozšíření nůž Azure. To poskytuje nůž s "modulem plug-in Azure".

Spusťte následující příkaz.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argument – pre zajišťuje, že budete dostávat nejnovější verzi RC modulu plug-in Azure pro nůž, který poskytuje přístup k nejnovější sadě rozhraní API.
>
>

Je možné, že bude současně nainstalováno několik závislostí.

![][8]

Abyste zajistili, že všechno je správně nakonfigurované, spusťte následující příkaz.

    knife azurerm server list

Pokud je všechno správně nakonfigurované, zobrazí se seznam dostupných imagí Azure, které můžete procházet.

Blahopřejeme. Vaše pracovní stanice je nastavená!

## <a name="creating-a-cookbook"></a>Vytvoření kuchařka

Kuchařka používá k definování sady příkazů, které chcete spustit na spravovaném klientovi. Vytvoření kuchařka je jednoduché, stačí použít příkaz \ **Generate generují kuchařka** , který vygeneruje šablonu kuchařka. Tato kuchařka je určena pro webový server, který automaticky nasazuje službu IIS.

V adresáři C:\Chef spusťte následující příkaz.

    chef generate cookbook webserver

Tento příkaz vygeneruje sadu souborů v adresáři C:\Chef\cookbooks\webserver. Dále Definujte sadu příkazů pro klienta systému oprávnění ke spuštění ve spravovaném virtuálním počítači.

Příkazy jsou uloženy v souboru Default. RB. V tomto souboru Definujte sadu příkazů, které instalují službu IIS, spustí službu IIS a zkopíruje soubor šablony do složky Wwwroot.

Upravte soubor C:\chef\cookbooks\webserver\recipes\default.RB a přidejte následující řádky.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Po dokončení soubor uložte.

## <a name="creating-a-template"></a>Vytvoření šablony
V tomto kroku vygenerujete soubor šablony, který se použije jako výchozí stránka. html.

Spuštěním následujícího příkazu vygenerujte šablonu:

    chef generate template webserver Default.htm

Přejděte k `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` souboru. Upravte soubor tak, že přidáte nějaký jednoduchý kód HTML "Hello World" a pak soubor uložte.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Nahrání kuchařka na server s
V tomto kroku vytvoříte kopii kuchařka, kterou jste vytvořili v místním počítači, a nahrajete ji na hostovaný Server. Po nahrání se kuchařka zobrazí na kartě **zásady** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Nasazení virtuálního počítače pomocí nůž Azure
Nasaďte virtuální počítač Azure a použijte kuchařka "webserver", který nainstaluje webovou službu IIS a výchozí webovou stránku.

Abyste to mohli provést, použijte příkaz pro **Vytvoření azurerm serveru pro nůž** .

Příklad příkazu se zobrazí jako další.

    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"


Výše uvedený příklad vytvoří virtuální počítač s Standard_DS2_v2 s Windows serverem 2016 nainstalovaným v oblasti Západní USA. Nahraďte konkrétní proměnné a spusťte příkaz.

> [!NOTE]
> Pomocí příkazového řádku jsem také automatizuje pravidla filtru sítě koncového bodu pomocí parametru – TCP-Endpoints. Otevřel (a) jsem porty 80 a 3389, které poskytují přístup k webové stránce a relaci RDP.
>
>

Po spuštění příkazu přejděte na Azure Portal a podívejte se, jak se Váš počítač začne zřizovat.

![][15]

Zobrazí se příkazový řádek další.

![][16]

Po dokončení nasazení se veřejná IP adresa nového virtuálního počítače zobrazí při dokončení nasazení, můžete ho zkopírovat a vložit do webového prohlížeče a zobrazit web, který jste nasadili. Po nasazení virtuálního počítače jsme otevřeli port 80, takže by měl být k dispozici externě.   

![][11]

Tento příklad používá Creative kód HTML.

Můžete také zobrazit stav uzlu [Spravovat](https://manage.chef.io/). 

![][17]

Nezapomeňte se také připojit prostřednictvím relace RDP z Azure Portal přes port 3389.

Děkuju! Přečtěte si a začněte svoji infrastrukturu jako kód s cestou k Azure ještě dnes!

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->
