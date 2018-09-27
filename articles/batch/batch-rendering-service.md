---
title: Přehled vykreslování Azure Batch
description: Úvod pro vykreslování a základní informace o možnosti vykreslování v Azure Batch pomocí Azure
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: fc26e1d32332bb0ed9624b7442e38ea79b7bfb1d
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393549"
---
# <a name="rendering-using-azure"></a>Vykreslování s využitím Azure

Vykreslování je proces trvá 3D modely a převod na 2D imagí. 3D Scéna soubory jsou vytvořeny v aplikací, jako jsou Autodesk 3ds Max, Autodesk Maya a Blender.  Vykreslování aplikací, jako jsou Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray a cykly blenderu pro vytváření 2D imagí.  Vytváření imagí někdy jeden ze souborů scény. Je však společné pro modelování a vykreslení více bitových kopií a pak je zkombinovat animace.

Úlohy vykreslování se často používá pro zvláštních efektů (vizuálních EFEKTŮ) v odvětví média a zábava. Vykreslování se používá také v mnoha industires například reklamy, maloobchodního prodeje, ropy a plynu a výroby.

Proces vykreslování je výpočetně náročný; může být mnoho snímků a imagí k vytvoření a každá image může trvat několik hodin k vykreslení.  Vykreslování je proto ideální batch úlohy zpracování, který můžete využít Azure a Azure Batch k paralelnímu spouštění mnoha vykreslení.

## <a name="why-use-azure-for-rendering"></a>Proč používat Azure pro vykreslování?

Pro mnoho důvodů, proč vykreslování je úloha dokonale hodí pro Azure a Azure Batch:

* Vykreslení úlohy je možné rozdělit na mnoho částí, které můžete spustit paralelně několik virtuálních počítačů pomocí:
  * Animace obsahovat počet snímků a každém snímku lze vykreslit paralelně.  Další virtuální počítače k dispozici pro každý snímek, tím rychleji všechny snímky a animaci lze vytvořit proces.
  * Některé softwary vykreslování umožňuje jeden rámce, které se rozdělit do několika částí, například dlaždice nebo kolekce obsahuje nějaké řezy.  Jednotlivé komponenty můžete vykreslen samostatně a pak zkombinovány do finální bitové kopie po dokončení všech částí.  Další virtuální počítače, které jsou k dispozici, tím rychleji můžete být vykreslen blok.
* Vykreslování projekty mohou vyžadovat velkou škálovací:
  * Jednotlivé snímky může být složité a vyžadují mnoho hodin k vykreslení, dokonce i na hardware vyšší kategorie. animace se může skládat ze stovek tisíc snímků.  Velký objem výpočtů, je potřeba vykreslování animací vysoce kvalitní v rozumném čase.  V některých případech se používají k vykreslení tisíce snímků paralelně více než 100 000 jader.
* Vykreslování projekty jsou založeny na projekt a vyžadují různé množství výpočetního výkonu:
  * Přidělit kapacitu výpočetních prostředků a úložiště v případě potřeby vertikálně navýšit nebo snížit kapacitu podle zatížení v průběhu projektu a odebrat po dokončení projektu.
  * Platit za kapacitu při přidělování, ale za to, pokud neexistuje žádné zatížení, například mezi projekty.
  * Postarejte nárůstech kvůli neočekávaným změnám; škálování vyšší, pokud jsou neočekávané změny v projektu, které je potřeba zpracovat v těsné plánu.
* Vyberte z široké škály hardwaru podle aplikací, úloh a časový rámec:
  * Není k dispozici v Azure, která je přidělena a spravovat pomocí služby Batch široké škály hardwaru.
  * V závislosti na projektu může být požadavek pro nejlepší ceny a výkonu nebo nejlepší výkon.  Jednotlivé scény a/nebo vykreslovací aplikace bude mít požadavky na různých paměť.  Některé aplikace vykreslování můžete využít GPU pro zajištění nejlepšího výkonu ani určité funkce. 
* Virtuální počítače s nízkou prioritou snížení nákladů:
  * Virtuální počítače s nízkou prioritou jsou dostupné pro velké sleva ve srovnání s běžnými virtuálními počítači na vyžádání a jsou vhodné pro některé typy úloh.
  * Virtuální počítače s nízkou prioritou je možné přidělit službou Azure Batch pomocí služby Batch poskytuje flexibilitu při jejich použití jako pravidlo pro široké spektrum požadavků.  Fondy služby batch se může skládat z vyhrazené a s nízkou prioritou virtuální počítače s ním je možné kdykoli změnit kombinaci různých typů virtuálních počítačů.

## <a name="options-for-rendering-on-azure"></a>Možnosti pro vykreslování v Azure

Existují možnosti oblast Azure, které lze použít pro úlohy vykreslování.  Jaké schopnosti použití závisí na všechny stávající prostředí a požadavky.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Existující místní prostředí vykreslování pomocí aplikace pro správu vykreslení

Nejběžnější případ je existuje jako existující místní vykreslení farmy spravované pomocí aplikace pro správu vykreslovacích například PipelineFX Qube, Royal vykreslování nebo Thinkbox termínu.  Tento požadavek je rozšířit místní vykreslovací farmy kapacitu pomocí virtuálních počítačů Azure.

Software pro správu vykreslovacích buď má integrovanou podporu Azure nebo Usnadňujeme dostupných modulů plug-in, které přidávají podporu Azure. Pro další informace o podporovaných vykreslení manažerů a funkce povolená, najdete v článku [pomocí vykreslení správci](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers).

### <a name="custom-rendering-workflow"></a>Vlastní vykreslení pracovního postupu

