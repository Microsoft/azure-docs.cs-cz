---
title: Funkce požadavek dat zákazníka
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: d6355926c8fac62b01c36d28265842b1233ce213
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629316"
---
# <a name="summary-of-customer-data-request-features"></a>Souhrn funkcí požadavek data zákazníků

Azure IoT Hub zařízení zřizovací služby je založené na REST API Cloudová služba zaměřený na podnikových zákazníků, která umožňuje bezproblémový, automatizované nula touch zřizování zařízení Azure IoT Hub s zabezpečení, který začíná na zařízení a končí cloudu.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Jednotlivá zařízení přiřadil registrace ID a ID zařízení správce klienta. Data z a o těchto zařízení je založena na těchto ID. Společnost Microsoft udržuje žádné informace a nemá přístup k datům, která by umožnilo korelace těchto zařízení až k jednotlivcům.

Mnoho zařízení spravovaných v zařízení zřizování služby nejsou osobní zařízení, například služby office Termostat nebo vytváření robot. Zákazníci, však zvažte některá zařízení jako osobní identifikovatelné a podle svého uvážení mohou zachovat své vlastní prostředek nebo inventáře sledování metody, které tie zařízení jednotlivcům. Služba zřizování zařízení spravuje a ukládá všechna data přidružená k zařízení, jako by šlo osobní data.

Správci klienta můžete použít portál Azure nebo rozhraní API služby REST pro splnění požadavků na informace tak, že vyexportujete nebo odstranění dat spojených s ID zařízení nebo ID registrace.

> [!NOTE]
> Zařízení, které se zřizují v Azure IoT Hub prostřednictvím služby zřizování zařízení mají další data uložená ve službě Azure IoT Hub. Najdete v článku [Azure IoT Hub referenční dokumentaci k nástroji](../iot-hub/iot-hub-customer-data-requests.md) aby bylo možné dokončit žádost o úplné pro dané zařízení.

## <a name="deleting-customer-data"></a>Odstraňování dat zákazníka

Služba zřizování zařízení ukládá registrace a záznamy registrace. Registrace obsahují informace o zařízení, která jsou povolena zřídit a zobrazit záznamy registrace, které již šly zařízení prostřednictvím procesu zřizování.

Správci klientů může odebrat registrace z portálu Azure a odebere všechny přidružené registrace záznamy.

Další informace najdete v tématu [jak spravovat registrace zařízení](how-to-manage-enrollments.md).

Také je možné provádět operace delete pro registraci a registrace záznamů pomocí rozhraní REST API:

* Chcete-li odstranit informace o registraci pro jedno zařízení, můžete použít [registrace zařízení - odstranění](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/delete).
* Chcete-li odstranit informace o registraci pro skupinu zařízení, můžete použít [skupiny registrace zařízení - odstranění](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/delete).
* Chcete-li odstranit informace o zařízení, které byly zřízeny, můžete použít [stav registrace – stav registrace odstranit](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/deleteregistrationstate).

## <a name="exporting-customer-data"></a>Export dat zákazníka

Služba zřizování zařízení ukládá registrace a záznamy registrace. Registrace obsahují informace o zařízení, která jsou povolena zřídit a zobrazit záznamy registrace, které již šly zařízení prostřednictvím procesu zřizování.

Správci klienta můžete zobrazit registrace a záznamy registrace prostřednictvím portálu Azure a exportovat je pomocí kopírování a vkládání.

Další informace o tom, jak spravovat registrace najdete v tématu [jak spravovat registrace zařízení](how-to-manage-enrollments.md).

Také je možné provádět operace exportu pro registraci a registrace záznamů pomocí rozhraní REST API:

* Pokud chcete exportovat informace o registraci pro jedno zařízení, můžete použít [registrace zařízení - Get](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/get).
* Pokud chcete exportovat informace o registraci pro skupinu zařízení, můžete použít [skupiny registrace zařízení - Get](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/get).
* Pokud chcete exportovat informace o zařízení, které již byly zřízeny, můžete použít [stav registrace – stav registrace Get](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/getregistrationstate).

> [!NOTE]
> Při použití služby enterprise společnosti Microsoft generuje některé informace, které jsou známé jako protokoly generované systémem. Některé služby zřizování zařízení generované systémem protokoly nejsou přístupné nebo exportovatelný Správci klienta. Tyto protokoly tvoří konkrétní akce prováděné v rámci služby a diagnostická data související s jednotlivých zařízení.

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Úplnou dokumentaci k rozhraní API služby pro zřizování zařízení se nachází v [ https://docs.microsoft.com/rest/api/iot-dps ](https://docs.microsoft.com/rest/api/iot-dps).

Azure IoT Hub [zákaznická data žádosti o funkce](../iot-hub/iot-hub-customer-data-requests.md).