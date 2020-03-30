---
title: 'Reference: Data Science Virtuální počítač Image Deprecation'
titleSuffix: Azure Data Science Virtual Machine
description: Podrobnosti o vyřazení ovlivňující virtuální počítač Azure Data Science
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525783"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Reference: Vyřazení snímků DSVM

Níže se zabýváme návrhy pro řešení nadcházejících vyřazení na virtuálním počítači Azure Data Science.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Migrace datových disků

31. prosince 2019 přestaneme podporovat bitovou kopii systému Windows 2012 DSVM. Chcete-li migrovat datový disk ze stávajícího systému Windows 2012 DSVM do systému Windows 2016 DSVM, postupujte takto:

1. Vytvořte nový Systém Windows 2016 DSVM podle [pokynů uvedených zde](./provision-vm.md#create-your-dsvm).
1. Odpojte existující datové disky od bitové kopie systému Windows 2012 podle [těchto pokynů](../../virtual-machines/windows/detach-disk.md).
1. Připojte disk z předchozího kroku k bitové kopii systému Windows 2016 [podle těchto pokynů](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
