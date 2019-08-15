---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/16/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 98172c2c487488a72bbfdd3a8205ac7d8668db60
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035756"
---
Vygenerujte certifikát certifikační autority.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

Vytiskněte certifikát certifikační autority ve formátu base64. Formát podporovaný v Azure. Později ji do Azure nahrajete jako součást konfigurace P2S.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

Vygenerujte uživatelský certifikát.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

Vygenerujte sadu P12 obsahující certifikát uživatele. Tato sada se použije v dalších krocích při práci s konfiguračními soubory klienta.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```