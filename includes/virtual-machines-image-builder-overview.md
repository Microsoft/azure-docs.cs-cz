---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: b9b2461d888f37b7ae72a3e097d77856255d7e2e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160002"
---
Image standardizované virtuálních počítačů (VM) umožňují organizacím migrace do cloudu a zajistit konzistenci v nasazení. Image obvykle zahrnují předdefinované nastavení zabezpečení a konfigurace a potřebný software. Nastavení pro vytváření bitových kopií kanálu vyžaduje čas, infrastruktury a nastavení, ale pomocí Tvůrce Image virtuálního počítače Azure, stačí zadat jednoduchou konfiguraci a popis bitové kopie, odeslat ho do služby a bitové kopie vytvořené a distribuovat.
 
Image Builder pro virtuální počítač Azure (Azure Image Builder) umožňuje začínat Windows nebo bitové kopie založené na Linuxu v Azure Marketplace, existující vlastních imagí nebo Red Hat Enterprise Linux (RHEL) ISO a začít přidávat své vlastní úpravy. Vzhledem k tomu, že Image Builder je postavená na [HashiCorp Packeru](https://packer.io/), můžete také importovat existující skripty zajištění webu Packeru prostředí. Můžete také určit, kde chcete váš obrázky hostované ve sdílené Galerie Imagí Azure (virtual-machines-common-shared-image-galleries.md), jako spravované image nebo virtuální pevný disk.

> [!IMPORTANT]
> Image Builder pro Azure je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funkce verze Preview

Pro verzi preview se podporují tyto funkce:

- Vytváření imagí zlaté směrného plánu, který obsahuje minimální zabezpečení a podnikové konfigurace a umožňují oddělení dále přizpůsobit pro svoje potřeby.
- Opravy chyb z existujících imagí, Image Builder vám umožní průběžně opravu existující vlastní Image.
- Integrovat do Galerie Imagí Azure sdílené, můžete distribuovat, verze, a škálování imagí globally a poskytuje bitové kopie systému pro správu.
- Integraci pomocí stávající image vytvářet kanály, stačí volání Image Builder pro z vašeho kanálu nebo pomocí jednoduchých Azure DevOps úlohy Tvůrce Image ve verzi Preview.
- Migrace existující kanál přizpůsobení image do Azure. Přizpůsobení imagí pomocí stávající skripty, příkazy a procesy.
- Použití podpory Red Hat přineste si vlastní předplatné. Vytváření imagí Red Hat Enterprise pro použití pomocí vašich způsobilých nevyužitých předplatných Red Hat.
- Vytváření obrázků ve formátu virtuálního pevného disku.
 

## <a name="regions"></a>Oblasti
Služba Azure Image Builder bude dostupná ve verzi preview v těchto oblastech. Bitové kopie mohou být distribuovány mimo tyto oblasti.
- USA – východ
- Východní USA 2
- Západní střed USA
- Západní USA
- Západní USA 2

## <a name="os-support"></a>Podporu operačního systému
AIB bude podporovat Azure Marketplace základní Image operačního systému:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6
- CentOS 7.6
- Windows 2016
- Windows 2019


## <a name="how-it-works"></a>Jak to funguje


![Koncepční nákres Image Builder pro Azure](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure Image Builder je plně spravovaná služba Azure, který je přístupný pro poskytovatele prostředků Azure. Proces Azure Image Builder má tři hlavní části: zdroje, přizpůsobit a distribuci, ty jsou reprezentovány v šabloně. Následující diagram znázorňuje komponenty, pomocí některé z jejich vlastností. 
 


**Proces Image Builder** 

![Koncepční nákres proces Image Builder pro Azure](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Vytvoření Image šablony jako soubor .json. Tento soubor .json obsahuje informace o zdroj bitové kopie, přizpůsobení a distribuci. Existuje několik příkladů v [úložiště Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Odeslat do služby, tím se vytvoří artefakt Image šablony ve skupině prostředků, kterou zadáte. Na pozadí stáhne Image Builder zdrojového obrázku nebo ISO a skripty podle potřeby. Tyto jsou uložené v samostatné skupiny prostředků, které se automaticky vytvoří ve vašem předplatném, ve formátu: IT_<DestinationResourceGroup>_<TemplateName>. 
1. Po vytvoření Image šablony lze následně vytvořit bitovou kopii. Image Builder na pozadí používá šablony a zdrojových souborech k vytvoření virtuálního počítače, sítě a úložiště v IT_<DestinationResourceGroup>_<TemplateName> skupinu prostředků.
1. Jako součást vytváření image, Image builder distribuuje bitovou kopii podle šablony, pak odstraní další prostředky v IT_<DestinationResourceGroup>_<TemplateName> skupinu prostředků, která byla vytvořena pro proces.


## <a name="permissions"></a>Oprávnění

Povolit Azure VM Image Builder pro distribuci Image do spravované imagí, nebo do Galerie Imagí sdílené, budete muset zadat oprávnění "Přispěvatel" služby "Image Builder pro virtuální počítač Azure" (ID aplikace: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) o skupinách prostředků. 

Pokud používáte k existující vlastní spravované image nebo verze image, Image Builder pro Azure, bude nutné minimálně "Čtečky" přístup do těchto skupin prostředků.

Můžete přiřadit přístup pomocí rozhraní příkazového řádku Azure:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Pokud účet služby se nenašel, může to znamená, že předplatné, ve kterém chcete přidat přiřazení role nebyl ještě zaregistrované u poskytovatele prostředků.


## <a name="costs"></a>Náklady
Vám budou účtovat nějaké compute, sítě a náklady na úložiště při vytváření, sestavování a ukládání imagí s Image Builder pro Azure. Tyto náklady jsou podobné náklady na ruční vytvoření vlastních imagí. Za prostředky vám bude účtovat sazby Azure. 

Během procesu vytváření image, stáhnou se soubory a uložená v `IT_<DestinationResourceGroup>_<TemplateName>` skupinu prostředků, která budou účtovat poplatky za malé úložiště. f nechcete zachovat, odstranit šablonu Image po sestavení image.
 
Image Builder vytvoří virtuální počítač pomocí velikost virtuálního počítače. D1v2 a úložiště a sítě potřebná pro virtuální počítač. Tyto prostředky se naposledy po dobu trvání procesu sestavení a odstraní po dokončení vytváření bitové kopie Image Builder. 
 
Image Builder pro Azure bude distribuovat bitovou kopii do vybrané oblasti, kterému můžou být účtovány poplatky za výchozí přenos dat sítě.
 
## <a name="next-steps"></a>Další postup 
 
Vyzkoušejte si Azure Image Builder, najdete v článcích pro sestavení [Linux](../articles/virtual-machines/linux/image-builder.md) nebo [Windows](../articles/virtual-machines/windows/image-builder.md) bitové kopie.
 
 
