---
title: Zastaralování TLS 1,0 a 1,1 v IoT Hub | Microsoft Docs
description: Pokyny týkající se vyřazení TLS 1,0 a 1,1 a podporovaných šifr v IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381296"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Zastaralost TLS 1,0 a 1,1 v IoT Hub

Pro zajištění nejlepšího šifrování v rámci své třídy se IoT Hub přesouvá do TLS (Transport Layer Security) 1,2 jako šifrovací mechanizmus výběru pro zařízení a služby IoT. 

## <a name="timeline"></a>Časová osa

IoT Hub bude nadále podporovat protokol TLS 1.0/1.1 až do dalšího upozornění. Nicméně doporučujeme, aby všichni zákazníci migrovali do TLS 1,2 co nejdříve.

## <a name="supported-ciphers"></a>Podporovaná šifry

Časová osa dostupnosti různých šifr používaných v TLS handshake je následující:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (aktuálně podporováno)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (bude podporován v druhé polovině 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (bude podporován v druhé polovině 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (bude podporován v druhé polovině 2020)

## <a name="customer-feedback"></a>Názory zákazníků

I když je vynucování TLS 1,2 v celém oboru nejlepší volbou šifrování a bude se používat jako plánované, budeme rádi vědět od zákazníků, kteří se týkají jejich konkrétního nasazení a potíží se zachováním TLS 1,2. Pro účely tohoto účelu můžete odeslat komentáře do [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)aplikace.
