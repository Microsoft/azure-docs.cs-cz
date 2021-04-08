---
title: Výhody migrace klasického nasazení v Azure AD Domain Services | Microsoft Docs
description: Další informace o výhodách migrace klasického nasazení Azure Active Directory Domain Services do modelu nasazení Správce prostředků
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: justinha
ms.openlocfilehash: 8cc5f5ebf389d35df02474d0561dc7827cde4d0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620082"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>Výhody migrace z modelu nasazení Classic na Správce prostředků v Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure služba AD DS) umožňuje migrovat existující spravovanou doménu, která používá model nasazení Classic, do modelu nasazení Správce prostředků. Azure služba AD DS spravované domény, které používají model nasazení Správce prostředků poskytují další funkce, jako jsou jemně odstupňované zásady pro hesla, protokoly auditu a ochrana před uzamčením účtu.

Tento článek popisuje výhody migrace. Informace o tom, jak začít, najdete v tématu [migrace Azure AD Domain Services z modelu klasických virtuálních sítí do Správce prostředků][howto-migrate].

> [!NOTE]
> V 2017 je Azure AD Domain Services k dispozici pro hostování v Azure Resource Manager síti. Od té doby jsme dokázali vytvořit bezpečnější službu pomocí moderních možností Azure Resource Manager. Vzhledem k tomu, že Azure Resource Manager nasazení plně nahrazují klasická nasazení, nasazení Azure služba AD DS Classic Virtual Network se vyřadí 1. března 2023.
>
> Další informace najdete v [oficiálním oznámení o zastarání](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/) .

## <a name="migration-benefits"></a>Výhody migrace

Proces migrace převezme existující spravovanou doménu, která používá model nasazení Classic, a přesune se na použití modelu nasazení Správce prostředků. Když migrujete spravovanou doménu z modelu nasazení Classic na Správce prostředků, vyhnete se nutnosti znovu připojit počítače ke spravované doméně nebo odstranit spravovanou doménu a vytvořit ji úplně od začátku. Na konci procesu migrace jsou virtuální počítače i nadále připojené ke spravované doméně.

Po migraci poskytuje Azure služba AD DS mnoho funkcí, které jsou k dispozici pouze pro domény pomocí modelu nasazení Správce prostředků, jako je například následující:

* [Podpora jemně odstupňovaných zásad hesel][password-policy].
* Rychlejší synchronizace rychlostí mezi Azure AD a Azure AD Domain Services.
* Dva nové [atributy, které se synchronizují z Azure AD][attributes]  -  *Manageru* a *EmployeeID*.
* Přístup k řadičům domény s vyšším výkonem při [upgradu SKU][skus].
* Ochrana při uzamčení účtu AD.
* [E-mailová oznámení pro výstrahy ve spravované doméně][email-alerts]
* [K zobrazení protokolů auditu a aktivit přihlášení použijte Azure sešity a Azure monitor][workbooks].
* V podporovaných oblastech [zóny dostupnosti Azure][availability-zones].
* Integruje se s ostatními produkty Azure, jako jsou [soubory Azure][azure-files], [HD Insights][hd-insights]a [virtuální plocha Windows][wvd].
* Podpora má přístup k další telemetrii a může pomoct řešit efektivněji.
* Šifrování v klidovém formátu pomocí [Azure Managed disks][managed-disks] pro data na spravovaných řadičích domény.

Spravované domény, které používají model nasazení Správce prostředků, vám pomůžou udržet aktuálnost s nejnovějšími novými funkcemi. Nové funkce nejsou k dispozici pro spravované domény, které používají model nasazení Classic.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít, najdete v tématu [migrace Azure AD Domain Services z modelu klasických virtuálních sítí do Správce prostředků][howto-migrate].

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/managed-disks-overview.md
