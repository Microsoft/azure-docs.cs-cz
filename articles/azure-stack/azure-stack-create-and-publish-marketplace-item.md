---
title: Vytvoření a publikování položky Marketplace ve službě Azure Stack | Dokumentace Microsoftu
description: Vytvoření a publikování položky Marketplace ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 9e579123124615df83483e244ef11810ca590844
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633959"
---
# <a name="create-and-publish-a-marketplace-item"></a>Vytvoření a publikování položky Marketplace

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

## <a name="create-a-marketplace-item"></a>Vytvoření položky Marketplace
1. [Stáhněte si](http://www.aka.ms/azurestackmarketplaceitem) nástroj Azure Galerie Packageru a ukázkové položky Azure Stack Marketplace.
2. Otevření ukázkové položky Marketplace a přejmenovat **SimpleVMTemplate** složky. (Použijte stejný název jako vaše položky Marketplace – například **Contoso.TodoList**.) Tato složka obsahuje:
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Vytvoření šablony Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) nebo výběr šablony z Githubu. Položka Marketplace používá tuto šablonu k vytvoření prostředku.

    > [!Note]  
    > Nikdy intenzivně kódu všechny tajné kódy, jako jsou kódy product key, heslo nebo žádné identifikovatelné informace o zákaznících v šablony Azure Resource Manageru. Soubory json šablony jsou přístupné bez nutnosti ověřování po publikování v galerii.  Store všech tajných kódů v [služby Key Vault](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-keyvault-parameter) a zavoláme z v rámci šablony.

