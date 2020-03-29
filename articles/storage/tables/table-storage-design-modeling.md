---
title: Modelování vztahů v návrhu úložiště Azure Table | Dokumenty společnosti Microsoft
description: Seznamte se s procesem modelování při navrhování řešení úložiště tabulek.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 25082c107fbc0feeb533aa2b4fc56cff960e778d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457561"
---
# <a name="modeling-relationships"></a>Modelování relací
Tento článek popisuje proces modelování, který vám pomůže navrhnout řešení úložiště Azure Table.

Vytváření doménových modelů je klíčovým krokem při návrhu složitých systémů. Proces modelování se obvykle používá k identifikaci entit a vztahů mezi nimi jako způsob, jak porozumět obchodní doméně a informovat o návrhu systému. Tato část se zaměřuje na to, jak můžete přeložit některé běžné typy vztahů, které se nacházejí v modelech domény, do návrhů pro službu Table Service. Proces mapování z logického datového modelu na fyzický datový model založený na NoSQL se liší od procesu používaného při navrhování relační databáze. Návrh relačních databází obvykle předpokládá proces normalizace dat optimalizovaný pro minimalizaci redundance – a deklarativní dotazování schopnost, která abstrahuje, jak implementace databáze funguje.  

## <a name="one-to-many-relationships"></a>Vztahy 1:N
Vztahy 1:N mezi objekty obchodní domény se vyskytují často: například jedno oddělení má mnoho zaměstnanců. Existuje několik způsobů, jak implementovat relace 1:N ve službě Table service s klady a zápory, které mohou být relevantní pro konkrétní scénář.  

Vezměme si příklad velké nadnárodní korporace s desítkami tisíc oddělení a zaměstnaneckých subjektů, kde každé oddělení má mnoho zaměstnanců a každý zaměstnanec je spojen s jedním konkrétním oddělením. Jedním z přístupů je ukládání samostatných oddělení a zaměstnaneckých entit, jako jsou tyto:  


