---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/29/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 66ec923f12c59d606056d48c09f1ba346e9a4d32
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827077"
---
## <a name="sign-in-to-azure"></a>Přihlásit se k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

> [!NOTE]
> Pokud jste zaregistrovali pomocí Galerie obrázků Shared ve verzi preview, mohou muset znovu zaregistrovat `Microsoft.Compute` zprostředkovatele. Otevřít [Cloud Shell](https://shell.azure.com/bash) a zadejte: `az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Vytvoření galerie obrázků

Galerie obrázků je primární prostředek, který používá k povolení sdílení imagí. Povolené znaky pro název galerie jsou malá a velká písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky.  Galerie názvy musí být jedinečné v rámci vašeho předplatného. 

Následující příklad vytvoří galerii s názvem *myGallery* v *myGalleryRG* skupinu prostředků.

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
1. Použijte typ **Galerie obrázků Shared** vyhledávacího pole a vyberte **Galerie obrázků Shared** ve výsledcích.
1. V **Galerie obrázků Shared** klikněte na **vytvořit**.
1. Vyberte správné předplatné.
1. V **skupiny prostředků**vyberte **vytvořit nový** a typ *myGalleryRG* pro název.
1. V **název**, typ *myGallery* pro název galerie.
1. Ponechte výchozí nastavení pro **oblasti**.
1. Můžete zadat krátký popis galerii, jako je třeba *Moje galerie imagí pro testování.* a pak klikněte na tlačítko **revize + vytvořit**.
1. Jakmile úspěšně proběhne ověření, vyberte **vytvořit**.
1. Po dokončení nasazení vyberte **přejít k prostředku**.
   
## <a name="create-an-image-definition"></a>Vytvoření definice bitové kopie 

Definice Image vytvořte logické seskupení pro bitové kopie. Používají se ke správě informace o verzích bitové kopie, které jsou vytvořeny v nich. Názvy imagí definice mohou být tvořené malá a velká písmena, číslice, tečky, pomlčky a tečky. Další informace o hodnotách, můžete použít definici image, najdete v části [obrázku definice](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Vytvořte definici image Galerie uvnitř galerie. V tomto příkladu je název image z Galerie *myImageDefinition*.

1. Na stránce pro vaši novou galerii imagí, vyberte **přidejte novou definici image** z horní části stránky. 
1. Pro **název definice Image**, typ *myImageDefinition*.
1. Pro **operačního systému**, vyberte správné možnost v závislosti na zdrojové image.
1. Pro **vydavatele**, typ *myPublisher*. 
1. Pro **nabízejí**, typ *myOffer*.
1. Pro **SKU**, typ *mySKU*.
1. Až budete hotovi, vyberte **revize + vytvořit**.
1. Když k definici image úspěšně proběhne ověření, vyberte **vytvořit**.
1. Po dokončení nasazení vyberte **přejít k prostředku**.



## <a name="create-an-image-version"></a>Vytvoření image verze

Vytvoření image verze ze spravované image. V tomto příkladu je verze image *1.0.0* a se replikuje do obou *střed USA – západ* a *střed USA – jih* datových centrech. Při výběru cílových oblastí pro replikaci, mějte na paměti, že je také nutné zahrnout *zdroj* oblasti jako cíl pro replikaci.

Povolené znaky pro verze image jsou čísla a tečky. Čísla musí být v rozsahu 32bitového celého čísla. Formát: *Hlavní verze*. *Podverze*. *Oprava*.

1. Na stránce vaší definice bitové kopie, vyberte **verzi přidat** z horní části stránky.
1. V **oblasti**, vyberte oblast, kde spravované image uložená. Verze bitové kopie je potřeba vytvořit ve stejné oblasti jako spravované image jsou vytvořeny z.
1. Pro **název**, typ *1.0.0*. Postupujte podle názvu verze image *hlavní*. *vedlejší*. *oprava* naformátovat pomocí celých čísel. 
1. V **zdrojového obrázku**, vyberte zdroj spravované image z rozevíracího seznamu.
1. V **vyloučit z nejnovější**, ponechte výchozí hodnotu *ne*.
1. Pro **života datum konce**, vyberte datum z kalendáře, která je pár měsíců v budoucnu.
1. V **replikace**, nechat **výchozí počet replik** jako 1. Je potřeba replikovat do zdrojové oblasti, takže políčko nechte první repliku jako výchozí nastavení a potom si vyberte druhé oblasti repliky bude *USA – východ*.
1. Jakmile budete hotovi, vyberte **revize + vytvořit**. Azure ověří konfiguraci.
1. Když se verze image úspěšně proběhne ověření, vyberte **vytvořit**.
1. Po dokončení nasazení vyberte **přejít k prostředku**.

Může trvat nějakou image replikovat na všechny cílové oblasti.
