---
title: Video aplikace SaaS SQL
description: Tento článek indexuje různé časové body v našem videu 81 minut o návrhu aplikace SaaS DB tenantů, od konference Ignite, která se drží 11. října 2017. Můžete přeskočit na část, která vás zajímá. Jsou popsány alespoň 3 vzory. Popisujeme funkce Azure, které zjednodušují vývoj a správu.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.custom: sqldbrb=1
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 8cceeef7e41e8fc5c48b4e67cdb1adf3aa4f02a1
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89483860"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Video indexované a s poznámkami pro víceklientské aplikace SaaS pomocí Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek je komentovaný index do časových umístění s 81 minutou videa o modelech a vzorcích architektury SaaS. Tento článek vám umožní přeskočit zpět nebo dopředu ve videu, na které vás podílí. Video vysvětluje hlavní možnosti návrhu aplikace pro více tenantů databází v Azure SQL Database. Video obsahuje ukázky, návody z kódu pro správu a v době, kdy se podrobněji informují o zkušenostech, než se může jednat o naši písemnou dokumentaci.

Video se dokončí v naší písemné dokumentaci, která se nachází na adrese: 
- *Koncepční:* [modely tenantů SaaS Database pro více tenantů][saas-concept-design-patterns-563e]
- *Kurzy:* [aplikace Wingtip vstupenky SaaS][saas-how-welcome-wingtip-app-679t]

Video a články popisují mnoho fází vytváření víceklientské aplikace v Azure SQL Database v cloudu. Speciální funkce Azure SQL Database usnadňují vývoj a implementaci aplikací pro více tenantů, které jsou jednodušší pro správu a spolehlivé provádění.

Pravidelně aktualizujeme naši písemnou dokumentaci. Video se neupravuje ani neaktualizuje, takže jeho podrobnosti můžou být zastaralá.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Sekvence snímků obrazovky s časovým indexem 38

V této části se indexuje časové umístění pro 38 diskuze během až 81 minutového videa. Pokaždé, když je index poopatřený snímekem obrazovky z videa a někdy s dalšími informacemi.

Každý časový index má formát *h:mm: SS*. Například druhé indexované časové umístění, které je označeno jako **cíle relace**, začíná přibližným časovým umístěním **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Kompaktní odkazy na umístění indexovaných časů videa

Následující názvy odkazují na jejich odpovídající části s poznámkami dále v tomto článku:

