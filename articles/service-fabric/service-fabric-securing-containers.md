---
title: Import certifikátů do kontejneru běžícího na Azure Service Fabric | Microsoft Docs
description: Naučte se importovat soubory certifikátů do služby Service Fabric Container Service.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 496d47c88636b11375d97cc4bc50efd9d468d645
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170466"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importovat soubor certifikátu do kontejneru běžícího na Service Fabric

Služby kontejneru můžete zabezpečit zadáním certifikátu. Service Fabric poskytuje mechanismus pro služby uvnitř kontejneru pro přístup k certifikátu, který je nainstalovaný na uzlech v clusteru s Windows nebo Linux (verze 5,7 nebo vyšší). Certifikát musí být nainstalovaný v úložišti certifikátů v části LocalMachine na všech uzlech clusteru. Privátní klíč odpovídající certifikátu musí být dostupný, přístupný a povolený pro Windows, který lze exportovat. Informace o certifikátu jsou uvedeny v manifestu aplikace pod značkou `ContainerHostPolicies`, jak ukazuje následující fragment kódu:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

U clusterů Windows při spuštění aplikace exportuje modul runtime každý odkazovaný certifikát a příslušný privátní klíč do souboru PFX, který je zabezpečený pomocí náhodně generovaného hesla. Soubory PFX a hesla jsou v kontejneru přístupné pomocí následujících proměnných prostředí: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

V případě clusterů se systémem Linux se certifikáty (PEM) zkopírují z úložiště určeného parametrem X509StoreName do kontejneru. Odpovídající proměnné prostředí v systému Linux jsou:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Případně, pokud již máte certifikáty v požadovaném formuláři a chcete k nim přistupovat v rámci kontejneru, můžete vytvořit datový balíček v rámci balíčku aplikace a zadat následující v manifestu aplikace:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Služba kontejneru nebo proces zodpovídá za Import souborů certifikátů do kontejneru. Chcete-li importovat certifikát, můžete použít skripty `setupentrypoint.sh` nebo spustit vlastní kód v rámci procesu kontejneru. Zde je ukázkový kód C# pro import souboru PFX:

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
Tento certifikát PFX se dá použít k ověřování aplikace nebo služby nebo k zabezpečení komunikace s ostatními službami. Ve výchozím nastavení jsou soubory ACLed pouze v systému. Můžete ho SEZNAMovat podle potřeby na jiné účty, podle kterých služba vyžaduje.

V dalším kroku si přečtěte následující články:

* [Nasazení kontejneru Windows pro Service Fabric v systému Windows Server 2016](service-fabric-get-started-containers.md)
* [Nasazení kontejneru Docker pro Service Fabric v systému Linux](service-fabric-get-started-containers-linux.md)
