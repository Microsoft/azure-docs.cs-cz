---
title: Nastavení certifikátu šifrování u clusterů se systémem Linux
description: Naučte se, jak nastavit šifrovací certifikát a šifrovat tajné klíče v clusterech se systémem Linux.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "78969037"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Nastavení šifrovacího certifikátu a šifrování tajných klíčů v clusterech se systémem Linux
V tomto článku se dozvíte, jak nastavit šifrovací certifikát a použít ho k šifrování tajných klíčů v clusterech se systémem Linux. Clustery Windows najdete v tématech [Nastavení šifrovacího certifikátu a šifrování tajných klíčů v clusterech Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Získání certifikátu pro zakódování dat
Certifikát zašifrování dat se používá výhradně pro šifrování a dešifrování [parametrů][parameters-link] v Settings.xml služby a [proměnných prostředí][environment-variables-link] v ServiceManifest.xml služby. Nepoužívá se k ověřování nebo podepisování šifrovacího textu. Certifikát musí splňovat následující požadavky:

* Certifikát musí obsahovat privátní klíč.
* Použití klíče certifikátu musí zahrnovat zašifrování dat (10) a nemělo by zahrnovat ověřování serveru nebo ověřování klientů.

  Například následující příkazy lze použít k vygenerování požadovaného certifikátu pomocí OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalace certifikátu do clusteru
Certifikát musí být nainstalovaný na všech uzlech v clusteru v části `/var/lib/sfcerts` . Uživatelský účet, pod kterým je služba spuštěna (ve výchozím nastavení sfuser), **by měl mít přístup pro čtení** nainstalovaného certifikátu (tj `/var/lib/sfcerts/TestCert.pem` . pro aktuální příklad).

## <a name="encrypt-secrets"></a>Šifrování tajných kódů
Následující fragment kódu lze použít k šifrování tajného klíče. Tento fragment kódu šifruje pouze hodnotu. **nepodepisuje** šifrovaný text. Pokud chcete pro tajné hodnoty vydávat šifrovaný text, **musíte použít** stejný certifikát zašifrování, který je nainstalovaný v clusteru.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
Výsledný výstup řetězce s kódováním Base-64 na encrypted.txt obsahuje šifrovaný šifrovaný kód i informace o certifikátu, který se použil k zašifrování. Jeho platnost můžete ověřit tak, že ho dešifrujete pomocí OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Další kroky
Naučte se, jak [zadat šifrované tajné klíče v aplikaci.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
