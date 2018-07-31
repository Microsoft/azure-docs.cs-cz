---
title: Protokoly aktivit Azure Active Directory ve službě Azure Monitor (Preview) | Microsoft Docs
description: Přehled protokolů aktivit Azure Active Directory ve službě Azure Monitor (Preview)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 9b594360bc760fa9eec8ab9900c3aadcb10e9db6
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240100"
---
# <a name="azure-active-directory-activity-logs-in-azure-monitor-preview"></a>Protokoly aktivit Azure Active Directory ve službě Azure Monitor (Preview)

Teď můžete pomocí služby Azure Monitor směrovat protokoly aktivit Azure AD do vlastního účtu úložiště nebo centra událostí. Veřejná verze Preview protokolů Azure Active Directory ve službě Azure Monitor umožňuje:

* Archivovat protokoly auditu pro určitý účet úložiště Azure, abyste mohli uchovávat data po dlouhou dobu
* Streamovat protokoly auditu do centra událostí Azure pro účely analýzy pomocí oblíbených nástrojů SIEM, jako jsou Splunk a QRadar
* Integrovat protokoly auditu s vlastními řešeními protokolů tím, že je budete streamovat do centra událostí

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Podporované sestavy

Pomocí této funkce můžete směrovat protokoly aktivit auditu a přihlašování do svého účtu úložiště Azure, centra událostí nebo vlastního řešení. 

* **Protokoly auditu**: [Sestava aktivit protokolů auditu](active-directory-reporting-activity-audit-logs.md) poskytuje přístup k historii každé úlohy provedené ve vašem tenantovi.
* **Přihlášení**: Se [sestavou aktivit přihlašování](active-directory-reporting-activity-sign-ins.md) můžete určit, kdo provedl úlohy hlášené sestavou protokolů auditu.

> [!NOTE]
> Zatím není dostupná podpora protokolů aktivit auditu a přihlašování souvisejících s B2C.
>

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:

