---
title: Vytvoření testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Tento článek vás provede procesem vytvoření testovacího prostředí pomocí Azure Portal a Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 09999c5b0187f924f9cfbbc2afad8954adee0fd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481251"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Vytvoření testovacího prostředí v Azure DevTest Labs
Testovací prostředí ve službě Azure DevTest Labs je infrastruktura, která zahrnuje skupinu prostředků, třeba službu Virtual Machines, která vám umožní lépe spravovat tyto prostředky zadáním omezení a kvót. Tento článek vás provede procesem vytvoření testovacího prostředí pomocí webu Azure Portal.

## <a name="prerequisites"></a>Požadavky
K vytvoření testovacího prostředí potřebujete:

* Předplatné Azure. Informace o možnostech nákupu Azure najdete v tématech [Jak koupit Azure](https://azure.microsoft.com/pricing/purchase-options/) nebo [Bezplatná zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/). Abyste mohli vytvořit testovací prostředí, musíte být vlastníky předplatného.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Postup vytvoření testovacího prostředí ve službě Azure DevTest Labs
Následující kroky ukazují postup vytvoření testovacího prostředí ve službě Azure DevTest Labs pomocí webu Azure Portal. 

1. Přihlaste se k [portálu Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V hlavní nabídce na levé straně vyberte **Všechny služby** (v horní části seznamu). V části **DEVOPS** vyberte * (hvězdička) vedle **DevTest Labs** . Tato akce přidá **DevTest Labs** do levé navigační nabídky, abyste k ní mohli snadno získat přístup později. 

    ![Všechny služby – výběr DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
2. Teď v levé navigační nabídce vyberte **DevTest Labs** . Na panelu nástrojů vyberte **Přidat** . 
   
    ![Přidání testovacího prostředí](./media/devtest-lab-create-lab/add-lab-button.png)
1. Na stránce **vytvořit testovací prostředí DevTest** proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí.
    2. Vyberte **Předplatné**, které bude přidruženo k testovacímu prostředí.
    3. Zadejte **název skupiny prostředků** pro testovací prostředí. 
    4. Vyberte **umístění** , do kterého chcete testovací prostředí uložit.
    4. Chcete-li povolit a nastavit parametry pro automatické vypínání virtuálních počítačů v testovacím prostředí, vyberte **Automatické vypnutí**. Funkce automatického vypnutí je především funkce na úsporu nákladů, pomocí které můžete určit, kdy se má virtuální počítač automaticky vypnout. Po vytvoření testovacího prostředí můžete nastavení automatického vypnutí změnit podle kroků uvedených v článku [Správa všech zásad pro testovací prostředí v Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    1. Pokud chcete vytvořit vlastní označování, které se přidá ke každému prostředku vytvořenému v testovacím prostředí, zadejte informace **NÁZEV** a **HODNOTA** pro **Značky**. Značky jsou užitečné a pomáhají při správě a organizaci prostředků testovacího prostředí podle kategorií. Další informace o značkách, včetně postupu pro přidání značek po vytvoření testovacího prostředí, najdete v tématu [Přidání značek do testovacího prostředí](devtest-lab-add-tag.md).
    6. Pokud chcete získat šablony Azure Resource Manageru pro automatizaci konfigurace, vyberte **Možnosti automatizace**. 
    7. Vyberte **Vytvořit**. Stav procesu vytvoření testovacího prostředí můžete monitorovat v oblasti **Oznámení**. 
    
        ![Vytvoření části testovacího prostředí služby DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. Po dokončení vyberte v oznámení **Přejít na prostředek** . Případně aktualizujte stránku **DevTest Labs** , abyste viděli nově vytvořenou laboratoř v seznamu cvičení.  V seznamu vyberte testovací prostředí. Zobrazí se Domovská stránka vašeho testovacího prostředí. 

        ![Domovská stránka pro testovací prostředí](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
Po vytvoření testovacího prostředí je zde několik kroků, které je vhodné zvážit:

* [Zabezpečení přístupu k testovacímu prostředí](devtest-lab-add-devtest-user.md)
* [Nastavení zásad testovacího prostředí](devtest-lab-set-lab-policy.md)
* [Vytvoření šablony testovacího prostředí](devtest-lab-create-template.md)
* [Vytvoření vlastních artefaktů pro virtuální počítače](devtest-lab-artifact-author.md)
* [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)

