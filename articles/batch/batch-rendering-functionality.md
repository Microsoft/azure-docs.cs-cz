---
title: Možnosti vykreslování
description: Pro spouštění úloh vykreslování a aplikací se používají standardní Azure Batch možnosti. Batch zahrnuje konkrétní funkce pro podporu vykreslování úloh.
author: mscurrell
ms.author: markscu
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: a2e2cfb71999bd5ab83591448342d4bac1dabdd5
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496333"
---
# <a name="azure-batch-rendering-capabilities"></a>Možnosti vykreslování Azure Batch

Pro spouštění úloh vykreslování a aplikací se používají standardní Azure Batch možnosti. Batch zahrnuje také konkrétní funkce pro podporu úloh vykreslování.

Přehled konceptů služby Batch, včetně fondů, úloh a úkolů, najdete v [tomto článku](./batch-service-workflow-features.md).

## <a name="batch-pools-using-custom-vm-images-and-standard-application-licensing"></a>Fondy služby Batch pomocí vlastních imagí virtuálních počítačů a licencování standardních aplikací

Stejně jako u jiných úloh a typů aplikace je možné vytvořit vlastní image virtuálního počítače s požadovanými vykreslovacími aplikacemi a moduly plug-in. Vlastní image virtuálního počítače se umístí do [Galerie sdílených imagí](../virtual-machines/shared-image-galleries.md) a [dá se použít k vytvoření fondů Batch](batch-sig-images.md).

Řetězce příkazového řádku úlohy budou muset odkazovat na aplikace a cesty použité při vytváření vlastní image virtuálního počítače.

Většina aplikací pro vykreslování bude vyžadovat licence získané z licenčního serveru. Pokud existuje stávající místní licenční server, musí být fond i licenční server ve stejné [virtuální síti](../virtual-network/virtual-networks-overview.md). Je také možné spustit licenční server na virtuálním počítači Azure s fondem služby Batch a virtuálním počítačem licenčního serveru, který se nachází ve stejné virtuální síti.

## <a name="batch-pools-using-rendering-vm-images"></a>Fondy dávek využívající vykreslování imagí virtuálních počítačů

