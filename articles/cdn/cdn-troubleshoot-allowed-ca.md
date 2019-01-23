---
title: Povolené certifikačních autorit pro povolení vlastní protokol HTTPS ve službě Azure CDN | Dokumentace Microsoftu
description: Pokud používáte vlastní certifikát pro povolení HTTPS pro vlastní doménu, musíte použít povolené certifikační autority (CA) k jeho vytvoření.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 11651c2721756a4f750a5a5e78f86fdbd363fb9d
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462585"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Pro povolení vlastní protokol HTTPS ve službě Azure CDN povolená certifikační autority

Pro vlastní doménu Azure Content Delivery Network (CDN) na **Azure CDN Standard od společnosti Microsoft** koncový bod, když jste [povolení funkce HTTPS s použitím vlastní certifikát](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), je nutné použít povolený certifikační autorita (CA) k vytvoření certifikátu SSL. Jinak Pokud používáte bez povolené certifikační Autority nebo certifikát podepsaný svým držitelem, bude požadavek zamítnut.

> [!NOTE]
> Možnost používat vlastní certifikát pro povolení HTTPS pro vlastní je k dispozici pouze s **Azure CDN Standard od společnosti Microsoft** profily. 
>

Následující certifikační autority jsou povoleny v případě, že vytvoříte vlastní certifikát:

- Kořenové AddTrust externí certifikační Autority
- AlphaSSL Root CA
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- APCA-DM3P
- AutoPilot kořenové certifikační Autority
- Baltimore CyberTrust Root
- Třída 3 veřejný primárního certifikační autority
- COMODO RSA certifikační autority
- COMODO RSA domény ověření zabezpečeného serveru certifikační Autority
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert globální kořenové certifikační Autority
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV Root CA
- DigiCert SHA2 rozšířené ověřování serveru certifikační Autority
- DigiCert SHA2 vysokou zárukou Server certifikační Autority
- DigiCert SHA2 zabezpečeného serveru certifikační Autority
- Kořenové certifikační Autority X3 letního času
- D-trust Root Class 3 CA 2 2009
- Všude šifrování TLS DV certifikační Autority
- Kořenové certifikační autority Entrust
- Entrust kořenové certifikační autority - G2
- Entrust.NET certifikační autority (2048)
- GeoTrust Global CA
- Primární GeoTrust certifikační autority
- Primární GeoTrust certifikační autorita – G2
- Geotrust RSA CA 2018
- GlobalSign
- GlobalSign rozšířené G2 CA - SHA256 – ověření
- Ověřování organizace GlobalSign CA - G2
- GlobalSign Root CA
- Go Daddy kořenová certifikační autorita – G2
- Go Daddy zabezpečení certifikační autority - G2
- G3 CA2 QuoVadis kořenové
- RapidSSL RSA CA 2018
- Třída Symantec 3 EV SSL CA - G3
- Třída Symantec 3 zabezpečeného serveru certifikační Autority - G4
- Mobilní kořenové Symantec Enterprise pro Microsoft
- Primární Thawte kořenové certifikační Autority
- Primární Thawte, kořenové certifikační Autority - G2
- Primární Thawte, kořenové certifikační Autority - G3
- Thawte RSA CA 2018
- Vytvoření časového razítka Thawte certifikační Autority
- TrustAsia TLS RSA CA
- Třída VeriSign 3 rozšíření ověřování SSL CA
- Třída VeriSign 3 rozšíření ověřování SSL potřebujete certifikační Autority
- Třídy VeriSign 3 veřejný primárního certifikační autorita – G5
- Mezinárodní serveru VeriSign CA – třída 3
- Čas VeriSign razítek kořenový adresář
- Univerzální VeriSign kořenové certifikační autority


