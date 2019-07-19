---
title: Stažení sady Azure SDK pro PHP
description: Přečtěte si, jak stáhnout a nainstalovat sadu Azure SDK pro PHP.
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
ms.author: yaya
ms.openlocfilehash: 9dd336ef973b603108aad62c90e4dab385d20317
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67873897"
---
# <a name="download-the-azure-sdk-for-php"></a>Stažení sady Azure SDK pro PHP

## <a name="overview"></a>Přehled

Sada Azure SDK pro PHP obsahuje komponenty, které umožňují vyvíjet, nasazovat a spravovat aplikace PHP pro Azure. Konkrétně sada Azure SDK pro PHP zahrnuje následující:

* **Knihovny klienta php pro Azure**. Tyto knihovny tříd poskytují rozhraní pro přístup k funkcím Azure, jako jsou například služby správy dat a cloudové služby.
* **Rozhraní příkazového řádku Azure pro Mac, Linux a Windows (Azure CLI)** . Toto je sada příkazů pro nasazení a správu služeb Azure, jako jsou Azure websites a Azure Virtual Machines. Rozhraní příkazového řádku Azure CLI funguje na libovolné platformě, včetně počítačů Mac, Linux a Windows.
* **Azure PowerShell (pouze Windows)** . Toto je sada rutin PowerShellu pro nasazení a správu služeb Azure, například Cloud Services a Virtual Machines.
* **Emulátory Azure (jenom Windows)** . Emulátory výpočtů a úložiště jsou místní emulátory cloudových služeb a služeb pro správu dat, které umožňují místní testování aplikace. Emulátory Azure se spouštějí jenom v systému Windows.

Níže uvedené části popisují, jak stáhnout a nainstalovat komponenty popsané výše.

V pokynech v tomto tématu se předpokládá, že máte nainstalovaný [php][install-php] .

> [!NOTE]
> Abyste mohli používat klientské knihovny PHP pro Azure, musíte mít PHP 5,5 nebo vyšší.
>
>

## <a name="php-client-libraries-for-azure"></a>Klientské knihovny PHP pro Azure

Klientské knihovny PHP pro Azure poskytují rozhraní pro přístup k funkcím Azure, jako jsou například služby správy dat a cloudové služby, z libovolného operačního systému. Tyto knihovny lze instalovat prostřednictvím skladatele.

Informace o tom, jak používat klientské knihovny PHP pro Azure, najdete v tématech [Jak používat službu BLOB Service][blob-service] , [How to Use the Table Service][table-service] a [Jak používat službu Queue Service][Queue-Service].

### <a name="install-via-composer"></a>Instalace prostřednictvím skladatele

1. [Nainstalovat Git][install-git]. Ve Windows budete taky muset do proměnné prostředí PATH přidat spustitelný soubor Git.

2. V kořenovém adresáři projektu vytvořte soubor s názvem **skladatel. JSON** a přidejte do něj následující kód:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Stáhněte si **[skladatel. phar][composer-phar]** do kořenového adresáře projektu.

4. Otevřete příkazový řádek a spusťte ho v kořenovém adresáři projektu.

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell a emulátory Azure

Azure PowerShell je sada rutin PowerShellu pro nasazení a správu služeb Azure (například Cloud Services a Virtual Machines). Emulátory Azure jsou emulátory služeb Cloud Services a správy dat, které umožňují místní testování aplikace. Tyto součásti jsou podporovány pouze v systému Windows.

Doporučený způsob, jak nainstalovat Azure PowerShell a emulátory Azure, je použití [Instalace webové platformy Microsoft][download-wpi]. Všimněte si, že můžete také nainstalovat další součásti pro vývoj, jako je PHP, SQL Server, ovladače Microsoftu pro SQL Server pro PHP a WebMatrix.

Informace o tom, jak používat Azure PowerShell, najdete v tématu [Jak používat Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>Azure CLI

Azure CLI je sada příkazů pro nasazení a správu služeb Azure, jako jsou Azure websites a Azure Virtual Machines. Informace o instalaci rozhraní příkazového řádku Azure CLI najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](cli-install-nodejs.md).

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[install-php]: https://www.php.net/manual/en/install.php
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
