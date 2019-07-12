---
title: Nasazení virtuálních počítačů Azure pomocí Chefu | Dokumentace Microsoftu
description: Zjistěte, jak pomocí nástroje Chef provedete nasazení automatizovaných virtuálního počítače a konfigurace v Microsoft Azure
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
ms.devlang: na
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 74b92c277b1d6eaa0984e55a70459bad59c2bf84
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719271"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatizace nasazení virtuálních počítačů Azure pomocí Chefu

Chef je skvělým nástrojem k zajištění automatizace a požadované konfigurace stavu.

S nejnovější verzí cloudového rozhraní API Chef poskytuje bezproblémovou integraci s Azure, což vám umožní zřídit a nasadit stavům konfigurace prostřednictvím jediného příkazu.

V tomto článku se nastavení prostředí Chef ke zřízení virtuálních počítačů Azure a vás provedou vytvořením zásady nebo "Kuchařka" a poté nasaďte tento kuchařka pro virtuální počítač Azure.

## <a name="chef-basics"></a>Základní informace o chefu
Než začnete, [přečtěte si o základních konceptech služby Chef](https://www.chef.io/chef).

Následující diagram znázorňuje základní architektura Chef.

![][2]

Chef má tři hlavní součásti architektury: Chef serveru, Chef klienta (uzel) a pracovní stanice Chef.

Chef serveru je bod správy a existují dvě možnosti pro Chef Server: hostovaného řešení nebo místního řešení.

Klient Chef (node) je agent, který je umístěný na serverech, které spravujete.

Pracovní stanice Chef, což je název pro obě správce pracovní stanice, kde vytvářet zásady a provádět příkazy pro správu a softwarového balíčku nástroje Chef.

Obecně platí, zobrazí se vám _pracovní stanice_ jako umístění, kde můžete provádět akce a _pracovní stanice Chef_ pro balíček softwaru.
Například, stáhněte si příkazu nůž jako součást _Chef pracovní stanice_, ale spouštíte příkazy nůž z _pracovní stanice_ se správou infrastruktury.

Chef také používá koncepty "Návody" a "Recepty", které jsou zásady jsme definovat a použít na servery.

## <a name="preparing-your-workstation"></a>Příprava pracovní stanice

Pracovní stanice pro přípravu nejprve vytvoříte adresář pro uložení konfiguračních souborů Chef a návody.

Vytvořte adresář s názvem C:\Chef.

Stáhněte a nainstalujte nejnovější [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) verze k pracovní stanici.

## <a name="configure-azure-service-principal"></a>Konfigurace instančního objektu Azure

V nejjednodušší podmínek a instanční objekt Azure je účet služby.   Jsme nám vytváření prostředků Azure z našich Chef pracovní stanice pomocí instančního objektu.  Chcete-li vytvořit odpovídající objekt služby s požadovanými oprávněními potřebujeme spusťte následující příkazy powershellu:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Věnujte prosím poznamenejte si ID předplatného, ID Tenanta, ID klienta a tajný klíč klienta (heslo, které jste nastavili výše), budete ho potřebovat později. 

## <a name="setup-chef-server"></a>Nastavte Chef Server

Tento průvodce to předpokládá, že se zaregistrujete hostované Chef.

Pokud ještě není používáte Chef Server, vám umožňuje:

* Zaregistrujte si [hostované Chef](https://manage.chef.io/signup), což je nejrychlejší způsob, jak začít pracovat s Chef.
* Nainstalovat samostatnou Chef serveru na počítači s linuxem, následující [pokyny k instalaci](https://docs.chef.io/install_server.html) z [Chef dokumentace](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Vytváří se účet hostovaný Chef

Zaregistrujte si účet hostovaný Chef [tady](https://manage.chef.io/signup).

Při registraci zobrazí se výzva k vytvořte novou organizaci.

![][3]

Po vytvoření vaší organizaci, stáhněte si starter kit.

![][4]

> [!NOTE]
> Pokud se zobrazí výzva s upozorněním, že vaše klíče se resetuje, je možné pokračovat, protože máme k dispozici žádné existující infrastruktury ještě nakonfigurované.
>

Tento soubor zip starter kit obsahuje vaše organizace konfigurační soubory a klíče uživatele v `.chef` adresáře.

`organization-validator.pem` Musí stáhnout samostatně, protože je privátní klíč a privátní klíče by neměly být uloženy na serveru Chef. Z [Chef spravovat](https://manage.chef.io/), přejděte do části Správa a vyberte možnost "Obnovit ověřovací klíč", který poskytuje soubor si můžete stáhnout samostatně. Uložte soubor do c:\chef.

### <a name="configuring-your-chef-workstation"></a>Konfigurace pracovní stanice Chef

Extrahujte obsah starter.zip chef k c:\chef.

Zkopírujte všechny soubory pod chef starter\chef úložišti\.chef k adresáři c:\chef.

Kopírovat `organization-validator.pem` soubor c:\chef, pokud je uložen v c:\Downloads

Adresáře by teď měl vypadat přibližně jako v následujícím příkladu.

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

Teď byste měli mít pět souborů a čtyři adresáře (včetně prázdné úložiště chef adresáře) v kořenové složce c:\chef.

### <a name="edit-kniferb"></a>Upravit knife.rb

Soubory PEM obsahují vaší organizace a správy privátních klíčů pro komunikaci a knife.rb soubor obsahuje konfiguraci nůž. Budeme muset upravit soubor knife.rb.

Otevřete soubor knife.rb v editoru podle vašeho výběru. Beze změny souboru by měla vypadat podobně jako:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Na vaše knife.rb, přidejte následující informace:

validation_client_name "myorg ověření"

validation_key "#{current_dir}/myorg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


Tyto řádky zajistí, že odkazuje na adresář návody v c:\chef\cookbooks nůž a také používá Azure instanční objekt, který jste vytvořili během operací Azure.

Váš soubor knife.rb by teď měl vypadat podobně jako v následujícím příkladu:

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

## <a name="install-chef-workstation"></a>Nainstalujte pracovní stanice Chef

Dále [stáhnout a nainstalovat](https://downloads.chef.io/chef-workstation/) Chef pracovní stanice.
Pracovní stanice Chef výchozí umístění instalace. Tato instalace může trvat několik minut.

Na ploše uvidíte "Powershellu SH", což je prostředí načteny pomocí nástroje, které budete potřebovat pro interakci s produkty Chef. SH Powershellu zpřístupní nové příkazy ad hoc, jako například `chef-run` příkazy i tradiční Chef rozhraní příkazového řádku, jako například `chef`. Zobrazit nainstalované verze aplikace Chef pracovní stanice a nástrojů Chefu s `chef -v`. Můžete také zkontrolujte pracovní stanice verzi výběrem "O Chefu pracovní stanice" z aplikace pracovní stanice Chef.

`chef --version` by měla vrátit vypadat:

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
> Důležité je pořadí cesty! Pokud vaše opscode cesty nejsou ve správném pořadí budete mít problémy.
>

Než budete pokračovat, restartujte počítač pracovní stanice.

### <a name="install-knife-azure"></a>Instalace Azure nůž

V tomto kurzu se předpokládá, že používáte Azure Resource Manageru k interakci s virtuálním počítačem.

Nainstalujte rozšíření Azure nůž. Tímto způsobem nůž "Modulu plug-in Azure".

Spusťte následující příkaz.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argument – pre zajistí, že přijímáte RC nejnovější modul plug-in Azure nůž, který poskytuje přístup k nejnovější sadu rozhraní API.
>
>

Je pravděpodobné, že počet závislostí se nainstaluje taky ve stejnou dobu.

![][8]

K zajištění, že je všechno správně nastavené, spusťte následující příkaz.

    knife azurerm server list

Pokud je vše nastaveno správně, zobrazí se seznam dostupných imagí Azure procházení.

Blahopřejeme. Pracovní stanice je nastavený!

## <a name="creating-a-cookbook"></a>Kuchařka pro vytváření

Kuchařka používá Chef k definování sady příkazů, které chcete spustit na spravovaného klienta. Vytváření kuchařka je jednoduché, stačí použít **generovat chef cookbook** příkazu vygenerujte kuchařka šablony. Je toto kuchařka pro webový server, který automaticky nasadí služby IIS.

V adresáři C:\Chef spusťte následující příkaz.

    chef generate cookbook webserver

Tento příkaz generuje sadu souborů v adresáři C:\Chef\cookbooks\webserver. Dále definujte sadu příkazů pro klienta Chef se promítnou u spravovaný virtuální počítač.

Příkazy jsou uloženy v souboru default.rb. V tomto souboru definujte sadu příkazů, které nainstaluje službu IIS, spustí službu IIS a zkopíruje soubor šablony do složky wwwroot.

Upravit soubor C:\chef\cookbooks\webserver\recipes\default.rb a přidejte následující řádky.

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

Až budete hotovi, uložte soubor.

## <a name="creating-a-template"></a>Vytvoření šablony
V tomto kroku vytvoříte soubor šablony chcete použít jako stránce default.html.

Spusťte následující příkaz pro vytvoření šablony:

    chef generate template webserver Default.htm

Přejděte `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` souboru. Upravte soubor tak, že přidáte jednoduchým kódem "Hello World" HTML a pak soubor uložte.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Nahrát na Server Chef Cookbook
V tomto kroku provedete kopii Cookbook, které jste vytvořili v místním počítači a nahrát na Server hostované Chef. Po nahrání kuchařka je zobrazen **zásady** kartu.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Nasazení virtuálního počítače s nůž Azure
Nasazení virtuálního počítače Azure a použít kuchařka "Webový server", který se nainstaluje na webové služby a výchozí webová stránka služby IIS.

Chcete-li to provést, použijte **server nůž azurerm vytvořit** příkazu.

Příklad příkazu se zobrazí další.

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


Výše uvedený příklad vytvoří Standard_DS2_v2 virtuálního počítače s Windows serverem 2016 nainstalovaný v oblasti západní USA. Nahraďte konkrétní proměnných a spustit.

> [!NOTE]
> Pomocí příkazového řádku můžu jsem také automatizace pravidel filtru koncový bod sítě s použitím parametru – tcp koncových bodů. Můžu otevřeli porty 80 a pro poskytování přístupu k webové stránce a relaci RDP 3389.
>
>

Po spuštění příkazu přejdete na web Azure Portal najdete v článku váš počítač zahájit zřizování.

![][15]

Příkazový řádek se následně zobrazí.

![][16]

Po dokončení nasazení veřejnou IP adresu nového virtuálního počítače se zobrazí po dokončení nasazení, můžete zkopírovat to a vložte ji do webového prohlížeče a zobrazit web, který jste nasadili. Když jsme nasadili virtuální počítač otevřeme port 80, měla by být dostupná externě.   

![][11]

Tento příklad používá creative kódu HTML.

Můžete také zobrazit uzlu stav [Chef spravovat](https://manage.chef.io/). 

![][17]

Nezapomeňte, že můžete také připojit přes relaci protokolu RDP z portálu Azure portal přes port 3389.

Děkuju! Přejděte a začněte infrastruktury jako kódu cestě s Azure ještě dnes!

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
