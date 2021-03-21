---
title: Integrace s Microsoft 365 Defenderem s Sentinel Azure Microsoft Docs
description: Přečtěte si, jak integrace Microsoft 365 Defenderu s Azure Sentinel vám umožní používat jako svou univerzální frontu pro incidenty Azure Sentinel, a přitom zachovává sílu M365D's, která pomáhá při zkoumání M365ch incidentů zabezpečení a také způsob ingestování pokročilých dat do Azure Sentinel komponent Defenderu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: abace18db51a7a571ecc66d50253277fbd2296d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744993"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Integrace s Microsoft 365 Defenderem s využitím Azure Sentinel

> [!IMPORTANT]
> Konektor programu Microsoft 365 Defender je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

> [!IMPORTANT]
>
> **Microsoft 365 Defender** byl dřív známý jako **Microsoft Threat Protection** nebo **MTP**.
>
> **Microsoft Defender pro koncové body** se dřív jmenoval jako **Rozšířená ochrana před internetovými útoky v programu Microsoft Defender** nebo **MDATP**.
>
> Můžou se zobrazovat staré názvy, které se v časovém intervalu pořád používají.

## <a name="incident-integration"></a>Integrace incidentů

Integrace incidentů služby [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) v Azure Sentinel umožňuje streamování všech M365D incidentů do Azure Sentinel a jejich synchronizaci mezi oběma portály. Incidenty od M365D (dříve označované jako Microsoft Threat Protection nebo MTP) obsahují všechny přidružené výstrahy, entity a relevantní informace, které vám poskytnou dostatečný kontext pro provádění třídění a předběžné šetření v kontextu Azure Sentinel. Jednou v Sentinele budou incidenty stále synchronizované s M365D, což vám umožní využít výhody obou portálů při vyšetřování incidentů.

Tato integrace poskytuje Microsoft 365 incidentů zabezpečení, které se mají spravovat z Azure Sentinel, jako součást primární fronty incidentů napříč celou organizací, takže můžete vidět a korelovat – M365 incidenty společně s uživateli ze všech ostatních cloudových a místních systémů. Ve stejnou dobu vám umožňuje využít jedinečné síly a možnosti M365D pro důkladné vyšetřování a M365 prostředí v rámci M365 ekosystému. M365 Defender rozšiřuje a seskupuje výstrahy z více produktů M365, zmenšuje velikost fronty incidentů SOC a zkrátí dobu, kterou je potřeba vyřešit. Služby komponent, které jsou součástí zásobníku M365 Defender, jsou:

- **Microsoft Defender pro koncové body** (MDE, dřív MDATP)
- **Microsoft Defender pro identitu** (MDI, dříve AATP)
- **Microsoft Defender pro Office 365** (MDO, dříve O365ATP)
- **Microsoft Cloud App Security** (MCAS)

Kromě Shromažďování výstrah z těchto součástí M365 Defender generuje výstrahy vlastní. Vytvoří incidenty ze všech těchto výstrah a pošle je do Azure Sentinel.

### <a name="common-use-cases-and-scenarios"></a>Běžné případy použití a scénáře

- Připojte se incidenty M365 Defenderu jedním kliknutím, včetně všech výstrah a entit z komponent M365 Defenderu, do služby Azure Sentinel.

- Obousměrná synchronizace mezi incidenty Sentinel a M365D podle stavu, vlastníka a důvodu uzavření.

- Využijte možnosti seskupování a navýšení výstrah M365 Defenderu v Azure Sentinel, což zkracuje dobu, kterou je potřeba vyřešit.

- Přímý odkaz v kontextu mezi incidentem Sentinel Azure a incidentem paralelního M365u v programu Defender, aby se usnadnila šetření napříč oběma portály.

### <a name="connecting-to-microsoft-365-defender"></a>Připojení k Microsoft 365 Defenderu

Jakmile povolíte datový konektor programu Microsoft 365 Defender pro [shromažďování incidentů a upozornění](connect-microsoft-365-defender.md), M365D se incidenty ve frontě ověřovacích incidentů Azure s **Microsoft 365 Defenderem** v poli **název produktu** krátce po jejich vygenerování v M365 Defenderu.
- Může trvat až 10 minut od času, kdy se incident vygeneruje v M365 Defenderu až do doby, kdy se objeví v rámci Azure Sentinel.

- Incidenty se ingestují a synchronizují bez dalších poplatků.

Po připojení integrace M365 Defenderu se všechny konektory výstrah komponent (MDE, MDI, MDO, MCAS) automaticky připojí na pozadí, pokud už nejsou. Pokud jste po připojení M365 Defender nakoupili licence na součást, budou se upozornění a incidenty z nového produktu dál přesměrovat do Azure Sentinel bez další konfigurace nebo poplatku.

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>Incidenty M365 Defenderu a pravidla vytváření incidentů Microsoftu

- Incidenty vygenerované M365 Defenderem na základě výstrah přicházejících z produktů zabezpečení M365 se vytvářejí pomocí vlastní M365 logiky.

