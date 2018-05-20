---
title: Indexované video, Azure SaaS SQL aplikace | Microsoft Docs
description: Tento článek indexuje různé body čas v našem 81 minut video o návrhu aplikace klientů SaaS DB, z konference Ignite uchovávat 11 říjen 2017. Můžete přeskočit k části, která vás zajímá. Minimálně 3 vzory jsou popsány. Jsou popsané funkce Azure, které zjednodušují vývoj a správu.
services: sql-database
ms.date: 05/14/2018
ms.service: sql-database
ms.reviewer: billgib
ms.topic: article
author: MightyPen
ms.author: genemi
manager: craigg
ms.openlocfilehash: 4ea62855b61cb7439b19204564cbc2d7fcdbd0fa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Video indexované a opatřeny poznámkami pro víceklientské aplikace SaaS pomocí Azure SQL Database

Tento článek je poznámkami index do umístění čas 81 zápisu video o SaaS klientů modely nebo vzory. Tento článek vám umožňuje přeskočit zpátky nebo předání v videa a jaká část vás zajímá. Video popisuje možnosti hlavní návrhu aplikace víceklientské databáze v databázi SQL Azure. Video obsahuje ukázky, návody kódu správy a v některých případech další podrobnosti o tom informováni prostřednictvím prostředí, než může být v dokumentaci napsané.

Video zesílí informace v dokumentaci napsané nachází zde: 
- *Konceptuální:* [SaaS víceklientské databáze vzory klientů][saas-concept-design-patterns-563e]
- *Kurzy:* [Wingtip lístky SaaS aplikace][saas-how-welcome-wingtip-app-679t]

Přehrávání videa a články popisují mnoho fáze vytváření víceklientské aplikace v databázi SQL Azure v cloudu. Speciální funkce Azure SQL Database usnadňují vývoj a implementovat víceklientské aplikace, které jsou obě snazší správa a spolehlivě původce.

Pravidelně aktualizujeme napsané dokumentaci. Video není upravit nebo aktualizovat, takže nakonec další jeho podrobností může začnou být zastaralé.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Pořadí 38 indexované čas snímky obrazovky

Tato část indexuje čas umístění 38 diskusí v celé video 81 minutu. Každý index čas je poznámkou snímek z videa a někdy společně s dalšími informacemi.

Každý index čas je ve formátu *SS*. Například druhý indexované čas umístění s názvem bez přípony **relace cíle**, spustí na umístění Přibližná doba, po **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Compact odkazy na video indexované čas umístění

Následující názvy jsou odkazy na jejich odpovídající části s poznámkami později v tomto článku:

