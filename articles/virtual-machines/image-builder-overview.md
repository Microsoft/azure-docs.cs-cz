---
title: Seznamte se s Azure image Builder (Preview)
description: Přečtěte si další informace o Azure image Builder pro virtuální počítače v Azure.
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 20bb6925f859d497046eb42bbafb5264826b77b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604062"
---
# <a name="preview-azure-image-builder-overview"></a>Preview: Přehled nástroje Azure image Builder

Standardizované image virtuálních počítačů umožňují organizacím migrovat do cloudu a zajistit konzistenci nasazení. Obrázky obvykle zahrnují předdefinovaná nastavení zabezpečení a konfigurace a potřebný software. Nastavení vlastního kanálu pro vytváření imagí vyžaduje čas, infrastrukturu a instalaci, ale pomocí nástroje Azure VM Image Builder stačí poskytnout konfiguraci popisující vaši image, odeslat ji do služby a image se sestaví a distribuuje.
 
Správce imagí virtuálních počítačů Azure (Azure image Builder) umožňuje začít s bitovou kopií Azure Marketplace založenou na Windows nebo Linux, stávající vlastní image a začít přidávat vlastní vlastní nastavení. Vzhledem k tomu, že je tvůrce imagí postaven na [HashiCorp packu](https://packer.io/) , uvidíte některé podobnosti, ale budete mít výhodu spravované služby. Můžete taky určit, kde se mají vaše image hostovat, v [galerii sdílených imagí Azure](shared-image-galleries.md)jako spravovaná Image nebo VHD.

> [!IMPORTANT]
> Azure image Builder je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funkce ve verzi Preview

Pro verzi Preview jsou tyto funkce podporované:

- Vytváření základních imagí, které zahrnují vaše minimální zabezpečení a podnikové konfigurace, a umožňují oddělením jejich další přizpůsobení.
- Integrace základních aplikací, takže virtuální počítač může po vytvoření provádět úlohy nebo přidat konfigurace pro podporu imagí virtuálních klientů Windows.
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

## <a name="how-it-works"></a>Jak to funguje

Azure VM Image Builder je plně spravovaná služba Azure, která je přístupná pro poskytovatele prostředků Azure. Zadejte konfiguraci služby, která určuje zdrojovou image, vlastní nastavení, které se má provést a kam se má nový obrázek distribuovat, a níže uvedený diagram znázorňuje pracovní postup vysoké úrovně:

![Koncepční vykreslení procesu Azure image Builder zobrazující zdroje (Windows/Linux), přizpůsobení (prostředí, PowerShell, Windows restart & aktualizace, přidávání souborů) a globální distribuci s využitím Galerie sdílených imagí Azure](./media/image-builder-overview/image-builder-flow.png)

Konfigurace šablon se dají předávat pomocí PowerShellu, AZ CLI, šablon ARM a pomocí úlohy DevOps pro tvůrce imagí virtuálních počítačů Azure. když ji odešlete do služby, vytvoříme prostředek šablony obrázku. Po vytvoření prostředku šablony image se zobrazí pracovní skupina prostředků vytvořená ve vašem předplatném ve formátu: IT_ \<DestinationResourceGroup> _\<TemplateName>_ \( GUID). Pracovní skupina prostředků obsahuje soubory a skripty, na které se odkazuje v souboru, prostředí, přizpůsobení PowerShellu ve vlastnosti ScriptURI.

Chcete-li spustit sestavení, které použijete `Run` v prostředku šablony bitové kopie, služba pak nasadí další prostředky pro sestavení, jako je například virtuální počítač, síť, disk, síťový adaptér atd. Pokud sestavíte image bez použití existujícího nástroje VM Image Builder, nasadí se taky veřejná IP adresa a NSG, služba se připojí k virtuálnímu počítači sestavení pomocí SSH nebo WinRM. Pokud vyberete existující virtuální síť, služba se nasadí pomocí privátního odkazu Azure a veřejná IP adresa se nevyžaduje. Další podrobnosti o sítích tvůrce imagí si můžete prohlédnout v [podrobnostech](./linux/image-builder-networking.md).

Po dokončení sestavení budou všechny prostředky odstraněny, s výjimkou pracovní skupiny prostředků a účtu úložiště, abyste je odebrali, odstraníte prostředek šablony obrázku, nebo můžete nechat sestavení spustit znovu.

V této dokumentaci je několik příkladů a podrobných průvodců, které odkazují na šablony konfigurace a řešení v [úložišti GitHub Azure image Builder](https://github.com/azure/azvmimagebuilder).

### <a name="move-support"></a>Přesunout podporu
Prostředek šablony obrázku je neměnný a obsahuje odkazy na prostředky a pracovní skupinu prostředků, a proto typ prostředku nepodporuje přesunutí. Pokud chcete přesunout prostředek šablony obrázku, ujistěte se, že máte kopii šablony konfigurace (pokud ji nemáte, rozbalte existující konfiguraci z prostředku), vytvořte nový prostředek šablony image v nové skupině prostředků s novým názvem a odstraňte předchozí prostředek šablony obrázku. 

## <a name="permissions"></a>Oprávnění
Při registraci pro (AIB) udělí služba AIB oprávnění vytvořit, spravovat a odstranit pracovní skupinu prostředků (IT_ *) a mít práva k přidávání prostředků do této služby, které jsou požadovány pro sestavení bitové kopie. K tomu slouží AIB hlavní název služby (SPN), který je ve vašem předplatném dostupný během úspěšné registrace.

Pokud chcete, aby tvůrce imagí virtuálních počítačů Azure mohl distribuovat image do spravovaných imagí nebo do sdílené Galerie imagí, budete muset vytvořit uživatelem přiřazenou identitu Azure, která má oprávnění ke čtení a zápisu imagí. Pokud přistupujete k Azure Storage, budete potřebovat oprávnění ke čtení privátních a veřejných kontejnerů.

Oprávnění jsou vysvětlena podrobněji pro [PowerShell](./linux/image-builder-permissions-powershell.md)a [AZ CLI](./linux/image-builder-permissions-cli.md).

## <a name="costs"></a>Náklady
Při vytváření, sestavování a ukládání imagí pomocí Azure image Builder budete mít za následek několik výpočetních, síťových a úložných nákladů. Tyto náklady jsou podobné nákladům, které vznikly ručním vytvářením vlastních imagí. U prostředků se vám budou účtovat sazby za Azure. 

Během procesu vytváření imagí se soubory stahují a ukládají do `IT_<DestinationResourceGroup>_<TemplateName>` skupiny prostředků, čímž se účtují malé náklady na úložiště. Pokud je nechcete zachovat, odstraňte **šablonu image** po sestavení obrázku.
 
Image Builder vytvoří virtuální počítač s použitím velikosti virtuálního počítače s D1v2 a úložiště a sítě, které potřebuje pro virtuální počítač. Tyto prostředky budou poslední po dobu trvání procesu sestavení a budou odstraněny po dokončení vytváření image tvůrcem imagí. 
 
Azure image Builder rozšíří image do zvolených oblastí, což může způsobit poplatky za odchozí přenos v síti.

## <a name="hyper-v-generation"></a>Generace technologie Hyper-V
Nástroj image Builder aktuálně nativně podporuje vytváření imagí technologie Hyper-V Generation (Gen1) 1 do galerie sdílených imagí Azure (SIG) nebo spravované image. 
 
## <a name="next-steps"></a>Další kroky 
 
Pokud si chcete vyzkoušet Azure image Builder, přečtěte si články pro vytváření imagí pro [Linux](./linux/image-builder.md) nebo [Windows](./windows/image-builder.md) .