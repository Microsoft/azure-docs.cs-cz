---
title: Přidání úložiště Git do testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: Informace o postupu přidání úložiště GitHub nebo Visual Studio Team Services Git pro váš vlastní artefakty zdroj v Azure DevTest Labs.
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
ms.openlocfilehash: 80724a7d8d2b5cec19bdbce27cdafd4a9c09eb47
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787574"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Přidejte úložiště Git pro uložení vlastních artefaktů a šablony Resource Manageru

Můžete [vytvoření vlastních artefaktů](devtest-lab-artifact-author.md) pro virtuální počítače ve vašem testovacím prostředí, nebo [pomocí šablony Azure Resource Manager můžete vytvořit vlastní testovacím prostředí](devtest-lab-create-environment-from-arm.md). Je nutné přidat privátní úložiště Git pro artefakty nebo šablony správce prostředků, které vytvoří tým. Úložiště může být hostitelem [Githubu](https://github.com) nebo na [Visual Studio Team Services](https://visualstudio.com).

Nabízíme [úložiště GitHub artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) , kterou můžete nasadit jako-je, nebo si můžete přizpůsobit pro vaše prostředí. Když se upravit nebo vytvořit artefakt, nemůžete uložit artefaktu veřejného úložiště. Je třeba vytvořit vlastní privátní úložiště pro vlastní artefaktů a artefaktů, které vytvoříte. 

Při vytváření virtuálního počítače můžete uložit šablony Resource Manageru, upravit jej a poté ji později použít k vytvoření více virtuálních počítačů. Je třeba vytvořit vlastní privátní úložiště pro uložení vlastních šablon Resource Manager.  

* Naučte se vytvářet úložiště GitHub, najdete v tématu [Githubu Bootcamp](https://help.github.com/categories/bootcamp/).
* Naučte se vytvořit projekt Team Services, který má úložiště Git, najdete v tématu [připojit k Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Na následujícím obrázku je příklad, jak může vypadat úložiště, který má artefakty v Githubu:  

![Úložiště artefaktů ukázka GitHub](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Získejte informace o úložišti a přihlašovací údaje
Pokud chcete přidat úložiště do testovacího prostředí, nejprve získáte informace o klíči z úložiště. Následující části popisují, jak získat požadované informace pro úložiště, které jsou hostované na Githubu nebo Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Získat adresu URL klonu úložiště GitHub a osobní přístupový token

1. Přejděte na domovské stránce úložiště GitHub obsahující artefaktu nebo definice šablony Resource Manageru.
2. Vyberte **klonovat nebo stáhnout**.
3. Zkopírujte adresu URL do schránky, vyberte **url naklonovaného HTTPS** tlačítko. Uložte adresu URL pro pozdější použití.
4. V pravém horním rohu Githubu, vyberte bitovou kopii profil a pak vyberte **nastavení**.
5. V **osobní nastavení** nabídky na levé straně vyberte **osobní přístupové tokeny**.
6. Vyberte **vygenerovat nový token**.
7. Na **nový osobní přístupový token** v části **Token popis**, zadejte popis. Přijměte výchozí položky v rámci **vyberte obory**a potom vyberte **vygenerovat Token**.
8. Uložte vygenerovaný token. Použití tokenu později.
9. Zavřete Githubu.   
10. Pokračujte [připojit testovacího prostředí do úložiště](#connect-your-lab-to-the-repository) části.

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Získat adresu URL klonu úložiště Visual Studio Team Services a osobní přístupový token

1. Přejděte na domovskou stránku vaší kolekce team (například https://contoso-web-team.visualstudio.com)a potom vyberte projektu.
2. Na domovské stránce projektu vyberte **kód**.
3. Chcete-li zobrazit adresa URL klonování k projektu **kód** vyberte **klon**.
4. Uložte adresu URL. Použijete adresu URL později.
5. Pokud chcete vytvořit osobní přístupový token, v rozevírací nabídce účtu uživatele, vyberte **Můj profil**.
6. Na stránce informace o profilu vyberte **zabezpečení**.
7. Na **zabezpečení** vyberte **přidat**.
8. Na **vytvoření osobního přístupového tokenu** stránky:
   1. Zadejte **popis** pro daný token.
   2. V **vyprší v** seznamu, vyberte **180 dní**.
   3. V **účty** seznamu, vyberte **všechny dostupné účty**.
   4. Vyberte **všechny obory** možnost.
   5. Vyberte **vytvořit Token**.
9. Nový token se zobrazí v **osobní přístupové tokeny** seznamu. Vyberte **kopie tokenu**a potom uložte hodnota tokenu pro pozdější použití.
10. Pokračujte [připojit testovacího prostředí do úložiště](#connect-your-lab-to-the-repository) části.

## <a name="connect-your-lab-to-the-repository"></a>Připojení k úložišti testovacího prostředí
1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **více služeb**a potom vyberte **DevTest Labs** ze seznamu služeb.
3. Ze seznamu labs vyberte testovacího prostředí. 
4. Vyberte **konfiguraci a zásady** > **úložiště** > **+ přidat**.

    ![Tlačítko Přidat úložiště](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na druhé stránce **úložiště** stránky, zadejte následující informace:
  1. **Název**. Zadejte název pro úložiště.
  2. **Adresa Url klonu Git**. Zadejte adresu URL klonu Git HTTPS, který jste zkopírovali dříve z webu GitHub nebo Visual Studio Team Services.
  3. **Větev**. Chcete-li získat definic, zadejte větev.
  4. **Osobní přístupový Token**. Zadejte osobní přístupový token, který jste předtím získali z Githubu nebo Visual Studio Team Services.
  5. **Cesty ke složce zadat**. Zadejte relativní adresa URL klonování, který obsahuje vaše artefaktů nebo definice šablony Resource Manageru alespoň jednu cestu složky. Když zadáte podadresáři, ujistěte se, že obsahuje dopředné lomítko cesta ke složce.

     ![Oblast úložiště](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Vyberte **Uložit**.

### <a name="related-blog-posts"></a>Příspěvky blogu související
* [Řešení potíží s selhání artefakty v DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Připojení virtuálního počítače do existující domény služby Active Directory pomocí šablony Resource Manageru v DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
Po vytvoření privátní úložiště Git, můžete podle potřeby proveďte jednu nebo obě z následujících:
* Úložiště vaše [vlastních artefaktů](devtest-lab-artifact-author.md). Můžete později použít k vytvoření nové virtuální počítače.
* [Vytvoření prostředí více virtuálních počítačů a PaaS prostředky pomocí šablony Resource Manageru](devtest-lab-create-environment-from-arm.md). Pak můžete uložit šablony ve vaší privátní úložiště.

Když vytvoříte virtuální počítač, můžete ověřit, že šablony nebo artefaktů jsou přidány do úložiště Git. Jsou okamžitě k dispozici v seznamu šablon nebo artefaktů. Název vaší privátní úložiště se zobrazí ve sloupci, který určuje zdroj. 
