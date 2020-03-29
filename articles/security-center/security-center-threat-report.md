---
title: Sestava analýzy hrozeb v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže s použitím sestav analýzy hrozeb v Azure Security Center během vyšetřování za účelem nalezení dalších informací týkajících se výstrahy zabezpečení.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: f8b4063d87fa9a89dccd42eddea644609bd6ff27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921245"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Sestava analýzy hrozeb v Azure Security Center
Tento dokument vysvětluje, jakým způsobem vám mohou sestavy analýzy hrozeb v Azure Security Center pomoci zjistit více o hrozbě, který vygenerovala výstrahu zabezpečení.

## <a name="what-is-a-threat-intelligence-report"></a>Co je sestava analýzy hrozeb?
Ochrana před hrozbami Centra zabezpečení funguje tak, že sleduje informace o zabezpečení z prostředků Azure, sítě a řešení připojených partnerů. Za účelem identifikace hrozeb služba tyto informace analyzuje a často přitom koreluje data z různých zdrojů. Další informace najdete v tématu [Jak Azure Security Center detekuje a reaguje na hrozby](security-center-alerts-overview.md#detect-threats).

Když Security Center identifikuje hrozbu, aktivuje [výstrahu zabezpečení](security-center-managing-and-responding-alerts.md), která obsahuje podrobné informace týkající se konkrétní události, včetně návrhů na odstranění problémů. Centrum zabezpečení obsahuje zprávu o analýzách hrozeb, která obsahuje informace o zjištěné hrozbě, včetně informací, jako je:

* Identita nebo přidružení útočníka (pokud je tato informace k dispozici)
* Cíle útočníků
* Současné a historické útočné kampaně (pokud je tato informace k dispozici)
* Taktika, nástroje a postupy útočníků
* Přidružené ukazatele ohrožení zabezpečení, například adresy URL a hodnoty hash souborů
* Viktimologie, což je oborové a geografické rozšíření, které vám pomůže určit, zda jsou vaše prostředky Azure v ohrožení
* Informace o zmírnění a odstraňování problémů

> [!NOTE]
> Množství informací v jednotlivých konkrétních sestavách se bude lišit. Úroveň podrobností závisí na aktivitě a rozšíření malwaru.
>
>

Security Center obsahuje tři typy sestav hrozeb, které se mohou lišit podle útoku. K dispozici jsou tyto sestavy:

* **Sestava skupiny aktivit**: poskytuje podrobné informace o útočnících, jejich cílech a taktice.
* **Sestava kampaně**: zaměřuje se na podrobnosti o konkrétních útočných kampaních.
* **Sestava shrnutí hrozby**: pokrývá všechny položky v předchozích dvou sestavách.

Tento typ informací je užitečný během procesu reakce na incidenty, kde probíhá šetření, aby bylo možné pochopit zdroj útoku, motivaci útočníka a co udělat pro zmírnění tohoto problému.

## <a name="how-to-access-the-threat-intelligence-report"></a>Jak získat přístup k sestavě analýzy hrozeb?
Aktuální výstrahy můžete zkontrolovat jejich prohlížením na dlaždici **Výstrahy zabezpečení**. Otevřete portál Azure a podle následujících kroků se podívejte na další podrobnosti o jednotlivých výstrahách:

1. Na řídicím panelu Security Center uvidíte dlaždici **Výstrahy zabezpečení**.
2. Kliknutím na dlaždici otevřete okno **Výstrahy zabezpečení**, které obsahuje podrobnosti o výstrahách, a klikněte na výstrahu zabezpečení, o které chcete získat další informace.

    ![Výstrahy zabezpečení](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. V tomto případě **podezřelý proces provedený** okno zobrazuje podrobnosti o výstraze, jak je znázorněno na obrázku níže:

    ![Podrobnosti výstrahy zabezpečení](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. Množství dostupných informací pro jednotlivé výstrahy zabezpečení se bude lišit podle typu výstrahy. V poli **ZPRÁVY** máte odkaz na zprávu o zpravodajské hrozbě. Klikněte na něj a otevře se další okno prohlížeče se souborem PDF.

   ![Výběr úložiště](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Odtud si můžete stáhnout soubor PDF pro tuto sestavu a přečíst si více o detekovaných potížích se zabezpečením a provést akce na základě poskytnutých informací.

## <a name="see-also"></a>Viz také
V tomto dokumentu jste se dozvěděli, jak mohou sestavy analýzy hrozeb v Azure Security Center pomoci během vyšetřování výstrah zabezpečení. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Průvodce plánováním a operacemi Centra zabezpečení Azure](security-center-planning-and-operations-guide.md). Zjistěte, jak naplánovat a pochopit aspekty návrhu, abyste mohli přejít na Azure Security Center.
* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md). Zjistěte, jak spravovat a zpracovávat výstrahy zabezpečení.
* [Zpracování incidentu zabezpečení v Azure Security Center](security-center-incident.md)