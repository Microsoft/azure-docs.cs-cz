---
title: Zabezpečení dat ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje způsob správy a ochrany dat ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: 97d3b5030ed0ddb29fc439f4a0f6bcc7ff98b6ce
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268022"
---
# <a name="azure-security-center-data-security"></a>Zabezpečení dat ve službě Azure Security Center
Aby zákazníci mohli zabránit, detekovat a reagovat na hrozby, Azure Security Center shromažďuje a zpracovává data související se zabezpečením, včetně informací o konfiguraci, metadat, protokolů událostí a dalších. Společnost Microsoft dodržuje přísné pokyny pro dodržování předpisů a zabezpečení – od psaní kódu po provoz služeb.

Tento článek popisuje způsob správy a ochrany dat ve službě Azure Security Center.

## <a name="data-sources"></a>Zdroje dat
Azure Security Center analyzuje data z následujících zdrojů a poskytuje přehled o stavu vašeho zabezpečení, zjišťuje ohrožení zabezpečení a doporučuje způsoby zmírnění rizik a detekuje aktivní hrozby:

- Služby Azure: Využívá informace o konfiguraci služeb Azure, které máte nasazené, tím, že komunikuje s poskytovatelem prostředků pro příslušnou službu.
- Síťový provoz: Využívá vzorkovaná metadata síťového provozu z infrastruktury společnosti Microsoft, jako je třeba zdrojová a cílová IP adresa/port, velikost paketu nebo síťový protokol.
- Partnerská řešení: Využívá výstrahy zabezpečení ze všech integrovaných partnerských řešení, jako jsou třeba brány firewall a antimalwarová řešení.
- Vaše Virtual Machines a servery: používá informace o konfiguraci a informace o událostech zabezpečení, jako jsou protokoly událostí a auditu systému Windows, protokoly služby IIS a zprávy syslog z vašich virtuálních počítačů. Při vytvoření výstrahy Azure Security Center může navíc vygenerovat snímek příslušného disku virtuálního počítače a z tohoto disku extrahovat artefakty související s příslušnou výstrahou (jako je třeba soubor registru) pro účely forenzní analýzy.


## <a name="data-protection"></a>Ochrana dat
**Oddělení dat**: Data se v rámci služby ukládají logicky oddělená pro jednotlivé komponenty. Všechna data jsou označená podle organizace. Toto značení přetrvává v průběhu celého životního cyklu dat a je vyžadováno na každé úrovni služby.

**Přístup k datům**: aby bylo možné poskytnout doporučení pro zabezpečení a prozkoumat potenciální bezpečnostní hrozby, mohou pracovníci společnosti Microsoft získat přístup k informacím shromažďovaným nebo analyzovaným službami Azure, včetně událostí vytváření procesů, snímků disků virtuálních počítačů a artefaktů, které mohou neúmyslně zahrnovat zákaznická data nebo osobní údaje z vašich virtuálních počítačů. Dodržujeme [Podmínky online služeb společnosti Microsoft a Prohlášení o zásadách ochrany osobních údajů](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), ve kterých je uvedeno, že společnost Microsoft nebude informace o zákaznících používat ani z nich odvozovat další informace pro reklamní nebo podobné obchodní účely. Informace o zákaznících podle potřeby používáme pouze k poskytování služeb Azure a k účelům slučitelným s poskytováním těchto služeb. Všechna práva na informace o zákaznících zůstávají ve vašem vlastnictví.

**Použití dat**: Společnost Microsoft vylepšuje své schopnosti prevence a detekce pomocí schémat a analýzy hrozeb napříč několika klienty. Činíme tak v souladu se závazky k ochraně osobních údajů popsanými v našem [Prohlášení o zásadách ochrany osobních údajů](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).

## <a name="data-location"></a>Umístění dat

**Vaše pracovní prostory**: pracovní prostor je určený pro následující zeměpisných oblastech a data shromážděná z virtuálních počítačů Azure, včetně některých typů dat výstrah, se ukládají do nejbližšího pracovního prostoru.

