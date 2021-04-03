---
title: Použití schopností deklarace identity v Azure DevTest Labs | Microsoft Docs
description: Přečtěte si o různých scénářích použití funkcí deklarace identity nebo nedeklarované deklarace identity Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 73ed3c0b94a66f5d17b5c8e2561c65bb48579aa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476525"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Použití schopností deklarace identity v Azure DevTest Labs
Služba Azure DevTest Labs zlepšuje efektivitu a efektivitu vývojářů a testerů. Tento článek se zaměřuje na možnost deklarace nebo nedeklarované virtuální počítače v Azure DevTest Labs. Obsahuje také různé způsoby, kterými tato funkce zlepšuje uživatelské prostředí. Než se pustíte do různých scénářů, kde se tato funkce dá použít, Podívejme se na to, jaká je **deklarace identity** , a jak funguje.

## <a name="claimable-machines"></a>Nároky na počítače
Nárokující počítač je virtuální počítač, který se vytvoří v testovacím prostředí bez vlastníka. Jakmile je počítač přihlášený, má uživatel celou řadu možností pro tento virtuální počítač. Když uživatel přimítá počítač, provede se několik změn. Virtuální počítač se přesune ze seznamu **vynucené virtuální počítače** do seznamu **moje virtuální počítače** v Azure Portal. 

Uživatel se může připojit k virtuálnímu počítači, přizpůsobit artefakty, restartovat, zastavit nebo zrušit jeho deklaraci. Existuje několik způsobů, jak vytvořit virtuální počítač s nárokem:

