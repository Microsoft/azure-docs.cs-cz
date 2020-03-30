---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 729e757c69887bbdce324e2d8383c970995dc94a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903672"
---
## <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

> [!NOTE]
> Pokud jste se zaregistrovali k používání sdílených galerií obrázků během `Microsoft.Compute` náhledu, možná budete muset znovu zaregistrovat zprostředkovatele. Otevřete [cloudové prostředí](https://shell.azure.com/bash) a zadejte:`az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Vytvoření galerie obrázků

Galerie obrázků je primární prostředek používaný pro povolení sdílení obrázků. Povolené znaky pro název galerie jsou velká nebo malá písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky.  Názvy galerií musí být v rámci předplatného jedinečné. 

Následující příklad vytvoří galerii s názvem *myGallery* ve skupině prostředků *myGalleryRG.*

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
1. Použijte typ **Galerie sdílených obrázků** ve vyhledávacím poli a ve výsledcích vyberte **Sdílená galerie obrázků.**
1. Na stránce **Sdílená galerie obrázků** klikněte na **Vytvořit**.
1. Vyberte správné předplatné.
1. Ve **skupině prostředků**vyberte vytvořit **nový** a zadejte *název myGalleryRG.*
1. Do **pole Název**napište *myGallery* pro název galerie.
1. Ponechte výchozí hodnotu pro **region**.
1. Můžete zadat krátký popis galerie, například *Moje galerie obrázků pro testování.* a potom klepněte na tlačítko **Revize + vytvořit**.
1. Po průchodu ověření vyberte **Vytvořit**.
1. Po dokončení nasazení vyberte **Přejít na prostředek**.
   
## <a name="create-an-image-definition"></a>Vytvoření definice obrázku 

Definice obrázků vytvářejí logické seskupení pro obrazy. Používají se ke správě informací o verzích bitových obrázků, které jsou v nich vytvořeny. Názvy definic obrázků mohou být tvořeny velkými nebo velkými písmeny, číslicemi, tečkami, pomlčkami a tečkami. Další informace o hodnotách, které můžete zadat pro definici obrazu, naleznete v [tématu Definice obrázků](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Vytvořte definici obrázku galerie uvnitř galerie. V tomto příkladu je obrázek galerie pojmenován *myImageDefinition*.

1. Na stránce nové galerie obrázků vyberte **Přidat novou definici obrázku** z horní části stránky. 
1. Do **pole Název definice obrázku**zadejte příkaz *myImageDefinition*.
1. Pro **operační systém**vyberte správnou možnost na základě zdrojového virtuálního počítače.
1. Pro **generování virtuálního počítače**vyberte možnost na základě zdrojového virtuálního počítače. Ve většině případů to bude *Gen 1*. Další informace najdete [v tématu Podpora pro generování 2 virtuálních her](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2).
1. V **části Stav operačního systému**vyberte možnost na základě zdrojového virtuálního počítače. Další informace naleznete v tématu [Generalized and specialized](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images).
1. Do **pole Publisher**zadejte příkaz *myPublisher*. 
1. Do **pole Nabídka**napište příkaz *myOffer*.
1. Do **pole Skladová položka**zadejte příkaz *mySKU*.
1. Po dokončení vyberte **Zkontrolovat + vytvořit**.
1. Po uplynutí platnosti definice obrázku vyberte **vytvořit**.
1. Po dokončení nasazení vyberte **Přejít na prostředek**.


## <a name="create-an-image-version"></a>Vytvoření verze obrázku

Vytvořte verzi bitové kopie ze spravované bitové kopie. V tomto příkladu je verze bitové kopie *1.0.0* a je replikována do datových center *– střed USA* – západ a střed *usa.* Při výběru cílových oblastí pro replikaci nezapomeňte, že je také třeba zahrnout *zdrojovou* oblast jako cíl pro replikaci.

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32bitové celé číslo. Formát: *MajorVersion*. *MinorVersion*. *Záplata*.

Kroky pro vytvoření verze image se mírně liší v závislosti na tom, zda je zdroj generalizovaný obraz nebo snímek specializovaného virtuálního počítače. 

### <a name="option-generalized"></a>Možnost: Generalizované

1. Na stránce definice obrázku vyberte **Přidat verzi** v horní části stránky.
1. V **oblasti**vyberte oblast, ve které je uložená spravovaná bitová kopie. Verze bitových obrázků je třeba vytvořit ve stejné oblasti jako spravované image, ze které jsou vytvořeny.
1. Do **pole Název**zadejte *1.0.0*. Název verze obrázku by měl následovat *hlavní*. *nezletilá*. *formát opravy* pomocí celá čísla. 
1. Ve **zdrojovém obrázku**vyberte zdrojový spravovaný obrázek z rozevíracího souboru.
1. V **části Vyloučit z nejnovějšíponechte**výchozí hodnotu *Ne*.
1. V **části Datum ukončení životnosti**vyberte datum z kalendáře, které je v budoucnu několik měsíců.
1. V **replikaci**ponechejte **výchozí počet replik** jako 1. Je třeba replikovat do zdrojové oblasti, proto ponechte první repliku jako výchozí a pak vyberte druhou oblast repliky, která bude *východní USA*.
1. Až budete hotovi, vyberte **Zkontrolovat + vytvořit**. Azure ověří konfiguraci.
1. Když verze obrázku projde ověřením, vyberte **Vytvořit**.
1. Po dokončení nasazení vyberte **Přejít na prostředek**.

Replikace bitové kopie do všech cílových oblastí může chvíli trvat.

### <a name="option-specialized"></a>Možnost: Specializovaná

1. Na stránce definice obrázku vyberte **Přidat verzi** v horní části stránky.
1. V **oblasti**vyberte oblast, kde je uložen snímek. Verze bitové kopie je třeba vytvořit ve stejné oblasti jako zdroj, ze kterých jsou vytvořeny.
1. Do **pole Název**zadejte *1.0.0*. Název verze obrázku by měl následovat *hlavní*. *nezletilá*. *formát opravy* pomocí celá čísla. 
1. Ve **snímku disku operačního systému**vyberte snímek ze zdrojového virtuálního počítače z rozevíracího přehledu. Pokud váš zdrojový virtuální počítač měl datový disk, který chcete zahrnout, vyberte správné číslo **logické jednotky** z rozevíracího pole a pak vyberte snímek datového disku pro **snímek datového disku**. 
1. V **části Vyloučit z nejnovějšíponechte**výchozí hodnotu *Ne*.
1. V **části Datum ukončení životnosti**vyberte datum z kalendáře, které je v budoucnu několik měsíců.
1. V **replikaci**ponechejte **výchozí počet replik** jako 1. Je třeba replikovat do zdrojové oblasti, proto ponechte první repliku jako výchozí a pak vyberte druhou oblast repliky, která bude *východní USA*.
1. Až budete hotovi, vyberte **Zkontrolovat + vytvořit**. Azure ověří konfiguraci.
1. Když verze obrázku projde ověřením, vyberte **Vytvořit**.
1. Po dokončení nasazení vyberte **Přejít na prostředek**.

## <a name="share-the-gallery"></a>Sdílet galerii

Doporučujeme sdílet přístup na úrovni galerie obrázků. Následující vás provede sdílením galerie, kterou jste právě vytvořili.

1. Otevřete [portál Azure](https://portal.azure.com).
1. V nabídce vlevo vyberte **položku Skupiny zdrojů**. 
1. V seznamu skupin prostředků vyberte **myGalleryRG**. Otevře se okno pro skupinu prostředků.
1. V nabídce na levé straně stránky **myGalleryRG** vyberte **ovládací prvek přístupu (IAM)**. 
1. V části **Přidat přiřazení role**vyberte **Přidat**. Otevře se podokno **Přidat přiřazení role.** 
1. V části **Role**vyberte **Reader**.
1. V části **Přiřazení přístupu**ponechejte výchozí **nastavení uživatele, skupiny nebo instančního objektu Azure AD**.
1. V **části Vybrat**zadejte e-mailovou adresu osoby, kterou chcete pozvat.
1. Pokud je uživatel mimo vaši organizaci, zobrazí se zpráva **Tento uživatel bude odeslán e-mail, který mu umožní spolupracovat se společností Microsoft.** Vyberte uživatele s e-mailovou adresou a klepněte na tlačítko **Uložit**.

Pokud je uživatel mimo vaši organizaci, obdrží e-mailovou pozvánku, aby se k organizaci připojil. Uživatel musí přijmout pozvánku, pak budou moci vidět galerii a všechny definice obrázků a verze v jejich seznamu zdrojů.

