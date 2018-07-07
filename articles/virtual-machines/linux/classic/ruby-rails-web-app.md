---
title: Hostování Ruby on Rails webu na Linuxovém virtuálním počítači | Dokumentace Microsoftu
description: Nastavení a hostovat Ruby on Rails podle webu v Azure pomocí virtuálního počítače s Linuxem.
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: 6ea1d249b7f9aec3a45923b162a97ce7f83d0d31
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901149"
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Webová aplikace Ruby on Rails na virtuálním počítači Azure
Tento kurz ukazuje, jak hostovat Ruby on Rails webu v Azure pomocí virtuálního počítače s Linuxem.  

V tomto kurzu se ověřila pomocí Ubuntu Server 14.04 LTS. Pokud používáte jiné distribuce Linuxu, můžete potřebovat upravit kroky instalace Rails.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../../../azure-resource-manager/resource-manager-deployment-model.md).  Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]
>

## <a name="create-an-azure-vm"></a>Vytvoření virtuálního počítače Azure
Začněte tím, že vytvoříte virtuální počítač Azure s imagí Linuxu.

Pokud chcete vytvořit virtuální počítač, můžete na webu Azure portal nebo rozhraní příkazového řádku Azure (CLI).

### <a name="azure-portal"></a>Azure Portal
1. Přihlaste se [webu Azure portal](https://portal.azure.com)
2. Klikněte na tlačítko **vytvořit prostředek**, do vyhledávacího pole zadejte "Ubuntu Server 14.04". Klikněte na položku vrácenou při hledání. Pro model nasazení vyberte **Classic**, pak klikněte na možnost "Vytvořit".
3. V okně základy zadat hodnoty pro požadované pole: název (pro virtuální počítač), uživatelské jméno, typ ověřování a odpovídající přihlašovací údaje předplatného Azure, skupinu prostředků a umístění.

   ![Vytvořte novou Ubuntu Image](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. Po zřízení virtuálního počítače, klikněte na název virtuálního počítače a klikněte na tlačítko **koncové body** v **nastavení** kategorie. Najít koncový bod SSH, uvedený v části **samostatné**.

   ![Výchozí koncový bod](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Azure CLI
Postupujte podle kroků v [vytvořit s Linuxem spuštěný virtuální počítač][vm-instructions].

Po zřízení virtuálního počítače, získáte koncový bod SSH pomocí následujícího příkazu:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Nainstalovat rámec Ruby on Rails
1. Pomocí SSH se připojte k virtuálnímu počítači.
2. Z relace SSH pomocí následujících příkazů nainstalujte Ruby na virtuálním počítači:

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    Instalace může trvat několik minut. Po dokončení, použijte následující příkaz k ověření, že je nainstalovaná Ruby:

        ruby -v

3. Pomocí následujícího příkazu nainstalujte Rails:

        sudo gem install rails --no-rdoc --no-ri -V

    Použití příznaky--no-rdoc a – ne rezervované instance přeskočte instalaci dokumentace, která je rychlejší.
    Tento příkaz bude pravděpodobně trvat dlouhou dobu spuštění, tak přidání -V se zobrazí informace o průběhu instalace.

## <a name="create-and-run-an-app"></a>Vytvoření a spuštění aplikace
Zůstaňte přihlášení přes protokol SSH, spusťte následující příkazy:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

[Nové](http://guides.rubyonrails.org/command_line.html#rails-new) příkaz vytvoří novou aplikaci Rails. [Server](http://guides.rubyonrails.org/command_line.html#rails-server) příkaz spustí WEBrick webový server, který je součástí Rails. (Pro použití v produkčním prostředí by pravděpodobně chcete použít jiný server, jako je například Unicornu nebo osobní.)

Zobrazený výstup by měl vypadat přibližně takto:

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Přidání koncového bodu
1. Přejděte na [webu Azure portal] [https://portal.azure.com] a vyberte svůj virtuální počítač.

2. Vyberte **koncové body** v **nastavení** na levé straně okraj stránky.

3. Klikněte na tlačítko **přidat** v horní části stránky.

4. V **přidat koncový bod** dialogového okna stránky, zadejte následující informace:

   * **Název**: HTTP
   * **Protokol**: TCP
   * **Veřejný port**: 80
   * **Privátní port**: 3000
   * **Plovoucí adresa PI**: zakázáno
   * **Seznam řízení přístupu – pořadí**: 1001 nebo jinou hodnotu, která nastaví prioritu Toto přístupové pravidlo.
   * **Seznam řízení přístupu – název**: allowHTTP
   * **Seznam řízení přístupu – akce**: Povolit
   * **Seznam řízení přístupu – vzdálené podsítě**: 1.0.0.0/16

     Tento koncový bod má veřejný port 80, který bude směrovat provoz do privátní port 3000, ve kterém naslouchá Rails server. Pravidlo seznamu řízení přístupu umožňuje veřejné provozu na portu 80.

     ![Nový koncový bod](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Kliknutím na OK uložte koncový bod.

6. By měla zobrazit zpráva s oznámením **ukládá se koncový bod virtuálního počítače**. Jakmile se tato zpráva zmizí, koncový bod je aktivní. Nyní může otestovat aplikaci tak, že přejdete na název DNS virtuálního počítače. Web by měl vypadat nějak takto:

    ![Výchozí stránka rails][default-rails-cloud]

## <a name="next-steps"></a>Další postup
V tomto kurzu jste provedli většina kroků ručně. V produkčním prostředí by zápisu do aplikace na vývojovém počítači a nasaďte ji do virtuálního počítače Azure. Většina produkční prostředí také hostování aplikace Rails ve spojení s jiným procesem serveru, jako je Apache nebo NginX, která zpracovává žádosti na několik instancí aplikace Rails směrování a obsluhující statické prostředky. Další informace najdete v tématu http://guides.rubyonrails.org/routing.html.

Další informace o Ruby on Rails, přejděte [Ruby on Rails vodítka][rails-guides].

Chcete-li využívají služby Azure z vaší aplikace v Ruby, naleznete v tématu:

* [Store nestrukturovaných dat s využitím objektů BLOB][blobs]
* [Store páry klíč/hodnota pomocí tabulek][tables]
* [Obsluha širokopásmového obsahu s Content Delivery Network][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal-classic.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
