---
author: cynthn
ms.author: cynthn
ms.date: 05/15/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 9bbf2288ac92c33c2b24af4e6a97882013a4ff1d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673454"
---
Standardizované image virtuálních počítačů umožňují organizacím migrovat do cloudu a zajistit konzistenci nasazení. Obrázky obvykle zahrnují předdefinovaná nastavení zabezpečení a konfigurace a potřebný software. Nastavení vlastního kanálu pro vytváření imagí vyžaduje čas, infrastrukturu a instalaci, ale s nástrojem Azure VM Image Builder stačí jednoduše zadat jednoduchou konfiguraci popisující vaši image, odeslat ji do služby a image se sestaví a distribuuje.
 
Správce imagí virtuálních počítačů Azure (Azure image Builder) umožňuje začít s bitovou kopií Azure Marketplace založenou na Windows nebo Linux, stávající vlastní image nebo Red Hat Enterprise Linux (RHEL) ISO a začít přidávat vlastní vlastní nastavení. Vzhledem k tomu, že tvůrce imagí je postaven na [HashiCorp packu](https://packer.io/), můžete také naimportovat stávající skripty sestavovatele prostředí pro vytváření balíčků. Můžete taky určit, kde se mají vaše image hostovat, v [galerii sdílených imagí Azure](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)jako spravovaná Image nebo VHD.

> [!IMPORTANT]
> Azure image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funkce Preview

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
- USA – východ
- USA – východ 2
- USA – středozápad
- USA – západ
- USA – západ 2
- Severní Evropa
- Západní Evropa

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

Podpora RHEL soubory ISO se už nepodporuje.
## <a name="how-it-works"></a>Jak to funguje


![Koncepční vykreslování Azure image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure image Builder je plně spravovaná služba Azure, která je přístupná pro poskytovatele prostředků Azure. Proces sestavovatele imagí Azure má tři hlavní části: zdroj, přizpůsobení a distribuce, které jsou zastoupeny v šabloně. Následující obrázek znázorňuje komponenty s některými jejich vlastnostmi. 
 


**Proces tvůrce imagí** 

![Koncepční vykreslení procesu Azure image Builder](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Vytvořte šablonu obrázku jako soubor. JSON. Tento soubor. JSON obsahuje informace o zdroji, přizpůsobení a distribuci obrázku. V [úložišti GitHub Azure image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)je několik příkladů.
1. Odešlete ji do služby. tím se vytvoří artefakt šablony obrázku ve skupině prostředků, kterou zadáte. Na pozadí bude tvůrce imagí stahovat zdrojové Image nebo ISO a skripty podle potřeby. Ukládají se do samostatné skupiny prostředků, která se automaticky vytvoří v rámci vašeho předplatného, ve formátu: IT_ \< DestinationResourceGroup>_ \< template>. 
1. Po vytvoření šablony image pak můžete vytvořit image. V Tvůrci imagí na pozadí se pomocí šablony a zdrojových souborů vytvoří virtuální počítač (výchozí velikost: Standard_D1_v2), síť, veřejná IP adresa, NSG a úložiště v IT_ \< DestinationResourceGroup>_ \< šablony prostředků>.
1. V rámci vytváření image tvůrce imagí distribuuje image podle šablony a pak odstraní další prostředky IT_ ve \< \< skupině prostředků DestinationResourceGroup>_ Template>, která se vytvořila pro tento proces.


## <a name="permissions"></a>Oprávnění
Při registraci pro (AIB) udělí služba AIB oprávnění vytvořit, spravovat a odstranit pracovní skupinu prostředků (IT_ *) a mít práva k přidávání prostředků do této služby, které jsou požadovány pro sestavení bitové kopie. K tomu slouží AIB hlavní název služby (SPN), který je ve vašem předplatném dostupný během úspěšné registrace.

Pokud chcete, aby tvůrce imagí virtuálních počítačů Azure mohl distribuovat image do spravovaných imagí nebo do sdílené Galerie imagí, budete muset vytvořit uživatelem přiřazenou identitu Azure, která má oprávnění ke čtení a zápisu imagí. Pokud přistupujete k Azure Storage, budete potřebovat oprávnění ke čtení privátních kontejnerů.

Nejdřív musíte [vytvořit dokumentaci spravované identity přiřazené uživatelem Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli) , která vám umožní vytvořit identitu.

Jakmile budete mít identitu, kterou potřebujete udělit, můžete k tomu použít definici vlastní role Azure a potom přiřadit spravovanou identitu přiřazenou uživateli, aby používala vlastní definici role.

[Tady](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)jsou podrobněji vysvětlena oprávnění a příklady ukazují, jak je to implementováno.

> [!Note]
> V minulosti jste používali AIB hlavní název služby AIB a udělili oprávnění hlavního názvu služby pro skupiny prostředků imagí. Od tohoto modelu přesouváme, aby bylo možné budoucí možnosti využít. V 26 může 2020 image Builder nepřijímá šablony, které nemají přiřazenou identitu uživatele, takže stávající šablony bude nutné znovu odeslat do služby pomocí [uživatelské identity](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#identity). Níže uvedené příklady již ukazují, jak lze vytvořit uživatelem přiřazenou identitu a přidat je do šablony. Další informace najdete v této [dokumentaci](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information) k této změně a vydání aktualizací.

## <a name="costs"></a>Náklady
Při vytváření, sestavování a ukládání imagí pomocí Azure image Builder budete mít za následek několik výpočetních, síťových a úložných nákladů. Tyto náklady jsou podobné nákladům, které vznikly ručním vytvářením vlastních imagí. U prostředků se vám budou účtovat sazby za Azure. 

Během procesu vytváření imagí se soubory stahují a ukládají do `IT_<DestinationResourceGroup>_<TemplateName>` skupiny prostředků, čímž se účtují malé náklady na úložiště. Pokud je nechcete zachovat, odstraňte **šablonu image** po sestavení obrázku.
 
Image Builder vytvoří virtuální počítač s použitím velikosti virtuálního počítače s D1v2 a úložiště a sítě, které potřebuje pro virtuální počítač. Tyto prostředky budou poslední po dobu trvání procesu sestavení a budou odstraněny po dokončení vytváření image tvůrcem imagí. 
 
Azure image Builder rozšíří image do zvolených oblastí, což může způsobit poplatky za odchozí přenos v síti.
 
## <a name="next-steps"></a>Další kroky 
 
Pokud si chcete vyzkoušet Azure image Builder, přečtěte si články pro vytváření imagí pro [Linux](../articles/virtual-machines/linux/image-builder.md) nebo [Windows](../articles/virtual-machines/windows/image-builder.md) .
 
