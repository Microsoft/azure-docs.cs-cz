---
title: Kurz pro přístup k & Application Controls – Azure Security Center
description: V tomto kurzu se dozvíte, jak nakonfigurovat zásady přístupu k VIRTUÁLNÍm počítačům za běhu a zásadu řízení aplikací.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 56dd74fba46aa8b79c94b1460996bb6edb1ff93f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904602"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Kurz: Ochrana prostředků pomocí Azure Security Center
Security Center omezuje vaše vystavení hrozbám díky tomu, že pomocí řízení přístupu a aplikací blokuje škodlivé aktivity. Přístup k virtuálnímu počítači za běhu (JIT) snižuje vaši expozici útokům tím, že vám umožní odepřít trvalý přístup k virtuálním počítačům. Místo toho můžete poskytovat řízený a auditovaný přístup k virtuálním počítačům pouze v případě potřeby. Adaptivní řízení aplikací pomáhá posílit ochranu virtuálních počítačů před malwarem díky tomu, že řídí, které aplikace se na virtuálních počítačích můžou spouštět. Security Center využívá strojové učení k analýze procesů spuštěných ve virtuálním počítači a pomůže vám s použitím tohoto inteligentního zobrazení použít pravidla pro zadávání seznamů povolených.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Konfigurace zásad přístupu k VIRTUÁLNÍm počítačům za běhu
> * Konfigurace zásady řízení aplikací

## <a name="prerequisites"></a>Požadavky
Pro krokování s funkcemi popsanými v tomto kurzu musíte mít povolený Azure Defender. Azure Defender si můžete vyzkoušet bez jakýchkoli nákladů. Další informace najdete v tématu [Vyzkoušejte si Azure Defender](security-center-pricing.md).

## <a name="manage-vm-access"></a>Správa přístupu k virtuálním počítačům
Přístup k virtuálnímu počítači JIT se dá použít k uzamknutí příchozího provozu do virtuálních počítačů Azure. tím se sníží riziko útoků na útoky a zároveň se v případě potřeby zajistí snadný přístup k virtuálním počítačům.

Porty pro správu nemusí být otevřené nepřetržitě. Musí být otevřené pouze během připojení k virtuálnímu počítači, například kvůli provádění úloh správy nebo údržby. Pokud je povolený program just-in-time, Security Center používá pravidla skupiny zabezpečení sítě (NSG), která omezují přístup k portům pro správu, aby na ně nedokázali zacílit útočníci.

Postupujte podle pokynů v části [zabezpečení portů pro správu pomocí přístupu](security-center-just-in-time.md)za běhu.

## <a name="harden-vms-against-malware"></a>Posílení ochrany virtuálních počítačů před malwarem
Adaptivní řízení aplikací pomáhá definovat sadu aplikací, které mají povoleno spouštění v nakonfigurovaných skupinách prostředků. To kromě jiných výhod pomáhá posílit ochranu virtuálních počítačů před malwarem. Security Center využívá strojové učení k analýze procesů spuštěných ve virtuálním počítači a pomůže vám s použitím tohoto inteligentního zobrazení použít pravidla pro zadávání seznamů povolených.

Postupujte podle pokynů v části [použití adaptivních řízení aplikací k omezení ploch útoků počítačů](security-center-adaptive-application.md).

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak omezit své vystavení hrozbám prostřednictvím:

> [!div class="checklist"]
> * Konfigurace zásad přístupu k VIRTUÁLNÍm počítačům za běhu, aby poskytovala řízený a auditovaný přístup k virtuálním počítačům pouze v případě potřeby
> * Konfigurace zásady adaptivního řízení aplikací, která řídí, které aplikace se na virtuálních počítačích můžou spouštět.

V dalším kurzu najdete informace o reakci na incidenty zabezpečení.

> [!div class="nextstepaction"]
> [Kurz: Reakce na incidenty zabezpečení](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
