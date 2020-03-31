---
title: Vytvoření Galerie sdílených bitových obrázků Azure pomocí portálu
description: Přečtěte si, jak pomocí portálu Azure vytvářet a sdílet image virtuálních strojů.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 6273b58d9db53cfc4f6647885c70148982f0b950
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975495"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Vytvoření Galerie sdílených bitových obrázků Azure pomocí portálu

[Galerie sdílených obrázků](shared-image-galleries.md) zjednodušuje vlastní sdílení obrázků v celé organizaci. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image lze použít k zavádění úloh nasazení, jako je předběžné načtení aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených obrázků umožňuje sdílet vlastní image virtuálních počítačů s ostatními uživateli ve vaší organizaci, v rámci nebo napříč oblastmi, v rámci klienta AAD. Zvolte, které obrázky chcete sdílet, ve kterých oblastech je chcete zpřístupnit a s kým je chcete sdílet. Můžete vytvořit více galerií, abyste mohli logicky seskupit sdílené obrázky. 

Galerie je prostředek nejvyšší úrovně, který poskytuje úplné řízení přístupu na základě rolí (RBAC). Image může být verzí a můžete se rozhodnout replikovat každou verzi bitové kopie do jiné sady oblastí Azure. Galerie pracuje pouze se spravovanými obrázky.

Funkce Galerie sdílených obrázků má více typů prostředků. Budeme používat nebo stavební tyto v tomto článku:

| Prostředek | Popis|
|----------|------------|
| **Spravovaná bitová kopie** | Základní obrázek, který lze použít samostatně nebo použít k vytvoření **verze obrázku** v galerii obrázků. Spravované bitové kopie se vytvářejí z [generalizovaných](shared-image-galleries.md#generalized-and-specialized-images) virtuálních měn. Spravovaná bitová kopie je speciální typ virtuálního pevného disku, který lze použít k vytvoření více virtuálních počítačů a nyní lze použít k vytvoření verzí sdílených bitových obrázků. |
| **Snímek** | Kopie virtuálního pevného disku, který lze použít k vytvoření **verze bitové kopie**. Snímky lze pořizovat ze [specializovaného](shared-image-galleries.md#generalized-and-specialized-images) virtuálního počítače (ten, který nebyl zobecněn) a pak použit samostatně nebo se snímky datových disků, k vytvoření verze specializované bitové kopie.
| **Galerie obrázků** | Stejně jako Azure Marketplace, **galerie obrázků** je úložiště pro správu a sdílení bitových kopií, ale máte řízení, kdo má přístup. |
| **Definice obrázku** | Obrázky jsou definovány v galerii a nesou informace o obrázku a požadavky na jeho použití v rámci organizace. Můžete zahrnout informace, jako je zobecnění nebo specializované, operační systém, minimální a maximální požadavky na paměť a poznámky k verzi. Jedná se o definici typu obrazu. |
| **Verze obrázku** | **Verze bitové kopie** je to, co používáte k vytvoření virtuálního počítačů při použití galerie. Podle potřeby pro vaše prostředí můžete mít více verzí bitové kopie. Stejně jako spravovaná **image version** bitová kopie, když k vytvoření virtuálního počítače použijete verzi image k vytvoření nových disků pro virtuální počítače. Verze obrázků lze použít vícekrát. |

<br>


> [!IMPORTANT]
> Specializované obrázky jsou v současné době ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Známá omezení náhledu** Virtuální aplikace lze vytvořit pouze z specializovaných bitových kopií pomocí portálu nebo rozhraní API. Není žádná podpora cli nebo PowerShell pro náhled.

## <a name="before-you-begin"></a>Než začnete

Chcete-li dokončit příklad v tomto článku, musíte mít existující spravované image generalizovaného virtuálního počítače nebo snímek specializovaného virtuálního počítače. Můžete postupovat [podle kurzu: Vytvořte vlastní image virtuálního počítače Azure s Azure PowerShell](tutorial-custom-images.md) vytvořit spravovanou image nebo [vytvořit snímek](snapshot-copy-managed-disk.md) pro specializovaný virtuální počítač. Pro spravované bitové kopie a snímky velikost datového disku nesmí být větší než 1 TB.

Při práci v tomto článku nahraďte názvy skupin prostředků a virtuálních počítačů, kde je to potřeba.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Vytvoření virtuálních počítačů

Teď můžete vytvořit jeden nebo více nových virtuálních ms. Tento příklad vytvoří virtuální hod s názvem *myVM*, v *myResourceGroup*, v *usa východní* datového centra.

1. Přejděte na definici obrázku. Filtr zdrojů můžete použít k zobrazení všech dostupných definic obrázků.
1. Na stránce definice obrázku vyberte **Vytvořit virtuální počítač** z nabídky v horní části stránky.
1. Ve **skupině Resource vyberte** **možnost Vytvořit nový** a zadejte název *myResourceGroup.*
1. Do **pole Název virtuálního počítače**zadejte *myVM*.
1. V **popřípadě Oblast**vyberte *možnost Východní USA*.
1. V **případě možností dostupnosti**ponechejte výchozí hodnotu *Není vyžadována žádná redundance infrastruktury*.
1. Hodnota pro **obrázek** je `latest` automaticky vyplněna verzí obrázku, pokud jste začali ze stránky pro definici obrazu.
1. V **části Velikost**zvolte velikost virtuálního počítače ze seznamu dostupných velikostí a pak zvolte **Vybrat**.
1. V části **Účet správce**, pokud byla bitová kopie zobecněna, je třeba zadat uživatelské jméno, jako je *azureuser* a heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm). Pokud byla vaše image specializovaná, pole uživatelského jména a hesla se zašednou, protože se používá uživatelské jméno a heslo pro zdrojový virtuální počítač.
1. Pokud chcete povolit vzdálený přístup k virtuálnímu virtuálnímu serveru, v části **Veřejné příchozí porty**zvolte **Povolit vybrané porty** a pak z rozevíracího souboru vyberte **protokol RDP (3389).** Pokud nechcete povolit vzdálený přístup k virtuálnímu virtuálnímu serveru, ponechejte **možnost Žádný** pro veřejné **příchozí porty**.
1. Po dokončení vyberte tlačítko **Revize + vytvořit** v dolní části stránky.
1. Po virtuálním počítače projde ověření, vyberte **vytvořit** v dolní části stránky pro spuštění nasazení.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro příslušný virtuální počítač, vyberete **Odstranit** a pak potvrdíte název skupiny prostředků, kterou chcete odstranit.

Chcete-li odstranit jednotlivé prostředky, je třeba je odstranit v opačném pořadí. Chcete-li například odstranit definici obrázku, musíte odstranit všechny verze obrazu vytvořené z tohoto obrázku.

## <a name="next-steps"></a>Další kroky

Prostředek Galerie sdílených obrázků můžete také vytvořit pomocí šablon. K dispozici je několik šablon Azure QuickStart: 

- [Vytvoření galerie sdílených obrázků](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice obrazu ve sdílené galerii obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze obrázku ve sdílené galerii obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního virtuálního virtuálního mísy z verze bitové kopie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Další informace o sdílených galeriích obrázků naleznete v [tématu Přehled](shared-image-galleries.md). Pokud narazíte na problémy, [přečtěte si článek Poradce při potížích s galeriemi sdílených obrázků](troubleshooting-shared-images.md).

