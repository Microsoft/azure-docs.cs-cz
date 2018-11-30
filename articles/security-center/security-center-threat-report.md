---
title: Sestava analýzy hrozeb v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže s použitím sestav analýzy hrozeb v Azure Security Center během vyšetřování za účelem nalezení dalších informací týkajících se výstrahy zabezpečení.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: 90cf57e22c4bb86ed9a784731480bc3fb8d3025d
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619956"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Sestava analýzy hrozeb v Azure Security Center
Tento dokument vysvětluje, jakým způsobem vám mohou sestavy analýzy hrozeb v Azure Security Center pomoci zjistit více o hrozbě, který vygenerovala výstrahu zabezpečení.

## <a name="what-is-a-threat-intelligence-report"></a>Co je sestava analýzy hrozeb?
Detekce hrozeb v Security Center funguje tak, že monitoruje informace o zabezpečení z prostředků Azure, ze sítě a připojených partnerských řešení. Za účelem identifikace hrozeb služba tyto informace analyzuje a často přitom koreluje data z různých zdrojů. Tento proces je součástí [možností detekce](security-center-detection-capabilities.md) v Security Center.

Když Security Center identifikuje hrozbu, aktivuje [výstrahu zabezpečení](security-center-managing-and-responding-alerts.md), která obsahuje podrobné informace týkající se konkrétní události, včetně návrhů na odstranění problémů. Chcete-li pomoci týmům reakce na incidenty, vyšetřením a odstraněním hrozeb, Security Center zahrnuje sestavu analýzy hrozeb, která obsahuje informace o dané hrozbě, který byl zjištěn, včetně informací, jako:

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

Tento typ informací je užitečné v průběhu [reakce na incidenty](security-center-incident-response.md) procesu, ve kterých je probíhá vyšetřování za účelem pochopení zdroje útoku, motivací útočníka a toho, co dělat pro zmírnění tohoto problému v budoucnu .

## <a name="how-to-access-the-threat-intelligence-report"></a>Jak získat přístup k sestavě analýzy hrozeb?
Aktuální výstrahy můžete zkontrolovat jejich prohlížením na dlaždici **Výstrahy zabezpečení**. Otevřete na webu Azure portal a podle následujících pokynů zobrazte podrobnosti o jednotlivých výstrahách:

1. Na řídicím panelu Security Center uvidíte dlaždici **Výstrahy zabezpečení**.
2. Kliknutím na dlaždici otevřete okno **Výstrahy zabezpečení**, které obsahuje podrobnosti o výstrahách, a klikněte na výstrahu zabezpečení, o které chcete získat další informace.

    ![Výstrahy zabezpečení](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. V takovém případě **spuštění podezřelého procesu** okno zobrazuje podrobnosti o výstraze, jak je znázorněno na následujícím obrázku:

    ![Podrobnosti výstrahy zabezpečení](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. Množství dostupných informací pro jednotlivé výstrahy zabezpečení se bude lišit podle typu výstrahy. V **sestavy** pole, budete mít odkaz na sestavu analýzy hrozeb. Klikněte na něj a otevře se další okno prohlížeče se souborem PDF.

   ![Výběr úložiště](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Odtud si můžete stáhnout soubor PDF pro tuto sestavu a přečíst si více o detekovaných potížích se zabezpečením a provést akce na základě poskytnutých informací.

## <a name="see-also"></a>Další informace najdete v tématech
V tomto dokumentu jste se dozvěděli, jak mohou sestavy analýzy hrozeb v Azure Security Center pomoci během vyšetřování výstrah zabezpečení. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Přečtěte si nejčastější dotazy o použití této služby.
* [Využití Azure Security Center při reakci na incidenty](security-center-incident-response.md)
* [Možnosti detekce v Azure Security Center](security-center-detection-capabilities.md)
* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md). Zjistěte, jak naplánovat a pochopit aspekty návrhu, abyste mohli přejít na Azure Security Center.
* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md). Zjistěte, jak spravovat a zpracovávat výstrahy zabezpečení.
* [Řešení bezpečnostních incidentů v Azure Security Center](security-center-incident.md)
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
