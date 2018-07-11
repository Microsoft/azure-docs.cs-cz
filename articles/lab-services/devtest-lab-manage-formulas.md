---
title: Správa vzorců ve službě Azure DevTest Labs k vytvoření virtuálních počítačů | Dokumentace Microsoftu
description: Zjistěte, jak aktualizovat a odebírat vzorce Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 40c30958c5e0e24f2b35475848c1828df4a40d0c
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "33787609"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Správa vzorců Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Tento článek ukazuje, jak vytvořit vzorec ze základní (vlastní image, image z Marketplace nebo jiného vzorce) nebo existujícího virtuálního počítače. Tento článek také provede vás procesem správy existující vzorce.

## <a name="create-a-formula"></a>Vytvoření vzorce
Kdokoli s DevTest Labs *uživatelé* oprávnění je možné vytvořit pomocí vzorce jako základ virtuálních počítačů. Existují dva způsoby, jak vytvářet vzorce: 

* Z base - použijte, pokud chcete definovat všechny charakteristiky vzorec.
* Z existující testovací prostředí virtuálního počítače – použijte, pokud chcete vytvořit vzorec, na základě nastavení z existujícího virtuálního počítače.

Další informace o přidávání uživatelů a oprávnění najdete v tématu [přidat vlastníky a uživatele ve službě Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Vytvoření vzorce ze základní
Následující kroky vás provedou procesem vytvoření vzorce z vlastní image, image z Marketplace nebo jiného vzorce.

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.

3. V seznamu testovacích prostředí vyberte požadované prostředí.  

4. V okně testovacího prostředí, vyberte **vzorce (opakovaně použitelné báze)**.
   
    ![Vzorec nabídky](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Na **vzorce** okně vyberte **+ přidat**.
   
    ![Přidat vzorec](./media/devtest-lab-create-formulas/add-formula.png)

6. Na **vyberte bázi** okně vyberte base (vlastní image, image z Marketplace nebo vzorec), ze kterého chcete vytvořit vzorec.
   
    ![Základní přehled](./media/devtest-lab-create-formulas/base-list.png)

7. Na **vytvořit vzorec** okně zadejte následující hodnoty:
   
    * **Název vzorce** – zadejte název pro váš vzorec. Tato hodnota se zobrazí v seznamu základních imagí při vytváření virtuálního počítače. Název je ověřit, protože jeho typ, a pokud není platný, zobrazí se zpráva požadavky na platný název.
    * **Popis** – Zadejte výstižný popis vzorec. Tato hodnota je dostupná v kontextové nabídce na vzorec, při vytváření virtuálního počítače.
    * **Uživatelské jméno** – zadejte uživatelské jméno, které je udělen oprávnění správce.
    * **Heslo** – zadejte - nebo vyberte z rozevíracího seznamu - hodnotu, která souvisí s tajným klíčem (heslo), který chcete použít pro zadaného uživatele. Další informace o tajné klíče najdete v tématu [Azure DevTest Labs: osobní úložiště tajných kódů](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store/).
    * **Typ disku virtuálního počítače** – zadejte buď pevný disk (jednotku pevného disku) nebo SSD (jednotky SSD) k označení jaký typ disku úložiště je povolený pro virtuální počítače zřízené s využitím této základní image.
    * ** Virtuální počítač velikost ** – vyberte jednu z předdefinovaných položek, které určují jader procesoru, velikosti paměti RAM a velikost pevného disku virtuálního počítače k vytvoření. 
    * **Artefakty** – tuto možnost vyberte, chcete-li otevřít **přidat artefakty** okno, ve kterém můžete vybrat a nakonfigurovat artefakty, které chcete přidat do základní image. Další informace o artefakty, naleznete v tématu [při vytváření vlastních artefaktů pro virtuální počítač Azure DevTest Labs](devtest-lab-artifact-author.md).
    * **Upřesňující nastavení** – tuto možnost vyberte, chcete-li otevřít **Upřesnit** okno, kde můžete nakonfigurovat následující nastavení:
        * **Virtuální síť** – zadejte požadovanou virtuální síť.
        * **Podsíť** -zadat požadované podsítě.    
        * **Konfiguraci IP adresy** – určete, zda má veřejný, privátní nebo sdílených IP adres. Další informace o sdílených IP adres najdete v tématu [Principy sdílených IP adres ve službě Azure DevTest Labs](./devtest-lab-shared-ip.md).
        * **Nastavit tento počítač nárokovatelných** – provádění na počítači "nárokovatelných" znamená, že ho nepřiřadí vlastnictví v době vytvoření. Místo toho uživatelé testovacího prostředí budou moci převzít vlastnictví ("deklarace") počítače v okně testovacího prostředí.     
    * **Obrázek** – toto pole zobrazí název základní image, které jste vybrali v předchozím okně. 
     
       ![Vytvoření vzorce](./media/devtest-lab-create-formulas/create-formula.png)

8. Vyberte **vytvořit** vytvořit vzorec.

9. Po vytvoření vzorce, se zobrazí v seznamu na **vzorce** okno.

### <a name="create-a-formula-from-a-vm"></a>Vytvoření vzorce z virtuálního počítače
Následující kroky vás provedou procesem vytvoření vzorce na základě existujícího virtuálního počítače. 

> [!NOTE]
> Chcete-li vytvořit vzorec z virtuálního počítače, musí být nejprve vytvořen virtuální počítač po 30. března 2016. 
> 
> 

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte požadované prostředí.  
4. Cvičení **přehled** okno, vyberte virtuální počítač, ze kterého chcete vytvořit vzorec.
   
    ![Testovací prostředí virtuálních počítačů](./media/devtest-lab-create-formulas/my-vms.png)
5. V okně Virtuálního počítače, vyberte **vytvořit vzorec (opakovaně použitelná báze)**.
   
    ![Vytvoření vzorce](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na **vytvořit vzorec** okno, zadejte **název** a **popis** pro nový vzorec.
   
    ![Vytvořit okno vzorce](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Vyberte **OK** vytvořit vzorec.

## <a name="modify-a-formula"></a>Upravit vzorec
Pokud chcete upravit vzorec, postupujte takto:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte požadované prostředí.  
4. V okně testovacího prostředí, vyberte **vzorce (opakovaně použitelné báze)**.
   
    ![Vzorec nabídky](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na **Lab vzorce** okně vyberte vzorec, který chcete upravit.
6. Na **aktualizovat vzorec** okno, proveďte požadované úpravy a vyberte **aktualizace**.

## <a name="delete-a-formula"></a>Odstranit vzorce
Pokud chcete odstranit vzorec, postupujte takto:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte požadované prostředí.  
4. V testovacím prostředí **nastavení** okně vyberte **vzorce**.
   
    ![Vzorec nabídky](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na **Lab vzorce** okno, vyberte tři tečky vpravo od vzorec, který chcete odstranit.
   
    ![Vzorec nabídky](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. V místní nabídce na vzorec, vyberte **odstranit**.
   
    ![Vzorec kontextové nabídky](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Vyberte **Ano** k potvrzení odstranění.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související blogové příspěvky
* [Vlastní Image nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Další postup
Po vytvoření vzorce pro použití při vytváření virtuálního počítače, dalším krokem je [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md).

