---
title: Povolené certifikačních autorit pro povolení vlastní HTTPS na Azure CDN | Microsoft Docs
description: Pokud používáte vlastní certifikát tím povolíte protokol HTTPS na vlastní doménu, musíte použít povolené certifikační autoritu (CA) k jeho vytvoření.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 3c41ca7e375324ff784bf7bee347bb56400ddfbd
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238262"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Povolené certifikačních autorit pro povolení vlastní HTTPS na Azure CDN

Pro vlastní doménu služby Azure Content Delivery Network (CDN) na **Azure CDN Standard od společnosti Microsoft** koncový bod, když jste [povolit funkci HTTPS pomocí vlastního certifikátu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), je nutné použít povolené certifikační autority (CA) k vytvoření svůj certifikát SSL. Jinak Pokud používáte nepovolené certifikační Autority nebo certifikát podepsaný svým držitelem, budou odmítnuty vaši žádost.

> [!NOTE]
> Možnost použití vlastního certifikátu povolit vlastní HTTPS je dostupná jenom s **Azure CDN Standard od společnosti Microsoft** profily. 
>

Následující certifikační autority jsou povoleny, když vytvoříte svůj vlastní certifikát:

- Kořenové AddTrust externí certifikační Autority
- Asie a Tichomoří kořenové certifikační Autority
- Asie a Tichomoří kořenové certifikační autority 2013
- Asie a Tichomoří kořenové certifikační autority 2014
- APCA DM3P
- AutoPilot kořenové certifikační Autority
- Baltimore CyberTrust Root
- Třída 3 veřejné primární certifikační autority
- COMODO RSA certifikační autority
- COMODO RSA domény ověření zabezpečeného serveru certifikační Autority
- D DŮVĚRYHODNÉ kořenové certifikační Autority třídy 3 2 2009
- DigiCert cloudových služeb certifikační Autority-1
- DigiCert globální kořenové certifikační Autority
- Zajištění DigiCert vysokou certifikační Autority – 3
- Vysoce DigiCert záruku EV kořenové certifikační Autority
- DigiCert SHA2 vysokou zárukou serveru certifikační Autority
- DigiCert SHA2 zabezpečeného serveru certifikační Autority
- GlobalSign
- GlobalSign rozšířené G2 certifikační Autority - SHA256 – ověření
- Ověřování organizace GlobalSign certifikační Autority – G2
- GlobalSign kořenové certifikační Autority
- Microsoft Authenticode(tm) kořenovou autoritou.
- Služby Microsoft Exchange 2015 certifikační Autority
- Interní kořenovou společností Microsoft
- IT oddělení společnosti Microsoft ITO SSL certifikační Autority 1
- SHA1 SSL IT oddělení společnosti Microsoft
- SHA2 SSL IT oddělení společnosti Microsoft
- IT oddělení společnosti Microsoft TLS CA 1
- IT oddělení společnosti Microsoft TLS CA 2
- IT oddělení společnosti Microsoft TLS CA 4
- IT oddělení společnosti Microsoft TLS CA 5
- Microsoft kořenovou autoritou
- Microsoft kořenové certifikační autority
- Microsoft kořenové certifikační autority 2010
- Microsoft kořenové certifikační autority 2011
- Microsoft zabezpečeného serveru certifikační Autority 2011
- Kořenové partnera služby společnosti Microsoft
- Microsoft času časového razítka služby Root
- Kompatibility hardwaru systému Microsoft Windows
- MSIT Z2 CERTIFIKAČNÍ AUTORITY
- Certifikační Autorita organizace MSIT 1
- Certifikační Autorita organizace MSIT 3
- Kořenové agentura
- Třída Symantec 3 EV SSL CA - G3
- Třída Symantec 3 zabezpečeného serveru certifikační Autority – G4
- Mobilní kořenovém Symantec Enterprise pro Microsoft
- Thawte Timestamping certifikační Autority
- UTN-USERFirst – objekt
- Certifikační Autorita protokolu SSL 3 rozšířené ověření VeriSign – třída
- Třída VeriSign 3 rozšířené ověřování SSL potřebujete certifikační Autority
- Třída VeriSign 3 veřejné primární certifikační autority - G5
- VeriSign mezinárodní serveru certifikační Autority – 3 – třída
- Čas VeriSign časového razítka služby Root
- Univerzální VeriSign kořenové certifikační autority
- WMSvc. SHA2 DALEDGE1008

