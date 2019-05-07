---
title: Podporované zdroje pro Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Zjistěte, jaké oblasti Azure a velikosti virtuálních počítačů jsou podporovány pomocí Microsoft Azure Red Hat OpenShift.
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 22cc195b7808ad4a9111aafcf883a68c51f3709f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076102"
---
# <a name="azure-red-hat-openshift-resources"></a>Prostředky Azure Red Hat OpenShift

Toto téma obsahuje seznam oblastí Azure a velikosti virtuálních počítačů, které jsou podporované službou Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Oblast Azure

Do následujících oblastí Azure po celém světě můžete nasadit clustery Azure Red Hat OpenShift:

|Oblast|Rozhraní příkazového řádku kódu|
|-|-|
|🇦🇺 Austrálie – východ|`australiaeast`|
|🇨🇦 Kanada – střed|`canadacentral`|
|🇨🇦 Kanada – východ|`canadaeast`|
|🇺🇸 USA – východ|`eastus`|
|🇺🇸 USA – západ|`westus`|
|Západní Evropa 🇪🇺|`westeurope`|
|Severní Evropa 🇪🇺|`northeurope`|

## <a name="virtual-machine-sizes"></a>Velikosti virtuálních počítačů

Tady jsou velikosti podporovaných virtuálních počítačů, které můžete určit pro výpočetní uzly v clusteru Azure Red Hat OpenShift.

> [!Important]
> Každý virtuální počítač má jiný počet jednotek, které je možné připojit. Toto video asi jako okamžitě vymazat jako paměti nebo procesoru velikost.
> Ne všechny velikosti virtuálních počítačů jsou k dispozici ve všech oblastech. I v případě, že rozhraní API podporuje velikost, kterou zadáte, může být dojde k chybě, pokud je velikost není k dispozici v oblasti, kterou zadáte.
> Zobrazit [velikosti aktuální seznam podporovaných virtuálních počítačů v jedné oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) Další informace.

## <a name="compute-node-sizes"></a>Výpočet velikosti uzlů

Rozhraní REST API Azure Red Hat OpenShift podporuje následující velikosti výpočetních uzlů:

|Velikost|Virtuální procesory|Paměť RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standard F8s v2|8|16 GB|
|Standard F16s v2|16|32 GB|
|Standard F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Velikost hlavního uzlu

Následující master / REST API služby Azure Red Hat OpenShift podporují velikosti uzlů infrastruktury:

|Velikost|Virtuální procesory|Paměť RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>Další postup

Zkuste [vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) kurzu.