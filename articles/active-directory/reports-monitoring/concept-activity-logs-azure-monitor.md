---
title: Azure Active Directory protokoly aktivit v Azure Monitor | Microsoft Docs
description: Úvod do Azure Active Directory protokolů aktivit v Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/22/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f62ad020d2ec3b5ab712f50dca2dddd3b981f098
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656473"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Protokoly aktivit Azure AD v Azure Monitor

Protokoly aktivit služby Azure Active Directory (Azure AD) můžete směrovat do několika koncových bodů pro dlouhodobé uchovávání dat a přehledy dat. Tato funkce umožňuje:

* Archivace protokolů aktivit Azure AD do účtu služby Azure Storage za účelem zachování dat po dlouhou dobu.
* Streamování protokolů aktivit Azure AD do centra událostí Azure pro analýzy pomocí oblíbených nástrojů pro správu událostí a SIEM (Security Information and Event Management), jako je Splunk a QRadar.
* Integrujte protokoly aktivit služby Azure AD s vlastními řešeními pro vlastní protokoly jejich streamování do centra událostí.
* Pošlete protokoly aktivit Azure AD, abyste Azure Monitor protokoly, abyste umožnili bohatou vizualizaci, monitorování a upozorňování na připojená data.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Podporované sestavy

Pomocí této funkce můžete směrovat protokoly auditu Azure AD a protokoly přihlášení k vašemu účtu služby Azure Storage, centru událostí, protokolům Azure Monitor nebo vlastnímu řešení. 

* **Protokoly auditu**: [Sestava aktivita protokoly auditu](concept-audit-logs.md) vám poskytne přístup k historii každého úkolu, který se provádí ve vašem tenantovi.
* **Protokoly přihlášení**: Pomocí [sestavy aktivit přihlašování](concept-sign-ins.md)můžete určit, kdo provedl úkoly, které jsou hlášeny v protokolech auditu.

> [!NOTE]
> Zatím není dostupná podpora protokolů aktivit auditu a přihlašování souvisejících s B2C.
>

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:

* Předplatné Azure. Pokud předplatné Azure nemáte, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* [Licence](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Free, Basic, Premium 1 nebo Premium 2 pro přístup k protokolům auditu Azure AD na webu Azure Portal. 
* Tenanta Azure AD.
* Uživatele, který je **globálním správcem** nebo **správcem zabezpečení** pro tohoto tenanta Azure AD.
* [Licence](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Premium 1 nebo Premium 2 pro přístup k protokolům přihlášení Azure AD na webu Azure Portal. 

Podle toho, kam chcete směrovat data protokolů auditu, potřebujete některou z následujících položek:

* Účet úložiště Azure, pro který máte oprávnění *ListKeys*. Doporučujeme použít obecný účet úložiště, ne účet úložiště objektů blob. Informace o cenách úložiště najdete v [cenové kalkulačce služby Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Obor názvů služby Azure Event Hubs pro integraci s řešeními třetích stran.
* Pracovní prostor Azure Log Analytics k odesílání protokolů do protokolů Azure Monitor.

## <a name="cost-considerations"></a>Důležité informace o nákladech

Pokud už máte licenci Azure AD, potřebujete k vytvoření účtu úložiště a centra událostí předplatné Azure. Předplatné Azure je zdarma, ale platíte za využívání prostředků Azure, včetně účtu úložiště, který používáte k archivaci, a centra událostí, které používáte ke streamování. Množství dat a tedy i související náklady se můžou výrazně lišit v závislosti na velikosti tenanta. 

### <a name="storage-size-for-activity-logs"></a>Velikost úložiště pro protokoly aktivit

Každá událost protokolu auditu zabere v úložišti dat asi 2 kB. Protokoly událostí přihlášení mají přibližně 4 KB úložiště dat. V případě tenanta se 100 000 uživateli, ve kterém dojde asi k 1,5 milionu událostí denně, byste za den spotřebovali asi 3 GB úložiště. Vzhledem k tomu, že k zápisům dochází v dávkách asi po pěti minutách, můžete očekávat asi 9 000 operací zápisu za měsíc. 


Následující tabulka obsahuje odhad nákladů na účet úložiště pro obecné účely v2 v oblasti Západní USA s dobou uchování minimálně jeden rok, v závislosti na velikosti tenanta. Přesnější odhad objemu dat, který můžete ve své aplikaci očekávat, vám poskytne [cenová kalkulačka služby Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).


| Kategorie protokolu | Počet uživatelů | Počet událostí za den | Objem dat za měsíc (odhad) | Náklady za měsíc (odhad) | Náklady za rok (odhad) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Auditování | 100,000 | 1,5&nbsp;milionu | 90 GB | 1,93 USD | 23,12 USD |
| Auditování | 1 000 | 15 000 | 900 MB | 0,02 USD | 0,24 USD |
| Přihlášení | 1 000 | 34 800 | 4 GB | 0,13 USD | 1,56 USD |
| Přihlášení | 100,000 | 15&nbsp;milionů | 1,7 TB | 35,41 USD | 424,92 USD |
 









### <a name="event-hub-messages-for-activity-logs"></a>Zprávy centra událostí pro protokoly aktivit

Události se seskupují do dávek asi po pětiminutových intervalech a odesílají se jako jedna zpráva obsahující všechny události za dané období. Zpráva v centru událostí má maximální velikost 256 kB, a pokud velikost všech zpráv za dané období překročí tento objem, odešle se několik zpráv. 

Například u velkého tenanta s více než 100 000 uživateli běžně dochází k přibližně 18 událostem za sekundu, což odpovídá 5 400 událostem za každých pět minut. Vzhledem k tomu, že protokoly auditu pro každou událost mají velikost přibližně 2 kB, odpovídá toto množství 10,8 MB dat. Proto se v takovém pětiminutovém intervalu do centra událostí odešle 43 zpráv. 

Následující tabulka obsahuje odhad měsíčních nákladů na základní centrum událostí v oblasti Západní USA v závislosti na objemu dat událostí. Přesnější odhad objemu dat, který můžete ve své aplikaci očekávat, vám poskytne [cenová kalkulačka služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Kategorie protokolu | Počet uživatelů | Počet událostí za sekundu | Počet událostí za pětiminutový interval | Objem za interval | Počet zpráv za interval | Počet zpráv za měsíc | Náklady za měsíc (odhad) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Auditování | 100,000 | 18 | 5 400 | 10,8 MB | 43 | 371 520 | 10,83 USD |
| Auditování | 1 000 | 0.1 | 52 | 104 kB | 1 | 8 640 | 10,80 USD |
| Přihlášení | 1 000 | 178 | 53 400 | 106,8&nbsp;MB | 418 | 3 611 520 | 11,06 USD |  

### <a name="azure-monitor-logs-cost-considerations"></a>Náklady na Azure Monitor protokolů



| Kategorie protokolu       | Počet uživatelů | Počet událostí za den | Události za měsíc (30 dní) | Cena za měsíc v USD (EST) |
| :--                | ---             | ---            | ---                        | --:                          |
| Audit a přihlášení | 100,000         | 16 500 000     | 495 000 000                |  $1093,00                       |
| Auditování              | 100,000         | 1 500 000      | 45,000,000                 |  $246,66                     |
| Přihlášení           | 100,000         | 15,000,000     | 450 000 000                |  $847,28                     |










Pokud chcete zkontrolovat náklady související se správou protokolů Azure Monitor, přečtěte si téma [Správa nákladů pomocí řízení objemu dat a uchovávání v protokolech Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-cost-storage).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

V této části najdete odpovědi na nejčastější dotazy a popis známých problémů s protokoly Azure AD ve službě Azure Monitor.

**Otázka: Které protokoly jsou zahrnuty?**

**A**: Protokoly aktivit přihlašování a protokoly auditu jsou k dispozici pro směrování prostřednictvím této funkce, i když události auditu související s B2C nejsou aktuálně zahrnuty. Informace o tom, jaké typy protokolů a jaké protokoly podle funkcí se momentálně podporují, najdete v článcích o [schématu protokolů auditu](reference-azure-monitor-audit-log-schema.md) a [schématu protokolů přihlašování](reference-azure-monitor-sign-ins-log-schema.md). 

---

**Otázka: Jak brzy se v centru událostí zobrazí odpovídající protokoly?**

**A**: Protokoly by se měly zobrazit v centru událostí do dvou až pěti minut od provedení akce. Další informace o službě Event Hubs najdete v tématu [Co je Azure Event Hubs?](../../event-hubs/event-hubs-about.md).

---

**Otázka: Jak brzy po akci dojde k zobrazení odpovídajících protokolů v mém účtu úložiště?**

**A**: U účtů Azure Storage je latence kdekoli od 5 do 15 minut od provedení akce.

---

**Otázka: Co se stane, když správce změní dobu uchování nastavení diagnostiky?**

**A**: Nové zásady uchovávání informací se použijí na protokoly shromážděné po změně. Protokoly shromážděné před změnou zásad nebudou nijak ovlivněny.

---

**Otázka: Kolik budou náklady na ukládání dat?**

**A**: Náklady na úložiště závisí na velikosti protokolů i na době uchování, kterou zvolíte. Seznam odhadovaných nákladů na tenanty, které závisí na objemu generovaných protokolů, najdete v části [Velikost úložiště pro protokoly aktivit](#storage-size-for-activity-logs).

---

**Otázka: Kolik budou náklady na streamování dat do centra událostí?**

**A**: Náklady na streamování závisí na počtu přijatých zpráv za minutu. Tento článek popisuje způsob výpočtu nákladů a uvádí odhady nákladů na základě počtu zpráv. 

---

**Otázka: Návody integrace protokolů aktivit služby Azure AD se systémem SIEM?**

**A**: Můžete to provést dvěma způsoby:

- Pomocí služeb Azure Monitor a Event Hubs můžete protokoly streamovat do systému SIEM. Nejprve nastavte [streamování protokolů do centra událostí](tutorial-azure-monitor-stream-logs-to-event-hub.md) a pak [nastavte nástroj SIEM](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) s použitím nakonfigurovaného centra událostí. 

- Pomocí rozhraní [Graph API pro vytváření sestav](concept-reporting-api.md) můžete získat přístup k datům a pomocí vlastních skriptů je odesílat do systému SIEM.

---

**Otázka: Jaké nástroje SIEM se v tuto chvíli podporují?** 

**A**: V současné době je Azure Monitor podporován logikou [Splunk](tutorial-integrate-activity-logs-with-splunk.md), QRadar a [sumo](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory). Další informace o fungování konektorů najdete v tématu [Streamování dat monitorování Azure do centra událostí, aby je mohl používat externí nástroj](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

---

**Otázka: Návody integrace protokolů aktivit služby Azure AD s instancí my Splunk?**

**A**: Nejprve [směrujte protokoly aktivit služby Azure AD do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md)a pak postupujte podle kroků a integrujte [protokoly aktivit pomocí Splunk](tutorial-integrate-activity-logs-with-splunk.md).

---

**Otázka: Návody integrace protokolů aktivit služby Azure AD s logikou sumo?** 

**A**: Nejdřív nasměrujte [protokoly aktivit služby Azure AD do centra událostí](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)a pak postupujte podle pokynů k [instalaci aplikace Azure AD a zobrazení řídicích panelů v SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**Otázka: Můžu získat přístup k datům z centra událostí bez použití externího nástroje SIEM?** 

**A**: Ano. Pro přístup k protokolům z vlastní aplikace můžete použít [rozhraní API služby Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md). 

---


## <a name="next-steps"></a>Další postup

* [Archivace protokolů aktivit do účtu úložiště](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Směrování protokolů aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Integrace protokolů aktivit s Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)
