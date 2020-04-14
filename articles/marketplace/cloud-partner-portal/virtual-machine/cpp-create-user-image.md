---
title: Vytvoření image uživatele virtuálního počítače pro Azure Marketplace
description: Uvádí kroky a odkazy potřebné k vytvoření image virtuálního uživatele uživatele.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6bbee7f53cb9a61b72bdbbd941a3a0401f5b913b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273949"
---
# <a name="create-a-user-vm-image"></a>Vytvoření uživatelské image virtuálního počítače

> [!IMPORTANT]
> dubna 2020 začneme s přesouváním nabídky virtuálních strojů Azure do Partnerského centra. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření technických prostředků virtuálního počítače Azure](https://aka.ms/AzureVMTechAsset) a spravujte migrované nabídky.

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
