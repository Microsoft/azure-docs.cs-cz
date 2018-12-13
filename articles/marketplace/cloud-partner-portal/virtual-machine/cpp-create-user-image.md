---
title: Vytvoření uživatelské image virtuálního počítače pro Azure Marketplace | Dokumentace Microsoftu
description: Jsou uvedené kroky a odkazy na požadovaná k vytvoření uživatelské image virtuálního počítače.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: bf87856dc28e83fb1308f20613338b9bbfd8f896
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196751"
---
# <a name="create-a-user-vm-image"></a>Vytvoření uživatelské image virtuálního počítače

Tento článek vysvětluje dvě obecné kroky potřebné k vytvoření nespravované image z generalizovaného virtuálního pevného disku.  Odkazy jsou k dispozici a provede vás provede jednotlivými kroky: zachycení bitové kopie a Zobecněte image.


## <a name="capture-the-vm-image"></a>Zachycení image virtuálního počítače

Postupujte podle pokynů v článku o zachycení virtuálního počítače, který odpovídá vašemu přístupu k přístupu:

-  PowerShell: [Vytvoření nespravované image virtuálního počítače z virtuálního počítače Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [Jak vytvořit image virtuálního počítače nebo virtuálního pevného disku](../../../virtual-machines/linux/capture-image.md)
-  ROZHRANÍ API: [Virtual Machines – zachytávání](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Zobecněte image virtuálního počítače

Protože jste vygenerovali uživatelská image z dříve generalizovaného virtuálního pevného disku, jeho by měl také zobecnit.  Znovu vyberte v následujícím článku, který odpovídá vaší mechanismus přístupu.  (Můžete může mít již zobecněný disk když jste zachytili.)

-  PowerShell: [Generalizace virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [Krok 2: Vytvoření image virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  ROZHRANÍ API: [Virtual Machines – Generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Další postup

Dále se [vytvořit certifikát](cpp-create-key-vault-cert.md) a uložte ji na novou službu Azure Key Vault.  Tento certifikát je vyžadován pro vytvoření zabezpečeného WinRM připojení k virtuálnímu počítači.
