---
title: 'Odkaz: vyřazení obrázku Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Podrobnosti o zastaralosti ovlivňující Data Science Virtual Machine Azure
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80754765"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Odkaz: vyřazení imagí DSVM jako vyřazení

Níže jsme probrali návrhy týkající se obchodování s nadcházejícími vydanými službami Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: migrace datových disků

V 31. prosince 2019 přestane být podporován obrázek Windows 2012 DSVM. Chcete-li migrovat datový disk ze stávající sady Windows 2012 DSVM do systému Windows 2016 DSVM, proveďte následující kroky:

1. Vytvořte nový systém Windows 2016 DSVM podle zobrazených [pokynů.](./provision-vm.md#create-your-dsvm)
1. Pomocí [těchto pokynů](../../virtual-machines/windows/detach-disk.md)odpojte stávající datové disky z image Windows 2012.
1. Pomocí [těchto pokynů](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)připojte disk z předchozího kroku k imagi Windows 2016.

## <a name="centos"></a>CentOS

Noví uživatelé by měli používat nejnovější image Ubuntu nebo Windows. CentOS zůstane k dispozici pro použití s existujícími šablonami řešení.