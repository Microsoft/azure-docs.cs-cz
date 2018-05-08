---
title: Migrace platformy pro Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vysvětluje některé změny ve způsobu, jakým se shromažďují data ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.openlocfilehash: 67cb532b6c8a5d00923bc3b41709956971ead5c3
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="azure-security-center-platform-migration"></a>Migrace platformy pro Azure Security Center

Od začátku června 2017 Azure Security Center postupně zavádí důležité změny ve způsobu, jakým se shromažďují a ukládají data zabezpečení.  Tyto změny odemykají nové možnosti, jako je snadné prohledávání dat zabezpečení, a jsou lépe sladěné s dalšími službami pro správu a monitorování Azure.

> [!NOTE]
> Migrace platformy by neměla mít vliv na vaše produkční prostředky a nevyžaduje žádnou akci z vaší strany.


## <a name="whats-happening-during-this-platform-migration"></a>Co se děje během této migrace platformy?

Dříve služba Security Center využívala ke shromažďování dat zabezpečení z virtuálních počítačů agenta Azure Monitoring Agent. Tato data zahrnují informace o konfiguracích zabezpečení, které slouží k identifikaci ohrožení zabezpečení, a události zabezpečení, které slouží k rozpoznání hrozeb. Tato data se ukládala ve vašich účtech Storage v Azure.

Výhledově bude Security Center používat agenta Microsoft Monitoring Agent – to je stejný agent, kterého používá služba Log Analytics. Data shromážděná z tohoto agenta se ukládají v existujících [pracovním prostoru](../log-analytics/log-analytics-manage-access.md) *Log Analytics* přidruženém k vašemu předplatnému Azure nebo v nových pracovních prostorech s ohledem na geografickou polohu virtuálního počítače.

## <a name="agent"></a>Agent

V rámci přechodu se agent Microsoft Monitoring Agent (pro [Windows](../log-analytics/log-analytics-windows-agent.md) nebo [Linux](../log-analytics/log-analytics-linux-agents.md)) nainstaluje na všech virtuálních počítačích Azure, ze kterých se aktuálně shromažďují data.  Pokud je na virtuálním počítači agent Microsoft Monitoring Agent již nainstalovaný, Security Center bude využívat stávajícího nainstalovaného agenta.

Oba agenti budou po nějakou dobu (obvykle několik dnů) pracovat souběžně, aby se zajistil bezproblémový přechod bez ztráty dat. To umožní Microsoftu před ukončením používání stávajícího kanálu ověřit, že je nový datový kanál funkční. Po ověření bude agent Azure Monitoring Agent z virtuálních počítačů odebrán. Od vás se nic nevyžaduje. Jakmile budou všichni zákazníci úspěšně migrováni, obdržíte e-mail s oznámením.
 
Během migrace se nedoporučuje ručně odinstalovat agenta Azure Monitoring Agent, protože by to mohlo vést k mezerám v datech zabezpečení. Pokud potřebujete další pomoc, obraťte se na [Oddělení zákaznických služeb a podpory společnosti Microsoft](https://support.microsoft.com/contactus/). 

Microsoft Monitoring Agent pro Windows vyžaduje použití portu TCP 443. Další informace najdete v [průvodci odstraňováním potíží pro službu Azure Security Center](security-center-troubleshooting-guide.md).


> [!NOTE] 
> Protože agenta Microsoft Monitoring Agent můžou využívat další služby pro správu a monitorování Azure, po vypnutí shromažďování dat ve službě Security Center se agent neodinstaluje automaticky. V případě potřeby však můžete agenta odinstalovat ručně.

## <a name="workspace"></a>Pracovní prostor

Jak je popsáno výše, data shromážděná z agenta Microsoft Monitoring Agent (jménem služby Security Center) se ukládají v existujících pracovních prostorech Log Analytics přidružených k vašemu předplatnému Azure nebo v nových pracovních prostorech s ohledem na geografickou polohu virtuálního počítače.

Na webu Azure Portal můžete procházením zobrazit seznam pracovních prostorů Log Analytics, včetně těch vytvořených službou Security Center. Pro nové pracovní prostory se vytvoří související skupina prostředků. Obojí se řídí těmito zásadami vytváření názvů:

- Pracovní prostor: *DefaultWorkspace-[ID_předplatného]-[zeměpisné umístění]*
- Skupina prostředků: *DefaultResouceGroup-[zeměpisné_umístění]* 
 
U pracovních prostorů vytvořených službou Security Center se data uchovávají po dobu 30 dnů. U existujících pracovních prostorů uchovávání závisí na cenové úrovni pracovního prostoru.

> [!NOTE]
> Data shromážděná dříve službou Security Center zůstanou ve vašich účtech Storage. Po dokončení migrace můžete tyto účty Storage odstranit.

### <a name="security-management-solution"></a>Řešení pro správu zabezpečení 

Pro stávající zákazníky, kteří nemají v Log Analytics nainstalované řešení pro správu zabezpečení, ho Microsoft nainstaluje v jejich pracovním prostoru, ale bude cílit pouze na virtuální počítače Azure. Nesnažte se toto řešení odinstalovat, protože pokud to provedete z konzoly pro správu, neexistuje žádná možnost automatické nápravy.


## <a name="other-updates"></a>Další aktualizace

Ve spojení s migrací platformy postupně zavádíme některé další menší aktualizace:

- Budou podporovány další verze operačních systémů. Seznam najdete [zde](security-center-faq.md#virtual-machines).
- Seznam ohrožení zabezpečení operačních systémů se bude rozšiřovat. Seznam najdete [zde](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- [Ceny](https://azure.microsoft.com/pricing/details/security-center/) se budou účtovat poměrně po hodinách (dříve to bylo po dnech) a díky tomu někteří zákazníci ušetří.
- Shromažďování dat bude vyžadováno a automaticky povoleno pro zákazníky na cenové úrovni Standard.
- Azure Security Center začne zjišťovat antimalwarová řešení, která nebyla nasazena prostřednictvím rozšíření Azure. Jako první bude k dispozici zjišťování nástrojů Symantec Endpoint Protection a Defender pro Windows 2016.
- Zásady a oznámení prevence je možné konfigurovat pouze na úrovni *předplatného*, ale ceny je stále možné nastavit na úrovni *skupiny prostředků*.

