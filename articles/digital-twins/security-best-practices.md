---
title: Pochopení osvědčených postupů zabezpečení digitální dvojče Azure | Dokumentace Microsoftu
description: Digitální dvojče osvědčené postupy zabezpečení Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 6ca01523744dbce15f8fdb3bbe2d5a9b44510f3f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959473"
---
# <a name="security-best-practices"></a>Osvědčené postupy zabezpečení

Zabezpečení Azure digitální dvojče umožňuje přesné přístup ke konkrétním prostředkům a akcím v grafu IoT. Dělá to pomocí granulární role a oprávnění správy nazývá řízení přístupu na základě rolí.

Azure digitální dvojče používá také další funkce zabezpečení, které jsou k dispozici s využitím Azure IoT, které zahrnuje službu Azure Active Directory (Azure AD). Z tohoto důvodu konfigurace aplikace pro digitální dvojče Azure zahrnuje mnoho stejných pomocí [postupy zabezpečení Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/) aktuálně doporučené.

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
> * Důkladným omezením přístupu a oprávnění rolí. V části "přístup na základě rolí k řízení osvědčené postupy."
> * Pomocí výkonných šifrování. Vyžadovat dlouhá hesla a používají zabezpečené protokoly a dvojúrovňového ověřování.

Monitorujte prostředky IoT a sledujte odlehlé hodnoty, hrozby nebo parametry prostředků, které spadají mimo rozsah běžné operace. Pomocí Azure Analytics ke správě, monitorování.

> [!NOTE]
> Další informace o monitorování a zpracování událostí naleznete v tématu [směrování událostí a zpráv pomocí Azure digitální dvojče](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Osvědčené postupy pro Azure Active Directory

Azure digitální dvojče používá Azure AD k ověřování uživatelů a ochraně aplikací. Azure AD podporuje ověřování pro širokou škálu moderní architektury. Všechny založených na standardních oborových protokolů, jako jsou OAuth 2.0 nebo OpenID Connect. Pár klíčů postupy zabezpečení IoT prostoru pro Azure AD patří:

> [!div class="checklist"]
> * Store klíčů nebo tajných kódů aplikace Azure AD v zabezpečeném umístění, jako například [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Použít certifikát vydaný důvěryhodný [certifikační autorita](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started) místo tajných kódů aplikace k ověření.
> * Omezit obor přístupu pro token OAuth 2.0.
> * Zkontrolujte dobu, kterou je token platný a zda zůstane platný token.
> * Nastavte odpovídající délky času, které jsou platné pro tokeny.
> * Aktualizujte vypršení platnosti tokenů.

## <a name="role-based-access-control-best-practices"></a>Osvědčené postupy pro řízení přístupu na základě role

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Další postup

Další informace o osvědčených postupech pro Azure IoT, [osvědčené postupy zabezpečení IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Další informace o řízení přístupu na základě rolí najdete v článku [řízení přístupu na základě rolí](./security-role-based-access-control.md).

Další informace o ověřování najdete v článku [ověřování pomocí rozhraní API](./security-authenticating-apis.md).
