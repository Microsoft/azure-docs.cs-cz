---
title: Rychlý Start – konfigurace virtuálního počítače s Windows v Azure pomocí systému pro zprovoznění
description: V tomto rychlém startu se dozvíte, jak pomocí systému pro nasazení nasadit a nakonfigurovat virtuální počítač s Windows v Azure.
keywords: ', Azure, DevOps, virtuální počítač'
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590066"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Rychlý Start – konfigurace virtuálního počítače s Windows v Azure pomocí systému pro zprovoznění

S nástrojem pro vytváření a konfigurace požadovaných stavů vám umožní doručovat služby Automation.

S nejnovější verzí Cloud API poskytuje oprávnění k bezproblémové integraci s Azure, což vám dává možnost zřizovat a nasazovat stavy konfigurace prostřednictvím jediného příkazu.

V tomto článku jste nastavili své prostředí pro systém kuchařka pro zřizování virtuálních počítačů Azure a provedli jste vytváření zásad nebo a pak tento kuchařka nasadíte na virtuální počítač Azure.

## <a name="chef-basics"></a>Základní informace o pro.

Než začnete s tímto článkem, [Přečtěte si základní koncepty systému pro](https://www.chef.io/chef)nasazení.

Následující diagram znázorňuje architekturu s vysokou úrovní.

![Architektura](media/chef-automation/chef-architecure.png)

Má tři hlavní součásti architektury: 
- Server s nástrojem pro správu – bod správy a existují dvě možnosti pro server s podporou: hostované řešení nebo místní řešení.
- Klient systému pro správu systému (uzel) – agent, který je umístěn na serverech, které spravujete.
- Název pracovní stanice – název pro pracovní stanici správce (kde vytvoříte zásady a spustíte příkazy správy) a softwarový balíček nástrojů pro správu systému.

Obecně se zobrazuje **vaše pracovní stanice** jako umístění, kde spouštíte příkazy a **pracovní stanice** pro balíček pro software.

Například můžete stáhnout příkaz nůž v rámci **pracovní stanice**systému příkazového řádku, ale z **pracovní stanice** spustíte tyto příkazy pro správu infrastruktury.

Pro počítače s *návody* se používají koncepty a *recepty*. Tyto výrazy jsou zásady, které jsou definovány a aplikovány na servery, v uvedeném pořadí.

## <a name="preparing-your-workstation"></a>Příprava pracovní stanice

Nejprve vytvořte adresář pro ukládání konfiguračních souborů a návody pro autoritu pro vytváření a přípravu pracovní stanice.

Vytvořte adresář s názvem `C:\Chef`.

Stáhněte si a nainstalujte nejnovější verzi [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) do vaší pracovní stanice.

## <a name="configure-azure-service-principal"></a>Konfigurace instančního objektu Azure

Pomocí instančního objektu nám pomůžeme vytvořit prostředky Azure z naší pracovní stanice pro samoobslužné práce.  Pokud chcete vytvořit relevantní instanční objekt s požadovanými oprávněními, spusťte v PowerShellu následující příkazy:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Poznamenejte si ID předplatného, TenantID, ClientID a Client (heslo, které jste nastavili dříve v tomto kurzu), protože tyto hodnoty budete potřebovat. 

## <a name="setup-chef-server"></a>Nastavení serveru pro instalaci

V tomto průvodci se předpokládá, že se zaregistrujete do hostovaného prostředí pro registraci.

Pokud ještě nepoužíváte server systému, můžete:

* Zaregistrujte se do [hostovaného](https://manage.chef.io/signup)zprovoznění, což je nejrychlejší způsob, jak začít pracovat s nástrojem.
* Nainstalujte samostatný server systému pro počítače se systémem Linux, a to podle [pokynů k instalaci](https://docs.chef.io/install_server.html) z [docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Vytvoření hostovaného účtu pro prostředí pro hostování

[Zde si](https://manage.chef.io/signup)Zaregistrujte hostovaný účet pro účet.

Během procesu registrace budete požádáni o vytvoření nové organizace.

![Vytvořit organizační okno](media/chef-automation/create-organization.png)

Po vytvoření vaší organizace si stáhněte úvodní sadu.

![Konfigurace součásti pro konfiguraci](media/chef-automation/configure-chef.png)

> [!NOTE]
> Pokud se zobrazí výzva s upozorněním, že se vaše klíče resetují, je možné pokračovat, protože ještě není nakonfigurovaná žádná stávající infrastruktura.
>

Tento soubor zip úvodní sady obsahuje konfigurační soubory vaší organizace a klíč uživatele v adresáři `.chef`.

`organization-validator.pem` je třeba stáhnout samostatně, protože se jedná o privátní klíč a privátní klíče by se neměly ukládat na server systému. V nástroji pro [správu](https://manage.chef.io/)\ Správce služby použijte příkaz pro správu a vyberte možnost resetovat ověřovací klíč, která poskytuje soubor, který se má stáhnout samostatně. Uložte soubor do c:\chef.

### <a name="configuring-your-chef-workstation"></a>Konfigurace pracovní stanice pro správce

Extrahujte obsah `chef-starter.zip` do `c:\chef`.

Zkopírujte všechny soubory pod `chef-starter\chef-repo\.chef` do adresáře `c:\chef`.

Zkopírujte soubor `organization-validator.pem` do `c:\chef`, pokud je uložený v `c:\Downloads`.

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

Přidejte do svého nůž. RB následující informace, nahraďte zástupné symboly vašimi informacemi:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Tyto řádky zajistí, aby nůž odkazovala na adresář návody v části `c:\chef\cookbooks`.

Soubor `knife.rb` by teď měl vypadat podobně jako v následujícím příkladu:

![Příklad souboru nůž](./media/chef-automation/knife-file-example.png)

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

Dále [si stáhněte a nainstalujte pracovní stanici](https://downloads.chef.io/chef-workstation/)systému.

Nainstalujte pracovní stanici systému pro instalaci do výchozího umístění.

Na ploše se zobrazí prostředí PowerShell pro SH. Tento nástroj slouží k interakci s produkty v produktech. Prostředí PowerShell pro SH zpřístupňuje nové příkazy, jako jsou příkazy `chef-run` a příkazového řádku pro rozhraní příkazového řádku (například `chef`). Podívejte se na vaši nainstalovanou verzi sady nástrojů pro správu systému a nástroje pro správu systému `chef -v`. Verzi pracovní stanice si můžete prohlédnout taky tak, že v aplikaci systému pro správu pracovní stanice vyberete **o pracovní** stanici.

`chef --version` by měl vracet něco podobného:

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
> Pořadí cesty je důležité. Pokud vaše cesty opscode nejsou ve správném pořadí, budou výsledkem problémy.
>

Než budete pokračovat, restartujte svoji pracovní stanici.

### <a name="install-knife-azure"></a>Nainstalovat nůž – Azure

V tomto kurzu se předpokládá, že používáte Azure Resource Manager k interakci s virtuálním počítačem.

Nainstalujte rozšíření nůž Azure, které zahrnuje modul plug-in Azure.

Spusťte následující příkaz.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argument `–-pre` zajišťuje, že obdržíte nejnovější verzi RC modulu plug-in Azure, který poskytuje přístup k nejnovější sadě rozhraní API.
>
>

Je možné, že bude současně nainstalováno několik závislostí.

![Výstup z instalace nůž – Azure](./media/chef-automation/install-knife-azure.png)

Abyste zajistili, že všechno je správně nakonfigurované, spusťte následující příkaz.

    knife azurerm server list

Pokud je všechno správně nakonfigurované, zobrazí se seznam dostupných imagí Azure, které můžete procházet.

Blahopřejeme. Vaše pracovní stanice je nastavená!

## <a name="creating-a-cookbook"></a>Vytvoření kuchařka

Kuchařka používá k definování sady příkazů, které chcete spustit na spravovaném klientovi. Vytvoření kuchařka je jednoduché, stačí k vytvoření šablony kuchařka použít příkaz `chef generate cookbook`. Tato kuchařka je určena pro webový server, který automaticky nasazuje službu IIS.

V části `C:\Chef directory`spusťte následující příkaz.

    chef generate cookbook webserver

Tento příkaz vygeneruje sadu souborů v adresáři C:\Chef\cookbooks\webserver. Dále Definujte sadu příkazů pro klienta systému, který se má spustit na spravovaném virtuálním počítači.

Příkazy jsou uloženy v souboru Default. RB. V tomto souboru Definujte sadu příkazů, které instalují službu IIS, spustí službu IIS a zkopíruje soubor šablony do složky `wwwroot`.

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

V tomto kroku vygenerujete soubor šablony, který použijete jako stránku `default.html`.

Spuštěním následujícího příkazu vygenerujte šablonu:

    chef generate template webserver Default.htm

Přejděte do souboru `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Upravte soubor tak, že přidáte nějaký jednoduchý kód HTML *Hello World* a pak soubor uložte.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Nahrání kuchařka na server s

V tomto kroku vytvoříte kopii kuchařka, kterou jste vytvořili v místním počítači, a nahrajete ji na hostovaný Server. Po nahrání se kuchařka zobrazí na kartě **zásady** .

    knife cookbook upload webserver

![Výsledky instalace kuchařka na server s](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Nasazení virtuálního počítače pomocí nůž Azure

Nasaďte virtuální počítač Azure a použijte `Webserver` kuchařka pomocí příkazu `knife`.

Příkaz `knife` také nainstaluje webovou službu IIS a výchozí webovou stránku.

```bash
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
```

Příklad příkazu `knife` vytvoří *Standard_DS2_v2* virtuální počítač s Windows serverem 2016 nainstalovaným v oblasti západní USA. Upravte tyto hodnoty podle potřeb vaší aplikace.

Po spuštění příkazu přejděte na Azure Portal a podívejte se, jak se Váš počítač začne zřizovat.

![Zřízený virtuální počítač](./media/chef-automation/virtual-machine-being-provisioned.png)

Zobrazí se příkazový řádek další.

![Výstup nůž při vytváření virtuálního počítače](./media/chef-automation/knife-output-when-creating-vm.png)

Po dokončení nasazení se zobrazí veřejná IP adresa nového virtuálního počítače. Pokud chcete zobrazit nový web, vložte tuto hodnotu do webového prohlížeče. Po nasazení virtuálního počítače jsme otevřeli port 80, takže by měl být externě dostupný.   

![Testuje se virtuální počítač.](./media/chef-automation/testing-the-virtual-machine.png)

Tento příklad používá Creative kód HTML.

Můžete také zobrazit stav uzlu [Spravovat](https://manage.chef.io/). 

![Zobrazení stavu uzlu](./media/chef-automation/viewing-node-status.png)

Nezapomeňte se také připojit prostřednictvím relace RDP z Azure Portal přes port 3389.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [V Azure](/azure/chef/)