---
title: Nastavit šifrovací certifikát a šifrování tajných kódů v clusterech Windows Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak nastavit šifrovací certifikát a šifrování tajných kódů v clusterech Windows.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: f7582f26eb65526ad392f2df29f5686e18262e40
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54069002"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Nastavit šifrovací certifikát a šifrování tajných kódů v clusterech Windows
Tento článek popisuje, jak nastavit šifrovací certifikát a jeho použití k šifrování tajných kódů v clusterech Windows. Linuxové clustery, naleznete v tématu [nastavit až šifrovací certifikát a šifrování tajných kódů na clusterech s Linuxem.][secret-management-linux-specific-link]

[Služba Azure Key Vault] [ key-vault-get-started] zde slouží jako umístění úložiště pro bezpečné pro certifikáty a jako způsob, jak získat certifikátů nainstalovaných na clustery Service Fabric v Azure. Pokud nenasazujete do Azure, není potřeba pomocí služby Key Vault pro správu tajných kódů aplikace Service Fabric. Ale *pomocí* tajných kódů v aplikaci je cloud více platforem, aby umožňovala nasadit do clusteru už jsou hostované kdekoli. 

## <a name="obtain-a-data-encipherment-certificate"></a>Získat certifikát pro šifrování dat
Certifikát pro šifrování dat se používá výhradně pro šifrování a dešifrování [parametry] [ parameters-link] v služby Settings.xml a [proměnné prostředí] [ environment-variables-link] v souboru ServiceManifest.xml služby. Není použit pro ověření nebo podepisování šifrovaného textu. Certifikát musí splňovat následující požadavky:

* Certifikát musí obsahovat privátní klíč.
* Certifikát musí být vytvořen pro výměnu klíčů, musí umožňovat export do souboru Personal Information Exchange (.pfx).
* Použití klíče certifikátu musí obsahovat šifrování dat (10) a nesmí obsahovat ověření serveru nebo klienta. 
  
  Při vytváření certifikátu podepsaného svým držitelem pomocí Powershellu, například `KeyUsage` příznak musí být nastaveno na `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalace certifikátu ve vašem clusteru
Tento certifikát musí být nainstalována na každém uzlu v clusteru. Zobrazit [vytvoření clusteru pomocí Azure Resource Manageru] [ service-fabric-cluster-creation-via-arm] pokyny pro instalaci. 

## <a name="encrypt-application-secrets"></a>Šifrování tajných klíčů aplikací
Následující příkaz Powershellu se používá k šifrování tajného kódu. Tento příkaz šifruje pouze hodnotu parametru. provádí **není** podepsat šifrovaného textu. Je nutné použít stejný certifikát šifrování, která je nainstalovaná ve vašem clusteru k vytvoření šifrovaného textu pro hodnoty tajných kódů:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Výsledný řetězec base-64 kódovaných obsahuje šifrovaného textu tajného kódu i informace o certifikátu, který byl použit k šifrování.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [zadejte šifrované tajné klíče v aplikaci.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
