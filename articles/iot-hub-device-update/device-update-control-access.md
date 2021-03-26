---
title: Pochopení aktualizace zařízení pro IoT Hub ověřování a autorizaci | Microsoft Docs
description: Seznamte se s tím, jak aktualizace zařízení pro IoT Hub používá službu Azure RBAC k poskytování ověřování a autorizace pro uživatele a rozhraní API služeb.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ca55b1df347b47a6eb82557658d59a3de666b703
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558393"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Řízení přístupu na základě role (RBAC) v Azure a aktualizace zařízení

Aktualizace zařízení pomocí služby Azure RBAC zajišťuje ověřování a autorizaci pro uživatele a rozhraní API služeb.

## <a name="configure-access-control-roles"></a>Konfigurace rolí řízení přístupu

Aby mohli ostatní uživatelé a aplikace mít přístup k aktualizaci zařízení, musí být uživatelům nebo aplikacím udělen přístup k tomuto prostředku. Tady jsou role, které podporuje aktualizace zařízení.

|   Název role   | Description  |
| :--------- | :---- |
|  Správce aktualizace zařízení | Má přístup ke všem prostředkům aktualizace zařízení.  |
|  Čtenář aktualizace zařízení| Může zobrazit všechny aktualizace a nasazení. |
|  Správce obsahu aktualizace zařízení | Může zobrazovat, importovat a odstraňovat aktualizace.  |
|  Čtenář obsahu aktualizace zařízení | Může zobrazit aktualizace.  |
|  Správce nasazení aktualizací zařízení | Může spravovat nasazení aktualizací do zařízení.|
|  Čtečka nasazení aktualizací zařízení| Může zobrazit nasazení aktualizací do zařízení. |

K poskytnutí správné úrovně přístupu lze použít kombinaci rolí. Vývojář může například importovat a spravovat aktualizace pomocí role správce obsahu aktualizace zařízení, ale potřebuje k zobrazení průběhu aktualizace roli Čtenář nasazení aktualizací zařízení. V opačném případě může operátor řešení pomocí role čtenář aktualizace zařízení zobrazit všechny aktualizace, ale potřebuje použít roli Správce nasazení aktualizací zařízení k nasazení konkrétní aktualizace do zařízení.


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>Ověřování pro aktualizace zařízení rozhraní REST API pro publikování a správu

K publikování a správě obsahu prostřednictvím rozhraní API služby používá aktualizace zařízení taky Azure AD k ověřování. Chcete-li začít, je nutné vytvořit a nakonfigurovat klientskou aplikaci.

### <a name="create-client-azure-ad-app"></a>Vytvořit Aplikace Azure AD klienta

K integraci aplikace nebo služby se službou Azure AD [nejdřív zaregistrujete](../active-directory/develop/quickstart-register-app.md) aplikaci s využitím Azure AD. Instalace klientské aplikace se liší v závislosti na použitém toku autorizace.  Následující konfigurace uvádí pokyny k použití rozhraní REST API pro aktualizace zařízení.

* Nastavte ověřování klientů: identifikátory URI pro přesměrování pro nativní nebo webový klient.
* Nastavení oprávnění rozhraní API – aktualizace zařízení pro IoT Hub zpřístupňuje:
  * Delegovaná oprávnění: ' user_impersonation '
  * **Volitelné**, udělení souhlasu správce

[Další kroky: vytvoření prostředků aktualizace zařízení a konfigurace rolí řízení přístupu](./create-device-update-account.md)