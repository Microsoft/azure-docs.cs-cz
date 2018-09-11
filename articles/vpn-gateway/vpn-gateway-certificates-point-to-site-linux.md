---
title: 'Generování a export certifikátů pro Point-to-Site: Linux: rozhraní příkazového řádku: Azure | Dokumentace Microsoftu'
description: Vytvořit certifikát podepsaný svým držitelem, exportujte veřejný klíč a generovat klientské certifikáty s Linuxem (strongSwan) rozhraní příkazového řádku.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 8647b3b3eda980dbd5d5ec368b6b4b13949ecaf1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305738"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-linux-strongswan-cli"></a>Generování a export certifikátů pro Point-to-Site pomocí Linuxu strongSwan rozhraní příkazového řádku

Připojení point-to-Site používat certifikáty k ověřování. Tento článek ukazuje, jak vytvořit certifikát podepsaný svým držitelem a generování klientských certifikátů pomocí rozhraní příkazového řádku systému Linux a strongSwan. Pokud hledáte pokyny jiný certifikát, najdete v článku [Powershell](vpn-gateway-certificates-point-to-site.md) nebo [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) článků.

> [!NOTE]
> Kroky v tomto článku vyžadují strongSwan.
>

Konfigurace počítače používat pro kroky pro účely tohoto článku byl následující:

| | |
|---|---|
|**Počítače**| Ubuntu Server 16.04<br>ID_LIKE = debian<br>PRETTY_NAME = "Ubuntu 16.04.4 LTS"<br>VERSION_ID = "16.04" |
|**Závislosti**| apt-get install strongswan ikev2 strongswan-plugin-eap-tls<br>apt-get install libstrongswan – standardní – moduly plug-in |

## <a name="install-strongswan"></a>Nainstalujte strongSwan

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

Informace o tom, jak nainstalovat strongSwan pomocí grafického uživatelského rozhraní najdete v tématu Postup v [konfigurace klienta](point-to-site-vpn-client-configuration-azure-cert.md#install) článku.

## <a name="generate-keys-and-certificate"></a>Generování klíčů a certifikátů

1. Vygenerování certifikátu certifikační Autority.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```
2. Tisk certifikát certifikační Autority ve formátu base64. To je formát, který je podporovaný Azure. Můžete se později odeslat ho do Azure jako součást konfigurace P2S.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```
3. Generovat certifikát uživatele.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```
4. Generovat p12 sadu obsahující certifikát uživatele. Tato sada se použije v dalších krocích při práci s [konfiguračních souborů klienta](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```

## <a name="next-steps"></a>Další postup

Pokračovat v konfiguraci Point-to-Site tak, aby [vytvoření a instalace konfiguračních souborů klienta VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).