- [1. **(začátek)** úvodní snímek, 0:00:03](#anchor-image-wtip-min00001)
- [2. cíle relace, 0:03:11](#anchor-image-wtip-min00311)
- [3. Agenda, 0:04:17](#anchor-image-wtip-min00417)
- [4. webová aplikace s více klienty, 0:05:05](#anchor-image-wtip-min00505)
- [5. webový formulář aplikace v akci, 0:05:55](#anchor-image-wtip-min00555)
- [6. náklady na tenanta (škálování, izolace, obnovení), 0:09:31](#anchor-image-wtip-min00931)
- [7. databázové modely pro víceklientské klienty: specialisté a nevýhody, 0:11:59](#anchor-image-wtip-min01159)
- [8. hybridní model se smíchá s výhodami MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. jeden tenant vs – tenant: specialisté a nevýhody, 0:16:44](#anchor-image-wtip-min01644)
- [10. fondy jsou nákladově efektivní pro nepředvídatelné úlohy, 0:19:36](#anchor-image-wtip-min01936)
- [11. Ukázka databáze na tenanta a hybridní ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Live – formulář aplikace zobrazující Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB a ne DBA v pohledu, 0:28:54](#anchor-image-wtip-min02854)
- [14. Příklad použití elastického fondu MYOB, 0:29:40](#anchor-image-wtip-min02940)
- [15. učení od MYOB a jiného nezávislého výrobce softwaru, 0:31:36](#anchor-image-wtip-min03136)
- [16. vzory, které tvoří scénář E2E SaaS, 0:43:15](#anchor-image-wtip-min04315)
- [17. kanonická hybridní aplikace pro více tenantů SaaS, 0:47:33](#anchor-image-wtip-min04733)
- [18. SaaS vzorová aplikace Wingtip, 0:48:10](#anchor-image-wtip-min04810)
- [19. scénáře a vzory prozkoumané v kurzech, 0:49:10](#anchor-image-wtip-min04910)
- [20. Ukázka kurzů a úložiště GitHub, 0:50:18](#anchor-image-wtip-min05018)
- [21. GitHub úložiště Microsoft/úložišti wingtipsaas, 0:50:38](#anchor-image-wtip-min05038)
- [22. zkoumání vzorů, 0:56:20](#anchor-image-wtip-min05620)
- [23. zřizování klientů a registraci, 0:57:44](#anchor-image-wtip-min05744)
- [24. zřizování klientů a připojení aplikací, 0:58:58](#anchor-image-wtip-min05858)
- [25. Ukázka skriptů pro správu, které provisionují jeden tenant, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell ke zřízení a katalogu, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Správa nepředvídatelných úloh tenanta, 1:04:36](#anchor-image-wtip-min10436)
- [29. monitorování elastického fondu, 1:06:39](#anchor-image-wtip-min10639)
- [30. generování zátěže a monitorování výkonu, 1:09:42](#anchor-image-wtip-min10942)
- [31. Správa schématu ve velkém měřítku, 1:10:33](#anchor-image-wtip-min11033)
- [32. distribuovaný dotaz napříč databázemi klientů, 1:12:21](#anchor-image-wtip-min11221)
- [33. Ukázka generování lístku, 1:12:32](#anchor-image-wtip-min11232)
- [34 SSMS ad hoc Analytics, 1:12:46](#anchor-image-wtip-min11246)
- [35. extrahujte data tenanta do služby Azure synapse Analytics, 1:16:32](#anchor-image-wtip-min11632)
- [36. graf denní distribuce prodeje, 1:16:48](#anchor-image-wtip-min11648)
- [37. zabalení a volání akce, 1:19:52](#anchor-image-wtip-min11952)
- [38. prostředků pro další informace, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Umístění indexu s poznámkou ve videu

Kliknutím na libovolný snímek obrazovky přejdete do přesného časového umístění ve videu.


&nbsp; <a name="anchor-image-wtip-min00001"></a>
#### <a name="1-start-welcome-slide-00001"></a>1. *(začátek)* úvodní snímek, 0:00:01

*Učení z MYOB: vzory návrhu pro SaaS aplikace na Azure SQL Database – BRK3120*

[![Uvítací snímek][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Title: učení z MYOB: vzory návrhu pro SaaS aplikace na Azure SQL Database
- Bill.Gibson@microsoft.com
- Hlavní správce programů, Azure SQL Database
- Microsoft Ignite Session BRK3120, Orlandu, FL USA, říjen/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"></a>
#### <a name="2-session-objectives-00153"></a>2. cíle relace, 0:01:53
[![Cíle relace][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternativní modely pro víceklientské aplikace s využitím specialistů a nevýhody.
- SaaS vzory pro snížení nákladů na vývoj, správu a prostředky.
- Ukázková aplikace a skripty
- PaaS Features a SaaS Patterns umožňují SQL Database vysoce škálovatelnou a cenově výhodnější datovou platformu pro více tenantů SaaS.


&nbsp; <a name="anchor-image-wtip-min00417"></a>
#### <a name="3-agenda-00409"></a>3. Agenda, 0:04:09
[![Program][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"></a>
#### <a name="4-multi-tenant-web-app-00500"></a>4. webová aplikace s více klienty, 0:05:00
[![Aplikace Wingtip SaaS: webová aplikace s více klienty][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"></a>
#### <a name="5-app-web-form-in-action-00539"></a>5. webový formulář aplikace v akci, 0:05:39
[![Webový formulář aplikace v akci][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"></a>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. náklady na tenanta (škálování, izolace, obnovení), 0:06:58
[![Náklady na tenanta, škálování, izolaci, obnovení][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"></a>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. databázové modely pro víceklientské klienty: specialisté a nevýhody, 0:09:52
[![Databázové modely pro více tenantů: specialisté a nevýhody][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"></a>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. hybridní model se smíchá s výhodami MT/ST, 0:12:29
[![Hybridní model – výhody MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"></a>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. jeden tenant vs – tenant: specialisté a nevýhody, 0:13:11
[![Jeden tenant vs – tenant: specialisté a nevýhody][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"></a>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. fondy jsou nákladově efektivní pro nepředvídatelné úlohy, 0:17:49
[![Fondy jsou nákladově efektivní pro nepředvídatelné úlohy.][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"></a>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Ukázka databáze na tenanta a hybridní ST/MT, 0:19:59
[![Ukázka databáze na tenanta a hybridní ST/MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"></a>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Live – formulář aplikace zobrazující Dojo, 0:20:10
[![Formulář živé aplikace zobrazující Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"></a>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB a ne DBA v pohledu, 0:25:06
[![MYOB a ne DBA v pohledu][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"></a>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. Příklad použití elastického fondu MYOB, 0:29:30
[![Příklad použití elastického fondu MYOB][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"></a>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. učení od MYOB a jiného nezávislého výrobce softwaru, 0:31:25
[![Učení od MYOB a jiného nezávislého výrobce softwaru][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"></a>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. vzory, které tvoří scénář E2E SaaS, 0:31:42
[![Vzorce, které se vytvářejí ve scénáři E2E SaaS][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"></a>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. kanonická hybridní aplikace pro více tenantů SaaS, 0:46:04
[![Kanonická hybridní aplikace pro více tenantů SaaS][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"></a>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. SaaS vzorová aplikace Wingtip, 0:48:01
[![Ukázková aplikace Wingtip SaaS][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"></a>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. scénáře a vzory prozkoumané v kurzech, 0:49:00
[![Scénáře a vzory prozkoumání v kurzech][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"></a>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Ukázka kurzů a úložiště GitHub, 0:50:12
[![Ukázkové kurzy a úložiště GitHub][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"></a>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub úložiště Microsoft/úložišti wingtipsaas, 0:50:32
[![Úložiště GitHub Microsoft/úložišti wingtipsaas][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"></a>
#### <a name="22-exploring-the-patterns-05615"></a>22. zkoumání vzorů, 0:56:15
[![Zkoumání vzorů][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"></a>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. zřizování klientů a registraci, 0:56:19
[![Zřizování klientů a připojování][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"></a>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. zřizování klientů a připojení aplikací, 0:57:52
[![Zřizování klientů a připojení aplikací][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"></a>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Ukázka skriptů pro správu, které provisionují jeden tenant, 0:59:36
[![Ukázka skriptů pro správu, které zřídí jednoho tenanta][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"></a>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell ke zřízení a katalogu, 0:59:56
[![PowerShell ke zřízení a katalogu][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"></a>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![T-SQL SELECT * FROM TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"></a>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Správa nepředvídatelných úloh tenanta, 1:03:34
[![Správa nepředvídatelných úloh tenanta][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"></a>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. monitorování elastického fondu, 1:06:32
[![Monitorování elastického fondu][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"></a>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. generování zátěže a monitorování výkonu, 1:09:37
[![Generování zátěže a sledování výkonu][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"></a>
#### <a name="31-schema-management-at-scale-10940"></a>31. Správa schématu ve velkém měřítku, 1:09:40
[![Správa schématu ve velkém měřítku][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"></a>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. distribuovaný dotaz napříč databázemi klientů, 1:11:18
[![Distribuovaný dotaz napříč databázemi klientů][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"></a>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Ukázka generování lístku, 1:12:28
[![Ukázka generování lístku][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"></a>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34 SSMS ad hoc Analytics, 1:12:35
[![SSMS analýzy ad hoc][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"></a>
#### <a name="35-extract-tenant-data-into-azure-synapse-analytics-11546"></a>35. extrahujte data tenanta do služby Azure synapse Analytics, 1:15:46
[![Extrakce dat tenanta do Azure synapse Analytics][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"></a>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. graf denní distribuce prodeje, 1:16:38
[![Graf denní distribuce prodeje][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"></a>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. zabalení a volání akce, 1:17:43
[![Zabalení a volání akce][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"></a>
#### <a name="38-resources-for-more-information-12035"></a>38. prostředků pro další informace, 1:20:35
[![Prostředky pro další informace][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Blogový příspěvek, květen 22. května 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Koncepční:* [modely tenantů SaaS Database pro více tenantů][saas-concept-design-patterns-563e]

- *Kurzy:* [aplikace Wingtip vstupenky SaaS][saas-how-welcome-wingtip-app-679t]

- Úložiště GitHub pro charakter aplikace Wingtip Tickets SaaS tenantů:
    - [Úložiště GitHub pro samostatný aplikační model][github-wingtip-standaloneapp]
    - [Úložiště GitHub pro model databáze na úrovni tenanta][github-wingtip-dbpertenant]
    - [Úložiště GitHub pro model databáze s více klienty][github-wingtip-multitenantdb]





## <a name="next-steps"></a>Další kroky

- [První článek kurzu][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Uvítací snímek"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Cíle relace."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Program."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Aplikace Wingtip SaaS: webová aplikace s více klienty"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Webový formulář aplikace v akci"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Náklady na tenanta, škálování, izolaci, obnovení"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Databázové modely pro více tenantů: specialisté a nevýhody"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Hybridní model – výhody MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Jeden tenant vs – tenant: specialisté a nevýhody"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Fondy jsou nákladově efektivní pro nepředvídatelné úlohy."

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Ukázka databáze na tenanta a hybridní ST/MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Formulář živé aplikace zobrazující Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB a ne DBA v pohledu"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Příklad použití elastického fondu MYOB"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Učení od MYOB a jiného nezávislého výrobce softwaru"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Vzorce, které se vytvářejí ve scénáři E2E SaaS"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Kanonická hybridní aplikace pro více tenantů SaaS"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Ukázková aplikace Wingtip SaaS"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Scénáře a vzory prozkoumání v kurzech"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Ukázka kurzů a úložiště GitHub"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "Úložiště GitHub Microsoft/úložišti wingtipsaas"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Zkoumání vzorů"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Zřizování klientů a připojování"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Zřizování klientů a připojení aplikací"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Ukázka skriptů pro správu, které zřídí jednoho tenanta"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell ke zřízení a katalogu"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * FROM TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Správa nepředvídatelných úloh tenanta"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Monitorování elastického fondu"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Generování zátěže a sledování výkonu"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Správa schématu ve velkém měřítku"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Distribuovaný dotaz napříč databázemi klientů"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Ukázka generování lístku"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS analýzy ad hoc"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Extrakce dat tenanta do Azure synapse Analytics"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Graf denní distribuce prodeje"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Zabalení a volání akce"

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

