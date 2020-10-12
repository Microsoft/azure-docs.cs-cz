---
title: Přehled vykreslování
description: Seznámení s používáním Azure pro vykreslování a přehled možností vykreslování Azure Batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 9fac5d3efabc5d9f796c91d688f35e01aeefdca3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092758"
---
# <a name="rendering-using-azure"></a>Vykreslování s využitím Azure

Vykreslování je proces přebírání 3D modelů a jejich převod na 2D obrázky. 3D soubory scény jsou vytvořené v aplikacích, jako je například Autodesk 3ds Max, Autodesk Maya a Blend.  Vykreslování aplikací, jako jsou Autodesk Maya, Autodesk Arnold, chaos Group V-Ray a cykly Blendu, vytváří 2D obrázky.  Někdy se v souborech scény vytvoří jeden obrázek. Je však běžné modelování a vykreslování více imagí a jejich kombinování do animace.

Úlohy vykreslování se silně používají pro zvláštní efekty (VFX) v mediálním a zábavním odvětví. Vykreslování se používá i v mnoha dalších odvětvích, jako je reklama, maloobchodní prodej, ropný a plynárenský průmysl nebo výroba.

Proces vykreslování je výpočetně náročný; můžete vytvářet spoustu snímků a obrázků a každý z nich může vykreslování trvat spoustou hodin.  Vykreslování je proto ideální úlohou dávkového zpracování, která může využít Azure a Azure Batch k paralelnímu spuštění mnoha vykreslování.

## <a name="why-use-azure-for-rendering"></a>Proč používat Azure pro vykreslování?

Z mnoha důvodů je vykreslování úlohou naprosto vhodné pro Azure a Azure Batch:

* Úlohy vykreslování lze rozdělit do mnoha částí, které lze spustit paralelně pomocí několika virtuálních počítačů:
  * Animace se skládají z mnoha snímků a každý rámec lze vykreslit paralelně.  Čím víc virtuálních počítačů je dostupných pro zpracování každého snímku, tím rychleji se vytvoří všechny snímky a animace.
  * Některé vykreslovací software umožňuje rozdělit jednotlivé snímky do více částí, jako jsou například dlaždice nebo řezy.  Každý kus se dá vykreslit samostatně a pak se sloučí do finální image, až se všechny díly dokončí.  Čím více virtuálních počítačů je k dispozici, tím rychlejší je možné vykreslení rámce.
* Vykreslování projektů může vyžadovat obrovský rozsah:
  * Jednotlivé snímky můžou být složité a vyžadují mnoho hodin pro vykreslování, i na špičkovém hardwaru. animace se můžou skládat ze stovky tisíc snímků.  K vykreslování vysoce kvalitních animací v rozumné době je potřeba velké množství výpočetních prostředků.  V některých případech se více než 100 000 jader používá k vykreslování tisíců snímků paralelně.
* Projekty vykreslování jsou založené na projektu a vyžadují proměnlivé množství výpočetních prostředků:
  * Přidělte výpočetní a úložnou kapacitu v případě potřeby, škálovat ji nahoru nebo dolů v závislosti na zatížení během projektu a po dokončení projektu ho odeberte.
  * Platíte za kapacitu při přidělení, ale Neplaťte za ni, pokud není žádné zatížení, například mezi projekty.
  * Stravování pro shluky kvůli neočekávaným změnám škálování vyšší, pokud v projektu nedošlo k neočekávaným změnám, a tyto změny je potřeba zpracovat v těsném plánu.
* Vyberte si z nejrůznějších hardwarových zařízení podle aplikace, úlohy a časového rámce:
  * V Azure je dostupná celá síť, kterou je možné přidělit a spravovat pomocí služby Batch.
  * V závislosti na projektu může požadavek vycházet z hlediska nejvyšší ceny a výkonu nebo nejlepšího celkového výkonu.  Různé scény nebo vykreslování aplikací budou mít různé požadavky na paměť.  Některé aplikace pro vykreslování můžou využít GPU k dosažení nejlepšího výkonu nebo určitých funkcí. 
* Virtuální počítače s nízkou prioritou snižují náklady:
  * Virtuální počítače s nízkou prioritou jsou k dispozici pro velkou slevu v porovnání s běžnými virtuálními počítači na vyžádání a jsou vhodné pro některé typy úloh.
  * Virtuální počítače s nízkou prioritou je možné přidělit pomocí Azure Batch a služba Batch poskytuje flexibilitu způsobu jejich použití pro širokou škálu požadavků.  Fondy Batch se můžou skládat z virtuálních počítačů s nízkou prioritou, ale díky tomu je možné kdykoli změnit kombinaci typů virtuálních počítačů.

## <a name="options-for-rendering-on-azure"></a>Možnosti pro vykreslování v Azure

K dispozici je řada možností Azure, které se dají použít pro vykreslování úloh.  Jaké funkce budou použity, závisí na jakémkoli existujícím prostředí a požadavcích.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Existující místní vykreslovací prostředí pomocí aplikace pro správu vykreslování

Nejběžnějším případem je, že se má jednat o existující místní farmu vykreslování, kterou spravuje aplikace pro správu vykreslování, jako je PipelineFX Qube, Královská Render nebo Thinkbox konečný termín.  Požadavek slouží k prodloužení kapacity místní vykreslovací farmy pomocí virtuálních počítačů Azure.

Software pro správu vykreslování buď má vestavěnou podporu Azure, nebo zpřístupňuje dostupné moduly plug-in, které přidávají podporu Azure. Další informace o podporovaných správcech a funkcích vykreslování najdete v článku o [používání správců vykreslování](./batch-rendering-render-managers.md).

### <a name="custom-rendering-workflow"></a>Pracovní postup vlastního vykreslování

