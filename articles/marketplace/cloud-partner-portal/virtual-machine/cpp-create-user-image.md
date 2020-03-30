---
title: Vytvoření image uživatele virtuálního počítače pro Azure Marketplace
description: Uvádí kroky a odkazy potřebné k vytvoření image virtuálního uživatele uživatele.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 49db8c6717cd26886c3b49f8c99fdd2b08e8713d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278003"
---
# <a name="create-a-user-vm-image"></a>Vytvoření uživatelské image virtuálního počítače

Tento článek vysvětluje dva obecné kroky potřebné k vytvoření nespravovaného obrazu z generalizovaného virtuálního pevného disku.  Odkazy jsou k dispozici, aby vás provedly jednotlivými kroky: zachycení obrazu a zobecnění obrazu.


## <a name="capture-the-vm-image"></a>Zachycení image virtuálního mísa

Použijte pokyny v následujícím článku o zachycení virtuálního počítače, který odpovídá přístupu k přístupu:

-  Prostředí PowerShell: [Jak vytvořit nespravovanou image virtuálního počítače z virtuálního počítače Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [Jak vytvořit image virtuálního počítače nebo virtuálního pevného disku](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtuální počítače – digitalizace](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Zobecnění image virtuálního mísa

Vzhledem k tomu, že jste vygenerovali bitovou kopii uživatele z dříve generalizovaného virtuálního pevného disku, měla by být také generalizována.  Opět vyberte následující článek, který odpovídá mechanismu přístupu.  (Je možné, že jste disk při jeho zachycení již zobecnili.)

-  Prostředí PowerShell: [Zobecnění virtuálního virtuálního montovadou](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [Krok 2: Vytvoření image virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtuální počítače – generalizace](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Další kroky

Dále [vytvoříte certifikát](cpp-create-key-vault-cert.md) a uložíte ho do nového trezoru klíčů Azure.  Tento certifikát je vyžadován pro navázání zabezpečeného připojení WinRM k virtuálnímu virtuálnímu účtu.
