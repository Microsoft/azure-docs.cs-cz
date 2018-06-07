---
title: Modelování vztahů v Azure storage návrh tabulky | Microsoft Docs
description: Princip modelování při navrhování řešení tabulka úložiště.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 561281375273135009a956fd27dfe9f193ab92ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660763"
---
# <a name="modeling-relationships"></a>Modelování vztahů
Tento článek popisuje proces modelování při návrhu řešení úložiště Azure Table.

Vytváření modelů domény je klíče krokem návrhu komplexní systémů. Obvykle použijete procesu modelování k identifikaci entity a vztahy mezi nimi jako způsob, jak pochopit obchodní domény a informujte návrh vašeho systému. Tato část se zaměřuje na tom, jak může překládat některé běžné typy vztah nacházející se v modelech domény pro návrhy pro služby Table. Proces mapování z logický datový model na fyzické dat typu NoSQL na základě-model se liší od kterého při navrhování relační databáze. Návrh relační databáze obvykle předpokládá proces normalizace dat optimalizované pro minimalizaci redundance – a deklarativní dotazování funkci, která abstrahuje implementace jak databázi fungování.  

## <a name="one-to-many-relationships"></a>Vztahy jeden mnoho
Na více vztahů mezi objekty domény obchodní vyskytovat často: například jeden oddělení má mnoho pracovníků. Existuje několik způsobů, jak implementovat na více vztahů ve službě Table každý s výhody a nevýhody, které mohou být relevantní pro konkrétní scénář.  

Podívejte se na příklad velké korporace více national s desítkami tisíc oddělení a zaměstnanci entity, kde každé oddělení má mnoho zaměstnance a zaměstnance, jak je přidružený jeden konkrétní oddělení. Jeden z přístupů je k uložení samostatné oddělení a zaměstnanci entitami, jako je například tyto:  


