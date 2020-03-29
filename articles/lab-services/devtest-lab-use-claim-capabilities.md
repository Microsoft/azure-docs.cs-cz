---
title: Použití funkcí deklarací v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Informace o různých scénářích pro použití funkcí deklarace nebo nenárokování v azure devtest labs
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: a15148260bccadc59966c86031100f0e0332b0f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67861427"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Použití funkcí deklarací v laboratořích Azure DevTest Labs
Služba Azure DevTest Labs zvyšuje efektivitu a efektivitu vývojářů a testerů. Tento článek se zaměřuje na možnost deklarace nebo unclaim virtuálních počítačů v Azure DevTest Labs. Obsahuje také seznam různých způsobů, jak tato funkce zlepšuje uživatelské prostředí. Než se podíváte na různé scénáře, kde lze tuto funkci použít, podívejme se na to, co je **deklarace a** jak funguje.

## <a name="claimable-machines"></a>Nárokovatelné stroje
Nárokovatelný počítač je virtuální počítač (VM), který se vytvořil v testovacím prostředí bez vlastníka. Jakmile je počítač nárokován, uživatel má celou řadu možností pro tento virtuální počítač. Když uživatel nárokuje počítač, je provedeno několik změn. Virtuální počítač se přesune ze seznamu **nárokovatelných virtuálních počítačů** do seznamu **Moje virtuální počítače** na webu Azure Portal. 

Uživatel se může připojit k virtuálnímu počítači, přizpůsobit artefakty, restartovat, zastavit nebo zrušit nárok na počítač. Existuje několik způsobů, jak vytvořit nárokvirtuální ho virtuálního mísu:

