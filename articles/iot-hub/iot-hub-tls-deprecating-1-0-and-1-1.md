---
title: Zavržení TLS 1.0 a 1.1 ve službě IoT Hub a zřizování zařízení (DPS) | Dokumenty společnosti Microsoft
description: Pokyny týkající se vyřazení TLS 1.0 a 1.1 a podporovaných šifer v IoT Hubu a DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402784"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Vyřazení TLS 1.0 a 1.1 ve službě IoT Hub a device provisioning Service

Chcete-li poskytnout nejlepší šifrování ve své třídě, služby IoT Hub a služba zřizování zařízení (DPS) se přesouvají do služby TLS (Transport Layer Security) 1.2 jako mechanismus šifrování pro zařízení a služby IoT. 

## <a name="supported-ciphers"></a>Podporované šifry

Časová osa pro dostupnost různých šifer používaných v tls handshake je následující:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (aktuálně podporováno)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (bude podporováno v druhé polovině roku 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (bude podporováno v druhé polovině roku 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (bude podporováno v druhé polovině roku 2020)


## <a name="customer-feedback"></a>Zpětná vazba od zákazníků

Zatímco vynucení TLS 1.2 je nejlepší volbou šifrování ve své třídě v celém odvětví a bude povoleno podle plánu, stále bychom chtěli slyšet od zákazníků ohledně jejich konkrétních nasazení a potíží s přijetím TLS 1.2. Za tímto účelem můžete [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)své připomínky zaslat na aplikaci .
