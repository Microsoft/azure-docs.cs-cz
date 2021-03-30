---
title: Přidání úložiště Git do testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: Naučte se, jak do Azure DevTest Labs přidat úložiště GitHubu nebo Azure DevOps Services úložiště Git pro vlastní zdroj artefaktů.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 225aae5e0a017b711f29a47829b06f8bb7a6a8ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483138"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Přidání úložiště Git pro uložení vlastních artefaktů a šablon Správce prostředků

Můžete [vytvořit vlastní artefakty](devtest-lab-artifact-author.md) pro virtuální počítače v testovacím prostředí, nebo můžete [použít šablony Azure Resource Manager k vytvoření vlastního testovacího prostředí](devtest-lab-create-environment-from-arm.md). Je nutné přidat privátní úložiště Git pro artefakty nebo šablony Správce prostředků, které váš tým vytvoří. Úložiště je možné hostovat na [GitHubu](https://github.com) nebo na [Azure DevOps Services](https://visualstudio.com).

Nabízíme [úložiště GitHub artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) , které můžete nasadit tak, jak jsou, nebo si je můžete přizpůsobit pro vaše cvičení. Při přizpůsobování nebo vytváření artefaktu nemůžete uložit artefakt ve veřejném úložišti. Musíte vytvořit vlastní privátní úložiště pro vlastní artefakty a artefakty, které vytvoříte. 

Když vytváříte virtuální počítač, můžete šablonu Správce prostředků uložit, přizpůsobit ji, pokud chcete, a pak ji později použít k vytvoření dalších virtuálních počítačů. Abyste mohli ukládat vlastní šablony Správce prostředků, musíte si vytvořit vlastní privátní úložiště.  

* Informace o tom, jak vytvořit úložiště GitHubu, najdete v článku [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Informace o tom, jak vytvořit projekt Azure DevOps Services s úložištěm Git, najdete v tématu [připojení k Azure DevOps Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Následující obrázek je příkladem toho, jak by úložiště, které má artefakty, vypadalo na GitHubu:  

![Ukázka úložiště artefaktů GitHubu](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Získat informace o úložišti a přihlašovací údaje
Pokud chcete do testovacího prostředí přidat úložiště, nejdřív Získejte klíčové informace z vašeho úložiště. Následující části popisují, jak získat požadované informace pro úložiště, která jsou hostovaná na GitHubu nebo Azure DevOps Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Získání adresy URL pro klonování úložiště GitHub a tokenu osobního přístupu

1. Přejít na domovskou stránku úložiště GitHub, které obsahuje definice artefaktů nebo Správce prostředků šablon.
2. Vyberte **Clone or download** (Naklonovat nebo stáhnout).
3. Chcete-li zkopírovat adresu URL do schránky, vyberte tlačítko **Adresa URL klonování https** . Uložte adresu URL pro pozdější použití.
4. V pravém horním rohu GitHubu vyberte profilový obrázek a pak vyberte **Nastavení**.
5. V nabídce **osobní nastavení** na levé straně vyberte **tokeny osobních přístupů**.
6. Vyberte možnost **generovat nový token**.
7. Na stránce **Nový token osobního přístupu** zadejte v části **Popis tokenu** popis. Přijměte výchozí položky v části **Vybrat obory** a pak vyberte **vygenerovat token**.
8. Uložte vygenerovaný token. Token použijete později.
9. Zavřete GitHub.   
10. Přejděte k části [připojení laboratoře k úložišti](#connect-your-lab-to-the-repository) .

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Získat adresu URL pro klonování Azure Repos a osobní přístupový token

1. Přejít na domovskou stránku kolekce týmu (například `https://contoso-web-team.visualstudio.com` ) a pak vyberte svůj projekt.
2. Na domovské stránce projektu vyberte možnost **kód**.
3. Chcete-li zobrazit adresu URL klonování, vyberte na stránce **kód** projektu možnost **klonovat**.
4. Uložte adresu URL. Adresu URL použijete později.
5. Pokud chcete vytvořit osobní přístupový token, v rozevírací nabídce uživatelský účet vyberte **můj profil**.
6. Na stránce informace o profilu vyberte **zabezpečení**.
7. Na kartě **zabezpečení** vyberte **Přidat**.
8. Na stránce **Vytvoření osobního přístupového tokenu** :
   1. Zadejte **Popis** tokenu.
   2. V seznamu **vyprší platnost** vyberte **180 dnů**.
   3. V seznamu **účty** vyberte **všechny účty k dispozici**.
   4. Vyberte možnost jen **pro čtení** .
   5. Vyberte **vytvořit token**.
9. Nový token se zobrazí v seznamu **tokenů osobního přístupu** . Vyberte možnost **Kopírovat token** a pak hodnotu token uložte pro pozdější použití.
10. Přejděte k části [připojení laboratoře k úložišti](#connect-your-lab-to-the-repository) .

## <a name="connect-your-lab-to-the-repository"></a>Připojení testovacího prostředí k úložišti
1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **Další služby** a v seznamu služeb vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte testovací prostředí. 
4. Vyberte **Konfigurace a**  >  **úložiště** zásad  >  **+ Přidat**.

    ![Tlačítko Přidat úložiště](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na stránce druhá **úložiště** zadejte následující informace:
   1. **Název:** Zadejte název úložiště.
   2. **Adresa Url git clone** Zadejte adresu URL klonu Git HTTPS, kterou jste zkopírovali dříve z GitHubu nebo z Azure DevOps Services.
   3. **Větev**. Pokud chcete získat definice, zadejte větev.
   4. **Osobní přístupový token** Zadejte osobní přístupový token, který jste už dříve získali z GitHubu nebo Azure DevOps Services.
   5. **Cesty ke složkám**. Zadejte alespoň jednu cestu ke složce vzhledem k adrese URL klonování, která obsahuje definice artefaktů nebo Správce prostředků šablon. Když zadáte podadresář, nezapomeňte v cestě ke složce zadat lomítko.

      ![Oblast úložišť](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Vyberte **Uložit**.

### <a name="related-blog-posts"></a>Související blogové příspěvky
* [Řešení potíží s neúspěšnými artefakty v DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Připojení virtuálního počítače k existující doméně služby Active Directory pomocí Správce prostředků šablony v DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
Po vytvoření privátního úložiště Git můžete v závislosti na vašich potřebách provést jednu nebo obě následující akce:
* Uložte [vlastní artefakty](devtest-lab-artifact-author.md). Později je můžete použít k vytvoření nových virtuálních počítačů.
* [Vytvářejte prostředí s více virtuálními počítači a PaaS prostředky pomocí šablon Správce prostředků](devtest-lab-create-environment-from-arm.md). Pak můžete šablony Uložit do soukromého úložiště.

Když vytváříte virtuální počítač, můžete ověřit, že se artefakty nebo šablony přidaly do úložiště Git. Jsou okamžitě k dispozici v seznamu artefaktů nebo šablon. Název soukromého úložiště se zobrazí ve sloupci, který určuje zdroj. 
