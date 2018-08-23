---
title: Nasazení virtuálních počítačů Azure pomocí Chefu | Dokumentace Microsoftu
description: Zjistěte, jak pomocí nástroje Chef provedete nasazení automatizovaných virtuálního počítače a konfigurace v Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: jeconnoc
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: 3a6fbc8410dbc5aec4522f0972a29c67527edb23
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42057470"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatizace nasazení virtuálních počítačů Azure pomocí Chefu
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef je skvělým nástrojem k zajištění automatizace a požadované konfigurace stavu.

S nejnovějším cloudových rozhraní api verze, Chef poskytuje bezproblémovou integraci s Azure, což vám umožní zřídit a nasadit stavům konfigurace prostřednictvím jediného příkazu.

V tomto článku se nastavení prostředí Chef ke zřízení virtuálních počítačů Azure a vás provedou vytvořením zásady nebo "Kuchařka" a poté nasaďte tento kuchařka pro virtuální počítač Azure.

Můžeme začít!

## <a name="chef-basics"></a>Základní informace o chefu
Než začnete, [přečtěte si o základních konceptech služby Chef](http://www.chef.io/chef). 

Následující diagram znázorňuje základní architektura Chef.

![][2]

Chef má tři hlavní součásti architektury: Chef, Chef klienta (uzel), Chef pracovní stanici a serveru.

Chef serveru je bod správy a existují dvě možnosti pro Chef Server: hostovaného řešení nebo místního řešení. Použijeme hostovaného řešení.

Klient Chef (node) je agent, který je umístěný na serverech, které spravujete.

Pracovní stanice Chef je pracovní stanice správce, můžeme vytvořit zásady a spouštět příkazy pro správu. Můžeme spouštět **nůž** příkazu z pracovní stanice Chef ke správě infrastruktury.

Existuje také koncept "Návody" a "Recepty". Toto jsou účinně zásady jsme definovat a použít na servery.

## <a name="preparing-the-workstation"></a>Příprava pracovní stanice
Umožňuje nejprve pro přípravu pracovní stanici. Používám standardní pracovní stanice Windows. Potřebujeme vytvořit adresář pro uložení konfiguračních souborů a návody.

Nejprve vytvořte adresář s názvem C:\chef.

Potom vytvořte druhý adresář s názvem c:\chef\cookbooks.

Nyní potřebujeme stáhnout soubor nastavení služby Azure, takže Chef může komunikovat s předplatným služby Azure.

Stáhněte si vaše publikovat nastavení pomocí Azure Powershellu [Get-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0) příkazu. 

Uložte soubor nastavení publikování v C:\chef.

## <a name="creating-a-managed-chef-account"></a>Vytváří se spravovaný účet Chef
Zaregistrujte si účet hostovaný Chef [tady](https://manage.chef.io/signup).

Během procesu registrace zobrazí se výzva k vytvořte novou organizaci.

![][3]

Po vytvoření vaší organizaci, stáhněte si starter kit.

![][4]

> [!NOTE]
> Pokud se zobrazí výzva s upozorněním, že vaše klíče se resetuje, je ok pokračovat, protože máme k dispozici žádné existující infrastruktury ještě nakonfigurované.
> 
> 

Tento soubor zip starter kit obsahuje vaše organizace konfigurační soubory a klíče.

## <a name="configuring-the-chef-workstation"></a>Konfigurace pracovní stanice Chef
Extrahujte obsah starter.zip chef k C:\chef.

Zkopírujte všechny soubory pod chef starter\chef úložišti\.chef k adresáři c:\chef.

Adresáře by teď měl vypadat přibližně jako v následujícím příkladu.

![][5]

Teď byste měli mít čtyři soubory v kořenové složce c:\chef včetně souboru publikování Azure.

Soubory PEM obsahují vaší organizace a privátního klíče správce pro komunikaci, zatímco knife.rb soubor obsahuje konfiguraci nůž. Budeme muset upravit soubor knife.rb.

Otevřete soubor v editoru podle výběru a upravit tak, že odeberete "cookbook_path" /.. / z cesty, takže se zobrazuje, jak je ukázáno dále.

    cookbook_path  ["#{current_dir}/cookbooks"]

Také přidejte následující řádek, odráží název vašeho Azure souboru s nastavením publikování.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Váš soubor knife.rb by teď měl vypadat podobně jako v následujícím příkladu.

![][6]

Tyto řádky zajistí, že odkazuje na adresář návody v c:\chef\cookbooks nůž a také používá naše soubor nastavení publikování v Azure během operací Azure.

## <a name="installing-the-chef-development-kit"></a>Instalace sady Chef Development Kit
Další [stáhněte a nainstalujte](http://downloads.getchef.com/chef-dk/windows) ChefDK (Chef Development Kit) nastavit pracovní stanice Chef.

![][7]

Ve výchozím umístění c:\opscode nainstalujte. Tato instalace zabere přibližně 10 minut.

Potvrďte, že vaše proměnná PATH obsahuje položky pro C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Pokud nejsou existuje, ujistěte se, že přidáte tyto cesty!

*VŠIMNĚTE SI, ŽE JE DŮLEŽITÉ POŘADÍ CESTY!* Pokud vaše opscode cesty nejsou ve správném pořadí budete mít problémy.

Než budete pokračovat, restartujte počítač pracovní stanice.

V dalším kroku nainstalujeme rozšíření nůž Azure. Tímto způsobem nůž "Modulu plug-in Azure".

Spusťte následující příkaz.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argument – pre zajistí, že přijímáte RC nejnovější modul plug-in Azure nůž, který poskytuje přístup k nejnovější sadu rozhraní API.
> 
> 

Je pravděpodobné, že počet závislostí se nainstaluje taky ve stejnou dobu.

![][8]

K zajištění, že je všechno správně nastavené, spusťte následující příkaz.

    knife azure image list

Pokud je vše nastaveno správně, zobrazí se seznam dostupných imagí Azure procházení.

Blahopřejeme. Je nastavený pracovní stanici!

## <a name="creating-a-cookbook"></a>Kuchařka pro vytváření
Kuchařka používá Chef k definování sady příkazů, které chcete spustit na spravovaného klienta. Vytváření kuchařka je jednoduché a používáme **generovat chef cookbook** příkazu vygenerujte kuchařka šablony. Můžu bude volat kuchařka webovém serveru jako chtěl bych zásadu, která automaticky nasadí služby IIS.

V adresáři C:\Chef spusťte následující příkaz.

    chef generate cookbook webserver

Tím se vygeneruje sadu souborů v adresáři C:\Chef\cookbooks\webserver. Teď musíme definovat sadu příkazů, že rádi bychom Chef klienta ke spuštění na spravovaný virtuální počítač.

Příkazy jsou uloženy v souboru default.rb. V tomto souboru můžu budete definovat sadu příkazů, které nainstaluje službu IIS, spustí službu IIS a zkopíruje soubor šablony do složky wwwroot.

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
Jak už jsme zmínili dřív, musíte vygenerovat soubor šablony, který se použije jako stránka default.html.

Spusťte následující příkaz pro vytvoření šablony.

    chef generate template webserver Default.htm

Teď přejděte k souboru C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Upravte soubor tak, že přidáte jednoduchým kódem "Hello World" HTML a pak soubor uložte.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Nahrát na Server Chef Cookbook
V tomto kroku jsme udělali kopii Cookbook, který jsme vytvořili v místním počítači a pak ho nahrát na Server hostované Chef. Po nahrání kuchařka se zobrazí v části **zásady** kartu.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Nasazení virtuálního počítače s nůž Azure
Nyní jsme nasadí virtuální počítač Azure a použít kuchařka "Webový server", který se nainstaluje na webové služby a výchozí webová stránka služby IIS.

Chcete-li to provést, použijte **vytvořit server azure nůž** příkazu.

Jsem, že příklad příkazu se zobrazí další.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Parametry není potřeba vysvětlovat. Nahraďte konkrétní proměnných a spustit.

> [!NOTE]
> Pomocí příkazového řádku můžu jsem také automatizace pravidel filtru koncový bod sítě s použitím parametru – tcp koncových bodů. Můžu otevřeli porty 80 a 3389 a zajistit tak přístup do své webové stránky a relaci protokolu RDP.
> 
> 

Po spuštění příkazu Přejít na web Azure Portal a zobrazí se váš počítač zahájit zřizování.

![][13]

Příkazový řádek se následně zobrazí.

![][10]

Po dokončení nasazení se jsme by mělo být možné se připojit k webové službě přes port 80 jako když jsme zřídili virtuální počítač pomocí příkazu nůž Azure jsme měli otevřený port. Tento virtuální počítač je jenom virtuální počítač ve svojí cloudové služby, budete připojení s adresou url cloudové služby.

![][11]

Jak je vidět, mi dává creative kód HTML.

Nezapomeňte, že jsme také se můžete připojit přes relaci protokolu RDP z portálu Azure portal přes port 3389.

Doufám, že byla užitečná. Přejděte a začněte infrastruktury jako kódu cestě s Azure ještě dnes!

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


<!--Link references-->
