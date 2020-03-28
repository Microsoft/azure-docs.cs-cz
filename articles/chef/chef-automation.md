---
title: Úvodní příručka – Konfigurace virtuálního počítače s Windows v Azure pomocí Chefa
description: V tomto rychlém startu se dozvíte, jak pomocí Chefa nasadit a nakonfigurovat virtuální počítač s Windows v Azure.
keywords: šéfkuchař, azurový, devops, virtuální stroj
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77590066"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Úvodní příručka – Konfigurace virtuálního počítače s Windows v Azure pomocí Chefa

Chef umožňuje poskytovat automatizaci a požadované konfigurace stavu.

Díky nejnovější verzi cloudového rozhraní API poskytuje Chef bezproblémovou integraci s Azure, což vám dává možnost zřazovat a nasazovat stavy konfigurace pomocí jediného příkazu.

V tomto článku nastavíte prostředí Chef pro zřízení virtuálních počítačů Azure a projít vytvoření zásady nebo kuchařka a pak nasazení této kuchařky do virtuálního počítače Azure.

## <a name="chef-basics"></a>Základy šéfkuchaře

Než začnete s tímto článkem, [přečtěte si základní koncepty Chef](https://www.chef.io/chef).

Následující diagram znázorňuje architekturu chef vysoké úrovně.

![Šéfkuchařská architektura](media/chef-automation/chef-architecure.png)

Šéfkuchař má tři hlavní architektonické složky: 
- Chef Server - bod správy a existují dvě možnosti pro Chef Server: hostované řešení nebo místní řešení.
- Chef Client (uzel) - Agent, který je na serverech, které spravujete.
- Chef Workstation - Název pracovní stanice pro správce (kde vytváříte zásady a spouštějte příkazy pro správu) a softwarový balíček nástrojů Chef.

Obecně se zobrazí **vaše pracovní stanice** jako umístění, kde spustíte příkazy a Chef **Workstation** pro softwarový balíček.

Například stáhnete příkaz nůž jako součást **pracovní stanice chef**, ale spustíte příkazy nože z pracovní **stanice** pro správu infrastruktury.

Šéfkuchař také používá koncepty *kuchařek* a *receptů*. Tyto podmínky jsou zásady, které jsou definovány a použity na serverech, v uvedeném pořadí.

## <a name="preparing-your-workstation"></a>Příprava pracovní stanice

Nejprve připravte pracovní stanici vytvořením adresáře pro uložení konfiguračních souborů chef a kuchařek.

Vytvořte adresář s názvem `C:\Chef`.

Stáhněte a nainstalujte nejnovější verzi [rozhraní příkazového příkazu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) do pracovní stanice.

## <a name="configure-azure-service-principal"></a>Konfigurace instančního objektu Azure

Budeme používat instanční objekt, který nám pomůže vytvářet prostředky Azure z naší pracovní stanice Chef.  Chcete-li vytvořit příslušný objekt zabezpečení služeb s požadovanými oprávněními, spusťte v prostředí PowerShellu následující příkazy:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Poznamenejte si vaše SubscriptionID, TenantID, ClientID a clientSecret (heslo, které jste nastavili dříve v tomto kurzu), jak budete potřebovat tyto hodnoty. 

## <a name="setup-chef-server"></a>Instalační server chef

Tato příručka předpokládá, že se zaregistrujete do hostovaného šéfkuchaře.

Pokud ještě nepoužíváte chef server, můžete:

* Zaregistrujte se do [hostovaného šéfkuchaře](https://manage.chef.io/signup), což je nejrychlejší způsob, jak začít s Chef.
* Nainstalujte samostatný Chef Server na linux-založené stroje, podle [pokynů pro instalaci](https://docs.chef.io/install_server.html) od Chef [Docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Vytvoření účtu hostovaného šéfkuchaře

Zaregistrujte si účet Hosted Chef [zde](https://manage.chef.io/signup).

Během procesu registrace budete vyzváni k vytvoření nové organizace.

![Okno Vytvořit organizaci](media/chef-automation/create-organization.png)

Po vytvoření organizace si stáhněte startovní soupravu.

![Konfigurace chef](media/chef-automation/configure-chef.png)

> [!NOTE]
> Pokud se zobrazí výzva s upozorněním, že vaše klíče budou resetovány, je v pořádku pokračovat, protože zatím nemáme žádnou existující infrastrukturu.
>

Tento soubor zip startovací sady obsahuje konfigurační soubory vaší organizace a uživatelský klíč v adresáři. `.chef`

Musí `organization-validator.pem` být staženy samostatně, protože se jedná o soukromý klíč a soukromé klíče by neměly být uloženy na serveru chef. V [části Chef Manage](https://manage.chef.io/)přejděte do části Správa a vyberte možnost Obnovit ověřovací klíč, který vám poskytne soubor, který si můžete stáhnout samostatně. Uložte soubor do c:\chef.

### <a name="configuring-your-chef-workstation"></a>Konfigurace pracovní stanice Chef

Extrahujte obsah `chef-starter.zip` `c:\chef`to .

Zkopírujte všechny `chef-starter\chef-repo\.chef` soubory `c:\chef` pod do adresáře.

Zkopírujte `organization-validator.pem` soubor `c:\chef`do aplikace , `c:\Downloads`pokud je uložen v .

Adresář by nyní měl vypadat podobně jako v následujícím příkladu.

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

Nyní byste měli mít pět souborů a čtyři adresáře (včetně prázdného adresáře chef-repo) v kořenovém adresáři c:\chef.

### <a name="edit-kniferb"></a>Upravit knife.rb

PEM soubory obsahují vaše organizace a administrativní soukromé klíče pro komunikaci a knife.rb soubor obsahuje konfiguraci nože. Budeme muset upravit soubor knife.rb.

Otevřete soubor knife.rb v editoru dle vašeho výběru. Nezměněný soubor by měl vypadat nějak takto:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Přidejte do souboru knife.rb následující informace a vyměňte zástupné symboly za své:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Tyto řádky zajistí, že nůž odkazuje `c:\chef\cookbooks`na kuchařky adresář pod .

Soubor `knife.rb` by nyní měl vypadat podobně jako v následujícím příkladu:

![Příklad souboru nože](./media/chef-automation/knife-file-example.png)

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

## <a name="install-chef-workstation"></a>Instalace pracovní stanice Chef

Dále [stáhněte a nainstalujte pracovní stanici Chef](https://downloads.chef.io/chef-workstation/).

Nainstalujte pracovní stanici Chef workstation do výchozího umístění.

Na ploše uvidíte CW PowerShell. Tento nástroj se používá k interakci s produkty Chef. Cw PowerShell zpřístupňuje nové příkazy, například `chef-run` příkazy `chef`chef CLI (například ). Podívejte se na nainstalovanou verzi Chef `chef -v`Workstation a chef nástroje s . Verzi pracovní stanice můžete také zkontrolovat tak, že v aplikaci Chef Workstation vyberete **možnost O pracovní stanici** Chef.

`chef --version`by měl vrátit něco jako:

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
> Pořadí cesty je důležité! Pokud vaše opscode cesty nejsou ve správném pořadí, dojde k problémům.
>

Než budete pokračovat, restartujte pracovní stanici.

### <a name="install-knife-azure"></a>Instalace nože Azure

Tento kurz předpokládá, že používáte Správce prostředků Azure k interakci s virtuálním počítačem.

Nainstalujte rozšíření Knife Azure, které zahrnuje modul ový plugin Azure.

Spusťte následující příkaz.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argument `–-pre` zajišťuje, že obdržíte nejnovější RC verzi modulu plug-in Knife Azure, který poskytuje přístup k nejnovější sadě api.
>
>

Je pravděpodobné, že několik závislostí bude také nainstalována ve stejnou dobu.

![Výstup z instalace nože-azure](./media/chef-automation/install-knife-azure.png)

Chcete-li zajistit, aby bylo vše správně nakonfigurováno, spusťte následující příkaz.

    knife azurerm server list

Pokud je vše nakonfigurované správně, zobrazí se seznam dostupných imitacích Azure.

Blahopřejeme. Vaše pracovní stanice je nastavena!

## <a name="creating-a-cookbook"></a>Vytvoření kuchařky

Kuchařka je používána chef definovat sadu příkazů, které chcete spustit na spravovaného klienta. Vytvoření kuchařka je jednoduché, `chef generate cookbook` stačí použít příkaz pro generování kuchařka šablony. Tato kuchařka je určen pro webový server, který automaticky nasazuje službu IIS.

Pod `C:\Chef directory`vaším , spusťte následující příkaz.

    chef generate cookbook webserver

Tento příkaz vygeneruje sadu souborů pod adresářem C:\Chef\cookbooks\webserver. Dále definujte sadu příkazů pro klienta Chef spustit na spravovaném virtuálním počítači.

Příkazy jsou uloženy v souboru default.rb. V tomto souboru definujte sadu příkazů, které instalují službu IIS, spouští službu IIS a zkopírují soubor šablony do `wwwroot` složky.

Upravte soubor C:\chef\cookbooks\webserver\recipes\default.rb a přidejte následující řádky.

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

Po dokončení souboru uložte.

## <a name="creating-a-template"></a>Vytvoření šablony

V tomto kroku vygenerujete soubor šablony, `default.html` který se použije jako stránka.

Chcete-li vytvořit šablonu, spusťte následující příkaz:

    chef generate template webserver Default.htm

Přejděte `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` k souboru. Upravte soubor přidáním jednoduchého kódu *Hello World* HTML a pak soubor uložte.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Nahrajte kuchařku na chef server

V tomto kroku vytvoříte kopii kuchařky, kterou jste vytvořili v místním počítači, a nahrajete ji na hostovaný server chef. Po nahrání se kuchařka zobrazí na kartě **Zásady.**

    knife cookbook upload webserver

![Výsledky instalace kuchařky na Chef Server](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Nasazení virtuálního počítače s Knife Azure

Nasaďte virtuální počítač Azure `Webserver` a použijte `knife` kuchařku pomocí příkazu.

Příkaz `knife` také nainstaluje webovou službu služby IIS a výchozí webovou stránku.

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

Příklad `knife` příkazu vytvoří *Standard_DS2_v2* virtuální mů e-Standard_DS2_v2 s nainstalovaným systémem Windows Server 2016 v oblasti Západní USA. Upravte tyto hodnoty podle potřeb aplikace.

Po spuštění příkazu, přejděte na portál Azure a uvidíte, že váš počítač začne zřídit.

![Zřizování virtuálního počítače](./media/chef-automation/virtual-machine-being-provisioned.png)

Dále se zobrazí příkazový řádek.

![Výstup nože při vytváření virtuálního počítače](./media/chef-automation/knife-output-when-creating-vm.png)

Po dokončení nasazení se zobrazí veřejná IP adresa nového virtuálního počítače. Vložte tuto hodnotu do webového prohlížeče a zobrazte nový web. Když jsme nasadili virtuální počítač, otevřeli jsme port 80, takže by měl být k dispozici externě.   

![Testování virtuálního počítače](./media/chef-automation/testing-the-virtual-machine.png)

Tento příklad používá kód HTML kreativy.

Můžete také zobrazit stav uzlu [Chef Manage](https://manage.chef.io/). 

![Zobrazení stavu uzlu](./media/chef-automation/viewing-node-status.png)

Nezapomeňte, že se můžete připojit také prostřednictvím relace RDP z portálu Azure přes port 3389.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Chef v Azure](/azure/chef/)