---
title: Funkce pro žádosti o zákaznických datech pro zařízení Azure DPS
description: Pro zařízení spravovaná ve službě Azure Device Provisioning (DPS), která jsou osobní, se v tomto článku zobrazují správci, jak exportovat nebo odstranit osobní údaje.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 1dcf1b9f62f94b8f75ef2fe77f3e237a387c53eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "73890657"
---
# <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí pro žádosti o data zákazníků

Azure IoT Hub Device Provisioning Service je cloudová služba založená na REST API, která je zacílená na podnikové zákazníky, která umožňuje bezproblémové automatizované zřizování zařízení do Azure IoT Hub se zabezpečením, které začíná na zařízení a končí v cloudu.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Jednotlivým zařízením je přiřazeno ID registrace a ID zařízení správce klienta. Data z a o těchto zařízeních jsou založená na těchto ID. Společnost Microsoft nespravuje žádné informace a nemá přístup k datům, která by jim umožnila korelaci těchto zařízení s jednotlivcem.

Mnohé ze zařízení spravovaných ve službě Device Provisioning nejsou osobní zařízení, například Kancelářský termostat nebo robot pro továrny. Zákazníci si ale můžou všimnout, že některá zařízení budou identifikovatelné osobně a na základě jejich uvážení můžou udržovat své vlastní prostředky nebo metody sledování inventáře, které propojují zařízení s jednotlivci. Služba Device Provisioning spravuje a ukládá všechna data, která jsou přidružená k zařízením, jako by šlo o osobní údaje.

Správci klientů můžou pomocí Azure Portal nebo rozhraní REST API služby vyplnit požadavky na informace exportem nebo odstraněním dat přidružených k ID zařízení nebo ID registrace.

> [!NOTE]
> Zařízení, která byla zřízená v Azure IoT Hub prostřednictvím služby Device Provisioning, mají další data uložená ve službě Azure IoT Hub. Úplný požadavek na dané zařízení dokončíte v [referenční dokumentaci k Azure IoT Hub](../iot-hub/iot-hub-customer-data-requests.md) .

## <a name="deleting-customer-data"></a>Odstraňují se zákaznická data

Služba Device Provisioning ukládá registrace a registrační záznamy. Registrace obsahují informace o zařízeních, která jsou povolená k zřizování, a registrační záznamy ukazují, která zařízení už prošla procesem zřizování.

Správci klientů mohou odebrat registrace z Azure Portal a tím dojde také k odebrání všech přidružených registračních záznamů.

Další informace najdete v tématu [Správa registrace zařízení](how-to-manage-enrollments.md).

Je také možné provádět operace odstranění pro registrace a registrační záznamy pomocí rozhraní REST API:

* Pokud chcete odstranit informace o registraci pro jednotlivá zařízení, můžete použít [registraci zařízení – odstranit](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Pokud chcete odstranit informace o registraci pro skupinu zařízení, můžete použít [skupinu registrace zařízení-odstranit](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Pokud chcete odstranit informace o zařízeních, která byla zřízena, můžete použít stav [registrace – odstranit stav registrace](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Export zákaznických dat

Služba Device Provisioning ukládá registrace a registrační záznamy. Registrace obsahují informace o zařízeních, která jsou povolená k zřizování, a registrační záznamy ukazují, která zařízení už prošla procesem zřizování.

Správci klientů mohou zobrazit registrace a záznamy o registraci prostřednictvím Azure Portal a exportovat je pomocí funkce kopírování a vložení.

Další informace o tom, jak spravovat registrace, najdete v tématu [Správa registrace zařízení](how-to-manage-enrollments.md).

Je také možné provádět operace exportu pro registrace a registrační záznamy pomocí rozhraní REST API:

* Pokud chcete exportovat informace o registraci pro jednotlivá zařízení, můžete použít [registraci zařízení – získat](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Pokud chcete exportovat informace o registraci pro skupinu zařízení, můžete použít [skupinu registrace zařízení – získat](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Pokud chcete exportovat informace o zařízeních, která už jsou zřízená, můžete použít stav [registrace – získat stav registrace](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> Když použijete podnikové služby společnosti Microsoft, vygeneruje společnost Microsoft nějaké informace, které se nazývají systémem generované protokoly. Některé protokoly generované službou Device Provisioning nejsou pro správce klientů dostupné nebo je můžou exportovat. Tyto protokoly představují věcné akce prováděné v rámci služby a diagnostická data související s jednotlivými zařízeními.

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Úplná dokumentace k rozhraním API služby Device Provisioning se nachází na adrese [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps) .

Funkce pro [žádosti o data zákazníků](../iot-hub/iot-hub-customer-data-requests.md)v Azure IoT Hub.