---
title: Přidání úložiště Git do testovacího prostředí v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak přidat gitové úložiště GitHub u AzureHubu nebo Azure DevOps Services pro vlastní zdroj artefaktů v Azure DevTest Labs.
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
ms.openlocfilehash: 1e7587c60e180fb35e1a2bed735b053b6b0c388a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294608"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Přidání úložiště Git pro ukládání vlastních artefaktů a šablon Správce prostředků

Můžete [vytvořit vlastní artefakty](devtest-lab-artifact-author.md) pro virtuální počítače ve vašem testovacím prostředí nebo [pomocí šablon Azure Resource Manager vytvořit vlastní testovací prostředí](devtest-lab-create-environment-from-arm.md). Je nutné přidat soukromé úložiště Git pro artefakty nebo šablony Správce prostředků, které váš tým vytvoří. Úložiště může být hostované na [GitHubu](https://github.com) nebo ve [službách Azure DevOps](https://visualstudio.com).

Nabízíme [úložiště artefaktů GitHub,](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) které můžete nasadit tak, jak jsou, nebo je můžete přizpůsobit pro vaše testovací prostředí. Při přizpůsobení nebo vytvoření artefaktu nelze uložit artefakt ve veřejném úložišti. Je nutné vytvořit vlastní soukromé úložiště pro vlastní artefakty a artefakty, které vytvoříte. 

Když vytvoříte virtuální hod, můžete šablonu Správce prostředků uložit, přizpůsobit ji, pokud chcete, a později ji použít k vytvoření dalších virtuálních počítače. Chcete-li uložit vlastní šablony Správce prostředků, musíte vytvořit vlastní soukromé úložiště.  

* Informace o tom, jak vytvořit úložiště GitHub, najdete v tématu [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Informace o tom, jak vytvořit projekt Služby Azure DevOps Services, který má úložiště Git, najdete v tématu [Připojení ke službám Azure DevOps](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Následující obrázek je příkladem toho, jak může vypadat úložiště s artefakty na GitHubu:  

![Ukázkové úložiště artefaktů GitHubu](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Získání informací a přihlašovacích údajů k úložišti
Chcete-li přidat úložiště do testovacího prostředí, nejprve získejte klíčové informace z úložiště. Následující části popisují, jak získat požadované informace pro úložiště, které jsou hostované na GitHubu nebo Azure DevOps Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Získání klonované adresy URL úložiště GitHub a osobního přístupového tokenu

1. Přejděte na domovskou stránku úložiště GitHub, která obsahuje definice šablon artefaktů nebo Správce prostředků.
2. Vyberte **Clone or download** (Naklonovat nebo stáhnout).
3. Chcete-li adresu URL zkopírovat do schránky, vyberte tlačítko **URL klonování HTTPS.** Uložte adresu URL pro pozdější použití.
4. V pravém horním rohu GitHubu vyberte profilový obrázek a pak vyberte **Nastavení**.
5. V nabídce **Osobní nastavení** vlevo vyberte **osobní přístupové tokeny**.
6. Vyberte **Generovat nový token**.
7. Na stránce **Nový osobní přístupový token** zadejte v části Popis **tokenu**popis. Přijměte výchozí položky v části **Vybrat obory**a pak vyberte **Generovat token**.
8. Uložte generovaný token. Token použijete později.
9. Zavřete GitHub.   
10. Pokračujte do části [Připojit testovací prostředí k úložišti.](#connect-your-lab-to-the-repository)

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Získání adresy URL klonování azure repos a osobnípřístupový token

1. Přejděte na domovskou stránku kolekce `https://contoso-web-team.visualstudio.com`týmů (například) a vyberte projekt.
2. Na domovské stránce projektu vyberte **Kód**.
3. Chcete-li zobrazit adresu URL klonování, vyberte na stránce **Kód** projektu **možnost Klonovat**.
4. Uložte adresu URL. Adresu URL použijete později.
5. Chcete-li vytvořit osobní přístupový token, vyberte v rozevírací nabídce uživatelského účtu **položku Můj profil**.
6. Na stránce s informacemi o profilu vyberte **možnost Zabezpečení**.
7. Na kartě **Zabezpečení** vyberte **Přidat**.
8. Na stránce **Vytvořit osobní přístupový token:**
   1. Zadejte **popis** tokenu.
   2. V seznamu **Platnost vyprší** vyberte **180 dní**.
   3. V seznamu **Účty** vyberte **Všechny přístupné účty**.
   4. Vyberte možnost **Jen pro čtení.**
   5. Vyberte **Vytvořit token**.
9. Nový token se zobrazí v seznamu **tokenů osobního přístupu.** Vyberte **Kopírovat token**a uložte hodnotu tokenu pro pozdější použití.
10. Pokračujte do části [Připojit testovací prostředí k úložišti.](#connect-your-lab-to-the-repository)

## <a name="connect-your-lab-to-the-repository"></a>Připojení testovacího prostředí k úložišti
1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **Další služby**a ze seznamu služeb vyberte **DevTest Labs.**
3. Ze seznamu testovacích prostředí vyberte testovací prostředí. 
4. Vyberte **Konfigurace a zásady** > **Repozitáře** > **+ Přidat**.

    ![Tlačítko Přidat úložiště](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na druhé stránce **repozitáře** zadejte následující informace:
   1. **Název**. Zadejte název úložiště.
   2. **Adresa URL klonování gitu**. Zadejte adresu URL klonování Git HTTPS, kterou jste dříve zkopírovali z GitHubu nebo služby Azure DevOps Services.
   3. **Pobočka**. Chcete-li získat své definice, zadejte větev.
   4. **Osobní přístupový token**. Zadejte osobní přístupový token, který jste získali dříve z GitHubu nebo Služby Azure DevOps.
   5. **Cesty ke složkám**. Zadejte alespoň jednu cestu ke složce složky, která obsahuje definice šablon artefaktů nebo správce prostředků. Když zadáte podadresář, ujistěte se, že do cesty ke složce zahrnete lomítko.

      ![Oblast repozitářů](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Vyberte **Uložit**.

### <a name="related-blog-posts"></a>Související příspěvky blogu
* [Poradce při potížích s neúspěšnými artefakty v devtest labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Připojení virtuálního počítače k existující doméně služby Active Directory pomocí šablony Správce prostředků v devTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
Po vytvoření soukromého úložiště Git můžete v závislosti na vašich potřebách provést jednu nebo obě následující akce:
* Uložte [vlastní artefakty](devtest-lab-artifact-author.md). Můžete je později použít k vytvoření nových virtuálních virtuálních měn.
* [Vytvořte prostředí s více virtuálními počítačemi a prostředky PaaS pomocí šablon Správce prostředků](devtest-lab-create-environment-from-arm.md). Potom můžete šablony uložit do soukromého úložiště.

Když vytvoříte virtuální počítač, můžete ověřit, že artefakty nebo šablony jsou přidány do úložiště Git. Jsou okamžitě k dispozici v seznamu artefaktů nebo šablon. Název soukromého repo je zobrazen ve sloupci, který určuje zdroj. 
