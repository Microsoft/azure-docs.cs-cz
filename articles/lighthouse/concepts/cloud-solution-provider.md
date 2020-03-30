---
title: Aspekty programu Cloud Solution Provider
description: Pro partnery CSP azure delegované správy prostředků pomáhá zlepšit zabezpečení a řízení povolením podrobných oprávnění.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: 66ea74751f12a499a1e2d9e083497da31746e3c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456906"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse a program poskytovatele cloudových řešení

Pokud jste partnerem [zprostředkovatele csp (Poskytovatel cloudových řešení),](https://docs.microsoft.com/partner-center/csp-overview) můžete již získat přístup k předplatným Azure vytvořeným pro vaše zákazníky prostřednictvím programu CSP pomocí funkce [Spravovat jménem (AOBO).](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) Tento přístup umožňuje přímou podporu, konfiguraci a správu předplatných zákazníků.

S [Azure Lighthouse](../overview.md)můžete používat Azure delegované správy prostředků spolu s AOBO. To pomáhá zlepšit zabezpečení a snižuje zbytečný přístup povolením podrobnější oprávnění pro uživatele. Umožňuje také vyšší efektivitu a škálovatelnost, protože vaši uživatelé mohou pracovat napříč více zákaznickými předplatnými pomocí jediného přihlášení ve vašem tenantovi.

> [!TIP]
> Chcete-li chránit prostředky zákazníků, zkontrolujte a dodržujte naše [doporučené postupy zabezpečení](recommended-security-practices.md) spolu s požadavky na zabezpečení [partnera](https://docs.microsoft.com/partner-center/partner-security-requirements).

## <a name="administer-on-behalf-of-aobo"></a>Spravovat jménem (AOBO)

S AOBO bude mít každý uživatel s rolí [agenta správce](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) ve vašem tenantovi přístup AOBO k předplatným Azure, které vytvoříte prostřednictvím programu CSP. Všichni uživatelé, kteří potřebují přístup k odběru zákazníků, musí být členem této skupiny. AOBO neumožňuje flexibilitu vytvářet odlišné skupiny, které pracují s různými zákazníky, nebo povolit různé role pro skupiny nebo uživatele.

![Správa tenanta pomocí AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Správa delegovaných prostředků Azure

Pomocí Azure delegované správy prostředků můžete přiřadit různé skupiny různým zákazníkům nebo rolím, jak je znázorněno v následujícím diagramu. Vzhledem k tomu, že uživatelé budou mít odpovídající úroveň přístupu prostřednictvím správy delegovaných prostředků Azure, můžete snížit počet uživatelů, kteří mají roli agenta správce (a tedy mít úplný přístup AOBO). To pomáhá zlepšit zabezpečení tím, že omezuje zbytečný přístup k prostředkům vašich zákazníků. Poskytuje také větší flexibilitu při správě více zákazníků ve velkém měřítku.

Registrace předplatného, které jste vytvořili prostřednictvím programu CSP, se řídí kroky popsanými v [části Onboard a subscription to Azure delegated resourcemanagement](../how-to/onboard-customer.md). Toto připojení může provést každý uživatel, který má roli agenta správce ve vašem tenantovi.

![Správa tenanta pomocí AOBO a Azure delegované správy prostředků](../media/csp-2.jpg)

> [!NOTE]
> Stránka [ **Moji zákazníci** na portálu Azure](../how-to/view-manage-customers.md) teď obsahuje část **Poskytovatel cloudových řešení (Preview),** která zobrazuje fakturační údaje a prostředky pro zákazníky CSP, kteří [podepsali smlouvu microsoft zákazníka (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) a jsou [v rámci plánu Azure](https://docs.microsoft.com/partner-center/azure-plan-get-started). Další informace najdete [v tématu Začínáme s fakturačním účtem smlouvy Microsoft Partner .](../../billing/mpa-overview.md)
>
> Zákazníci CSP se mohou v této části zobrazit bez ohledu na to, zda byli také na palubě pro správu delegovaných prostředků Azure. Pokud ano, zobrazí se také v části **Zákazníci,** jak je popsáno v [části Zobrazení a správa zákazníků a delegovaných prostředků](../how-to/view-manage-customers.md). Podobně se zákazník zprostředkovatele csp nemusí zobrazit v části **Poskytovatel cloudových řešení (preview)** **Moji zákazníci,** abyste je mohli zavést pro správu delegovaných prostředků Azure.

## <a name="next-steps"></a>Další kroky

- Další informace o [prostředích správy mezi tenanty](cross-tenant-management-experience.md).
- Přečtěte si, jak [zavést předplatné pro správu delegovaných prostředků Azure](../how-to/onboard-customer.md).
- Další informace o [programu Poskytovatele cloudových řešení](https://docs.microsoft.com/partner-center/csp-overview).
