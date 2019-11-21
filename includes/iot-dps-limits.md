---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: fb77682bce0d9faef8f9ac8197afd8751560d515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224329"
---
The following table lists the limits that apply to Azure IoT Hub Device Provisioning Service resources.

| Prostředek | škálování |
| --- | --- |
| Maximum device provisioning services per Azure subscription | 10 |
| Maximum number of enrollments | 1 000 000 |
| Maximum number of registrations | 1 000 000 |
| Maximum number of enrollment groups | 100 |
| Maximum number of CAs | 25 |
| Maximum number of linked IoT hubs | 50 |
| Maximum size of message | 96 KB|


> [!NOTE]
> To increase the number of instances in your subscription, contact [Microsoft Support](https://azure.microsoft.com/support/options/).

> [!NOTE]
> To increase the number of enrollments and registrations on your provisioning service, contact [Microsoft Support](https://azure.microsoft.com/support/options/).

The Device Provisioning Service throttles requests when the following quotas are exceeded.

| Omezení | Per-unit value |
| --- | --- |
| Operations | 200/min/service |
| Registrace zařízení | 200/min/service |
| Device polling operation | 5/10 sec/device |
