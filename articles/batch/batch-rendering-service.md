---
title: Přehled vykreslování – Azure Batch
description: Zavedení používání Azure pro vykreslování a přehled možností vykreslování Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d4423b22c4c8afea5afa9c7040e081665b17ba87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60774025"
---
# <a name="rendering-using-azure"></a>Vykreslování s využitím Azure

Vykreslování je proces pořizování 3D modelů a jejich převedení na 2D obrazy. Soubory 3D scény jsou vytvářeny v aplikacích, jako jsou Autodesk 3ds Max, Autodesk Maya a Blender.  Vykreslovací aplikace jako Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray a Blender Cycles vytvářejí 2D obrazy.  Někdy jsou ze souborů scény vytvořeny jednotlivé obrázky. Je však běžné modelovat a vykreslovat více obrazů a pak je kombinovat v animaci.

Úloha vykreslování se používá pro speciální efekty (VFX) v mediálním a zábavním průmyslu. Vykreslování se používá i v mnoha dalších odvětvích, jako je reklama, maloobchodní prodej, ropný a plynárenský průmysl nebo výroba.

Proces vykreslování je výpočtově náročný; může být mnoho snímků / obrázků k vytvoření a každý obrázek může trvat mnoho hodin k vykreslení.  Vykreslování je tedy perfektní úlohy dávkového zpracování, které můžete využít Azure a Azure Batch ke spuštění mnoha vykreslení paralelně.

## <a name="why-use-azure-for-rendering"></a>Proč používat Azure pro vykreslování?

Z mnoha důvodů je vykreslování úlohou, která je ideální pro Azure a Azure Batch:

* Úlohy vykreslování lze rozdělit na mnoho částí, které lze spustit paralelně pomocí více virtuálních počítačů:
  * Animace se skládají z mnoha snímků a každý snímek lze vykreslit paralelně.  Čím více virtuálních počítačů je k dispozici pro zpracování každého snímku, tím rychleji lze vytvořit všechny snímky a animaci.
  * Některý vykreslovací software umožňuje rozdělit jednotlivé snímky na více částí, například dlaždice nebo řezy.  Každý kus může být vykreslen samostatně, pak zkombinován do konečného obrazu, když jsou všechny kusy dokončeny.  Čím více virtuálních počítačů je k dispozici, tím rychleji lze snímek vykreslit.
* Vykreslovací projekty mohou vyžadovat obrovské měřítko:
  * Jednotlivé rámce mohou být složité a k vykreslení vyžadují mnoho hodin, a to i na špičkovém hardwaru; animace se mohou skládat ze stovek tisíc snímků.  K vykreslení vysoce kvalitních animací v rozumném čase je zapotřebí obrovské množství výpočetních prostředků.  V některých případech bylo k paralelnímu vykreslení tisíců snímků použito více než 100 000 jader.
* Vykreslovací projekty jsou založené na projektu a vyžadují různé množství výpočetních prostředků:
  * Přidělit výpočetní kapacitu a kapacitu úložiště v případě potřeby, škálovat nahoru nebo dolů podle zatížení během projektu a odebrat po dokončení projektu.
  * Platit za kapacitu při přidělení, ale neplatit za to, když není žádné zatížení, například mezi projekty.
  * Uspokojte výbuchy v důsledku neočekávaných změn; škálovat vyšší, pokud dojde k neočekávaným změnám pozdě v projektu a tyto změny je třeba zpracovat podle plánu.
* Vyberte si z široké nabídky hardwaru podle aplikace, pracovního vytížení a časového rámce:
  * V Azure je k dispozici široký výběr hardwaru, který se dá přidělit a spravovat pomocí Batch.
  * V závislosti na projektu může být požadavek na nejlepší cenu / výkon nebo nejlepší celkový výkon.  Různé scény nebo vykreslovací aplikace budou mít různé požadavky na paměť.  Některé vykreslovací aplikace mohou využívat grafické procesory pro nejlepší výkon nebo určité funkce. 
* Virtuální virtuální maři s nízkou prioritou snižují náklady:
  * Virtuální počítače s nízkou prioritou jsou k dispozici s velkou slevou ve srovnání s běžnými virtuálními počítači na vyžádání a jsou vhodné pro některé typy úloh.
  * Virtuální počítače s nízkou prioritou mohou přidělit Azure Batch, přičemž Batch poskytuje flexibilitu ohledně toho, jak se používají k zajištění široké sady požadavků.  Dávkové fondy se mohou skládat z vyhrazených virtuálních počítačů i virtuálních počítačů s nízkou prioritou, přičemž je možné kdykoli změnit kombinaci typů virtuálních počítačů.

## <a name="options-for-rendering-on-azure"></a>Možnosti vykreslování v Azure

Existuje celá řada funkcí Azure, které lze použít pro vykreslování úloh.  Které funkce, které chcete použít, závisí na všech existujících prostředích a požadavcích.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Existující místní vykreslování prostředí pomocí aplikace pro správu vykreslení

Nejběžnější případ je pro existující místní vykreslovací farmy spravované aplikace pro správu vykreslení, jako je PipelineFX Qube, Royal Render nebo Thinkbox termín.  Požadavkem je rozšíření místní kapacity vykreslovací farmy pomocí virtuálních počítačích Azure.

Software pro správu vykreslení má buď integrovanou podporu Azure, nebo zpřístupníme moduly plug-in, které přidávají podporu Azure. Další informace o podporovaných správcích vykreslení a povolených funkcích naleznete v článku o [použití správců vykreslení](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers).

### <a name="custom-rendering-workflow"></a>Vlastní pracovní postup vykreslování

