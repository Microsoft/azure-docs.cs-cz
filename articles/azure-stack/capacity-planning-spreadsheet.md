---
title: Plánování kapacity spreadheet pro Azure Stack | Dokumentace Microsoftu
description: Další informace o plánování tabulky pro nasazení Azure Stack kapacity.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.openlocfilehash: 7b8276281b8b550492fed17adc516e9539380987
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269732"
---
# <a name="azure-stack-capacity-planner"></a>Capacity Planner služby Azure Stack
Azure Stack Capacity Planner je tabulka pro plánování kapacity prostředků Azure Stack. Capacity planner vám poskytuje schopnost navrhovat různých přidělení výpočetní prostředky a zobrazit, jak by přizpůsobit přes výběr nabídky hardwaru. Níže jsou uvedené podrobné pokyny pro použití kalkulačky Azure Stack.

## <a name="worksheet-descriptions"></a>Popisy list
Následuje krátký popis součástí Azure stacku Capacity Planner tabulky, který si můžete stáhnout z listů [ http://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner):

|Název karty|Popis|
|-----|-----|
|Verze právní omezení|Stručný přehled účelu kalkulačky, číslo verze a datum vydání.|
|Pokyny|Poskytuje podrobné pokyny pro použití nástroje Capacity Planner služby Azure Stack.|
|DefinedSolutionSKUs|Vícesloupcové tabulky obsahující až o pěti definice hardwaru. Příklady jsou položky v tomto listu. Cílem je, že uživatel změní podrobnosti pro vyhledání konfigurace systému považují za pro používání nebo zakoupení.|
|DefineByVMFootprint|Najdete vhodný hardware SKU tak, že vytvoříte kolekci různé velikosti a množství virtuálních počítačů.|
|DefineByWorkloadFootprint|Najdete vhodný hardware SKU tak, že vytvoříte kolekci úloh služby Azure Stack.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs pokyny
Tento sešit obsahuje příklady definice až o pěti hardwaru. Změnit podrobnosti tak, aby odpovídaly konfigurace systému považují za pro použití nebo zakoupit.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Výběr hardwaru poskytuje oprávnění hardwarových partnerů
Azure Stack se dodává jako integrovaný systém se softwarem nainstalovaným partneři řešení. Tito partneři řešení budou mít své vlastní, autoritativní verze služby Azure Stack tyto nástroje a nástroje pro plánování kapacity by měla sloužit k dokončení diskuse o kapacitě řešení.

### <a name="multiple-ways-to-model-computing-resources"></a>Několik způsobů, jak Model výpočetních prostředků
Základní stavební bloky, které používají pro modelování v rámci služby Azure Stack planneru prostředků jsou různých velikostí virtuálních počítačů Azure Stack (VM). Tyto virtuální počítače rozsah od nejmenší, "Základní 0", až do aktuálního největší virtuálního počítače, "Standard_Fsv2." Podle svých potřeb, různé počty pro různé virtuální počítače je možné vytvořit výpočetní přidělení prostředků pomocí tohoto nástroje dvěma různými způsoby.

1. Plánovač uživatel vybere konkrétní hardware nabídky a vidí, jaké kombinace různým prostředkům vejít do tohoto prostředku hardwaru. 

2. Uživatel planneru vytvoří konkrétní kombinaci přidělení virtuálního počítače a umožňuje ukazuje kalkulačky prostředků Azure, kterých se tyto skladové položky k dispozici hardwaru schopný zajistit podporu této konfigurace virtuálního počítače.

Tento nástroj nabízí dvě metody přidělování prostředků virtuálního počítače; buď jako jednu jedinou kolekci přidělení prostředků virtuálního počítače nebo kolekce až šest různé úlohy konfigurace. Každá úloha konfigurace může obsahovat různé přidělení dostupné prostředky virtuálních počítačů. Podrobné informace o vytvoření a použití všech těchto modelů přidělení jsou uvedeny níže. Pouze hodnoty obsažené v jiných pozadí Stínované buňky nebo v rámci SKU rozevírací seznamech tohoto listu by měl být upraven. Změny provedené v rámci Stínované buňky způsobit nefunkčnost prostředků výpočty.


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint pokyny
Vytvořit model s použitím jedné kolekce různé velikosti a množství virtuálních počítačů, vyberte na kartě "DefineByVMFootprint" a postupujte podle tohoto pořadí kroků.

1. V pravém horním rohu tohoto listu pomocí ovládací prvky zadané rozevírací seznamu vyberte počáteční počet serverů (mezi 4 a 16), které chcete nainstalovat v každém hardwaru systému (SKU). Tento počet serverů může změnit kdykoli během procesu modelování, chcete-li zjistit, jak to ovlivní celkové dostupné prostředky pro váš model přidělení prostředků.
2. Pokud chcete k modelování různých přidělení prostředků virtuálních počítačů na jeden konkrétní hardwarové konfigurace, najdete v pravém horním rohu stránky modré rozevírací seznam přímo pod popiskem "Aktuální skladová položka". Stáhněte dolů toto pole se seznamem a vyberte požadovaný hardware SKU.
3. Nyní jste připraveni začít přidávat různé velikosti virtuálních počítačů do modelu. Zahrnout konkrétní typ virtuálního počítače, zadejte hodnotu množství do modré pole osnovy nalevo od položky tohoto virtuálního počítače.

  > [!NOTE]
  > Celková velikost úložiště virtuálního počítače odkazuje na celkovou kapacitu datového disku virtuálního počítače (počet podporovaných disky * maximální kapacitu jednoho disku (1 TB)). Založené na ukazatelích konfigurace tak, aby se můžete rozhodnout požadovanou úroveň prostředku úložiště pro každý virtuální počítač Azure Stack jsme jste vyplnili tabulku dostupné úložiště konfigurace. Je důležité si uvědomit, že můžete přidat nebo změnit tabulku jako dostupné úložiště konfigurace podle potřeby.<br><br>Začíná na každý virtuální počítač původně přiřazené místní dočasné úložiště. Tak, aby odrážely dynamické zajišťování dočasného úložiště můžete změnit temp místní číslo k ničemu v rozevírací nabídce včetně dočasného úložiště o maximální povolená velikost.

4. Jak budete přidávat virtuální počítače, zobrazí se grafy, které se zobrazí dostupné zdroje SKU změna. To umožňuje vidět jejich vliv přidávání různé velikosti a množství virtuálních počítačů během procesu modelování. Dalším způsobem, jak zobrazit změny je sledovat spotřebovaná a stále k dispozici čísla, které jsou uvedeny přímo pod seznamem dostupných virtuálních počítačů. Tato čísla odrážejí hodnoty podle aktuálně vybraného hardwaru SKU.
5. Jakmile vytvoříte sadu virtuálních počítačů můžete najít navrhované hardwaru SKU kliknutím na tlačítko "Navrhované SKU" v pravém horním rohu stránky přímo pod popiskem "Aktuální skladová položka". Pomocí tohoto tlačítka můžete potom změnit konfiguraci virtuálního počítače a podívejte se, jaké hardware podporuje každou konfiguraci.


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint pokyny
Vytvořit model pomocí sady Azure Stack úlohy, vyberte na kartě "DefineByWorkloadFootprint" a postupujte podle tohoto pořadí kroků. Úlohy v Azure Stack, vytvářely pomocí dostupných prostředků virtuálního počítače.   

> [!TIP]
> Chcete-li změnit velikost zadaná úložiště pro virtuální počítač Azure Stack, přečtěte si poznámku z kroku 3 v předchozí části.

1. V pravém horním rohu této stránky pomocí ovládací prvky zadané rozevírací seznamu vyberte počáteční počet serverů (mezi 4 a 16), které chcete nainstalovat v každém hardwaru systému (SKU).
2. Pokud chcete k modelování různých přidělení prostředků virtuálních počítačů na jeden konkrétní hardwarové konfigurace, najdete v pravém horním rohu stránky modré rozevírací seznam přímo pod popiskem "Aktuální skladová položka". Stáhněte dolů toto pole se seznamem a vyberte požadovaný hardware SKU.
3. Vyberte vhodnou velikost pro všechny vaše požadované zásobníku virtuálních počítačů Azure na stránce DefineByVMFootprint, jak je popsáno výše v kroku 3 DefineByVMFootprint pokyny. Velikost úložiště na virtuální počítač je definována v listu DefineByVMFootprint.
4. Spouští se v levém horním rohu stránky DefineByWorkloadFootprint, vytvořte konfigurace pro až šest různých typů zatížení tak, že zadáte množství jednotlivých virtuálních počítačů typu obsažené v rámci úlohy. To se provádí tak, že číselné hodnoty do sloupce přímo pod názvem této úlohy. Názvy úloh může být upravena podle typu úlohy, které budou podporované v této konkrétní konfiguraci.
5. Můžete zahrnout konkrétní množství jednotlivých typů úloh tak, že zadáte hodnotu v dolní části sloupce přímo pod popiskem "Quantity".
6. Po vytvoření typů úloh a množství kliknutím na "Navrhované SKU" tlačítko v pravém horním rohu stránky přímo pod popiskem "Aktuální SKU" způsobí nejmenší SKU s dostatkem prostředků pro podporu tohoto celkové Konfigurace úloh, který se má zobrazit.
7. Další modelování může udělat tak, že upravíte počet serverů vybraných pro hardware SKU, nebo změna přidělení virtuálního počítače nebo množství v rámci konfigurace vaší úlohy. Přidružené grafy se zobrazují okamžitou zpětnou vazbu znázorňující, jak provedené změny ovlivní celkové spotřeby prostředků.
8. Jakmile budete spokojeni se změnami, klepnutím na tlačítko "Navrhované SKU" zobrazí SKU navržené pro novou konfiguraci.


## <a name="next-steps"></a>Další postup
Další informace o [aspekty integrace datových center pro službu Azure Stack](azure-stack-datacenter-integration.md)
