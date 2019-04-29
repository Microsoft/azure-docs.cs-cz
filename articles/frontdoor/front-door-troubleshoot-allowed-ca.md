---
title: Povolené certifikačních autorit pro povolení vlastní protokol HTTPS pro službu Azure, branou | Dokumentace Microsoftu
description: Pokud používáte vlastní certifikát pro povolení HTTPS pro vlastní doménu, musíte použít povolené certifikační autority (CA) k jeho vytvoření.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 5cf94079dcd68887d9725ffbe9124f9b6c897dd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736152"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Povolené certifikačních autorit pro povolení vlastní protokol HTTPS pro službu Azure, přední dveře

Pro vlastní doménu Azure branou služby při vám [povolení funkce HTTPS s použitím vlastní certifikát](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), musíte použít povolené certifikační autority (CA) k vytvoření certifikátu SSL. Jinak Pokud používáte bez povolené certifikační Autority nebo certifikát podepsaný svým držitelem, bude požadavek zamítnut.

Následující certifikační autority jsou povoleny v případě, že vytvoříte vlastní certifikát:

- Kořenové AddTrust externí certifikační Autority
- AlphaSSL Root CA
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- Asie a Tichomoří kořenové certifikační Autority
- Asie a Tichomoří kořenové certifikační autority 2013
- Asie a Tichomoří kořenové certifikační autority 2014
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
- RapidSSL RSA CA 2018
- Agentura kořenové
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
