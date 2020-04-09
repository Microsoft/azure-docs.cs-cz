---
title: Co je Azure Lighthouse?
description: Azure Lighthouse umožňuje poskytovatelům služeb poskytovat spravované služby svým zákazníkům s vyšší automatizací a efektivitou ve velkém měřítku.
ms.date: 11/11/2019
ms.topic: overview
ms.openlocfilehash: 1d4d33238f30c2a579c6a0da6a39238207db4f4a
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982782"
---
# <a name="what-is-azure-lighthouse"></a>Co je Azure Lighthouse?

Azure Lighthouse nabízí poskytovatelům služeb jednu řídicí rovinu pro zobrazení a správu Azure napříč všemi svými zákazníky s vyšší automatizací, škálovacím škálem a vylepšeným řízením zásad správného řízení. S Azure Lighthouse můžou poskytovatelé služeb poskytovat spravované služby pomocí komplexních a robustních nástrojů pro správu integrovaných do platformy Azure. Tato nabídka může být také přínosem pro podnikové IT organizace, které spravují prostředky napříč více tenanty.

![Přehledový diagram Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Výhody

Azure Lighthouse vám pomůže se ziskem a efektivním vytvářením a poskytováním spravovaných služeb pro vaše zákazníky. Nabízí například tyto výhody:

- **Správa ve velkém měřítku:** Zapojení zákazníků a operace životního cyklu pro správu zdrojů zákazníků jsou jednodušší a škálovatelnější.
- **Větší viditelnost a přesnost pro zákazníky**: Zákazníci, jejichž prostředky spravujete, budou mít větší přehled o vašich akcích a přesnou kontrolu nad oborem, který delegují pro správu, zatímco vaše IP adresa bude zachována.
- **Komplexní a jednotné nástroje pro platformu**: Naše nástroje se zabývají klíčovými scénáři poskytovatelů služeb, včetně několika licenčních modelů, jako je EA, CSP a průběžné platby. Nové funkce pracují s existujícími nástroji a api, licenčními modely a partnerskými programy, jako je [program Zprostředkovatel cloudových řešení (CSP).](https://docs.microsoft.com/partner-center/csp-overview) Možnosti azure lighthouse, které zvolíte, lze integrovat do vašich stávajících pracovních postupů a aplikací a můžete sledovat svůj vliv na zapojení zákazníků [propojením id svého partnera](../billing/billing-partner-admin-link-started.md).

Se správou prostředků Azure Azure Pro správu prostředků Azure nejsou spojeny žádné další náklady.

## <a name="capabilities"></a>Možnosti

Azure Lighthouse obsahuje několik způsobů, jak zefektivnit zapojení a správu zákazníků:

- **Správa delegovaných prostředků Azure:** Spravujte prostředky Azure bezpečně z vlastního tenanta, aniž byste museli přepínat kontext a řídit roviny. Další informace najdete v tématu [Azure delegované správy prostředků](concepts/azure-delegated-resource-management.md).
- **Nová prostředí portálu Azure**: Zobrazení informací mezi klienty na nové stránce **Moji zákazníci** na [webu Azure Portal](https://portal.azure.com). Odpovídající okno **poskytovatelů služeb** umožňuje zákazníkům zobrazit a spravovat přístup k poskytovateli služeb. Další informace najdete v [tématu Zobrazení a správa zákazníků](./how-to/view-manage-customers.md) a Zobrazení a správa poskytovatelů [služeb](how-to/view-manage-service-providers.md).
- **Šablony Azure Resource Manageru:** Snadněji provádějte úlohy správy, včetně registrace zákazníků pro správu delegovaných prostředků Azure. Další informace najdete v našich [ukázkách repo](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) a [na palubě zákazníka do Azure delegované správy prostředků](how-to/onboard-customer.md).
- **Nabídky spravovaných služeb na Azure Marketplace**: Nabídněte své služby zákazníkům prostřednictvím soukromých nebo veřejných nabídek a nechte je automaticky na palubě do azure delegované správy prostředků jako alternativu k onboardingu pomocí šablon Azure Resource Manager. Další informace najdete v tématu [Nabídky spravovaných služeb na Azure Marketplace](concepts/managed-services-offers.md).
- **Spravované aplikace Azure:** Balíčky a doručují aplikace, které mohou zákazníci snadno nasadit a používat ve vlastních předplatných. Aplikace se nasadí do skupiny prostředků, ke které máte přístup od svého tenanta, což vám umožní spravovat službu jako součást celkového prostředí Azure Lighthouse. Další informace najdete v tématu [Přehled spravovaných aplikací Azure](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Další kroky

- Další informace o [správě delegovaných prostředků Azure](concepts/azure-delegated-resource-management.md)
- Další informace o [prostředích správy mezi tenanty](concepts/cross-tenant-management-experience.md).
