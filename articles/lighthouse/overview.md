---
title: Co je Azure maják?
description: Azure maják umožňuje poskytovat spravované služby pro své zákazníky s využitím efektivity a vyšší automatizace ve velkém měřítku poskytovatelů služeb.
author: JnHs
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
ms.service: lighthouse
manager: carmonm
ms.openlocfilehash: eb55af5a1121eb193bb76efc9f9e0b833f4b5a1f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809817"
---
# <a name="what-is-azure-lighthouse"></a>Co je Azure maják?

Azure maják nabízí poskytovatelé služeb roviny jeden ovládací prvek zobrazení a správa Azure napříč svým zákazníkům s vyšší automatizace, škálování a vylepšené zásad správného řízení. S Azure maják poskytovatelů služeb doručovat spravované služby pomocí nástroje pro správu komplexní a robustní integrovaná do platformy Azure. Tuto nabídku mohou také těžit podnikové IT organizace, Správa prostředků ve více tenantech.

![Diagram s přehledem nástroje maják Azure](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Výhody

Maják Azure vám umožní ziskem a efektivně vytvářet a poskytovat spravované služby pro vaše zákazníky. Mezi výhody patří:

- **Správy ve velkém měřítku**: Operace zapojení a životního cyklu zákazníků ke správě prostředků zákazníků jsou větší škálovatelnost a jednodušší.
- **Nabízí větší přehled a přesnosti pro zákazníky, kteří**: Zákazníci, jehož prostředky, které spravujete, bude mít větší přehled o vašich akcí a mít naprostou kontrolu nad oboru delegují pro správu, zatímco vaše IP adresa se zachovají.
- **Nástroje pro komplexní a unifikované platformy**: Naše nástroje a funkce řeší klíče služby poskytovatele scénáře, včetně více licenční modely, jako je například EA, CSP a s průběžnými platbami. Nové možnosti práce s existující nástroje a rozhraní API, licenční modely a partnerských programy, jako [programu Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/csp-overview). Azure maják možností si zvolit je možné integrovat do stávajících pracovních postupů a aplikací, a můžete sledovat vliv na zákazníky podle [propojení ID partnera](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

Nejsou žádné další náklady spojené s použitím maják Azure ke správě prostředků Azure vašich zákazníků.

## <a name="capabilities"></a>Možnosti

Azure maják zahrnuje několik způsobů, jak vám pomůžou zjednodušit zapojení zákazníků a správy:

- **Delegovaná správa prostředků Azure**: Spravovat vaši zákazníci prostředků Azure z v rámci vašeho vlastního tenanta, aniž byste museli přepínat kontext a ovládací prvek rovin. Další informace najdete v tématu [delegovat správu prostředků Azure](./concepts/azure-delegated-resource-management.md).
- **Nový web Azure portal, prostředí**: Zobrazit informace o napříč tenanty v nové **zákazníci** stránku [webu Azure portal](https://portal.azure.com). Odpovídající **poskytovatelé služeb** okno dá vašim zákazníkům umožňuje zobrazit a spravovat přístup poskytovatele služeb. Další informace najdete v tématu [zobrazit a spravovat zákazníky](./how-to/view-manage-customers.md) a [zobrazení a Správa poskytovatelů služeb](./how-to/view-manage-service-providers.md).
- **Šablony Azure Resource Manageru**: Mnohem snazší provádět úlohy správy, včetně zprovoznění zákazníků Azure delegovat správu prostředků. Další informace najdete v tématu naše [ukázkové úložiště](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) a [zapojení zákazníků Azure Delegovaná správa prostředků](how-to/onboard-customer.md).
- **Spravované služby nabídky na webu Azure Marketplace**: Nabídka služeb zákazníkům prostřednictvím nabídky privátní nebo veřejné a potom kliknul automaticky připojit ke správě prostředků Azure delegovaný jako alternativu k registraci pomocí šablon Azure Resource Manageru. Další informace najdete v tématu [spravované nabídky služeb v Azure Marketplace](./concepts/managed-services-offers.md).
- **Spravované aplikace Azure**: Balíček a dodávejte aplikace, které jsou vaši zákazníci mohli nasazení a používání v jejich vlastních předplatných. Aplikace je nasazená do skupiny prostředků můžete přistupovat z vašeho tenanta, takže můžete jednoduše spravovat službu jako součást celkové prostředí Azure maják. Další informace najdete v tématu [Azure managed applications přehled](https://docs.microsoft.com/azure/managed-applications/overview).

> [!NOTE]
> Možnosti popsané výše jsou aktuálně k dispozici ve veřejných cloudech. Regionální dostupnost jednotlivých služeb, naleznete v tématu [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).