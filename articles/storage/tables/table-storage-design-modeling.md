---
title: Vztahy modelování v Azure Table Storage – návrh | Microsoft Docs
description: Pochopení procesu modelování při návrhu řešení úložiště tabulek v Azure Přečtěte si o vztazích 1:1 a dědičnosti.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 3023b478ef7a4aaf6d9239e997bdf63282b56210
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88271188"
---
# <a name="modeling-relationships"></a>Modelování relací
Tento článek popisuje proces modelování, který vám může pomáhat navrhovat řešení úložiště tabulek v Azure.

Vytváření doménových modelů je klíčový krok v rámci navrhování složitých systémů. Obvykle se používá proces modelování k identifikaci entit a vztahů mezi nimi jako způsob pochopení obchodní domény a informování o návrhu systému. Tato část se zaměřuje na to, jak můžete přeložit některé běžné typy vztahů nalezené v doménových modelech na návrhy pro Table service. Proces mapování z logického datového modelu na fyzický datový model založený na fyzickém NoSQL se liší od toho, který se používá při navrhování relační databáze. Návrh relačních databází obvykle předpokládá proces normalizace dat, který je optimalizován pro minimalizaci redundance – a deklarativní možnosti dotazování, které jsou abstraktní, jak implementace databáze funguje.  

## <a name="one-to-many-relationships"></a>Relace 1: n
Relace 1: n mezi objekty obchodní domény se často vyskytují: například jedno oddělení má mnoho zaměstnanců. Existuje několik způsobů, jak v Table service implementovat relace 1: n, a to s využitím specialistů a nevýhody, které mohou být relevantní pro konkrétní scénář.  

Vezměte v úvahu příklad velkého víceúrovňového podniku s desítkami tisíc států a entit zaměstnanců, kde každé oddělení má mnoho zaměstnanců a každého zaměstnance, který je přidružený k jednomu konkrétnímu oddělení. Jedním z možností je ukládat samostatné entity oddělení a zaměstnanců, jako jsou tyto:  


