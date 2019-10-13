---
title: Známé problémy & řešení potíží
titleSuffix: Azure Data Science Virtual  Machine
description: Seznam známých problémů, řešení a řešení potíží pro Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: a0b22326a429edfa2f2b8741453215b42910891c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301916"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Známé problémy a řešení potíží s Data Science Virtual Machine Azure

Tento článek vám pomůže najít a opravit chyby nebo chyby při použití Data Science Virtual Machine Azure.

## <a name="python-package-installation-issues"></a>Problémy s instalací balíčku Pythonu

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalace balíčků se závislostmi přerušení v programu PIP na platformě Linux

Při instalaci balíčků použijte `sudo pip install` místo `pip install`.

## <a name="disk-encryption-issues"></a>Problémy s šifrováním disku

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Šifrování disku se na Ubuntu DSVM nezdařilo.

Azure Disk Encryption (ADE) aktuálně není v DSVM Ubuntu podporován. Jako alternativní řešení zvažte konfiguraci [Azure Storageho šifrování pomocí klíčů spravovaných zákazníkem](../../storage/common/storage-encryption-keys-portal.md).

## <a name="tool-appears-disabled"></a>Nástroj se zobrazuje zakázaný

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Technologie Hyper-V nefunguje na DSVM Windows

Jedná se o očekávané chování, jako u spouštěcího výkonu jsme zakázali některé služby. Pokud ho chcete znovu povolit, otevřete panel hledání ve Windows DSVM, zadejte "Services", pak nastavte všechny služby Hyper-V na ruční a nastavte možnost Správa virtuálních počítačů s technologií Hyper-V na hodnotu automaticky.

Poslední obrazovka by měla vypadat takto:

   ![Povolení Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

