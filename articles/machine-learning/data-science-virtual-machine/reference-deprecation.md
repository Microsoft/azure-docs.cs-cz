---
title: 'Odkaz: vyřazení obrázku Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Podrobnosti o zastaralosti ovlivňující Data Science Virtual Machine Azure
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525783"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Odkaz: vyřazení imagí DSVM jako vyřazení

Níže jsme probrali návrhy týkající se obchodování s nadcházejícími vydanými službami Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: migrace datových disků

V 31. prosince 2019 přestane být podporován obrázek Windows 2012 DSVM. Chcete-li migrovat datový disk ze stávající sady Windows 2012 DSVM do systému Windows 2016 DSVM, proveďte následující kroky:

1. Vytvořte nový systém Windows 2016 DSVM podle zobrazených [pokynů.](./provision-vm.md#create-your-dsvm)
1. Pomocí [těchto pokynů](../../virtual-machines/windows/detach-disk.md)odpojte stávající datové disky z image Windows 2012.
1. Pomocí [těchto pokynů](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)připojte disk z předchozího kroku k imagi Windows 2016.
