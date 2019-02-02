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
ms.openlocfilehash: c6f9065786879749eee6187e93283f4c026b7fff
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568800"
---
Následující konfigurace počítače se použil pro následující postup:

  | | |
  |---|---|
  |Počítač| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME = "Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
  |Závislosti| strongSwan |

#### <a name="1-install-strongswan"></a>1. Nainstalujte strongSwan

Pomocí následujících příkazů nainstalujte požadované strongSwan konfigurace:

```
apt-get install strongswan-ikev2 strongswan-plugin-eap-tls
```

```
apt-get install libstrongswan-standard-plugins
```

```
apt-get install strongswan-pki
```

#### <a name="2-generate-keys-and-certificate"></a>2. Generování klíčů a certifikátů

Vygenerování certifikátu certifikační Autority.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

Tisk certifikát certifikační Autority ve formátu base64. To je formát, který je podporovaný Azure. Můžete se později odeslat ho do Azure jako součást konfigurace P2S.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

Generovat certifikát uživatele.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

Generovat p12 sadu obsahující certifikát uživatele. Tato sada se použije v dalších krocích při práci s konfiguračních souborů klienta.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```