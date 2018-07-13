---
title: Zákaznická data žádosti o funkce
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: d6355926c8fac62b01c36d28265842b1233ce213
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666934"
---
# <a name="summary-of-customer-data-request-features"></a>Souhrnné informace o zákaznická data žádosti o funkce

Azure IoT Hub Device Provisioning Service je založená na rozhraní REST API Cloudová služba určenou pro podnikové zákazníky, které umožňuje bezproblémové a automatizované plně automatizované zřizování zařízení k Azure IoT Hubu se zabezpečením, které začíná u zařízení a končí u cloudu.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Jednotlivá zařízení jsou přiřazené registrace ID a ID zařízení správcem tenanta. Data z a o těchto zařízeních je založená na tyto identifikátory. Společnost Microsoft udržuje žádné informace a nemá přístup k datům, která umožňují korelaci těchto zařízení, aby jednotlivec.

Mnoho zařízení spravovaná ve službě Device Provisioning nejsou osobní zařízení, třeba office Termostat nebo objekt pro vytváření robota. Zákazníci mohou, ale vezměte v úvahu některé zařízení, která budou identifikovatelné osobní údaje a podle vlastního uvážení může udržovat vlastní prostředek nebo metod, které jsou zařízení, která jednotlivcům sledování inventáře. Služby Device Provisioning Service spravuje a ukládá všechna data přidružená k zařízení, jako by šlo osobní údaje.

Správce tenanta můžete použít na webu Azure portal nebo rozhraní REST API služby ke splnění požadavků na informace tak, že vyexportujete nebo odstranění dat spojené s ID zařízení nebo ID registrace.

> [!NOTE]
> Zařízení, které se zřizují ve službě Azure IoT Hub pomocí služby Azure Device Provisioning mají další data uložená ve službě Azure IoT Hub. Zobrazit [referenční dokumentace služby Azure IoT Hub](../iot-hub/iot-hub-customer-data-requests.md) aby bylo možné dokončit žádost o úplné pro dané zařízení.

## <a name="deleting-customer-data"></a>Odstraňuje se zákaznická data

Služby Device Provisioning ukládá registrace a záznamy registrace. Registrace obsahují informace o zařízení, které můžou být zřízené a registraci záznamy ukazují, které zařízení už prošly přes během procesu zřizování.

Správci klientů mohou odebrat registrace z webu Azure portal a tato operace odebere všechny přidruženou registraci záznamy.

Další informace najdete v tématu [Správa registrace zařízení](how-to-manage-enrollments.md).

Je také možné provést operace odstranění registrace a registraci záznamů pomocí rozhraní REST API:

* Pokud chcete odstranit informace o registraci pro jedno zařízení, můžete použít [registrace zařízení – odstranit](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/delete).
* Pokud chcete odstranit informace o registraci pro skupinu zařízení, můžete použít [skupiny pro registraci zařízení – odstranit](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/delete).
* Můžete odstranit informace o zařízení, které se zřizují, můžete použít [stav registrace – odstranit stav registrace](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/deleteregistrationstate).

## <a name="exporting-customer-data"></a>Export zákaznických dat

Služby Device Provisioning ukládá registrace a záznamy registrace. Registrace obsahují informace o zařízení, které můžou být zřízené a registraci záznamy ukazují, které zařízení už prošly přes během procesu zřizování.

Správci klienta můžete zobrazit registrace a záznamy registrace na webu Azure portal a je exportovat pomocí zkopírovat a vložit.

Další informace o tom, jak spravovat registrace najdete v tématu [Správa registrace zařízení](how-to-manage-enrollments.md).

Je také možné provádět operace export pro registrace a registraci záznamů pomocí rozhraní REST API:

* Pokud chcete exportovat informace o registraci pro jedno zařízení, můžete použít [registrace zařízení – Get](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/get).
* Pokud chcete exportovat informace o registraci pro skupinu zařízení, můžete použít [skupiny pro registraci zařízení – Get](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/get).
* Pokud chcete exportovat informace o zařízeních, která už máte zřízená, můžete použít [stav registrace – získat stav registrace](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/getregistrationstate).

> [!NOTE]
> Při použití služby enterprise od Microsoftu, Microsoft generuje určité informace, označované jako protokoly generované systémem. Některé služby Device Provisioning systémem generované protokoly nejsou přístupné nebo exportovat správci tenanta. Tyto protokoly představují skutečné akce provedené v rámci služby a diagnostických dat týkající se pro jednotlivá zařízení.

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Kompletní dokumentaci k rozhraním API služby zřizování zařízení se nachází na [ https://docs.microsoft.com/rest/api/iot-dps ](https://docs.microsoft.com/rest/api/iot-dps).

Azure IoT Hub [zákaznická data funkce požadavků](../iot-hub/iot-hub-customer-data-requests.md).