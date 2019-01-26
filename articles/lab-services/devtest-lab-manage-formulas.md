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
ms.openlocfilehash: 60790f0f31915a50829df09d039a4f74860a47d7
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076430"
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

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.

3. V seznamu testovacích prostředí vyberte požadované prostředí.  

4. Na stránce testovacího prostředí, vyberte možnost **vzorce (opakovaně použitelné báze)**.
   
    ![Vzorec nabídky](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Na **vzorce** stránce **+ přidat**.
   
    ![Přidat vzorec](./media/devtest-lab-create-formulas/add-formula.png)

6. Na **vyberte bázi** vyberte base (vlastní image, image z Marketplace nebo vzorec), ze kterého chcete vytvořit vzorec.
   
    ![Základní přehled](./media/devtest-lab-create-formulas/base-list.png)

7. Na **základní nastavení** karty **vytvořit vzorec** stránky, zadejte následující hodnoty:
   
    * **Název vzorce** – zadejte název pro váš vzorec. Tato hodnota se zobrazí v seznamu základních imagí při vytváření virtuálního počítače. Název je ověřit, protože jeho typ, a pokud není platný, zobrazí se zpráva požadavky na platný název.
    * **Uživatelské jméno** – zadejte uživatelské jméno, které je udělen oprávnění správce.
    * **Heslo** – zadejte - nebo vyberte z rozevíracího seznamu - hodnotu, která souvisí s tajným klíčem (heslo), který chcete použít pro zadaného uživatele. Další informace o ukládání tajných klíčů v trezoru klíčů a jejich používání při vytváření prostředků testovacího prostředí, najdete v článku [Store tajné kódy ve službě Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).
    * **Velikost virtuálního počítače** – vyberte **změnit velikost** ke změně velikosti virtuálního počítače. 
    * **Artefakty** – vyberte **přidat nebo odebrat artefakty** stránky, ve kterém můžete vybrat a nakonfigurovat artefakty, které chcete přidat do základní image. Další informace o artefakty, naleznete v tématu [při vytváření vlastních artefaktů pro virtuální počítač Azure DevTest Labs](devtest-lab-artifact-author.md).
8. Přepněte **upřesňující nastavení** kartu a zadejte následující hodnoty:
    - **Virtuální síť** – Chcete-li změnit virtuální síť, vyberte **změnit virtuální síť**. 
    - **Podsíť** – Chcete-li změnit podsíť, vyberte **změnit podsíť**. 
    - **Konfiguraci IP adresy** – určete, zda má veřejný, privátní nebo sdílených IP adres. Další informace o sdílených IP adres najdete v tématu [Principy sdílených IP adres ve službě Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Datum a čas vypršení** – zadejte datum vypršení platnosti a čas pro virtuální počítač tak, aby virtuální počítač se automaticky odstraní. 
    - **Nastavit tento počítač nárokovatelných** – provádění na počítači "nárokovatelných" znamená, že ho nepřiřadí vlastnictví v době vytvoření. Místo toho uživatelé testovacího prostředí budou moci převzít vlastnictví ("deklarace") počítače na stránce testovacího prostředí.     
    - **Počet instancí nárokovatelných** – určete, kolik nárokovatelných instacnes, kterou chcete vytvořit. 
8. Vyberte **odeslat** vytvořit vzorec.

9. Po vytvoření vzorce, se zobrazí v seznamu na **vzorce** stránky.

### <a name="create-a-formula-from-a-vm"></a>Vytvoření vzorce z virtuálního počítače
Následující kroky vás provedou procesem vytvoření vzorce na základě existujícího virtuálního počítače. 

> [!NOTE]
> Chcete-li vytvořit vzorec z virtuálního počítače, musí být nejprve vytvořen virtuální počítač po 30. března 2016. 
> 
> 

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte požadované prostředí.  
4. Cvičení **přehled** stránky, vyberte virtuální počítač, ze kterého chcete vytvořit vzorec.
   
    ![Testovací prostředí virtuálních počítačů](./media/devtest-lab-create-formulas/my-vms.png)
5. Na stránce Virtuálního počítače, vyberte možnost **vytvořit vzorec (opakovaně použitelná báze)**.
   
    ![Vytvoření vzorce](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na **vytvořit vzorec** stránky, zadejte **název** a **popis** pro nový vzorec.
   
    ![Vytvoření vzorce stránky](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Vyberte **OK** vytvořit vzorec.

## <a name="modify-a-formula"></a>Upravit vzorec
Pokud chcete upravit vzorec, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte požadované prostředí.  
4. Na stránce testovacího prostředí, vyberte možnost **vzorce (opakovaně použitelné báze)**.
   
    ![Vzorec nabídky](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na **Lab vzorce** vyberte vzorec, který chcete upravit.
6. Na **aktualizovat vzorec** stránce, proveďte požadované úpravy a vyberte **aktualizace**.

## <a name="delete-a-formula"></a>Odstranit vzorce
Pokud chcete odstranit vzorec, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte požadované prostředí.  
4. V testovacím prostředí **nastavení** stránce **vzorce**.
   
    ![Vzorec nabídky](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na **Lab vzorce** stránky, vyberte tři tečky vpravo od vzorec, který chcete odstranit.
   
    ![Vzorec nabídky](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. V místní nabídce na vzorec, vyberte **odstranit**.
   
    ![Vzorec kontextové nabídky](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Vyberte **Ano** k potvrzení odstranění.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související blogové příspěvky
* [Vlastní Image nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Další postup
Po vytvoření vzorce pro použití při vytváření virtuálního počítače, dalším krokem je [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md).

