---
title: Pomocí Docker rozšíření virtuálního počítače pro Linux | Microsoft Docs
description: Popisuje Docker a rozšíření Azure virtuálních počítačů a postupy k vytvoření virtuálních počítačů Azure, které jsou hostitelů docker pomocí rozhraní příkazového řádku Azure v modelu nasazení classic.
services: virtual-machines-linux
documentationcenter: ''
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 19cf64e8-f92c-43ad-a120-8976cd9102ac
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/27/2016
ms.author: rasquill
ms.openlocfilehash: 674bc870bbbf4e076fbd1d88fcc3bf299eccde84
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="using-the-docker-vm-extension-with-the-azure-portal"></a>Pomocí Docker rozšíření virtuálního počítače pomocí portálu Azure
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[Docker](https://www.docker.com/) je jednou z nejčastěji používané virtualizace přístupy, které používá [Linux kontejnery](http://en.wikipedia.org/wiki/LXC) místo virtuální počítače jako způsob oddělením dat a výpočty na sdílených prostředků. Můžete použít rozšíření virtuálního počítače Docker spravuje [Azure Linux Agent] vytvoření Docker virtuálního počítače, který je hostitelem libovolný počet kontejnerů pro vaše aplikace v Azure.

> [!NOTE]
> Toto téma popisuje, jak vytvořit virtuální počítač Docker z portálu Azure. Jak vytvořit virtuální počítač Docker na příkazovém řádku, najdete v sekci [jak používat rozšíření virtuálního počítače z rozhraní příkazového řádku (Azure CLI) Docker]. Podrobný popis kontejnery a jejich výhody, najdete v sekci [Docker vysokou úroveň tabulí](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).
> 
> 

## <a name="create-a-new-vm-from-the-image-gallery"></a>Vytvoření nového virtuálního počítače z Galerie obrázků
Prvním krokem vyžaduje virtuální počítač Azure z Linux bitovou kopii, která podporuje Docker rozšíření virtuálního počítače, pomocí image Ubuntu 14.04 LTS z Galerie obrázků jako obrázek příkladu server a Ubuntu 14.04 Desktop jako klient. Na portálu, klikněte na tlačítko **+ nový** k vytvoření nové instance virtuálního počítače a vyberte bitovou kopii Ubuntu 14.04 LTS z dostupných možnostech nebo dokončení Galerie obrázků, jak je uvedeno níže.

> [!NOTE]
> V současné době podporují pouze Ubuntu 14.04 LTS obrázky novější než červenec 2014 Docker rozšíření virtuálního počítače.
> 
> 

![Vytvořit novou bitovou kopii Ubuntu](./media/portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Vytvoření Docker certifikátů
Po vytvoření virtuálního počítače, zkontrolujte, zda je na klientském počítači nainstalovaný Docker. (Podrobnosti najdete v tématu [pokyny k instalaci Docker](https://docs.docker.com/installation/#installation).)

Vytvořte certifikát a klíč soubory pro komunikaci Docker podle [systémem Docker s protokolem https] a umístěte je do **`~/.docker`** adresář na klientském počítači.

> [!NOTE]
> Rozšíření virtuálního počítače Docker portálu aktuálně vyžaduje přihlašovací údaje, které kódováním base64.
> 
> 

Na příkazovém řádku použít **`base64`** nebo jiný Oblíbené kódování nástroj k vytváření témat s kódováním base64. Díky tomuto s jednoduchou sadou souborů certifikát a klíč může vypadat podobně jako tento:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Přidání rozšíření virtuálního počítače Docker
Chcete-li přidat rozšíření virtuálního počítače Docker, najděte instance virtuálního počítače, který jste vytvořili a přejděte dolů k položce **rozšíření** a klikněte na něj se zprovoznit rozšíření virtuálních počítačů, jak je uvedeno níže.

![](media/portal-use-docker/ClickExtensions.png)

### <a name="add-an-extension"></a>Přidání rozšíření
Klikněte **+ přidat** zobrazíte možné rozšíření virtuálních počítačů můžete přidat do tohoto virtuálního počítače.

![](media/portal-use-docker/ClickAdd.png)

### <a name="select-the-docker-vm-extension"></a>Vyberte rozšíření virtuálního počítače Docker
Zvolte Docker rozšíření virtuálního počítače, které se vyvolá Docker popis a důležité odkazy, a pak klikněte na tlačítko **vytvořit** v dolní části zahájíte proces instalace.

![](media/portal-use-docker/ChooseDockerExtension.png)

![](media/portal-use-docker/CreateButtonFocus.png)

### <a name="add-your-certificate-and-key-files"></a>Přidáte certifikát a soubory klíčů:
Pole formuláře zadejte kódováním base64 verze váš certifikát certifikační Autority, certifikátu serveru a váš klíč serveru, jak je znázorněno na následujícím obrázku.

![](media/portal-use-docker/AddExtensionFormFilled.png)

> [!NOTE]
> Všimněte si, že (jako v předchozí obrázek). 2376 se vyplní ve výchozím nastavení. Můžete zadat libovolný koncový bod, ale dalším krokem bude otevře odpovídající koncový bod. Pokud změníte výchozí, ujistěte se, že jste otevře odpovídající koncový bod v dalším kroku.
> 
> 

## <a name="add-the-docker-communication-endpoint"></a>Přidání koncového bodu komunikace Docker
Při zobrazení skupiny prostředků, který jste vytvořili, vyberte skupinu zabezpečení sítě spojené s virtuální počítač a klikněte na **příchozí pravidla zabezpečení** zobrazte pravidla, jak je vidět tady.

![](media/portal-use-docker/AddingEndpoint.png)

Klikněte na tlačítko **+ přidat** chcete přidat jiné pravidlo a v případě výchozí, zadejte název pro koncový bod (v tomto příkladu **Docker**) a. 2376 'rozsah cílových portů'. Nastavit zobrazuje hodnota protokol **TCP**a klikněte na tlačítko **OK** vytvoření pravidla.

![](media/portal-use-docker/AddEndpointFormFilledOut.png)

## <a name="test-your-docker-client-and-azure-docker-host"></a>Testování vaší Docker klientských a hostitelských Azure Docker
Vyhledejte a zkopírujte název domény Virtuálního počítače a na příkazovém řádku klientského počítače, typ `docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` (kde *dockerextension* je nahrazena poddomény pro virtuální počítač).

Výsledek by měl vypadat přibližně takto:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Po dokončení výše uvedené kroky máte nyní plně funkční Docker hostitele se systémem na virtuální počítač Azure, nakonfigurované k připojení k vzdáleně z jiných klientů.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
Jste připravení přejít ke [Docker uživatelská příručka] a používat virtuální počítač Docker. Pokud chcete automatizovat vytváření hostitelů Docker na virtuálních počítačích Azure prostřednictvím rozhraní příkazového řádku, přečtěte si téma [jak používat rozšíření virtuálního počítače z rozhraní příkazového řádku (Azure CLI) Docker]

<!--Anchors-->
[Create a new VM from the Image Gallery]:#createvm
[Create Docker Certificates]:#dockercerts
[Add the Docker VM Extension]:#adddockerextension
[Test Docker Client and Azure Docker Host]:#testclientandserver
[Next steps]:#next-steps

<!--Image references-->
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[6]:./media/markdown-template-for-new-articles/pretty49.png
[7]:./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[jak používat rozšíření virtuálního počítače z rozhraní příkazového řádku (Azure CLI) Docker]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Azure Linux Agent]:../../extensions/agent-linux.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md

[systémem Docker s protokolem https]:http://docs.docker.com/articles/https/
[Docker uživatelská příručka]:https://docs.docker.com/userguide/
