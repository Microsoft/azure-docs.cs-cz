---
title: 'Odkaz: vyřazení image DSVM'
description: Podrobnosti o zastaralosti ovlivňující Data Science Virtual Machine Azure (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 252f9c66034dbadaf7a2e9e6f78665c26d414deb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456289"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Odkaz: vyřazení imagí DSVM jako vyřazení

Níže jsme probrali návrhy týkající se obchodování s nadcházejícími vydanými službami Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: migrace datových disků

V 31. prosince 2019 přestane být podporován obrázek Windows 2012 DSVM. Chcete-li migrovat datový disk ze stávající sady Windows 2012 DSVM do systému Windows 2016 DSVM, proveďte následující kroky:

1. Vytvořte nový systém Windows 2016 DSVM podle zobrazených [pokynů.](./provision-vm.md#create-your-dsvm)
1. Pomocí [těchto pokynů](../../virtual-machines/windows/detach-disk.md)odpojte stávající datové disky z image Windows 2012.
1. Pomocí [těchto pokynů](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)připojte disk z předchozího kroku k imagi Windows 2016.
