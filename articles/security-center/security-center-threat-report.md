---
title: Sestava analýzy hrozeb v Azure Security Center | Dokumentace Microsoftu
description: Tato stránka vám pomůže s použitím sestav analýzy hrozeb Azure Security Center během šetření najít další informace o výstrahách zabezpečení.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: f9b3fd0000a1b5dbba00995c37f96a89319de0e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440489"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Sestava analýzy hrozeb Azure Security Center

Tato stránka vysvětluje, jak Azure Security Center sestavy analýzy hrozeb, které vám pomůžou získat další informace o hrozbě, která aktivovala výstrahu zabezpečení.


## <a name="what-is-a-threat-intelligence-report"></a>Co je sestava analýzy hrozeb?

Security Center ochrana před hrozbami funguje tak, že monitoruje informace o zabezpečení z vašich prostředků Azure, sítě a připojených partnerských řešení. Za účelem identifikace hrozeb služba tyto informace analyzuje a často přitom koreluje data z různých zdrojů. Další informace najdete v tématu [jak Azure Security Center detekuje hrozby a reaguje na](security-center-alerts-overview.md#detect-threats)ně.

Pokud Security Center identifikuje hrozbu, aktivuje [výstrahu zabezpečení](security-center-managing-and-responding-alerts.md), která obsahuje podrobné informace týkající se události, včetně návrhů pro nápravu. Aby mohli týmy reakcí na incidenty prozkoumat a opravit hrozby, Security Center poskytuje sestavy analýzy hrozeb obsahující informace o zjištěných hrozbách. Sestava obsahuje následující informace:

* Identita nebo přidružení útočníka (pokud je tato informace k dispozici)
* Cíle útočníků
* Současné a historické útočné kampaně (pokud je tato informace k dispozici)
* Taktiku, nástroje a postupy pro útočníky
* Přidružené ukazatele ohrožení zabezpečení, například adresy URL a hodnoty hash souborů
* Viktimologie, což je oborové a geografické rozšíření, které vám pomůže určit, zda jsou vaše prostředky Azure v ohrožení
* Informace o zmírnění a odstraňování problémů

> [!NOTE]
> Množství informací v jednotlivých konkrétních sestavách se bude lišit. Úroveň podrobností závisí na aktivitě a rozšíření malwaru.

Security Center obsahuje tři typy sestav hrozeb, které se mohou lišit podle útoku. K dispozici jsou tyto sestavy:

* **Sestava skupiny aktivit**: poskytuje rozsáhlé komentáře k útočníkům, jejich cílům a taktiku.
* **Sestava kampaně**: zaměřuje se na podrobnosti o konkrétních útočných kampaních.
* **Sestava shrnutí hrozby**: pokrývá všechny položky v předchozích dvou sestavách.

Tento typ informací je užitečný během procesu reakce na incidenty, kde existuje průběžné šetření, které porozuměl zdroji útoku, motivům útočníka a k tomu, co dělat k vyřešení tohoto problému v budoucnu.



## <a name="how-to-access-the-threat-intelligence-report"></a>Jak získat přístup k sestavě analýzy hrozeb?

1. Z bočního panelu Security Center otevřete stránku **výstrahy zabezpečení** .
1. Vyberte výstrahu. 
    Otevře se stránka podrobnosti o výstrahách s dalšími podrobnostmi o výstraze. Níže vidíte stránku s podrobnostmi o **zjištěných indikátorech ransomwarem** .

    [![Ransomwarem indikátory zjištěné stránky podrobností výstrahy](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Vyberte odkaz na sestavu a otevře se PDF ve výchozím prohlížeči.

    [![Stránka podrobností výstrahy potenciálně nezabezpečené akce](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    Volitelně můžete stáhnout sestavu PDF. 

    >[!TIP]
    > Množství dostupných informací pro jednotlivé výstrahy zabezpečení se bude lišit podle typu výstrahy.



## <a name="next-steps"></a>Další kroky

Tato stránka vysvětluje, jak otevřít sestavy analýzy hrozeb při zkoumání výstrah zabezpečení. Související informace najdete na následujících stránkách:

* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md). Zjistěte, jak spravovat a zpracovávat výstrahy zabezpečení.
* [Zpracování incidentů zabezpečení v Azure Security Center](security-center-incident.md)