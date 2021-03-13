---
title: Konfigurace zásad autorizace klíče obsahu pomocí REST – Azure | Microsoft Docs
description: Zjistěte, jak nakonfigurovat zásady autorizace pro klíč obsahu pomocí REST API Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7af5f9e2-8ed8-43f2-843b-580ce8759fd4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 66649eb6109339cd7723ab4a7e3a12c5c322973d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017286"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Dynamické šifrování: Konfigurace zásad autorizace klíče obsahu

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Přehled
 Můžete použít Azure Media Services k doručování obsahu (dynamicky) pomocí standard AES (Advanced Encryption Standard) (AES) pomocí 128 bitových šifrovacích klíčů a technologie PlayReady nebo Widevine DRM (Správa digitálních práv). Media Services taky poskytuje službu pro doručování klíčů a licencí PlayReady/Widevine autorizovaným klientům.

Pokud chcete, Media Services k šifrování assetu, musíte k assetu přidružit šifrovací klíč (CommonEncryption nebo EnvelopeEncryption). Další informace najdete v tématu [vytvoření klíčů obsahu pomocí REST](media-services-rest-create-contentkey.md). Je také potřeba nakonfigurovat zásady autorizace pro klíč (jak je popsáno v tomto článku).

Když hráč vyžádá datový proud, Media Services použije zadaný klíč k dynamickému šifrování vašeho obsahu pomocí šifrování AES nebo PlayReady. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. Aby bylo možné zjistit, zda je uživatel autorizován pro získání klíče, služba vyhodnotí zásady autorizace, které jste zadali pro klíč.

Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíč. Zásady autorizace klíče obsahu můžou mít jedno nebo několik autorizačních omezení pomocí omezení Open nebo token. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Media Services podporuje tokeny ve formátech jednoduchých webových tokenů ([SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)) a JSON web token (Jwt).

Media Services neposkytuje službu STS. Můžete vytvořit vlastní STS nebo použít Azure Active Directory (Azure AD) k vydávání tokenů. Služba STS musí být nakonfigurovaná tak, aby vytvořila token podepsaný pomocí zadaného klíče a vydávala deklarace identity, které jste zadali v konfiguraci omezení tokenu (jak je popsáno v tomto článku). Pokud je token platný a deklarace identity v tokenu odpovídají nastavením nakonfigurovaným pro klíč obsahu, služba doručování Media Services Key vrátí šifrovací klíč klientovi.

Další informace najdete v následujících článcích:
- [Ověřování tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrace aplikace založené na Azure Media Services OWIN MVC s Azure Active Directory a omezení doručování klíčů obsahu na základě deklarací JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Platí některé předpoklady.
* Pokud chcete použít dynamické balení a dynamické šifrování, ujistěte se, že koncový bod streamování, ze kterého chcete streamovat obsah, je ve stavu spuštěno.
* Vaše Asset musí obsahovat sadu adaptivních přenosů rychlostmi nebo souborů s adaptivní přenosovou rychlostí Smooth Streaming. Další informace najdete v tématu [kódování assetu](media-services-encode-asset.md).
* Nahrajte a zakódujte své prostředky pomocí možnosti AssetCreationOptions. StorageEncrypted.
* Pokud máte v úmyslu mít více klíčů obsahu, které vyžadují stejnou konfiguraci zásad, doporučujeme vytvořit jednu zásadu autorizace a znovu ji použít s více klíči obsahu.
* Služba doručení klíčů ukládá do mezipaměti ContentKeyAuthorizationPolicy a související objekty (možnosti zásad a omezení) po dobu 15 minut. Můžete vytvořit ContentKeyAuthorizationPolicy a určit, že se má použít omezení tokenu, otestovat ho a pak aktualizovat zásadu na otevřené omezení. Tento proces trvá zhruba 15 minut, než se zásada přepne na otevřenou verzi zásady.
* Pokud přidáte nebo aktualizujete zásady pro doručení prostředku, musíte odstranit stávající lokátor a vytvořit nový.
* V současné době nemůžete šifrovat progresivní stahování.
* Koncový bod streamování Media Services nastaví hodnotu hlavičky Access-Control-Allow-Origin v odpovědi na kontrolu před výstupem jako zástupný znak " \* .". Tato hodnota dobře funguje u většiny hráčů, včetně Azure Media Player, roku a JWPlayer a dalších. Někteří hráči, kteří používají dash.js nefungují, protože s režimem přihlašovacích údajů nastaveným na include, XMLHttpRequest v jejich dash.js nepovoluje zástupný znak \* "" jako hodnotu možnosti Access-Control-Allow-Origin. V případě tohoto omezení v dash.js se jedná o alternativní řešení, pokud klienta hostuje z jedné domény, Media Services může tuto doménu v hlavičce odpovědi na předběžné služby zadat. Pokud potřebujete pomoc, otevřete lístek podpory prostřednictvím Azure Portal.

## <a name="aes-128-dynamic-encryption"></a>Dynamické šifrování AES-128
> [!NOTE]
> Při práci s Media Services REST API platí následující požadavky.
> 
> Při přístupu k entitám v Media Services musíte nastavit konkrétní pole a hodnoty hlaviček v požadavcích HTTP. Další informace najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md).
> 
> 
> 

