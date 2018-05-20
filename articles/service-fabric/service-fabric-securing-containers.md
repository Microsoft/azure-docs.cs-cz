---
title: Importovat certifikáty do kontejneru systémem Azure Service Fabric | Microsoft Docs
description: Naučte se importujte soubory certifikátů do kontejneru služby Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: f234a6f6ca56d1833aac53f490feb5f667a6bf1b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importovat soubor certifikátu do kontejneru systémem Service Fabric

Zadáním certifikát můžete zabezpečit vaše služby kontejneru. Service Fabric poskytuje mechanismus pro služby uvnitř kontejneru pro přístup k certifikátu, který je nainstalován na uzly v clusteru systému Windows nebo Linux (verze 5.7 nebo vyšší). Certifikát musí být nainstalovaný do LocalMachine na všech uzlech clusteru. Informace o certifikátu je součástí manifest aplikace v rámci `ContainerHostPolicies` značce jako následující fragment kódu ukazuje:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Modul runtime pro Windows clusterů, při spouštění aplikace, čte certifikáty a vygeneruje soubor PFX a heslo pro každý certifikát. Tento soubor PFX a heslo jsou přístupné uvnitř kontejneru pomocí následující proměnné prostředí: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Pro Linux clusterů se zkopíruje certifikáty (PEM) po z obchodu určeného X509StoreName do kontejneru. Odpovídající proměnné prostředí v systému Linux jsou:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Případně, pokud už máte ve formuláři požadované certifikáty a chtějí mít přístup k uvnitř kontejneru, můžete vytvořit balíček data uvnitř vaší balíček aplikace a zadejte následující uvnitř manifest aplikace:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Službu kontejneru nebo procesu je zodpovědná za importování soubory certifikátu do kontejneru. Chcete-li import certifikátu, můžete použít `setupentrypoint.sh` skriptů nebo spuštění vlastního kódu v rámci procesu kontejneru. Tady je ukázkový kód v jazyce C# pro import souboru PFX:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Tento certifikát PFX, který slouží pro ověřování aplikace nebo služby nebo zabezpečenou komunikaci s jinými službami. Ve výchozím nastavení jsou soubory ACLed pouze do systému. Seznam ACL můžete ho na jiné účty podle potřeby službou.

Jako další krok přečtěte si následující články:

* [Nasazení kontejneru systému Windows pro Service Fabric na Windows Server 2016](service-fabric-get-started-containers.md)
* [Nasadit kontejner Docker do Service Fabric v systému Linux](service-fabric-get-started-containers-linux.md)
