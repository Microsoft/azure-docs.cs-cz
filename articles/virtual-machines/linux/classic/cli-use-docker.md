---
title: Pomocí Docker rozšíření virtuálního počítače pro systém Linux na Azure
description: Popisuje Docker a rozšíření Azure Virtual Machines a ukazuje, jak programově vytvářet virtuální počítače na platformě Azure, které jsou hostitelů docker z příkazového řádku pomocí rozhraní příkazového řádku Azure.
services: virtual-machines-linux
documentationcenter: ''
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: eaff75e3-d929-4931-a4a1-8c377a8e7302
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: rasquill
ms.openlocfilehash: 91f7ea54afce0e94953d4bb01bbb1b33f167fe22
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Použití rozšíření Docker VM z rozhraní příkazového řádku Azure (Azure CLI)
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Informace o rozšíření virtuálního počítače Docker pomocí modelu Resource Manager najdete v tématu [zde](../dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Toto téma popisuje postup vytvoření virtuálního počítače s Docker rozšíření virtuálního počítače z režimu service management (asm) v rozhraní příkazového řádku Azure na jakékoli platformě. [Docker](https://www.docker.com/) je jednou z nejčastěji používané virtualizace přístupy, které používá [Linux kontejnery](http://en.wikipedia.org/wiki/LXC) místo virtuální počítače jako způsob oddělením dat a výpočty na sdílených prostředků. Můžete použít rozšíření virtuálního počítače Docker a [Azure Linux Agent](../../extensions/agent-linux.md) vytvoření Docker virtuálního počítače, který je hostitelem libovolný počet kontejnerů pro vaše aplikace v Azure. Podrobný popis kontejnery a jejich výhody, najdete v sekci [Docker vysokou úroveň tabulí](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Jak používat rozšíření virtuálního počítače Docker s Azure
Pokud chcete používat rozšíření virtuálního počítače Docker s Azure, musíte nainstalovat verzi [rozhraní příkazového řádku Azure](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) vyšší než 0.8.6 (jak psaní tohoto textu aktuální verze je 0.10.0). Rozhraní příkazového řádku Azure můžete nainstalovat na Mac, Linux a Windows.

Proces dokončení použít Docker v Azure je jednoduchý:

* Instalace rozhraní příkazového řádku Azure a jeho závislé součásti na počítači, ze kterého chcete řízení Azure (v systému Windows, bude jím Linux distribuční spuštěná jako virtuální počítač)
* Vytvořit virtuální počítač Docker hostitele v Azure pomocí příkazů Docker rozhraní příkazového řádku Azure
* Použijte místní Docker příkazy ke správě Docker kontejnerů v Docker virtuálního počítače v Azure.

### <a name="install-the-azure-command-line-interface-azure-cli"></a>Nainstalovat Azure rozhraní příkazového řádku (Azure CLI)
Instalace a konfigurace rozhraní příkazového řádku Azure CLI, najdete v části [postup instalace rozhraní příkazového řádku Azure](../../../cli-install-nodejs.md). Pokud chcete ověřit instalaci, zadejte `azure` na příkazovém řádku a po krátké chvíli byste měli vidět obrázky ASCII rozhraní příkazového řádku Azure, který obsahuje seznam základních příkazů, které jsou k dispozici. Pokud instalace fungovala správně, byste měli mít na typ `azure help vm` a zjistíte, že jeden z uvedených příkazů "docker".

> [!NOTE]
> Docker má nástroje pro systém Windows, [počítač Docker](https://docs.docker.com/installation/windows/), který můžete také použít k automatizaci tvorby docker klienta, který můžete použít pro práci s virtuálními počítači Azure jako hostitelů docker.
> 
> 

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Azure CLI pro připojení k účtu Azure
Abyste mohli používat rozhraní příkazového řádku Azure je nutné přidružit přihlašovací údaje účtu Azure pomocí Azure CLI na vaši platformu. V části [jak se připojit k předplatnému Azure](/cli/azure/authenticate-azure-cli) vysvětluje, jak stáhnout a naimportovat vaše **.publishsettings** souboru nebo Azure CLI přidružit id organizace.

> [!NOTE]
> Existují určité rozdíly v chování při použití jednoho nebo jiné metody ověřování, takže nezapomeňte si přečíst dokument výše a pochopit různé funkce.
> 
> 

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Nainstalujete Docker a Docker rozšíření virtuálního počítače pro Azure.
Postupujte podle [pokyny k instalaci Docker](https://docs.docker.com/installation/#installation) nainstalujte Docker místně v počítači.

Použití Docker s virtuální počítače Azure, musí mít bitovou kopii systému Linux používat pro virtuální počítač [agenta virtuálního počítače Azure Linux](../../extensions/agent-linux.md) nainstalována. V současné době existují jenom dva typy bitových kopií, které poskytují toto:

* Ubuntu obrázek z Galerie obrázků Azure nebo
* Vlastní image Linux, kterou jste vytvořili pomocí Azure Linux virtuálního počítače Agent nainstalován a nakonfigurován. V tématu [agenta virtuálního počítače Azure Linux](../../extensions/agent-linux.md) Další informace o tom, jak vytvářet vlastní virtuální počítač s Linuxem pomocí agenta virtuálního počítače Azure.

### <a name="using-the-azure-image-gallery"></a>Pomocí Galerie obrázků Azure
Z Bash nebo relaci Terminálové služby použijte následující příkaz rozhraní příkazového řádku Azure nalézt nejnovější Ubuntu obrázek v galerii virtuálních počítačů použít zadáním

`azure vm image list | grep Ubuntu-14_04`

a vyberte jednu z bitové kopie názvy, například `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`a použijte následující příkaz k vytvoření nového virtuálního počítače pomocí této bitové kopie.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

Kde:

* *&lt;název virtuálního počítače cloudservice&gt;*  je název virtuálního počítače, který se stane kontejner Docker hostitelský počítač v Azure
* *&lt;uživatelské jméno&gt;*  je uživatelské jméno uživatele root výchozí virtuálního počítače
* *&lt;heslo&gt;*  je heslo *uživatelské jméno* účet, který splňuje požadavky na složitost pro Azure.

> [!NOTE]
> V současné době heslo musí být dlouhé alespoň 8 znaků, musí obsahovat jeden malá písmena a jedno velké písmeno, číslo a zvláštní znak například jeden z následujících znaků: `!@#$%^&+=`. Ne, tečka na konci předcházející věty není speciální znak.
> 
> 

Pokud se příkaz úspěšně dokončil, byste měli vidět něco podobného jako následující příkaz, v závislosti na přesné argumentů a možnosti, které jste použili:

![](media/cli-use-docker/dockercreateresults.png)

> [!NOTE]
> Vytvoření virtuálního počítače může trvat několik minut, ale poté, co se zřizují (je hodnota stavu `ReadyRole`) spustí démon Docker (služba Docker) a může připojit k hostiteli kontejner Docker.
> 
> 

Chcete-li otestovat Docker virtuálních počítačů, které jste vytvořili v Azure, zadejte

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

kde *&lt;vm název--použili&gt;* je název virtuálního počítače, který jste použili v volání `azure vm docker create`. Měli byste vidět něco podobného jako následující příkaz, který označuje, zda je virtuální počítač Docker hostitele a běží v Azure a čekání příkazech. 

Nyní můžete zkusit připojení pomocí vašeho klienta docker se získat informace o (v některých nastavení klienta Docker, například v systému Mac, které možná budete muset použít `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Stačí si být jisti, že je všechny pracovní, můžete zkontrolovat ve virtuálním počítači Docker rozšíření:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Ověřování docker hostitele virtuálního počítače
Kromě vytvoření virtuální počítač Docker `azure vm docker create` příkaz také automaticky vytvoří potřebné certifikáty umožňující klientský počítač Docker pro připojení k hostiteli kontejner Azure pomocí protokolu HTTPS a certifikáty jsou uložené na jak klientských a hostitelských počítačích, podle potřeby. Na následné pokusy existující certifikáty jsou opakovaně a sdílet s novým hostitelem.

Ve výchozím nastavení, certifikáty jsou umístěny v `~/.docker`, a Docker bude nakonfigurován ke spuštění na portu **. 2376**. Pokud chcete použít jiný port nebo adresář, pak můžete použít jednu z následujících `azure vm docker create` možnosti příkazového řádku ke konfiguraci vaší kontejner Docker hostitele virtuálního počítače použít jiný port nebo různé certifikáty pro připojení klientů:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Démon Docker v hostiteli je nakonfigurován k naslouchání a ověření připojení klienta na zadaný port pomocí certifikáty generované infrastrukturou `azure vm docker create` příkaz. Tyto certifikáty k získání přístupu k hostiteli Docker musí mít klientský počítač.

> [!NOTE]
> Síťová hostitele se systémem bez těchto certifikátů bude citlivé na každý uživatel, který můžete připojit k počítači. Než změníte výchozí konfiguraci, ujistěte se, že rozumíte rizika pro vaše počítače a aplikace.
> 
> 

## <a name="next-steps"></a>Další postup
* Jste připravení přejít ke [Docker uživatelská příručka] a používat virtuální počítač Docker. Vytvoření virtuálního počítače s povoleným Docker v nového portálu, najdete v tématu [jak používat rozšíření virtuálního počítače Docker s portálem].
* Rozšíření virtuálního počítače Azure Docker podporuje také Docker Compose, která používá soubor deklarativní YAML trvat aplikaci na vývojáře modelován v každém prostředí a generování konzistentní nasazení. V tématu [začít pracovat s Docker a vytvářené definovat a spusťte aplikaci služby kontejneru na virtuální počítač Azure].  

<!--Anchors-->
[Subheading 1]:#subheading-1
[Subheading 2]:#subheading-2
[Subheading 3]:#subheading-3
[Next steps]:#next-steps

[How to use the Docker VM Extension with Azure]:#How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]:#Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]:#Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]:../../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]:../../../app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md
[jak používat rozšíření virtuálního počítače Docker s portálem]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Docker uživatelská příručka]:https://docs.docker.com/userguide/

[začít pracovat s Docker a vytvářené definovat a spusťte aplikaci služby kontejneru na virtuální počítač Azure]:../docker-compose-quickstart.md
