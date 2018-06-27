---
title: Konfigurace certifikátů pro aplikace Azure Service Fabric v systému Linux | Microsoft Docs
description: Konfigurace certifikátů pro vaši aplikaci s modulem runtime Service Fabric na cluster systému Linux
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: v-jamebr
ms.openlocfilehash: 2d6d387ed12e7261d09669686c0710786a4302dd
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025676"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certifikáty a zabezpečení v clusterech Linux

Tento článek obsahuje informace o konfiguraci certifikátů X.509 pro Linux clusterů.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Umístění a formát certifikáty X.509 na Linuxových uzlů

Service Fabric obecně očekává certifikáty X.509 v */var/lib/sfcerts* adresář na uzlech clusteru systému Linux. To platí pro certifikáty clusteru klientské certifikáty, atd. V některých případech můžete zadat umístění jiné než *lib/var/sfcerts* složku pro certifikáty. Například se sestavuje pomocí Service Fabric Java SDK službami Reliable Services, můžete do jiného umístění prostřednictvím konfigurační balíček (souborech Settings.xml) pro některé certifikáty specifické pro aplikaci. Další informace najdete v tématu [certifikáty, kterou se odkazuje v konfigurační balíček (souborech Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

Pro Linux clusterů Service Fabric očekává, že certifikáty být jako buď soubor .pem, který obsahuje certifikát a privátní klíč, nebo jako soubor .crt, který obsahuje certifikát a soubor s příponou Key, který obsahuje soukromý klíč. Všechny soubory musí být ve formátu PEM. 

Pokud nainstalujete certifikát z Azure Key Vault pomocí [šablony Resource Manageru](./service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template) nebo [prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric) příkazy, certifikát je nainstalovaný ve správném formátu v */var/ lib/sfcerts* adresáře v každém uzlu. Pokud nainstalujete certifikát prostřednictvím jinou metodu, musí se ujistěte, zda je certifikát správně nainstalován na uzlech clusteru.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certifikáty uvedené v manifestu aplikace

Certifikáty, zadaný v aplikaci manifest, například prostřednictvím [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) nebo [ **EndpointCertificate** ](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element)prvky, musí existovat v */var/lib/sfcerts* adresáře. Prvky, které se používají k určení certifikátů v manifestu aplikace nepřebírají atribut cesty, takže certifikáty musí být součástí výchozí adresář. Tyto prvky provést volitelný **X509StoreName** atribut. Výchozí hodnota je "Moje", která odkazuje */var/lib/sfcerts* adresář na Linuxových uzlů. Jakákoli jiná hodnota je definovaná na cluster systému Linux. Doporučujeme, abyste při vynechání **X509StoreName** atribut pro aplikace, které běží na systému Linux clusterů. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certifikáty, kterou se odkazuje v konfigurační balíček (souborech Settings.xml)

U některých služeb můžete nakonfigurovat certifikáty X.509 v [ConfigPackage](./service-fabric-application-and-service-manifests.md) (ve výchozím nastavení v souborech Settings.xml). Například jedná o tento případ po deklarování certifikátů používaných pro zabezpečenou RPC kanály pro spolehlivé služby, které jsou vytvořené s nástroji Service Fabric .NET Core nebo sady Java SDK. Existují dva způsoby, jak certifikáty odkaz v konfigurační balíček. Podpora se liší mezi .NET Core a sady Java SDK.

### <a name="using-x509-securitycredentialstype"></a>Pomocí X509 SecurityCredentialsType

S .NET nebo sady Java SDK, můžete zadat **X509** pro **SecurityCredentialsType**. To odpovídá `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric._x509_credentials)) typ `SecurityCredentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric._security_credentials)).

**X509** odkaz vyhledá certifikát v úložišti certifikátů. Následující kód XML ukazuje parametry použité k určení umístění certifikátu:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Pro služby spuštěné v systému Linux **LocalMachine**/**Moje** ukazuje na výchozí umístění pro certifikáty, */var/lib/sfcerts* adresáře. Pro systémy Linux jinou kombinaci těchto **CertificateStoreLocation** a **Název_úložiště_certifikátů** nejsou definovány. 

Vždycky zadat **LocalMachine** pro **CertificateStoreLocation** parametr. Není nutné specifikovat **Název_úložiště_certifikátů** parametr vzhledem k tomu, že je standardně nastavena na "Moje". S **X509** odkaz, soubory certifikátu se musí nacházet v */var/lib/sfcerts* adresáře na uzlu clusteru.  

Následují XML **TransportSettings** části podle tento styl:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x5092-securitycredentialstype"></a>Pomocí X509_2 SecurityCredentialsType

Pomocí sady Java SDK, můžete určit **X509_2** pro **SecurityCredentialsType**. To odpovídá `X509Credentials2` ([Java](https://docs.microsoft.com/java/api/system.fabric._x509_credentials2)) typ `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric._security_credentials)). 

S **X509_2** odkaz, zadáte parametr cesty, tak můžete certifikát vyhledat v adresáři jiné než */var/lib/sfcerts*.  Následující kód XML ukazuje parametry použité k určení umístění certifikátu: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Následují XML **TransportSettings** části založené na tomto stylu.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> Certifikát je zadán jako soubor .crt v předchozím soubor XML. Z toho vyplývá, že existuje také soubor s příponou Key obsahující privátní klíč ve stejném umístění.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Konfigurace aplikace pro spouštění v systému Linux clusterů spolehlivé služby

Sady SDK služby Fabric umožňují komunikovat s modulem runtime Service Fabric rozhraní API můžete využít platformu. Při spuštění jakékoli aplikace, která používá tuto funkci zabezpečeného Linux clusterů, budete muset nakonfigurovat svoji aplikaci se certifikát, který můžete použít k ověření s modulem runtime Service Fabric. Aplikace, které obsahují služby Service Fabric spolehlivé Service napsané pomocí .NET Core nebo sady Java SDK vyžadovat tuto konfiguraci. 

Pro konfiguraci aplikace, přidejte [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) prvek v rámci **certifikáty** značku, která je umístěná **ApplicationManifest**  značky v *ApplicationManifest.xml* souboru. Následující kód XML ukazuje odkazuje jeho kryptografický otisk certifikátu: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Můžete odkazovat na certifikát clusteru nebo certifikát, který nainstalujete na každý uzel clusteru. V systému Linux, soubory certifikátu musí být umístěny v */var/lib/sfcerts* adresáře. Další informace najdete v tématu [umístění a formátu X.509 – certifikáty na uzly Linux](#location-and-format-of-x509-certificates-on-linux-nodes).



