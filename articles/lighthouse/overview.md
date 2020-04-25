---
title: Co je Azure Lighthouse?
description: Azure Lighthouse umožňuje poskytovatelům služeb poskytovat spravované služby pro zákazníky s vyšším stupněm automatizace a efektivitou.
ms.date: 04/23/2020
ms.topic: overview
ms.openlocfilehash: aea1451d995d135506fb93e3fc9e86f67227d5b8
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144263"
---
# <a name="what-is-azure-lighthouse"></a>Co je Azure Lighthouse?

Azure Lighthouse nabízí poskytovatelům služeb jedinou řídicí plochu pro zobrazení a správu Azure napříč všemi zákazníky, kteří mají vyšší automatizaci, škálování a vylepšené zásady správného řízení. S Azure Lighthouse můžou poskytovatelé služeb poskytovat spravované služby pomocí komplexních a robustních nástrojů pro správu, které jsou integrované v platformě Azure. Tato nabídka může také těžit z podnikových IT organizací, které spravují prostředky napříč více klienty.

![Diagram s přehledem Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Výhody

Azure Lighthouse pomáhá efektivně a efektivně sestavovat a doručovat spravované služby pro vaše zákazníky. Nabízí například tyto výhody:

- **Správa ve velkém měřítku**: zapojení zákazníků a operace životního cyklu pro správu zákaznických prostředků jsou jednodušší a lépe škálovatelné.
- **Lepší viditelnost a přesnost pro zákazníky**: zákazníci budou mít lepší přehled o vašich akcích a přesnou kontrolu nad oborem, který je delegovaný pro správu, včetně možnosti odebrat přístup kompletně, i když se vaše IP adresa zachová.
- **Komplexní a sjednocené nástroje pro platformy**: naše prostředí nástrojů řeší scénáře poskytovatele služby Key, včetně několika modelů licencování, jako jsou EA, CSP a průběžné platby. Nové funkce fungují s existujícími nástroji a rozhraními API, licenčními modely a partnerskými programy, jako je například [program Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/csp-overview). Možnosti Azure Lighthouse, které zvolíte, se dají integrovat do stávajících pracovních postupů a aplikací. svůj dopad na zapojení zákazníků můžete sledovat tak, že [propojíte ID partnera](../billing/billing-partner-admin-link-started.md).

K používání Azure Lighthouse se nevztahují žádné další poplatky za správu prostředků Azure vašich zákazníků.

## <a name="capabilities"></a>Možnosti

Azure Lighthouse nabízí několik způsobů, jak zjednodušit zapojení a správu zákazníků:

- **Správa delegovaných prostředků Azure**: zabezpečená Správa prostředků Azure pro zákazníky z vašeho vlastního tenanta bez nutnosti přepínat mezi nimi a řídicími rovinami kontextu. Předplatná a skupiny prostředků se dají delegovat na konkrétní uživatele a role v tenantovi, který má oprávnění k odebrání přístupu podle potřeby. Další informace najdete v tématu [Správa delegovaných prostředků Azure](concepts/azure-delegated-resource-management.md).
- **Nové prostředí Azure Portal**: informace o křížovém tenantovi najdete na stránce noví **moji zákazníci** v [Azure Portal](https://portal.azure.com). Odpovídající okno **poskytovatelé služeb** umožňuje vašim zákazníkům zobrazovat a spravovat přístup k poskytovateli služeb. Další informace najdete v tématech [zobrazení a Správa zákazníků](./how-to/view-manage-customers.md) a [zobrazení a Správa poskytovatelů služeb](how-to/view-manage-service-providers.md).
- **Azure Resource Manager šablony**: snadnější provádění úloh správy, včetně připojování zákazníků pro správu delegovaných prostředků Azure. Další informace najdete v našem [úložišti ukázek](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) a připojení [zákazníka k delegované správě prostředků Azure](how-to/onboard-customer.md).
- **Nabídky spravovaných služeb v Azure Marketplace**: nabízet vaše služby zákazníkům prostřednictvím soukromých nebo veřejných nabídek a automaticky je přinášet do správy delegovaných prostředků Azure, jako alternativu k připojování pomocí šablon Azure Resource Manager. Další informace najdete v tématu [Azure Marketplace nabídky spravovaných služeb](concepts/managed-services-offers.md).
- **Spravované aplikace Azure**: balíčky a dodávání aplikací, které můžou zákazníci snadno nasadit a používat ve svých vlastních předplatných. Aplikace se nasadí do skupiny prostředků, ke které přistupujete z vašeho tenanta, což vám umožní spravovat službu jako součást celkového prostředí Azure Lighthouse. Další informace najdete v tématu [Přehled spravovaných aplikací Azure](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Další kroky

- Další informace o [správě delegovaných prostředků Azure](concepts/azure-delegated-resource-management.md)
- Přečtěte si o [prostředích pro správu mezi klienty](concepts/cross-tenant-management-experience.md).
