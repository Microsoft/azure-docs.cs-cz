---
title: Vytvoření uživatelské image virtuálního počítače pro Azure Marketplace
description: Obsahuje seznam kroků a odkazů vyžadovaných k vytvoření uživatelské image virtuálního počítače.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: e63f09dc538c5e66b244826cf3b5f92ac388b6a9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818701"
---
# <a name="create-a-user-vm-image"></a>Vytvoření uživatelské image virtuálního počítače

Tento článek vysvětluje dva obecné kroky potřebné k vytvoření nespravované image z zobecněného virtuálního pevného disku.  K dispozici jsou odkazy, které vás provedou jednotlivými kroky: zachycení image a generalizace obrázku.


## <a name="capture-the-vm-image"></a>Zachycení image virtuálního počítače

Postupujte podle pokynů v následujícím článku o zachycení virtuálního počítače, který odpovídá vašemu přístupovému přístupu:

-  PowerShell: [Vytvoření image nespravovaného virtuálního počítače z virtuálního počítače Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [Vytvoření image virtuálního počítače nebo virtuálního pevného disku](../../../virtual-machines/linux/capture-image.md)
-  Rozhraní API: [Virtual Machines-Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Generalizace image virtuálního počítače

Vzhledem k tomu, že jste vygenerovali image uživatele z dříve zobecněného virtuálního pevného disku, měl by být taky zobecněný.  Znovu vyberte následující článek, který odpovídá mechanismu přístupu.  (Už jste si zachytili disk, možná jste si ho zaznamenali.)

-  PowerShell: [generalizace virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [Krok 2: vytvoření image virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtual Machines-generalizace](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Další kroky

V dalším kroku [vytvoříte certifikát](cpp-create-key-vault-cert.md) a uložíte ho do nového Azure Key Vault.  Tento certifikát je nutný k navázání zabezpečeného připojení WinRM k virtuálnímu počítači.
