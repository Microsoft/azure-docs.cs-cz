---
title: Vítejte v aplikaci Wingtips
description: Další informace o modelech pronájmu databáze a o ukázkové aplikaci Wingtips SaaS pro Azure SQL Database v cloudovém prostředí.
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
ms.date: 01/25/2019
ms.openlocfilehash: 4e0b3afe51ac7c7a6b9213fcee79af57cbbd8197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818318"
---
# <a name="the-wingtip-tickets-saas-application"></a>Aplikace Wingtip Tickets SaaS

Stejná aplikace *Wingtip Tickets* SaaS je implementována v každém ze tří ukázek. Aplikace je jednoduchá událost výpis a ticketing SaaS aplikace zaměřená na malé podniky - divadla, kluby, atd. Každé místo je nájemcem aplikace a má své vlastní údaje: podrobnosti o místě, seznamy událostí, zákazníci, objednávky vstupenek atd.  Aplikace spolu se skripty pro správu a kurzy představuje komplexní scénář SaaS. To zahrnuje zřizování klientů, monitorování a správu výkonu, správu schémat a vytváření sestav a analýz y napříč tenanty.

## <a name="three-saas-application-and-tenancy-patterns"></a>Tři saas aplikace a nájemní vzory

K dispozici jsou tři verze aplikace; každý zkoumá jiný vzor klienta databáze v Azure SQL Database.  První používá samostatnou aplikaci na klienta s vlastní databází. Druhý používá víceklientské aplikace s databází na klienta. Třetí ukázka používá víceklientské aplikace s databázemi s oddíly více klientů.

![Tři nájemní vzory][image-three-tenancy-patterns]

 Každá ukázka obsahuje kód aplikace a skripty pro správu a kurzy, které zkoumají řadu vzorů návrhu a správy.  Každý vzorek se nasazuje za méně než pět minut.  Všechny tři lze nasadit vedle sebe, takže můžete porovnat rozdíly v návrhu a správě.

## <a name="standalone-application-per-tenant-pattern"></a>Samostatná aplikace podle vzoru klienta

Samostatná aplikace na vzorek klienta používá aplikaci jednoho klienta s databází pro každého klienta. Aplikace každého klienta, včetně jeho databáze, se nasadí do samostatné skupiny prostředků Azure. Skupinu prostředků lze nasadit v předplatném poskytovatele služeb nebo předplatného klienta a spravovat poskytovatele mj. Samostatná aplikace na vzor klienta poskytuje největší izolace klienta, ale je obvykle nejdražší, protože neexistuje žádná možnost sdílet prostředky mezi více klientů.  Tento vzor je vhodný pro aplikace, které mohou být složitější a které jsou nasazeny na menší počet klientů.  Se samostatnými nasazeními lze aplikaci přizpůsobit pro každého klienta snadněji než v jiných vzorcích.  

Podívejte se na [výukové programy][docs-tutorials-for-wingtip-sa] a kód na GitHubu [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Databáze na vzor klienta

Vzor databáze na tenanta je efektivní pro poskytovatele služeb, kteří se zabývají izolací klienta a chtějí spustit centralizovanou službu, která umožňuje nákladově efektivní využití sdílených prostředků. Databáze je vytvořena pro každé místo nebo tenanta a všechny databáze jsou centrálně spravované. Databáze mohou být hostovány v elastických fondech, které poskytují nákladově efektivní a snadnou správu výkonu, která využívá nepředvídatelné vzory úloh klientů. Databáze katalogu obsahuje mapování mezi klienty a jejich databázemi. Toto mapování je spravováno pomocí funkcí správy map [skrečů klientské knihovny elastické databáze](sql-database-elastic-database-client-library.md), které poskytují efektivní správu připojení k aplikaci.

Podívejte se na [výukové programy][docs-tutorials-for-wingtip-dpt] a kód na GitHubu [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Vzor databáze s oddíly s více klienty

Víceklientské databáze jsou efektivní pro poskytovatele služeb, kteří hledají nižší náklady na klienta a v pořádku se sníženou izolací tenanta. Tento vzor umožňuje balení velkého počtu klientů do individuální databáze, řízení náklady na klienta dolů. Téměř nekonečné škálování je možné horizontálním horizontálem klienty napříč více databází. Databáze katalogu mapuje klienty na databáze.  

Tento vzor také umožňuje *hybridní* model, ve kterém můžete optimalizovat pro náklady s více klienty v databázi nebo optimalizovat pro izolaci s jedním tenantem ve své vlastní databázi. Volba může být provedena na základě tenant-by-tenant, buď při zřízení klienta nebo později, bez dopadu na aplikaci.  Tento model lze efektivně použít, když skupiny klientů je třeba zacházet odlišně. Například nízkonákladové klienty lze přiřadit ke sdíleným databázím, zatímco klienti premium lze přiřadit k vlastním databázím. 

Podívejte se na [výukové programy][docs-tutorials-for-wingtip-mt] a kód na GitHubu [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Další kroky

#### <a name="conceptual-descriptions"></a>Koncepční popisy

- Podrobnější vysvětlení vzorců nájmu aplikace je k dispozici na [víceklientské saas databáze nájemnívzory][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Návody a kód

- Samostatná aplikace na klienta:
    - [Návody pro samostatnou aplikaci][docs-tutorials-for-wingtip-sa].
    - [Kód pro samostatnou aplikaci na GitHubu][github-code-for-wingtip-sa].

- Databáze na klienta:
    - [Kurzy pro databázi na klienta][docs-tutorials-for-wingtip-dpt].
    - [Kód pro databázi na klienta na GitHubu][github-code-for-wingtip-dpt].

- Víceklient s pevnými vrstvami:
    - [Kurzy pro víceklientské oddíly][docs-tutorials-for-wingtip-mt].
    - [Kód pro víceklientské oddíly na GitHubu][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Tři nájemní vzorce."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

