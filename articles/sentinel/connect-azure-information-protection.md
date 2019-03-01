---
title: Shromažďování dat služby Azure Information Protection ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Zjistěte, jak shromažďovat data služby Azure Information Protection v nástroji Azure Sentinelu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: db87561fc8c94776335ea910f8a4c0577d5f51fc
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993045"
---
# <a name="collect-data-from-azure-information-protection"></a>Shromažďování dat ze služby Azure Information Protection

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete Streamovat protokoly z [služby Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) do Azure Sentinelu jediným kliknutím. Služba Azure Information Protection pomáhá chránit vaše data, ať už jsou uložená v cloudu nebo místních infrastrukturách a ovládací prvek a pomáhají zabezpečit e-mail, dokumenty a citlivá data, která sdílíte mimo vaši společnost. Od jednoduché klasifikace po vložené popisky a oprávnění zvýšení ochrany dat po celou dobu pomocí služby Azure Information Protection. Při připojení služby Azure Information Protection k Sentinelu Azure, můžete stream všechna oznámení ze služby Azure Information Protection do ověřovacích Azure.


## <a name="prerequisites"></a>Požadavky

- Uživatel s globální správce, správce zabezpečení nebo informace protection oprávnění


## <a name="connect-to-azure-information-protection"></a>Připojení k Azure Information Protection

Pokud už máte Azure Information Protection, ujistěte se, že je [povolena ve vaší síti](https://docs.microsoft.com/azure/information-protection/activate-service).
Pokud je nasazení služby Azure Information Protection a získávání dat, data upozornění snadno Streamovat do ověřovacích Azure.


1. V Azure Sentinelu, vyberte **shromažďování dat** a potom klikněte na tlačítko **služby Azure Information Protection** dlaždici.

2. Přejděte [portál služby Azure Information Protection](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade) 

3. V části **připojení**, nastavit streamování protokolů ze služby Azure Information Protection pro Azure Sentinelu kliknutím [konfigurace analytics](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade)

4. Vyberte pracovní prostor, do kterého jste nasadili Sentinelu Azure. 

5. Klikněte na **OK**.

6. Chcete-li použít příslušné schéma v Log Analytics pro oznámení služby Azure Information Protection, vyhledejte **InformationProtectionLogs_CL**.




## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu služby Azure Information Protection. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).
