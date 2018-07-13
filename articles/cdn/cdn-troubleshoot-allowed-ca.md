---
title: Povolené certifikačních autorit pro povolení vlastní protokol HTTPS ve službě Azure CDN | Dokumentace Microsoftu
description: Pokud používáte vlastní certifikát pro povolení HTTPS pro vlastní doménu, musíte použít povolené certifikační autority (CA) k jeho vytvoření.
services: cdn
documentationcenter: ''
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 28d6d24266c11b1295c57c8ec46c2bd5ec690b28
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005913"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Pro povolení vlastní protokol HTTPS ve službě Azure CDN povolená certifikační autority

Pro vlastní doménu Azure Content Delivery Network (CDN) na **Azure CDN Standard od společnosti Microsoft** koncový bod, když jste [povolení funkce HTTPS s použitím vlastní certifikát](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), je nutné použít povolený certifikační autorita (CA) k vytvoření certifikátu SSL. Jinak Pokud používáte bez povolené certifikační Autority nebo certifikát podepsaný svým držitelem, bude požadavek zamítnut.

> [!NOTE]
> Možnost používat vlastní certifikát pro povolení HTTPS pro vlastní je k dispozici pouze s **Azure CDN Standard od společnosti Microsoft** profily. 
>

Následující certifikační autority jsou povoleny v případě, že vytvoříte vlastní certifikát:

- Kořenové AddTrust externí certifikační Autority
- Asie a Tichomoří kořenové certifikační Autority
- Asie a Tichomoří kořenové certifikační autority 2013
- Asie a Tichomoří kořenové certifikační autority 2014
- APCA DM3P
- AutoPilot kořenové certifikační Autority
- Baltimore CyberTrust Root
- Třída 3 veřejný primárního certifikační autority
- COMODO RSA certifikační autority
- COMODO RSA domény ověření zabezpečeného serveru certifikační Autority
- D DŮVĚRYHODNÉ kořenové certifikační Autority třídy 3 2 2009
- DigiCert cloudových služeb certifikační Autority-1
- DigiCert globální kořenové certifikační Autority
- Vysoce DigiCert Assurance CA-3
- Vysoce DigiCert Assurance EV kořenové certifikační Autority
- DigiCert SHA2 vysokou zárukou Server certifikační Autority
- DigiCert SHA2 zabezpečeného serveru certifikační Autority
- Globální certifikační Autority GeoTrust
- Primární GeoTrust certifikační autority
- Primární GeoTrust certifikační autorita – G2
- GlobalSign
- GlobalSign rozšířené G2 CA - SHA256 – ověření
- Ověřování organizace GlobalSign CA - G2
- GlobalSign kořenové certifikační Autority
- Go Daddy kořenová certifikační autorita – G2
- Microsoft Authenticode(tm) kořenová autorita
- Služby Microsoft Exchange 2015 certifikační Autority
- Interní kořenovou společnosti Microsoft
- Microsoft IT ITO SSL CA 1
- Microsoft IT SHA1 SSL
- Microsoft IT SSL SHA2
- Microsoft IT TLS CA 1
- Microsoft IT TLS CA 2
- Microsoft IT TLS CA 4
- Microsoft IT TLS CA 5
- Microsoft kořenová autorita
- Microsoft kořenové certifikační autority
- Microsoft kořenové certifikační autority 2010
- Microsoft kořenové certifikační autority 2011
- Microsoft zabezpečeného serveru certifikační Autority 2011
- Kořenové partnerské služby společnosti Microsoft
- Čas Microsoft razítek kořenový adresář
- Kompatibilita hardwaru Microsoft Windows
- MSIT Z2 CERTIFIKAČNÍ AUTORITY
- MSIT certifikační autority organizace 1
- MSIT certifikační autority organizace 3
- Agentura kořenové
- Třída Symantec 3 EV SSL CA - G3
- Třída Symantec 3 zabezpečeného serveru certifikační Autority - G4
- Mobilní kořenové Symantec Enterprise pro Microsoft
- Primární Thawte kořenové certifikační Autority
- Primární Thawte, kořenové certifikační Autority - G2
- Primární Thawte, kořenové certifikační Autority - G3
- Vytvoření časového razítka Thawte certifikační Autority
- UTN-USERFirst – objekt
- Třída VeriSign 3 rozšíření ověřování SSL CA
- Třída VeriSign 3 rozšíření ověřování SSL potřebujete certifikační Autority
- Třídy VeriSign 3 veřejný primárního certifikační autorita – G5
- Mezinárodní serveru VeriSign CA – třída 3
- Čas VeriSign razítek kořenový adresář
- Univerzální VeriSign kořenové certifikační autority
- WMSvc. SHA2 DALEDGE1008

