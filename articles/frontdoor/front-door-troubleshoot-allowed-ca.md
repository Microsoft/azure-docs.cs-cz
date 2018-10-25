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
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984762"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Povolené certifikačních autorit pro povolení vlastní protokol HTTPS pro službu Azure, přední dveře

Pro vlastní doménu Azure branou služby při vám [povolení funkce HTTPS s použitím vlastní certifikát](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), musíte použít povolené certifikační autority (CA) k vytvoření certifikátu SSL. Jinak Pokud používáte bez povolené certifikační Autority nebo certifikát podepsaný svým držitelem, bude požadavek zamítnut.

Následující certifikační autority jsou povoleny v případě, že vytvoříte vlastní certifikát:

- Kořenové AddTrust externí certifikační Autority
- AlphaSSL kořenové certifikační Autority
- Název infrastruktura certifikační Autority 01
- Název infrastruktura certifikační Autority 02
- Ameroot
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
- DigiCert SHA2 rozšířené ověřování serveru certifikační Autority
- DigiCert SHA2 vysokou zárukou Server certifikační Autority
- DigiCert SHA2 zabezpečeného serveru certifikační Autority
- Kořenové certifikační Autority X3 letního času
- D důvěryhodné kořenové certifikační Autority třídy 3 2 2009
- Všude šifrování TLS DV certifikační Autority
- Kořenové certifikační autority Entrust
- Entrust kořenové certifikační autority - G2
- Entrust.NET certifikační autority (2048)
- Globální certifikační Autority GeoTrust
- Primární GeoTrust certifikační autority
- Primární GeoTrust certifikační autorita – G2
- RSA Geotrust 2018 certifikační Autority
- GlobalSign
- GlobalSign rozšířené G2 CA - SHA256 – ověření
- Ověřování organizace GlobalSign CA - G2
- GlobalSign kořenové certifikační Autority
- Go Daddy kořenová certifikační autorita – G2
- Go Daddy zabezpečení certifikační autority - G2
- RSA RapidSSL 2018 certifikační Autority
- Agentura kořenové
- Třída Symantec 3 EV SSL CA - G3
- Třída Symantec 3 zabezpečeného serveru certifikační Autority - G4
- Mobilní kořenové Symantec Enterprise pro Microsoft
- Primární Thawte kořenové certifikační Autority
- Primární Thawte, kořenové certifikační Autority - G2
- Primární Thawte, kořenové certifikační Autority - G3
- RSA Thawte 2018 certifikační Autority
- Vytvoření časového razítka Thawte certifikační Autority
- TrustAsia TLS RSA certifikační Autority
- Třída VeriSign 3 rozšíření ověřování SSL CA
- Třída VeriSign 3 rozšíření ověřování SSL potřebujete certifikační Autority
- Třídy VeriSign 3 veřejný primárního certifikační autorita – G5
- Mezinárodní serveru VeriSign CA – třída 3
- Čas VeriSign razítek kořenový adresář
- Univerzální VeriSign kořenové certifikační autority
