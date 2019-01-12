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
ms.date: 10/15/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: 26abc338b617b967c6919ebbe556a81226ff47d8
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247856"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Spravovat využití a fakturace pro Azure Stack jako poskytovatele cloudových služeb 

*Platí pro: Integrované systémy Azure Stack*

Tento článek vás provede registrace Azure Stack jako Cloud Provider (CSP) a přidání zákazníků.

Jako odběratel CSP pracujete s různými zákazníky za použití služby Azure Stack. Každý zákazník má předplatné zprostředkovatele kryptografických služeb v Azure. Bude potřeba nasměrovat pro použití služby Azure Stack na každé předplatné uživatele.

Následující diagram znázorňuje kroky, které budete muset zvolit svůj účet sdílené služby a registraci účtu Azure pomocí účtu Azure Stack. Zaregistrované, můžete si zaregistrovat vaše koncové zákazníky.

**Postup přidání jako zprostředkovatel kryptografických služeb pro sledování využití**

[ ![Proces pro povolení použití a správa jako poskytovatele cloudových služeb](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "proces pro povolení použití a správa jako poskytovatele cloudových služeb") ](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Vytvoření zprostředkovatele kryptografických služeb nebo APSS odběru

### <a name="cloud-service-provider-subscription-types"></a>Typy předplatného poskytovatele služeb v cloudu

Musíte zvolit typ účtu sdílené služby, který používáte pro službu Azure Stack. Jsou typy předplatných, které lze použít pro registraci víceklientské služby Azure Stack:

 - Poskytovatel cloudových služeb 
 - Partnerského předplatného sdílené služby 

#### <a name="azure-partner-shared-services"></a>Azure Partner sdílené služby

Předplatná Azure Partner sdílených služeb (APSS) jsou upřednostňované volbou pro registraci při přímé poskytovatele CSP nebo distributora CSP funguje Azure Stack.

APSS předplatná jsou přidružená k tenantovi sdílených služeb. Když si zaregistrujete Azure Stack, budete muset zadat přihlašovací údaje pro účet, který je vlastníkem předplatného. Účet, který používáte k registraci Azure Stack se může lišit od účtu správce, který používáte pro nasazení. Kromě toho proveďte dva účty *není* musí patřit do stejné domény. Jinými slovy může nasazení, můžete použít klienta, které už používáte. Může například používat ContosoCSP.onmicrosoft.com a poté funkci zaregistrujte pomocí jiného tenanta, třeba IURContosoCSP.onmicrosoft.com. Je potřeba pamatovat, že se přihlásit pomocí ContosoCSP.onmicrosoft.com, když provedete den Azure stacku správu. Při přihlášení do Azure s využitím IURContosoCSP.onmicrosoft.com, když budete chtít provést operace registrace.

Přečtěte si následující popis APSS předplatná a pokyny o tom, jak vytvořit odběr [přidat Azure Partner Shared Services](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Předplatná CSP

Cloudová předplatná poskytovatele služeb (CSP) jsou upřednostňované volbou pro registraci při prodejce CSP nebo koncového zákazníka funguje Azure Stack.

## <a name="register-azure-stack"></a>Registrace Azure Stack

Registraci Azure Stack v Azure použijte APSS předplatné vytvořili uvedenými informacemi v předchozí části. Další informace najdete v tématu [registrace Azure Stack s vaším předplatným Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Přidání koncového zákazníka

Ke konfiguraci Azure Stack, takže pokud nového tenanta používá prostředky jejich používání se ohlásí na svoje předplatné Cloud Service Provider (CSP), najdete v článku [přidání tenanta pro využití a fakturaci ke službě Azure Stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Poplatek za správné předplatné

Azure Stack používá funkci registrace. Registrace je objekt uložený v Azure. Registrace objektu dokumenty, které předplatných Azure pomocí za dané služby Azure Stack. Tato část se zabývá významu registrace.

Pomocí registrace Azure Stack můžete:
 - Azure Commerce předávat data o využití služby Azure Stack a vyúčtování předplatného Azure.
 - Zasílání zpráv o využití jednotlivých zákazníků v jiném předplatném s víceklientského nasazení Azure Stack. Víceklientské architektury umožňuje službě Azure Stack pro podporu různých organizací na stejnou instanci služby Azure Stack.

Pro každý Azure Stack je jedno výchozí předplatné a mnoho tenantů předplatných. Výchozí předplatné je předplatné Azure, které se účtují, pokud není k dispozici předplatné specifickým pro tenanta. Musí být první odběrům registrovaným. Předplatné pro použití více tenantů reporting fungovat, musí být CSP nebo APSS předplatného.

Registrace se pak aktualizuje s předplatným Azure pro každého klienta, na který budete používat Azure Stack. Předplatná tenanta musí být typu CSP a musí zahrnout do partnera, který vlastní výchozí předplatné. Jinými slovy nelze zaregistrovat zákazníci někoho jiného.

Když Azure Stack předává informace o využití na global Azure, služba v Azure consults registrace a použití jednotlivých tenantů se mapuje na příslušnou tenanta předplatného. Pokud tenanta není zaregistrovaný, že využití přejde na výchozí předplatné pro instanci služby Azure Stack, ze které pochází.

Protože tenanta předplatná jsou předplatná CSP, faktuře bude zaslána CSP partner a informace o použití není viditelný pro koncové zákazníky.

## <a name="next-steps"></a>Další postup

 - Další informace o programu CSP najdete v tématu [programu Cloud Solution Provider](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
 - Další informace o tom, jak načíst informace o využití prostředků ze služby Azure Stack, najdete v článku [využití a fakturace ve službě Azure Stack](azure-stack-billing-and-chargeback.md).
