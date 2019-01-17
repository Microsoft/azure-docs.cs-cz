---
title: Pochopení osvědčených postupů zabezpečení digitální dvojče Azure | Dokumentace Microsoftu
description: Digitální dvojče osvědčené postupy zabezpečení Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: adgera
ms.openlocfilehash: 16bb148a0b3a424c9ba3aaae422f423ebd40793b
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358809"
---
# <a name="security-best-practices"></a>Osvědčené postupy zabezpečení

Zabezpečení Azure digitální dvojče umožňuje přesné přístup ke konkrétním prostředkům a akcím v grafu IoT. Provádí se prostřednictvím detailní role a oprávnění správy volá [řízení přístupu na základě rolí](./security-role-based-access-control.md).

Azure digitální dvojče používá také další funkce zabezpečení, které se nacházejí ve službě Azure IoT, včetně služby Azure Active Directory (Azure AD). Z tohoto důvodu, konfiguraci a zabezpečení aplikace založené na Azure digitální dvojče využívá mnoho stejných [postupy zabezpečení Azure IoT](../iot-fundamentals/iot-security-best-practices.md) aktuálně doporučené.

Tento článek shrnuje klíčové doporučené postupy.

> [!IMPORTANT]
> K zajištění maximální zabezpečení pro váš prostor IoT, zkontrolujte další bezpečnostní prostředky. Ujistěte se, že zahrnete dodavatele vašeho zařízení.

## <a name="iot-security-best-practices"></a>Osvědčené postupy zabezpečení IoT

Některé klíče postupy bezpečně zabezpečit zařízení IoT zahrnují:

> [!div class="checklist"]
> * Zabezpečení všech zařízení, která je připojen k IoT prostoru způsobem odolný proti manipulaci.
> * Omezte role jednotlivých zařízení, senzorů a osoby v rámci adresního prostoru vaše IoT. Pokud dojde k narušení, je minimalizovat vliv.
> * Vezměte v úvahu potenciální využívání zařízení IP adres, filtrování a portu omezení.
> * Omezit šířku pásma vstupně-výstupní operace a zařízení pro zlepšení výkonu. Omezení četnosti můžete zlepšit zabezpečení zabráněním útoky s cílem odepření služeb.
> * Zajištění aktuálnosti firmwaru zařízení.

Některé klíče postupy k zabezpečení bezpečně prostorem IoT patří:

> [!div class="checklist"]
> * Šifrovat data uložená, uložené nebo trvalé.
> * Vyžadovat hesla nebo klíče pravidelně změnit nebo aktualizovat.
> * Důkladným omezením přístupu a oprávnění rolí. V části [osvědčené postupy pro řízení přístupu na základě Role](#rbac) níže.
> * Pomocí výkonných šifrování. Vyžadovat dlouhá hesla a používají zabezpečené protokoly a dvojúrovňového ověřování.

[Monitorování](./how-to-configure-monitoring.md) prostředky IoT a sledujte odlehlé hodnoty, hrozby nebo parametry prostředků, které spadají mimo rozsah běžné operace. Pomocí Azure Analytics pro monitorování správy.

> [!NOTE]
> Další informace o monitorování a zpracování událostí naleznete v tématu [směrování událostí a zpráv pomocí Azure digitální dvojče](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Osvědčené postupy pro Azure Active Directory

Azure digitální dvojče používá Azure Active Directory k ověřování uživatelů a ochraně aplikací. Azure Active Directory podporuje ověřování pro širokou škálu moderní architektury. Všechny založených na standardních oborových protokolů, jako jsou OAuth 2.0 nebo OpenID Connect. Pár klíčů postupy zabezpečení IoT prostoru pro Azure Active Directory jsou následující:

> [!div class="checklist"]
> * Store klíčů nebo tajných kódů aplikace Azure Active Directory v zabezpečeném umístění, jako například [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Použít certifikát vydaný důvěryhodný [certifikační autorita](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) místo tajných kódů aplikace k ověření.
> * Omezit obor přístupu pro token OAuth 2.0.
> * Zkontrolujte dobu, kterou je token platný a zda zůstane platný token.
> * Nastavte odpovídající délky času, které jsou platné pro tokeny.
> * Aktualizujte vypršení platnosti tokenů.

<div id="rbac"></div>

## <a name="role-based-access-control-best-practices"></a>Osvědčené postupy pro řízení přístupu na základě role

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Další postup

* Další informace o osvědčených postupech pro Azure IoT, [osvědčené postupy zabezpečení IoT](../iot-fundamentals/iot-security-best-practices.md).

* Další informace o řízení přístupu na základě rolí najdete v článku [řízení přístupu na základě rolí](./security-role-based-access-control.md).

* Další informace o ověřování najdete v článku [ověřování pomocí rozhraní API](./security-authenticating-apis.md).
