---
title: Modelování vztahů v návrh tabulky Azure storage | Dokumentace Microsoftu
description: Vysvětlení procesu modelování při návrhu řešení pro úložiště tabulky.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 5d83e61282d2f21a3016997e324d0f58eff15e78
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813003"
---
# <a name="modeling-relationships"></a>Modelování relací
Tento článek popisuje proces modelování při návrhu řešení Azure Table storage.

Vytváření modelů domény je klíče krokem návrhu komplexních systémů. Proces modelování se obvykle používají k identifikaci entit a vztahů mezi nimi jako způsob, jak pochopit obchodní domény a informujte návrhu vašeho systému. Tato část se zaměřuje na způsob lze přeložit některé nejčastější typy relací v doméně modelů a návrhů pro služby Table service. Proces mapování ze logický datový model do fyzické SQL Server – modelu dat se liší od, který používá při navrhování relační databáze. Návrh relačních databází obvykle předpokládá optimalizována pro minimalizaci redundance – a deklarativní zjišťování schopností, který získává implementaci jak databázi fungování procesu normalizace data.  

## <a name="one-to-many-relationships"></a>Vztah jeden mnoho
1 n vztahy mezi objekty obchodní domény docházet často: například jeden oddělení má mnoho zaměstnanců. Existuje několik způsobů, jak implementovat vztahů jednoho k několika ve službě Table service každý s výhody a nevýhody, které můžou být relevantní pro konkrétní scénář.  

Podívejte se na příklad velké korporace zemí s desítkami tisíc oddělení a zaměstnance entit, kde každé oddělení má mnoho zaměstnance a zaměstnance, jako přidružený k jedné konkrétní oddělení. Jedním z přístupů je uložit samostatné oddělení a entity zaměstnance například tyto:  


