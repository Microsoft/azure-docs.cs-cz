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
ms.openlocfilehash: de709133099674a0aa0386113b6459f8bc05e378
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047273"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Povolené certifikačních autorit pro povolení vlastní protokol HTTPS pro službu Azure, přední dveře

Pro vlastní doménu Azure branou služby při vám [povolení funkce HTTPS s použitím vlastní certifikát](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), musíte použít povolené certifikační autority (CA) k vytvoření certifikátu SSL. Jinak Pokud používáte bez povolené certifikační Autority nebo certifikát podepsaný svým držitelem, bude požadavek zamítnut.

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