4. Pokud chcete mít jistotu, že je úspěšně nasazena na prostředek, otestujte šablonu s rozhraními API sady Microsoft Azure Stack.
5. Pokud vaše šablony závisí na image virtuálního počítače, postupujte podle pokynů a [přidat image virtuálního počítače do služby Azure Stack](azure-stack-add-vm-image.md).
6. Uložit šablonu Azure Resource Manageru v **/Contoso.TodoList/DeploymentTemplates/** složky.
7. Zvolte ikon a textu pro položku Marketplace. Přidání ikon **ikony** složky a přidejte text, který má **prostředky** ve **řetězce** složky. Použijte zásady vytváření názvů malá, střední, velká a celý ikon. Zobrazit [položky Marketplace Reference k uživatelskému rozhraní](#reference-marketplace-item-ui) podrobný popis.
   
   > [!NOTE]
   > Všechny čtyři ikony velikosti (malá, střední, velká široký) jsou požadovány pro vytváření položky Marketplace správně.
   > 
   > 
8. V **manifest.json** změňte **název** k názvu položky Marketplace. Také změnit **vydavatele** na název vaší společnosti.
9. V části **artefakty**, změňte **název** a **cesta** do správné informace pro šablony Azure Resource Manageru, který jste zahrnuli.
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. Nahraďte **Moje položky Marketplace** seznam kategorie, ve kterém by se měla zobrazit vaši položku Marketplace.
    
             "categories":[
                 "My Marketplace Items"
              ],
11. Veškeré další úpravy do souboru manifest.json najdete [odkaz: manifest.json položky Marketplace](#reference-marketplace-item-manifestjson).
12. Do složky balíčku do souboru .azpkg, otevřete příkazový řádek a spusťte následující příkaz:
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > Úplná cesta k výstupní balíčku musí existovat. Například pokud výstupní cesta je C:\MarketPlaceItem\yourpackage.azpkg, složka C:\MarketPlaceItem musí existovat.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Zveřejnění položky Marketplace
1. Nahrát položky Marketplace (.azpkg) do úložiště objektů Blob v Azure pomocí Powershellu nebo Průzkumníka služby Azure Storage. Můžete nahrát do místního úložiště služby Azure Stack nebo nahrát do služby Azure Storage. (Jedná o dočasné umístění pro balíček.) Ujistěte se, že objekt blob je veřejně dostupná.
2. Na virtuálním klientovi v prostředí Microsoft Azure Stack Ujistěte se, že relace prostředí PowerShell je nastavené pomocí svých přihlašovacích údajů správce služby. Můžete najít pokyny k ověřování ve službě Azure Stack v Powershellu [nasazení šablony pomocí prostředí PowerShell](user/azure-stack-deploy-template-powershell.md).
3. Při použití [PowerShell 1.3.0]( azure-stack-powershell-install.md) nebo později, můžete použít **přidat AzsGalleryItem** rutiny Powershellu k publikování položky Marketplace do služby Azure Stack. Před použitím prostředí PowerShell 1.3.0, použijte rutinu **přidat AzureRMGalleryitem** místo **přidat AzsGalleryItem**.  Například při použití prostředí PowerShell 1.3.0 nebo novější:
   
       Add-AzsGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | Parametr | Popis |
   | --- | --- |
   | ID předplatného |ID správce předplatného. To můžete načíst pomocí prostředí PowerShell. Pokud chcete získat na portálu, přejděte na předplatné poskytovatele a zkopírujte ID předplatného. |
   | GalleryItemUri |Identifikátor URI objektu BLOB pro balíček galerie, která je již nahraná do služby storage. |
   | Verze rozhraní API |Nastavit jako **2015-04-01**. |
4. Přejděte na portál. Nyní je vidět položku Marketplace. na portálu – jako operátor nebo jako uživatel.
   
   > [!NOTE]
   > Balíček může trvat několik minut.
   > 
   > 
5. Položky Marketplace se nyní uložil do Tržiště Azure Stack. Můžete ho odstranit z umístění úložiště objektů Blob.
    > [!Caution]  
    > Všechny artefakty výchozí galerie a Galerie vlastních artefaktů jsou teď k dispozici bez ověřování v rámci následující adresy URL:  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`  
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`  
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. Můžete odebrat položky Marketplace pomocí **odebrat AzureRMGalleryItem** rutiny. Příklad:
   
        Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > Po odebrání položky se můžou zobrazovat uživatelského rozhraní Marketplace k chybě. Oprava chyby, klikněte na tlačítko **nastavení** na portálu. Vyberte **zahodit úpravy** pod **přizpůsobení portálu**.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>Referenční dokumentace: Manifest.json položky Marketplace
### <a name="identity-information"></a>Informace o identitě
| Název | Požaduje se | Typ | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Název |X |Řetězec |[A-Za-z0-9]+ | |
| Vydavatel |X |Řetězec |[A-Za-z0-9]+ | |
| Verze |X |Řetězec |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metadata
| Název | Požaduje se | Typ | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Zobrazovaný název |X |Řetězec |Doporučení 80 znaků. |Na portálu se nemusí zobrazit název vaší položky řádně Pokud je delší než 80 znaků. |
| PublisherDisplayName |X |Řetězec |Doporučení 30 znaků |Na portálu nemusí řádně zobrazit název vydavatele, pokud je delší než 30 znaků. |
| PublisherLegalName |X |Řetězec |Maximálně 256 znaků. | |
| Souhrn |X |Řetězec |60 až 100 znaků | |
| LongSummary |X |Řetězec |140 až 256 znaků |Není zatím k dispozici ve službě Azure Stack. |
| Popis |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 až 5 000 znaků | |

### <a name="images"></a>Image
Na webu Marketplace používá následující ikony:

| Název | Šířka | Výška | Poznámky |
| --- | --- | --- | --- |
| Široký |255 px |115 px |Vždycky se vyžaduje |
| Dlouhodobé používání |115 px |115 px |Vždycky se vyžaduje |
| Střednědobé používání |90 px |90 px |Vždycky se vyžaduje |
| Krátkodobé používání |40 px |40 px |Vždycky se vyžaduje |
| Snímek obrazovky |533 px |32 px |Nepovinné |

### <a name="categories"></a>Kategorie
Každá položka Marketplace by měl být s klíčovým slovem kategorii, která určuje, kde se položka zobrazí v Uživatelském rozhraní portálu. Můžete zvolit jednu existující kategorie ve službě Azure Stack (výpočetní prostředky, Data + úložiště, atd.) nebo zvolit nové.

### <a name="links"></a>Odkazy
Každá položka Marketplace může obsahovat různé odkazy na další obsah. Odkazy jsou zadané jako seznam názvy a identifikátory URI.

| Název | Požaduje se | Typ | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Zobrazovaný název |X |Řetězec |Maximálně 64 znaků. | |
| URI |X |URI | | |

### <a name="additional-properties"></a>Další vlastnosti
Kromě předchozích metadat můžete Marketplace autoři uvádějí data dvojice vlastní klíč/hodnota v následujícím tvaru:

| Název | Požaduje se | Typ | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Zobrazovaný název |X |Řetězec |Maximálně 25 znaků | |
| Hodnota |X |Řetězec |Maximálně 30 znaků | |

### <a name="html-sanitization"></a>Sanitizace HTML
Pro všechna pole, která umožňuje HTML jsou povoleny následující prvky a atributy:

H1, h2, h3, h4, h5, p, ol, ul, li, [cíl | href], Brazílie, silné, em, b, můžu

## <a name="reference-marketplace-item-ui"></a>Referenční dokumentace: Položky Marketplace uživatelského rozhraní
Ikon a textu pro položky Marketplace, jak je vidět na portálu Azure Stack se.

### <a name="create-blade"></a>Okno Vytvořit
![Okno Vytvořit](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Okno s podrobnostmi o položky Marketplace
![Okno s podrobnostmi o položky Marketplace](media/azure-stack-marketplace-item-ui-reference/image3.png)

