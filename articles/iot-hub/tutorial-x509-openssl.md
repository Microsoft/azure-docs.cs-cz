---
title: Kurz – použití OpenSSL k vytvoření testovacích certifikátů X. 509 pro Azure IoT Hub | Microsoft Docs
description: Kurz – použití OpenSSL k vytvoření certifikátů certifikační autority a zařízení pro službu Azure IoT Hub
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
ms.openlocfilehash: 0d083d856138d7895a6e03f4d290ef3c4ddebd05
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630661"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Kurz: vytvoření testovacích certifikátů pomocí OpenSSL

I když si můžete koupit certifikáty X. 509 od důvěryhodné certifikační autority, vytvořit vlastní hierarchii testovacích certifikátů nebo použít certifikáty podepsané svým držitelem, které jsou vhodné pro testování ověřování zařízení ve službě IoT Hub. Následující příklad používá [OpenSSL](https://www.openssl.org/) a [OpenSSL kuchařka](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) k vytvoření certifikační autority (CA), podřízené certifikační autority a certifikátu zařízení. Příklad pak podepíše podřízenou certifikační autoritu a certifikát zařízení do hierarchie certifikátů. Tento příklad se zobrazí pouze pro účely.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Krok 1 – Vytvoření adresářové struktury kořenové certifikační autority

Vytvořte adresářovou strukturu pro certifikační autoritu.

* V adresáři **certifikáty** jsou uloženy nové certifikáty.
* Adresář **DB** se používá pro databázi certifikátů.
* **Privátní** adresář ukládá privátní klíč certifikační autority.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Krok 2 – vytvoření konfiguračního souboru kořenové certifikační autority

Před vytvořením certifikační autority vytvořte konfigurační soubor a uložte ho jako `rootca.conf` v adresáři rootca.

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>Krok 3 – vytvoření kořenové certifikační autority

Nejdřív vygenerujte klíč a žádost o podepsání certifikátu (CSR) v adresáři rootca.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Potom vytvořte certifikát certifikační autority podepsané svým držitelem. Samoobslužné podepisování je vhodné pro účely testování. Na příkazovém řádku zadejte přípony konfiguračního souboru ca_ext. To znamená, že certifikát je určený pro kořenovou certifikační autoritu a dá se použít k podepisování certifikátů a seznamů odvolaných certifikátů (CRL). Podepište certifikát a potvrďte ho do databáze.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Krok 4 – Vytvoření adresářové struktury podřízené certifikační autority

Vytvořte adresářovou strukturu pro podřízenou certifikační autoritu.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Krok 5 – vytvoření konfiguračního souboru podřízené certifikační autority

Vytvořte konfigurační soubor a uložte ho jako SubCA. conf v `subca` adresáři.

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days           
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>Krok 6 – vytvoření podřízené certifikační autority

V souboru vytvořte nové sériové číslo `rootca/db/serial` pro certifikát podřízené certifikační autority.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>Musíte vytvořit nové sériové číslo pro každý certifikát podřízené certifikační autority a každý certifikát zařízení, který vytvoříte. Různé certifikáty nemohou mít stejné sériové číslo.

V tomto příkladu se dozvíte, jak vytvořit podřízenou nebo registrační certifikační autoritu. Vzhledem k tomu, že k podepisování certifikátů můžete použít kořenovou certifikační autoritu, vytváření podřízené certifikační autority není bezpodmínečně nutné. Podřízená certifikační autorita však napodobuje hierarchie certifikátů reálného světa, ve kterých je kořenová certifikační autorita udržována v režimu offline a podřízené certifikační autority vydávají certifikáty klienta.

K vygenerování klíče a žádosti o podepsání certifikátu (CSR) použijte konfigurační soubor.

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Odešlete CSR na kořenovou certifikační autoritu a k vystavení a podepsání certifikátu podřízené certifikační autority použijte kořenovou certifikační autoritu. Na příkazovém řádku zadejte sub_ca_ext pro přepínač Extensions. Tato rozšíření označují, že certifikát je pro certifikační autoritu, která může podepisovat certifikáty a seznamy odvolaných certifikátů (CRL). Po zobrazení výzvy podepište certifikát a potvrďte ho do databáze.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Krok 7 – předvedení důkazu o vlastnictví

Nyní máte certifikát kořenové certifikační autority i certifikát podřízené certifikační autority. K podepisování certifikátů zařízení můžete použít buď jednu. Ten, který zvolíte, musí být nahrán do vašeho IoT Hub. V následujících krocích se předpokládá, že používáte certifikát podřízené certifikační autority. Postup nahrání a registrace certifikátu podřízené certifikační autority do IoT Hub:

1. V Azure Portal přejděte na IoTHub a vyberte **nastavení > certifikáty**.

1. Vyberte **Přidat** a přidejte svůj nový certifikát podřízené certifikační autority.

1. Do pole **název certifikátu** zadejte zobrazovaný název a vyberte soubor certifikátu PEM, který jste předtím vytvořili.

1. Vyberte **Uložit**. Certifikát se zobrazí v seznamu certifikátů se stavem **Neověřeno**. Proces ověření vám ukáže, že vlastníte certifikát.

   
1. Vyberte certifikát pro zobrazení dialogového okna **podrobností certifikátu** .

1. Vyberte možnost **Generovat ověřovací kód**. Další informace najdete v tématu [prokázání vlastnictví certifikátu certifikační autority](tutorial-x509-prove-possession.md).

1. Zkopírujte ověřovací kód do schránky. Je nutné nastavit ověřovací kód jako předmět certifikátu. Pokud je například ověřovací kód BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A, přidejte ho jako předmět certifikátu, jak je znázorněno v následujícím kroku.

1. Vygenerujte privátní klíč.

  ```bash
    $ openssl req -new -key pop.key -out pop.csr

    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

9. Vytvořte certifikát pomocí konfiguračního souboru kořenové certifikační autority a CSR.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

10. Výběr nového certifikátu v zobrazení **Podrobnosti o certifikátu**

11. Po nahrání certifikátu vyberte **ověřit**. Stav certifikátu certifikační autority by měl být změněn na **ověřeno**.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Krok 8 – vytvoření zařízení v IoT Hub

V Azure Portal přejděte do svého IoT Hub a vytvořte novou identitu zařízení IoT s následujícími hodnotami:

1. Zadejte **ID zařízení** , které se shoduje s názvem subjektu vašich certifikátů zařízení.

1. Vyberte typ ověřování **podepsaný certifikační autoritou X. 509** .

1. Vyberte **Uložit**.

## <a name="step-9---create-a-client-device-certificate"></a>Krok 9 – Vytvoření certifikátu klientského zařízení

Pokud chcete vygenerovat klientský certifikát, musíte nejdřív vygenerovat privátní klíč. Následující příkaz ukazuje, jak pomocí OpenSSL vytvořit privátní klíč. Vytvořte klíč v adresáři SubCA.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Vytvořte žádost o podepsání certifikátu (CSR) pro tento klíč. Nemusíte zadávat heslo pro výzvu ani volitelný název společnosti. V poli společné jméno však musíte zadat ID zařízení.

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

Ověřte, že je oddělení IT co očekávalo.

```bash
openssl req -text -in device.csr -noout
```

Odešlete CSR na podřízenou certifikační autoritu pro přihlášení k hierarchii certifikátů. Zadejte `client_ext` v `-extensions` přepínači. Všimněte si, že `Basic Constraints` ve vystaveném certifikátu označuje, že tento certifikát není pro certifikační autoritu. Pokud podepisujete více certifikátů, nezapomeňte aktualizovat sériové číslo před vygenerováním každého certifikátu pomocí `rand -hex 16 > db/serial` příkazu OpenSSL.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Další kroky

Pokud chcete zjistit, jestli váš certifikát může ověřit vaše zařízení IoT Hub, můžete přejít na [test certifikátu ověřování](tutorial-x509-test-certificate.md) .