- Vytvořte počítač a zrušit jeho nárok tak, aby se přesune do nárokovatelného fondu. 
- Vytvořte virtuální virtuální počítače a umístěte se do sdíleného fondu pomocí [upřesňujících nastavení](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Existují dva případy, kdy lze efektivně využít možnosti deklarace pohledávky nebo zrušení deklarace. První případ vyžaduje více prozíravosti a plánování, které mají být navrženy a provedeny správně. A druhá je více situační. Následují některé příklady různých případů.

## <a name="designed-use-of-claimable-machines"></a>Navržené použití reklamovatelných strojů

- **Vývoj / testování softwaru:** Umožněte vývojářům nebo testerům zvýšit produktivitu díky konfiguraci počítačů připravených a v nevyzvednutém stavu. S sadou virtuálních počítačů s různými konfiguracemi, potřebné nástroje a s nejnovějšíkód umožňuje uživatelům nárokovat virtuální počítač a začít pracovat, aniž by museli trávit čas nastavit počítač. Před nárokování virtuálních počítačů jsou zřízeny počítače, ale jsou vypnutí minimalizace nákladů na mít počítače, které se používají méně často. Když jsou potřeba virtuální počítače, uživatel jednoduše nárokuje virtuální počítač, který spustí počítač. Možnost unclaim není v tomto případě tak užitečná, protože vytvoření nového virtuálního virtuálního aplikace je často jednodušší a levnější.
- **Učebna/labs:** Mít virtuální počítače předem nakonfigurované pro třídu nebo testovací prostředí tak, aby studenti okamžitě připojit k počítači pomocí portálu Azure.  Jakmile student nárokuje virtuální počítač, testovací prostředí zajistí, že nikdo nemůže nárokovat stejný počítač. Automatizace tohoto procesu zajišťuje, že je k dispozici požadovaný počet počítačů se zadaným prostředím. Pokud se studenti neukážou nebo mají zpoždění, mohou být nevyzvednuté stroje k dispozici, dokud relace neskončí s minimálními náklady. Možnost unclaim není v tomto scénáři tak efektivní, protože virtuální počítače jsou v neznámém stavu, když je předchozí uživatel hotový.
- **Ukázky:** Používejte stroje pro ukázky, kde jsou stroje v laboratoři nastaveny s konkrétními prostředími. Tato funkce je užitečná, pokud více lidí může být předvádění ve stejnou dobu nebo v náhodných časech, například na konferenci. Možnost unclaim může být užitečné v této situaci jako ukázka by neměla změnit stav počítače, umožňuje uživatelům vrátit virtuální počítač zpět do nárokovatelné fondu pro další demonstraci. S nevyzvednutý počítač je de-zřízené a vznikají minimální náklady, virtuální počítače mohou být ponechány v testovacím prostředí pro delší časové období.
- **Dočasní/smluvní pracovníci:** Umožněte uživatelům používat počítač. Když odejdou, vrátí virtuální ho do fondu nárokovatelné bez ztráty dat. S nevyzvednutým virtuálním počítačem může jiný uživatel nárokovat virtuální počítač a pokračovat nebo zkontrolovat počítač pro další informace.
- **Obecně:** Možnost mít jediný zdroj automaticky konfigurovat a nasazovat virtuální počítače, na konkrétní kadence, je užitečné v mnoha různých situacích. Existuje několik různých situací, kdy funkce deklarace nebo nenárokování pomáhá uživatelům efektivnější tím, že má automatizovaný proces k sestavení virtuálních počítačů v nevyzvednutém stavu s nastavenou konfigurací. Konfigurace mohou zahrnovat různé operační systémy, jazyky, disky nebo [jiný software (artefakty)](devtest-lab-artifact-author.md) v závislosti na vašich potřebách. Možnost nárokovat virtuální počítač z testovacího prostředí umožňuje uživateli testovacího prostředí získat správně nakonfigurovaný systém bez vynaložení času nebo úsilí na konfiguraci počítače. Vedoucí testovacího prostředí může použít nárokovaný stav virtuálních počítačů ke zlepšení počtu počítačů generovaných, vyčištění počítačů a určení priority konfigurací. [Image Factory](image-factory-create.md) je dobrým příkladem automatizovaného procesu vytváření virtuálních počítačů a bitové kopie pro více testovacích prostředí. Skripty mohou být upraveny tak, aby provedly některou z následujících situací s příslušnými změnami nebo mohly být použity jako reference pro vytvoření vlastního systému.

## <a name="situational-use-of-claimable-machines"></a>Situační použití vyhlazovacích strojů

- Použijte funkci deklarace nebo zrušení deklarace, která uživatelům umožňuje předávat řízení počítačů z jednoho do druhého a nemusí explicitně vědět, kdo bude stroj vyzvedávat příště.
- Vývoj, testování a ladění scénáře, kde konkrétní konfigurace počítače může reprodukovat chybu, pak může být počítač nevyzvednut, což umožňuje jinému vývojáři nárokovat počítač a pokračovat v práci. Tato funkce je obzvláště užitečná, protože více lidí pracuje na dálku v různých oblastech světa. 
- Členové týmu mohou pracovat s jedním prostředím. Můžete například ručně nastavit složité prostředí, které nelze automatizovat, nebo vytvořit prostředky, které mohou zpracovávat pouze úpravy pro jeden vstup, jako jsou obrazy. V minulosti byl tento problém řešen tím, že byl zprovozněn specializovaný stroj. Nárokovatelná funkce je zlepšení oproti ručnímu procesu tím, že má vestavěnou kontrolu přístupu uživatelů a vizuální identifikaci, pokud je k dispozici. Když není nárokován, virtuální ho sazí se zřazené ke snížení nákladů.
- Mít datový disk, který je připojený k virtuálnímu počítače. Každý disk až ~ 1 TB dat umožňuje velké množství dat, které mají být předány bez nutnosti kopírovat nebo duplikovat data. Virtuální modul by byl původně vytvořen s připojeným diskem, který měl velký objem dat.  Každý uživatel pak může nárokovat počítač a získat přístup k datům. Až bude hotovo, unclaim virtuálního počítače povolit ostatním uživatelům do počítače.

Tam jsou některé výhrady k používání nárokovatelné stroje, nejčastěji kolem získání přístupu k počítači. Pokud je počítač připojen k doméně, pak uživatel, který tvrdí, že počítač bude muset mít již udělen přístup, obvykle se provádí udělením přístupu ke skupině, která zahrnuje všechny uživatele v testovacím prostředí při vytvoření virtuálního počítače. Pokud počítač není připojen k doméně, bude nutné spustit artefakt **resetování hesla virtuálního počítače** ve veřejném úložišti, aby bylo možné přidat uživatele jako správce.  Artefakty lze použít i po spuštění nebo nárokovaném počítači.

## <a name="next-steps"></a>Další kroky
Podívejte se na následující článek: [Vytvoření a správa nárokovatelných virtuálních počítačů v Azure DevTest Labs](devtest-lab-add-claimable-vm.md)
