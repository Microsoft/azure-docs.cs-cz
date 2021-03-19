---
title: Nastavení certifikátu šifrování v clusterech Windows
description: Naučte se, jak nastavit šifrovací certifikát a šifrovat tajné klíče v clusterech Windows.
ms.topic: conceptual
ms.date: 01/04/2019
ms.openlocfilehash: eb4909d62a2627c368f24dab572b25c6f1df30ec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "82583292"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Nastavení šifrovacího certifikátu a šifrování tajných klíčů v clusterech Windows
V tomto článku se dozvíte, jak nastavit šifrovací certifikát a použít ho k šifrování tajných klíčů v clusterech Windows. Clustery se systémem Linux najdete v tématech [Nastavení šifrovacího certifikátu a šifrování tajných klíčů v clusterech se systémem Linux.][secret-management-linux-specific-link]

[Azure Key Vault][key-vault-get-started] se tady používá jako bezpečné umístění úložiště pro certifikáty a jako způsob, jak získat certifikáty nainstalované v clusterech Service Fabric v Azure. Pokud neprovádíte nasazení do Azure, nemusíte používat Key Vault ke správě tajných kódů v aplikacích Service Fabric. *Používání* tajných klíčů v aplikaci je ale cloudová platforma – nezávislá umožňuje nasazení aplikací do clusteru hostovaného kdekoli. 

## <a name="obtain-a-data-encipherment-certificate"></a>Získání certifikátu pro zakódování dat
Certifikát zašifrování dat se používá výhradně pro šifrování a dešifrování [parametrů][parameters-link] v Settings.xml služby a [proměnných prostředí][environment-variables-link] v ServiceManifest.xml služby. Nepoužívá se k ověřování nebo podepisování šifrovacího textu. Certifikát musí splňovat následující požadavky:

* Certifikát musí obsahovat privátní klíč.
* Certifikát musí být vytvořen pro výměnu klíčů, který lze exportovat do souboru. pfx (Personal Information Exchange).
* Použití klíče certifikátu musí zahrnovat zašifrování dat (10) a nemělo by zahrnovat ověřování serveru nebo ověřování klientů. 
  
  Například při vytváření certifikátu podepsaného svým držitelem pomocí PowerShellu `KeyUsage` musí být příznak nastaven na `DataEncipherment` :
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalace certifikátu do clusteru
Tento certifikát musí být nainstalovaný na každém uzlu v clusteru. Pokyny k instalaci najdete v tématu [Postup vytvoření clusteru pomocí Azure Resource Manager][service-fabric-cluster-creation-via-arm] . 

## <a name="encrypt-application-secrets"></a>Šifrování tajných klíčů aplikace
Následující příkaz prostředí PowerShell slouží k šifrování tajného klíče. Tento příkaz zašifruje pouze hodnotu. **nepodepisuje** šifrovaný text. Ke generování šifrovaného textu pro tajné hodnoty musíte použít stejný certifikát zašifrování, který je nainstalovaný ve vašem clusteru:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Výsledný řetězec s kódováním Base-64 obsahuje tajný šifrovaný text i informace o certifikátu, který se použil k zašifrování.

## <a name="next-steps"></a>Další kroky
Naučte se, jak [zadat šifrované tajné klíče v aplikaci.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
