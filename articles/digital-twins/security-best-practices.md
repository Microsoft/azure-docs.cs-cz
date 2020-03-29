---
title: Seznamte se s doporučenými postupy zabezpečení – Azure Digital Twins | Dokumenty společnosti Microsoft
description: Přečtěte si o doporučených postupech zabezpečení pro Azure Digital Twins a Internet věcí.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122555"
---
# <a name="azure-digital-twins-security-best-practices"></a>Osvědčené postupy zabezpečení Azure Digital Twins

Zabezpečení Azure Digital Twins umožňuje přesný přístup ke konkrétním prostředkům a akcím v grafu IoT. Činí tak prostřednictvím podrobné role a správy oprávnění s názvem [řízení přístupu na základě rolí](./security-role-based-access-control.md).

Azure Digital Twins také používá další funkce zabezpečení, které jsou k dispozici v Azure IoT, včetně Azure Active Directory (Azure AD). Z tohoto důvodu konfigurace a zabezpečení aplikací postavených na Azure Digital Twins zahrnuje použití mnoha stejných [postupů zabezpečení Azure IoT,](../iot-fundamentals/iot-security-best-practices.md) které jsou aktuálně doporučeny.

Tento článek shrnuje klíčové osvědčené postupy, které je třeba dodržovat.

> [!IMPORTANT]
> Chcete-li zajistit maximální zabezpečení prostoru IoT, zkontrolujte další prostředky zabezpečení. Nezapomeňte zahrnout dodavatele zařízení.

> [!TIP]
> Pomocí [Centra zabezpečení Azure pro IoT](https://docs.microsoft.com/azure/asc-for-iot/) můžete odhalit bezpečnostní hrozby a chyby zabezpečení IoT.

## <a name="iot-security-best-practices"></a>Osvědčené postupy zabezpečení IoT

Některé klíčové postupy pro bezpečné zabezpečení zařízení IoT zahrnují:

> [!div class="checklist"]
> * Zabezpečte každé zařízení, které je připojené k vašemu ioT prostoru, způsobem odolným proti manipulaci.
> * Omezte roli každého zařízení, senzoru a osoby v rámci vašeho prostoru IoT. Pokud je ohrožena, efekt je minimalizován.
> * Zvažte potenciální použití filtrování IP adres zařízení a omezení portů.
> * Omezte vstupně-výkon a šířku pásma zařízení a zvyšte tak výkon. Omezení rychlosti může zlepšit zabezpečení tím, že zabrání útokům odmítnutí služby.
> * Udržujte firmware zařízení, operační systém a software aktuální.
> * Pravidelně auditujte a kontrolujte osvědčené postupy zabezpečení zařízení, softwaru, sítě a brány, protože se neustále zlepšují a vyvíjejí.
> * Používejte důvěryhodné, certifikované a kompatibilní bezpečnostní systémy, software a zařízení. Můžete například zkontrolovat [nabídky dodržování předpisů](https://azure.microsoft.com/overview/trusted-cloud/compliance/) pro Azure Cloud.

Některé klíčové postupy pro bezpečné zabezpečení prostoru IoT zahrnují:

> [!div class="checklist"]
> * Šifrujte uložená, uložená nebo trvalá data.
> * Vyžadovat, aby hesla nebo klíče byly pravidelně měněny nebo aktualizovány.
> * Pečlivě omezte přístup a oprávnění podle role. Přečtěte si část [Doporučené postupy řízení přístupu na základě rolí](#role-based-access-control-best-practices) níže.
> * Zvažte rozdělenou síťovou topologii, aby zařízení v každé síti byla izolována od ostatních.
> * Používejte výkonné šifrování. Vyžadovat dlouhá hesla, používat zabezpečené protokoly a [vícefaktorové ověřování](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

[Monitor](./how-to-configure-monitoring.md) IoT prostředky sledovat odlehlé hodnoty, hrozby nebo parametry prostředků, které spadají mimo rozsah obvyklé operace. Azure Analytics použijte pro správu monitorování.

> [!IMPORTANT]
> Přečtěte si osvědčené postupy zabezpečení Azure [IoT](../iot-fundamentals/iot-security-best-practices.md) a začněte komplexní strategii zabezpečení IoT.

> [!NOTE]
> Další informace o zpracování a monitorování událostí načtete [si informace o událostech a zprávách pomocí Azure Digital Twins](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Doporučené postupy azure služby Active Directory

Azure Digital Twins používá [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) k ověřování uživatelů a ochraně aplikací. Azure Active Directory podporuje ověřování pro celou řadu moderních architektur. Všechny jsou založeny na standardních protokolech, jako je OAuth 2.0 nebo OpenID Connect. Mezi některé klíčové postupy zabezpečení vašeho místa IoT pro Azure Active Directory patří:

> [!div class="checklist"]
> * Uložte tajné klíče a klíče aplikace Azure Active Directory na zabezpečeném místě, jako je [například Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * K ověření použijte certifikát vydaný [důvěryhodnou certifikační autoritou,](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) nikoli tajné kódy aplikací.
> * Omezte rozsah přístupu OAuth 2.0 pro token.
> * Ověřte, jak dlouho je token platný a zda token zůstává platný.
> * Nastavte vhodné délky času, pro který jsou tokeny platné. Aktualizovat tokeny s ukončenou platností.
> * Odeberte nepoužívané **identifikátory URI přesměrování** a oprávnění podle [doporučených postupů řízení přístupu na základě rolí](#role-based-access-control-best-practices).

## <a name="role-based-access-control-best-practices"></a>Osvědčené postupy řízení přístupu na základě rolí

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Další kroky

* Další informace o doporučených postupech Azure IoT najdete v [doporučených postupech zabezpečení IoT](../iot-fundamentals/iot-security-best-practices.md).

* Další informace o řízení přístupu na základě rolí najdete v textu [řízení přístupu na základě rolí](./security-role-based-access-control.md).

* Další informace o ověřování najdete v článek [Ověření pomocí souborů API](./security-authenticating-apis.md).
