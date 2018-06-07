---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/29/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 8f3d57f8f1f3631421618e31e943606a16e6bf5b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34670015"
---
## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

### <a name="nvidia-tesla-cuda-drivers"></a>Ovladače NVIDIA tesla – měrná (CUDA)

Tesla – měrná NVIDIA (CUDA) ovladače NC, NCv2, NCv3 a virtuální počítače a series (volitelné pro vs series) jsou podporovány pouze na operačních systémů uvedených v následující tabulce. Odkazy na stažení ovladačů jsou aktuální v době publikace. Nejnovější ovladače najdete na webu [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Jako alternativu k ruční instalaci ovladačů CUDA na virtuální počítač Windows serveru, můžete nasadit Azure [datové vědy virtuálního počítače](../articles/machine-learning/data-science-virtual-machine/overview.md) bitové kopie. V edicích DSVM pro Windows Server 2016 předinstalační ovladače NVIDIA CUDA, CUDA hluboké Neuronové sítě knihovny a další nástroje.


| Operační systém | Ovladač |
| -------- |------------- |
| Windows Server 2016 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |


### <a name="nvidia-grid-drivers"></a>Ovladače NVIDIA mřížky

Microsoft znovu distribuuje NVIDIA mřížky ovladač instalačních programů pro virtuální počítače vs series používá jako virtuální pracovní stanice nebo pro virtuální aplikace. Nainstalujte pouze tyto ovladače mřížky na virtuálních počítačích Azure vs, jenom na operačních systémů uvedených v následující tabulce. Tyto ovladače zahrnují licencování pro mřížky virtuální grafický procesor Software v Azure.

| Operační systém | Ovladač |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [MŘÍŽKY 6.1 (391.58)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [MŘÍŽKY 6.1 (391.58)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |