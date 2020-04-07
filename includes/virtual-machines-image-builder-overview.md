---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 658910dc4291375c7b2ab22e88c599b970b885af
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80419077"
---
Image standardizovaných virtuálních strojů umožňují organizacím migrovat do cloudu a zajistit konzistenci v nasazeních. Obrázky obvykle obsahují předdefinované nastavení zabezpečení a konfigurace a potřebný software. Nastavení vlastního kanálu pro vytváření bitových kopií vyžaduje čas, infrastrukturu a nastavení, ale s Azure VM Image Builder, stačí poskytnout jednoduchou konfiguraci popisující vaši bitovou kopii, odeslat do služby a image je sestavena a distribuována.
 
Azure VM Image Builder (Azure Image Builder) umožňuje začít s windows nebo Linux-založené Image Azure Marketplace, existující vlastní image nebo Red Hat Enterprise Linux (RHEL) ISO a začít přidávat vlastní vlastní přizpůsobení. Vzhledem k tomu, že tvůrce obrázků je postaven na [hashicorp packer](https://packer.io/), můžete také importovat stávající skripty packer shell provisioner. Můžete také určit, kde chcete, aby vaše image hostované, v [Galerii sdílených bitových kopií Azure](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), jako spravovaná bitová kopie nebo VHD.

> [!IMPORTANT]
> Azure Image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funkce Preview

V náhledu jsou podporovány tyto funkce:

- Vytváření zlatých základních obrázků, které zahrnují minimální zabezpečení a podnikové konfigurace a umožňují oddělením dále přizpůsobit je jejich potřebám.
- Oprava stávajících obrázků, Image Builder vám umožní neustále patch stávající vlastní obrázky.
- Připojte tvůrce obrázků ke stávajícím virtuálním sítím, abyste se mohli připojit ke stávajícím konfiguračním serverům (DSC, Chef, Puppet atd.), sdíleným složek souborů nebo jiným směrovatelným serverům/službám.
- Integrace s Galerií sdílených bitových kopií Azure umožňuje distribuovat, verzí a škálovat bitové kopie globálně a poskytuje systém pro správu bitových kopií.
- Integrace s existujícími kanály sestavení image, stačí zavolat Image Builder z vašeho kanálu, nebo použít jednoduchý náhled image Builder Azure DevOps úlohy.
- Migrujte existující kanál přizpůsobení image do Azure. Pomocí existujících skriptů, příkazů a procesů můžete přizpůsobit obrázky.
- Tvorba obrázků ve formátu VHD.
 

## <a name="regions"></a>Oblasti
Služba Azure Image Builder bude k dispozici pro náhled v těchto oblastech. Obrázky mohou být distribuovány mimo tyto oblasti.
- USA – východ
- USA – východ 2
- USA – středozápad
- USA – západ
- USA – západ 2
- Severní Evropa
- Západní Evropa

## <a name="os-support"></a>Podpora pro Operační režim
AIB bude podporovat základní image operačního operačního operačního služby Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- Centos 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multi-session/Professional
- Windows 2016
- Windows 2019

Podpora ISO RHEL je zastaralá, přečtěte si dokumentaci k šabloně pro další podrobnosti.

## <a name="how-it-works"></a>Jak to funguje


![Koncepční výkres Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure Image Builder je plně spravovaná služba Azure, která je přístupná poskytovateli prostředků Azure. Proces Azure Image Builder má tři hlavní části: zdroj, přizpůsobení a distribuci, ty jsou reprezentovány v šabloně. Následující diagram znázorňuje součásti s některými jejich vlastnostmi. 
 


**Proces tvůrce obrázků** 

![Koncepční výkres procesu Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Vytvořte šablonu obrázku jako soubor JSON. Tento soubor JSON obsahuje informace o zdroji obrazu, vlastním nastavení a distribuci. Existuje několik příkladů v [úložišti GitHub Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Odešlete ji do služby, vytvoří se artefakt šablony obrázku ve skupině prostředků, kterou zadáte. Na pozadí image builder stáhne zdrojový obrázek nebo ISO a skripty podle potřeby. Ty jsou uloženy v samostatné skupině prostředků, která je automaticky\<vytvořena ve\<vašem předplatném, ve formátu: IT_ DestinationResourceGroup>_ TemplateName>. 
1. Po vytvoření šablony obrázku můžete vytvořit bitovou kopii. V nástroji Image Builder na pozadí používá šablonu a zdrojové soubory k vytvoření virtuálního počítače (výchozí\<velikost: Standard_D1_v2), sítě, veřejné IP, skupiny nsg a úložiště ve skupině>>> IT_ DestinationResource>_Group.\<
1. Jako součást vytvoření obrázku image builder distribuuje bitovou kopii podle šablony\<a poté\<odstraní další prostředky v IT_ DestinationResourceGroup>_ TemplateName> skupiny prostředků, která byla vytvořena pro proces.


## <a name="permissions"></a>Oprávnění

Chcete-li povolit Azure VM Image Builder distribuovat image do spravované image nebo do galerie sdílených bitových kopií, budete muset poskytnout oprávnění přispěvatele pro službu "Azure Virtual Machine Image Builder" (ID aplikace: cf32a0cc-373c-47c9-9156-0db11f6a6dfc) na skupiny prostředků. 

Pokud používáte existující vlastní spravovanou image nebo verzi image, bude Azure Image Builder potřebovat minimální přístup "Reader" k těmto skupinám prostředků.

Přístup můžete přiřadit pomocí příkazového příkazu k příkazu Azure:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Přístup můžete přiřadit pomocí Prostředí PowerShell:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Pokud účet služby nebyl nalezen, může to znamenat, že předplatné, kde přidáváte přiřazení role, ještě nebylo pro poskytovatele prostředků zaregistrováno.


## <a name="costs"></a>Náklady
Při vytváření, vytváření a ukládání ibi pomocí Azure Image Builder vám budou účtovány určité náklady na výpočetní prostředky, sítě a úložiště. Tyto náklady jsou podobné nákladům vzniklým při ručním vytváření vlastních bitových kopií. Za prostředky se vám budou účtovat sazby za Azure. 

Během procesu vytváření bitové kopie jsou `IT_<DestinationResourceGroup>_<TemplateName>` soubory staženy a uloženy ve skupině prostředků, které budou mít za následek malé náklady na úložiště. Pokud nechcete zachovat tyto, odstraňte **šablonu obrázku** po sestavení bitové kopie.
 
Image Builder vytvoří virtuální ho s využitím velikosti virtuálního počítače D1v2 a úložiště a sítě potřebné pro virtuální počítače. Tyto prostředky budou trvat po dobu trvání procesu sestavení a budou odstraněny, jakmile tvůrce obrázků dokončí vytváření bitové kopie. 
 
Azure Image Builder distribuuje image do vybraných oblastí, které mohou vzniknout poplatky za odchozí síťové přenosy.
 
## <a name="next-steps"></a>Další kroky 
 
Pokud otomnete Azure Image Builder, přečtěte si články o vytváření ibi [Linuxu](../articles/virtual-machines/linux/image-builder.md) nebo [Windows.](../articles/virtual-machines/windows/image-builder.md)
 
 
