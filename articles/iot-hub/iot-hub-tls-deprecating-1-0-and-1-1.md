---
title: Zavržení TLS 1.0 a 1.1 v iot hubu | Dokumenty společnosti Microsoft
description: Pokyny týkající se vyřazení TLS 1.0 a 1.1 a podporovaných šifer v IoT Hubu.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381296"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Vyřazení TLS 1.0 a 1.1 v iot hubu

Chcete-li poskytnout nejlepší šifrování ve své třídě, IoT Hub se přesouvá do zabezpečení transportní vrstvy (TLS) 1.2 jako mechanismus šifrování volby pro zařízení a služby IoT. 

## <a name="timeline"></a>Časová osa

IoT Hub bude až do odvolání nadále podporovat TLS 1.0/1.1. Doporučujeme však, aby všichni zákazníci migrovali na TLS 1.2 co nejdříve.

## <a name="supported-ciphers"></a>Podporované šifry

Časová osa pro dostupnost různých šifer používaných v tls handshake je následující:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (aktuálně podporováno)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (bude podporováno v druhé polovině roku 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (bude podporováno v druhé polovině roku 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (bude podporováno v druhé polovině roku 2020)

## <a name="customer-feedback"></a>Zpětná vazba od zákazníků

Zatímco vynucení TLS 1.2 je nejlepší volbou šifrování ve své třídě v celém odvětví a bude povoleno podle plánu, stále bychom chtěli slyšet od zákazníků ohledně jejich konkrétních nasazení a potíží s přijetím TLS 1.2. Za tímto účelem můžete [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)své připomínky zaslat na aplikaci .
