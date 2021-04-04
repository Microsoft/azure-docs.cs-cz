---
title: Azure Lighthouse ve scénářích ISV
description: Funkce Azure Lighthouse může používat ISV pro větší flexibilitu s nabídkami zákazníků.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696338"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>Azure Lighthouse ve scénářích ISV

Běžným scénářem pro [Azure Lighthouse](../overview.md) je poskytovatel služeb spravující prostředky v klientech Azure Active Directory (Azure AD) svých zákazníků. Funkce Azure Lighthouse můžou používat i nezávislí výrobci softwaru (ISV), kteří používají nabídky založené na SaaS se svými zákazníky. Azure Lighthouse může být obzvláště užitečná pro nezávislé výrobce softwaru, kteří nabízejí spravované služby nebo podporu, které vyžadují přístup k oboru předplatného.

## <a name="managed-service-offers-in-azure-marketplace"></a>Nabídky spravovaných služeb na Azure Marketplace

Jako ISV jste už možná publikovali řešení Azure Marketplace. Pokud zákazníkům nabízíte spravované služby, můžete tak učinit publikováním nabídky spravované služby. Tyto nabídky zjednodušují proces připojování a zajištění větší škálovatelnosti služeb pro tolik zákazníků podle potřeby. Azure Lighthouse podporuje široké spektrum [úloh správy a scénářů](cross-tenant-management-experience.md#enhanced-services-and-scenarios) , které je možné použít k poskytování hodnoty vašim zákazníkům.

Další informace najdete v tématu [publikování nabídky spravované služby pro Azure Marketplace](../how-to/publish-managed-services-offers.md).

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Používání Azure Lighthouse se spravovanými aplikacemi Azure

[Spravované aplikace Azure](../../azure-resource-manager/managed-applications/overview.md) představují jiný způsob, jakým prodejci můžou poskytovat služby zákazníkům. Pomocí Azure Lighthouse spolu se spravovanými aplikacemi Azure můžete povolit rozšířené scénáře.

Další informace najdete v tématu [Azure Lighthouse a Azure Managed Applications](managed-applications.md).

## <a name="saas-based-multi-tenant-offerings"></a>SaaS nabídky s více klienty

Dalším scénářem je situace, kdy ISV hostuje prostředky v předplatném ve svém vlastním tenantovi, a pak používá Azure Lighthouse, aby zákazníkům umožnili přístup k těmto prostředkům. Zákazník se pak může přihlásit ke svému vlastnímu tenantovi a přistupovat k těmto prostředkům podle potřeby. Nezávislý výrobce softwaru udržuje své IP adresy ve svém vlastním tenantovi a můžou použít vlastní plány podpory k vyvýšení lístků souvisejících s řešením hostovaným ve svém tenantovi, a ne pomocí plánu zákazníka. Vzhledem k tomu, že se prostředky nacházejí v tenantovi nezávislého výrobce softwaru, mohou být všechny akce provedeny přímo nezávislým výrobcem softwaru, jako je například přihlášení k virtuálním počítačům, instalace aplikací a provádění úloh údržby.

V tomto scénáři se uživatelům v tenantovi zákazníka v zásadě udělí přístup jako "Správa tenanta", i když zákazník nespravuje prostředky ISV. Vzhledem k tomu, že přistupují k tenantovi ISV přímo, je důležité udělit jenom minimální potřebná oprávnění, aby zákazníci nemohli neúmyslně provádět změny řešení nebo jiných prostředků ISV.

Aby bylo možné tuto architekturu povolit, musí ISV získat ID objektu pro skupinu uživatelů v tenantovi Azure AD zákazníka spolu s jejich ID tenanta. ISV pak vytvoří šablonu ARM, která tomuto uživateli přidělí příslušná oprávnění a [nasadí ho v předplatném ISV](../how-to/onboard-customer.md) , které obsahuje prostředky, ke kterým má zákazník přístup.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
- Další informace o [správě delegovaných prostředků Azure](azure-delegated-resource-management.md)