### <a name="open-restriction"></a>Otevřené omezení
Otevřené omezení znamená, že systém přidává klíč každému uživateli, který posílá požadavek na klíč. Toto omezení může být užitečné pro účely testování.

Následující příklad vytvoří otevřené zásady autorizace a přidá je do klíče obsahu.

#### <a name="create-contentkeyauthorizationpolicies"></a><a id="ContentKeyAuthorizationPolicies"></a>Vytvořit ContentKeyAuthorizationPolicies
Požadavek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 36

{"Name":"Open Authorization Policy"}
```

Odpověď:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 211
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Adb4593da-f4d1-4cc5-a92a-d20eacbabee4')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
request-id: aabfa731-e884-4bf3-8314-492b04747ac4
x-ms-request-id: aabfa731-e884-4bf3-8314-492b04747ac4
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 08:25:56 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:db4593da-f4d1-4cc5-a92a-d20eacbabee4","Name":"Open Authorization Policy"}
```

#### <a name="create-contentkeyauthorizationpolicyoptions"></a><a id="ContentKeyAuthorizationPolicyOptions"></a>Vytvořit ContentKeyAuthorizationPolicyOptions
Požadavek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 168

{"Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}
```

Odpověď:    

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 349
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
x-ms-request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 08:56:40 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:57829b17-1101-4797-919b-f816f4a007b7","Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a><a id="LinkContentKeyAuthorizationPoliciesWithOptions"></a>Propojit ContentKeyAuthorizationPolicies s možnostmi
Požadavek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3A0baa438b-8ac2-4c40-a53c-4d4722b78715')/$links/Options HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 9847f705-f2ca-4e95-a478-8f823dbbaa29
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 154

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')"}
```

Odpověď:

```output
HTTP/1.1 204 No Content
```

#### <a name="add-an-authorization-policy-to-the-content-key"></a><a id="AddAuthorizationPolicyToKey"></a>Přidání autorizačních zásad k klíči obsahu
Požadavek:

```console
PUT https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A2e6d36a7-a17c-4e9a-830d-eca23ad1a6f9') HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: e613efff-cb6a-41b4-984a-f4f8fb6e76a4
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 78

{"AuthorizationPolicyId":"nb:ckpid:UUID:c06cebb8-c4f0-4d1a-ba00-3273fb2bc3ad"}
```

Odpověď:

```output
HTTP/1.1 204 No Content
```

### <a name="token-restriction"></a>Omezení tokenu
Tato část popisuje, jak vytvořit zásady autorizace klíčů obsahu a přidružit ji k klíči obsahu. Zásady autorizace popisují, jaké autorizační požadavky musí být splněné, aby bylo možné zjistit, jestli má uživatel oprávnění k přijetí klíče. Například seznam ověřovacích klíčů obsahuje klíč, pomocí kterého byl token podepsán?

Chcete-li nakonfigurovat možnost omezení tokenu, je nutné použít XML k popisu požadavků na autorizaci tokenu. XML pro konfiguraci omezení tokenů musí odpovídat následujícímu schématu XML:


#### <a name="token-restriction-schema"></a><a id="schema"></a>Schéma omezení tokenu
```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
  <xs:complexType name="TokenClaim">
    <xs:sequence>
      <xs:element name="ClaimType" nillable="true" type="xs:string" />
      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
  <xs:complexType name="TokenRestrictionTemplate">
    <xs:sequence>
      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
  <xs:complexType name="ArrayOfTokenVerificationKey">
    <xs:sequence>
      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
  <xs:complexType name="TokenVerificationKey">
    <xs:sequence />
  </xs:complexType>
  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
  <xs:complexType name="ArrayOfTokenClaim">
    <xs:sequence>
      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
  <xs:complexType name="SymmetricVerificationKey">
    <xs:complexContent mixed="false">
      <xs:extension base="tns:TokenVerificationKey">
        <xs:sequence>
          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
</xs:schema>
```

Když konfigurujete zásady s omezením tokenu, musíte zadat primární ověřovací klíč, Vystavitel a parametry cílové skupiny. Primární ověřovací klíč obsahuje klíč, pomocí kterého byl token podepsán. Vystavitel je STS, který vydává token. Cílová skupina (někdy označovaná jako obor) popisuje účel tokenu nebo prostředku, ke kterému token opravňuje přístup. Služba doručení Media Services Key ověřuje, že se tyto hodnoty v tokenu shodují s hodnotami v šabloně.

Následující příklad vytvoří zásadu autorizace s omezením tokenu. V tomto příkladu musí klient předložit token, který obsahuje podpisový klíč (VerificationKey), Vystavitel tokenu a požadované deklarace identity.

### <a name="create-contentkeyauthorizationpolicies"></a>Vytvořit ContentKeyAuthorizationPolicies
Vytvořte zásady omezení tokenů, jak je znázorněno v části[Vytvoření ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies).

### <a name="create-contentkeyauthorizationpolicyoptions"></a>Vytvořit ContentKeyAuthorizationPolicyOptions
Požadavek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 1079

{"Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

Odpověď:    

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1260
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae1ef6145-46e8-4ee6-9756-b1cf96328c23')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
x-ms-request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:10:37 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e1ef6145-46e8-4ee6-9756-b1cf96328c23","Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>Propojit ContentKeyAuthorizationPolicies s možnostmi
Propojte ContentKeyAuthorizationPolicies s možnostmi, jak je znázorněno v oddílu "[Vytvoření ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies)".

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Přidání autorizačních zásad k klíči obsahu
Přidejte AuthorizationPolicy do ContentKey, jak je znázorněno v části[Přidání zásady autorizace do klíče obsahu](#AddAuthorizationPolicyToKey).

## <a name="playready-dynamic-encryption"></a>Dynamické šifrování PlayReady
Pomocí Media Services můžete nakonfigurovat práva a omezení, která má modul runtime PlayReady DRM vyhovět, když se uživatel pokusí přehrát chráněný obsah. 

Když chráníte obsah pomocí PlayReady, je jedním z věcí, které potřebujete zadat v zásadách autorizace, řetězec XML, který definuje [šablonu licence PlayReady](media-services-playready-license-template-overview.md). 

### <a name="open-restriction"></a>Otevřené omezení
Otevřené omezení znamená, že systém přidává klíč každému uživateli, který posílá požadavek na klíč. Toto omezení může být užitečné pro účely testování.

Následující příklad vytvoří otevřené zásady autorizace a přidá je do klíče obsahu.

#### <a name="create-contentkeyauthorizationpolicies"></a><a id="ContentKeyAuthorizationPolicies2"></a>Vytvořit ContentKeyAuthorizationPolicies
Požadavek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 58

{"Name":"Deliver Common Content Key"}
```

Odpověď:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 233
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Acc3c64a8-e2fc-4e09-bf60-ac954251a387')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
x-ms-request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:26:00 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:cc3c64a8-e2fc-4e09-bf60-ac954251a387","Name":"Deliver Common Content Key"}
```


#### <a name="create-contentkeyauthorizationpolicyoptions"></a>Vytvořit ContentKeyAuthorizationPolicyOptions
Požadavek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 593

{"Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
```

Odpověď:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 774
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A1052308c-4df7-4fdb-8d21-4d2141fc2be0')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
x-ms-request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:23:24 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:1052308c-4df7-4fdb-8d21-4d2141fc2be0","Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>Propojit ContentKeyAuthorizationPolicies s možnostmi
Propojte ContentKeyAuthorizationPolicies s možnostmi, jak je znázorněno v oddílu "[Vytvoření ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies)".

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Přidání autorizačních zásad k klíči obsahu
Přidejte AuthorizationPolicy do ContentKey, jak je znázorněno v části[Přidání zásady autorizace do klíče obsahu](#AddAuthorizationPolicyToKey).

### <a name="token-restriction"></a>Omezení tokenu
Chcete-li nakonfigurovat možnost omezení tokenu, je nutné použít XML k popisu požadavků na autorizaci tokenu. XML pro konfiguraci omezení tokenů musí odpovídat schématu XML uvedenému v oddílu "[schéma omezení tokenu](#schema)".

#### <a name="create-contentkeyauthorizationpolicies"></a>Vytvořit ContentKeyAuthorizationPolicies
Vytvořte ContentKeyAuthorizationPolicies, jak je znázorněno v oddílu "[Vytvoření ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies2)".

#### <a name="create-contentkeyauthorizationpolicyoptions"></a>Vytvořit ContentKeyAuthorizationPolicyOptions
Požadavek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 1525

{"Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

Odpověď:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1706
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae42bbeae-de42-4077-90e9-a844f297ef70')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
request-id: ccf8a4ba-731e-4124-8192-079592c251cc
x-ms-request-id: ccf8a4ba-731e-4124-8192-079592c251cc
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:58:47 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e42bbeae-de42-4077-90e9-a844f297ef70","Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>Propojit ContentKeyAuthorizationPolicies s možnostmi
Propojte ContentKeyAuthorizationPolicies s možnostmi, jak je znázorněno v oddílu "[Vytvoření ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies)".

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Přidání autorizačních zásad k klíči obsahu
Přidejte AuthorizationPolicy do ContentKey, jak je znázorněno v části[Přidání zásady autorizace do klíče obsahu](#AddAuthorizationPolicyToKey).

## <a name="types-used-when-you-define-contentkeyauthorizationpolicy"></a><a id="types"></a>Typy používané při definování ContentKeyAuthorizationPolicy
### <a name="contentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
```csharp
public enum ContentKeyRestrictionType
{
    Open = 0,
    TokenRestricted = 1, 
    IPRestricted = 2, // IP restriction on content key is not currently supported, reserved for future.
}
```


> [!NOTE]
> V této službě ještě není k dispozici omezení IP adres pro zásady autorizace klíče obsahu.


### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
```csharp
public enum ContentKeyDeliveryType
{
    None = 0,
    PlayReadyLicense = 1,
    BaselineHttp = 2,
    Widevine = 3
}
```

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
Teď, když jste nakonfigurovali zásady autorizace klíče obsahu, najdete informace v tématu [Konfigurace zásad doručení assetu](media-services-rest-configure-asset-delivery-policy.md).
