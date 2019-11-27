---
title: Aspekty programu Cloud Solution Provider
description: Pro partnery CSP pomáhá Správa delegovaných prostředků Azure zdokonalit zabezpečení a řízení tím, že umožňuje podrobná oprávnění.
ms.date: 10/23/2019
ms.topic: conceptual
ms.openlocfilehash: 7e1e371d8c31c45828ee0565545cb40145b40e92
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463960"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Program Azure Lighthouse a Cloud Solution Provider

Pokud jste partnerem [CSP (Cloud Solution Provider)](https://docs.microsoft.com/partner-center/csp-overview) , můžete již přístup k předplatným Azure vytvořeným pro vaše zákazníky prostřednictvím programu CSP použít pomocí funkce [Spravovat za uživatele (administrate)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) . Tento přístup umožňuje přímou podporu, konfiguraci a správu předplatných vašich zákazníků.

Pomocí [Azure Lighthouse](../overview.md)můžete využít správu delegovaných prostředků Azure spolu s administrate. To pomáhá zvýšit zabezpečení a omezuje zbytečný přístup tím, že umožňuje uživatelům přesnější oprávnění. Umožňuje taky zvýšit efektivitu a škálovatelnost, protože uživatelé můžou pracovat na několika zákaznických předplatných pomocí jediného přihlášení ve vašem tenantovi.

> [!TIP]
> Pokud chcete zajistit ochranu zákaznických prostředků, Projděte si [Doporučené postupy zabezpečení](recommended-security-practices.md) spolu s [požadavky na zabezpečení partnerů](https://docs.microsoft.com/partner-center/partner-security-requirements).

## <a name="administer-on-behalf-of-aobo"></a>Správa jménem (ADMINISTRATE)

V ADMINISTRATE bude mít každý uživatel s rolí [agenta správce](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) ve vašem tenantovi administrate přístup k předplatným Azure, která vytvoříte prostřednictvím programu CSP. Všichni uživatelé, kteří potřebují přístup k předplatným zákazníků, musí být členy této skupiny. ADMINISTRATE neumožňuje flexibilní vytváření různých skupin, které pracují s různými zákazníky, nebo pro povolení různých rolí pro skupiny nebo uživatele.

![Správa tenanta pomocí ADMINISTRATE](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Správa delegovaných prostředků Azure

Pomocí delegované správy prostředků Azure můžete přiřadit různé skupiny různým zákazníkům nebo rolím, jak je znázorněno v následujícím diagramu. Vzhledem k tomu, že uživatelé budou mít odpovídající úroveň přístupu prostřednictvím delegované správy prostředků Azure, můžete snížit počet uživatelů, kteří mají roli agenta správce (a mít tak úplný přístup k ADMINISTRATE). To pomáhá zlepšovat zabezpečení tím, že omezuje zbytečný přístup k prostředkům vašich zákazníků. Nabízí také větší flexibilitu při správě více zákazníků ve velkém měřítku.

Registrace předplatného, které jste vytvořili prostřednictvím programu CSP, se řídí postupem popsaným v tématu připojení [předplatného ke správě delegovaných prostředků Azure](../how-to/onboard-customer.md). Tuto registraci může provádět libovolný uživatel, který má ve vašem tenantovi roli agenta správce.

![Správa tenanta pomocí ADMINISTRATE a správy delegovaných prostředků Azure](../media/csp-2.jpg)

> [!NOTE]
> Stránka [ **moji zákazníci** v Azure Portal](../how-to/view-manage-customers.md) teď obsahuje oddíl **Cloud Solution Provider (Preview)** , který zobrazuje informace o fakturaci a prostředky pro zákazníky CSP, kteří [podepsali smlouvu o zákaznících Microsoftu (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) a jsou v rámci plánu Azure. Další informace najdete v tématu [Začínáme s fakturačním účtem Microsoft Partner Agreement](https://docs.microsoft.com/azure/billing/mpa-overview).
>
> Zákazníci CSP se můžou v této části vyskytnout bez ohledu na to, jestli jsou připojené ke správě delegovaných prostředků Azure. Pokud mají, pak se zobrazí také v části **zákazníci** , jak je popsáno v tématu [zobrazení a Správa zákazníků a delegovaných prostředků](../how-to/view-manage-customers.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
- Naučte se, jak připojit [předplatné ke správě delegovaných prostředků Azure](../how-to/onboard-customer.md).
- Přečtěte si o [programu Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview).
