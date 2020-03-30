---
title: Import certifikátů do kontejneru
description: Naučte se importovat soubory certifikátů do služby kontejneru Service Fabric.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: da4babd8f9d1a25a8514d0c6f1526b43a9723854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614107"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Import souboru certifikátu do kontejneru spuštěného na service fabric

Kontejnerové služby můžete zabezpečit zadáním certifikátu. Service Fabric poskytuje mechanismus pro služby uvnitř kontejneru pro přístup k certifikátu, který je nainstalován na uzlech v clusteru Windows nebo Linux (verze 5.7 nebo vyšší). Certifikát musí být nainstalován v úložišti certifikátů v části LocalMachine ve všech uzlech clusteru. Soukromý klíč odpovídající certifikátu musí být dostupný, přístupný a – v systému Windows – exportovatelný. Informace o certifikátu jsou uvedeny `ContainerHostPolicies` v manifestu aplikace pod značkou, jak ukazuje následující úryvek:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Pro clustery Windows při spuštění aplikace runtime exportuje každý odkazovaný certifikát a jeho odpovídající soukromý klíč do souboru PFX, zabezpečené ho náhodně generované heslo. Soubory PFX a hesla jsou přístupné uvnitř kontejneru pomocí následujících proměnných prostředí: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Pro clustery Linux certifikáty (PEM) jsou zkopírovány z úložiště určeného X509StoreName do kontejneru. Odpovídající proměnné prostředí na Linuxu jsou:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Případně pokud už certifikáty máte v požadovaném formuláři a chcete k nim přistupovat uvnitř kontejneru, můžete vytvořit datový balíček uvnitř balíčku aplikace a zadat následující v manifestu aplikace:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Služba nebo proces kontejneru je zodpovědný za import souborů certifikátů do kontejneru. Chcete-li importovat certifikát, můžete použít `setupentrypoint.sh` skripty nebo spustit vlastní kód v rámci procesu kontejneru. Zde je ukázkový kód v C# pro import souboru PFX:

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
Tento certifikát PFX lze použít k ověření aplikace nebo služby nebo k zabezpečené komunikaci s jinými službami. Ve výchozím nastavení jsou soubory ACLed pouze pro SYSTEM. Můžete acl na jiné účty, jak to vyžaduje služba.

Jako další krok si přečtěte následující články:

* [Nasazení kontejneru Windows do service fabricu na Windows Serveru 2016](service-fabric-get-started-containers.md)
* [Nasazení kontejneru Dockeru do Service Fabric na Linuxu](service-fabric-get-started-containers-linux.md)