* Předplatné Azure. Pokud předplatné Azure nemáte, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* [Licence](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Free, Basic, Premium 1 nebo Premium 2 pro přístup k protokolům Azure AD na webu Azure Portal. 

Podle toho, kam chcete směrovat data protokolů auditu, potřebujete některou z těchto položek:

* Účet úložiště Azure, ve kterém máte oprávnění *ListKeys*. Doporučujeme použít obecný účet úložiště, ne účet úložiště objektů blob. Informace o cenách úložiště získáte v [cenové kalkulačce služby Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Obor názvů center událostí Azure, aby byla možná integrace s řešeními třetích stran.

> [!NOTE]
> Pokud chcete získat přístup k protokolům aktivit Azure AD, potřebujete mít v tenantovi Azure AD roli *Globální správce* nebo *Správce zabezpečení*.
>

## <a name="cost-considerations"></a>Důležité informace o nákladech

Pokud už máte licenci Azure AD, potřebujete k vytvoření účtu úložiště a centra událostí předplatné Azure. Předplatné Azure je zdarma, ale platíte za využívání prostředků Azure, třeba účtu úložiště, který používáte k archivaci, a centra událostí, které používáte ke streamování. Množství dat a tedy i související náklady se můžou výrazně lišit v závislosti na velikosti tenanta. 

### <a name="storage-size-for-activity-logs"></a>Velikost úložiště pro protokoly aktivit

Každá událost protokolu auditu zabere v úložišti dat asi 2 kB. V případě tenanta se 100 000 uživateli, ve kterém dojde asi k 1,5 milionu událostí denně, byste tedy za den spotřebovali asi 3 GB úložiště. Vzhledem k tomu, že k zápisům dochází v dávkách asi po 5 minutách, můžete očekávat asi 9000 operací zápisu za měsíc. Následující tabulka obsahuje přibližný odhad nákladů pro obecný účet úložiště verze 2 v oblasti USA – západ s dobou uchování minimálně jeden rok, v závislosti na velikosti tenanta. Přesnější odhad objemu dat, který můžete ve své aplikaci očekávat, vám poskytne [cenová kalkulačka služby Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/). Každá událost protokolu auditu zabere v úložišti dat asi 2 kB. V případě tenanta se 100 000 uživateli, ve kterém dojde asi k 1,5 milionu událostí denně, byste tedy za den spotřebovali asi 3 GB úložiště. Vzhledem k tomu, že k zápisům dochází v dávkách asi po 5 minutách, můžete očekávat asi 9000 operací zápisu za měsíc. Následující tabulka obsahuje přibližný odhad nákladů pro obecný účet úložiště verze 2 v oblasti USA – západ s dobou uchování minimálně jeden rok, v závislosti na velikosti tenanta. Přesnější odhad objemu dat, který můžete ve své aplikaci očekávat, vám poskytne [cenová kalkulačka služby Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/). 

| Kategorie protokolu | Počet uživatelů | Počet událostí / den | Přibližný objem dat za měsíc | Přibližné náklady za měsíc | Přibližné náklady za rok |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Auditování | 100 000 | 1,5 milionu | 90 GB | 1,93 USD | 23,12 USD |
| Auditování | 1000 | 15 000 | 900 MB | 0,02 USD | 0,24 USD |
| Přihlášení | 1000 | 34 800 | 4 GB | 0,13 USD | 1,56 USD |
| Přihlášení | 100 000 | 15 milionů | 1,7 TB | 35,41 USD | 424,92 USD | 


### <a name="event-hub-messages-for-activity-logs"></a>Zprávy centra událostí pro protokoly aktivit

Události se seskupují do dávek asi po pětiminutových intervalech a odesílají se jako jedna zpráva obsahující všechny události za dané období. Zpráva v centru událostí má maximální velikost 256 kB, a pokud velikost všech zpráv za dané období překročí tento objem, odešle se několik zpráv. 

Například u velkých tenantů s více než 100 000 uživateli většinou dochází zhruba k 18 událostem za sekundu, což znamená 5400 událostí za každých 5 minut. Jelikož mají protokoly auditu velikost asi 2 kB na událost, odpovídá to 10,8 MB dat, takže za jeden 5minutový interval se do centra událostí odešle 43 zpráv. Následující tabulka obsahuje přibližné náklady na základní centrum událostí v oblasti USA – západ v závislosti na objemu dat událostí. Přesnější odhad objemu dat, který můžete ve své aplikaci očekávat, vám poskytne [cenová kalkulačka centra událostí](https://azure.microsoft.com/pricing/details/event-hubs/).

| Kategorie protokolu | Počet uživatelů | Počet událostí za sekundu | Počet událostí za 5minutový interval | Objem za interval | Počet zpráv za interval | Počet zpráv za měsíc | Přibližné náklady za měsíc |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Auditování | 100 000 | 18 | 5400 | 10,8 MB | 43 | 371 520 | 10,83 USD |
| Auditování | 1000 | 0.1 | 52 | 104 kB | 1 | 8640 | 10,8 USD |
| Přihlášení | 1000 | 178 | 53 400 | 106,8 MB | 418 | 3 611 520 | 11,06 USD |  


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tato část obsahuje nejčastější dotazy a známé problémy s protokoly Azure Active Directory ve službě Azure Monitor.

**Otázka: Kde mám začít?** 

**Odpověď:** Začněte u [přehledu](reporting-azure-monitor-diagnostics-overview.md), abyste získali představu o tom, co k nasazení této funkce potřebujete. Jakmile se seznámíte s požadavky, projděte si kurzy týkající se konfigurace a směrování protokolů do služby Event Hubs.

---

**Otázka: Jaké protokoly jsou zahrnuté?**

**Odpověď:** Pomocí této funkce se dají směrovat jak protokoly přihlášení, tak protokoly auditu, i když události auditu související s B2C se momentálně nepodporují. Přečtěte si články o [schématu protokolů auditu](reporting-azure-monitor-diagnostics-audit-log-schema.md) a [schématu protokolů přihlášení](reporting-azure-monitor-diagnostics-sign-in-log-schema.md), ve kterých zjistíte, jaké typy protokolů a jaké protokoly podle funkcí se momentálně podporují. 

---

**Otázka: Jak brzy po akci se ve službě Event Hubs zobrazí odpovídající protokoly?**

**Odpověď:** Protokoly by se měly ve službě Event Hubs zobrazit do dvou až pěti minut od provedení akce. Další informace o službě Event Hubs najdete v tématu [Co je Event Hubs?](/azure/event-hubs/event-hubs-what-is-event-hubs.md).

---

**Otázka: Jak brzy po akci se v účtu úložiště zobrazí odpovídající protokoly?**

**Odpověď:** V případě účtů úložiště Azure se latence pohybuje mezi 5 až 15 minutami od provedení akce.

---

**Otázka: Kolik bude uložení mých dat stát?**

**Odpověď:** Náklady na úložiště závisí na velikosti protokolů a také na vybrané době uchování. Přibližný odhad nákladů pro tenanty v závislosti na objemu generovaných protokolů najdete v [přehledu](reporting-azure-monitor-diagnostics-overview.md).

---

**Otázka: Kolik stojí streamování dat do služby Event Hubs?**

**Odpověď:** Náklady na streamování závisí na počtu přijatých zpráv za minutu. V [přehledu](reporting-azure-monitor-diagnostics-overview.md) se dozvíte podrobnosti o tom, jak se náklady počítají, a najdete tu i přibližný odhad na základě počtu zpráv. 

---

**Otázka: Jaké nástroje SIEM se aktuálně podporují?** 

**Odpověď:** V současné době podporují službu Azure Monitor nástroje Splunk, QRadar a Sumologic. Splunk je ale jediným nástrojem SIEM, který podporují protokoly Azure Active Directory. Další informace o fungování konektorů najdete v tématu [Streamování dat monitorování Azure do centra událostí, aby je mohl používat externí nástroj](/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs).

---

**Otázka: Můžu získat přístup k datům z centra událostí bez použití externího nástroje SIEM?** 

**Odpověď:** Ano, pomocí rozhraní [API Event Hub](/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph) můžete získat přístup k protokolům ze své vlastní aplikace. 

---


## <a name="next-steps"></a>Další kroky

* [Archivace protokolů aktivit do účtu úložiště](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [Směrování protokolů aktivit do centra událostí](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [Další informace o diagnostických protokolech Azure](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)