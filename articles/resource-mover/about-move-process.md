---
title: O procesu přesunu v nástroji Azure Resource stěhovací
description: Přečtěte si o procesu přesouvání prostředků v různých oblastech pomocí nástroje Azure Resource stěhovací.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 02/01/2021
ms.author: raynew
ms.openlocfilehash: facbb30201aa6bde2044ca647383cc32ecd9ba26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980554"
---
# <a name="about-the-move-process"></a>O procesu přesunutí

[Azure Resource](overview.md) Centre vám pomůže přesunout prostředky Azure napříč oblastmi Azure. Tento článek shrnuje komponenty používané nástrojem Resource stěhovací a popisuje proces přesunutí. 


## <a name="components"></a>Komponenty

Tyto součásti se používají během přesunu oblasti.

**Komponenta** | **Podrobnosti**
--- | ---
**Resource stěhovací** |  Zdroje informací o přístředcích prostředků s [poskytovateli prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md) orchestrují přesun prostředků mezi oblastmi. Resource stěhovací analyzuje závislosti prostředků a udržuje a spravuje stav prostředků během procesu přesunutí. 
**Přesunout kolekci** |  Kolekce přesunutí je objekt [Azure Resource Manager](../azure-resource-manager/management/overview.md) .<br/><br/> Kolekce přesunutí se vytvoří během procesu přesunutí oblasti pro každou spárovánou kombinaci zdrojových a cílových oblastí v rámci předplatného. Kolekce obsahuje metadata a konfigurační informace o prostředcích, které chcete přesunout.<br/><br/>Prostředky přidané do kolekce přesunů musí být ve stejném předplatném, ale můžou být v různých skupinách prostředků. 
**Přesunout prostředek** | Když přidáte prostředek do kolekce přesunutí, je sledován jako prostředek pro přesunutí.<br/><br/> Resource stěhovací uchovává informace pro všechny prostředky přesunu v kolekci přesunů a udržuje vztah 1:1 mezi zdrojovým a cílovým prostředkem. 
**Závislosti** | Resource stěhovací ověří prostředky, které přidáte do kolekce, a zkontroluje, jestli prostředky mají nějaké závislosti, které nejsou v kolekci přesunů.<br/><br/> Po určení závislostí prostředku můžete buď přidat závislosti do kolekce přesunutí a přesunout je, nebo můžete vybrat alternativní existující prostředky v cílové oblasti. Před zahájením přesunu je nutné vyřešit všechny závislosti. 



## <a name="move-region-process"></a>Přesunout proces oblasti 

![Diagram znázorňující kroky přesunutí](./media/about-move-process/move-steps.png)

Každý prostředek přesunutí prochází souhrnnými kroky.

