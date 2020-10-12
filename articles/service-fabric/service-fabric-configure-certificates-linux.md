---
title: Konfigurace certifikátů pro aplikace na platformě Linux
description: Konfigurace certifikátů pro aplikaci pomocí modulu runtime Service Fabric v clusteru se systémem Linux
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: a97c8b8315fe3be405aed9c6570004afb8fafd1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258673"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certifikáty a zabezpečení v clusterech se systémem Linux

Tento článek poskytuje informace o konfiguraci certifikátů X. 509 v clusterech se systémem Linux.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Umístění a formát certifikátů X. 509 na uzlech se systémem Linux

Service Fabric obvykle očekává, že se v adresáři */var/lib/sfcerts* v uzlech clusteru se systémem Linux nacházejí certifikáty X. 509. To platí pro certifikáty clusteru, klientské certifikáty atd. V některých případech můžete pro certifikáty zadat jiné umístění, než je složka *var/lib/sfcerts* . Například pomocí Reliable Services sestaveného pomocí Service Fabric Java SDK můžete pro některé certifikáty specifické pro aplikaci zadat jiné umístění prostřednictvím konfiguračního balíčku (Settings.xml). Další informace najdete v tématu [certifikáty, na které se odkazuje v konfiguračním balíčku (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

U clusterů se systémem Linux Service Fabric očekává, že se certifikáty nacházejí jako soubor. pem, který obsahuje certifikát i privátní klíč, nebo jako soubor. CRT, který obsahuje certifikát a soubor. Key, který obsahuje soukromý klíč. Všechny soubory musí být ve formátu PEM. 

Pokud certifikát nainstalujete z Azure Key Vault pomocí [Správce prostředků šablony](./service-fabric-cluster-creation-create-template.md) nebo příkazů [PowerShellu](/powershell/module/az.servicefabric/?view=azps-2.6.0) , certifikát se nainstaluje do správného formátu v adresáři */var/lib/sfcerts* na každém uzlu. Pokud certifikát nainstalujete pomocí jiné metody, musíte se ujistit, že je certifikát správně nainstalovaný na uzlech clusteru.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certifikáty, na které se odkazuje v manifestu aplikace

Certifikáty, které jsou zadány v manifestu aplikace, například prostřednictvím prvků [**SecretsCertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) nebo [**EndpointCertificate**](./service-fabric-service-model-schema-elements.md#endpointcertificate-element) , musí být přítomny v adresáři */var/lib/sfcerts* . Prvky, které slouží k zadání certifikátů v manifestu aplikace, nevezmou atribut path, takže certifikáty musí být přítomny ve výchozím adresáři. Tyto prvky pobírají volitelný atribut **X509StoreName** . Výchozí hodnota je "my", která odkazuje na adresář */var/lib/sfcerts* v uzlech systému Linux. Jakákoli jiná hodnota není definována v clusteru se systémem Linux. Doporučujeme vynechat atribut **X509StoreName** pro aplikace, které běží na clusterech se systémem Linux. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certifikáty, na které se odkazuje v konfiguračním balíčku (Settings.xml)

U některých služeb můžete nakonfigurovat certifikáty X. 509 v [ConfigPackage](./service-fabric-application-and-service-manifests.md) (ve výchozím nastavení Settings.xml). Jedná se například o případ, kdy deklarujete certifikáty používané k zabezpečení kanálů RPC pro Reliable Services služby vytvořené pomocí sady Service Fabric .NET Core nebo Java SDK. Existují dva způsoby, jak odkazovat na certifikáty v konfiguračním balíčku. Podpora se liší v různých sadách SDK .NET Core a Java.

### <a name="using-x509-securitycredentialstype"></a>Použití x509 SecurityCredentialsType

Pomocí sad SDK pro .NET nebo Java můžete zadat **x509** pro **SecurityCredentialsType**. To odpovídá `X509Credentials` typu (.NET Java[.NET](/previous-versions/azure/reference/mt124925(v=azure.100)) / [Java](/java/api/system.fabric.x509credentials)) systému `SecurityCredentials` ([.NET](/previous-versions/azure/reference/mt124894(v=azure.100)) / [Java](/java/api/system.fabric.securitycredentials)).

Odkaz **x509** vyhledá certifikát v úložišti certifikátů. Následující kód XML ukazuje parametry používané k určení umístění certifikátu:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

U služby spuštěné v systému Linux **LocalMachine** / **mé** body do výchozího umístění pro certifikáty, adresář */var/lib/sfcerts* . Pro Linux nejsou definovány žádné další kombinace **CertificateStoreLocation** a **CertificateStoreName** . 

Pro parametr **CertificateStoreLocation** vždy zadejte **LocalMachine** . Není nutné zadat parametr **CertificateStoreName** , protože je nastaven na hodnotu My. S odkazem na **x509** se soubory certifikátů musí nacházet v adresáři */var/lib/sfcerts* na uzlu clusteru.  

Následující kód XML ukazuje oddíl **TransportSettings** na základě tohoto stylu:

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

### <a name="using-x509_2-securitycredentialstype"></a>Použití X509_2 SecurityCredentialsType

Pomocí sady Java SDK můžete zadat **X509_2** pro **SecurityCredentialsType**. To odpovídá typu (Java `X509Credentials2` )[Java](/java/api/system.fabric.x509credentials2) `SecurityCredentials` ([Java](/java/api/system.fabric.securitycredentials)). 

Pomocí odkazu na **X509_2** určíte parametr cesty, takže můžete najít certifikát v jiném adresáři než */var/lib/sfcerts*.  Následující kód XML ukazuje parametry používané k určení umístění certifikátu: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Následující kód XML ukazuje oddíl **TransportSettings** založený na tomto stylu.

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
> Certifikát je zadán jako soubor. CRT v předchozím kódu XML. To znamená, že existuje také soubor. Key obsahující privátní klíč ve stejném umístění.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Konfigurace aplikace Reliable Services pro spouštění v clusterech se systémem Linux

Sady SDK pro Service Fabric vám umožňují komunikovat s rozhraními API Service Fabric runtime, aby mohli využívat platformu. Když spustíte libovolnou aplikaci, která tuto funkci používá v systému zabezpečených clusterů systému Linux, je nutné aplikaci nakonfigurovat s certifikátem, který může použít k ověření pomocí modulu runtime Service Fabric. Tato konfigurace vyžaduje aplikace, které obsahují Service Fabric spolehlivých služeb služeb napsaných pomocí sad .NET Core nebo Java SDK. 

Chcete-li nakonfigurovat aplikaci, přidejte element [**SecretsCertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) pod značku **certifikáty** , který je umístěn pod značkou **souboru ApplicationManifest** v souboru *ApplicationManifest.xml* . Následující kód XML ukazuje certifikát, na který odkazuje jeho kryptografický otisk: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Můžete odkazovat buď na certifikát clusteru, nebo na certifikát, který nainstalujete na každý uzel clusteru. V systému Linux musí být soubory certifikátů přítomny v adresáři */var/lib/sfcerts* . Další informace najdete v tématu [umístění a formát certifikátů X. 509 na uzlech se systémem Linux](#location-and-format-of-x509-certificates-on-linux-nodes).
