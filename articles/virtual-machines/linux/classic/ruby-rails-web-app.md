---
title: Hostování Ruby, na které webu na virtuální počítač s Linuxem | Microsoft Docs
description: Nastavení a hostitelem Ruby na web na základě které v Azure pomocí virtuální počítač s Linuxem.
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
ms.openlocfilehash: fa19f3dc7dded712102d4ba9b66dd4df1bfd20dd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29397593"
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Webová aplikace Ruby on Rails na virtuálním počítači Azure
Tento kurz ukazuje, jak k hostování Ruby, na které webu v Azure pomocí virtuální počítač s Linuxem.  

V tomto kurzu se ověřila pomocí Ubuntu Server 14.04 LTS. Pokud chcete použít jiný distribuční Linux, může musíte upravit postup instalace které.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../../../azure-resource-manager/resource-manager-deployment-model.md).  Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]
>

## <a name="create-an-azure-vm"></a>Vytvoření virtuálního počítače Azure
Začněte vytvořením virtuálního počítače Azure s bitovou kopii systému Linux.

Pokud chcete vytvořit virtuální počítač, můžete použít portál Azure nebo rozhraní příkazového řádku Azure (CLI).

### <a name="azure-portal"></a>Azure Portal
1. Přihlaste se k [portálu Azure](https://portal.azure.com)
2. Klikněte na tlačítko **vytvořit prostředek**, zadejte do vyhledávacího pole "Ubuntu Server 14.04". Klikněte na položku nalezené. Pro model nasazení vyberte **Classic**, pak klikněte na tlačítko "Vytvořit".
3. V okně základy zadat hodnoty povinných polí: název (pro virtuální počítač), uživatelské jméno, typ ověřování a odpovídající údaje předplatného Azure, skupinu prostředků a umístění.

   ![Vytvořit novou bitovou kopii Ubuntu](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. Po zřízení virtuálního počítače, klikněte na název virtuálního počítače a klikněte na tlačítko **koncové body** v **nastavení** kategorie. Najít koncový bod SSH, uvedené v části **samostatné**.

   ![Výchozí koncový bod](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Azure CLI
Postupujte podle kroků v [vytvořit virtuálním počítači systémem Linux][vm-instructions].

Po zřízení virtuálního počítače, můžete získat koncový bod SSH spuštěním následujícího příkazu:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Nainstalujte na které Ruby
1. Použití SSH se připojit k virtuálnímu počítači.
2. Z relace SSH použijte následující příkazy pro instalaci Ruby ve virtuálním počítači:

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    Instalace může trvat několik minut. Po dokončení, ověřte, zda je nainstalován Ruby pomocí následujícího příkazu:

        ruby -v

3. Použijte následující příkaz k instalaci které:

        sudo gem install rails --no-rdoc --no-ri -V

    Použití--ne rdoc a--ne ri příznaky tak, aby přeskočil instalace dokumentace, která je rychlejší.
    Tento příkaz bude pravděpodobně trvat dlouhou dobu spuštění, takže přidání -V se zobrazí informace o průběhu instalace.

## <a name="create-and-run-an-app"></a>Vytvoření a spuštění aplikace
Při přihlášení se stále pomocí protokolu SSH, spusťte následující příkazy:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

[Nové](http://guides.rubyonrails.org/command_line.html#rails-new) příkaz vytvoří novou aplikaci které. [Server](http://guides.rubyonrails.org/command_line.html#rails-server) příkaz spustí WEBrick webový server, který se dodává s které. (Pro použití v provozním prostředí, by pravděpodobně chcete použít jiný server, jako je například Unicorn nebo osobní.)

Zobrazený výstup by měl vypadat přibližně takto:

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Přidání koncového bodu
1. Přejděte k [portálu Azure] [https://portal.azure.com] a vyberte virtuální počítač.

2. Vyberte **koncové body** v **nastavení** na levé straně okraj stránky.

3. Klikněte na tlačítko **přidat** v horní části stránky.

4. V **přidání koncového bodu** dialogové okno stránky, zadejte následující informace:

   * **Název**: HTTP
   * **Protokol**: TCP
   * **Veřejný port**: 80
   * **Privátní port**: 3000
   * **Plovoucí PÍ adresu**: zakázáno
   * **Seznam řízení přístupu – pořadí**: 1001 nebo jinou hodnotu, která nastavuje prioritu Toto přístupové pravidlo.
   * **Seznam řízení přístupu – název**: allowHTTP
   * **Seznam řízení přístupu – akce**: Povolit
   * **Seznam řízení přístupu – vzdálené podsíti**: 1.0.0.0/16

     Tento koncový bod má veřejný port 80, který bude směrovat provoz privátní port 3000, kde je server které přijímá. Pravidlo seznamu řízení přístupu umožňuje veřejné přenosy na portu 80.

     ![Nový koncový bod](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Klikněte na tlačítko OK uložíte koncový bod.

6. Měli zobrazí zpráva s oznámením **ukládání koncový bod virtuálního počítače**. Jakmile se tato zpráva zmizí, koncový bod je aktivní. Teď může otestovat aplikaci tak, že přejdete na název DNS virtuálního počítače. Web by měl vypadat přibližně takto:

    ![Výchozí stránka které][default-rails-cloud]

## <a name="next-steps"></a>Další postup
V tomto kurzu jste to udělali většinu kroky ručně. V produkčním prostředí by zapsat aplikaci na vývojovém počítači a nasazení virtuálního počítače Azure. Většině produkčních prostředích hostovat které aplikace ve spojení s jiným procesem serveru, jako je například Apache či NginX, která zpracovává požadavků směrování na více instancí, které aplikace a obsluhuje statické prostředky. Další informace najdete v tématu http://rubyonrails.org/deploy/.

Další informace o Ruby, na které, najdete [Ruby, na které příručky][rails-guides].

Použití služeb Azure z Ruby aplikace naleznete v tématu:

* [Ukládání nestrukturovaných dat pomocí objektů BLOB][blobs]
* [Páry klíč – hodnota úložiště pomocí tabulky][tables]
* [Poskytovat obsah velkou šířku pásma s sítě pro doručování obsahu][cdn-howto]

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
