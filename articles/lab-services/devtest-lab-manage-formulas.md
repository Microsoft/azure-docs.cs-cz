---
title: Spravovat vzorce v Azure DevTest Labs pro vytvoření virtuálních počítačů | Microsoft Docs
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
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "33787609"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Správa Azure DevTest Labs vzorce

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Tento článek ukazuje, jak vytvořit vzorec ze základní (vlastní image, Marketplace image nebo jiného vzorce) nebo stávajícího virtuálního počítače. Tento článek také vás provede Správa existující vzorce.

## <a name="create-a-formula"></a>Vytvoření vzorce
Každý, kdo má DevTest Labs *uživatelé* oprávnění je možné vytvořit pomocí vzorce jako na základní virtuální počítače. Existují dva způsoby vytvoření vzorce: 

* Z základní - použijte, pokud chcete definovat všechny vlastnosti vzorec.
* Z existujícího testovacího prostředí virtuálního počítače – použijte v případě, že chcete vytvořit vzorec na základě nastavení existujícího virtuálního počítače.

Další informace o přidávání uživatelů a oprávnění najdete v tématu [přidat vlastníků a uživatelé v Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Vytvoření vzorce od základní
Následující postup vás provede procesem vytvoření vzorec z vlastní image, Marketplace image nebo jiného vzorce.

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.

3. Ze seznamu labs vyberte požadované testovací prostředí.  

4. V okně v prostředí, vyberte **vzorce (opakovaně použitelné základů)**.
   
    ![Vzorce nabídky](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Na **vzorce** vyberte **+ přidat**.
   
    ![Přidat vzorec.](./media/devtest-lab-create-formulas/add-formula.png)

6. Na **zvolte na základní** okně vyberte základní (vlastní image, Marketplace image nebo vzorec), ze kterého chcete vytvořit vzorec.
   
    ![Základní seznam](./media/devtest-lab-create-formulas/base-list.png)

7. Na **vytvořit vzorec** okno, zadejte následující hodnoty:
   
    * **Název vzorce** – zadejte název vzorce. Tato hodnota se zobrazí v seznamu základní Image při vytváření virtuálního počítače. Název je ověřit, protože jej zadáte, a pokud není platný, zprávu označuje požadavky na platný název.
    * **Popis** -Zadejte výstižný popis vzorce. Tato hodnota je k dispozici vzorec místní nabídce, když vytvoření virtuálního počítače.
    * **Uživatelské jméno** -zadejte uživatelské jméno, které jsou udělena oprávnění správce.
    * **Heslo** – zadejte - nebo vyberte z rozevíracího seznamu - hodnotu, která souvisí s tajný klíč (heslo), který chcete použít pro zadaného uživatele. Další informace o těchto tajných klíčů najdete v tématu [Azure DevTest Labs: osobním úložišti tajný](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store/).
    * **Typ disku virtuálního počítače** – zadejte buď HDD (jednotku pevného disku) nebo SSD (jednotka SSD) k označení disku typů úložiště, které je povoleno pro virtuální počítače zřízené tuto základní image používá.
    * ** Virtuální počítač velikost ** – vyberte jednu z předdefinovaných položky, které zadejte jader procesoru, velikosti paměti RAM a velikost pevného disku pro vytvoření virtuálního počítače. 
    * **Artefakty** – vyberte, chcete-li otevřít **přidat artefakty** okno, ve kterém můžete vybrat a nakonfigurovat artefaktů, které chcete přidat do základní bitové kopie. Další informace o artefakty najdete v tématu [vytvoření vlastních artefaktů pro virtuální počítač Azure DevTest Labs](devtest-lab-artifact-author.md).
    * **Upřesňující nastavení** – vyberte, chcete-li otevřít **Upřesnit** okno, kde můžete nakonfigurovat následující nastavení:
        * **Virtuální síť** -zadejte požadované virtuální síť.
        * **Podsíť** -zadejte požadovanou podsíť.    
        * **Konfiguraci IP adresy** – určete, zda mají veřejné, privátní nebo sdílet IP adres. Další informace o sdílené IP adresy najdete v tématu [Rady pro pochopení sdílet IP adresy v Azure DevTest Labs](./devtest-lab-shared-ip.md).
        * **Nastavit tento počítač vymahatelných** -provádění na počítač "vymahatelných" znamená, že jej nebude přiřadit vlastnictví v době vytvoření. Místo toho lab uživatelů bude moci převzít vlastnictví ("deklarace identity") počítače v okně v prostředí.     
    * **Obrázek** – toto pole zobrazuje název základní image jste vybrali na předchozí okna. 
     
       ![Vytvoření vzorce](./media/devtest-lab-create-formulas/create-formula.png)

8. Vyberte **vytvořit** vytvořit vzorec.

9. Po vytvoření vzorec, se zobrazí v seznamu na **vzorce** okno.

### <a name="create-a-formula-from-a-vm"></a>Vytvoření vzorce z virtuálního počítače
Následující postup vás provede procesem vytvoření vzorec podle stávajícího virtuálního počítače. 

> [!NOTE]
> Pokud chcete vytvořit vzorec z virtuálního počítače, musí být nejprve vytvořen virtuální počítač po 30. března 2016. 
> 
> 

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.
3. Ze seznamu labs vyberte požadované testovací prostředí.  
4. V tomto prostředí **přehled** okně vyberte virtuální počítač, ze kterého chcete vytvořit vzorec.
   
    ![Virtuální laboratoře počítače](./media/devtest-lab-create-formulas/my-vms.png)
5. V okně Virtuálního počítače, vyberte **vytvořit vzorec (opakovaně použitelné base)**.
   
    ![Vytvoření vzorce](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na **vytvořit vzorec** okno, zadejte **název** a **popis** pro nový vzorec.
   
    ![Vzorce okno vytvořit](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Vyberte **OK** vytvořit vzorec.

## <a name="modify-a-formula"></a>Upravit vzorec.
Pokud chcete upravit vzorec, postupujte takto:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.
3. Ze seznamu labs vyberte požadované testovací prostředí.  
4. V okně v prostředí, vyberte **vzorce (opakovaně použitelné základů)**.
   
    ![Vzorce nabídky](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na **testovacím vzorce** okně vyberte vzorec, který chcete upravit.
6. Na **aktualizovat vzorec** okně udělejte požadované úpravy a vyberte **aktualizace**.

## <a name="delete-a-formula"></a>Odstranit vzorec.
Pokud chcete odstranit vzorec, postupujte takto:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.
3. Ze seznamu labs vyberte požadované testovací prostředí.  
4. V testovacím prostředí **nastavení** vyberte **vzorce**.
   
    ![Vzorce nabídky](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na **testovacím vzorce** okně vyberte se třemi tečkami napravo od vzorec chcete odstranit.
   
    ![Vzorce nabídky](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. V místní nabídce vzorec, vyberte **odstranit**.
   
    ![Vzorce kontextové nabídky](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Vyberte **Ano** do dialogového okna potvrzení odstranění.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Příspěvky blogu související
* [Vlastní Image nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Další postup
Po vytvoření vzorec pro použití při vytváření virtuálního počítače, dalším krokem je [přidat virtuální počítač do testovacího prostředí](devtest-lab-add-vm.md).

