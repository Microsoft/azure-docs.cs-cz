---
title: Zastaralování TLS 1,0 a 1,1 v IoT Hub | Microsoft Docs
description: Pokyny týkající se vyřazení TLS 1,0 a 1,1 a podporovaných šifr v IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 5c717a02c2008436617d16f08625a1cecc204340
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83849514"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Zastaralost TLS 1,0 a 1,1 v IoT Hub

Pro zajištění nejlepšího šifrování v rámci své třídy se IoT Hub přesouvá do TLS (Transport Layer Security) 1,2 jako šifrovací mechanizmus výběru pro zařízení a služby IoT. 

## <a name="timeline"></a>Časová osa

IoT Hub bude nadále podporovat protokol TLS 1.0/1.1 až do dalšího upozornění. Nicméně doporučujeme, aby všichni zákazníci migrovali do TLS 1,2 co nejdříve.

## <a name="deprecating-tls-11-ciphers"></a>Zastaralá šifra TLS 1,1

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>Zastaralá šifra TLS 1,0

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-ciphers"></a>Šifry TLS 1,2

Viz [IoT Hub protokolu TLS 1,2 Doporučené šifry](iot-hub-tls-support.md#recommended-ciphers).
 
## <a name="customer-feedback"></a>Názory zákazníků

I když je vynucování TLS 1,2 v celém oboru nejlepší volbou šifrování a bude se používat jako plánované, budeme rádi vědět od zákazníků, kteří se týkají jejich konkrétního nasazení a potíží se zachováním TLS 1,2. Pro účely tohoto účelu můžete odeslat komentáře do aplikace [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com) .
