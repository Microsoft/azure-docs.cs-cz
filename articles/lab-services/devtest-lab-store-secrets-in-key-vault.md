---
title: Ukládání tajných kódů v trezoru klíčů v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak ukládat tajné klíče v trezoru klíčů Azure a používat je při vytváření virtuálního počítače, vzorce nebo prostředí.
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
ms.openlocfilehash: 0ca36a7081aaf70ee2045ee7586184c89591df16
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461510"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Ukládání tajných kódů do trezoru klíčů v laboratořích Azure DevTest Labs
Možná budete muset zadat komplexní tajný klíč při použití Azure DevTest Labs: heslo pro váš virtuální počítač s Windows, veřejný klíč SSH pro váš virtuální počítač s Linuxem nebo osobní přístupový token pro klonování úložiště Gitprostředprostřednictvím artefaktu. Tajemství jsou obvykle dlouhá a mají náhodné znaky. Proto jejich zadání může být složité a nepohodlné, zvláště pokud používáte stejné tajemství vícekrát.

Chcete-li tento problém vyřešit a také uchovávat vaše tajemství na bezpečném místě, DevTest Labs podporuje ukládání tajných kódů v [trezoru klíčů Azure](../key-vault/general/overview.md). Když uživatel uloží tajný klíč poprvé, služba DevTest Labs automaticky vytvoří trezor klíčů ve stejné skupině prostředků, která obsahuje testovací prostředí a uloží tajný klíč do trezoru klíčů. DevTest Labs vytvoří samostatný trezor klíčů pro každého uživatele. 

Upozorňujeme, že uživatel testovacího prostředí bude muset nejprve vytvořit virtuální počítač testovacího prostředí, než bude moci vytvořit tajný klíč v trezoru klíčů. Důvodem je, že služba DevTest Lab potřebuje přidružit uživatele testovacího prostředí k platnému uživatelskému dokumentu, než mohou vytvářet a ukládat tajné klíče v trezoru klíčů. 


## <a name="save-a-secret-in-azure-key-vault"></a>Uložení tajného klíče do služby Azure Key Vault
Pokud chcete tajný klíč uložit do služby Azure Key Vault, postupujte takto:

1. V levé nabídce vyberte **moje tajemství.**
2. Zadejte **název** tajného klíče. Tento název se zobrazí v rozevíracím seznamu při vytváření virtuálního virtuálního soudu, vzorce nebo prostředí. 
3. Zadejte tajný klíč jako **hodnotu**.

    ![Ukládat tajné klíče](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Použití tajného klíče z azure key vaultu
Když potřebujete zadat tajný klíč k vytvoření virtuálního virtuálního soudu, vzorce nebo prostředí, můžete buď zadat tajný klíč ručně, nebo vybrat uložený tajný klíč z trezoru klíčů. Chcete-li použít tajný klíč uložený v trezoru klíčů, proveďte následující akce:

1. Vyberte **Použít uložený tajný klíč**. 
2. Vyberte svůj tajný klíč z rozevíracího seznamu **pro vyskladnění tajného klíče**. 

    ![Použití tajného klíče ve virtuálním provozu](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Použití tajného klíče v šabloně Správce prostředků Azure
Svůj tajný název můžete zadat v šabloně Azure Resource Manager, která se používá k vytvoření virtuálního počítače, jak je znázorněno v následujícím příkladu:

![Použití tajného klíče ve vzorci nebo prostředí](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Další kroky

- [Vytvoření virtuálního virtuálního aplikace pomocí tajného klíče](devtest-lab-add-vm.md) 
- [Vytvoření vzorce pomocí tajného klíče](devtest-lab-manage-formulas.md)
- [Vytvoření prostředí pomocí tajného klíče](devtest-lab-create-environment-from-arm.md)
