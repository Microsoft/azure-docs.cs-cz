---
title: Kurz – prokázání vlastnictví certifikátů certifikační autority v Azure IoT Hub | Microsoft Docs
description: Kurz – prokázání, že vlastníte certifikát certifikační autority pro Azure IoT Hub
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
- devx-track-azurecli
ms.openlocfilehash: 5e2827a4f87398f0a37ef04f797d2c7276d1a66d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384166"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Kurz: prokazující vlastnictví certifikátu certifikační autority

Po nahrání certifikátu kořenové certifikační autority (CA) nebo podřízeného certifikátu certifikační AUTORITy do služby IoT Hub musíte prokázat, že vlastníte certifikát:

1. V Azure Portal přejděte na IoTHub a vyberte **nastavení > certifikáty**.

2. Vyberte **Přidat** a přidejte nový certifikát certifikační autority.

3. Do pole **název certifikátu** zadejte zobrazovaný název a vyberte certifikát PEM, který chcete přidat.

4. Vyberte **Uložit**. Certifikát se zobrazí v seznamu certifikátů se stavem **Neověřeno**. Tento proces ověření vám ukáže, že máte k dispozici certifikát.

5. Vyberte certifikát pro zobrazení dialogového okna **podrobností certifikátu** .

6. V dialogovém okně vyberte **Generovat ověřovací kód** .

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Dialog podrobnosti certifikátu}":::

7. Zkopírujte ověřovací kód do schránky. Je nutné nastavit ověřovací kód jako předmět certifikátu. Pokud je například ověřovací kód 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3, přidejte ho jako předmět certifikátu, jak je znázorněno v následujícím kroku.

8. Existují tři způsoby, jak vygenerovat ověřovací certifikát:

    * Pokud používáte PowerShellový skript dodaný Microsoftem, spusťte příkaz `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` a vytvořte certifikát s názvem `VerifyCert4.cer` . Další informace najdete v tématu [použití skriptů poskytovaných společností Microsoft](tutorial-x509-scripts.md).

    * Pokud používáte skript bash dodávaný Microsoftem, spusťte příkaz `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` a vytvořte certifikát s názvem `verification-code.cert.pem` . Další informace najdete v tématu [použití skriptů poskytovaných společností Microsoft](tutorial-x509-scripts.md).

    * Pokud k vygenerování certifikátů používáte OpenSSL, musíte nejdřív vygenerovat privátní klíč a pak žádost o podepsání certifikátu (CSR):

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      Pak vytvořte certifikát pomocí konfiguračního souboru kořenové certifikační autority (viz níže) nebo konfiguračního souboru podřízené certifikační autority a CSR.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Další informace najdete v tématu [použití OpenSSL k vytváření testovacích certifikátů](tutorial-x509-openssl.md).

10. Vyberte nový certifikát v zobrazení **Podrobnosti o certifikátu** .

11. Po nahrání certifikátu vyberte **ověřit**. Stav certifikátu certifikační autority by měl být změněn na **ověřeno**.