- [1. **(Spustit)**  Úvodní snímek, 0:00:03](#anchor-image-wtip-min00001)
- [2. Cíle relace, 0:03:11](#anchor-image-wtip-min00311)
- [3. Program schůzky, 0:04:17](#anchor-image-wtip-min00417)
- [4. Víceklientské webové aplikace, 0:05:05](#anchor-image-wtip-min00505)
- [5. Aplikace webového formuláře v akci, 0:05:55](#anchor-image-wtip-min00555)
- [6. Náklady (rozsah, izolaci, obnovení), 0 pro klienta: 09:31](#anchor-image-wtip-min00931)
- [7. Databáze modely pro více klientů: výhody a nevýhody, 0:11:59](#anchor-image-wtip-min01159)
- [8. Hybridní modelu smíchá výhod MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. Jednoho klienta vs víceklientské: výhody a nevýhody, 0:16:44](#anchor-image-wtip-min01644)
- [10. Fondy jsou nákladově efektivní pro nepředvídatelným úlohy, 0:19:36](#anchor-image-wtip-min01936)
- [11. Ukázkové databáze za klienta a hybridní ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Live aplikace formulář, který zobrazuje Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB a není DBA nebyl zřejmý, 0:28:54](#anchor-image-wtip-min02854)
- [14. Příklad použití MYOB elastického fondu, 0:29:40](#anchor-image-wtip-min02940)
- [15. Učení z MYOB a dalších. nezávislí dodavatelé softwaru 0:31:36](#anchor-image-wtip-min03136)
- [16. Vzory tvoří do scénáři E2E SaaS 0:43:15](#anchor-image-wtip-min04315)
- [17. Aplikace SaaS kanonický hybridní víceklientské, 0:47:33](#anchor-image-wtip-min04733)
- [18. Adresář Wingtip SaaS ukázkovou aplikaci, 0:48:10](#anchor-image-wtip-min04810)
- [19. Scénáře a vzory prozkoumali v kurzech, 0:49:10](#anchor-image-wtip-min04910)
- [20. Ukázku kurzy a úložiště Github, 0:50:18](#anchor-image-wtip-min05018)
- [21. Úložiště Github Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Zkoumání vzory, 0:56:20](#anchor-image-wtip-min05620)
- [23. Zřizování klientů a registrace, 0:57:44](#anchor-image-wtip-min05744)
- [24. Zřizování klientů a připojení aplikace, 0:58:58](#anchor-image-wtip-min05858)
- [25. Ukázka správy skriptů zřizování jednoho klienta, 0:59:43](#anchor-image-wtip-min05943)
- [26. Prostředí PowerShell pro zřizování a katalog, 1:00:02](#anchor-image-wtip-min10002)
- [27. Vyberte T-SQL * z TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Správa nepředvídatelným klientské úlohy, 1:04:36](#anchor-image-wtip-min10436)
- [29. Elastický fond monitorování, 1:06:39](#anchor-image-wtip-min10639)
- [30. Generování zátěže a výkonu monitorování, 1:09:42](#anchor-image-wtip-min10942)
- [31. Správa schématu v rozsahu 1:10:33](#anchor-image-wtip-min11033)
- [32. Distribuovaného dotazu mezi databázemi klienta, 1:12:21](#anchor-image-wtip-min11221)
- [33. Ukázku generování lístku, 1:12:32](#anchor-image-wtip-min11232)
- [34. Aplikace SSMS ad hoc analýzy, 1:12:46](#anchor-image-wtip-min11246)
- [35. Extrakce dat klienta do datového skladu SQL, 1:16:32](#anchor-image-wtip-min11632)
- [36. Graf denní distribuční prodej, 1:16:48](#anchor-image-wtip-min11648)
- [37. Zabalení a výzva k akci, 1:19:52](#anchor-image-wtip-min11952)
- [38. Prostředky pro další informace, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Index s poznámkami čas umístění ve videu

Kliknutím na žádný obrázek snímek přejdete do umístění přesný čas na videu.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Spustit)*  Úvodní snímek, 0:00:01

*Učení z MYOB: vzory pro SaaS aplikace na Azure SQL Database – BRK3120 návrhu*

[![Úvodní snímek][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Title: Učení z MYOB: návrh schématech aplikací SaaS v databázi SQL Azure
- Bill.Gibson@microsoft.com
- Hlavní programový manažer, databáze Azure SQL
- Relace Microsoft Ignite BRK3120, Orlandu, FL USA, 2017 říjen 11.


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Cíle relace, 0:01:53
[![Relace cíle][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternativní vzory pro víceklientské aplikace s výhody a nevýhody.
- Vzory SaaS snižuje náklady na vývoj, správu a prostředků.
- Ukázková aplikace + skripty.
- Funkce PaaS + SaaS vzory zkontrolujte SQL Database vysoce škálovatelnou a cenově efektivní datovou platformu pro více klientů SaaS.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Program schůzky, 0:04:09
[![Program schůzky][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Víceklientské webové aplikace, 0:05:00
[![Aplikace SaaS Wingtip: víceklientské webové aplikace][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. Aplikace webového formuláře v akci, 0:05:39
[![Aplikace webového formuláře v akci][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Náklady (rozsah, izolaci, obnovení), 0 pro klienta: 06:58
[![Za klienta náklady, škálování, izolaci, obnovení][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Databáze modely pro více klientů: výhody a nevýhody, 0:09:52
[![Databáze modely pro více klientů: výhody a nevýhody][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Hybridní modelu smíchá výhod MT/ST, 0:12:29
[![Hybridní modelu smíchá výhod MT nebo ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Jednoho klienta vs víceklientské: výhody a nevýhody, 0:13:11
[![Jednoho klienta vs víceklientské: výhody a nevýhody][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Fondy jsou nákladově efektivní pro nepředvídatelným úlohy, 0:17:49
[![Fondy jsou nákladově efektivní pro nepředvídatelným úlohy][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Ukázkové databáze za klienta a hybridní ST/MT, 0:19:59
[![Ukázkové databáze za klienta a hybridní ST/MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Live aplikace formulář, který zobrazuje Dojo, 0:20:10
[![Za provozu aplikace formulář, který zobrazuje Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB a není DBA nebyl zřejmý, 0:25:06
[![MYOB a není DBA nebyl zřejmý][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. Příklad použití MYOB elastického fondu, 0:29:30
[![Příklad použití MYOB elastického fondu][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Učení z MYOB a dalších. nezávislí dodavatelé softwaru 0:31:25
[![Učení se z MYOB a další nezávislí dodavatelé softwaru][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Vzory tvoří do scénáři E2E SaaS 0:31:42
[![Vzory tvoří do scénářů E2E SaaS][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Aplikace SaaS kanonický hybridní víceklientské, 0:46:04
[![Kanonický hybridní víceklientské SaaS aplikace][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Adresář Wingtip SaaS ukázkovou aplikaci, 0:48:01
[![Adresář Wingtip SaaS ukázkové aplikace][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Scénáře a vzory prozkoumali v kurzech, 0:49:00
[![Scénáře a vzory prozkoumali v kurzů k][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Ukázku kurzy a úložiště Github, 0:50:12
[![Ukázkový kurzy a úložiště Github][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. Úložiště Github Microsoft/WingtipSaaS, 0:50:32
[![Úložiště Github Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. Zkoumání vzory, 0:56:15
[![Zkoumání vzory][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Zřizování klientů a registrace, 0:56:19
[![Zřizování klientů a registrace][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Zřizování klientů a připojení aplikace, 0:57:52
[![Zřizování klientů a připojení aplikace][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Ukázka správy skriptů zřizování jednoho klienta, 0:59:36
[![Ukázkové skripty správy zřizování jednoho klienta][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. Prostředí PowerShell pro zřizování a katalog, 0:59:56
[![Prostředí PowerShell pro zřizování a katalog][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. Vyberte T-SQL * z TenantsExtended, 1:03:25
[![Vyberte T-SQL * z TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Správa nepředvídatelným klientské úlohy, 1:03:34
[![Správa nepředvídatelným klientské úlohy][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Elastický fond monitorování, 1:06:32
[![Monitorování elastického fondu][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Generování zátěže a výkonu monitorování, 1:09:37
[![Generování zatížení a sledování výkonu][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Správa schématu v rozsahu 1:09:40
[![Správa schématu ve velkém měřítku][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Distribuovaného dotazu mezi databázemi klienta, 1:11:18
[![Distribuovaného dotazu mezi databázemi klienta][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Ukázku generování lístku, 1:12:28
[![Ukázku generování lístku][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. Aplikace SSMS ad hoc analýzy, 1:12:35
[![Analýza aplikace SSMS ad hoc][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Extrakce dat klienta do datového skladu SQL, 1:15:46
[![Extrakce dat klienta do datového skladu SQL][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Graf denní distribuční prodej, 1:16:38
[![Graf denní prodej distribuce][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Zabalení a výzva k akci, 1:17:43
[![Zabalení a výzva k akci][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Prostředky pro další informace, 1:20:35
[![Prostředky pro další informace][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Příspěvku na blogu 22 může 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Konceptuální:* [SaaS víceklientské databáze vzory klientů][saas-concept-design-patterns-563e]

- *Kurzy:* [Wingtip lístky SaaS aplikace][saas-how-welcome-wingtip-app-679t]

- Úložišť Github pro typů aplikace SaaS lístky Wingtip klientů:
    - [Úložiště Github pro - samostatné aplikace model][github-wingtip-standaloneapp].
    - [Úložiště Github pro - DB za klienta model][github-wingtip-dbpertenant].
    - [Úložiště Github pro - víceklientské DB – model][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Další postup

- [První kurz článku][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Úvodní snímek"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Relace cíle."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Program jednání."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Aplikace SaaS Wingtip: víceklientské webové aplikace"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Aplikace webového formuláře v akci"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Za klienta náklady, škálování, izolaci, obnovení"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Databáze modely pro více klientů: výhody a nevýhody"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Hybridní modelu smíchá výhod MT nebo ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Jednoho klienta vs víceklientské: výhody a nevýhody"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Fondy jsou nákladově efektivní pro nepředvídatelným úlohy"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Ukázkové databáze za klienta a hybridní ST/MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Za provozu aplikace formulář, který zobrazuje Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB a není DBA nebyl zřejmý"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Příklad použití MYOB elastického fondu"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Učení se z MYOB a další nezávislí dodavatelé softwaru"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Vzory tvoří do scénářů E2E SaaS"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Kanonický hybridní víceklientské SaaS aplikace"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Adresář Wingtip SaaS ukázkové aplikace"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Scénáře a vzory prozkoumali v kurzů k"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Ukázku kurzy a úložiště Github"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "Úložiště Github Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Zkoumání vzory"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Zřizování klientů a registrace"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Zřizování klientů a připojení aplikace"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Ukázkové skripty správy zřizování jednoho klienta"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "Prostředí PowerShell pro zřizování a katalog"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "Vyberte T-SQL * z TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Správa nepředvídatelným klientské úlohy"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Monitorování elastického fondu"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Generování zatížení a sledování výkonu"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Správa schématu ve velkém měřítku"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Distribuovaného dotazu mezi databázemi klienta"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Ukázku generování lístku"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "Analýza aplikace SSMS ad hoc"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Extrakce dat klienta do datového skladu SQL"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Graf denní prodej distribuce"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Zabalení a výzva k akci"

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