![Uložení samostatných oddělení a zaměstnaneckých entit](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Tento příklad ukazuje implicitní vztah 1:N mezi typy založenými na hodnotě **PartitionKey.** Každé oddělení může mít mnoho zaměstnanců.  

Tento příklad také zobrazuje entitu oddělení a její související entity zaměstnanců ve stejném oddílu. Můžete použít různé oddíly, tabulky nebo dokonce účty úložiště pro různé typy entit.  

Alternativním přístupem je denormalizovat data a ukládat pouze entity zaměstnanců s nenormalizovanými daty oddělení, jak je znázorněno v následujícím příkladu. V tomto konkrétním scénáři tento nenormalizovaný přístup nemusí být nejlepší, pokud máte požadavek, aby bylo možné změnit podrobnosti vedoucího oddělení, protože k tomu je třeba aktualizovat každého zaměstnance v oddělení.  

![Zaměstnanecká entita](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Další informace naleznete v [tématu Denormalization vzor](table-storage-design-patterns.md#denormalization-pattern) dále v této příručce.  

Následující tabulka shrnuje výhody a nevýhody každého z výše uvedených přístupů pro ukládání entit zaměstnanců a oddělení, které mají vztah 1:N. Měli byste také zvážit, jak často očekáváte provádět různé operace: může být přijatelné mít návrh, který zahrnuje nákladnou operaci, pokud tato operace probíhá pouze zřídka.  

<table>
<tr>
<th>Přístup</th>
<th>Výhody</th>
<th>Nevýhody</th>
</tr>
<tr>
<td>Samostatné typy entit, stejný oddíl, stejná tabulka</td>
<td>
<ul>
<li>Entitu oddělení můžete aktualizovat pomocí jedné operace.</li>
<li>EGT můžete použít k udržení konzistence, pokud máte požadavek na úpravu entity oddělení při každé aktualizaci/vložení/odstranění entity zaměstnance. Pokud například udržujete počet zaměstnanců oddělení pro každé oddělení.</li>
</ul>
</td>
<td>
<ul>
<li>Možná budete muset načíst zaměstnance i entity oddělení pro některé aktivity klienta.</li>
<li>Operace úložiště dojít ve stejném oddílu. Při vysokých objemech transakcí to může mít za následek hotspot.</li>
<li>Zaměstnance nelze přesunout do nového oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Samostatné typy entit, různé oddíly nebo tabulky nebo účty úložiště</td>
<td>
<ul>
<li>Entitu oddělení nebo entitu zaměstnance můžete aktualizovat pomocí jedné operace.</li>
<li>Při vysokých objemech transakcí to může pomoci rozložit zatížení na více oddílů.</li>
</ul>
</td>
<td>
<ul>
<li>Možná budete muset načíst zaměstnance i entity oddělení pro některé aktivity klienta.</li>
<li>Egts nelze použít k udržení konzistence při aktualizaci/vložení/odstranění zaměstnance a aktualizaci oddělení. Například aktualizace počtu zaměstnanců v entitě oddělení.</li>
<li>Zaměstnance nelze přesunout do nového oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizovat do typu jedné entity</td>
<td>
<ul>
<li>Pomocí jediného požadavku můžete načíst všechny potřebné informace.</li>
</ul>
</td>
<td>
<ul>
<li>Zachování konzistence může být nákladné, pokud potřebujete aktualizovat informace o oddělení (to by vyžadovalo aktualizaci všech zaměstnanců v oddělení).</li>
</ul>
</td>
</tr>
</table>

Způsob výběru mezi těmito možnostmi a které výhody a nevýhody jsou nejvýznamnější, závisí na konkrétní scénáře aplikace. Například jak často upravujete entity oddělení; všechny dotazy zaměstnanců potřebují další informace o oddělení; jak blízko jste k omezení škálovatelnosti na oddíly nebo váš účet úložiště?  

## <a name="one-to-one-relationships"></a>Vztahy 1:1
Modely domény mohou zahrnovat vztahy 1:1 mezi entitami. Pokud potřebujete implementovat relaci 1:1 ve službě Tabulka, musíte také zvolit, jak propojit dvě související entity, když je potřebujete načíst oba. Toto propojení může být implicitní, založené na konvenci v klíčových hodnotách nebo explicitní uložením odkazu ve formě hodnot **PartitionKey** a **RowKey** v každé entitě do související entity. Diskuse o tom, zda byste měli uložit související entity ve stejném oddílu, naleznete v části [Relace 1:N](#one-to-many-relationships).  

Existují také důležité informace o implementaci, které by mohly vést k implementaci relací 1:1 ve službě Table Service:  

* Zpracování velkých entit (další informace naleznete v tématu [Vzor velkých entit).](table-storage-design-patterns.md#large-entities-pattern)  
* Implementace ovládacích prvků přístupu (další informace naleznete v tématu Řízení přístupu pomocí sdílených přístupových podpisů).  

## <a name="join-in-the-client"></a>Připojte se ke klientovi
Přestože existují způsoby, jak modelovat vztahy ve službě Tabulka, neměli byste zapomínat, že dva hlavní důvody pro použití table service jsou škálovatelnost a výkon. Pokud zjistíte, že modelujete mnoho relací, které ohrožují výkon a škálovatelnost vašeho řešení, měli byste se zeptat sami sebe, zda je nutné vytvořit všechny datové relace do návrhu tabulky. Pokud umožníte klientské aplikaci provádět potřebná spojení, můžete zjednodušit návrh a zlepšit škálovatelnost a výkon vašeho řešení.  

Například pokud máte malé tabulky, které obsahují data, která se nemění často, pak můžete načíst tato data jednou a ukládat do mezipaměti na straně klienta. To může zabránit opakované zpáteční lety načíst stejná data. V příkladech, které jsme zkoumali v této příručce, je pravděpodobné, že sada oddělení v malé organizaci bude malá a zřídka se změní, což z ní činí vhodného kandidáta pro data, která může klientská aplikace stáhnout jednou a uložit do mezipaměti při vyhledávání dat.  

## <a name="inheritance-relationships"></a>Vztahy dědičnosti
Pokud vaše klientská aplikace používá sadu tříd, které jsou součástí vztahu dědičnosti k reprezentaci obchodních entit, můžete tyto entity snadno zachovat ve službě Tabulka. Můžete mít například následující sadu tříd definovaných v klientské aplikaci, kde **Person** je abstraktní třída.

![Třída Abstraktní osoba](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Můžete zachovat instance dvou konkrétních tříd ve službě Tabulka pomocí jedné tabulky osoba pomocí entit v tom, které vypadají takto:  

![Tabulka osob](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Další informace o práci s více typy entit ve stejné tabulce v kódu klienta naleznete v části Práce s heterogenními typy entit dále v této příručce. To poskytuje příklady, jak rozpoznat typ entity v kódu klienta.  


## <a name="next-steps"></a>Další kroky

- [Způsoby návrhu tabulek](table-storage-design-patterns.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
