---
title: 'Reference: Data Science Virtuální počítač Image Deprecation'
titleSuffix: Azure Data Science Virtual Machine
description: Podrobnosti o vyřazení ovlivňující virtuální počítač Azure Data Science
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754765"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Reference: Vyřazení snímků DSVM

Níže se zabýváme návrhy pro řešení nadcházejících vyřazení na virtuálním počítači Azure Data Science.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Migrace datových disků

31. prosince 2019 přestaneme podporovat bitovou kopii systému Windows 2012 DSVM. Chcete-li migrovat datový disk ze stávajícího systému Windows 2012 DSVM do systému Windows 2016 DSVM, postupujte takto:

1. Vytvořte nový Systém Windows 2016 DSVM podle [pokynů uvedených zde](./provision-vm.md#create-your-dsvm).
1. Odpojte existující datové disky od bitové kopie systému Windows 2012 podle [těchto pokynů](../../virtual-machines/windows/detach-disk.md).
1. Připojte disk z předchozího kroku k bitové kopii systému Windows 2016 [podle těchto pokynů](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

Noví uživatelé by měli používat nejnovější obrázky Ubuntu nebo Windows. CentOS zůstane k dispozici pro použití s existujícími šablonami řešení.