Požadavek platí pro virtuální počítače, které by rozšířily existující farmu vykreslování.  Fondy Azure Batch můžou přidělovat velký počet virtuálních počítačů, umožnit používání virtuálních počítačů s nízkou prioritou a dynamicky se automaticky škálovat s využitím virtuálních počítačů s plnými cenami a poskytovat licencování s platbami za použití pro oblíbené aplikace pro vykreslování.

### <a name="no-existing-render-farm"></a>Žádná existující farma vykreslování

Klientské pracovní stanice mohou provádět vykreslování, ale úlohy vykreslování se zvyšují a trvá příliš dlouho, aby používala kapacitu pracovní stanice.  Azure Batch lze použít k přidělení výpočetní kapacity farmy vykreslování na vyžádání i k naplánování úloh vykreslování do farmy Azure rendering.

## <a name="azure-batch-rendering-capabilities"></a>Možnosti vykreslování Azure Batch

Azure Batch umožňuje spouštění paralelních úloh v Azure.  Umožňuje vytváření a správu velkého počtu virtuálních počítačů, na kterých jsou aplikace nainstalované a spuštěné.  Poskytuje taky ucelené možnosti plánování úloh, které spouštějí instance těchto aplikací, a poskytuje přiřazení úkolů virtuálním počítačům, řazení a monitorování aplikací atd.

Azure Batch se používá pro mnoho úloh, ale k dispozici jsou následující možnosti, které usnadňují a urychlují spouštění úloh vykreslování.

* Image virtuálních počítačů s předem nainstalovanými grafickými a vykreslovacími aplikacemi:
  * K dispozici jsou image virtuálních počítačů s Azure Marketplace, které obsahují oblíbené grafické a vykreslovací aplikace, a zabraňují tak nutnosti instalovat aplikace sami nebo vytvářet vlastní image s nainstalovanými aplikacemi. 
* Licencování s platbami za použití pro vykreslování aplikací:
  * Můžete se rozhodnout platit za aplikace po minutách, kromě toho, že platíte za výpočetní virtuální počítače, což předchází nutnosti koupit licence a případně nakonfigurovat licenční server pro aplikace.  Výplata za použití také znamená, že je možné využít možnost stravování pro různé a neočekávané zatížení, protože se nejedná o pevný počet licencí.
  * Je také možné použít předem nainstalované aplikace s vlastními licencemi a nepoužívat licencování s platbami za použití. K tomu obvykle nainstalujete místní nebo server licencí založený na Azure a pomocí virtuální sítě Azure připojte fond vykreslování k licenčnímu serveru.
* Moduly plug-in pro aplikace návrhu a modelování klienta:
  * Moduly plug-in umožňují koncovým uživatelům využívat Azure Batch přímo z klientských aplikací, jako je Autodesk Maya, jejich povolení vytvářet fondy, odesílat úlohy a využívat větší výpočetní kapacitu k rychlejšímu vykreslení.
* Integrace správce vykreslování:
  * Azure Batch je integrována do aplikací pro správu vykreslování a moduly plug-in jsou k dispozici pro zajištění Azure Batch integrace.

Existuje několik způsobů, jak použít Azure Batch, všechny, které platí také pro Azure Batch vykreslování.

* Rozhraní API:
  * Pište kód pomocí rozhraní [REST](/rest/api/batchservice), [.NET](/dotnet/api/overview/azure/batch), [Python](/python/api/overview/azure/batch), [Java](/java/api/overview/azure/batch)nebo dalších podporovaných rozhraní API.  Vývojáři mohou integrovat Azure Batch možností do svých stávajících aplikací nebo pracovních postupů, ať už v cloudu, nebo na základě místního prostředí.  Například [modul plug-in Autodesk Maya](https://github.com/Azure/azure-batch-maya) využívá rozhraní API služby Batch pro Python k vyvolání služby Batch, vytváření a správě fondů, odesílání úloh a úloh a stav monitorování.
* Nástroje příkazového řádku:
  * Ke skriptování dávkového použití lze použít [příkazový řádek Azure](/cli/azure/) nebo [Azure PowerShell](/powershell/azure/) .
  * Konkrétně Podpora šablon Batch CLI zjednodušuje vytváření fondů a odesílání úloh.
* Uživatelská rozhraní
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) je klientský nástroj pro různé platformy, který umožňuje spravovat a monitorovat účty Batch, ale poskytuje některé bohatší funkce v porovnání s uživatelským rozhraním Azure Portal.  K dispozici je sada šablon fondů a úloh, které jsou upraveny pro každou podporovanou aplikaci a lze je použít ke snadnému vytváření fondů a k odesílání úloh.
  * Azure Portal lze použít ke správě a monitorování Azure Batch.
* Modul plug-in klientské aplikace:
  * K dispozici jsou moduly plug-in, které umožňují použití dávkového vykreslování přímo v rámci aplikace návrh a modelování klienta. Moduly plug-in hlavně vyvolají aplikaci Batch Explorer s kontextové informace o aktuálním 3D model.
  * K dispozici jsou následující moduly plug-in:
    * [Azure Batch pro Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blenderu](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Začínáme s vykreslováním Azure Batch

V následujících úvodních kurzech se můžete pokusit Azure Batch vykreslování:

* [Vykreslení scény v Blendu pomocí Batch Explorer](./tutorial-rendering-batchexplorer-blender.md)
* [Použití dávkového rozhraní příkazového řádku pro vykreslení maximální scény aplikace Autodesk 3ds](./tutorial-rendering-cli.md)

## <a name="next-steps"></a>Další kroky

Určení seznamu aplikací a verzí pro vykreslování obsažených v Azure Marketplace imagí virtuálních počítačů v [tomto článku](./batch-rendering-applications.md).
