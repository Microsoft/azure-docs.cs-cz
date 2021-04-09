---
title: Podporované prostředky pro Azure Red Hat OpenShift 3.11
description: Informace o tom, které oblasti Azure a velikosti virtuálních počítačů podporuje Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ad0837ae110b84cdff690fe76e13923a0ab60996
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100635604"
---
# <a name="azure-red-hat-openshift-resources"></a>Prostředky Azure Red Hat OpenShift

Toto téma obsahuje seznam oblastí Azure a velikostí virtuálních počítačů, které podporuje služba Microsoft Azure Red Hat OpenShift 3,11.

## <a name="azure-regions"></a>Oblast Azure

Aktuální seznam oblastí, kde můžete nasadit clustery Azure Red Hat OpenShift, najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) .

## <a name="virtual-machine-sizes"></a>Velikosti virtuálních počítačů

Tady jsou podporované velikosti virtuálních počítačů, které můžete zadat pro výpočetní uzly v clusteru Azure Red Hat OpenShift.

> [!Important]
> Každý virtuální počítač má jiný počet jednotek, které je možné připojit. To nemusí být okamžitě jasné jako paměť nebo velikost procesoru.
> Ne všechny velikosti virtuálních počítačů jsou dostupné ve všech oblastech. I v případě, že rozhraní API podporuje zadanou velikost, může se zobrazit chyba, pokud velikost není k dispozici v oblasti, kterou zadáte.
> Další informace najdete v tématu [aktuální seznam podporovaných velikostí virtuálních počítačů na oblast](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) .

## <a name="compute-node-sizes"></a>Velikosti výpočetních uzlů

REST API Azure Red Hat OpenShift podporuje následující velikosti výpočetních uzlů:

|Velikost|Virtuální procesory|Paměť RAM|
|-|-|-|
|D4S V3 úrovně Standard|4|16 GB|
|D8s V3 úrovně Standard|8|32 GB|
|D16s V3 úrovně Standard|16|64 GB|
|D32s V3 úrovně Standard|32|128 GB|
|-|-|-|
|E4s V3 úrovně Standard|4|32 GB|
|E8s V3 úrovně Standard|8|64 GB|
|E16s V3 úrovně Standard|16|128 GB|
|E32s V3 úrovně Standard|32|256 GB|
|-|-|-|
|F8s úrovně v2 úrovně Standard|8|16 GB|
|F16s úrovně v2 úrovně Standard|16|32 GB|
|F32s v2 úrovně Standard|32|64 GB|

## <a name="master-node-sizes"></a>Velikosti hlavního uzlu

REST API Azure Red Hat OpenShift podporuje následující velikosti uzlů hlavní/infrastruktura:

|Velikost|Virtuální procesory|Paměť RAM|
|-|-|-|
|D4S V3 úrovně Standard|4|16 GB|
|D8s V3 úrovně Standard|8|32 GB|
|D16s V3 úrovně Standard|16|64 GB|
|D32s V3 úrovně Standard|32|128 GB|

## <a name="next-steps"></a>Další kroky

Vyzkoušejte kurz [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) .
