---
title: Nastavit šifrovací certifikát a šifrování tajných kódů v clusterech Azure Service Fabric s Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak nastavit šifrovací certifikát a šifrování tajných kódů na clusterech s Linuxem.
services: service-fabric
documentationcenter: .net
author: shsha
manager: ''
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 9589d6ea69a2293d592a9e63f2b726f1a620bb9e
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54068974"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Nastavit šifrovací certifikát a šifrování tajných kódů na clusterech s Linuxem
Tento článek popisuje, jak nastavit šifrovací certifikát a jeho použití k šifrování tajných kódů na clusterech s Linuxem. V případě clusterů Windows najdete v článku [nastavit až šifrovací certifikát a šifrování tajných kódů v clusterech Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Získat certifikát pro šifrování dat
Certifikát pro šifrování dat se používá výhradně pro šifrování a dešifrování [parametry] [ parameters-link] v služby Settings.xml a [proměnné prostředí] [ environment-variables-link] v souboru ServiceManifest.xml služby. Není použit pro ověření nebo podepisování šifrovaného textu. Certifikát musí splňovat následující požadavky:

* Certifikát musí obsahovat privátní klíč.
* Použití klíče certifikátu musí obsahovat šifrování dat (10) a nesmí obsahovat ověření serveru nebo klienta.

  Například můžete použít následující příkazy ke generování požadovaný certifikát pomocí OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalace certifikátu ve vašem clusteru
Certifikát musí být nainstalovaný na každém uzlu v clusteru pod `/var/lib/sfcerts`. Uživatelský účet, pod kterým běží služba (sfuser ve výchozím nastavení) **by měl mít oprávnění ke čtení** k nainstalovaný certifikát (tedy `/var/lib/sfcerts/TestCert.pem` aktuální příkladu).

## <a name="encrypt-secrets"></a>Šifrování tajných kódů
Následující fragment kódu můžete použít k šifrování tajného kódu. Tento fragment kódu šifruje pouze hodnotu parametru. provádí **není** podepsat šifrovaného textu. **Je nutné použít** stejný certifikát šifrování, která je nainstalovaná ve vašem clusteru k vytvoření šifrovaného textu pro hodnoty tajných kódů.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
Výsledný výstup řetězce s kódováním base-64 na encrypted.txt obsahuje šifrovaného textu tajného kódu i informace o certifikátu, který byl použit k šifrování. Ověření platnosti dešifrováním protokolem OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Další postup
Zjistěte, jak [zadejte šifrované tajné klíče v aplikaci.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