![Ukládat samostatné entity oddělení a zaměstnanců](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Tento příklad ukazuje implicitní relaci 1:1 mezi typy na základě hodnoty **PartitionKey** . Každé oddělení může mít mnoho zaměstnanců.  

Tento příklad také ukazuje entitu oddělení a její související entity zaměstnanců ve stejném oddílu. Pro různé typy entit se můžete rozhodnout pro použití různých oddílů, tabulek nebo dokonce účtů úložiště.  

Alternativním řešením je denormalizovat data a ukládat pouze entity zaměstnanců s denormalizovanými daty oddělení, jak je znázorněno v následujícím příkladu. V tomto konkrétním scénáři nemusí být tento denormalizovaný přístup nejlepší, pokud budete vyžadovat, abyste mohli změnit podrobnosti o manažerovi oddělení, protože to provedete tak, že budete muset aktualizovat každého zaměstnance v oddělení.  

![Entita zaměstnance](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Další informace naleznete v části [vzory denormalizace](table-storage-design-patterns.md#denormalization-pattern) dále v této příručce.  

Následující tabulka shrnuje odborníky a nevýhody jednotlivých přístupů uvedených výše pro ukládání entit zaměstnanců a oddělení, které mají vztah 1: n. Měli byste taky zvážit, jak často očekáváte provádění různých operací: může být přijatelné vytvořit návrh, který zahrnuje náročnou operaci, pokud tato operace proběhne jenom zřídka.  

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
<li>Entitu oddělení můžete aktualizovat jedinou operací.</li>
<li>Můžete použít transakci skupiny entit * (EGT) k zachování konzistence v případě, že máte požadavek na změnu entity oddělení vždy, když aktualizujete/vložíte/odstraníte entitu zaměstnance. Pokud například udržujete počet zaměstnanců oddělení pro každé oddělení.</li>
</ul>
</td>
<td>
<ul>
<li>Možná budete muset pro některé aktivity klienta načíst jak zaměstnance, tak i entitu oddělení.</li>
<li>K operacím úložiště dochází ve stejném oddílu. Ve velkém objemu transakcí může to mít za následek hotspot.</li>
<li>Zaměstnance nemůžete přesunout do nového oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Samostatné typy entit, různé oddíly nebo tabulky nebo účty úložiště</td>
<td>
<ul>
<li>Můžete aktualizovat entitu oddělení nebo entitu zaměstnanci jedinou operací.</li>
<li>U vysoce transakčních svazků to může přispět k rozšíření zatížení napříč více oddíly.</li>
</ul>
</td>
<td>
<ul>
<li>Možná budete muset pro některé aktivity klienta načíst jak zaměstnance, tak i entitu oddělení.</li>
<li>EGTs se nedá použít k zachování konzistence při aktualizaci, vložení nebo odstranění zaměstnance a aktualizaci oddělení. Například aktualizace počtu zaměstnanců v entitě oddělení.</li>
<li>Zaměstnance nemůžete přesunout do nového oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Odnormalizovat do jedné entity typu</td>
<td>
<ul>
<li>Všechny potřebné informace můžete načíst pomocí jediného požadavku.</li>
</ul>
</td>
<td>
<ul>
<li>Pokud potřebujete aktualizovat informace o odděleních, může být náročné zajistit konzistenci (to by vyžadovalo, abyste aktualizovali všechny zaměstnance v oddělení).</li>
</ul>
</td>
</tr>
</table>

* Další informace najdete v tématu [transakce skupin entit](table-storage-design.md#entity-group-transactions) .  


Jak si zvolíte mezi těmito možnostmi, které jsou nejdůležitější pro odborníky a nevýhody, závisí na konkrétních scénářích aplikací. Například jak často měníte entity oddělení. všechny dotazy zaměstnanců potřebují další informace oddělení; Jak blízko máte omezení škálovatelnosti pro vaše oddíly nebo účet úložiště?  

## <a name="one-to-one-relationships"></a>Relace 1:1
Doménové modely můžou zahrnovat relace 1:1 mezi entitami. Pokud potřebujete v Table service implementovat relaci 1:1, musíte také zvolit způsob propojení těchto dvou souvisejících entit, pokud je potřebujete načítat. Tento odkaz může být buď implicitní, na základě konvence v hodnotách klíče, nebo explicitní uložením odkazu ve formě hodnot **PartitionKey** a **RowKey** v každé entitě ke své související entitě. Diskuzi o tom, jestli byste měli související entity ukládat do stejného oddílu, najdete v části [relace 1: n](#one-to-many-relationships).  

K dispozici jsou také pokyny k implementaci, které by vám mohly vést k implementaci vztahů 1:1 v Table service:  

* Zpracování velkých entit (Další informace najdete v tématu [vzor velkých entit](table-storage-design-patterns.md#large-entities-pattern)).  
* Implementace ovládacích prvků přístupu (Další informace najdete v tématu řízení přístupu pomocí sdílených přístupových podpisů).  

## <a name="join-in-the-client"></a>Připojit se v klientovi
I když existují způsoby modelování vztahů v Table service, neměli byste si pamatovat, že dva hlavní důvody pro použití Table service jsou škálovatelnost a výkon. Pokud zjistíte, že pracujete s modelem mnoha vztahů, které ohrožují výkon a škálovatelnost vašeho řešení, měli byste se sami zeptat, pokud je nutné sestavit všechny relace dat do návrhu tabulky. Je možné zjednodušit návrh a zlepšit škálovatelnost a výkon vašeho řešení, pokud povolíte, aby klientská aplikace prováděla potřebná spojení.  

Například pokud máte malé tabulky, které obsahují data, která se nemění často, pak můžete tato data načíst jednou a uložit je do mezipaměti v klientovi. To se může vyhnout opakovaným zpětným vrácením, aby se načetla stejná data. V příkladech, které jsme prohlédli v tomto průvodci, se sada oddělení v malé organizaci pravděpodobně stane malou a v případě, že je to pro data, která může klientská aplikace Stáhnout jednou, je vhodné je změnit.  

## <a name="inheritance-relationships"></a>Vztahy dědičnosti
Pokud vaše klientská aplikace používá sadu tříd, které tvoří část vztahu dědičnosti k reprezentaci obchodních entit, můžete tyto entity snadno uchovat v Table service. Například můžete mít následující sadu tříd definované v klientské aplikaci, kde **Person** je abstraktní třída.

![Abstraktní třída person](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Můžete zachovat instance dvou konkrétních tříd v Table service pomocí jedné tabulky Person pomocí entit, které vypadají takto:  

![Tabulka Person](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Další informace o práci s více typy entit ve stejné tabulce v kódu klienta najdete v části práce s heterogenními typy entit dále v této příručce. V této části najdete příklady, jak rozpoznat typ entity v kódu klienta.  


## <a name="next-steps"></a>Další kroky

- [Způsoby návrhu tabulek](table-storage-design-patterns.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrovat data tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
