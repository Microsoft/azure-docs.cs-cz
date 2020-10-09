---
title: Podporované prostředky pro Azure Red Hat OpenShift 3.11
description: Informace o tom, které oblasti Azure a velikosti virtuálních počítačů podporuje Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 38203cede755d776ba9142ad16e1bea1fd1637a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82203671"
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
