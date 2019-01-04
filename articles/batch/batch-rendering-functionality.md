---
title: Azure Batch Rendering možnosti –
description: Možnosti vykreslování konkrétní ve službě Azure Batch
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: a1408720a5387d044416ded377189e4539f782a7
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543032"
---
# <a name="azure-batch-rendering-capabilities"></a>Možnosti vykreslování Azure Batch

Standardní možnosti služby Azure Batch se používají pro spouštění úloh vykreslování a aplikací. Batch také konkrétní funkce pro podporu úloh vykreslování.

Přehled koncepty služby Batch, včetně fondy, úlohy a úkoly, naleznete v tématu [v tomto článku](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Fondy služby batch

### <a name="rendering-application-installation"></a>Instalace aplikace vykreslování

Image virtuálního počítače Azure Marketplace vykreslování lze zadat v konfiguraci fondu, pokud jen pro předem nainstalované aplikace je třeba použít.

Je Windows 2016 image a CentOS image.  V [Azure Marketplace](https://azuremarketplace.microsoft.com), Image virtuálních počítačů lze najít hledáním 'batch rendering'.

Příklad konfigurace fondu, najdete v článku [rozhraní příkazového řádku Azure vykreslování kurzu](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  Webu Azure portal a Průzkumníka služby Batch nabízejí grafické uživatelské rozhraní nástroje pro výběr image virtuálního počítače s vykreslování při vytváření fondu.  Pokud používáte rozhraní API služby Batch, zadejte následující hodnoty vlastností pro [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) při vytváření fondu:

| Vydavatel | Nabídka | Skladová jednotka (SKU) | Verze |
|---------|---------|---------|--------|
| dávka | vykreslování centos73 | vykreslování | nejnovější |
| dávka | vykreslování windows2016 | vykreslování | nejnovější |

Další možnosti jsou k dispozici, pokud jsou vyžadovány další aplikace ve fondu virtuálních počítačů:

* Vlastní image na základě standardní image Marketplace:
  * Pomocí této možnosti můžete na virtuálním počítači nakonfigurovat konkrétní aplikace a verze, které požadujete. Další informace najdete v tématu [použití vlastní image k vytvoření fondu virtuálních počítačů](https://docs.microsoft.com/azure/batch/batch-custom-images). Autodesk a Chaos Group byly upraveny Arnold a V-Ray, má vyhodnotit proti služby Azure Batch licencování služby. Ujistěte se, že máte verze těchto aplikací s touto podporou, jinak licencování platbami za použití nebude fungovat. Aktuální verze sady aplikací Maya a 3ds Max nevyžadují licenční server při bezobslužném spouštění (v batch nebo příkazového řádku režim). Pokud si nejste jistí, jak pokračovat dál s tímto parametrem, kontaktujte podporu Azure.
* [Balíčky aplikací](https://docs.microsoft.com/azure/batch/batch-application-packages):
  * Soubory aplikace pomocí jednoho nebo více souborů ZIP balíčku, odeslat prostřednictvím webu Azure portal a vyberte balíček v konfiguraci fondu. Po vytvoření fondu virtuálních počítačů jsou soubory ZIP jsou stažené a rozbalené soubory.
* Soubory prostředků:
  * Soubory aplikace se nahrají do služby Azure blob storage a zadejte odkazy na soubory v [spouštěcí úkol fondu](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask). Při vytvoření fondu virtuálních počítačů, se stáhnou soubory prostředků na každém virtuálním počítači.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Placené využití licencí pro předem nainstalované aplikace

Aplikace, které se použije a máte licenční poplatek je potřeba zadat v konfiguraci fondu.

* Zadejte `applicationLicenses` vlastnost při [vytváření fondu](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  Tyto hodnoty se dá nastavit jako pole řetězců - "vray", "arnold", "3dsmax", "maya".
* Pokud chcete zadat jednu nebo více aplikací, náklady na tyto aplikace se přidá do náklady na virtuální počítače.  Aplikace ceny jsou uvedené na [stránce s cenami služby Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Pokud místo toho připojit k licenčnímu serveru používat aplikace vykreslování, nezadávejte `applicationLicenses` vlastnost.

Vyberte aplikace, a zobrazit ceny aplikace můžete použít Průzkumníka služby Batch na webu Azure portal.

Pokud je proveden pokus o použití aplikace, ale nebyl zadán v aplikaci `applicationLicenses` vlastnost konfigurace fondu nebo nemá není dosah licenčního serveru a pak je spuštění aplikace nezdaří a zobrazí se chyba licencování a nenulový ukončovací kód.

### <a name="environment-variables-for-pre-installed-applications"></a>Proměnné prostředí pro předem nainstalované aplikace

Aby bylo možné vytvořit příkazového řádku pro úlohy vykreslování, musí zadat umístění instalace spustitelné soubory aplikace vykreslování.  Proměnné prostředí systému byly vytvořeny na Image virtuálního počítače Azure Marketplace, které je možné použít místo by bylo nutné určit skutečné cesty.  Tyto proměnné prostředí jsou kromě [standardní proměnné prostředí služby Batch](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) vytvořena pro každou úlohu.

|Aplikace|Spustitelný soubor aplikace|Proměnná prostředí|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|Render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|Render.exe|MAYA_2018_EXEC|
|Chaos Group V-Ray samostatné|vray.exe|VRAY_3.60.4_EXEC|
Arnold 2017 příkazového řádku|kick.exe|ARNOLD_2017_EXEC|
|Arnold 2018 příkazového řádku|kick.exe|ARNOLD_2018_EXEC|
|Blenderu|Blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Azure rodiny virtuálních počítačů

Stejně jako u jiných úloh se požadavky na systém aplikace vykreslování se liší a požadavky na výkon se liší pro projekty a úlohy.  Širokou škálu řad virtuálních počítačů jsou dostupné v Azure podle vašich potřeb – nejnižší náklady, nejlepší poměr cena/výkon, nejlepší výkon a tak dále.
Některé aplikace vykreslování, jako je například Arnold, jsou založené na procesoru; jiné například V-Ray a blenderu cyklů můžete použít CPU nebo GPU.
Popis dostupných virtuálních počítačů řady a velikosti virtuálních počítačů [naleznete v tématu typy virtuálních počítačů a velikosti](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>Virtuální počítače s nízkou prioritou

Stejně jako u jiných úloh můžete používat virtuální počítače s nízkou prioritou ve fondech služby Batch pro vykreslování.  Virtuální počítače s nízkou prioritou provést stejný jako regulární vyhrazených virtuálních počítačích, ale využívají nadbytečnou kapacitu Azure a jsou k dispozici pro velké slevy.  Kompromis pro použití virtuálních počítačů s nízkou prioritou je, že tyto virtuální počítače nemusí být možné přidělit nebo může dojít ke zrušení v okamžiku, v závislosti na dostupné kapacity. Z tohoto důvodu nejsou virtuální počítače s nízkou prioritou má být vhodný pro všechny úlohy vykreslování. Například pokud bitové kopie trvat několik hodin k vykreslení, pak je pravděpodobné, vykreslování obrázků a odešle restartování z důvodu virtuální počítače se ke zrušení přidělením s nebude přijatelný.

Další informace o vlastnostech virtuálních počítačů s nízkou prioritou a různé způsoby, jak se konfigurují pomocí služby Batch, najdete v článku [používejte virtuální počítače s nízkou prioritou pomocí služby Batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Úlohy a úkoly

Žádná podpora vykreslování konkrétní je vyžadován pro úlohy a úkoly.  Hlavní konfigurační položky je příkazového řádku úkolu, který musí odkazovat na požadovanou aplikaci.
Pokud se používá Image virtuálního počítače Azure Marketplace, osvědčeným postupem je použití proměnných prostředí a zadat cestu a spustitelný soubor aplikace.

## <a name="next-steps"></a>Další postup

Pro příklady Batch rendering vyzkoušet dva kurzy:

* [Vykreslování pomocí Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Použití Průzkumníka služby Batch Rendering](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
