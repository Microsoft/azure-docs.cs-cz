---
title: Nastavení šifrovacího certifikátu v clusterech Windows
description: Přečtěte si, jak nastavit šifrovací certifikát a šifrovat tajné klíče v clusterech Windows.
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d563b338169ab26649b42c73f5fb7ed2fe8c0312
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460184"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Nastavení šifrovacího certifikátu a šifrování tajných kódů v clusterech Windows
Tento článek ukazuje, jak nastavit šifrovací certifikát a použít jej k šifrování tajných kódů v clusterech Windows. Pro linuxové clustery najdete [v tématu Nastavení šifrovacího certifikátu a šifrování tajných kódů v clusterech Linux.][secret-management-linux-specific-link]

[Azure Key Vault][key-vault-get-started] se zde používá jako bezpečné úložiště pro certifikáty a jako způsob, jak získat certifikáty nainstalované v clusterech Service Fabric v Azure. Pokud se nenasazujete do Azure, není nutné používat trezor klíčů ke správě tajných kódů v aplikacích Service Fabric. Použití *tajných* kódů v aplikaci je však cloudová platforma nezávislá, aby aplikace mohly být nasazeny do clusteru hostovaného kdekoli. 

## <a name="obtain-a-data-encipherment-certificate"></a>Získání certifikátu zašepletování dat
Certifikát zakódování dat se používá výhradně pro šifrování a dešifrování [parametrů][parameters-link] v proměnné Settings.xml služby a proměnné prostředí v servicemanifest.xml [služby.][environment-variables-link] Nepoužívá se pro ověřování nebo podepisování šifrovacího textu. Osvědčení musí splňovat tyto požadavky:

* Certifikát musí obsahovat soukromý klíč.
* Certifikát musí být vytvořen pro výměnu klíčů, exportovatelný do souboru Výměny osobních informací (.pfx).
* Použití klíče certifikátu musí zahrnovat šifrování dat (10) a nemělo by zahrnovat ověřování serveru nebo ověřování klienta. 
  
  Například při vytváření certifikátu podepsaného svým `KeyUsage` držitelem pomocí `DataEncipherment`prostředí PowerShell musí být příznak nastaven na :
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalace certifikátu v clusteru
Tento certifikát musí být nainstalován na každém uzlu v clusteru. Pokyny k nastavení [najdete v tématu Jak vytvořit cluster pomocí Správce prostředků Azure.][service-fabric-cluster-creation-via-arm] 

## <a name="encrypt-application-secrets"></a>Šifrování tajných kódů aplikací
Následující příkaz Prostředí PowerShell se používá k šifrování tajného klíče. Tento příkaz pouze šifruje hodnotu; **nepodepíše** šifrovaný text. K vytvoření šifrovacího textu pro tajné hodnoty je nutné použít stejný certifikát šifrování, který je nainstalován v clusteru:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Výsledný řetězec zakódovaný base-64 obsahuje jak tajný šifrovaný text, tak informace o certifikátu, který byl použit k jeho šifrování.

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [zadat šifrované tajné klíče v aplikaci.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