![Uložení samostatné oddělení a zaměstnanci entity](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Tento příklad ukazuje implicitní vztah jeden mnoho mezi typy na základě **PartitionKey** hodnotu. Každé oddělení může mít mnoho pracovníků.  

Tento příklad také uvádí entity oddělení a jeho souvisejících zaměstnanec entity ve stejném oddílu. Mohli byste používat různé oddíly, tabulky nebo účty úložiště i pro typy různých entit.  

Alternativní způsob je denormalize vaše data a uložit jenom zaměstnanec entity daty nenormalizované oddělení, jak je znázorněno v následujícím příkladu. V tomto scénáři konkrétní tento nenormalizované přístup nemusí být nejvhodnější, pokud máte požadavek, abyste mohli změnit podrobnosti o oddělení manager, protože k tomu je potřeba aktualizovat všechny zaměstnance v oddělení.  

![Zaměstnanec entity](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Další informace najdete v tématu [Denormalization vzor](table-storage-design-patterns.md#denormalization-pattern) dál v této příručce.  

Následující tabulka shrnuje výhody a nevýhody jednotlivých přístupů uvedených výše pro ukládání zaměstnanců a oddělení entity, které mají vztah jeden mnoho. Také byste měli zvážit, jak často předpokládáte provádět různé operace: může být přijatelné tak, aby měl návrh, který zahrnuje náročná operace, pokud tuto operaci pouze dochází zřídka.  

<table>
<tr>
<th>Přístup</th>
<th>Odborníci na</th>
<th>Nevýhody</th>
</tr>
<tr>
<td>Jednotlivé typy entit, stejného oddílu, stejné tabulky</td>
<td>
<ul>
<li>Entity oddělení můžete aktualizovat pomocí jedné operace.</li>
<li>Můžete použít EGT zachování konzistence, pokud máte požadavky k úpravě entity oddělení vždy, když jste aktualizace, insert nebo odstranění entity zaměstnanců. Například pokud chcete zachovat počet zaměstnanců oddělení pro každé oddělení.</li>
</ul>
</td>
<td>
<ul>
<li>Musíte získat zaměstnanec a oddělení entity pro některé činnosti klienta.</li>
<li>Operace úložiště dojít ve stejném oddílu. Na vysoké transakce svazky proto může docházet v aktivní oblast.</li>
<li>Zaměstnanec nelze přesunout do nového oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Typy samostatné entity, různých oddílů nebo tabulek účty úložiště</td>
<td>
<ul>
<li>Oddělení entity nebo zaměstnanec entity můžete aktualizovat pomocí jedné operace.</li>
<li>Na vysoké transakce svazky to pomůže rozkládá zatížení mezi více oddílů.</li>
</ul>
</td>
<td>
<ul>
<li>Musíte získat zaměstnanec a oddělení entity pro některé činnosti klienta.</li>
<li>EGTs nelze použít k zajištění konzistence když jste update, insert nebo odstranění zaměstnanec a aktualizace oddělení. Aktualizuje se například počet zaměstnanců v oddělení entity.</li>
<li>Zaměstnanec nelze přesunout do nového oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalize do jedné entity typu</td>
<td>
<ul>
<li>Můžete načíst všechny informace, které je nutné se jeden požadavek.</li>
</ul>
</td>
<td>
<ul>
<li>To může být nákladné zachování konzistence, pokud je potřeba aktualizovat informace o oddělení (to se vyžaduje k aktualizaci všechny zaměstnance v oddělení).</li>
</ul>
</td>
</tr>
</table>

Jak můžete vybrat mezi tyto možnosti a které výhody a nevýhody jsou nejdůležitější, závisí na konkrétní aplikaci scénářů. Například jak často upravíte entities oddělení; mají všechny dotazy zaměstnanec další oddělení informace; jak zavřít jste k omezení škálovatelnosti na oddíly nebo účtu úložiště?  

## <a name="one-to-one-relationships"></a>Relace 1: 1
Modely domény může zahrnovat relace 1: 1 mezi entitami. Pokud potřebujete implementovat relace ve službě Table, musíte také zvolit způsob propojení dvou entit v relaci, pokud budete potřebovat načíst z obou. Tento odkaz může být buď implicitní, na základě konvencí v hodnoty klíče nebo explicitní uložením odkaz ve formě **PartitionKey** a **RowKey** hodnoty v každé entity k jeho související entity. Informace o tom, zda by měl uložit entit v relaci v stejného oddílu, najdete v části [na více vztahů](#one-to-many-relationships).  

Existují také aspekty implementace, které mohou vést k implementaci relace 1: 1 ve službě Table:  

* Zpracování velkých entit (Další informace najdete v tématu [velké vzor entity](table-storage-design-patterns.md#large-entities-pattern)).  
* Implementace řízení přístupu (Další informace najdete v tématu [řízení přístupu s podpisy sdíleného přístupu](#controlling-access-with-shared-access-signatures)).  

## <a name="join-in-the-client"></a>Připojení v klientovi
I když existují způsoby, jak model vztahy ve službě Table, by neměl zapomenete, zda jsou dva prvotní důvody pro používání služby Table škálovatelnost a výkon. Pokud zjistíte, že jsou modelování více vztahů, které ohrožují výkon a škálovatelnost řešení, měli byste požádat sami Pokud je nutné vytvořit všechny vztahy data do návrhu tabulky. Bude pravděpodobně možné zjednodušit návrh a zlepšit škálovatelnost a výkon vašeho řešení, pokud jste povolili klientské aplikace, proveďte všechny nezbytné spojení.  

Například pokud máte malé tabulky, které obsahují data, která se nemění často, pak můžete tato data načíst jednou a uložení do mezipaměti na straně klienta. To se můžete vyhnout opakovaných zbytečné komunikace načíst stejná data. V příkladech, které jsme jste prohlédli v této příručce bude pravděpodobně být malý a změňte zřídka díky tomu vhodným kandidátem na data, která klientská aplikace můžete stáhnout jednou a mezipaměti jako vyhledávání dat sadu oddělení v organizaci, malé.  

## <a name="inheritance-relationships"></a>Vztahy dědičnosti
Pokud klientské aplikace používá sadu tříd, které tvoří součást vztah dědičnosti představují obchodní entity, můžete snadno zachovat tyto entity ve službě Table. Například můžete mít následující sadu tříd definovaných v aplikaci klienta kde **osoba** je abstraktní třída.

![Abstraktní třída osoba](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Můžete zachovat instancí dvě konkrétní třídy ve službě Table pomocí jedné tabulky osoba pomocí entity v tomto vypadají:  

![Tabulka osoba](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Další informace o práci s více typy entit ve stejné tabulce v kódu klienta najdete v části [práce s typy entit heterogenní](#working-with-heterogeneous-entity-types) dál v této příručce. To poskytuje příklady, jak rozpoznat typ entity v kódu klienta.  


## <a name="next-steps"></a>Další postup

- [Vzory návrhu tabulky](table-storage-design-patterns.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat v tabulce](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)