- Pravidla vytváření incidentu Microsoftu v Azure Sentinel taky vytvářejí incidenty ze stejných výstrah pomocí vlastní logiky Sentinel Azure.

- Použití obou mechanismů je zcela podporováno a tuto konfiguraci lze použít k usnadnění přechodu na novou logiku vytvoření incidentu v programu M365 Defender. Tato akce však vytvoří **duplicitní incidenty** pro stejné výstrahy.

- Abyste se vyhnuli vytváření duplicitních incidentů pro stejné výstrahy, doporučujeme, aby zákazníci při připojení M365 Defenderu vypnuli všechna **pravidla vytváření incidentů Microsoftu** pro M365 Products (MDE, MDI a MDO – viz MCAS níže). To lze provést tak, že označíte příslušné zaškrtávací políčko na stránce konektoru. Mějte na paměti, že pokud to uděláte, nepoužijí se pro integraci incidentu M365 Defender žádné filtry, které byly aplikovány na pravidla vytváření incidentu.

- Pro výstrahy Microsoft Cloud App Security (MCAS) nejsou v současné době připojení všech typů výstrah k M365 Defenderu. Abyste se ujistili, že pořád máte incidenty pro všechny výstrahy MCAS, musíte zachovat nebo vytvořit **pravidla pro vytváření incidentů od Microsoftu** pro typy výstrah, které *nejsou* připojené k M365D.

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>Práce s incidenty M365 Defenderu v Azure Sentinel a Obousměrná synchronizace

Incidenty programu M365 Defender se zobrazí ve frontě událostí Sentinel Azure s názvem produktu **Microsoft 365 Defenderu** a s podobnými podrobnostmi a funkcemi pro jakékoli jiné incidenty Sentinel. Každý incident obsahuje odkaz zpátky na paralelní incident na portálu M365 Defender.

Když se incident vyvíjí v M365 Defenderu a do něj se přidají další výstrahy nebo entity, incident Sentinel Azure se odpovídajícím způsobem aktualizuje.

Změny stavu, důvodu uzavření nebo přiřazení incidentu M365 v M365D nebo ve službě Azure Sentinel se také aktualizují ve frontě incidentů druhé. Synchronizace bude provedena na obou portálech hned po použití změny incidentu bez prodlení. K zobrazení nejnovějších změn může být potřeba aktualizovat.

V M365 Defenderu je možné všechny výstrahy z jednoho incidentu přenést na jiný, což vede ke sloučeným incidentům. Pokud k tomu dojde, projeví se změny v incidentech Sentinel Azure. Jeden incident bude obsahovat všechny výstrahy z původních incidentů a druhý incident bude automaticky uzavřen s příznakem "Přesměrováno".

> [!NOTE]
> Incidenty v Azure Sentinel můžou obsahovat maximálně 150 výstrah. M365D incidenty můžou mít víc než tento. Pokud se M365D incident s více než 150 výstrahami synchronizuje do Azure Sentinel, bude se incident Sentinel zobrazovat jako "150 +" výstrahy a bude obsahovat odkaz na paralelní incident v M365D, kde se zobrazí kompletní sada výstrah.

## <a name="advanced-hunting-event-collection"></a>Pokročilá kolekce událostí pro lov

Konektor Microsoft 365 Defender také umožňuje streamovat pokročilé události pro **lovecké** události – typ nezpracovaných dat událostí od Microsoft 365 Defenderu a služeb komponent do Azure Sentinel. V současné době můžete shromažďovat [Pokročilé](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) události v [programu Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) a streamovat je přímo do účelově sestavených tabulek v pracovním prostoru Sentinel Azure. Tyto tabulky jsou postavené na stejném schématu, které se používá na portálu Microsoft 365 Defender. získáte tak úplný přístup k celé sadě pokročilých loveckých událostí, které vám umožní provádět následující akce:

- Ke snadnému kopírování vašich stávajících dotazů na řešení pro řešení pro koncové body z Azure na službu Azure Sentinel.

- Pomocí nezpracovaných protokolů událostí můžete poskytovat další přehledy o výstrahách, jejich vzájemném využívání a vyšetřování a korelovat tyto události s ostatními zdroji dat v Azure Sentinel.

- Uložte protokoly se zvýšeným uchováváním, a to nad rámec programu Microsoft Defender pro koncový bod nebo výchozí uchování Microsoft 365 Defenderu po dobu 30 dnů. Můžete to udělat tak, že nakonfigurujete uchovávání pracovního prostoru nebo nakonfigurujete uchovávání podle jednotlivých tabulek v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak využívat Microsoft 365 Defender společně s Azure Sentinel pomocí konektoru Microsoft 365 Defenderu.

- Získejte pokyny [, jak povolit konektor programu Microsoft 365 Defender](connect-microsoft-365-defender.md).
- Vytvářejte [vlastní výstrahy](tutorial-detect-threats-custom.md) a [Prozkoumejte incidenty](tutorial-investigate-cases.md).
