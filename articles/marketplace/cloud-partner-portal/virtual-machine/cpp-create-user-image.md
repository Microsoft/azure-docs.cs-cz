---
title: Vytvoření uživatelské image virtuálního počítače pro Azure Marketplace
description: Jsou uvedené kroky a odkazy na požadovaná k vytvoření uživatelské image virtuálního počítače.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 0005ab517d38903b87889b67449569495e396265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938334"
---
# <a name="create-a-user-vm-image"></a>Vytvoření uživatelské image virtuálního počítače

Tento článek vysvětluje dvě obecné kroky potřebné k vytvoření nespravované image z generalizovaného virtuálního pevného disku.  Odkazy jsou k dispozici a provede vás provede jednotlivými kroky: zachycení bitové kopie a Zobecněte image.


## <a name="capture-the-vm-image"></a>Zachycení image virtuálního počítače

Postupujte podle pokynů v článku o zachycení virtuálního počítače, který odpovídá vašemu přístupu k přístupu:

-  PowerShell: [Vytvoření nespravované image virtuálního počítače z virtuálního počítače Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [Jak vytvořit image virtuálního počítače nebo virtuálního pevného disku](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtual Machines – zachytávání](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Zobecněte image virtuálního počítače

Protože jste vygenerovali uživatelská image z dříve generalizovaného virtuálního pevného disku, jeho by měl také zobecnit.  Znovu vyberte v následujícím článku, který odpovídá vaší mechanismus přístupu.  (Můžete může mít již zobecněný disk když jste zachytili.)

-  PowerShell: [Generalizace virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [Krok 2: Vytvoření image virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtual Machines – Generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Další postup

Dále se [vytvořit certifikát](cpp-create-key-vault-cert.md) a uložte ji na novou službu Azure Key Vault.  Tento certifikát je vyžadován pro vytvoření zabezpečeného WinRM připojení k virtuálnímu počítači.
