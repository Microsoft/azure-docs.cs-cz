---
title: Konfigurace certifikátů pro aplikace v Linuxu
description: Konfigurace certifikátů pro vaši aplikaci pomocí runtime Service Fabric v clusteru Linux
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: 802e76614f51e1f6479a311e61a49d83b8125546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282572"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certifikáty a zabezpečení na linuxových clusterech

Tento článek obsahuje informace o konfiguraci certifikátů X.509 v clusterech Linux.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Umístění a formát certifikátů X.509 na linuxových uzlech

Service Fabric obecně očekává, že certifikáty X.509 budou k dispozici v adresáři */var/lib/sfcerts* v uzlech clusteru Linux. To platí pro clusterové certifikáty, klientské certifikáty atd. V některých případech můžete zadat jiné umístění než složku *var/lib/sfcerts* pro certifikáty. Například se spolehlivými službami vytvořenými pomocí sady Service Fabric Java SDK můžete pro některé certifikáty specifické pro aplikaci zadat jiné umístění prostřednictvím konfiguračního balíčku (Settings.xml). Další informace naleznete [v tématu Certifikáty odkazované v konfiguračním balíčku (Settings.xml).](#certificates-referenced-in-the-configuration-package-settingsxml)

U clusterů S IP očekává Service Fabric, že certifikáty budou k dispozici jako soubor PEM, který obsahuje certifikát i soukromý klíč, nebo jako soubor CRT, který obsahuje certifikát a soubor .key, který obsahuje soukromý klíč. Všechny soubory by měly být ve formátu PEM. 

Pokud nainstalujete certifikát z trezoru klíčů Azure pomocí [šablony Správce prostředků](./service-fabric-cluster-creation-create-template.md) nebo příkazů prostředí [PowerShell,](https://docs.microsoft.com/powershell/module/az.servicefabric/?view=azps-2.6.0) certifikát se nainstaluje ve správném formátu v adresáři */var/lib/sfcerts* v každém uzlu. Pokud nainstalujete certifikát jinou metodou, musíte se ujistit, že je certifikát správně nainstalován v uzlech clusteru.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certifikáty uvedené v manifestu žádosti

Certifikáty zadané v manifestu aplikace, například prostřednictvím prvků [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) nebo [**EndpointCertificate,**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element) musí být k dispozici v adresáři */var/lib/sfcerts.* Prvky, které se používají k určení certifikátů v manifestu aplikace, neberou atribut path, takže certifikáty musí být přítomny ve výchozím adresáři. Tyto prvky převzít volitelný **X509StoreName** atribut. Výchozí hodnota je "My", která odkazuje na adresář */var/lib/sfcerts* na linuxových uzlech. Jakákoli jiná hodnota není v clusteru Linux definována. Doporučujeme vynechat atribut **X509StoreName** pro aplikace, které běží na linuxových clusterech. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certifikáty odkazované v konfiguračním balíčku (Settings.xml)

U některých služeb můžete nakonfigurovat certifikáty X.509 v [balíčku ConfigPackage](./service-fabric-application-and-service-manifests.md) (ve výchozím nastavení Soubor Settings.xml). Například to je případ, kdy deklarujete certifikáty používané k zabezpečení kanálů RPC pro služby spolehlivé služby vytvořené pomocí sad Service Fabric .NET Core nebo Java SDK. Existují dva způsoby, jak odkazovat na certifikáty v konfiguračním balíčku. Podpora se liší mezi sadami .NET Core a Java SDK.

### <a name="using-x509-securitycredentialstype"></a>Použití typu Zabezpečení X509

WIth .NET nebo Java SDKs, můžete zadat **X509** pro **SecurityCredentialsType**. To odpovídá typu `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)Java `SecurityCredentials` ) ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)).

Odkaz **X509** vyhledá certifikát v úložišti certifikátů. Následující jazyk XML zobrazuje parametry použité k určení umístění certifikátu:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Pro službu spuštěnou na Linuxu **localmachine**/**My** odkazuje na výchozí umístění certifikátů, adresář */var/lib/sfcerts.* Pro Linux nejsou definovány žádné jiné kombinace **CertificateStoreLocation** a **CertificateStoreName.** 

Vždy zadejte **LocalMachine** pro **parametr CertificateStoreLocation.** Není nutné zadávat parametr **CertificateStoreName,** protože je výchozí na "My". S odkazem **X509** musí být soubory certifikátů umístěny v adresáři */var/lib/sfcerts* v uzlu clusteru.  

Následující jazyk XML zobrazuje oddíl **TransportSettings** založený na tomto stylu:

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

Pomocí sady Java SDK můžete zadat **X509_2** pro **securitycredentialsType**. To `X509Credentials2` odpovídá typu ([Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2) `SecurityCredentials` ) ([Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)). 

Pomocí **X509_2** odkazu zadáte parametr cesty, takže můžete certifikát vyhledat v jiném adresáři než */var/lib/sfcerts*.  Následující jazyk XML zobrazuje parametry použité k určení umístění certifikátu: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Následující jazyk XML zobrazuje oddíl **TransportSettings** založený na tomto stylu.

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
> Certifikát je určen jako soubor CRT v předchozím xml. To znamená, že je také soubor .key obsahující soukromý klíč ve stejném umístění.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Konfigurace aplikace Spolehlivé služby pro spuštění v clusterech Linuxu

Sady Service Fabric SDK umožňují komunikovat s runtime API Service Fabric využít platformu. Při spuštění libovolné aplikace, která používá tuto funkci v zabezpečených clusterech Linux, je třeba nakonfigurovat aplikaci s certifikátem, který lze použít k ověření pomocí runtime Service Fabric. Aplikace, které obsahují služby Service Fabric Reliable Service služby napsané pomocí sad SDK .NET Core nebo Java, vyžadují tuto konfiguraci. 

Chcete-li nakonfigurovat aplikaci, přidejte element [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) pod značku **Certificates,** která je umístěna pod značkou **ApplicationManifest** v souboru *ApplicationManifest.xml.* Následující kód XML zobrazuje certifikát odkazovaný jeho kryptografickým otiskem: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Můžete odkazovat na certifikát clusteru nebo na certifikát, který nainstalujete do každého uzlu clusteru. V Linuxu musí být soubory certifikátů přítomny v adresáři */var/lib/sfcerts.* Další informace najdete [v tématu Umístění a formát certifikátů X.509 v linuxových uzlech](#location-and-format-of-x509-certificates-on-linux-nodes).



