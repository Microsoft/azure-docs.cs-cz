---
title: Naimportujte certifikáty do kontejneru spuštěného v Azure Service Fabric | Dokumentace Microsoftu
description: Naučte se importujte soubory certifikátů do služby kontejneru Service Fabric.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: twhitney, subramar
ms.openlocfilehash: d49c16741f581b2ad09dc173e8380fdf77391dbe
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51299057"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importovat soubor certifikátu do kontejneru spuštěného v Service Fabric

Služby kontejneru můžete zabezpečit zadáním certifikát. Service Fabric poskytuje mechanismus pro služby uvnitř kontejnerů pro přístup k certifikátu, který je nainstalován na uzlech v clusteru s Windows nebo Linuxem (verze 5.7 nebo novější). Certifikát musí být nainstalovány ve LocalMachine na všech uzlech clusteru. Informace o certifikátu je součástí manifest aplikace pod `ContainerHostPolicies` označit jako následující fragment kódu ukazuje:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Pro clustery Windows, při spuštění aplikace modul runtime načítá certifikáty a vygeneruje soubor PFX a heslo pro každý certifikát. Tento soubor PFX a heslo jsou dostupné v kontejneru pomocí následující proměnné prostředí: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Pro clustery s Linuxem certifikáty (PEM) zkopíruje z určeného X509StoreName do kontejneru úložiště. Odpovídající proměnné prostředí v Linuxu jsou:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Případně, pokud již máte certifikáty v požadované podobě a chtějí mít přístup k uvnitř kontejneru, můžete vytvořit balíček dat uvnitř balíčku aplikace a zadejte následující v manifestu aplikace:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Služba kontejneru nebo procesu je zodpovědná za Import certifikátu souborů do kontejneru. Chcete-li importovat certifikát, můžete použít `setupentrypoint.sh` skripty nebo když spouští vlastní kód v rámci procesu kontejneru. Tady je ukázkový kód v C# pro import souboru PFX:

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
Tento certifikát PFX lze použít k ověřování aplikace nebo služby nebo zabezpečenou komunikaci s dalšími službami. Ve výchozím nastavení jsou soubory ACLed pouze do systému. Seznam ACL můžete ji na jiné účty podle požadavků službu.

V dalším kroku přečtěte si následující články:

* [Nasazení kontejneru Windows do Service Fabric na Windows serveru 2016](service-fabric-get-started-containers.md)
* [Nasazení kontejneru Dockeru pro Service Fabric v Linuxu](service-fabric-get-started-containers-linux.md)
