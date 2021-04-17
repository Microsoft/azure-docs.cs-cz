---
title: Kurz – použití OpenSSL k vytvoření certifikátů podepsaných svým držitelem pro Azure IoT Hub | Microsoft Docs
description: Kurz – použití OpenSSL k vytvoření certifikátů X. 509 podepsaných svým držitelem pro Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 982e402946cbd71d946bc1e316cef99621c536a3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378189"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Kurz: použití OpenSSL k vytvoření certifikátů podepsaných svým držitelem

Pomocí dvou certifikátů zařízení podepsaných svým držitelem můžete ověřit zařízení do svého IoT Hub. To se někdy nazývá ověřování kryptografických otisků, protože certifikáty obsahují kryptografické otisky (hodnoty hash), které odesíláte do centra IoT. V následujících krocích se dozvíte, jak vytvořit dva certifikáty podepsané svým držitelem.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Krok 1 – vytvoření klíče pro první certifikát

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Krok 2 – Vytvoření CSR pro první certifikát

Po zobrazení výzvy Nezapomeňte zadat ID zařízení.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>Krok 3 – ověření CSR

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>Krok 4 – certifikát podepsaný svým držitelem 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>Krok 5 – vytvoření klíče pro certifikát 2

Po zobrazení výzvy zadejte stejné ID zařízení, které jste použili pro certifikát 1.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-6---self-sign-certificate-2"></a>Krok 6 – podpis certifikátu 2 – držitele

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>Krok 7 – Načtení kryptografického otisku pro certifikát 1

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>Krok 8 – Načtení kryptografického otisku pro certifikát 2

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>Krok 9 – vytvoření nového zařízení IoT

V Azure Portal přejděte do svého IoT Hub a vytvořte novou identitu zařízení IoT s následujícími charakteristikami:

* Zadejte **ID zařízení** , které se shoduje s názvem subjektu vašich dvou certifikátů.
* Vyberte typ ověřování **podepsaný svým držitelem X. 509** .
* Vložte kryptografické otisky řetězců, které jste zkopírovali ze svého primárního a sekundárního certifikátu zařízení. Ujistěte se, že šestnáctkové řetězce nemají žádné oddělovače dvojtečky.

## <a name="next-steps"></a>Další kroky

Pokud chcete zjistit, jestli váš certifikát může ověřit vaše zařízení IoT Hub, můžete přejít na [test certifikátu ověřování](tutorial-x509-test-certificate.md) .
