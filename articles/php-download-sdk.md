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
ms.openlocfilehash: 4f2c242e27d8a0ed6687eb4a3510bbce6e07694f
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52421483"
---
# <a name="download-the-azure-sdk-for-php"></a>Stažení sady Azure SDK pro PHP

## <a name="overview"></a>Přehled

Sada Azure SDK pro PHP obsahuje součásti, které umožňují vyvíjet, nasazovat a spravovat aplikace PHP pro Azure. Konkrétně sady Azure SDK pro PHP obsahuje následující:

* **Klientské knihovny PHP pro Azure**. Tyto knihovny tříd poskytují rozhraní pro přístup k funkcím Azure, jako je například služeb správy dat. a cloudové služby.
* **Rozhraní příkazového řádku Azure pro Mac, Linux a Windows (Azure CLI)**. Toto je sadu příkazů pro nasazení a správu služeb Azure, jako jsou Azure Websites a Azure Virtual Machines. Rozhraní příkazového řádku Azure práci na všechny platformy včetně Windows, Mac a Linux.
* **Prostředí Azure PowerShell (jenom Windows)**. To je sada rutin Powershellu pro nasazení a správu služeb Azure, jako je například Cloud Services a Virtual Machines.
* **Emulátory Azure (jenom Windows)**. Emulátory výpočetní výkon a úložiště jsou místní emulátory cloudových služeb a služeb správy dat., které umožňují otestovat aplikaci místně. Emulátory Azure spustit jenom na Windows.

Následující části popisují, jak stáhnout a nainstalovat komponenty popsané výše.

Pokyny v tomto tématu se předpokládá, že máte [PHP] [ install-php] nainstalované.

> [!NOTE]
> Musíte mít PHP 5.5 nebo novější pomocí klientských knihoven PHP pro Azure.
>
>

## <a name="php-client-libraries-for-azure"></a>Klientské knihovny PHP pro Azure

Klientské knihovny PHP pro Azure poskytuje rozhraní pro přístup k funkcím Azure, jako je například služeb správy dat. a cloudovým službám, ve všech operačních systémech. Tyto knihovny je možné nainstalovat přes autora.

Informace o tom, jak pomocí klientských knihoven PHP pro Azure najdete v tématu [použití služby Blob Service][blob-service], [použití služby Table Service] [ table-service]a [jak používat fronty Service][queue-service].

### <a name="install-via-composer"></a>Instalace přes Composer

1. [Instalace Gitu][install-git]. Na Windows je také potřeba přidat spustitelný Git do proměnné prostředí PATH.

2. Vytvořte soubor s názvem **composer.json** v kořenovém adresáři vašeho projektu a přidejte do ní následující kód:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Stáhněte si **[composer.phar] [ composer-phar]** v kořenovém adresáři projektu.

4. Otevřete příkazový řádek a spustit v kořenovém adresáři projektu

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Emulátory Azure a Azure Powershellu

Prostředí Azure PowerShell je sada rutin Powershellu pro nasazení a správu služeb Azure (třeba Cloud Services a Virtual Machines). Emulátory Azure jsou emulátory cloudových služeb a služeb správy dat., které umožňují otestovat aplikaci místně. Tyto součásti jsou podporovány pouze Windows.

Doporučeným způsobem, jak nainstalovat Azure PowerShell a emulátory Azure je použít [instalačního programu webové platformy Microsoft][download-wpi]. Všimněte si, že můžete také nainstalovat další komponenty pro vývoj, jako je PHP, SQL Server, Drivers společnosti Microsoft pro systém SQL Server pro PHP a službě WebMatrix.

Informace o tom, jak pomocí Azure Powershellu najdete v tématu [tom, jak pomocí Azure Powershellu][powershell-tools].

## <a name="azure-cli"></a>Azure CLI

Rozhraní příkazového řádku Azure je sadu příkazů pro nasazení a správu služeb Azure, jako jsou Azure Websites a Azure Virtual Machines. Informace o instalaci Azure CLI najdete v tématu [instalace rozhraní příkazového řádku Azure](cli-install-nodejs.md).

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: https://getcomposer.org/composer.phar
[nodejs-org]: https://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: https://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: https://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: https://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: https://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: https://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: https://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: https://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: https://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
