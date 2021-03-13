---
title: Aspekty programu Cloud Solution Provider
description: Pro partnery CSP pomáhá Správa delegovaných prostředků Azure zdokonalit zabezpečení a řízení tím, že umožňuje podrobná oprávnění.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 8736cf913739f2bd16fb519aed98fd336f6876a5
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419384"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Program Azure Lighthouse a Cloud Solution Provider

Pokud jste partnerem [CSP (Cloud Solution Provider)](/partner-center/csp-overview) , můžete již přístup k předplatným Azure vytvořeným pro vaše zákazníky prostřednictvím programu CSP použít pomocí funkce [Spravovat za uživatele (administrate)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) . Tento přístup umožňuje přímou podporu, konfiguraci a správu předplatných vašich zákazníků.

Pomocí [Azure Lighthouse](../overview.md)můžete využít správu delegovaných prostředků Azure spolu s administrate. To pomáhá zvýšit zabezpečení a omezuje zbytečný přístup tím, že umožňuje uživatelům přesnější oprávnění. Umožňuje taky zvýšit efektivitu a škálovatelnost, protože uživatelé můžou pracovat na několika zákaznických předplatných pomocí jediného přihlášení ve vašem tenantovi.

> [!TIP]
> Pokud chcete zajistit ochranu zákaznických prostředků, Projděte si [Doporučené postupy zabezpečení](recommended-security-practices.md) spolu s [požadavky na zabezpečení partnerů](/partner-center/partner-security-requirements).

## <a name="administer-on-behalf-of-aobo"></a>Správa jménem (ADMINISTRATE)

V ADMINISTRATE bude mít každý uživatel s rolí [agenta správce](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) ve vašem tenantovi administrate přístup k předplatným Azure, která vytvoříte prostřednictvím programu CSP. Všichni uživatelé, kteří potřebují přístup k předplatným zákazníků, musí být členy této skupiny. ADMINISTRATE neumožňuje flexibilní vytváření různých skupin, které pracují s různými zákazníky, nebo pro povolení různých rolí pro skupiny nebo uživatele.

![Diagram znázorňující správu tenanta pomocí ADMINISTRATE](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Správa delegovaných prostředků Azure

Pomocí Azure Lighthouse můžete přiřadit různé skupiny různým zákazníkům nebo rolím, jak je znázorněno v následujícím diagramu. Vzhledem k tomu, že uživatelé budou mít odpovídající úroveň přístupu prostřednictvím delegované správy prostředků Azure, můžete snížit počet uživatelů, kteří mají roli agenta správce (a mít tak úplný přístup k ADMINISTRATE). To pomáhá zlepšovat zabezpečení tím, že omezuje zbytečný přístup k prostředkům vašich zákazníků. Nabízí také větší flexibilitu při správě více zákazníků ve velkém měřítku.

Registrace předplatného, které jste vytvořili prostřednictvím programu CSP, se řídí postupem popsaným v tématu připojení [předplatného do Azure Lighthouse](../how-to/onboard-customer.md). Tuto registraci může provádět libovolný uživatel, který má ve vašem tenantovi roli agenta správce.

![Diagram znázorňující správu tenanta pomocí ADMINISTRATE a správy delegovaných prostředků Azure](../media/csp-2.jpg)

> [!TIP]
> [Nabídky spravované služby](managed-services-offers.md) s privátními plány se nepodporují u předplatných vytvořených prostřednictvím prodejce v programu Cloud Solution Provider (CSP). Tyto odběry můžete připojit k Azure Lighthouse [pomocí šablon Azure Resource Manager](../how-to/onboard-customer.md).

> [!NOTE]
> Stránka [ **moji zákazníci** v Azure Portal](../how-to/view-manage-customers.md) teď obsahuje oddíl **Cloud Solution Provider (Preview)** , který zobrazuje informace o fakturaci a prostředky pro zákazníky CSP, kteří [podepsali smlouvu o zákaznících Microsoftu (MCA)](/partner-center/confirm-customer-agreement) a jsou [v rámci plánu Azure](/partner-center/azure-plan-get-started). Další informace najdete v tématu [Začínáme s fakturačním účtem Microsoft Partner Agreement](../../cost-management-billing/understand/mpa-overview.md).
>
> Zákazníci CSP se můžou v této části Zobrazit bez ohledu na to, jestli jsou taky připojené ke službě Azure Lighthouse. Pokud jsou, zobrazí se také v části **zákazníci** , jak je popsáno v tématu [zobrazení a Správa zákazníků a delegovaných prostředků](../how-to/view-manage-customers.md). Podobně není nutné, aby se zákazník v části Customers **(Cloud Solution Provider)** ve svých **zákaznících** nemusel zobrazovat do Azure Lighthouse.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
- Naučte se, jak připojit [předplatné do Azure Lighthouse](../how-to/onboard-customer.md).
- Přečtěte si o [programu Cloud Solution Provider](/partner-center/csp-overview).
