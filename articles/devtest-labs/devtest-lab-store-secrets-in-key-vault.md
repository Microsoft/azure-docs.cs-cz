---
title: Ukládejte tajné klíče v trezoru klíčů v Azure DevTest Labs | Microsoft Docs
description: Naučte se ukládat tajné klíče do Azure Key Vault a používat je při vytváření virtuálního počítače, vzorce nebo prostředí.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5714279ef183cb930d643575466dae3d6cb69bba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481642"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Ukládejte tajné klíče v trezoru klíčů v Azure DevTest Labs
Pokud používáte Azure DevTest Labs: heslo pro virtuální počítač s Windows, veřejný klíč SSH pro virtuální počítač se systémem Linux nebo token pro osobní přístup k klonování úložiště Git pomocí artefaktu, může být potřeba zadat složitý tajný klíč. Tajné kódy jsou obvykle dlouhé a mají náhodné znaky. Zadávání těchto údajů může být obtížné a nepohodlné, obzvláště pokud použijete stejný tajný klíč několikrát.

DevTest Labs podporuje ukládání tajných klíčů v [trezoru klíčů Azure](../key-vault/general/overview.md), aby tento problém vyřešil a na bezpečném místě zajistil i tajné klíče. Když uživatel poprvé uloží tajný klíč, služba DevTest Labs automaticky vytvoří Trezor klíčů ve stejné skupině prostředků, která obsahuje testovací prostředí a uloží tajný klíč do trezoru klíčů. DevTest Labs vytvoří pro každého uživatele samostatný Trezor klíčů. 

Všimněte si, že před vytvořením tajného klíče v trezoru klíčů bude uživatel testovacího prostředí muset nejdřív vytvořit virtuální počítač testovacího prostředí. Je to proto, že služba DevTest Labs musí přidružit uživatele testovacího prostředí k platnému uživatelskému dokumentu, aby mohli vytvářet a ukládat tajné klíče v trezoru klíčů. 


## <a name="save-a-secret-in-azure-key-vault"></a>Uložení tajného kódu v Azure Key Vault
Pokud chcete svůj tajný klíč uložit v Azure Key Vault, proveďte následující kroky:

1. V nabídce vlevo vyberte **Moje tajné** kódy.
2. Zadejte **název** tajného kódu. Tento název se zobrazí v rozevíracím seznamu při vytváření virtuálního počítače, vzorce nebo prostředí. 
3. Jako **hodnotu** zadejte tajný klíč.

    ![Tajný kód úložiště](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Použít tajný klíč z Azure Key Vault
Pokud k vytvoření virtuálního počítače, vzorce nebo prostředí potřebujete zadat tajný klíč, můžete buď zadat tajný klíč ručně, nebo z trezoru klíčů vybrat uložený tajný klíč. Chcete-li použít tajný klíč uložený v trezoru klíčů, proveďte následující akce:

1. Vyberte možnost **použít uložený tajný kód**. 
2. Z rozevíracího seznamu vyberte svůj tajný kód pro **Výběr tajného klíče**. 

    ![Použití tajného klíče ve virtuálním počítači](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Použití tajného klíče v šabloně Azure Resource Manager
Svůj tajný název můžete zadat v šabloně Azure Resource Manager, která se používá k vytvoření virtuálního počítače, jak je znázorněno v následujícím příkladu:

![Použití tajného klíče ve vzorci nebo prostředí](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Další kroky

- [Vytvoření virtuálního počítače pomocí tajného klíče](devtest-lab-add-vm.md) 
- [Vytvoření vzorce pomocí tajného klíče](devtest-lab-manage-formulas.md)
- [Vytvoření prostředí pomocí tajného klíče](devtest-lab-create-environment-from-arm.md)
