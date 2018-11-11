---
title: Přidání úložiště Git do testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak přidat úložiště Githubu nebo Azure DevOps služby Git pro váš zdroj vlastních artefaktů ve službě Azure DevTest Labs.
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: c7b9921d7eacb9b40e39f8e68d13357ce6bcfd78
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241544"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Přidání úložiště Git pro ukládání vlastních artefaktů a šablony Resource Manageru

Je možné [při vytváření vlastních artefaktů](devtest-lab-artifact-author.md) pro virtuální počítače ve vaší laboratoři nebo [pomocí šablon Azure Resource Manageru k vytvoření vlastního testovacího prostředí](devtest-lab-create-environment-from-arm.md). Privátní úložiště Git pro artefakty nebo šablony Resource Manageru, které váš tým vytváří, je nutné přidat. Úložiště je možné hostovat na [Githubu](https://github.com) nebo na [Azure DevOps služby](https://visualstudio.com).

Nabízíme [úložiště GitHub artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) , kterou můžete nasadit jako-je, nebo si můžete přizpůsobit pro vaše testovací prostředí. Když upravit nebo vytvořit artefakt, nejde uložit artefaktů ve veřejném úložišti. Je nutné vytvořit vaše vlastní privátní úložiště vlastních artefaktů a artefakty, které vytvoříte. 

Při vytváření virtuálního počítače, můžete šablonu Resource Manageru uložit, upravit jej a pak ho později použít k vytvoření více virtuálních počítačů. Je třeba vytvořit vlastní privátní úložiště pro ukládání své vlastní šablony Resource Manageru.  

* Zjistěte, jak vytvořit úložiště GitHub, najdete v článku [Githubu Bootcamp](https://help.github.com/categories/bootcamp/).
* Zjistěte, jak vytvořit projekt Azure DevOps služby, který má úložiště Git, naleznete v tématu [připojit ke službám Azure DevOps](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Na následujícím obrázku je příklad toho, jak může vypadat úložiště, který má artefaktů v Githubu:  

![Ukázkové artefakty úložiště GitHub](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Získejte informace o úložišti a přihlašovací údaje
Přidání úložiště do vašeho testovacího prostředí, nejprve získejte informace o klíči z úložiště. Následující části popisují, jak získat požadované informace pro úložiště, které jsou hostované na Githubu nebo služby Azure DevOps.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Získat adresu URL klonování úložiště Githubu a osobní přístupový token

1. Přejděte na domovskou stránku úložiště GitHub obsahující artefakt nebo definice šablon Resource Manageru.
2. Vyberte **Clone or download** (Naklonovat nebo stáhnout).
3. Zkopírujte adresu URL do schránky, vyberte **adresa url HTTPS clone** tlačítko. Uložte adresu URL pro pozdější použití.
4. V pravém horním rohu GitHub, vyberte profilový obrázek a pak vyberte **nastavení**.
5. V **osobní nastavení** nabídky na levé straně vyberte **osobní přístupové tokeny**.
6. Vyberte **vygenerovat nový token**.
7. Na **nový token pat** stránce v části **Token popis**, zadejte popis. Přijměte výchozí položky v rámci **vyberte obory**a pak vyberte **vygenerovat Token vygenerujte**.
8. Uložte vygenerovaný token. Tento token použijete později.
9. Zavřete Githubu.   
10. Dál [připojit se k úložišti testovacího prostředí](#connect-your-lab-to-the-repository) oddílu.

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Klonování úložiště Azure získat adresu URL a osobní přístupový token

1. Přejděte na domovskou stránku týmu kolekce (například https://contoso-web-team.visualstudio.com)a pak vyberte svůj projekt.
2. Na domovské stránce projektu, vyberte **kód**.
3. Chcete-li zobrazit adresu URL klonu na projekt **kód** stránce **klonování**.
4. Adresu URL si uložte. Adresa URL použijete později.
5. Chcete-li vytvořit osobní přístupový token, v rozevírací nabídce účtu uživatele, vyberte **Můj profil**.
6. Na stránce informace o profilu, vyberte **zabezpečení**.
7. Na **zabezpečení** kartu, vyberte možnost **přidat**.
8. Na **vytvořit osobní přístupový token** stránky:
   1. Zadejte **popis** pro daný token.
   2. V **vyprší v** seznamu vyberte **180 dnů**.
   3. V **účty** seznamu vyberte **všechny přístupné účty**.
   4. Vyberte **všechny obory** možnost.
   5. Vyberte **vytvořit Token**.
9. Nový token se zobrazí v **osobní přístupové tokeny** seznamu. Vyberte **kopírování Token**a potom uložte hodnota tokenu pro pozdější použití.
10. Dál [připojit se k úložišti testovacího prostředí](#connect-your-lab-to-the-repository) oddílu.

## <a name="connect-your-lab-to-the-repository"></a>Připojit se k úložišti testovacího prostředí
1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **další služby**a pak vyberte **DevTest Labs** ze seznamu služeb.
3. V seznamu testovacích prostředí vyberte vaše testovací prostředí. 
4. Vyberte **konfigurace a zásad** > **úložišť** > **+ přidat**.

    ![Tlačítko Přidat úložiště](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. V druhém **úložišť** stránky, zadejte následující informace:
  1. **Název**. Zadejte název úložiště.
  2. **Adresa Url klonu Git**. Zadejte adresu URL klonu Git HTTPS, který jste si zkopírovali z Githubu nebo služby Azure DevOps.
  3. **Větev**. Chcete-li získat vaše definice, zadejte větev.
  4. **Token pat**. Zadejte osobní přístupový token, který jste předtím získali z Githubu nebo služby Azure DevOps.
  5. **Cesty ke složkám**. Zadejte alespoň jedna cesta ke složce relativní adresa URL klonu, který obsahuje vaše artefakt nebo definice šablon Resource Manageru. Při zadávání podadresář, nezapomeňte že zahrnout lomítko cesta ke složce.

     ![Oblast úložiště](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Vyberte **Uložit**.

### <a name="related-blog-posts"></a>Související blogové příspěvky
* [Řešení potíží s selhání artefaktů ve službě DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Připojte se k virtuální počítač do existující domény služby Active Directory pomocí šablony Resource Manageru ve službě DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
Jakmile vytvoříte privátní úložiště Git máte jeden nebo oba z těchto možností podle svých potřeb:
* Store vaše [vlastních artefaktů](devtest-lab-artifact-author.md). Můžete později použít k vytvoření nové virtuální počítače.
* [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Resource Manageru](devtest-lab-create-environment-from-arm.md). Potom můžete ukládat šablony ve vašem privátním úložišti.

Při vytváření virtuálního počítače můžete ověřit, že se artefakty nebo šablony jsou přidány do úložiště Git. Jsou okamžitě dostupné v seznamu artefakty nebo šablon. Název vašeho privátního úložiště zobrazuje ve sloupci, který určuje zdroj. 
