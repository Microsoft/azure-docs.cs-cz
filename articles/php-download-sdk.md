---
title: Stažení sady Azure SDK pro PHP
description: Zjistěte, jak stáhnout a nainstalovat sadu Azure SDK pro jazyk PHP.
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: ''
ms.assetid: bac355ac-4c25-42f4-8273-c5112eafa8d4
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: allclark;yaqiyang
ms.openlocfilehash: cfcf908145e8a384782953e045f9e10fd3c0e8f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639465"
---
# <a name="download-the-azure-sdk-for-php"></a>Stažení sady Azure SDK pro PHP

## <a name="overview"></a>Přehled

Azure SDK pro jazyk PHP obsahuje součásti, které vám umožní vyvíjet, nasazovat a spravovat aplikace PHP pro Azure. Konkrétně sadu Azure SDK pro jazyk PHP zahrnuje následující:

* **PHP klientské knihovny pro Azure**. Tyto knihovny tříd poskytují rozhraní pro přístup k Azure funkcí, jako jsou služby pro správu dat a cloudových služeb.
* **Rozhraní příkazového řádku Azure pro Mac, Linux a Windows (Azure CLI)**. Toto je sadu příkazů pro nasazení a správu služby Azure, jako jsou weby Azure a virtuálních počítačích Azure. Rozhraní příkazového řádku Azure práce na jakékoli platformě, včetně Mac, Linux a Windows.
* **Prostředí Azure PowerShell (jenom Windows)**. To je sada rutin prostředí PowerShell pro nasazení a Správa služby Azure, jako je cloudových služeb a virtuálních počítačů.
* **(Jenom Windows) Azure emulátorů**. Emulátorů výpočetního prostředí a úložiště jsou místní emulátorů cloudové služby a služby pro data, které vám umožní testovat aplikaci místně. Emulátorů Azure lze spustit pouze v systému Windows.

V níže uvedených částech popisují, jak stáhnout a nainstalovat komponenty popsané výše.

Podle pokynů v tomto tématu se předpokládá, že máte [PHP] [ install-php] nainstalována.

> [!NOTE]
> Musíte mít PHP 5.5 nebo vyšší použít knihovny klienta PHP pro Azure.
>
>

## <a name="php-client-libraries-for-azure"></a>Klientské knihovny PHP pro Azure

PHP klientské knihovny pro Azure poskytují rozhraní pro přístup k Azure funkcí, jako jsou služby pro správu dat a cloudové služby ve všech operačních systémech. Tyto knihovny se může nainstalovat prostřednictvím autora.

Informace o tom, jak použít knihovny klienta PHP pro Azure najdete v tématu [jak používat služby objektů Blob][blob-service], [použití služby Table] [ table-service]a [jak používat fronty služby][queue-service].

### <a name="install-via-composer"></a>Nainstalovat prostřednictvím autora

1. [Nainstalovat Git][install-git]. V systému Windows bude také muset přidat Git spustitelný soubor do vaší proměnné prostředí PATH.

2. Vytvořte soubor s názvem **composer.json** v kořenu projektu a přidejte do ní následující kód:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Stáhněte si **[composer.phar] [ composer-phar]** v kořenového adresáře projektu.

4. Otevřete příkazový řádek a spusťte tento v kořenového adresáře projektu

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Prostředí Azure PowerShell a Azure emulátorů

Prostředí Azure PowerShell je sada rutin prostředí PowerShell pro nasazení a Správa služby Azure (například cloudové služby a virtuální počítače). Emulátorů Azure jsou emulátorů cloudové služby a služby pro data, které vám umožní testovat aplikaci místně. Tyto součásti jsou podporovány pouze v systému Windows.

Doporučený způsob, jak nainstalovat Azure PowerShell a emulátorů Azure je použití [instalačního programu webové platformy Microsoft][download-wpi]. Všimněte si, že můžete nainstalovat jiné komponenty, vývoj, například PHP, SQL Server, Drivers společnosti Microsoft pro systém SQL Server pro PHP a službě WebMatrix.

Informace o tom, jak pomocí prostředí Azure PowerShell najdete v tématu [jak používat Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>Azure CLI

Rozhraní příkazového řádku Azure je sadu příkazů pro nasazení a správu služby Azure, jako jsou weby Azure a virtuálních počítačích Azure. Informace o instalaci rozhraní příkazového řádku Azure najdete v tématu [nainstalovat Azure CLI](cli-install-nodejs.md).

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [středisku pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
