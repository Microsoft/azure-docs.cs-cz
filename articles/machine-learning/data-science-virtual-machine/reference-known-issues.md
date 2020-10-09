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
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78206516"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Známé problémy a řešení potíží s Data Science Virtual Machine Azure

Tento článek vám pomůže najít a opravit chyby nebo chyby, ke kterým může dojít při použití Data Science Virtual Machine Azure.

## <a name="python-package-installation-issues"></a>Problémy s instalací balíčku Pythonu

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalace balíčků se závislostmi přerušení v programu PIP na platformě Linux

`sudo pip install`Místo toho použijte `pip install` při instalaci balíčků.

## <a name="disk-encryption-issues"></a>Problémy se šifrováním disků

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Šifrování disku se na Ubuntu DSVM nezdařilo.

Azure Disk Encryption (ADE) není aktuálně podporován na Ubuntu DSVM. Alternativním řešením je zvážit konfiguraci [šifrování na straně serveru Azure Managed disks](../../virtual-machines/windows/disk-encryption.md).

## <a name="tool-appears-disabled"></a>Nástroj se zobrazuje zakázaný

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Technologie Hyper-V nefunguje na DSVM Windows

Tato technologie Hyper-V zpočátku nefunguje v systému Windows je očekávané chování. Pro výkon spuštění jsme zakázali některé služby. Povolení technologie Hyper-V:

1. Otevřete panel hledání ve Windows DSVM
1. Zadejte "Services".
1. Nastavit všechny služby technologie Hyper-V na ruční
1. Nastavte možnost Správa virtuálních počítačů s technologií Hyper-V na hodnotu automaticky.

Poslední obrazovka by měla vypadat takto:

   ![Povolení Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

