---
title: Nejčastější dotazy k Azure Security Center – otázky k virtuálním počítačům
description: Nejčastější dotazy k virtuálním počítačům v Azure Security Center, produkt, který pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 1d91f8242e31aa1efdf9301101307d5bad39a459
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342208"
---
# <a name="faq---questions-about-virtual-machines"></a>Nejčastější dotazy týkající se virtuálních počítačů


## <a name="what-types-of-virtual-machines-are-supported"></a>Jaké typy virtuálních počítačů se podporují?

Monitorování a doporučení jsou k dispozici pro virtuální počítače vytvořené pomocí [modelu nasazení Classic i správce prostředků](/azure/security-center/./azure-resource-manager/management/deployment-models).

Seznam podporovaných platforem najdete [v tématu podporované platformy v Azure Security Center](security-center-os-coverage.md) .


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Proč Azure Security Center nerozpoznají antimalwarové řešení běžící na mém virtuálním počítači Azure?

Azure Security Center má přehled o antimalwaru nainstalovaném prostřednictvím rozšíření Azure. Security Center například nedokáže detekovat Antimalwarový program, který byl předem nainstalován na vámi poskytnutou bitovou kopii, nebo pokud jste nainstalovali antimalware na virtuálních počítačích pomocí vlastních procesů (například systémů pro správu konfigurace).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Proč se mi zobrazí zpráva "chybějící data kontroly" pro svůj virtuální počítač?

Tato zpráva se zobrazí, pokud na virtuálním počítači nejsou žádná data kontroly. Naplnění dat kontroly po povolení shromažďování dat ve službě Azure Security Center může nějakou chvíli trvat (méně než hodinu). Tato zpráva se vám může zobrazit po počátečním naplnění dat kontroly, protože neexistují vůbec žádná data kontroly ani žádná nová data kontroly. Kontroly se nenaplňují u virtuálních počítačů v zastaveném stavu. Tato zpráva se může zobrazit i v poslední době (v souladu se zásadami uchovávání informací pro agenta pro Windows, který má výchozí hodnotu 30 dnů).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak často Security Center hledat ohrožení zabezpečení operačního systému, aktualizace systému a problémy s ochranou koncového bodu?

Níže jsou uvedeny doby latence Security Center prověřování chyb zabezpečení, aktualizací a problémů:

- Konfigurace zabezpečení operačního systému – data se aktualizují během 48 hodin.
- Aktualizace systému – data se aktualizují během 24 hodin.
- Problémy s Endpoint Protection – data se aktualizují během 8 hodin.

Security Center obvykle vyhledává nová data každou hodinu a příslušná doporučení aktualizuje. 

> [!NOTE]
> Security Center používá agenta Log Analytics ke shromažďování a ukládání dat. Další informace najdete v tématu [migrace platformy Azure Security Center](./security-center-enable-data-collection.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Proč se zobrazí zpráva "agent virtuálního počítače chybí?"

Na virtuálních počítačích musí být nainstalován agent virtuálního počítače, aby bylo možné shromažďování dat povolit. Agent virtuálního počítače je ve výchozím nastavení nainstalovaný na virtuálních počítačích nasazených z Azure Marketplace. Informace o tom, jak nainstalovat agenta virtuálního počítače na jiné virtuální počítače, najdete v blogovém příspěvku [agenta a rozšíření virtuálního počítače](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).