Požadavek je pro virtuální chod rozšířit existující vykreslovací farmy.  Fondy Azure Batch můžou přidělit velký počet virtuálních počítačů, povolit použití virtuálních počítače s nízkou prioritou a dynamické automatické škálování pomocí virtuálních počítačů s plnou cenou a poskytovat licence pro platby za použití pro oblíbené vykreslovací aplikace.

### <a name="no-existing-render-farm"></a>Žádná existující vykreslovatete farma

Klientské pracovní stanice mohou provádět vykreslování, ale zatížení vykreslování se zvyšuje a trvá příliš dlouho, než se použije pouze kapacita pracovní stanice.  Azure Batch se dá použít k přidělení výpočetních prostředků vykreslování farmy na vyžádání a také k plánování vykreslení úloh do vykreslovací farmy Azure.

## <a name="azure-batch-rendering-capabilities"></a>Možnosti vykreslování Azure Batch

Azure Batch umožňuje paralelní úlohy, které mají být spuštěny v Azure.  Umožňuje vytváření a správu velkého počtu virtuálních počítačů, na kterých jsou aplikace nainstalovány a spouštěny.  Poskytuje také komplexní možnosti plánování úloh pro spouštění instancí těchto aplikací, které poskytují přiřazení úkolů virtuálním počítačům, řazení do fronty, monitorování aplikací a tak dále.

Azure Batch se používá pro mnoho úloh, ale následující funkce jsou k dispozici speciálně usnadnit a urychlit spouštění úloh vykreslování.

* Image virtuálních obrazovek s předinstalovanými grafickými a vykreslovacími aplikacemi:
  * Image virtuálních počítačů Azure Marketplace jsou k dispozici, které obsahují oblíbené grafické a vykreslovací aplikace, aniž by bylo nutné instalovat aplikace sami nebo vytvářet vlastní image s nainstalovanými aplikacemi. 
* Licencování za použití pro vykreslovací aplikace:
  * Můžete se rozhodnout platit za aplikace po minutách, kromě placení za výpočetní virtuální počítače, což zabrání nutnosti kupovat licence a potenciálně nakonfigurovat licenční server pro aplikace.  Placení za použití také znamená, že je možné uspokojit různé a neočekávané zatížení, protože neexistuje pevný počet licencí.
  * Je také možné používat předinstalované aplikace s vlastními licencemi a nepoužívat licence na platbu za použití. Chcete-li to provést, obvykle nainstalujete místní nebo licenční server založený na Azure a pomocí virtuální sítě Azure pro připojení fondu vykreslování k licenčnímu serveru.
* Moduly plug-in pro návrh a modelování klientských aplikací:
  * Moduly plug-in umožňují koncovým uživatelům využívat Azure Batch přímo z klientské aplikace, jako je například Autodesk Maya, což jim umožňuje vytvářet fondy, odesílat úlohy a využívat větší výpočetní kapacitu k rychlejšímu vykreslení.
* Integrace správce vykreslení:
  * Azure Batch je integrovaná do aplikací pro správu vykreslování nebo moduly plug-in jsou k dispozici pro poskytování integrace Azure Batch.

Existuje několik způsobů, jak používat Azure Batch, které platí také pro vykreslování Azure Batch.

* Rozhraní API:
  * Napište kód pomocí [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch), [Python](https://docs.microsoft.com/python/api/overview/azure/batch), [Java](https://docs.microsoft.com/java/api/overview/azure/batch)nebo jiných podporovaných rozhraní API.  Vývojáři můžou integrovat funkce Azure Batch do svých stávajících aplikací nebo pracovních postupů, ať už cloudových nebo založených na místním prostředí.  [Například modul plug-in Autodesk Maya](https://github.com/Azure/azure-batch-maya) využívá rozhraní API dávky pythonu k vyvolání dávky, vytváření a správě fondů, odesílání úloh a úkolů a stavu monitorování.
* Nástroje příkazového řádku:
  * [Příkazový řádek Azure](https://docs.microsoft.com/cli/azure/) nebo [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) se dá použít k skriptování dávkového použití.
  * Podpora šablony dávkového uživatelského nastavení zejména usnadňuje vytváření fondů a odesílání úloh.
* Uis:
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) je klientský nástroj pro různé platformy, který také umožňuje spravovat a monitorovat dávkové účty, ale poskytuje některé bohatší funkce ve srovnání s uživatelskérozhraní portálu Azure.  Sada fondů a šablon úloh jsou k dispozici, které jsou přizpůsobeny pro každou podporovanou aplikaci a lze je použít ke snadnému vytváření fondů a odesílání úloh.
  * Portál Azure se dá použít ke správě a monitorování Azure Batch.
* Plug-in klientských aplikací:
  * K dispozici jsou moduly plug-in, které umožňují použití dávkového vykreslování přímo v aplikacích návrhu a modelování klienta. Moduly plug-in vyvolávají především aplikaci Batch Explorer s kontextovými informacemi o aktuálním 3D modelu.
  * K dispozici jsou následující moduly plug-in:
    * [Azure Batch pro Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Mixér](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Začínáme s vykreslováním Azure Batch

Podívejte se na následující úvodní kurzy, které vyzkoušejte vykreslování Azure Batch:

* [Vykreslení scény Blenderu pomocí Dávkového průzkumníka](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Vykreslení scény Autodesk 3ds Max pomocí souboru cli dávky](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>Další kroky

Určete seznam vykreslovacích aplikací a verzí zahrnutých v ibi virtuálních počítačích Azure Marketplace v [tomto článku](https://docs.microsoft.com/azure/batch/batch-rendering-applications).