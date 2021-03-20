---
title: Co je Azure Lighthouse?
description: Azure Lighthouse umožňuje poskytovatelům služeb poskytovat spravované služby pro zákazníky s vyšším stupněm automatizace a efektivitou.
ms.date: 12/16/2020
ms.topic: overview
ms.openlocfilehash: b9215f42e3ad3b7517d14fdaee710a31680ce453
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97617149"
---
# <a name="what-is-azure-lighthouse"></a>Co je Azure Lighthouse?

Azure Lighthouse umožňuje správu pro více tenantů a zajišťuje vyšší automatizaci, větší škálovatelnost a vylepšené zásady správného řízení napříč prostředky i tenanty.

S Azure Lighthouse můžou poskytovatelé služeb poskytovat spravované služby pomocí komplexních a robustních nástrojů pro správu, které jsou integrované v platformě Azure. Zákazníci mají kontrolu nad tím, kdo má přístup ke svému tenantovi a k jakým prostředkům mají přístup, a jaké akce je možné učinit. Tato nabídka může také těžit z [podnikových IT organizací,](concepts/enterprise.md) které spravují prostředky napříč více klienty.

![Diagram s přehledem Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Výhody

Azure Lighthouse pomáhá poskytovatelům služeb efektivně sestavovat a dodávat spravované služby. Mezi výhody patří:

- **Správa ve velkém měřítku**: zapojení zákazníků a operace životního cyklu pro správu zákaznických prostředků jsou jednodušší a lépe škálovatelné. Existující rozhraní API, nástroje pro správu a pracovní postupy lze použít u delegovaných prostředků, včetně počítačů hostovaných mimo Azure, bez ohledu na oblasti, ve kterých se nacházejí.
- **Lepší viditelnost a kontrola pro zákazníky**: zákazníci mají přesnou kontrolu nad obory, které jsou delegované pro správu, a oprávnění, která jsou povolená. Můžou auditovat akce poskytovatele služeb a v případě potřeby odebrat přístup úplně.
- **Komplexní a sjednocené nástroje pro platformy**: naše prostředí nástrojů řeší scénáře poskytovatele služby Key, včetně několika modelů licencování, jako jsou EA, CSP a průběžné platby. Azure Lighthouse funguje se stávajícími nástroji a rozhraními API, licenčními modely, [spravovanými aplikacemi Azure](concepts/managed-applications.md)a partnerskými programy, jako je například [program Cloud Solution Provider (CSP)](/partner-center/csp-overview). Azure Lighthouse můžete integrovat do stávajících pracovních postupů a aplikací a sledovat svůj dopad na zapojení zákazníků [propojením ID partnera](./how-to/partner-earned-credit.md).

K používání Azure Lighthouse ke správě prostředků Azure se nevztahují žádné další poplatky. Azure Lighthouse může používat libovolný zákazník nebo partner Azure.

## <a name="capabilities"></a>Možnosti

Azure Lighthouse nabízí několik způsobů, jak zjednodušit zapojení a správu:

- **Správa delegovaných prostředků Azure**: [Zabezpečená správa prostředků Azure pro zákazníky z vašeho vlastního tenanta](concepts/azure-delegated-resource-management.md)bez nutnosti přepínat mezi nimi a řídicími rovinami kontextu. Předplatná zákazníků a skupiny prostředků je možné delegovat na konkrétní uživatele a role v tenantovi, který má oprávnění k odebrání přístupu podle potřeby.
- **Nové prostředí Azure Portal**: zobrazení informací o vzájemném tenantovi na [stránce **moji zákazníci**](how-to/view-manage-customers.md) v Azure Portal. Odpovídající stránka [ **poskytovatelé služeb**](how-to/view-manage-service-providers.md) umožňuje zákazníkům zobrazit a spravovat jejich přístup k poskytovateli služeb.
- **Azure Resource Manager šablony**: k [zprovoznění delegovaných prostředků zákazníků](how-to/onboard-customer.md) a [provádění úloh správy mezi klienty](samples/index.md)použijte šablony ARM.
- **Nabídky spravovaných služeb v Azure Marketplace**: nabízí [vaše služby zákazníkům](concepts/managed-services-offers.md) prostřednictvím soukromých nebo veřejných nabídek a automaticky je zařadí do Azure Lighthouse.

> [!TIP]
> Podobná nabídka, [Microsoft 365 Lighthouse](https://techcommunity.microsoft.com/t5/small-and-medium-business-blog/announcing-microsoft-365-lighthouse-for-managed-service/ba-p/1698181), pomůže PARTNERům IT přihlížet do provozu, monitorovat a spravovat své Microsoft 365 zákazníky ve velkém měřítku. Microsoft 365 Lighthouse je aktuálně ve verzi Private Preview.

## <a name="next-steps"></a>Další kroky

- Další informace o [správě delegovaných prostředků Azure](concepts/azure-delegated-resource-management.md)
- Prozkoumejte [prostředí pro správu mezi klienty](concepts/cross-tenant-management-experience.md).
- Podívejte se, jak [používat Azure Lighthouse v rámci podniku](concepts/enterprise.md).
- Podívejte se na podrobnosti o rozsahu auditu [dostupnosti](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all) a [FEDRAMP a DoD CC SRG](../azure-government/compliance/azure-services-in-fedramp-auditscope.md) pro Azure Lighthouse.
