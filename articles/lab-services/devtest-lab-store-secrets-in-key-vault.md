---
title: Store tajné klíče v trezoru klíčů ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak tajné kódy ukládat v Azure Key Vault a jejich používání při vytváření virtuálního počítače, vzorců, nebo prostředí.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: d87c8a46459a9b4bf80bef895ec97e436d38e699
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186828"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Store tajné klíče v trezoru klíčů ve službě Azure DevTest Labs
Budete muset zadat komplexní tajný klíč, když pomocí Azure DevTest Labs: heslo pro váš virtuální počítač Windows veřejný klíč SSH pro virtuální počítač s Linuxem nebo osobní přístupový token pro klonování úložiště Git pomocí artefakt. Tajné kódy jsou obvykle dlouhé a mají náhodných znaků. Proto je zadání může být složité a nepohodlná, zejména pokud používáte stejný tajný klíč více než jednou.

Chcete-li tento problém vyřešit a zároveň udržovat vaše tajné kódy na bezpečném místě, DevTest Labs podporuje ukládání tajných kódů v [službou Azure key vault](../key-vault/key-vault-overview.md). Když uživatel uloží tajného kódu poprvé, služba DevTest Labs automaticky vytvoří trezor klíčů ve stejné skupině prostředků, který obsahuje testovací prostředí a uloží ve službě key vault tajný klíč. DevTest Labs vytvoří samostatné služby key vault pro každého uživatele. 

## <a name="save-a-secret-in-azure-key-vault"></a>Uložte tajný klíč ve službě Azure Key Vault
Uložte tajný klíč ve službě Azure Key Vault, proveďte následující kroky:

1. Vyberte **tajné** v nabídce vlevo.
2. Zadejte **název** pro tajný kód. Zobrazí tento název v rozevíracím seznamu při vytváření virtuálního počítače, vzorců, nebo v prostředí. 
3. Zadejte tajný kód jako **hodnota**.

    ![Tajný kód Store](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Použití tajného klíče ze služby Azure Key Vault
Když budete muset zadat tajný kód k vytvoření virtuálního počítače, vzorců, nebo v prostředí, můžete zadat tajný klíč ručně nebo vyberte uložený tajný kód trezoru klíčů. Pokud chcete použít tajného klíče uložené v trezoru klíčů, proveďte následující akce:

1. Vyberte **použít uložený tajný kód**. 
2. Vyberte z rozevíracího seznamu pro váš tajný klíč **výběr tajného kódu**. 

    ![Použijte tajný klíč na virtuálním počítači](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Použití tajného kódu v šabloně Azure Resource Manageru
Název tajného kódu můžete zadat v šabloně Azure Resource Manageru, který slouží k vytvoření virtuálního počítače, jak je znázorněno v následujícím příkladu:

![Použijte tajný klíč v prostředí nebo vzorce](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Další postup

- [Vytvoření virtuálního počítače pomocí tajného klíče](devtest-lab-add-vm.md) 
- [Vytvořit vzorec, pomocí tajného klíče](devtest-lab-manage-formulas.md)
- [Vytvořte prostředí pomocí tajného klíče](devtest-lab-create-environment-from-arm.md)
