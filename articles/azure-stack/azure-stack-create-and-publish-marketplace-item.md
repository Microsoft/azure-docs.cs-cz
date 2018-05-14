---
title: Vytvoření a publikování v Azure zásobníku položku Marketplace. | Microsoft Docs
description: Vytvoření a publikování v Azure zásobníku položku Marketplace.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5e0349d6bae9295e7a0ba9f366f84753ebd838c2
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="create-and-publish-a-marketplace-item"></a>Vytvoření a publikování položky Marketplace

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

## <a name="create-a-marketplace-item"></a>Vytvoření položky Marketplace
1. [Stáhněte si](http://www.aka.ms/azurestackmarketplaceitem) nástroj Balíčkovač Galerie Azure a ukázkové položky Azure Marketplace zásobníku.
2. Otevřete položku Marketplace. ukázka a přejmenovat **SimpleVMTemplate** složky. (Používají stejný název jako vaše položku Marketplace – například **Contoso.TodoList**.) Tato složka obsahuje:
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Vytvořit šablonu Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) nebo výběr šablony z Githubu. Položku Marketplace. Tato šablona používá k vytvoření prostředku.
4. Abyste měli jistotu, že je úspěšně nasazena na prostředek, testovat šablony s rozhraními API sady Microsoft Azure zásobníku.
5. Pokud vaše šablona závisí na bitovou kopii virtuálního počítače, postupujte podle pokynů [přidat bitovou kopii virtuálního počítače do zásobníku Azure](azure-stack-add-vm-image.md).
6. Uložte šablonu Azure Resource Manager v **/Contoso.TodoList/DeploymentTemplates/** složky.
7. Zvolte ikony a text pro položku Marketplace. Přidání ikon do **ikony** složku a přidejte text, který má **prostředky** souboru v **řetězce** složky. Použijte zásady vytváření názvů s malým, středním, velké nebo celou pro ikon. V tématu [položku Marketplace reference k uživatelskému rozhraní](#reference-marketplace-item-ui) podrobný popis.
   
   > [!NOTE]
   > Všechny čtyři ikonu velikosti (malé, střední, velký, celou) jsou požadovány pro sestavení položky Marketplace správně.
   > 
   > 
8. V **manifest.json** změňte **název** na název vaší položky Marketplace. Také změnit **vydavatele** název nebo společnosti.
9. V části **artefakty**, změňte **název** a **cesta** do správné informace pro šablonu Azure Resource Manager, který jste zahrnuli.
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. Nahraďte **Moje položky Marketplace** seznam kategorií, kde by se zobrazí vaše položku Marketplace.
    
             "categories":[
                 "My Marketplace Items"
              ],
11. Žádné další úpravy manifest.json, najdete v části [odkaz: manifest.json položky Marketplace](#reference-marketplace-item-manifestjson).
12. Do složky balíčku do souboru .azpkg, otevřete příkazový řádek a spusťte následující příkaz:
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > Úplná cesta k výstupní balíčku musí existovat. Například pokud výstupní cesta C:\MarketPlaceItem\yourpackage.azpkg, složka C:\MarketPlaceItem musí existovat.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Zveřejnění položky Marketplace
1. Pomocí prostředí PowerShell nebo Azure Storage Explorer pro nahrání vaší položku Marketplace (.azpkg) do úložiště objektů Blob Azure. Můžete nahrát do místního úložiště zásobník Azure nebo nahrát do Azure Storage. (Je dočasné umístění balíčku.) Ujistěte se, že objekt blob je veřejně přístupná.
2. Na virtuálním počítači klienta v prostředí Microsoft Azure zásobníku Ujistěte se, zda relace prostředí PowerShell nastavená pomocí svých přihlašovacích údajů správce služby. Najdete pokyny, jak ověřovat prostředí PowerShell v zásobníku Azure v [nasazení šablony v prostředí PowerShell](user/azure-stack-deploy-template-powershell.md).
3. Použití **přidat AzureRMGalleryItem** rutiny prostředí PowerShell pro publikování do zásobníku Azure položku Marketplace. Příklad:
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | Parametr | Popis |
   | --- | --- |
   | ID předplatného |ID správce předplatného. Můžete ji načíst pomocí prostředí PowerShell. Pokud chcete získat na portálu, přejděte na předplatné zprostředkovatele a zkopírujte ID předplatného. |
   | GalleryItemUri |Identifikátor URI objektu BLOB pro váš balíček galerie, která již byla uložena do úložiště. |
   | Apiversion |Nastavit jako **2015-04-01**. |
4. Přejděte na portál. Nyní můžete vidět položku Marketplace. na portálu – jako operátor nebo jako uživatel.
   
   > [!NOTE]
   > Balíček může trvat několik minut, než se objeví.
   > 
   > 
5. Vaše položku Marketplace. nyní byla uložena do Azure Marketplace zásobníku. Můžete jej odstranit z vaší umístění úložiště objektů Blob.
6. Položku Marketplace. můžete odebrat pomocí **odebrat AzureRMGalleryItem** rutiny. Příklad:
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > Rozhraní Marketplace může zobrazovat chybu po odebrání položky. Chcete-li opravit chyby, klikněte na tlačítko **nastavení** na portálu. Pak vyberte **zahodit změny** pod **přizpůsobení portálu**.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>– Referenční informace: Manifest.json položky Marketplace
### <a name="identity-information"></a>Informace o identitě
| Název | Požaduje se | Typ | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Název |X |Řetězec |[A-Za-z0-9]+ | |
| Vydavatel |X |Řetězec |[A-Za-z0-9]+ | |
| Verze |X |Řetězec |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metadata
| Název | Požaduje se | Typ | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Zobrazovaný název |X |Řetězec |Doporučení 80 znaků |Na portálu nemusí řádně zobrazit název položky, pokud je delší než 80 znaků. |
| PublisherDisplayName |X |Řetězec |Doporučení 30 znaků |Na portálu nemusí řádně zobrazit název vydavatele, pokud je delší než 30 znaků. |
| PublisherLegalName |X |Řetězec |Maximálně 256 znaků | |
| Souhrn |X |Řetězec |60 až 100 znaků. | |
| LongSummary |X |Řetězec |140 na 256 znaků |Není zatím k dispozici v zásobníku Azure. |
| Popis |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 až 5 000 znaků. | |

### <a name="images"></a>Image
Na webu Marketplace používá následující ikony:

| Název | Šířka | Výška | Poznámky |
| --- | --- | --- | --- |
| Široké |255 px |115 px |Vždy vyžadován |
| Dlouhodobé používání |115 px |115 px |Vždy vyžadován |
| Střednědobé používání |90 px |90 px |Vždy vyžadován |
| Krátkodobé používání |40 px |40 px |Vždy vyžadován |
| Snímek obrazovky |533 px |32 px |Nepovinné |

### <a name="categories"></a>Kategorie
Každá položka Marketplace by měl označené kategorie, která určuje, kde se položka zobrazí na portálu uživatelského rozhraní. Vyberte jednu z existující kategorie v Azure zásobníku (výpočetní, Data + úložiště, atd.) nebo zvolit nové.

### <a name="links"></a>Odkazy
Každá položka Marketplace může zahrnovat různé odkazy na další obsah. Odkazy jsou zadané jako seznam názvů a identifikátory URI.

| Název | Požaduje se | Typ | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Zobrazovaný název |X |Řetězec |Nesmí být delší než 64 znaků. | |
| URI |X |URI | | |

### <a name="additional-properties"></a>Další vlastnosti
Kromě předchozích metadata může poskytovat autoři Marketplace. data pár vlastní klíč/hodnota v následující podobě:

| Název | Požaduje se | Typ | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Zobrazovaný název |X |Řetězec |Maximální povolenou délku 25 znaků | |
| Hodnota |X |Řetězec |Maximálně 30 znaků | |

### <a name="html-sanitization"></a>Čištění HTML
Pro každé pole, které umožňuje HTML jsou povoleny následující elementy a atributy:

H1, h2, h3, h4, h5, p, ol, ul, li, [cíl | href], Brazílie silné, em, b, i

## <a name="reference-marketplace-item-ui"></a>Referenční dokumentace: Marketplace položky uživatelského rozhraní
Ikony a text položky Marketplace, jak je vidět na portálu Azure zásobníku jsou.

### <a name="create-blade"></a>Okno Vytvořit
![Okno Vytvořit](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Okno Podrobnosti položky Marketplace.
![Okno Podrobnosti položky Marketplace.](media/azure-stack-marketplace-item-ui-reference/image3.png)