- Vytvořte počítač a oddeklarujte ho, aby se přesunul do fondu s nárokem. 
- Vytvořte virtuální počítač a umístěte ho do sdíleného fondu pomocí [rozšířených nastavení](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Existují dva případy, kdy je možné využít možnosti deklarace identity/zrušení deklarace identity. První případ vyžaduje více Forethought a plánování, aby bylo možné je navrhovat a provádět správně. A druhá situace je větší. Níže jsou uvedeny některé příklady různých případů.

## <a name="designed-use-of-claimable-machines"></a>Navržení použití nárokných počítačů

- **Vývoj a testování softwaru:** Umožněte vývojářům nebo testerům zvýšit produktivitu tím, že nakonfigurujete počítače připravené a v nevynuceném stavu. Použití sady virtuálních počítačů s různými konfiguracemi, nezbytných nástrojů a pomocí nejnovějšího kódu umožňuje uživatelům nárokovat virtuální počítač a začít pracovat, aniž by museli strávit čas potřebný k nastavování počítače. Před tím, než budou virtuální počítače vyžádány, se počítače zřídí, ale vypne se minimalizace nákladů na počítače, které se používají méně často. Když jsou virtuální počítače potřeba, uživatel jednoduše přiškodí virtuální počítač, který spustí počítač. Možnost nedeklarací není v tomto případě vhodná, protože vytvoření nového virtuálního počítače je často jednodušší a levnější.
- **Učebna/laboratoře:** Mít virtuální počítače, které jsou předem nakonfigurované pro třídu nebo testovací prostředí, aby se studenti mohli hned připojit k počítači pomocí Azure Portal.  Jakmile student nároke na virtuální počítač, testovací prostředí zajistí, že žádný z nich nemůže uplatnit stejný počítač. Automatizace tohoto procesu zajistí, aby byl k dispozici požadovaný počet počítačů se zadaným prostředím. Pokud se studenti nezobrazují nebo běží pozdě, můžou neoprávněné počítače zůstat dostupné, dokud se relace neuvolní s minimálními náklady. Možnost zrušit deklaraci v tomto scénáři neplatí, protože virtuální počítač je v neznámém stavu, když je předchozí uživatel hotový.
- **Ukázky:** Použijte počítače pro ukázky, ve kterých jsou počítače v testovacím prostředí nastavené s konkrétními prostředími. Tato možnost je užitečná v případě, že více lidí může poskytovat ukázku ve stejnou dobu nebo v náhodných časech, například při konferenci. Možnost nedeklarací může být v této situaci užitečná, protože ukázka by neměla měnit stav počítače, takže uživatelé můžou vrátit virtuální počítač zpátky do fondu s nárokem pro další ukázku. V případě nevynuceného neoprávněného počítače a vzniku minimálních nákladů můžou virtuální počítače zůstat v testovacím prostředí za delší časová období.
- **Pracovní procesy pro dočasné/smluvní procesy:** Umožňuje uživatelům používat počítač. Když odejdou, vrátí virtuální počítač do fondu nároků bez ztráty dat. Když je virtuální počítač neoprávněný, může virtuální počítač vyžádat jiný uživatel a pokračovat nebo zkontrolovat počítač, kde najdete další informace.
- **Obecně platí:** Možnost mít jediný zdroj automaticky nakonfigurované a nasazovat virtuální počítače na konkrétní tempo je užitečná v mnoha různých situacích. K dispozici je několik různých situací, kdy funkce deklarace identity nebo omezení pomáhá uživatelům zefektivnit vytváření virtuálních počítačů v nevynuceném stavu s nastavením konfigurace. Konfigurace může obsahovat různé operační systémy, jazyky, disky nebo [jiný software (artefakty)](devtest-lab-artifact-author.md) v závislosti na vašich potřebách. Možnost vyžádat si virtuální počítač z testovacího prostředí umožní uživateli testovacího prostředí získat správně nakonfigurovaný systém bez útraty času nebo úsilí při konfiguraci počítače. Správce testovacího prostředí může použít deklarovaný stav virtuálních počítačů ke zlepšení počtu vygenerovaných počítačů, čištění počítačů a určování priority konfigurací. [Továrna imagí](image-factory-create.md) je dobrým příkladem automatizovaného procesu vytváření virtuálních počítačů a imagí pro více cvičení. Skripty mohou být upraveny tak, aby se příslušné změny prováděly v některé z následujících situací nebo byly použity jako referenční informace pro vytvoření vlastního systému.

## <a name="situational-use-of-claimable-machines"></a>Situace v případě potřeby počítačů s nárokem

- Využijte schopnost deklarace identity nebo nedeklarace identity, která umožňuje uživatelům předat řízení počítačů od jednoho na druhý a nemusíte výslovně znát, kdo bude počítač vyzvednutím dalšího počítače.
- Vývoj, testování a ladění scénáře, kdy konkrétní konfigurace počítače může reprodukování chyby, počítač může být nenárokný, aby mohl jiný vývojář nárok na počítač a pokračovat v práci. Tato funkce je užitečná hlavně v případě, že uživatelé pracují vzdáleně v různých oblastech světa. 
- Členové týmu mohou pracovat s jedním prostředím. Můžete například ručně nastavit složitá prostředí, které nemůže být automatizované nebo vytvářet prostředky, které mohou zpracovávat pouze úpravy jednoho vstupu, jako jsou obrázky. V minulosti se tento problém zabývá tím, že je počítač s vyhrazeným a spuštěným počítačem. Vynucená funkce je vylepšení pro ruční proces pomocí integrovaného řízení přístupu uživatele a identifikace vizuální identifikace, pokud je k dispozici. Pokud se neuplatní, virtuální počítač se zruší, aby se snížily náklady.
- Mít datový disk, který je připojený k virtuálnímu počítači. Každý disk až do velikosti ~ 1 TB dat umožňuje předat velký objem dat bez nutnosti kopírování nebo duplikování dat. Virtuální počítač se zpočátku vytvoří s připojeným diskem, který má velký objem dat.  Každý uživatel pak může tento počítač uplatnit a získat přístup k datům. Až to budete mít, Nedeklarujte virtuální počítač, aby bylo možné ostatním uživatelům počítač.

Existují určitá upozornění na použití nárokných počítačů, nejčastěji k přístupu k počítači. Pokud je počítač připojený k doméně, musí mít uživatel, který je k počítači nárok, mít již udělený přístup, obvykle to provede přidělením přístupu ke skupině, která zahrnuje všechny uživatele v testovacím prostředí při vytvoření virtuálního počítače. Pokud počítač není připojený k doméně, bude nutné spustit artefakt **resetování hesla virtuálního počítače** ve veřejném úložišti, aby bylo možné uživatele přidat jako správce.  Artefakty mohou být aplikovány i po spuštění nebo uplatnění počítače.

## <a name="next-steps"></a>Další kroky
Přečtěte si následující článek: [vytváření a správa virtuálních počítačů s deklarací identity v Azure DevTest Labs](devtest-lab-add-claimable-vm.md)
