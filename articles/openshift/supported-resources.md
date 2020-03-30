---
title: Podporované prostředky pro Azure Red Hat OpenShift
description: Zjistěte, které oblasti Azure a velikosti virtuálních strojů jsou podporované microsoft azure red hat openshift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243663"
---
# <a name="azure-red-hat-openshift-resources"></a>Prostředky Azure Red Hat OpenShift

V tomto tématu jsou uvedeny oblasti Azure a velikosti virtuálních strojů podporované službou Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Oblast Azure

Aktuální seznam oblastí, kde můžete nasadit clustery Azure Red Hat OpenShift, najdete v tématu [Produkty dostupné podle oblastí.](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all)

## <a name="virtual-machine-sizes"></a>Velikosti virtuálních počítačů

Tady jsou podporované velikosti virtuálních strojů, které můžete zadat pro výpočetní uzly v clusteru Azure Red Hat OpenShift.

> [!Important]
> Každý virtuální virtuální virtuální mši má jiný počet jednotek, které lze připojit. To nemusí být tak okamžitě vymazat jako velikost paměti nebo procesoru.
> Ne všechny velikosti virtuálních počítače jsou k dispozici ve všech oblastech. I v případě, že rozhraní API podporuje velikost, kterou zadáte, může dojít k chybě, pokud velikost není k dispozici v oblasti, kterou zadáte.
> Další informace najdete [v tématu Aktuální seznam podporovaných velikostí virtuálních](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) počítače na oblast.

## <a name="compute-node-sizes"></a>Výpočetní velikosti uzlů

Následující velikosti výpočetních uzlů jsou podporovány rozhraním Azure Red Hat OpenShift REST API:

|Velikost|Virtuální procesory|Paměť RAM|
|-|-|-|
|Standardní D4s v3|4|16 GB|
|Standardní D8s v3|8|32 GB|
|Standardní D16s v3|16|64 GB|
|Standardní D32s v3|32|128 GB|
|-|-|-|
|Standardní E4s v3|4|32 GB|
|Standardní E8s v3|8|64 GB|
|Standardní E16s v3|16|128 GB|
|Standardní E32s v3|32|256 GB|
|-|-|-|
|Standardní F8s v2|8|16 GB|
|Standardní F16s v2|16|32 GB|
|Standardní F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Velikosti hlavních uzlů

Následující velikosti uzlů hlavního serveru a infrastruktury jsou podporovány rozhraním AZURE Red Hat OpenShift REST API:

|Velikost|Virtuální procesory|Paměť RAM|
|-|-|-|
|Standardní D4s v3|4|16 GB|
|Standardní D8s v3|8|32 GB|
|Standardní D16s v3|16|64 GB|
|Standardní D32s v3|32|128 GB|

## <a name="next-steps"></a>Další kroky

Vyzkoušejte kurz [vytvoření clusteru Azure Red Hat OpenShift.](tutorial-create-cluster.md)
