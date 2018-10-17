---
title: Principy Azure digitální dvojče osvědčené postupy zabezpečení | Dokumentace Microsoftu
description: Digitální dvojče osvědčené postupy zabezpečení Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: adgera
ms.openlocfilehash: 28eb8b5dc0f75b5e031070803d35c8a1ceb1f000
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364333"
---
# <a name="security-best-practices"></a>Osvědčené postupy zabezpečení

Zabezpečení Azure digitální dvojče umožňuje přesné přístup ke konkrétním prostředkům a akcím v grafu IoT. Dělá to pomocí granulární role a oprávnění správy nazývá řízení přístupu na základě rolí.

Azure digitální dvojče využívá také další funkce zabezpečení v Azure IoT, které patří Azure Active Directory. Z tohoto důvodu konfigurace aplikace pro digitální dvojče Azure zahrnuje mnoho stejných pomocí [postupy zabezpečení Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/) aktuálně doporučené.

Tento článek shrnuje klíčové doporučené postupy.

> [!IMPORTANT]
> Projděte si další bezpečnostní prostředky (včetně zařízení dodavatele) zajistit maximální zabezpečení pro váš prostor IoT.

## <a name="iot-security-best-practices"></a>Osvědčené postupy zabezpečení IoT

Některé klíče postupy bezpečně zabezpečit zařízení IoT zahrnují:

> [!div class="checklist"]
> * Zabezpečení všech zařízení, která je připojen k IoT prostoru způsobem odolný proti manipulaci.
> * Omezte role jednotlivých zařízení, senzorů a osoby v rámci adresního prostoru vaše IoT. Pokud dojde k narušení, je minimalizovat dopad.
> * Potenciální použití filtrování adresu IP zařízení.
> * Omezit šířku pásma vstupně-výstupní operace a zařízení pro zlepšení výkonu. Omezení četnosti můžete zlepšit zabezpečení zabráněním útoky s cílem odepření služeb.

Některé klíče postupy k zabezpečení bezpečně prostorem IoT patří:

> [!div class="checklist"]
> * Šifrovat data uložená, uložené nebo trvalé.
> * Vyžadovat hesla nebo klíče pravidelně změnit nebo aktualizovat.
> * Důkladným omezením přístupu a oprávnění rolí (viz následující osvědčené postupy řízení přístupu na základě Role).
> * Pomocí výkonných šifrování. To znamená, že vyžadování dlouhá hesla, pomocí protokolů zabezpečení a dvojúrovňového ověřování.

Monitorování prostředků IoT a sledujte odlehlé hodnoty, hrozby nebo parametry prostředků, které spadají mimo rozsah obvyklé operace se spravuje přes Azure Analytics.

> [!NOTE]
> Další informace o události zpracování a monitorování, najdete v článku na [události > směrování](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Osvědčené postupy pro Azure Active Directory

Azure digitální dvojče používá Azure Active Directory k ověřování uživatelů a ochraně aplikací. Azure Active Directory podporuje ověřování pro širokou škálu moderní architektury, všechny z nich založené na standardních oborových protokolů, jako jsou OAuth 2.0 nebo OpenID Connect. Pár klíčů postupy zabezpečení IoT prostoru pro Azure Active Directory jsou následující:

> [!div class="checklist"]
> * Store klíčů nebo tajných kódů aplikace Azure Active Directory v zabezpečeném umístění, jako [služby Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Použít certifikát vydaný důvěryhodný [certifikační autority](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started) místo tajných kódů aplikace k ověření.
> * Omezit obor přístupu pro token OAuth 2.0.
> * Zkontrolujte dobu, kterou je token platný a zda zůstane platný token.
> * Nastavte odpovídající délky času, které jsou platné pro tokeny.
> * Aktualizujte vypršení platnosti tokenů.

## <a name="role-based-access-control-best-practices"></a>Osvědčené postupy pro řízení přístupu na základě role

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Další postup

Další informace o osvědčených postupech pro Azure IoT, [osvědčené postupy zabezpečení IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Další informace o řízení přístupu na základě rolí najdete v článku [řízení přístupu na základě Role](./security-role-based-access-control.md).

Ověřování, přečtěte si [ověřování pomocí rozhraní API](./security-authenticating-apis.md).
