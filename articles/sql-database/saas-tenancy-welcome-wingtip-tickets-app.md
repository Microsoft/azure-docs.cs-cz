---
title: Vítá vás Wingtips aplikace – Azure SQL Database | Dokumentace Microsoftu
description: Další informace o databázi tenantů modelech a o vás Wingtips SaaS ukázkovou aplikaci pro službu Azure SQL Database v cloudovém prostředí.
keywords: kurz k sql database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b27877e25dd3bdd4711d1c036e2f203e1b8c0e7b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462133"
---
# <a name="the-wingtip-tickets-saas-application"></a>Aplikace SaaS aplikace Wingtip Tickets

Stejné *Wingtip Tickets* aplikací SaaS je implementována ve všech třech ukázky. Aplikace je jednoduchá událost výpis a vytváření tiketů SaaS aplikace určené pro malé venues - kinům kluby, atd. Každé místo tenantem aplikace a má svoje vlastní data: Podrobnosti míst, seznam událostí, zákazníky, objednávky lístek, atd.  Aplikace, společně s skripty pro správu a kurzy, prezentuje scénáři SaaS začátku do konce. To zahrnuje zřizování tenantů, monitorování a správu výkonu, Správa schématu a napříč tenanty generování sestav a analýzy.

## <a name="three-saas-application-and-tenancy-patterns"></a>Tři vzory SaaS aplikace a tenantů

Tři verze aplikace jsou k dispozici. každý vzor jiné databázi tenantů zkoumá na Azure SQL Database.  První způsob využívá samostatné aplikace na klienta s vlastní databázi. Druhý používá aplikace s více tenanty s databází na tenanta. Třetí příklad používá aplikace s více tenanty s horizontálně dělené databáze s více tenanty.

![Tři vzory tenantů][image-three-tenancy-patterns]

 Každá ukázka zahrnuje kód aplikace a skripty pro správu a kurzy, které prozkoumejte celou řadu vzorů návrhu a správy.  Každá ukázka nasadí za méně této pět minut.  Všechny tři může být nasazené side-by-side, abyste mohli porovnat rozdíly v návrhu a správy.

## <a name="standalone-application-per-tenant-pattern"></a>Samostatná aplikace za vzor tenanta

Samostatná aplikace za vzor tenanta používá jednoho tenanta aplikaci s databází pro každého tenanta. Každý tenant aplikace, včetně jeho databáze, se nasadí do samostatných skupinu prostředků Azure. Skupina prostředků je možné nasadit v poskytovatele služeb předplatného nebo předplatného tenanta a spravované poskytovatelem jménem klienta. Samostatná aplikace za vzor tenanta poskytuje největší izolaci klientů, ale je obvykle nejvíce nákladné, protože není příležitost ke sdílení prostředků mezi více tenantů.  Tento vzor je velmi vhodná pro aplikace, která může být postup mnohem složitější a které jsou nasazené na menší počty klientů.  U samostatného nasazení aplikace je možné přizpůsobit pro každého tenanta snadněji než v jiných vzorků.  

Podívejte se [kurzy] [ docs-tutorials-for-wingtip-sa] a kódu na Githubu [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Databáze na tenanta vzor

Databáze pro každý model tenanta platí pro poskytovatele služeb, které se zabývají izolaci klientů a chcete spouštět centralizované služby, která umožňuje cenově efektivní využívání sdílených prostředků. Databáze se vytvoří pro každého tenanta nebo míst a všechny databáze jsou centrálně spravovány. Databáze je možné hostovat v elastických fondech k poskytování nákladově efektivní a snadno výkonu management, která využívá vzory nepředvídatelné zatížení klientům. Databáze katalogu obsahuje mapování mezi klienty a jejich databáze. Toto mapování se spravuje pomocí funkce správy mapování horizontálních oddílů z [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md), která poskytuje správu efektivní připojení k aplikaci.

Podívejte se [kurzy] [ docs-tutorials-for-wingtip-dpt] a kódu na Githubu [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Vzor horizontálně dělené databázi s více tenanty

Poskytovatelé služeb hledají nižší náklady na tenanta a dobře s izolací sníženou tenanta platí databáze s více tenanty. Tento model umožňuje balení velkého počtu klientů do jednotlivých databází, snižte svoje náklady na tenanta. Téměř nekonečné škálování je možné pomocí horizontálního dělení tenanty napříč několika databázemi. Databáze katalogu mapuje databází tenantů.  

Tento model umožňuje také *hybridní* model, ve kterém můžete optimalizovat náklady s více tenanty do databáze nebo optimalizovat pro zajištění izolace pomocí jednoho tenanta v svou vlastní databázi. Možnost provádět na základě tenanta tenanta, buď když tenanta je zřízená nebo novější, bez jakéhokoli dopadu na aplikace.  Tento model dá efektivně skupiny tenantů potřeba zpracovávat odděleně. Například tenantů s nízkými náklady je možné přiřadit na sdílené databáze, zatímco tenanti úrovně premium je možné přiřadit vlastní databáze. 

Podívejte se [kurzy] [ docs-tutorials-for-wingtip-mt] a kódu na Githubu [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Další postup

#### <a name="conceptual-descriptions"></a>Koncepční popisy

- Podrobnější vysvětlení modely tenantů aplikací je k dispozici na [SaaS s více tenanty, databáze tenantů vzory][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Kurzy a kódu

- Samostatná aplikace na tenanta:
    - [Kurzy pro samostatnou aplikaci ] [ docs-tutorials-for-wingtip-sa].
    - [Kód pro samostatnou aplikaci na Githubu][github-code-for-wingtip-sa].

- Databáze na tenanta:
    - [Kurzy pro databáze na tenanta][docs-tutorials-for-wingtip-dpt].
    - [Kód pro databáze na tenanta na Githubu][github-code-for-wingtip-dpt].

- Horizontálně dělené víceklientské:
    - [Kurzy pro více tenantů horizontálně dělené][docs-tutorials-for-wingtip-mt].
    - [Kód pro horizontálně dělené více tenantů a na Githubu][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Tři vzory tenantů."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

