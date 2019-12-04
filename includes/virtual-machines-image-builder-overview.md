---
author: cynthn
ms.author: cynthn
ms.date: 11/25/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2a763bbd50f009ae469be889e6ebae0b0d90848b
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795814"
---
Standardizované image virtuálních počítačů umožňují organizacím migrovat do cloudu a zajistit konzistenci nasazení. Obrázky obvykle zahrnují předdefinovaná nastavení zabezpečení a konfigurace a potřebný software. Nastavení vlastního kanálu pro vytváření imagí vyžaduje čas, infrastrukturu a instalaci, ale s nástrojem Azure VM Image Builder stačí jednoduše zadat jednoduchou konfiguraci popisující vaši image, odeslat ji do služby a image se sestaví a distribuuje.
 
Správce imagí virtuálních počítačů Azure (Azure image Builder) umožňuje začít s bitovou kopií Azure Marketplace založenou na Windows nebo Linux, stávající vlastní image nebo Red Hat Enterprise Linux (RHEL) ISO a začít přidávat vlastní vlastní nastavení. Vzhledem k tomu, že tvůrce imagí je postaven na [HashiCorp packu](https://packer.io/), můžete také naimportovat stávající skripty sestavovatele prostředí pro vytváření balíčků. Můžete taky určit, kde se mají vaše image hostovat, v [galerii sdílených imagí Azure](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)jako spravovaná Image nebo VHD.

> [!IMPORTANT]
> Azure image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funkce verze Preview

Pro verzi Preview jsou tyto funkce podporované:

- Vytváření zlatých základních imagí, které zahrnují vaše minimální zabezpečení a podnikové konfigurace, a umožňují oddělením jejich přizpůsobení ještě pro své potřeby.
- Při opravě stávajících imagí vám tvůrce imagí umožní průběžně opravovat stávající vlastní image.
- Integrace s galerií sdílených imagí Azure umožňuje distribuovat, spravovat verze a škálovat image globálně a poskytuje systém pro správu imagí.
- Integrace s existujícími kanály pro sestavení obrázků – stačí, když z vašeho kanálu vyvoláte tvůrce imagí, nebo použijete úlohu Azure DevOps pro tvůrce imagí ve verzi Preview.
- Migrujte existující kanál přizpůsobení image do Azure. K přizpůsobení imagí použijte své existující skripty, příkazy a procesy.
- Použití Red Hat přináší vlastní podporu předplatného. Vytvářejte image Red Hat Enterprise pro použití s oprávněnými nepoužívanými předplatnými Red Hat.
- Vytváření imagí ve formátu VHD.
 

## <a name="regions"></a>Oblasti
Služba Azure image Builder bude k dispozici pro verzi Preview v těchto oblastech. Obrázky lze distribuovat mimo tyto oblasti.
- USA – východ
- Východ USA 2
- Středozápadní USA
- Západní USA
- Západní USA 2

## <a name="os-support"></a>Podpora operačního systému
AIB bude podporovat image základního operačního systému Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6
- CentOS 7,6
- Windows 10 RS5 Enterprise/Professional/Enterprise pro Virtual Desktop (EVD) 
- Windows 2016
- Systém Windows 2019

AIB bude podporovat RHEL ISO jako zdroj pro:
- RHEL 7,3
- RHEL 7,4
- RHEL 7.5

RHEL 7,6 soubory ISO nejsou podporovány, ale jsou testovány.

## <a name="how-it-works"></a>Jak to funguje


![Koncepční vykreslování Azure image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure image Builder je plně spravovaná služba Azure, která je přístupná pro poskytovatele prostředků Azure. Proces sestavovatele imagí Azure má tři hlavní části: zdroj, přizpůsobení a distribuce, které jsou zastoupeny v šabloně. Následující obrázek znázorňuje komponenty s některými jejich vlastnostmi. 
 


**Proces tvůrce imagí** 

![Koncepční vykreslení procesu Azure image Builder](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Vytvořte šablonu obrázku jako soubor. JSON. Tento soubor. JSON obsahuje informace o zdroji, přizpůsobení a distribuci obrázku. V [úložišti GitHub Azure image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)je několik příkladů.
1. Odešlete ji do služby. tím se vytvoří artefakt šablony obrázku ve skupině prostředků, kterou zadáte. Na pozadí bude tvůrce imagí stahovat zdrojové Image nebo ISO a skripty podle potřeby. Ukládají se do samostatné skupiny prostředků, která se automaticky vytvoří v rámci vašeho předplatného, ve formátu: IT_\<DestinationResourceGroup > _\<template >. 
1. Po vytvoření šablony image pak můžete vytvořit image. V Tvůrci imagí na pozadí používá šablona a zdrojové soubory k vytvoření virtuálního počítače (výchozí velikost: Standard_D1_v2), sítě, veřejné IP adresy, NSG a úložiště v IT_\<DestinationResourceGroup > _\<template > skupiny prostředků.
1. V rámci vytvoření bitové kopie obrázek sestaví image podle šablony a pak odstraní další prostředky v IT_\<DestinationResourceGroup > _\<template > Group, která byla vytvořena pro daný proces.


## <a name="permissions"></a>Oprávnění

Pokud chcete, aby tvůrce imagí virtuálních počítačů Azure mohl distribuovat image do spravovaných imagí nebo do sdílené Galerie imagí, budete muset pro službu Azure Virtual Machine image Builder zadat oprávnění Přispěvatel (ID aplikace: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) ve skupinách prostředků. 

Pokud používáte existující vlastní verzi spravované Image nebo Image, bude Azure image Builder potřebovat pro tyto skupiny prostředků minimálně přístup čtenářů.

Přístup můžete přiřadit pomocí Azure CLI:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Pokud se účet služby nenajde, může to znamenat, že předplatné, do kterého přidáváte přiřazení role, ještě není zaregistrované pro poskytovatele prostředků.


## <a name="costs"></a>Náklady
Při vytváření, sestavování a ukládání imagí pomocí Azure image Builder budete mít za následek několik výpočetních, síťových a úložných nákladů. Tyto náklady jsou podobné nákladům, které vznikly ručním vytvářením vlastních imagí. U prostředků se vám budou účtovat sazby za Azure. 

Během procesu vytváření imagí se soubory stahují a ukládají do skupiny prostředků `IT_<DestinationResourceGroup>_<TemplateName>`, čímž se účtují malé náklady na úložiště. Pokud je nechcete zachovat, odstraňte **šablonu image** po sestavení obrázku.
 
Image Builder vytvoří virtuální počítač s použitím velikosti virtuálního počítače s D1v2 a úložiště a sítě, které potřebuje pro virtuální počítač. Tyto prostředky budou poslední po dobu trvání procesu sestavení a budou odstraněny po dokončení vytváření image tvůrcem imagí. 
 
Azure image Builder rozšíří image do zvolených oblastí, což může způsobit poplatky za odchozí přenos v síti.
 
## <a name="next-steps"></a>Další kroky 
 
Pokud si chcete vyzkoušet Azure image Builder, přečtěte si články pro vytváření imagí pro [Linux](../articles/virtual-machines/linux/image-builder.md) nebo [Windows](../articles/virtual-machines/windows/image-builder.md) .
 
 
