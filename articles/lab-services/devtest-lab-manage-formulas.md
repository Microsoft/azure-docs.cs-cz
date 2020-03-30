---
title: Správa vzorců v azure devtest labs k vytváření virtuálních počítačů | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak vytvořit vzorec ze základní (vlastní image, image Marketplace nebo jiného vzorce) nebo z existujícího virtuálního virtuálního uživatele.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 4f8c8d4ff0a8014fe0b9a6ae4aead35ec7df4bf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501977"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Správa vzorců Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Tento článek ukazuje, jak vytvořit vzorec ze základní (vlastní image, image Marketplace nebo jiného vzorce) nebo z existujícího virtuálního virtuálního uživatele. Tento článek vás také provede správou existujících vzorců.

## <a name="create-a-formula"></a>Vytvoření vzorce
Kdokoli s *oprávněními uživatelů* DevTest Labs je schopen vytvářet virtuální uživatele pomocí vzorce jako základu. Vzorce lze vytvořit dvěma způsoby: 

* Ze základny - Použijte, pokud chcete definovat všechny charakteristiky vzorce.
* Z existujícího virtuálního počítače testovacího prostředí – použijte, pokud chcete vytvořit vzorec na základě nastavení existujícího virtuálního počítače.

Další informace o přidávání uživatelů a oprávnění najdete v tématu [Přidání vlastníků a uživatelů v laboratořích Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Vytvoření vzorce ze základny
Následující kroky vás provedou procesem vytváření vzorce z vlastního obrázku, obrázku Marketplace nebo jiného vzorce.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**

3. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.  

4. Na stránce testovacího prostředí vyberte **vzorce (opakovaně použitelné základy)** v levé nabídce.
5. Na stránce **Vzorce** vyberte **+ Přidat**.
   
    ![Přidání vzorce](./media/devtest-lab-create-formulas/add-formula.png)

6. Na stránce **Zvolit základní** vyberte základnu (vlastní obrázek nebo obrázek Marketplace), ze které chcete vzorec vytvořit.
7. Na kartě **Základní nastavení** na stránce **Vytvořit vzorec** zadejte následující hodnoty:
   
    * **Název vzorce** - Zadejte název vzorce. Tato hodnota se zobrazí v seznamu základních bitových kopií při vytváření virtuálního virtuálního soudu. Název je ověřen při psaní a pokud není platný, zpráva označuje požadavky na platný název.
    - Zadejte volitelný **popis** vzorce. 
    * **Uživatelské jméno** – Zadejte uživatelské jméno, kterému jsou udělena oprávnění správce.
    * **Heslo** - Zadejte - nebo vyberte z rozevíracího souboru - hodnotu, která je přidružena k tajnému (heslu), které chcete použít pro zadaného uživatele. Informace o ukládání tajných kódů v trezoru klíčů a jejich použití při vytváření prostředků testovacího prostředí najdete [v tématu Ukládání tajných kódů v úložišti klíčů Azure](devtest-lab-store-secrets-in-key-vault.md).

        Pokud chcete použít tajný klíč z trezoru klíčů Azure místo hesla, vyberte **Použít uložený tajný klíč.** 
    * **Velikost virtuálního počítače** – výběrem **možnosti Změnit velikost** změníte velikost virtuálního počítače. 
    - **Typ disku operačního systému** – vyberte typ disku, který chcete použít (Standardní HDD, Standardní SSD nebo Premium SSD).
    * **Artefakty** – vyberte **přidat nebo odebrat artefakty** stránku, ve kterém vyberete a nakonfigurovat artefakty, které chcete přidat do základní image. Další informace o artefaktech najdete v [tématu Vytváření vlastních artefaktů pro váš virtuální počítač Azure DevTest Labs](devtest-lab-artifact-author.md).

        ![Stránka základního nastavení](./media/devtest-lab-create-formulas/basic-settings.png)
8. Přepněte na kartu **Upřesnit nastavení** a určete následující hodnoty:
    - **Virtuální síť** – Chcete-li změnit virtuální síť, vyberte **změnit virtuální síť**. 
    - **Podsíť** - Chcete-li podsíť změnit, vyberte **Změnit podsíť**. 
    - **Konfigurace IP adresy** – Určete, zda chcete veřejnou, soukromou nebo sdílenou IP adresu. Další informace o sdílených IP adresách najdete [v tématu Principy sdílených IP adres v laboratořích Azure DevTest .](./devtest-lab-shared-ip.md)
    - **Datum a čas vypršení platnosti** – toto pole nelze upravit. 
    - **Aby byl tento stroj nárokovatelný** - Vytvoření stroje "nárokovatelného" znamená, že mu nebude přiděleno vlastnictví v době vytvoření. Místo toho uživatelé testovacího prostředí budou moci převzít vlastnictví ("deklarace") počítače na stránce testovacího prostředí.  

        ![Stránka základního nastavení](./media/devtest-lab-create-formulas/advanced-settings.png)
8. Chcete-li vzorec vytvořit, vyberte **Odeslat.**

9. Po vytvoření se vzorec zobrazí v seznamu na stránce **Vzorce.**

### <a name="create-a-formula-from-a-vm"></a>Vytvoření vzorce z virtuálního virtuálního mísy
Následující kroky vás provedou procesem vytváření vzorce založeného na existujícím virtuálním počítači. 

> [!NOTE]
> Chcete-li vytvořit vzorec z virtuálního virtuálního soudu, musí být virtuální ho dispono vytvořenpo 30. 
> 
> 

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
3. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.  
4. Na stránce **Přehled** testovacího prostředí vyberte virtuální počítač, ze kterého chcete vzorec vytvořit.
   
    ![Virtuální společnosti v laboratořích](./media/devtest-lab-create-formulas/my-vms.png)
5. Na stránce virtuálního počítači vyberte **Vytvořit vzorec (opakovaně použitelné základní)**.
   
    ![Vytvořit vzorec](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na stránce **Vytvořit vzorec** zadejte **název** a **popis** nového vzorce.
   
    ![Vytvořit stránku vzorců](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Chcete-li vzorec vytvořit, vyberte **OK.**

## <a name="modify-a-formula"></a>Úprava vzorce
Chcete-li upravit vzorec, postupujte takto:

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
3. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.  
4. Na stránce testovacího prostředí vyberte **Vzorce (opakovaně použitelné základy).**
   
    ![Nabídka Vzorec](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na stránce **Laboratorní vzorce** vyberte vzorec, který chcete upravit.
6. Na stránce **Aktualizovat vzorec** proveďte požadované úpravy a vyberte **Aktualizovat**.

## <a name="delete-a-formula"></a>Odstranění vzorce
Chcete-li vzorec odstranit, postupujte takto:

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
3. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.  
4. Na stránce **Nastavení** testovacího prostředí vyberte **Vzorce**.
   
    ![Nabídka Vzorec](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na stránce **Laboratorní vzorce** vyberte tři tečky napravo od vzorce, který chcete odstranit.
   
    ![Nabídka Vzorec](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. V kontextové nabídce vzorce vyberte **Odstranit**.
   
    ![Kontextová nabídka Vzorec](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. V dialogovém okně potvrzení odstranění vyberte **Ano.**

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související příspěvky blogu
* [Vlastní obrázky nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Další kroky
Po vytvoření vzorce pro použití při vytváření virtuálního počítače je dalším krokem [přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md).

