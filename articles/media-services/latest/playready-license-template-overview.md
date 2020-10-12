---
title: Šablona licence Microsoft PlayReady pro Azure Media Services V3
description: Přečtěte si o Azure Media Services V3 pomocí šablony licence PlayReady a o tom, jak nakonfigurovat licence PlayReady.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a4b694ca449390e5b2f282f1361f0a455ba5750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531436"
---
# <a name="media-services-v3-with-playready-license-template"></a>Media Services V3 se šablonou licence PlayReady

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services vám umožní šifrovat obsah pomocí **Microsoft PlayReady**. Media Services také poskytuje službu pro doručování licencí PlayReady. K nakonfigurování licencí PlayReady můžete použít rozhraní Media Services API. Když se hráč pokusí přehrát obsah chráněný pomocí PlayReady, pošle se mu požadavek na doručení licence. Pokud licenční služba žádost schválí, vydá licenci, která je odeslána klientovi a slouží k dešifrování a přehrání zadaného obsahu.

Licence PlayReady obsahují práva a omezení, která má modul runtime PlayReady DRM (Správa digitálních práv) vyhovět, když se uživatel pokusí přehrát chráněný obsah. Tady jsou některé příklady licenčních omezení PlayReady, které můžete zadat:

* Datum a čas, od kterého je licence platná.
* Hodnota DateTime po vypršení platnosti licence 
* Pro uložení licence do trvalého úložiště na klientovi. Trvalé licence se obvykle používají k povolení přehrávání obsahu v režimu offline.
* Minimální úroveň zabezpečení, kterou přehrávač musí mít, aby mohl přehrát váš obsah. 
* Úroveň ochrany výstupu pro ovládací prvky výstupu pro audio\video obsah. 
* Další informace najdete v oddílu "ovládací prvky výstupu" (3,5) v dokumentu [pravidla dodržování předpisů PlayReady](https://www.microsoft.com/playready/licensing/compliance/) .

> [!NOTE]
> V současné době můžete nakonfigurovat jenom PlayRight licence PlayReady. Toto právo je povinné. PlayRight dává klientovi možnost přehrávání obsahu. Můžete také použít PlayRight ke konfiguraci omezení určených pro přehrávání. 
> 

Toto téma popisuje, jak nakonfigurovat licence PlayReady pomocí Media Services.

## <a name="basic-streaming-license-example"></a>Příklad licence na základní streamování

Následující příklad ukazuje nejjednodušší (nejběžnější) šablonu, která konfiguruje základní licenci pro streamování. V rámci této licence můžou klienti přehrávat obsah chráněný pomocí PlayReady. 

XML odpovídá schématu XML šablony licence PlayReady definované v oddílu [schéma XML šablony licence PlayReady](#schema) .

```xml
<?xml version="1.0" encoding="utf-8"?>
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" 
                                  xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
    <LicenseTemplates>
    <PlayReadyLicenseTemplate>
        <ContentKey i:type="ContentEncryptionKeyFromHeader" />
        <PlayRight />
    </PlayReadyLicenseTemplate>
    </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

## <a name="use-media-services-apis-to-configure-license-templates"></a><a id="classes"></a>Použití rozhraní API pro Media Services ke konfiguraci šablon licencí

Media Services poskytuje typy, které můžete použít ke konfiguraci šablony licencí PlayReady. 

Následující fragment kódu používá Media Services třídy .NET ke konfiguraci šablony licence PlayReady. Třídy jsou definovány v oboru názvů [Microsoft. Azure. Management. Media. Models](/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) . Fragment kódu konfiguruje PlayRight licence PlayReady. PlayRight uděluje uživateli možnost přehrávání obsahu v závislosti na všech omezeních nakonfigurovaných v licenci a na PlayRight samotného (pro zásady specifické pro přehrávání). Většina zásad na PlayRight se týká omezení výstupu, které řídí typy výstupů, přes které lze obsah přehrát. Zahrnuje také omezení, která musí být zavedena při použití daného výstupu. Pokud je například povoleno DigitalVideoOnlyContentRestriction, modul runtime DRM umožňuje zobrazit pouze video přes digitální výstupy. (Výstupy analogového videa neumožňují předání obsahu.)

> [!IMPORTANT]
> Licence PlayReady má efektivní omezení. Pokud jsou ochrana výstupu příliš omezující, může být obsah pro některé klienty nezobrazitelný. Další informace najdete v článku [pravidla dodržování předpisů PlayReady](https://www.microsoft.com/playready/licensing/compliance/).

### <a name="configure-playready-license-template-with-net"></a>Konfigurace šablony licencí PlayReady pomocí .NET

```csharp
ContentKeyPolicyPlayReadyLicense objContentKeyPolicyPlayReadyLicense;
objContentKeyPolicyPlayReadyLicense = new ContentKeyPolicyPlayReadyLicense
{
    AllowTestDevices = true,
    BeginDate = new DateTime(2016, 1, 1),
    ContentKeyLocation = new ContentKeyPolicyPlayReadyContentEncryptionKeyFromHeader(),
    ContentType = ContentKeyPolicyPlayReadyContentType.UltraVioletStreaming,
    LicenseType = drmSettings.EnableOfflineMode ? ContentKeyPolicyPlayReadyLicenseType.Persistent : ContentKeyPolicyPlayReadyLicenseType.NonPersistent,
    PlayRight = new ContentKeyPolicyPlayReadyPlayRight
    {
        ImageConstraintForAnalogComponentVideoRestriction = true,
        ExplicitAnalogTelevisionOutputRestriction = new ContentKeyPolicyPlayReadyExplicitAnalogTelevisionRestriction(true, 2),
        AllowPassingVideoContentToUnknownOutput = ContentKeyPolicyPlayReadyUnknownOutputPassingOption.Allowed,
        FirstPlayExpiration = TimeSpan.FromSeconds(20.0),
    }
};
```

## <a name="playready-license-template-xml-schema"></a><a id="schema"></a>Schéma XML šablony licence PlayReady

```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
    <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
    <xs:complexType name="AgcAndColorStripeRestriction">
        <xs:sequence>
            <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
    <xs:simpleType name="ContentType">
        <xs:restriction base="xs:string">
            <xs:enumeration value="Unspecified" />
            <xs:enumeration value="UltravioletDownload" />
            <xs:enumeration value="UltravioletStreaming" />
        </xs:restriction>
    </xs:simpleType>
    <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
    <xs:complexType name="ExplicitAnalogTelevisionRestriction">
        <xs:sequence>
            <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
            <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
    <xs:complexType name="PlayReadyContentKey">
        <xs:sequence />
    </xs:complexType>
    <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
    <xs:complexType name="ContentEncryptionKeyFromHeader">
        <xs:complexContent mixed="false">
            <xs:extension base="tns:PlayReadyContentKey">
                <xs:sequence />
            </xs:extension>
        </xs:complexContent>
    </xs:complexType>
    <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
    <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
        <xs:complexContent mixed="false">
            <xs:extension base="tns:PlayReadyContentKey">
                <xs:sequence>
                    <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
                </xs:sequence>
            </xs:extension>
        </xs:complexContent>
    </xs:complexType>
    <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
    <xs:complexType name="PlayReadyLicenseResponseTemplate">
        <xs:sequence>
            <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
            <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
        </xs:sequence>
    </xs:complexType>
    <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
    <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
        <xs:sequence>
            <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
    <xs:complexType name="PlayReadyLicenseTemplate">
        <xs:sequence>
            <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
            <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
            <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
            <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
    <xs:simpleType name="PlayReadyLicenseType">
        <xs:restriction base="xs:string">
            <xs:enumeration value="Nonpersistent" />
            <xs:enumeration value="Persistent" />
        </xs:restriction>
    </xs:simpleType>
    <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
    <xs:complexType name="PlayReadyPlayRight">
        <xs:sequence>
            <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
        </xs:sequence>
    </xs:complexType>
    <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
    <xs:simpleType name="UnknownOutputPassingOption">
        <xs:restriction base="xs:string">
            <xs:enumeration value="NotAllowed" />
            <xs:enumeration value="Allowed" />
            <xs:enumeration value="AllowedWithVideoConstriction" />
        </xs:restriction>
    </xs:simpleType>
    <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
    <xs:complexType name="ScmsRestriction">
        <xs:sequence>
            <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
</xs:schema>
```

## <a name="next-steps"></a>Další kroky

Podívejte se, jak [chránit pomocí DRM](protect-with-drm.md)
