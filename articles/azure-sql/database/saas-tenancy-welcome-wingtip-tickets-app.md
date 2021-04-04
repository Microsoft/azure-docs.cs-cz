---
title: Vítá vás aplikace wingtips
description: Přečtěte si o modelech databázové architektury a o ukázkové aplikaci wingtips SaaS pro Azure SQL Database v cloudovém prostředí.
keywords: kurz k sql database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: a2969ce6ceda0d1b71ec991b32f5b10acf9bfa12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780372"
---
# <a name="the-wingtip-tickets-saas-application"></a>Aplikace SaaS lístky Wingtip
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V každé ze tří vzorků se *implementuje stejné aplikace* SaaS Ticket Tickets. Aplikace je jednoduchý seznam událostí a aplikace SaaS pro vytváření lístků, které cílí na malé místo – kino, klub atd. Každé místo je tenant aplikace a má vlastní data: podrobnosti místa, seznamy událostí, zákazníky, objednávky lístků atd.  Aplikace spolu se skripty a kurzy pro správu prezentují ucelený scénář SaaS. To zahrnuje zřizování klientů, monitorování a správu výkonu, správy schémat a vytváření sestav a analýz mezi klienty.

## <a name="three-saas-application-and-tenancy-patterns"></a>Tři SaaS aplikace a vzory tenantů

K dispozici jsou tři verze aplikace. Každý z nich zkoumá jiný model architektury databáze na Azure SQL Database.  První používá samostatnou aplikaci pro každého tenanta s vlastní databází. Druhý používá víceklientské aplikace s databází pro každého tenanta. Třetí ukázka používá víceklientské aplikace s horizontálně dělené databázemi s více klienty.

![Tři modely architektury][image-three-tenancy-patterns]

 Každá ukázka zahrnuje kód aplikace a také skripty pro správu a kurzy, které probírají řadu vzorů návrhu a správy.  Každá ukázka se nasadí méně než pět minut.  Všechny tři mohou být nasazeny souběžně, takže můžete porovnat rozdíly v návrhu a správě.

## <a name="standalone-application-per-tenant-pattern"></a>Vzor samostatné aplikace na tenanta

Samostatná aplikace na model tenanta používá pro každého tenanta jednu aplikaci tenanta s databází. Aplikace každého tenanta, včetně její databáze, je nasazená do samostatné skupiny prostředků Azure. Skupinu prostředků je možné nasadit v rámci předplatného poskytovatele služeb nebo předplatného tenanta a spravovat ji poskytovatelem v zastoupení tenanta. Samostatná aplikace na model tenanta poskytuje největší izolaci tenanta, ale je to ale nejdražší, protože není k dispozici možnost sdílení prostředků mezi více klienty.  Tento model je vhodný pro aplikace, které mohou být složitější a které jsou nasazené na menší počet klientů.  Díky samostatným nasazením se aplikace dá přizpůsobit pro každého tenanta snadněji než v jiných vzorcích.  

Přečtěte si [kurzy][docs-tutorials-for-wingtip-sa] a kód na GitHubu  [. ../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Model databáze na tenanta

Model databáze na tenanta je vhodný pro poskytovatele služeb, kteří se zabývají izolací klientů a chtějí provozovat centralizovanou službu, která umožňuje nákladově efektivní využívání sdílených prostředků. Databáze se vytvoří pro každé místo nebo tenanta a všechny databáze se centrálně spravují. Databáze je možné hostovat v elastických fondech, aby poskytovaly cenově efektivní a snadnou správu výkonu, která využívá nepředvídatelné vzorce úloh klientů. Databáze katalogu obsahuje mapování mezi klienty a jejich databázemi. Toto mapování je spravováno pomocí funkcí správy mapy horizontálních oddílů v [klientské knihovně elastic Database](elastic-database-client-library.md), která poskytuje efektivní správu připojení k aplikaci.

Přečtěte si [kurzy][docs-tutorials-for-wingtip-dpt] a kód na GitHubu  [. ../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Horizontálně dělené vzor databáze pro více tenantů

Víceklientské databáze jsou platné pro poskytovatele služeb, kteří hledají nižší náklady na tenanta a v pořádku s omezenou izolací tenanta. Tento model umožňuje vybalit velký počet klientů do individuální databáze a řídit náklady na klienta. Téměř neomezené škálování je možné tím, že horizontálního dělení klienty napříč více databázemi. Databáze katalogu mapuje klienty do databází.  

Tento vzor také umožňuje *hybridní* model, ve kterém je možné optimalizovat pro náklady s více klienty v databázi, nebo optimalizovat pro izolaci s jedním klientem ve vlastní databázi. Možnost volby se dá udělat na základě tenanta, a to buď při zřizování tenanta, nebo později, bez dopadu na aplikaci.  Tento model se dá efektivně použít, když je potřeba, aby se skupiny tenantů nacházely jinak. Například klienti s nízkými náklady se dají přiřadit ke sdíleným databázím, zatímco klienti úrovně Premium se dají přiřadit ke svým vlastním databázím. 

Přečtěte si [kurzy][docs-tutorials-for-wingtip-mt] a kód na GitHubu  [. ../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Další kroky

#### <a name="conceptual-descriptions"></a>Koncepční popisy

- Podrobnější vysvětlení vzorů aplikační architektury je k dispozici na [více vzorech tenantů pro databáze SaaS][saas-tenancy-app-design-patterns-md] .

#### <a name="tutorials-and-code"></a>Kurzy a kód

- Samostatná aplikace na tenanta:
    - [Kurzy pro samostatnou aplikaci][docs-tutorials-for-wingtip-sa]
    - [Kód pro samostatnou aplikaci na GitHubu][github-code-for-wingtip-sa].

- Databáze na tenanta:
    - [Kurzy pro databázi na tenanta][docs-tutorials-for-wingtip-dpt]
    - [Kód databáze pro každého klienta na GitHubu][github-code-for-wingtip-dpt].

- Horizontálně dělené více tenantů:
    - [Kurzy pro více tenantů horizontálně dělené][docs-tutorials-for-wingtip-mt]
    - [Kód pro více tenantů horizontálně dělené na GitHubu][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Tři modely architektury."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: ./saas-standaloneapp-get-started-deploy.md
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: ./saas-dbpertenant-wingtip-app-overview.md
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: ./saas-multitenantdb-get-started-deploy.md
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb