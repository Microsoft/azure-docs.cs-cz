---
title: 'Odkaz: vyřazení image DSVM'
description: Podrobnosti o zastaralosti ovlivňující Data Science Virtual Machine Azure (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 4968ac08b75141ed84994ca287215a34728232c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333336"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Odkaz: vyřazení imagí DSVM jako vyřazení

Níže jsme probrali návrhy týkající se obchodování s nadcházejícími vydanými službami Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: migrace datových disků

Po 5. listopadu 2019 přestane podporovat image Windows 2012 DSVM. Chcete-li migrovat datový disk ze stávající sady Windows 2012 DSVM do systému Windows 2016 DSVM, proveďte následující kroky:

1. Vytvořte nový systém Windows 2016 DSVM podle zobrazených [pokynů.](./provision-vm.md#create-your-dsvm)
1. Pomocí [těchto pokynů](../../virtual-machines/windows/detach-disk.md)odpojte stávající datové disky z image Windows 2012.
1. Pomocí [těchto pokynů](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)připojte disk z předchozího kroku k imagi Windows 2016.