![Store samostatné oddělení a zaměstnance entity](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Tento příklad ukazuje implicitní vztah jeden mnoho mezi typy, na základě **PartitionKey** hodnotu. Každé oddělení může mít mnoho zaměstnanců.  

Tento příklad také uvádí oddělení entity a její související zaměstnance entity do stejného oddílu. Můžete se rozhodnout používat různé oddíly, tabulky nebo účty úložiště i pro typy jiné entity.  

Alternativním přístupem je denormalizovat data a ukládat jenom zaměstnance entity s Nenormalizovaná oddělení dat, jak je znázorněno v následujícím příkladu. V našem konkrétním scénáři tento denormalizovaném přístupu nemusí být nejlepší, pokud máte požadavek, nebude moct změnit podrobnosti o vedoucí oddělení, protože k tomu je potřeba aktualizovat každý zaměstnanec v oddělení.  

![Zaměstnanec entity](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Další informace najdete v tématu [Denormalizace vzor](table-storage-design-patterns.md#denormalization-pattern) dále v tomto průvodci.  

Následující tabulka shrnuje výhody a nevýhody jednotlivých uvedených výše pro ukládání zaměstnanci a oddělení entity, které mají vztah jeden mnoho přístupů. Také byste měli zvážit, jak často předpokládáte můžete provádět různé operace: může být přijatelný mít návrh, který zahrnuje náročná operace, je-li tuto operaci situace nastane pouze zřídka.  

<table>
<tr>
<th>Přístup</th>
<th>V oblasti IT</th>
<th>Nevýhody</th>
</tr>
<tr>
<td>Typy entit, stejného oddílu, stejná tabulka</td>
<td>
<ul>
<li>Oddělení entity můžete aktualizovat pomocí jedné operace.</li>
<li>Můžete použít EGT můžete zachovat konzistenci, pokud máte požadavek na Upravit entity oddělení pokaždé, když je aktualizace, vložení nebo odstranění entity zaměstnance. Například, pokud chcete zachovat počet zaměstnanců oddělení pro každé oddělení.</li>
</ul>
</td>
<td>
<ul>
<li>Budete muset načíst zaměstnanci a oddělení entity pro některé činnosti klienta.</li>
<li>Operace úložiště stát do stejného oddílu. Na svazcích vysokou transakční to může vést hotspot.</li>
<li>Zaměstnanec nelze přesunout do jiného oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Typy entit, různé oddíly nebo tabulek účty úložiště</td>
<td>
<ul>
<li>Oddělení entity nebo zaměstnance entity můžete aktualizovat pomocí jedné operace.</li>
<li>Na vysokou transakční svazky to může pomoct rozložit zatížení mezi více oddílů.</li>
</ul>
</td>
<td>
<ul>
<li>Budete muset načíst zaměstnanci a oddělení entity pro některé činnosti klienta.</li>
<li>Nelze použít EGTs zajistit konzistenci při můžete aktualizace/insert/delete zaměstnance a aktualizace oddělení. Aktualizuje se například počet zaměstnanců v entitě oddělení.</li>
<li>Zaměstnanec nelze přesunout do jiného oddělení pomocí EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizovat do jedné entity typu</td>
<td>
<ul>
<li>Můžete načíst všechny informace, které potřebujete, prostřednictvím jednoho požadavku.</li>
</ul>
</td>
<td>
<ul>
<li>Může být nákladné můžete zachovat konzistenci, pokud je potřeba aktualizovat informace o oddělení (to bude potřeba aktualizovat všechny zaměstnance v oddělení).</li>
</ul>
</td>
</tr>
</table>

Jak zvolit mezi tyto možnosti a které z výhody a nevýhody jsou nejvýznamnější, závisí na konkrétní aplikaci scénáře. Například jak často upravíte entity oddělení; potřebují všechny vaše dotazy zaměstnance dalších informací o oddělení; jak blízko jste omezení škálovatelnosti na oddíly nebo účtu úložiště?  

## <a name="one-to-one-relationships"></a>Relace 1: 1
Doménových modelů může obsahovat mezi entitami relace 1: 1. Pokud potřebujete implementovat vztah 1: 1 ve službě Table service, musíte také zvolit způsob propojení dvě související entity, pokud je třeba je oba načíst. Tento odkaz může být implicitní, podle úmluvy v hodnotách klíčů nebo explicitní uložením odkaz ve formě **PartitionKey** a **RowKey** hodnoty v jednotlivých entit k jeho související entity. Informace o tom, jestli byste měli uložit související entity do stejného oddílu, naleznete v části [jeden mnoho relací](#one-to-many-relationships).  

Existují také důležité informace o implementaci, které by mohly vést k implementaci relace 1: 1 ve službě Table service:  

* Zpracování velkých entit (Další informace najdete v tématu [velkých entit vzor](table-storage-design-patterns.md#large-entities-pattern)).  
* Implementace řízení přístupu (Další informace najdete v tématu řízení přístupu s podpisy sdíleného přístupu).  

## <a name="join-in-the-client"></a>Připojte se k v klientovi
I když existují způsoby pro modelování vztahů ve službě Table service, by neměla zapomenete, že jsou dva primární důvody pro použití služby Table service škálovatelnost a výkon. Pokud zjistíte, že jsou modelována mnoho vztahů, které ohrožují výkon a škálovatelnost řešení, které by měl položte si otázku: Pokud je potřeba vytvořit všechny vztahy mezi daty do návrhu tabulky. Je možné, zjednodušuje návrh a zlepšit škálovatelnost a výkon vašeho řešení, pokud jste povolili klientské aplikace provádět žádné nezbytné spojení.  

Například pokud máte malé tabulky obsahující data, která se nemění často, potom můžete tato data načíst jednou a uložení do mezipaměti na straně klienta. To se můžete vyhnout opakované výměn dat k načtení stejná data. V příkladech, které jsme v této příručce se podívat na sadu oddělení v malé organizaci je pravděpodobně být malé a zřídka díky tomu je vhodným kandidátem pro data, která můžete po stažení aplikace pro klienta a mezipaměti jako vyhledávací data změnit.  

## <a name="inheritance-relationships"></a>Vztahy dědičnosti
Pokud vaše klientská aplikace využívá sadu tříd, které tvoří součást vztah dědičnosti představující obchodní entity, je možné snadno zachovat těchto entit ve službě Table service. Například můžete mít následující sadu tříd definovaných v klientské aplikaci kde **osoba** je abstraktní třída.

![Abstraktní třída osoby](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Je možné zachovat instancemi dvou tříd konkrétní ve službě Table service pomocí jedné osobě tabulky pomocí entit v tento vzhled takto:  

![Tabulka osoby](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Další informace o práci s více typy entit ve stejné tabulce v kódu klienta najdete v části Práce s typy entit heterogenní dál v této příručce. To poskytuje příklady toho, jak rozpoznat typ entity v klientském kódu.  


## <a name="next-steps"></a>Další postup

- [Vzory návrhu tabulky](table-storage-design-patterns.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)
