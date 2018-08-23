---
title: Konfigurace certifikátů pro aplikace Azure Service Fabric v Linuxu | Dokumentace Microsoftu
description: Konfigurace certifikátů pro vaši aplikaci pomocí modulu runtime Service Fabric v clusteru s Linuxem
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
ms.openlocfilehash: 97f33a1c0c42b534dafd1e4ed378b655b339395a
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42059447"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certifikáty a zabezpečení na clusterech s Linuxem

Tento článek obsahuje informace o konfiguraci certifikátů X.509 na clusterech s Linuxem.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Umístění a formátu X.509 certifikáty na uzly s Linuxem

Service Fabric obecně očekává, že certifikáty X.509 nacházet v */var/lib/sfcerts* adresáře na uzlech clusteru s Linuxem. To platí pro certifikáty clusteru, klientské certifikáty, atd. V některých případech můžete zadat umístění jiné než *var/lib/sfcerts* složku pro certifikáty. Například s využitím Reliable Services vytvořených pomocí Service Fabric Java SDK, můžete zadat jiné umístění prostřednictvím konfiguračního balíčku (Settings.xml) pro některé certifikáty specifické pro aplikaci. Další informace najdete v tématu [certifikáty odkazovat v balíčku pro konfiguraci (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

U Linuxových clusterů Service Fabric očekává, že certifikáty bude k dispozici jako buď soubor .pem, který obsahuje certifikát s privátním klíčem, nebo jako, který obsahuje certifikát soubor .crt a .key soubor, který obsahuje privátní klíč. Všechny soubory musí být ve formátu PEM. 

Při instalaci certifikátu ze služby Azure Key Vault pomocí [šablony Resource Manageru](./service-fabric-cluster-creation-create-template.md) nebo [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric) příkazy, certifikát nainstalovaný ve správném formátu v */var/ lib/sfcerts* adresáře v každém uzlu. Pokud nainstalujete certifikát prostřednictvím jinou metodu, musí Ujistěte se, že certifikát správně nainstalován na uzlech clusteru.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certifikáty se odkazuje v manifestu aplikace

V aplikaci se zadal manifestu, například prostřednictvím [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) nebo [ **EndpointCertificate** ](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element)prvky, musí být součástí */var/lib/sfcerts* adresáře. Prvky, které se používají k určení certifikáty v manifestu aplikace nepřebírají atribut path, aby certifikáty musí být k dispozici ve výchozím adresáři. Tyto prvky provést volitelný **X509StoreName** atribut. Výchozí hodnota je "My", která odkazuje na */var/lib/sfcerts* adresáře na uzly s Linuxem. Jakákoli jiná hodnota není definovaná pro cluster s Linuxem. Doporučujeme vám, že vynecháte **X509StoreName** atribut pro aplikace, které poběží na clusterech s Linuxem. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certifikáty se odkazuje v balíčku pro konfiguraci (Settings.xml)

U některých služeb můžete nakonfigurovat certifikáty X.509 v [složce ConfigPackage](./service-fabric-application-and-service-manifests.md) (ve výchozím nastavení Settings.xml). Například to platí při deklaraci certifikáty používané k zabezpečení vzdáleného volání Procedur kanály pro spolehlivé služby vytvořené pomocí sady Java SDK nebo .NET Core v Service Fabric. Existují dva způsoby, jak certifikáty odkaz v balíčku konfigurace. Podpora se pohybuje mezi .NET Core a sady Java SDK.

### <a name="using-x509-securitycredentialstype"></a>Pomocí X509 SecurityCredentialsType

.NET nebo sady Java SDK, můžete zadat **X509** pro **SecurityCredentialsType**. To odpovídá `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric._x509_credentials)) typ `SecurityCredentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric._security_credentials)).

**X509** odkaz vyhledá certifikát v úložišti certifikátů. Následující kód XML ukazuje parametrů použitých k zadání umístění certifikátu:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Pro služby spuštěné v Linuxu **LocalMachine**/**Moje** odkazuje na výchozí umístění pro certifikáty, */var/lib/sfcerts* adresáře. Pro Linux, žádné jiné kombinace **CertificateStoreLocation** a **Název_úložiště_certifikátů** nejsou definovány. 

Vždy zadávat **LocalMachine** pro **CertificateStoreLocation** parametru. Není potřeba specifikovat **Název_úložiště_certifikátů** parametr vzhledem k tomu, použije se výchozí "Moje". Pomocí **X509** odkaz, soubory certifikátu se musí nacházet v */var/lib/sfcerts* adresáře na uzlu clusteru.  

Zobrazí se následující XML **TransportSettings** části podle tento styl:

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

Pomocí **X509_2** odkaz, zadáte parametr cesty, aby certifikát můžete vyhledat v adresáři jiné než */var/lib/sfcerts*.  Následující kód XML ukazuje parametrů použitých k zadání umístění certifikátu: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Zobrazí se následující XML **TransportSettings** části podle tímto stylem.

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
> Certifikát je zadat jako soubor CRT v předchozím kódu XML. Z toho vyplývá, že je také .key soubor obsahující privátní klíč ve stejném umístění.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Konfigurace aplikace Reliable Services ke spuštění na clusterech s Linuxem

Service Fabric SDK vám umožňují komunikovat s modulem runtime Service Fabric rozhraní API pro využití platformy. Když spustíte všechny aplikace používající tuto funkci na zabezpečené clustery s Linuxem, budete muset nakonfigurovat svoji aplikaci se certifikát, který můžete použít k ověření pomocí modulu runtime Service Fabric. Aplikace, které obsahují služby Service Fabric Reliable Service napsané pomocí sady Java SDK nebo .NET Core vyžaduje tuto konfiguraci. 

Pro konfiguraci aplikace, přidejte [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) element v rámci **certifikáty** značku, která je umístěna ve složce **ApplicationManifest**  značku *ApplicationManifest.xml* souboru. Následující kód XML ukazuje odkazuje jeho kryptografický otisk certifikátu: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Můžete odkazovat na certifikát clusteru nebo certifikát, který nainstalujete na každý uzel clusteru. V Linuxu, soubory certifikátu musí být součástí */var/lib/sfcerts* adresáře. Další informace najdete v tématu [umístění a formátu X.509 certifikáty na uzly s Linuxem](#location-and-format-of-x509-certificates-on-linux-nodes).



