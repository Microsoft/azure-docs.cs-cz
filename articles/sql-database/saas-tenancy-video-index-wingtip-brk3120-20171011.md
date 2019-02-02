---
title: Indexované videa, aplikací Azure SaaS SQL | Dokumentace Microsoftu
description: Tento článek indexuje různé body v čase v našich 81 minut video o návrhu aplikací tenantů SaaS DB, z konference Ignite nachází 11. října 2017. Můžete přeskočit k části, která vás zajímá. Aspoň 3 vzory jsou popsány. Funkce Azure, které zjednodušují vývoj a správu jsou popsány.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: bbe220780a3c21e7bfb15d0568904af4ed47f765
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567549"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Video se indexují a opatřeno poznámkou pro víceklientské aplikace SaaS využívající Azure SQL Database

Tento článek se s poznámkami index do umístění čas 81 zápisu videa o SaaS tenantů modely nebo vzory. Tento článek vám umožňuje přeskočit zpět nebo vpřed na videu se mají jaká část vás zajímá. Video vysvětluje možnosti hlavních návrhu pro víceklientské databáze aplikace v Azure SQL Database. Video obsahuje ukázky, názorné postupy správy kódu a v některých případech více podrobností, než může být v naší dokumentaci písemné služby informované prostřednictvím prostředí.

Video roste informace v dokumentaci napsané v: 
- *Koncepční:* [Vzory víceklientské SaaS databáze tenantů][saas-concept-design-patterns-563e]
- *Kurzy:* [Aplikace SaaS aplikace Wingtip Tickets][saas-how-welcome-wingtip-app-679t]

Videa a články popisují mnoho fáze vytváření aplikace s více tenanty v Azure SQL Database v cloudu. Speciální funkce služby Azure SQL Database usnadňují vývoj a implementovat víceklientské aplikace, které usnadňují správu a spolehlivě výkonnější.

Pravidelně aktualizujeme naši dokumentaci napsané. Video se upravovat nebo aktualizovat, takže nakonec další jeho podrobností může začnou být zastaralé.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Posloupnost 38 indexované čas snímky obrazovky

Tato část indexuje časovou lokalitu 38 diskuse během 81 minuty videa. Každý index čas je označena pomocí snímek obrazovky videa a někdy společně s dalšími informacemi.

Každý index čas je ve formátu *SS*. Pro instanci, druhá indexované časovou lokalitu s popiskem **cíle relace**, začíná na jak dlouho umístění **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Kompaktní odkazy na videa indexované čas umístění

Následující názvy jsou odkazy na jejich odpovídající s poznámkami v dalších částech tohoto článku:

- [1. **(Spustit)**  Úvodní snímek, 0:00:03](#anchor-image-wtip-min00001)
- [2. Cíle relace, 0:03:11](#anchor-image-wtip-min00311)
- [3. Program, 0:04:17](#anchor-image-wtip-min00417)
- [4. Víceklientské aplikaci, 0:05:05](#anchor-image-wtip-min00505)
- [5. Formulář webové aplikace v akci, 0:05:55](#anchor-image-wtip-min00555)
- [6. Náklady (škálování, izolaci, obnovení), 0 na tenanta: 09:31](#anchor-image-wtip-min00931)
- [7. Databáze pro více tenantů modely: výhody a nevýhody, 0:11:59](#anchor-image-wtip-min01159)
- [8. Hybridního modelu sobě spojuje výhody MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. Víceklientské vs s jedním tenantem: výhody a nevýhody, 0:16:44](#anchor-image-wtip-min01644)
- [10. Fondy jsou cenově výhodné pro nepředvídatelnými úlohami, 0:19:36](#anchor-image-wtip-min01936)
- [11. Ukázkové databáze na tenanta a hybridní ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Živé aplikace formulář, který zobrazuje Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB a ne DBA zřejmý, 0:28:54](#anchor-image-wtip-min02854)
- [14. Příklad použití elastického fondu MYOB, 0:29:40](#anchor-image-wtip-min02940)
- [15. Učení z MYOB a další nezávislí dodavatelé softwaru, 0:31:36](#anchor-image-wtip-min03136)
- [16. Vzory sestavit do scénáře E2E SaaS, 0:43:15](#anchor-image-wtip-min04315)
- [17. Canonical hybridní víceklientskou aplikaci SaaS, 0:47:33](#anchor-image-wtip-min04733)
- [18. Ukázkové aplikace Wingtip SaaS, 0:48:10](#anchor-image-wtip-min04810)
- [19. Scénářům a vzorcům prozkoumali v kurzech, 0:49:10](#anchor-image-wtip-min04910)
- [20. Ukázka výukových kurzů a úložišti GitHub, 0:50:18](#anchor-image-wtip-min05018)
- [21. Úložiště GitHub se vzorovými Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Prozkoumání vzorců, 0:56:20](#anchor-image-wtip-min05620)
- [23. Zřizování tenantů a připojování, 0:57:44](#anchor-image-wtip-min05744)
- [24. Zřizování tenantů a připojení aplikace, 0:58:58](#anchor-image-wtip-min05858)
- [25. Ukázka správy skripty zřizování jednoho tenanta, 0:59:43](#anchor-image-wtip-min05943)
- [26. Prostředí PowerShell ke zřízení a katalog, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * z TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Správa úloh tenanta nepředvídatelný, 1:04:36](#anchor-image-wtip-min10436)
- [29. Elastický fond monitorování, 1:06:39](#anchor-image-wtip-min10639)
- [30. Generování zatížení a výkonu, monitorování, 1:09:42](#anchor-image-wtip-min10942)
- [31. Správa schématu ve velkém měřítku, 1:10:33](#anchor-image-wtip-min11033)
- [32. Distribuovaný dotaz napříč databází tenantů, 1:12:21](#anchor-image-wtip-min11221)
- [33. Ukázka lístků generace 1:12:32](#anchor-image-wtip-min11232)
- [34. SSMS adhoc analytics, 1:12:46](#anchor-image-wtip-min11246)
- [35. Extrahovat data tenanta do SQL data Warehouse, 1:16:32](#anchor-image-wtip-min11632)
- [36. Graf denní distribuce prodej, 1:16:48](#anchor-image-wtip-min11648)
- [37. Zabalit a výzva k akci, 1:19:52](#anchor-image-wtip-min11952)
- [38. Prostředky pro další informace, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Umístění indexu s poznámkami času ve videu

Kliknutím na jakýkoli obrázek snímku obrazovky vás nasměruje na umístění přesný čas ve videu.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Spustit)*  Úvodní snímek, 0:00:01

*Učení z MYOB: Vzory návrhu pro aplikace SaaS v Azure SQL Database – BRK3120*

[![Úvodní snímek][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Název: Učení z MYOB: Vzory návrhu pro aplikace SaaS v Azure SQL Database
- Bill.Gibson@microsoft.com
- Hlavní programový manažer, Azure SQL Database
- Relace Microsoft Ignite BRK3120, Orlando, USA USA, 11. října 2017


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Cíle relace, 0:01:53
[![Cíle relace][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternativní vzory pro aplikace s více tenanty, se výhody a nevýhody.
- Vzorce SaaS snižuje náklady na vývoj, správu a prostředků.
- Ukázkové aplikace a skripty.
- Funkce PaaS a SaaS vzory Ujistěte se, SQL Database nákladově efektivní, vysoce škálovatelnou datovou platformu pro více tenantů v SaaS.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Program, 0:04:09
[![Program][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Víceklientské aplikaci, 0:05:00
[![SaaS aplikace Wingtip: Víceklientské webová aplikace][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. Formulář webové aplikace v akci, 0:05:39
[![Formulář webové aplikace v akci][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Náklady (škálování, izolaci, obnovení), 0 na tenanta: 06:58
[![Na tenanta náklady, škálování, izolaci, obnovení][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Databáze pro více tenantů modely: výhody a nevýhody, 0:09:52
[![Databáze pro více tenantů modely: výhody a nevýhody][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Hybridního modelu sobě spojuje výhody MT/ST, 0:12:29
[![Hybridního modelu sobě spojuje výhody MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Víceklientské vs s jedním tenantem: výhody a nevýhody, 0:13:11
[![Víceklientské vs s jedním tenantem: výhody a nevýhody][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Fondy jsou cenově výhodné pro nepředvídatelnými úlohami, 0:17:49
[![Fondy jsou cenově výhodné pro nepředvídatelnými úlohami][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Ukázkové databáze na tenanta a hybridní ST/MT, 0:19:59
[![Ukázkové databáze na tenanta a hybridní ST/MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Živé aplikace formulář, který zobrazuje Dojo, 0:20:10
[![Formulář živé aplikaci zobrazující Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB a ne DBA zřejmý, 0:25:06
[![Není DBA zřejmý a MYOB][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. Příklad použití elastického fondu MYOB, 0:29:30
[![Příklad použití elastického fondu MYOB][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Učení z MYOB a další nezávislí dodavatelé softwaru, 0:31:25
[![Učení z MYOB a další nezávislí dodavatelé softwaru][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Vzory sestavit do scénáře E2E SaaS, 0:31:42
[![Vzory sestavit do scénáře E2E SaaS][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Canonical hybridní víceklientskou aplikaci SaaS, 0:46:04
[![Canonical hybridní víceklientskou aplikaci SaaS][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Ukázkové aplikace Wingtip SaaS, 0:48:01
[![Ukázková aplikace SaaS aplikace Wingtip][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Scénářům a vzorcům prozkoumali v kurzech, 0:49:00
[![Scénáře a prozkoumali jste ji v kurzech vzory][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Ukázka výukových kurzů a úložišti GitHub, 0:50:12
[![Ukázky, kurzy a úložišti na Githubu][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. Úložiště GitHub se vzorovými Microsoft/WingtipSaaS, 0:50:32
[![Úložiště GitHub se vzorovými Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. Prozkoumání vzorců, 0:56:15
[![Tyto vzory se dají zkoumat][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Zřizování tenantů a připojování, 0:56:19
[![Zřizování tenantů a připojování][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Zřizování tenantů a připojení aplikace, 0:57:52
[![Zřizování tenantů a připojení aplikace][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Ukázka správy skripty zřizování jednoho tenanta, 0:59:36
[![Ukázky skriptů pro správu zřizování jednoho tenanta][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. Prostředí PowerShell ke zřízení a katalog, 0:59:56
[![Prostředí PowerShell ke zřízení a katalog][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * z TenantsExtended, 1:03:25
[![T-SQL SELECT * z TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Správa úloh tenanta nepředvídatelný, 1:03:34
[![Správa úloh tenanta nepředvídatelný][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Elastický fond monitorování, 1:06:32
[![Sledování elastického fondu][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Generování zatížení a výkonu, monitorování, 1:09:37
[![Generování zátěže a sledování výkonu][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Správa schématu ve velkém měřítku, 1:09:40
[![Správa schématu ve velkém měřítku][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Distribuovaný dotaz napříč databází tenantů, 1:11:18
[![Distribuovaný dotaz napříč databází tenantů][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Ukázka lístků generace 1:12:28
[![Ukázka lístků generace][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS adhoc analytics, 1:12:35
[![Ad hoc analýzy aplikace SSMS][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Extrahovat data tenanta do SQL data Warehouse, 1:15:46
[![Extrahovat data tenanta do SQL data Warehouse][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Graf denní distribuce prodej, 1:16:38
[![Graf denní distribuce prodej][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Zabalit a výzva k akci, 1:17:43
[![Zabalit a výzva k akci][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Prostředky pro další informace, 1:20:35
[![Prostředky pro další informace][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Blogový příspěvek, 22. května 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Koncepční:* [Vzory víceklientské SaaS databáze tenantů][saas-concept-design-patterns-563e]

- *Kurzy:* [Aplikace SaaS aplikace Wingtip Tickets][saas-how-welcome-wingtip-app-679t]

- Úložiště GitHub pro tenantů aplikací SaaS aplikace Wingtip Tickets charakteristikami:
    - [Úložiště GitHub pro - samostatnou aplikační model][github-wingtip-standaloneapp].
    - [Úložiště GitHub pro – model databáze na Tenanta][github-wingtip-dbpertenant].
    - [Úložiště GitHub pro - DB Víceklientského modelu][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Další postup

- [První kurz článek][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Úvodní snímek"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Cíle relace."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Program."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "SaaS aplikace Wingtip: Víceklientské webová aplikace"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Formulář webové aplikace v akci"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Na tenanta náklady, škálování, izolaci, obnovení"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Databáze pro více tenantů modely: výhody a nevýhody"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Hybridního modelu sobě spojuje výhody MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Víceklientské vs s jedním tenantem: výhody a nevýhody"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Fondy jsou cenově výhodné pro nepředvídatelnými úlohami"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Ukázkové databáze na tenanta a hybridní ST/MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Formulář živé aplikaci zobrazující Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "Není DBA zřejmý a MYOB"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Příklad použití elastického fondu MYOB"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Učení z MYOB a další nezávislí dodavatelé softwaru"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Vzory sestavit do scénáře E2E SaaS"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Canonical hybridní víceklientskou aplikaci SaaS"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Ukázková aplikace SaaS aplikace Wingtip"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Scénáře a prozkoumali jste ji v kurzech vzory"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Ukázka výukových kurzů a úložišti na Githubu"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "Úložiště GitHub se vzorovými Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Tyto vzory se dají zkoumat"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Zřizování tenantů a připojování"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Zřizování tenantů a připojení aplikace"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Ukázky skriptů pro správu zřizování jednoho tenanta"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "Prostředí PowerShell ke zřízení a katalog"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * z TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Správa úloh tenanta nepředvídatelný"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Sledování elastického fondu"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Generování zátěže a sledování výkonu"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Správa schématu ve velkém měřítku"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Distribuovaný dotaz napříč databází tenantů"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Ukázka lístků generace"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "Ad hoc analýzy aplikace SSMS"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Extrahovat data tenanta do SQL data Warehouse"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Graf denní distribuce prodej"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Zabalit a výzva k akci"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Prostředky pro další informace"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

