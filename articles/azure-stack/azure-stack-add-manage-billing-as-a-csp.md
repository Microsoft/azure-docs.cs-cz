---
title: Spravovat využití a fakturace pro Azure Stack jako poskytovatele cloudových služeb | Dokumentace Microsoftu
description: Procházení prostřednictvím registrace Azure Stack jako Cloud Provider (CSP) a přidání zákazníků pro účely fakturace.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 1ba3697b5c683ed2e892396db71328e0ed41fdce
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56266913"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Spravovat využití a fakturace pro Azure Stack jako poskytovatele cloudových služeb

*Platí pro: Integrované systémy Azure Stack*

Tento článek vás provede registrace Azure Stack jako Cloud Provider (CSP) a přidání zákazníků.

Jako odběratel CSP pracujete s různými zákazníky za použití služby Azure Stack. Každý zákazník má předplatné zprostředkovatele kryptografických služeb v Azure. Využití musí přímo od služby Azure Stack na každé předplatné uživatele.

Následující obrázek znázorňuje kroky potřebné k zvolte svůj účet služby sdílené služby a registraci účtu Azure pomocí účtu Azure Stack. Po registraci můžete připojit vaše koncové zákazníky:

[![Proces pro povolení použití a správa jako poskytovatele cloudových služeb](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "proces pro povolení použití a správa jako poskytovatele cloudových služeb")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Vytvoření zprostředkovatele kryptografických služeb nebo APSS odběru

### <a name="cloud-service-provider-subscription-types"></a>Typy předplatného poskytovatele služeb v cloudu

Zvolte typ účtu sdílených služeb, který používáte pro službu Azure Stack. Jsou typy předplatných, které lze použít pro registraci víceklientské služby Azure Stack:

- Poskytovatel cloudových služeb
- Partnerského předplatného sdílené služby

#### <a name="azure-partner-shared-services"></a>Azure Partner sdílené služby

Předplatná Azure Partner sdílených služeb (APSS) jsou upřednostňované volbou pro registraci při přímé poskytovatele CSP nebo distributora CSP funguje Azure Stack.

APSS předplatná jsou přidružená k tenantovi sdílených služeb. Když si zaregistrujete Azure Stack, zadejte pověření pro účet, který je vlastníkem předplatného. Účet, který používáte k registraci Azure Stack se může lišit od účtu správce, který používáte pro nasazení. Kromě toho dva účty nemusí patřit do stejné domény. můžete nasadit pomocí tenanta, které už používáte. Například můžete použít `ContosoCSP.onmicrosoft.com`, pak zaregistruje pomocí jiného tenanta; například `IURContosoCSP.onmicrosoft.com`. Nezapomeňte se přihlásit pomocí `ContosoCSP.onmicrosoft.com` při provádění každodenní správu služby Azure Stack. Přihlaste se do Azure s využitím `IURContosoCSP.onmicrosoft.com` když potřebujete provádět operace registrace.

Popis APSS předplatná a jak je lze vytvořit, naleznete v tématu [přidat Azure Partner Shared Services](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Předplatná CSP

Cloudová předplatná poskytovatele služeb (CSP) jsou upřednostňované volbou pro registraci při prodejce CSP nebo koncového zákazníka funguje Azure Stack.

## <a name="register-azure-stack"></a>Registrace Azure Stack

Použijte předplatné APSS vytvořené pomocí informací v předchozí části registraci Azure Stack v Azure. Další informace najdete v tématu [registrace Azure Stack s vaším předplatným Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Přidání koncového zákazníka

Ke konfiguraci Azure Stack, takže pokud nového tenanta používá prostředky, jejich používání se oznamuje službě svoje předplatné Cloud Service Provider (CSP), najdete v článku [přidání tenanta pro využití a fakturaci ke službě Azure Stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Poplatek za správné předplatné

Azure Stack používá funkci registrace. Registrace je objekt uložený v Azure. Registrace objektu dokumenty, které předplatných Azure pomocí za dané služby Azure Stack. Tato část se zabývá významu registrace.

Pomocí registrace Azure Stack můžete:

- Azure Commerce předávat data o využití služby Azure Stack a vyúčtování předplatného Azure.
- Sestavy každého zákazníka "s využití v jiném předplatném s víceklientského nasazení Azure Stack. Víceklientské architektury umožňuje službě Azure Stack pro podporu různých organizací na stejnou instanci služby Azure Stack.

Pro každý Azure Stack je jedno výchozí předplatné a mnoho tenantů předplatných. Výchozí předplatné je předplatné Azure, které se účtují, pokud neexistuje odběr specifickým pro tenanta. Musí být první předplatné, které chcete zaregistrovat. Předplatné pro použití více tenantů reporting fungovat, musí být CSP nebo APSS předplatného.

Registrace se pak aktualizuje s předplatným Azure pro každého klienta, který používá Azure Stack. Předplatná tenanta musí být typu CSP a musí zahrnout do partnera, který vlastní výchozí předplatné. Nelze zaregistrovat zákazníci někoho jiného.

Když Azure Stack předává informace o využití na global Azure, služba v Azure consults registrace a použití jednotlivých tenantů se mapuje na příslušnou tenanta předplatného. Pokud tenanta není zaregistrovaný, že využití přejde na výchozí předplatné pro instanci služby Azure Stack, ze které pochází.

Protože tenanta předplatná jsou předplatná CSP, faktuře bude zaslána CSP partner a informace o použití není viditelný pro koncové zákazníky.

## <a name="next-steps"></a>Další postup

- Další informace o programu CSP najdete v tématu [programu Cloud Solution Provider](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Další informace o tom, jak načíst informace o využití prostředků ze služby Azure Stack, najdete v článku [využití a fakturace ve službě Azure Stack](azure-stack-billing-and-chargeback.md).
