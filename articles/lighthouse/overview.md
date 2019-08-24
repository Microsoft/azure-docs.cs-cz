---
title: Co je Azure Lighthouse?
description: Azure Lighthouse umožňuje poskytovatelům služeb poskytovat spravované služby pro zákazníky s vyšším stupněm automatizace a efektivitou.
author: JnHs
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
ms.service: lighthouse
manager: carmonm
ms.openlocfilehash: 05fa16504e25a6bf0f6aa1c0a348284abba6e1ed
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011906"
---
# <a name="what-is-azure-lighthouse"></a>Co je Azure Lighthouse?

Azure Lighthouse nabízí poskytovatelům služeb jedinou řídicí plochu pro zobrazení a správu Azure napříč všemi zákazníky, kteří mají vyšší automatizaci, škálování a vylepšené zásady správného řízení. S Azure Lighthouse můžou poskytovatelé služeb poskytovat spravované služby pomocí komplexních a robustních nástrojů pro správu, které jsou integrované v platformě Azure. Tato nabídka může také těžit z podnikových IT organizací, které spravují prostředky napříč více klienty.

![Diagram s přehledem Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Výhody

Azure Lighthouse pomáhá efektivně a efektivně sestavovat a doručovat spravované služby pro vaše zákazníky. Mezi výhody patří:

- **Správa ve**velkém měřítku: Činnost zákazníků a operace životního cyklu pro správu zákaznických prostředků jsou jednodušší a lépe škálovatelné.
- **Lepší viditelnost a přesnost pro zákazníky**: Zákazníci, jejichž prostředky, které spravujete, budou mít lepší přehled o vašich akcích a přesnou kontrolu nad oborem, který je delegovaný pro správu, zatímco vaše IP adresa se zachová.
- **Komplexní a sjednocené nástroje pro platformu**: Naše nástroje využívají klíčové scénáře poskytovatele služeb, včetně několika modelů licencování, jako jsou EA, CSP a průběžné platby. Nové funkce fungují s existujícími nástroji a rozhraními API, licenčními modely a partnerskými programy, jako je například [program Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/csp-overview). Možnosti Azure Lighthouse, které zvolíte, se dají integrovat do stávajících pracovních postupů a aplikací. svůj dopad na zapojení zákazníků můžete sledovat tak, že propojíte [ID partnera](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

K používání Azure Lighthouse se nevztahují žádné další poplatky za správu prostředků Azure vašich zákazníků.

## <a name="capabilities"></a>Možnosti

Azure Lighthouse nabízí několik způsobů, jak zjednodušit zapojení a správu zákazníků:

- **Správa delegovaných prostředků Azure**: Spravujte prostředky Azure vašich zákazníků bezpečně z vlastního tenanta, aniž byste museli přepínat mezi nimi a řídicími rovinami kontextu. Další informace najdete v tématu [Správa delegovaných prostředků Azure](./concepts/azure-delegated-resource-management.md).
- **Nové prostředí Azure Portal**: Zobrazit informace pro různé klienty na stránce noví **moji zákazníci** v [Azure Portal](https://portal.azure.com). Odpovídající okno **poskytovatelé služeb** umožňuje vašim zákazníkům zobrazovat a spravovat přístup k poskytovateli služeb. Další informace najdete v tématech [zobrazení a Správa zákazníků](./how-to/view-manage-customers.md) a [zobrazení a Správa poskytovatelů služeb](./how-to/view-manage-service-providers.md).
- **Šablony Azure Resource Manager**: Snadnější provádění úloh správy, včetně připojování zákazníků pro správu delegovaných prostředků Azure Další informace najdete v našem [úložišti ukázek](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) a připojení [zákazníka k delegované správě prostředků Azure](how-to/onboard-customer.md).
- **Spravované služby nabízí Azure Marketplace**: Poskytněte svým službám zákazníkům soukromé nebo veřejné nabídky a automaticky je přiřaďte do správy delegovaných prostředků Azure, jako alternativu k připojování pomocí Azure Resource Manager šablon. Další informace najdete v tématu [Azure Marketplace nabídky spravovaných služeb](./concepts/managed-services-offers.md).
- **Spravované aplikace Azure**: Balíčky a aplikace, které můžou zákazníci snadno nasadit a používat ve svých vlastních předplatných. Aplikace se nasadí do skupiny prostředků, ke které přistupujete z vašeho tenanta, což vám umožní spravovat službu jako součást celkového prostředí Azure Lighthouse. Další informace najdete v tématu [Přehled spravovaných aplikací Azure](https://docs.microsoft.com/azure/managed-applications/overview).

> [!NOTE]
> Výše popsané možnosti jsou aktuálně k dispozici ve veřejných cloudech. Informace o regionální dostupnosti jednotlivých služeb najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="next-steps"></a>Další postup

- Další informace o [správě delegovaných prostředků Azure](concepts/azure-delegated-resource-management.md)
- Přečtěte si o [prostředích pro správu mezi klienty](concepts/cross-tenant-management-experience.md).
