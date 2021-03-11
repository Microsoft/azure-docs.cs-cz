---
title: Vytvoření a nahrání Flatcar virtuálního pevného disku pro kontejner Linux pro použití v Azure
description: Naučte se vytvořit a nahrát VHD obsahující operační systém Flatcar Container Linux.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 5d8be9493b7a312270301e3520f301f797fe2167
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565287"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Použití předem sestavené image Flatcar pro Azure

Pro každý z podporovaných kanálů Flatcar si můžete stáhnout předem připravené image virtuálních pevných disků Azure pro Flatcar Container Linux:

- [zůstal](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [beta verze](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [Rozšířená](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [vstupního](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

Tato Image je už plně nastavená a optimalizovaná tak, aby běžela v Azure. Stačí ho jenom dekomprimovat.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Vytvoření vlastního virtuálního počítače založeného na Flatcar pro Azure

Alternativně můžete zvolit vytvoření vlastní image Flatcar Container Linux.

V jakémkoli počítači se systémem Linux postupujte podle pokynů v příručce k [sadě Flatcar Container Linux SDK pro vývojáře](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/). Při spuštění `image_to_vm.sh` skriptu Nezapomeňte předat, `--format=azure` abyste vytvořili virtuální pevný disk Azure.

## <a name="next-steps"></a>Další kroky

Jakmile budete mít soubor. VHD, můžete použít výsledný soubor k vytvoření nových virtuálních počítačů v Azure. Pokud soubor. VHD do Azure nahráváte poprvé, přečtěte si téma [Vytvoření virtuálního počítače se systémem Linux z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).
