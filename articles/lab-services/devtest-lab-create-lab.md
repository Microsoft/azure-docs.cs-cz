---
title: Vytvoření testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Vytvoření testovacího prostředí ve službě Azure DevTest Labs pro virtuální počítače
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 868417a9ca1dbcac9b2f77627944fc620fbe78cd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253903"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Vytvoření testovacího prostředí v Azure DevTest Labs
Testovací prostředí ve službě Azure DevTest Labs je infrastruktura, která zahrnuje skupinu prostředků, třeba službu Virtual Machines, která vám umožní lépe spravovat tyto prostředky zadáním omezení a kvót. Tento článek vás provede procesem vytvoření testovacího prostředí pomocí webu Azure Portal.

## <a name="prerequisites"></a>Požadavky
K vytvoření testovacího prostředí potřebujete:

* Předplatné Azure. Informace o možnostech nákupu Azure najdete v tématech [Jak koupit Azure](https://azure.microsoft.com/pricing/purchase-options/) nebo [Bezplatná zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/). Abyste mohli vytvořit testovací prostředí, musíte být vlastníky předplatného.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Postup vytvoření testovacího prostředí ve službě Azure DevTest Labs
Následující kroky ukazují postup vytvoření testovacího prostředí ve službě Azure DevTest Labs pomocí webu Azure Portal. 

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V hlavní nabídce na levé straně vyberte **Všechny služby** (v horní části seznamu).

    ![Možnost nabídky Všechny služby](./media/devtest-lab-create-lab/more-services-menu-option.png)

1. V seznamu dostupných služeb vyberte **DevTest Labs**.
1. V oblasti **DevTest Labs** vyberte **Přidat**.
   
    ![Přidání testovacího prostředí](./media/devtest-lab-create-lab/add-lab-button.png)

1. V části **Vytvořit DevTest Lab**:
   
    1. Zadejte **Název testovacího prostředí** pro nové prostředí.
    2. Vyberte **Předplatné**, které bude přidruženo k testovacímu prostředí.
    3. Vyberte **Umístění**, do kterého se testovací prostředí uloží.
    4. Chcete-li povolit a nastavit parametry pro automatické vypínání virtuálních počítačů v testovacím prostředí, vyberte **Automatické vypnutí**. Funkce automatického vypnutí je především funkce na úsporu nákladů, pomocí které můžete určit, kdy se má virtuální počítač automaticky vypnout. Po vytvoření testovacího prostředí můžete nastavení automatického vypnutí změnit podle kroků uvedených v článku [Správa všech zásad pro testovací prostředí v Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    1. Pokud chcete vytvořit vlastní označování, které se přidá ke každému prostředku vytvořenému v testovacím prostředí, zadejte informace **NÁZEV** a **HODNOTA** pro **Značky**. Značky jsou užitečné a pomáhají při správě a organizaci prostředků testovacího prostředí podle kategorií. Další informace o značkách, včetně postupu pro přidání značek po vytvoření testovacího prostředí, najdete v tématu [Přidání značek do testovacího prostředí](devtest-lab-add-tag.md).
    5. Pokud chcete na řídicím panelu portálu vytvořit zástupce testovacího prostředí, vyberte možnost **Připnout na řídicí panel**.
    6. Pokud chcete získat šablony Azure Resource Manageru pro automatizaci konfigurace, vyberte **Možnosti automatizace**. 
    7. Vyberte **Vytvořit**. Stav procesu vytvoření testovacího prostředí můžete monitorovat v oblasti **Oznámení**. Po dokončení aktualizujte stránku, abyste zobrazili nově vytvořené testovací prostředí v seznamu testovacích prostředí.  
    
    ![Vytvoření části testovacího prostředí služby DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
Po vytvoření testovacího prostředí je zde několik kroků, které je vhodné zvážit:

* [Zabezpečení přístupu k testovacímu prostředí](devtest-lab-add-devtest-user.md)
* [Nastavení zásad testovacího prostředí](devtest-lab-set-lab-policy.md)
* [Vytvoření šablony testovacího prostředí](devtest-lab-create-template.md)
* [Vytvoření vlastních artefaktů pro virtuální počítače](devtest-lab-artifact-author.md)
* [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)