| Geografie virtuálního počítače                              | Geografie pracovního prostoru |
|-------------------------------------|---------------|
| USA, Brazílie, Jižní Afrika | Česká republika |
| Canada                              | Kanada        |
| Evropa (s výjimkou Spojeného království)   | Evropa        |
| Spojené království                      | Spojené království |
| Asie (s výjimkou Indie, Japonska, Koreje, ČLR)   | Asie a Tichomoří  |
| Jižní Korea                              | Asie a Tichomoří  |
| Indie                               | Indie         |
| Japonsko                               | Japonsko         |
| Čína                               | Čína         |
| Austrálie                           | Austrálie     |


Snímky disků virtuálních počítačů se ukládají ve stejném účtu úložiště jako disk virtuálního počítače.

Pro virtuální počítače a servery spuštěné v jiných prostředích, například místně, můžete zadat pracovní prostor a oblast, kde se shromážděná data ukládají.

**Azure Security Center Storage:** Informace o výstrahách zabezpečení, včetně partnerských výstrah, se ukládají místně v závislosti na umístění souvisejícího prostředku Azure, zatímco informace o stavu zabezpečení a doporučení se ukládají centrálně v USA nebo v Evropě, v závislosti na umístění zákazníka. Artefakty počítačů se ukládají centrálně ve stejné oblasti jako virtuální počítač.

## <a name="manage-data-collection-from-virtual-machines"></a>Správa shromažďování dat z virtuálních počítačů

Když povolíte službu Security Center v Azure, u každého vašeho předplatného Azure se zapne funkce shromažďování dat. Shromažďování dat pro předplatná můžete zapnout také v části Zásady zabezpečení služby Azure Security Center. Když je shromažďování dat zapnuté, Azure Security Center zřídí agenta Log Analytics na všech stávajících podporovaných virtuálních počítačích Azure a všech nově vytvořených.
Agent Log Analytics vyhledává různé konfigurace a události související se zabezpečením v [trasování událostí pro Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Operační systém bude kromě toho během chodu počítače shromažďovat události protokolu událostí. Mezi příklady těchto údajů patří: typ a verze operačního systému, protokoly operačního systému (protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy, přihlášený uživatel a ID klienta. Agent Log Analytics načte položky protokolu událostí a trasování ETW a zkopíruje je do vašich pracovních prostorů pro účely analýzy. Agent Log Analytics taky umožňuje události vytváření procesů a auditování na příkazovém řádku.

Pokud nepoužíváte Azure Defender, můžete taky zakázat shromažďování dat z virtuálních počítačů v zásadách zabezpečení. Pro předplatná, která jsou chráněná pomocí Azure Defenderu, se vyžaduje shromažďování dat. Shromažďování artefaktů a snímků disku virtuálního počítače bude nadále povolené i v případě, že shromažďování dat je zakázané.

## <a name="data-consumption"></a>Využití dat

Zákazníci můžou využívat data související se službou Security Center z různých datových proudů, jak je znázorněno níže:

* **Aktivita Azure**: všechny výstrahy zabezpečení, schválené Security Center požadavky [za běhu](security-center-just-in-time.md) a všechny výstrahy vygenerované [adaptivními ovládacími prvky aplikace](security-center-adaptive-application.md).
* **Protokoly Azure monitor**: všechny výstrahy zabezpečení.


> [!NOTE]
> Doporučení zabezpečení je možné využívat také prostřednictvím rozhraní REST API. Další informace najdete v [referenčních materiálech k rozhraní REST API poskytovatele prostředků zabezpečení](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx).

## <a name="see-also"></a>Viz také
V tomto dokumentu jste se dozvěděli informace o způsobu správy a ochrany ve službě Azure Security Center. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md) – Zjistěte, jak naplánovat a pochopit aspekty návrhu, abyste mohli přejít na Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Blog o zabezpečení Azure](https://docs.microsoft.com/archive/blogs/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů
