---
title: Protokolu analýzy funkce pro poskytovatele služeb | Microsoft Docs
description: Analýzy protokolů může pomoci spravovat poskytovatelé služeb (MSPs), velké podniky nezávislí výrobci softwaru (ISV) a poskytovatele hostitelských služeb, spravovat a monitorovat servery v jeho místní nebo cloudovou infrastrukturu.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: meirm
ms.openlocfilehash: 97e36c624e865010ada67f5163af6d7f03de079f
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096566"
---
# <a name="log-analytics-features-for-service-providers"></a>Funkce analýzy protokolů pro poskytovatele služeb
Analýzy protokolů může pomoct zprostředkovatelé spravované služby (MSPs), velké podniky, nezávislí dodavatelé softwaru (ISV) a poskytovatele hostitelských služeb spravovat a monitorovat servery v jeho místní nebo cloudovou infrastrukturu. 

Velké podniky sdílet mnoho společného s poskytovatelé služeb, zejména v případě, že je centralizované IT tým, který je zodpovědný za správu IT pro mnoho různých podnikových jednotek. Pro jednoduchost, tento dokument používá termín *poskytovatele služeb* ale stejné funkce je také k dispozici pro podniky a dalších zákazníků.

Pro partnery a poskytovatelé, kteří jsou součástí služby [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) programu, analýzy protokolů je jednou ze služeb Azure k dispozici v [předplatného poskytovatele CSP Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Architektury pro poskytovatele služeb

Log Analytics pracovní prostory poskytují metodu pro správce k řízení toku a izolaci protokolů a vytvořit protokolu architektura, která řeší jeho konkrétní obchodní potřeby. [Tento článek](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-manage-access) vysvětluje obecných aspektů kolem pracovního prostoru Správa. Poskytovatelé služeb mají další aspekty.

Existují tři možné architektury pro poskytovatele služeb týkající se pracovních prostorů analýzy protokolů:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuované - protokoly se ukládají do pracovní prostory, které jsou umístěné v klientovi zákazníka 

V této architektuře prostoru nasadí zákazníka klienta, který se používá pro všechny protokoly tohoto zákazníka. Zprostředkovatel Správci služeb mají udělen přístup tento pracovní prostor pomocí [uživatele typu Host Azure Active Directory (B2B)](https://docs.microsoft.com/en-us/azure/active-directory/b2b/what-is-b2b). Správce poskytovatele služeb, bude mít na portálu Azure přejděte na jejich zákazníka adresáře se budou moci tyto pracovní prostory.

Výhody této architektury jsou:
* Zákazník můžete spravovat přístup k protokolům pomocí vlastní [přístupu podle rolí](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview).
* Každý zákazník může mít různá nastavení pro jejich prostoru například uchovávání informací a dat malá a velká.
* Izolace mezi zákazníci regulačních a dodržování předpisů.
* Zdarma pro každý pracovní prostor budou vráceny do předplatného zákazníka.
* Protokoly mohou být shromažďovány z všechny typy prostředků, není právě založené na agentovi. Například auditování Azure.

Nevýhody této architektury jsou:
* Je těžší pro poskytovatele služby ke správě velkého počtu zákazníků klientů najednou.
* Zprostředkovatel Správci služeb mají zřídit v adresáři zákazníka.
* Poskytovatel služeb nelze analyzovat data mezi svým zákazníkům.

### <a name="2-central---logs-are-stored-in-workspace-located-in-the-service-provider-tenant"></a>2. Střed - protokoly jsou uloženy v pracovním prostoru umístěný v klientovi služby zprostředkovatele

V této architektuře protokoly nejsou uložené v klientech zákazníka, ale jenom v centrálním umístění, v rámci jednoho z předplatných poskytovatele služeb. Agenti, kteří jsou nainstalováni na virtuálních počítačích zákazníka jsou nakonfigurované k odeslání protokolů s jejich tento pracovní prostor pomocí ID pracovního prostoru a tajný klíč.

Výhody této architektury jsou:
* Je snadnou správu velkého počtu zákazníků a jejich integraci do různých systémů back-end.
* Poskytovatel služeb má úplné vlastnictví přes protokoly a různých artefaktů, jako jsou funkce a uložené dotazy.
* Poskytovatel služeb můžete provádět analýzy napříč všemi zákazníků.

Nevýhody této architektury jsou:
* Bude obtížné oddělit data mezi zákazníků. Jediným dobrým metodou k tomu je použít název domény počítače.
* Všechna data z všem zákazníkům budou uloženy ve stejné oblasti s jednoho kusovníku a stejné nastavení uchování a konfigurace.
* Prostředky infrastruktury Azure a PaaS služby, jako je Azure Diagnostics a auditování Azure vyžaduje pracovní prostor a být ve stejném klientovi jako prostředek proto, že se nelze odeslat protokoly do centrální pracovního prostoru.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybridní - protokoly jsou uložené v pracovním prostoru umístěný v klientovi zákazníka a některá z nich jsou vyžádány do centrálního umístění.

Třetí architektura kombinovat ze dvou možností. Je založena na první distribuované architektuře, kde jsou protokoly místní u každého zákazníka ale používat stejný mechanismus vytvořit centrální úložiště protokolů. Část protokoly vložen do centrálního umístění pro analýz a generování sestav. Tato část může být malý počet datových typů nebo Souhrn aktivity, jako je například denní statistiky.

Existují dvě možnosti pro implementaci centrální umístění v analýzy protokolů:

1. Centrální prostoru: Zprostředkovatel služby můžete vytvořit pracovní prostor v jeho klienta a použít skript, který využívá [dotazu API](https://dev.loganalytics.io/) s [API kolekce dat](log-analytics-data-collector-api.md) přenést data z různých pracovní prostory k tomuto centrální umístění. Další možností, než skript je použít [aplikace logiky Azure](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview).

2. PowerBI jako centrální umístění: Power BI může fungovat jako centrální umístění, pokud v různých pracovních prostorech exportovat data do pomocí integrace mezi analýzy protokolů a [Power BI](log-analytics-powerbi.md). 


## <a name="next-steps"></a>Další kroky
* Automatizovat vytvoření a konfigurace pomocí pracovních prostorů [šablony Resource Manageru](log-analytics-template-workspace-configuration.md)
* Automatizovat vytváření pracovních prostorů pomocí [prostředí PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Použití [výstrahy](log-analytics-alerts.md) pro integraci s existujícími systémy
* Souhrnné sestavy pomocí [Power BI](log-analytics-powerbi.md)

