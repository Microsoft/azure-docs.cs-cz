---
title: Zastaralé TLS 1,0 a 1,1 ve službě IoT Hub a služba Device Provisioning (DPS) | Microsoft Docs
description: Pokyny týkající se vyřazení TLS 1,0 a 1,1 a podporovaná šifry v IoT Hub a DPS.
author: rezas
ms.author: reza
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f1ee3156e5639ae47d5bb323cf992586580668f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485907"
---
# <a name="tls-10-and-11-deprecation-in-iot-hub-and-device-provisioning-service"></a>Zastaralost TLS 1,0 a 1,1 ve službě IoT Hub a služba Device Provisioning

Aby se zajistilo nejlepší šifrování, IoT Hub a služba Device Provisioning Service (DPS) se přesouvá do protokolu TLS (Transport Layer Security) 1,2 jako šifrovací mechanizmus výběru pro zařízení a služby IoT. Starší verze protokolu TLS 1,0 a TLS 1,1 a také několik nedoporučených šifrovacích šifr se už od **1. července 2020**nedoporučuje.


## <a name="impact"></a>Dopad
Na základě specifických okolností a konfigurací pro konkrétní zákazníky může být vyřazení TLS 1,0 a 1,1 a nedoporučených šifrovaných šifr ovlivněná změnou pro zařízení a služby IoT, které komunikuje s IoT Hub nebo DPS. V některých případech se zařízení a služby, které jsou nekompatibilní s těmito změnami, nebudou moct po výše uvedeném datu vyjmutí připojit k IoT Hub nebo DPS.


## <a name="supported-ciphers"></a>Podporovaná šifry

Během TLS handshake budou povoleny pouze následující šifry:

* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Názory zákazníků

I když je vynucování TLS 1,2 v celém oboru nejlepší volbou šifrování a bude se používat jako plánované, budeme rádi vědět od zákazníků, kteří se týkají jejich konkrétního nasazení a potíží se zachováním TLS 1,2. Pro účely tohoto účelu můžete odeslat své komentáře do [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).