Požadavek je pro virtuální počítače, které rozšiřují existující vykreslovací farmě.  Fondy Azure Batch můžete přidělit velký počet virtuálních počítačů, mohly virtuální počítače s nízkou prioritou se používá a dynamicky automatické škálování pomocí úplné ceny virtuálních počítačů a poskytnout placené využití licencí pro oblíbené vykreslovacími aplikacemi.

### <a name="no-existing-render-farm"></a>Žádné existující farmě vykreslení

Klientské pracovní stanice může být provádění vykreslování, ale zvýšení úlohy vykreslování a to trvá moc dlouho výhradně používat pracovní stanici kapacity.  Služba Azure Batch umožňuje přidělit vykreslovací farmy výpočetní prostředky na vyžádání i plánování úloh vykreslování do Azure vykreslovací farmě.

## <a name="azure-batch-rendering-capabilities"></a>Možnosti vykreslování Azure Batch

Azure Batch umožňuje paralelní úlohy ke spuštění v Azure.  Umožňuje vytváření a správu velkého počtu virtuálních počítačů, na které se instalují a spouštějí aplikace.  Poskytuje také komplexní možnosti k používání instancí těchto aplikací poskytuje přiřazování úkolů k virtuálním počítačům, řazení do fronty, monitorování aplikací a tak dále pro plánování úloh.

Služba Azure Batch se používá pro mnoho úloh, ale jsou dostupné následující možnosti pro konkrétně umožňují snadněji a rychleji vykreslovací zátěž spustit.

* Image virtuálních počítačů s předinstalovanou grafické a vykreslovací aplikace:
  * Jsou dostupné Image Azure Marketplace virtuálních počítačů, které obsahují oblíbených grafické a vykreslovací aplikace, takže není nutné instalovat aplikace, nebo vytvořit vlastní Image s aplikacemi nainstalovat. 
* Platba za použití licencování pro aplikace vykreslování:
  * Můžete platit pro aplikace se po minutách, kromě placení za výpočetní virtuální počítače, který zabraňuje by bylo nutné koupit licence a potenciálně nakonfigurujte licenční server v aplikacích.  Platíte za použití také znamená, že je možné, aby vyhovovaly pro různé a neočekávané zatížení, protože není pevný počet licencí.
  * Je také možné použít předem nainstalované aplikace s vlastní licencí a nepoužívat platbami za použití licencování. K tomu obvykle instalujete na místním nebo založené na Azure licenci serveru a použít pro připojení fondu pro vykreslování k licenčnímu serveru služby Azure virtual network.
* Moduly plug-in pro klienta návrh a modelování aplikace:
  * Povolit moduly plug-in koncovým uživatelům využívat služby Azure Batch přímo z klientské aplikace, jako jsou Autodesk Maya, což jim umožňuje vytvářet fondy, úlohy a ujistěte se, použít větší výpočetní kapacitu provádět rychlejší vykreslení.
* Vykreslení integrace správce:
  * Služba Azure Batch je integrovaná do aplikace pro správu vykreslovacích nebo moduly plug-in jsou k dispozici pro zajištění integrace služby Azure Batch.

Existuje několik způsobů, jak používat službu Azure Batch, které platí také pro Azure Batch rendering.

* Rozhraní API:
  * Pište kód pomocí [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch), [Python](https://docs.microsoft.com/python/api/overview/azure/batch), [Java](https://docs.microsoft.com/java/api/overview/azure/batch), nebo jiné podporované rozhraní API.  Vývojáři mohou možnosti služby Azure Batch začlenit do svých stávajících aplikací nebo pracovního postupu, určuje, zda založené na místních i cloudových.  Například [modulu plug-in Autodesk Maya](https://github.com/Azure/azure-batch-maya) využívá rozhraní Python API služby Batch k vyvolání služby Batch, vytváření a správě fondů, odesílání úlohy a úkoly a sledování stavu.
* Nástroje příkazového řádku:
  * [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/) nebo [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) je možné skript použití služby Batch.
  * Konkrétně se podpora šablon rozhraní příkazového řádku služby Batch usnadňuje mnohem vytvořit fondy a odesílání úloh.
* Uživatelská rozhraní:
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) je multiplatformní klientský nástroj, který také umožňuje účtů služby Batch, které chcete spravovat a sledovat, ale poskytuje některé širší možnosti ve srovnání s na webu Azure portal uživatelského rozhraní.  Sadu šablon pro fond a úlohu se za předpokladu, že jsou přizpůsobené pro každou podporovanou aplikaci a je možné snadno vytvořit fondy a odesílání úloh.
  * Na webu Azure portal můžete použít ke správě a monitorování služby Azure Batch.
* Klienta aplikace plug v společnosti:
  * Moduly plug-in jsou k dispozici, která umožňují Batch rendering pro použití přímo v rámci klienta návrhu a modelování aplikace. Moduly plug-in hlavně vyvolat aplikaci služby Batch Explorer kontextové informace o aktuální 3D modelu.
  * Následující moduly plug-in jsou k dispozici:
    * [Azure Batch pro Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blenderu](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Začínáme se službou Azure Batch rendering

V následujících kurzech úvodní vyzkoušet Azure Batch rendering:

* [Vykreslení scény s blendrem. Tato pomocí Průzkumníka služby Batch](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Rozhraní příkazového řádku služby Batch použít k vykreslení scény Autodesk 3ds Max](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>Další postup

Určení seznamu aplikací vykreslování a verzí na Image virtuálního počítače Azure Marketplace v [v tomto článku](https://docs.microsoft.com/azure/batch/batch-rendering-applications).