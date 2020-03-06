---
title: Vyřazení TLS 1,0 a 1,1 ve službě IoT Hub a služba Device Provisioning (DPS) | Microsoft Docs
description: Pokyny týkající se vyřazení TLS 1,0 a 1,1 a podporovaná šifry v IoT Hub a DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402784"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Vyřazení TLS 1,0 a 1,1 ve službě IoT Hub a služba Device Provisioning

Aby se zajistilo nejlepší šifrování, IoT Hub a služba Device Provisioning Service (DPS) se přesouvá do protokolu TLS (Transport Layer Security) 1,2 jako šifrovací mechanizmus výběru pro zařízení a služby IoT. 

## <a name="supported-ciphers"></a>Podporovaná šifry

Časová osa dostupnosti různých šifr používaných v TLS handshake je následující:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (aktuálně podporováno)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (bude podporován v druhé polovině 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (bude podporován v druhé polovině 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (bude podporován v druhé polovině 2020)


## <a name="customer-feedback"></a>Názory zákazníků

I když je vynucování TLS 1,2 v celém oboru nejlepší volbou šifrování a bude se používat jako plánované, budeme rádi vědět od zákazníků, kteří se týkají jejich konkrétního nasazení a potíží se zachováním TLS 1,2. Pro účely tohoto účelu můžete odeslat své komentáře do [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
