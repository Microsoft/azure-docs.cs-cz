---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 2af8b429b6addf6da32b34773525c51a36624e78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85242130"
---
V Azure nabízíme možnost zvýšit výkon vstupně-výstupních operací disku a MB/s, který se označuje jako shlukování na obou Virtual Machines i discích. Busting je užitečné v mnoha scénářích, jako je například zpracování neočekávaných přenosů disku nebo zpracování dávkových úloh. Můžete efektivně využít rozpínání virtuálních počítačů a na úrovni disku pro zajištění skvělého směrného výkonu a nárůstu výkonu na VIRTUÁLNÍm počítači i na disku. Tímto způsobem můžete dosáhnout skvělého směrného výkonu a shlukování výkonu na virtuálním počítači i na disku. 

Počítejte s tím, že při navýšení zatížení disků a virtuálních počítačů je nezávisle na sobě. Pokud máte shlukový disk, nepotřebujete virtuální počítač pro shlukování, aby bylo možné disk rozzpůsobit. Pokud máte virtuální počítač pro shlukování, nepotřebujete k tomu, aby se váš virtuální počítač mohl rozdávat na více počítačů. 