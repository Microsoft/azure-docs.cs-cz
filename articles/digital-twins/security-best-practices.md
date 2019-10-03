---
title: Porozumění osvědčeným postupům zabezpečení – digitální vlákna Azure | Microsoft Docs
description: Seznamte se s osvědčenými postupy zabezpečení pro digitální vlákna Azure a Internet věcí.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: v-adgera
ms.openlocfilehash: e98f8a4becad41d27d89de16f598b6fa531ad5e3
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827641"
---
# <a name="security-best-practices"></a>Osvědčené postupy zabezpečení

Zabezpečení digitálních vláken Azure umožňuje přesný přístup ke konkrétním prostředkům a akcím v grafu IoT. K tomu připadá prostřednictvím podrobné správy rolí a oprávnění označovaného jako [řízení přístupu na základě role](./security-role-based-access-control.md).

Funkce digitálních vláken Azure používá i další funkce zabezpečení, které jsou k dispozici v Azure IoT, včetně Azure Active Directory (Azure AD). Z tohoto důvodu se při konfiguraci a zabezpečování aplikací založených na digitálních pročinnostech Azure používá celá řada stejných [postupů zabezpečení Azure IoT](../iot-fundamentals/iot-security-best-practices.md) , které se v tuto chvíli doporučují.

Tento článek shrnuje klíčové osvědčené postupy, které je potřeba sledovat.

> [!IMPORTANT]
> Pokud chcete zajistit maximální zabezpečení prostoru IoT, Projděte si další zdroje zabezpečení. Ujistěte se, že jste zahrnuli vaše dodavatele zařízení.

> [!TIP]
> Použijte [Azure Security Center pro IoT](https://docs.microsoft.com/azure/asc-for-iot/) , které vám pomůžou odhalit hrozby zabezpečení IoT a ohrožení zabezpečení.

## <a name="iot-security-best-practices"></a>Osvědčené postupy zabezpečení IoT

Mezi klíčové postupy pro bezpečné zabezpečení vašich zařízení IoT patří:

> [!div class="checklist"]
> * Zabezpečte všechna zařízení, která jsou připojená k prostoru IoT v případě úmyslného ověření.
> * Omezte role všech zařízení, senzorů a osob v rámci vašeho prostoru IoT. Pokud dojde k ohrožení zabezpečení, efekt se minimalizuje.
> * Vezměte v úvahu potenciální využívání filtrování IP adres a omezení portů zařízení.
> * Omezí vstupně-výstupní operace a šířku pásma zařízení pro zlepšení výkonu. Omezení rychlosti může zlepšit zabezpečení tím, že se zabrání útokům DOS (Denial-of-Service).
> * Udržujte firmware zařízení v aktuálním stavu.
> * Pravidelně auditujte a kontrolujte osvědčené postupy zabezpečení zařízení, sítě a brány, protože se dále zlepšují a vyvíjí.

Mezi klíčové postupy pro bezpečné zabezpečení prostoru IoT patří:

> [!div class="checklist"]
> * Šifrování uložených, uložených nebo trvalých dat.
> * Vyžadovat, aby hesla nebo klíče byly pravidelně měněny nebo aktualizovány.
> * Pozorně omezte přístup a oprávnění podle role. Další informace najdete v části [osvědčené postupy řízení přístupu založené na rolích](#role-based-access-control-best-practices) níže.
> * Vezměte v úvahu dělenou topologii sítě, aby byla zařízení v každé síti izolovaná od ostatních.
> * Používejte výkonné šifrování. Vyžadovat dlouhá hesla a používat zabezpečené protokoly a dvojúrovňové ověřování.

[Monitorování](./how-to-configure-monitoring.md) Prostředky IoT, které můžete sledovat pro odlehlé hodnoty, hrozby nebo parametry prostředků, které spadají mimo rozsah běžné operace. Využijte Azure Analytics ke správě monitorování.

> [!NOTE]
> Další informace o zpracování a monitorování událostí najdete v tématu [události a zprávy směrování pomocí digitálních vláken Azure](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory osvědčené postupy

Digitální vlákna Azure používá Azure Active Directory k ověřování uživatelů a ochraně aplikací. Azure Active Directory podporuje ověřování pro nejrůznější moderní architektury. Jsou založené na standardních protokolech, jako je OAuth 2,0 nebo OpenID Connect. Mezi nejdůležitější postupy zabezpečení prostoru IoT pro Azure Active Directory patří:

> [!div class="checklist"]
> * Ukládejte Azure Active Directory tajných klíčů a klíčů aplikace v zabezpečeném umístění, jako je například [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Použijte certifikát vydaný důvěryhodnou [certifikační autoritou](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) namísto ověřování tajných klíčů aplikací.
> * Omezení rozsahu OAuth 2,0 přístupu pro token.
> * Ověřte, že doba platnosti tokenu je platná a zda token zůstává platný.
> * Nastavte odpovídající délkové doby, pro které jsou tokeny platné.
> * Aktualizujte tokeny, jejichž platnost vypršela.

## <a name="role-based-access-control-best-practices"></a>Osvědčené postupy řízení přístupu založené na rolích

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Další kroky

* Další informace o osvědčených postupech pro Azure IoT najdete v článku [osvědčené postupy zabezpečení IoT](../iot-fundamentals/iot-security-best-practices.md).

* Další informace o řízení přístupu na základě rolí najdete v tématu [řízení přístupu na základě role](./security-role-based-access-control.md).

* Pokud se chcete dozvědět o ověřování, přečtěte si téma [ověřování pomocí rozhraní API](./security-authenticating-apis.md).