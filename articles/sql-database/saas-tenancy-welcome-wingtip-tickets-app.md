---
title: "Vítá vás aplikace Wingtips – Azure SQL Database | Microsoft Docs"
description: "Informace o modelech databáze klientů a o Wingtips SaaS ukázkovou aplikaci pro Azure SQL Database v cloudovém prostředí."
keywords: kurz k sql database
services: sql-database
author: billgib
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/17/2017
ms.author: billgib
ms.openlocfilehash: bb69a03333ed9dcdba1456d053c0080be5ba4d10
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="the-wingtip-tickets-saas-application"></a>Adresář Wingtip lístky SaaS aplikace

Stejné *Wingtip lístky* aplikace SaaS je implementována ve všech tří vzorků. Tato aplikace je jednoduchá událost výpis a lístků pro aplikace SaaS cílení na malé místa - divadla, kluby atd. Každý místo je klient aplikace a má svá vlastní data: místo podrobnosti, seznam událostí, zákazníky, objednávky lístek, atd.  Aplikace, společně s kurzy a správu skripty umožňující prezentovat scénářem SaaS začátku do konce. To zahrnuje zřizování klientů, monitorování a správu výkonu, Správa schématu a mezi klienta vytváření sestav a analýzy.

## <a name="three-saas-application-and-tenancy-patterns"></a>Tří vzorů SaaS aplikace a klientů

Tři verze aplikace jsou k dispozici. Každý prozkoumá vzor klientů jiné databázi v databázi SQL Azure.  První používá samostatné aplikace na klienta s svou vlastní databázi. Druhá používá víceklientské aplikace s databází na každého klienta. Třetí Ukázka používá víceklientské aplikace s horizontálně dělené víceklientské databáze.

![Tří vzorů klientů][image-three-tenancy-patterns]

 Každá ukázka obsahuje kód aplikace a správu skripty a návodů, které prozkoumat rozsah vzoru návrhu a správu.  Každá ukázka nasadí v menší této pět minut.  Všechny tři může být nasazený-souběžného, můžete porovnat rozdíly v návrhu a správu.

## <a name="standalone-application-per-tenant-pattern"></a>Samostatné aplikace za klienta vzor

Samostatné aplikace za klienta vzor používá aplikace jednoho klienta s databází pro každého klienta. Nasazení aplikace každého klienta, včetně své databázi, do skupiny samostatné prostředků Azure. Skupiny prostředků můžete nasadit v předplatné poskytovatele služeb nebo předplatné klienta a spravovaných zprostředkovatelem služby jménem klienta. Samostatné aplikace na klienta vzor poskytuje největší izolaci klientů, ale je obvykle nejvíce náročná, protože není příležitost ke sdílení prostředků mezi několik klientů.  Tento vzor je skvěle hodí pro aplikace, který může být složitější a které se nasadí na menší počty klientů.  U samostatných nasazení aplikace lze přizpůsobit pro každého klienta snadněji než v jiných vzorů.  

Podívejte se [kurzy] [ docs-tutorials-for-wingtip-sa] a kód na Githubu [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Databáze za klienta vzor

Databáze pro každý vzor klienta je platná pro poskytovatele služeb, které se týká izolaci mezi klienty a chcete spustit centralizovaná služba, která umožňuje cenově efektivní používání sdílených prostředků. Databáze se vytvoří pro každou místo nebo klienta, a všechny databáze jsou centrálně spravovány. Databáze může být hostovaný v elastické fondy k poskytování nákladově efektivní a snadno výkonu správu, který využívá vzory nepředvídatelným úlohy klienty. Databáze katalogu obsahuje mapování mezi klienty a jejich databáze. Toto mapování je spravovat pomocí horizontálního oddílu mapy funkcím pro správu [klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md), která nabízí správu efektivní připojení k aplikaci.

Podívejte se [kurzy] [ docs-tutorials-for-wingtip-dpt] a kód na Githubu [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Vzor horizontálně dělené víceklientské databáze

Víceklientské databáze jsou platné pro poskytovatele služeb hledá nižší náklady na klienta a nevadí izolace Snížené klienta. Tento vzor umožňuje balení velkého počtu klientů do jedné databáze, poklesne náklady za klienta. Je možné podle horizontálního dělení NEAR nekonečné škálování klientů napříč více databází. Databáze katalogu mapuje klienty k databázím.  

Tento vzor také umožňuje *hybridní* modelu, ve kterém můžete optimalizovat náklady s více klienty v databázi nebo optimalizovat pro izolaci s jednoho klienta ve vlastní databázi. Výběr můžete provést na základě klienta klienta, buď když klient je zřízená nebo novější, bez dopadu na aplikace.  Tento model dá efektivně skupin klientů, které musí být zpracovávat odděleně. Například klienti nízkonákladové lze přiřadit sdílené databáze, je-při premium klientů lze přiřadit k vlastní databáze. 

Podívejte se [kurzy] [ docs-tutorials-for-wingtip-mt] a kód na Githubu [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Další postup

#### <a name="conceptual-descriptions"></a>Koncepční popisy

- Podrobnější vysvětlení vzoru klientů aplikace je k dispozici na [víceklientské SaaS databáze vzory klientů][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Kurzy a kódu

- Samostatné aplikace podle jednotlivých klientů:
    - [Kurzy pro samostatné aplikace ] [ docs-tutorials-for-wingtip-sa].
    - [Kód pro samostatné aplikace na Githubu][github-code-for-wingtip-sa].

- Databáze každého klienta:
    - [Kurzy pro každého klienta databázi][docs-tutorials-for-wingtip-dpt].
    - [Kód pro databázi každého klienta, na Githubu][github-code-for-wingtip-dpt].

- Horizontálně dělené více klientů:
    - [Kurzy pro horizontálně dělenou víceklientské][docs-tutorials-for-wingtip-mt].
    - [Kód pro horizontálně dělenou víceklientské na Githubu][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Tří vzorů klientů."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