> [!IMPORTANT]
> Vykreslování imagí virtuálních počítačů a licencování s platbami za použití se už [nepoužívá a vyřadí se od 29. února 2024](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/). Chcete-li použít dávku pro vykreslování, je [třeba použít vlastní image virtuálního počítače a licencování standardní aplikace.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

### <a name="rendering-application-installation"></a>Vykreslování instalace aplikace

Image virtuálního počítače pro vykreslování Azure Marketplace se dá zadat v konfiguraci fondu, pokud je potřeba použít jenom předem nainstalované aplikace.

K dispozici je bitová kopie systému Windows a CentOS obrázek.  V [Azure Marketplace](https://azuremarketplace.microsoft.com)se image virtuálních počítačů dají najít hledáním dávkového vykreslování.

Příklad konfigurace fondu najdete v [kurzu vykreslování Azure CLI](./tutorial-rendering-cli.md).  Azure Portal a Batch Explorer poskytují nástroje grafického uživatelského rozhraní pro výběr image virtuálního počítače při vytváření fondu.  Pokud používáte rozhraní API služby Batch, zadejte pro [element imagereference](/rest/api/batchservice/pool/add#imagereference) při vytváření fondu následující hodnoty vlastností:

| Publisher | Nabídka | Skladová jednotka (SKU) | Verze |
|---------|---------|---------|--------|
| dávka | vykreslování – centos73 | vykreslování | nejnovější |
| dávka | vykreslování – windows2016 | vykreslování | nejnovější |

Další možnosti jsou k dispozici, pokud jsou na virtuálních počítačích fondu požadovány další aplikace:

* Vlastní image z Galerie sdílených imagí:
  * Pomocí této možnosti můžete na virtuálním počítači nakonfigurovat konkrétní aplikace a verze, které požadujete. Další informace najdete v tématu [Vytvoření fondu pomocí Galerie sdílených imagí](batch-sig-images.md). Skupina Autodesk a chaos upravila Arnold a V-Ray, aby se ověřila proti službě Azure Batch Licensing Service. Ujistěte se, že máte verze těchto aplikací s touto podporou, jinak licencování s platbami za použití nebude fungovat. Aktuální verze Maya nebo 3DS nevyžadují při spuštění bez periferního serveru licenční server (v režimu dávky nebo příkazového řádku). Pokud si nejste jistí, jak v této možnosti pokračovat, obraťte se na podporu Azure.
* [Balíčky aplikací](./batch-application-packages.md):
  * Zabalit soubory aplikace pomocí jednoho nebo více souborů ZIP, nahrajte je přes Azure Portal a určete balíček v konfiguraci fondu. Při vytváření virtuálních počítačů fondu se stáhnou soubory ZIP a extrahované soubory.
* Soubory prostředků:
  * Soubory aplikace se nahrají do úložiště objektů BLOB v Azure a v [úloze spuštění fondu](/rest/api/batchservice/pool/add#starttask)se určí odkazy na soubory. Při vytváření virtuálních počítačů fondu se soubory prostředků stáhnou do každého virtuálního počítače.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Licencování s platbami za použití pro předem nainstalované aplikace

V konfiguraci fondu je nutné zadat aplikace, které budou použity, a mít licenční poplatek.

* Zadejte `applicationLicenses` vlastnost při [vytváření fondu](/rest/api/batchservice/pool/add#request-body).  V poli řetězců lze zadat následující hodnoty: "Vray", "Arnold", "3dsmax", "Maya".
* Když zadáte jednu nebo více aplikací, náklady na tyto aplikace se přidají do nákladů na virtuální počítače.  Ceny za aplikace jsou uvedené na [stránce s cenami Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Pokud se místo toho připojíte k licenčnímu serveru, abyste mohli používat aplikace pro vykreslování, nezadávejte `applicationLicenses` vlastnost.

Pomocí Azure Portal nebo Batch Explorer můžete vybrat aplikace a zobrazit ceny aplikací.

Pokud dojde k pokusu o použití aplikace, ale aplikace nebyla zadána ve `applicationLicenses` Vlastnosti konfigurace fondu nebo nedorazila k licenčnímu serveru, spuštění aplikace se nezdaří s chybou licencování a nenulovým ukončovacím kódem.

### <a name="environment-variables-for-pre-installed-applications"></a>Proměnné prostředí pro předem nainstalované aplikace

Aby bylo možné vytvořit příkazový řádek pro úlohy vykreslování, musí být zadáno umístění instalace spustitelných souborů aplikace.  Na imagích Azure Marketplace virtuálních počítačů se vytvořily systémové proměnné prostředí, které se dají použít místo určení skutečných cest.  Tyto proměnné prostředí jsou kromě [standardních proměnných prostředí služby Batch](./batch-compute-node-environment-variables.md) vytvořených pro jednotlivé úlohy.

|Aplikace|Spustitelný soubor aplikace|Proměnná prostředí|
|---------|---------|---------|
|Autodesk 3ds Max 2021|3dsmaxcmdio.exe|3DSMAX_2021_EXEC|
|Autodesk Maya 2020|render.exe|MAYA_2020_EXEC|
|Chaos Group V-ray Standalone|vray.exe|VRAY_4 VRAY_4.10.03_EXEC|
|Příkazový řádek Arnold 2020|kick.exe|ARNOLD_2020_EXEC|
|Blenderu|blender.exe|BLENDER_2018_EXEC|

## <a name="azure-vm-families"></a>Rodiny virtuálních počítačů Azure

Stejně jako u jiných úloh se různé požadavky na vykreslování a požadavky na výkon pro úlohy a projekty liší.  V Azure je k dispozici velké množství rodin virtuálních počítačů v závislosti na vašich požadavcích – nejnižší náklady, Nejlepší cena/výkon, nejlepší výkon a tak dále.
Některé aplikace pro vykreslování, jako je například Arnold, jsou založené na procesoru. ostatní, například cykly V-Ray a V Blendu, můžou používat procesory nebo GPU.
Popis dostupných rodin virtuálních počítačů a velikostí virtuálních počítačů [najdete v tématu typy a velikosti virtuálních](../virtual-machines/sizes.md)počítačů.

## <a name="low-priority-vms"></a>Virtuální počítače s nízkou prioritou

Stejně jako u jiných úloh je možné virtuální počítače s nízkou prioritou využít ve fondech služby Batch pro vykreslování.  Virtuální počítače s nízkou prioritou fungují stejně jako běžné vyhrazené virtuální počítače, ale využívají nadbytečné kapacity Azure a jsou dostupné pro velkou slevu.  Kompromisy pro použití virtuálních počítačů s nízkou prioritou je, že tyto virtuální počítače nemusí být k dispozici, aby je bylo možné přidělit nebo kdykoli zrušit v závislosti na dostupné kapacitě. Z tohoto důvodu nebudou virtuální počítače s nízkou prioritou vhodné pro všechny úlohy vykreslování. Pokud například obrázky přestanou trvat mnoho hodin, je pravděpodobný, že se vykreslování těchto imagí přerušilo a restartuje kvůli tomu, že virtuální počítače nejsou v důsledku přerušení přijatelné.

Další informace o vlastnostech virtuálních počítačů s nízkou prioritou a různých způsobů jejich konfigurace pomocí služby Batch najdete v tématu [použití virtuálních počítačů s nízkou prioritou ve službě Batch](./batch-low-pri-vms.md).

## <a name="jobs-and-tasks"></a>Úlohy a úkoly

Pro úlohy a úlohy se nevyžaduje žádná podpora specifická pro vykreslování.  Hlavní položka konfigurace je příkazový řádek úkolu, který musí odkazovat na požadovanou aplikaci.
Při použití imagí virtuálních počítačů Azure Marketplace pak osvědčeným postupem použít proměnné prostředí k určení cesty a spustitelného souboru aplikace.

## <a name="next-steps"></a>Další kroky

Příklady dávkového vykreslování si můžete vyzkoušet v těchto dvou kurzech:

* [Vykreslování pomocí Azure CLI](./tutorial-rendering-cli.md)
* [Vykreslování scén s využitím Batch Exploreru](./tutorial-rendering-batchexplorer-blender.md)