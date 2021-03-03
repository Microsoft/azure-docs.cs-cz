---
title: Seznamte se s Azure image Builder (Preview)
description: Přečtěte si další informace o Azure image Builder pro virtuální počítače v Azure.
author: danielsollondon
ms.author: danis
ms.date: 05/02/2019
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.reviewer: cynthn
ms.openlocfilehash: 1c70edfc3bad2be70d26c71736ca06fcc4a8dcdb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672499"
---
# <a name="preview-azure-image-builder-overview"></a>Preview: Přehled nástroje Azure image Builder

Standardizované image virtuálních počítačů umožňují organizacím migrovat do cloudu a zajistit konzistenci nasazení. Obrázky obvykle zahrnují předdefinovaná nastavení zabezpečení a konfigurace a potřebný software. Nastavení vlastního kanálu pro vytváření imagí vyžaduje čas, infrastrukturu a instalaci, ale s nástrojem Azure VM Image Builder stačí jednoduše zadat jednoduchou konfiguraci popisující vaši image, odeslat ji do služby a image se sestaví a distribuuje.
 
Správce imagí virtuálních počítačů Azure (Azure image Builder) umožňuje začít s bitovou kopií Azure Marketplace založenou na Windows nebo Linux, stávající vlastní image nebo Red Hat Enterprise Linux (RHEL) ISO a začít přidávat vlastní vlastní nastavení. Vzhledem k tomu, že tvůrce imagí je postaven na [HashiCorp packu](https://packer.io/), můžete také naimportovat stávající skripty sestavovatele prostředí pro vytváření balíčků. Můžete taky určit, kde se mají vaše image hostovat, v [galerii sdílených imagí Azure](shared-image-galleries.md)jako spravovaná Image nebo VHD.

> [!IMPORTANT]
> Azure image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funkce ve verzi Preview

Pro verzi Preview jsou tyto funkce podporované:

- Vytváření zlatých základních imagí, které zahrnují vaše minimální zabezpečení a podnikové konfigurace, a umožňují oddělením jejich přizpůsobení ještě pro své potřeby.
- Při opravě stávajících imagí vám tvůrce imagí umožní průběžně opravovat stávající vlastní image.
- Připojte tvůrce imagí ke stávajícím virtuálním sítím, abyste se mohli připojit k existujícím konfiguračním serverům (DSC, Puppet atd.), sdíleným složkám nebo jiným směrovatelné serveru/službám.
- Integrace s galerií sdílených imagí Azure umožňuje distribuovat, spravovat verze a škálovat image globálně a poskytuje systém pro správu imagí.
- Integrace s existujícími kanály pro sestavení obrázků – stačí, když z vašeho kanálu vyvoláte tvůrce imagí, nebo použijete úlohu Azure DevOps pro tvůrce imagí ve verzi Preview.
- Migrujte existující kanál přizpůsobení image do Azure. K přizpůsobení imagí použijte své existující skripty, příkazy a procesy.
- Vytváření imagí ve formátu VHD pro podporu Azure Stack.
 

## <a name="regions"></a>Oblasti
Služba Azure image Builder bude k dispozici pro verzi Preview v těchto oblastech. Obrázky lze distribuovat mimo tyto oblasti.
- East US
- USA – východ 2
- USA – středozápad
- USA – západ
- Západní USA 2
- Severní Evropa
- West Europe

## <a name="os-support"></a>Podpora operačního systému
AIB bude podporovat image základního operačního systému Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CentOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise pro více relací/profesionály
- Systém Windows 2016
- Systém Windows 2019

Podpora RHEL soubory ISO už není podporovaná.

## <a name="how-it-works"></a>Jak to funguje

Azure image Builder je plně spravovaná služba Azure, která je přístupná pro poskytovatele prostředků Azure. Proces sestavovatele imagí Azure má tři hlavní části: zdroj, přizpůsobení a distribuce, které jsou zastoupeny v šabloně. Následující obrázek znázorňuje komponenty s některými jejich vlastnostmi. 
 

**Proces tvůrce imagí** 

![Koncepční vykreslení procesu Azure image Builder](./media/image-builder-overview/image-builder-process.png)

1. Vytvořte šablonu obrázku jako soubor. JSON. Tento soubor. JSON obsahuje informace o zdroji, přizpůsobení a distribuci obrázku. V [úložišti GitHub Azure image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)je několik příkladů.
1. Odešlete ji do služby. tím se vytvoří artefakt šablony obrázku ve skupině prostředků, kterou zadáte. Na pozadí bude tvůrce imagí stahovat zdrojové Image nebo ISO a skripty podle potřeby. Ukládají se do samostatné skupiny prostředků, která se automaticky vytvoří v rámci vašeho předplatného, ve formátu: IT_ \<DestinationResourceGroup> _ \<TemplateName> . 
1. Po vytvoření šablony image pak můžete vytvořit image. V Tvůrci imagí na pozadí používá šablona a zdrojové soubory k vytvoření virtuálního počítače (výchozí velikost: Standard_D1_v2), sítě, veřejné IP adresy, NSG a úložiště ve \<DestinationResourceGroup> skupině prostředků IT_ _ \<TemplateName> .
1. V rámci vytváření image tvůrce imagí distribuuje image podle šablony a pak odstraní další prostředky ve \<DestinationResourceGroup> \<TemplateName> skupině prostředků IT_ _, která se vytvořila pro tento proces.


## <a name="permissions"></a>Oprávnění
Při registraci pro (AIB) udělí služba AIB oprávnění vytvořit, spravovat a odstranit pracovní skupinu prostředků (IT_ *) a mít práva k přidávání prostředků do této služby, které jsou požadovány pro sestavení bitové kopie. K tomu slouží AIB hlavní název služby (SPN), který je ve vašem předplatném dostupný během úspěšné registrace.

Pokud chcete, aby tvůrce imagí virtuálních počítačů Azure mohl distribuovat image do spravovaných imagí nebo do sdílené Galerie imagí, budete muset vytvořit uživatelem přiřazenou identitu Azure, která má oprávnění ke čtení a zápisu imagí. Pokud přistupujete k Azure Storage, budete potřebovat oprávnění ke čtení privátních kontejnerů.

Nejdřív je nutné dodržet dokumentaci k [Vytvoření spravované identity přiřazené uživatelem Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) , jak vytvořit identitu.

Jakmile budete mít identitu, kterou potřebujete udělit, můžete k tomu použít definici vlastní role Azure a potom přiřadit spravovanou identitu přiřazenou uživateli, aby používala vlastní definici role.

[Tady](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)jsou podrobněji vysvětlena oprávnění a příklady ukazují, jak je to implementováno.

> [!Note]
> V minulosti jste používali AIB hlavní název služby AIB a udělili oprávnění hlavního názvu služby pro skupiny prostředků imagí. Od tohoto modelu přesouváme, aby bylo možné budoucí možnosti využít. V 26 může 2020 image Builder nepřijímá šablony, které nemají přiřazenou identitu uživatele, takže stávající šablony bude nutné znovu odeslat do služby pomocí [uživatelské identity](./linux/image-builder-json.md). Níže uvedené příklady již ukazují, jak lze vytvořit uživatelem přiřazenou identitu a přidat je do šablony. Další informace najdete v této [dokumentaci](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information) k této změně a vydání aktualizací.

## <a name="costs"></a>Náklady
Při vytváření, sestavování a ukládání imagí pomocí Azure image Builder budete mít za následek několik výpočetních, síťových a úložných nákladů. Tyto náklady jsou podobné nákladům, které vznikly ručním vytvářením vlastních imagí. U prostředků se vám budou účtovat sazby za Azure. 

Během procesu vytváření imagí se soubory stahují a ukládají do `IT_<DestinationResourceGroup>_<TemplateName>` skupiny prostředků, čímž se účtují malé náklady na úložiště. Pokud je nechcete zachovat, odstraňte **šablonu image** po sestavení obrázku.
 
Image Builder vytvoří virtuální počítač s použitím velikosti virtuálního počítače s D1v2 a úložiště a sítě, které potřebuje pro virtuální počítač. Tyto prostředky budou poslední po dobu trvání procesu sestavení a budou odstraněny po dokončení vytváření image tvůrcem imagí. 
 
Azure image Builder rozšíří image do zvolených oblastí, což může způsobit poplatky za odchozí přenos v síti.

## <a name="hyper-v-generation"></a>Generace technologie Hyper-V
Nástroj image Builder aktuálně nativně podporuje vytváření imagí technologie Hyper-V Generation (Gen1) 1 do galerie sdílených imagí Azure (SIG) nebo spravované image. Pokud chcete vytvořit image Gen2, musíte použít zdrojovou image Gen2 a distribuovat ji na VHD. Potom budete muset vytvořit spravovanou bitovou kopii z virtuálního pevného disku a vložit ji do souboru SIG jako Gen2 image.
 
## <a name="next-steps"></a>Další kroky 
 
Pokud si chcete vyzkoušet Azure image Builder, přečtěte si články pro vytváření imagí pro [Linux](./linux/image-builder.md) nebo [Windows](./windows/image-builder.md) .

