---
title: Funkce požadavků na zákaznická data pro zařízení Azure DPS
description: Pro zařízení spravovaná ve službě Azure Device Provisioning Service (DPS), která jsou osobní, tento článek ukazuje správcům, jak exportovat nebo odstranit osobní data.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 1dcf1b9f62f94b8f75ef2fe77f3e237a387c53eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890657"
---
# <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí požadavků na zákaznická data

Služba Azure IoT Hub Device Provisioning Service je cloudová služba založená na rozhraní REST API zaměřená na podnikové zákazníky, která umožňuje bezproblémové a automatizované zřizování zařízení bez dotykového ovládání do služby Azure IoT Hub se zabezpečením, které začíná na zařízení a končí cloudem.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Jednotlivým zařízením je přiřazeno ID registrace a ID zařízení správcem klienta. Data z těchto zařízení a o nich jsou založena na těchto ID. Společnost Microsoft neudržuje žádné informace a nemá přístup k datům, která by umožnila korelaci těchto zařízení s jednotlivcem.

Mnoho zařízení spravovaných ve službě Device Provisioning Service nejsou osobní zařízení, například kancelářský termostat nebo tovární robot. Zákazníci však mohou považovat některá zařízení za osobně identifikovatelná a podle svého uvážení mohou udržovat své vlastní metody sledování aktiv nebo inventáře, které spojují zařízení s jednotlivci. Služba Device Provisioning spravuje a ukládá všechna data spojená se zařízeními, jako by se jednalo o osobní údaje.

Správci tenanta můžou použít portál Azure nebo rozhraní REST služby ke splnění požadavků na informace exportem nebo odstraněním dat přidružených k ID zařízení nebo ID registrace.

> [!NOTE]
> Zařízení, která byla zřízena v azure iot hubu prostřednictvím služby zřizování zařízení mají další data uložená ve službě Azure IoT Hub. Podívejte se na [referenční dokumentaci k centru Azure IoT Hub,](../iot-hub/iot-hub-customer-data-requests.md) abyste mohli dokončit úplný požadavek na dané zařízení.

## <a name="deleting-customer-data"></a>Odstranění dat zákazníků

Služba device provisioning ukládá registrace a záznamy o registraci. Registrace obsahují informace o zařízeních, která mohou být zřízena, a záznamy registrace ukazují, která zařízení již prošla procesem zřizování.

Správci tenanta mohou odebrat registrace z portálu Azure, což odebere také všechny přidružené registrační záznamy.

Další informace najdete v tématu [Správa registrací zařízení](how-to-manage-enrollments.md).

Je také možné provádět operace odstranění pro registrace a registrace záznamů pomocí rest API:

* Chcete-li odstranit informace o registraci pro jedno zařízení, můžete použít [nástroj Device Enrollment - Delete](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Chcete-li odstranit informace o registraci pro skupinu zařízení, můžete použít [skupinu registrace zařízení - Odstranit](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Chcete-li odstranit informace o zařízeních, která byla zřízena, můžete použít [stav registrace - Odstranit stav registrace](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Export dat zákazníků

Služba device provisioning ukládá registrace a záznamy o registraci. Registrace obsahují informace o zařízeních, která mohou být zřízena, a záznamy registrace ukazují, která zařízení již prošla procesem zřizování.

Správci tenantů můžou zobrazit registrace a záznamy registrace na webu Azure portal a exportovat je pomocí kopírování a vkládání.

Další informace o tom, jak spravovat registrace, najdete v tématu [Správa registrací zařízení](how-to-manage-enrollments.md).

Je také možné provádět operace exportu pro zápisy a registrace záznamů pomocí rest API:

* Chcete-li exportovat informace o registraci pro jedno zařízení, můžete použít [registraci zařízení – získat](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Chcete-li exportovat informace o registraci pro skupinu zařízení, můžete použít [skupinu registrace zařízení - získat](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Chcete-li exportovat informace o zařízeních, která již byla zřízena, můžete použít [stav registrace – získat stav registrace](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> Při použití služeb společnosti Microsoft enterprise, Microsoft generuje některé informace, označované jako protokoly generované systémem. Některé protokoly generované systémem device provisioning service nejsou přístupné nebo exportovatelné správci klienta. Tyto protokoly představují věcné úkony prováděné v rámci služby a diagnostické údaje týkající se jednotlivých zařízení.

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Úplná dokumentace pro rozhraní API služby zřizování zařízení je umístěna na adrese [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps).

[Funkce požadavků na zákaznická data služby](../iot-hub/iot-hub-customer-data-requests.md)Azure IoT Hub .