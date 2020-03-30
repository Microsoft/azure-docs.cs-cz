---
title: 'Reference: Známé problémy & řešení potíží'
titleSuffix: Azure Data Science Virtual  Machine
description: Získejte seznam známých problémů, řešení a řešení potíží pro Virtuální počítač Azure Data Science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206516"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Známé problémy a řešení potíží s virtuálním počítačem Azure Data Science

Tento článek vám pomůže najít a opravit chyby nebo chyby, se kterými se můžete sejít při používání virtuálního počítače Azure Data Science.

## <a name="python-package-installation-issues"></a>Problémy s instalací balíčku Pythonu

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalace balíčků s pip přestávky závislosti na Linuxu

Použijte `sudo pip install` místo `pip install` při instalaci balíčků.

## <a name="disk-encryption-issues"></a>Problémy se šifrováním disků

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Šifrování disku se nezdaří na Ubuntu DSVM

Azure Disk Encryption (ADE) není aktuálně podporovánna Ubuntu DSVM. Jako zástupné řešení zvažte konfiguraci [šifrování na straně serveru disků spravovaných Azure](../../virtual-machines/windows/disk-encryption.md).

## <a name="tool-appears-disabled"></a>Nástroj se jeví jako zakázaný.

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Technologie Hyper-V nefunguje v systému Windows DSVM

To, že hyper-V zpočátku nefunguje v systému Windows, se očekává chování. Pro výkon při spuštění jsme zakázali některé služby. Povolení technologie Hyper-V:

1. Otevření vyhledávacího panelu v systému Windows DSVM
1. Zadejte "Služby".
1. Nastavte všechny služby Hyper-V na "Ruční"
1. Nastavení "Správa virtuálních strojů Hyper-V" na "Automatická"

Poslední obrazovka by měla vypadat takto:

   ![Povolení Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

