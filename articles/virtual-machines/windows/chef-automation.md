---
title: Nasazení virtuálního počítače Azure s Chef | Microsoft Docs
description: Další informace o použití Chef provedete nasazení automatizované virtuálního počítače a konfigurace v Microsoft Azure
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
ms.openlocfilehash: 36293c41219a1b42d75850fa66d3c631637bb855
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30916244"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatizace nasazení virtuálních počítačů Azure pomocí Chefu
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef je skvělý nástroj pro doručení automatizace a požadovaného stavu konfigurace.

Na nejnovější cloudových rozhraní api verze, Chef poskytuje bezproblémovou integraci s Azure, což vám umožní zřídit a nasadit stavům konfigurace prostřednictvím jeden příkaz.

V tomto článku nastavíte prostředí Chef provede procesem vytvoření zásadu nebo "Kuchařka" a pak tento kuchařka nasazení pro virtuální počítač Azure a zřizovat virtuální počítače Azure.

Můžeme začít!

## <a name="chef-basics"></a>Základy Chef
Než začnete, [zkontrolujte se základními koncepty Chef](http://www.chef.io/chef). 

Následující diagram znázorňuje architekturu Chef vysoké úrovně.

![][2]

Chef má třemi hlavními komponentami architektury: Chef, Chef klienta (uzel), Chef pracovní stanici a serveru.

Je Chef Server bod správy a existují dvě možnosti pro Chef Server: hostované řešení nebo v případě místních řešení. Použijeme hostované řešení.

Klient Chef (uzel) je agenta, která se nachází na serverech, které spravujete.

Pracovní stanice Chef je pracovní stanice správce, kde budeme vytvářet zásady a spustit příkazy pro správu. Jsme spustit **nůž** příkazu z Chef pracovní stanice ke správě infrastruktury.

Je také koncepci "Cookbooks" a "Recepty". Toto jsou efektivně zásady jsme definovat a použít na servery.

## <a name="preparing-the-workstation"></a>Příprava pracovní stanice
Umožňuje nejprve rychlá příprava pracovní stanice. Používám standardní pracovní stanice systému Windows. Je potřeba vytvořit adresář k uložení konfigurační soubory a cookbooks.

Nejprve vytvořte adresář s názvem C:\chef.

Potom vytvořte druhý adresář s názvem c:\chef\cookbooks.

Nyní potřebujeme stáhnout soubor nastavení Azure, Chef může komunikovat s předplatným služby Azure.

Stáhněte si vaše nastavení pomocí PowerShell Azure publikování [Get-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0) příkaz. 

Uložte soubor nastavení publikování v C:\chef.

## <a name="creating-a-managed-chef-account"></a>Vytvoření spravované Chef účtu
Zaregistrujte si účet hostované Chef [zde](https://manage.chef.io/signup).

Během procesu registrace se zobrazí výzva k vytvoření nové organizace.

![][3]

Po vytvoření vaší organizace, stáhněte si starter kit.

![][4]

> [!NOTE]
> Pokud se zobrazí výzva, upozornění, že vaše klíče bude restartován, je ok pokračovat, protože máme nakonfigurovanou zatím žádná existující infrastrukturu.
> 
> 

Tento soubor zip starter kit obsahuje vaše organizace konfigurační soubory a klíče.

## <a name="configuring-the-chef-workstation"></a>Konfigurace Chef pracovní stanice
Extrahujte obsah chef-starter.zip k C:\chef.

Zkopírujte všechny soubory pod chef. starter\chef úložišti\.chef do vašeho adresáře c:\chef.

Adresáře by měl nyní vypadat podobně jako v následujícím příkladu.

![][5]

Teď byste měli mít čtyři soubory včetně Azure publikování v kořenovém c:\chef.

Soubory PEM obsahují vaší organizace a správy privátních klíčů pro komunikaci při knife.rb soubor obsahuje konfiguraci nůž. Je potřeba upravit soubor knife.rb.

Otevřete soubor ve svém editoru volba a upravit "cookbook_path" odebráním nebo... / z cesty, zdá se, jak ukazuje následující.

    cookbook_path  ["#{current_dir}/cookbooks"]

Také přidejte následující řádek odrážející název vaší Azure soubor nastavení publikování.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Váš soubor knife.rb by teď měl vypadat podobně jako v následujícím příkladu.

![][6]

Tyto řádky zajišťují, že odkazuje na adresář cookbooks v c:\chef\cookbooks nůž a také naše soubor s nastavením publikování Azure používá během operace v Azure.

## <a name="installing-the-chef-development-kit"></a>Instalace Chef Development Kit
Další [stáhněte a nainstalujte](http://downloads.getchef.com/chef-dk/windows) ChefDK (Chef Development Kit) nastavit pracovní stanice Chef.

![][7]

Ve výchozím umístěním c:\opscode nainstalujte. Tato instalace bude trvat přibližně 10 minut.

Potvrďte, že vaše proměnná PATH obsahuje položky pro C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Pokud nejsou existuje, ujistěte se, že přidáte tyto cesty!

*VŠIMNĚTE SI, ŽE JE DŮLEŽITÉ POŘADÍ CESTY!* Pokud vaše opscode cesty nejsou ve správném pořadí budete mít problémy.

Než budete pokračovat, restartujte počítač pracovní stanice.

V dalším kroku se nainstaluje rozšíření nůž Azure. To poskytuje nůž s "Modul plug-in Azure".

Spusťte následující příkaz.

    chef gem install knife-azure ––pre

> [!NOTE]
> – Před argument zajistí, že se vám nejnovější verzi RC sad Azure nůž modul plug-in, který poskytuje přístup k nejnovější sadu rozhraní API.
> 
> 

Je pravděpodobné, že počet závislostí, budou také instalována ve stejnou dobu.

![][8]

K zajištění, že je všechno správně nastavené, spusťte následující příkaz.

    knife azure image list

Pokud se vše správně nakonfigurovaná, zobrazí se seznam dostupných imagí Azure procházení.

Blahopřejeme. Pracovní stanici je nastavený!

## <a name="creating-a-cookbook"></a>Vytváření kuchařka
Kuchařka slouží Chef definovat sadu příkazů, které chcete spustit na spravovaného klienta. Vytváření kuchařka je jednoduchý a používáme **chef generovat kuchařka** příkazu vygenerujte kuchařka šablony. I bude možné volání Moje kuchařka webový server jako nastavit jako zásadu, která automaticky nasadí služby IIS.

V části adresáře C:\Chef spusťte následující příkaz.

    chef generate cookbook webserver

Tím se vygeneruje určitou sadu souborů v adresáři C:\Chef\cookbooks\webserver. Nyní potřebujeme definovat sadu příkazů, že má klient Chef pro spuštění ve spravovaných virtuálního počítače.

Příkazy jsou uloženy v souboru default.rb. V tomto souboru I budete definování sadu příkazů, který nainstaluje službu IIS, spustí službu IIS a zkopíruje soubor šablony do složky wwwroot.

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

Až budete hotoví, uložte soubor.

## <a name="creating-a-template"></a>Vytvoření šablony
Jak jsme už bylo zmíněno dříve, je potřeba generovat soubor šablony, který se použije jako default.html stránky.

Spusťte následující příkaz pro vytvoření šablony.

    chef generate template webserver Default.htm

Nyní přejděte k souboru C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Upravte soubor přidáním jednoduchý "Hello, World" HTML kód a pak soubor uložte.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Nahrát na Server Chef kuchařka
V tomto kroku jsme jsou pořízení kopie Cookbook, který jsme vytvořili v místním počítači a pak ho nahrát na Server hostované Chef. Po nahrání kuchařka se zobrazí v části **zásad** kartě.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Nasazení virtuálního počítače s nůž Azure
Nyní změníme nasadit virtuální počítač Azure a použít kuchařka "Webový server", který nainstaluje službu IIS webové služby a výchozí webové stránce.

Chcete-li to provést, použijte **server nůž azure vytvořit** příkaz.

Se, že příklad příkazu se zobrazí další.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Parametry jsou není potřeba vysvětlovat. Nahraďte konkrétní proměnných a spustit.

> [!NOTE]
> Pomocí příkazového řádku, I se také automatizaci Moje pravidla filtru koncového bodu sítě pomocí parametru koncové body – tcp. I otevřeli porty 80 a 3389 k poskytování přístupu k Moje webová stránka a relaci protokolu RDP.
> 
> 

Po spuštění příkazu, přejděte na portál Azure a zobrazí se váš počítač začít zřizování.

![][13]

Do příkazového řádku se zobrazí na další.

![][10]

Po dokončení nasazení jsme byste měli mít připojení k webové službě přes port 80, jako jsme byl otevřen port při jsme zřízení virtuálního počítače pomocí příkazu nůž Azure. Tento virtuální počítač je pouze virtuální počítač v mé cloudové služby, budete ho připojení s adresou url cloudové služby.

![][11]

Jak vidíte, se zobrazí chybové tvůrčí s vlastního kódu HTML.

Nezapomeňte, že jsme také můžete připojit přes relaci protokolu RDP z portálu Azure přes port 3389.

Ať, že tento byl užitečné! Přejděte a spusťte infrastruktury jako cesty kódu s Azure ještě dnes!

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
