---
title: Nastavení šifrovacího certifikátu v clusterech Linuxu
description: Přečtěte si, jak nastavit šifrovací certifikát a šifrovat tajné kódy v clusterech Linuxu.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969037"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Nastavení šifrovacího certifikátu a šifrování tajných kódů v clusterech Linux
Tento článek ukazuje, jak nastavit šifrovací certifikát a použít jej k šifrování tajných kódů v clusterech Linux. Clustery Windows najdete [v tématu Nastavení šifrovacího certifikátu a šifrování tajných kódů v clusterech Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Získání certifikátu zašepletování dat
Certifikát zakódování dat se používá výhradně pro šifrování a dešifrování [parametrů][parameters-link] v proměnné Settings.xml služby a proměnné prostředí v servicemanifest.xml [služby.][environment-variables-link] Nepoužívá se pro ověřování nebo podepisování šifrovacího textu. Osvědčení musí splňovat tyto požadavky:

* Certifikát musí obsahovat soukromý klíč.
* Použití klíče certifikátu musí zahrnovat šifrování dat (10) a nemělo by zahrnovat ověřování serveru nebo ověřování klienta.

  Například následující příkazy lze použít ke generování požadovaného certifikátu pomocí OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalace certifikátu v clusteru
Certifikát musí být nainstalován na každém uzlu `/var/lib/sfcerts`v clusteru pod aplikací . Uživatelský účet, pod kterým je služba spuštěna (sfuser ve výchozím nastavení) **by měl mít přístup pro čtení** k nainstalovanému certifikátu (to znamená pro `/var/lib/sfcerts/TestCert.pem` aktuální příklad).

## <a name="encrypt-secrets"></a>Šifrování tajných kódů
Následující úryvek lze použít k šifrování tajného klíče. Tento úryvek pouze šifruje hodnotu; **nepodepíše** šifrovaný text. **K** vytvoření šifrovacího textu pro tajné hodnoty je nutné použít stejný certifikát šifrování, který je nainstalován v clusteru.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
Výsledný výstup řetězce base-64 pro encrypted.txt obsahuje jak tajný šifrovaný text, tak informace o certifikátu, který byl použit k jeho šifrování. Jeho platnost můžete ověřit dešifrováním pomocí OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [zadat šifrované tajné klíče v aplikaci.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
