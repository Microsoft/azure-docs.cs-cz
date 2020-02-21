---
title: 'Reference: známé problémy & řešení potíží'
titleSuffix: Azure Data Science Virtual  Machine
description: Seznam známých problémů, řešení a řešení potíží pro Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: d022b1124146a1e506401e6cee257805e3a38fd3
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526541"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Známé problémy a řešení potíží s Data Science Virtual Machine Azure

Tento článek vám pomůže najít a opravit chyby nebo chyby při použití Data Science Virtual Machine Azure.

## <a name="python-package-installation-issues"></a>Problémy s instalací balíčku Pythonu

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalace balíčků se závislostmi přerušení v programu PIP na platformě Linux

Při instalaci balíčků použít `sudo pip install` místo `pip install`

## <a name="disk-encryption-issues"></a>Problémy s šifrováním disku

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Šifrování disku se na Ubuntu DSVM nezdařilo.

Azure Disk Encryption (ADE) aktuálně není v DSVM Ubuntu podporován. Jako alternativní řešení zvažte konfiguraci [Azure Storageho šifrování pomocí klíčů spravovaných zákazníkem](../../storage/common/storage-encryption-keys-portal.md).

## <a name="tool-appears-disabled"></a>Nástroj se zobrazuje zakázaný

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Technologie Hyper-V nefunguje na DSVM Windows

Jedná se o očekávané chování, jako u spouštěcího výkonu jsme zakázali některé služby. Pokud ho chcete znovu povolit, otevřete panel hledání ve Windows DSVM, zadejte "Services", pak nastavte všechny služby Hyper-V na ruční a nastavte možnost Správa virtuálních počítačů s technologií Hyper-V na hodnotu automaticky.

Poslední obrazovka by měla vypadat takto:

   ![Povolení role Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