**Krok** | **Podrobnosti** | **Stav/problémy**
--- | --- | --- 
**Krok 1: výběr prostředků** | Vyberte prostředek. Prostředek se přidá do kolekce Move. | Stav prostředku se přesune na *Příprava čeká na vyřízení*.<br/><br/> Pokud prostředek obsahuje závislosti, *ověření závislosti* znamená, že je nutné přidat závislé prostředky do kolekce přesunutí.
**Krok 2: ověření závislostí** | Zahájí proces ověřování.<br/><br/> Pokud ověření ukáže, že čekají závislé prostředky, přidejte je do kolekce Move. <br/><br/> Přidejte všechny závislé prostředky, a to i v případě, že je nechcete přesunout. Později můžete určit, že prostředky, které přesouváte, by měly místo toho používat jiné prostředky v cílové oblasti.<br/><br/> Znovu se ověří, dokud neexistují žádné nedokončené závislosti. | Po přidání a ověření všech závislostí se stav prostředku přesune na *Příprava čeká* bez problémů.
**Krok 3: Příprava** | Zahájí proces přípravy. Přípravné kroky závisí na prostředcích, které přesouváte:<br/><br/> - **Bezstavové prostředky**: bezstavové prostředky mají jenom informace o konfiguraci. Tyto prostředky nepotřebují průběžnou replikaci dat, aby je bylo možné přesunout. Mezi příklady patří virtuální sítě Azure (virtuální sítě), síťové adaptéry, nástroje pro vyrovnávání zatížení a skupiny zabezpečení sítě. Pro tento typ prostředku vytvoří proces přípravy šablonu Azure Resource Manager.<br/><br/> - **Stavové prostředky**: stavové prostředky mají konfigurační informace a data, která je potřeba přesunout. Mezi příklady patří virtuální počítače Azure a databáze SQL Azure. Proces přípravy se u každého prostředku liší. Může zahrnovat replikaci zdrojového prostředku do cílové oblasti. | Probíhá ukončení přesunutí stavu prostředku pro *přípravu*.<br/><br/> Po dokončení přípravy se stav prostředku přesune na *iniciované probíhající přesunutí* bez problémů.<br/><br/> Neúspěšný proces přesune stav na *Příprava*.
**Krok 4: zahájení přesunu** | Zahájí proces přesunutí. Metoda Move závisí na typu prostředku:<br/><br/> - **Bezstavové**: obvykle pro bezstavové prostředky nasadí proces přesunutí importovanou šablonu v cílové oblasti. Šablona je založena na nastavení zdrojového prostředku a všech ručních úpravách, které provedete v cílovém nastavení.<br/><br/> - **Stav**: u stavových prostředků může proces přesunutí zahrnovat vytvoření prostředku nebo povolení kopie v cílové oblasti.<br/><br/>  Pouze pro stavové prostředky může zahájení přesunu způsobit výpadky zdrojových prostředků. Například virtuální počítače a SQL. | Při ukončení přesunu se přesune stav a *spustí se probíhající přesun*.<br/><br/> Úspěšný příkaz zahájit přesun přesune stav prostředku do *probíhajícího přesunu*, bez problémů. <br/><br/> Neúspěšný přesunutý proces přesune stav, aby *se zahájilo přesunutí*.
**Krok 5 – možnost 1: zahození přesunutí** | Po prvním přesunu se můžete rozhodnout, jestli chcete pokračovat s úplným přesunem. Pokud to neuděláte, můžete přesun zrušit a stěhovací společnosti odstraní prostředky vytvořené v cíli. Proces replikace stavových prostředků pokračuje i po zahození. Tato možnost je užitečná pro testování. | Vypuštění prostředků přesune stav, který *probíhá zahození*.<br/><br/> Úspěšné zrušení přesunutí stavu pro *zahájení probíhajícího přesunu* bez problémů.<br/><br/> Nepovedlo se zrušit stav přesunu *přesunutí na zrušení*. 
**Krok 5 – možnost 2: potvrzení přesunutí** | Pokud chcete pokračovat s úplným přesunem, můžete po prvním přesunutí ověřit prostředky v cílové oblasti a až budete připraveni, potvrdíte přesunutí.<br/><br/> U stavových prostředků může potvrzení způsobit, že se zdrojové prostředky, jako jsou virtuální počítače nebo SQL, nestane nedostupnými. | Když přesun potvrdíte, stav prostředku se přesune na * probíhající přesun probíhá * *.<br/><br/> Po úspěšném potvrzení se ve stavu prostředku zobrazí *dokončené přesuny*, bez problémů.<br/><br/> Nepovedlo se přesunout stav *přesunutí do potvrzování*.
**Krok 6: odstranění zdroje** | Po potvrzení přesunu a ověření prostředků v cílové oblasti můžete odstranit zdrojový prostředek. | Po potvrzení se stav prostředku přesune na čeká se na *odstranění zdroje*. Pak můžete vybrat zdrojový prostředek a odstranit ho.<br/><br/> -Odstranit lze pouze prostředky ve stavu *odstraňování čekají na zdroj* . | Odstranění skupiny prostředků nebo SQL Server na portálu Resource stěhovací není podporované. Tyto prostředky lze odstranit pouze ze stránky vlastností prostředku.


## <a name="move-region-states"></a>Přesunout stavy oblastí

Proces přesunutí má řadu stavů a problémů, které mohou nastat během každého stavu. Ty jsou shrnuté ve vývojovém diagramu.

![Vývojový diagram možných stavů a problémů](./media/about-move-process/process.png)


### <a name="dependency-analysis"></a>Analýza závislostí

V průběhu procesu přesunu se může zobrazit výzva k ověření závislostí, pokud:
- Prostředek používá závislé prostředky, které nejsou v kolekci Move.
- Závislý prostředek v kolekci Move má vlastní závislosti, které nejsou v kolekci Move.
- Změnili jste nastavení cíle pro daný prostředek a potřebujete znovu ověřit závislosti.


### <a name="remove-resources"></a>Odebrat prostředky

Pokud prostředek přesunout nechcete, můžete ho odebrat z kolekce přesunutí. Obecně platí, že prostředek je pak odstraněn z kolekce spolu s případnými přidruženými akcemi nebo objekty, jako jsou například replikace nebo uložené šablony. Přesně co se stane, když odeberete prostředek, závisí na typu prostředku a stavu prostředku při jeho odstranění. [Další informace](remove-move-resources.md).

## <a name="move-impact"></a>Přesunout dopad

Tabulka shrnuje, co je ovlivněno při pohybu mezi oblastmi.

**Chování** | **Napříč oblastmi**
--- | --- | --- 
**Data** | Data prostředku a metadata se přesunou.<br/><br/> Metadata jsou dočasně ukládána ke sledování stavu závislostí a operací prostředků.
**Prostředek** | Zdrojový prostředek zůstane nedotčený, aby se zajistilo, že aplikace budou dál fungovat, a můžete je po přesunu volitelně odebrat.<br/><br/> Prostředek se vytvoří v cílové oblasti.
**Přesunout proces** | Proces s více kroky vyžadující ruční zásah a monitorování.
**Testování** | Testování přesunutí je důležité, protože aplikace by po přesunutí měly v cílové oblasti dál fungovat podle očekávání.
**Výpadek** |  Neočekávala se žádná ztráta dat, ale některé výpadky přesunu prostředků.



## <a name="next-steps"></a>Další kroky

[Přesunout](tutorial-move-region-virtual-machines.md) Virtuální počítače Azure do jiné oblasti.
[Přesunout](tutorial-move-region-sql.md) Prostředky Azure SQL do jiné oblasti.