---
title: Povolené certifikační autority pro Azure přední dveře Standard/Premium (Preview)
description: V tomto článku jsou uvedené všechny certifikační autority, které jsou povolené při vytváření vlastního certifikátu.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 35c1e4f6c700333c72b97289cda1772335037211
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099271"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Povolené certifikační autority pro Azure přední dveře Standard/Premium (Preview)

Pokud povolíte funkci HTTPS pomocí vlastního certifikátu pro vlastní doménu Azure front-dveří Standard/Premium. K vytvoření certifikátu TLS/SSL potřebujete povolenou certifikační autoritu (CA). V opačném případě, pokud použijete nepovolenou certifikační autoritu nebo certifikát podepsaný svým držitelem, vaše žádost se odmítne.

Při vytváření vlastního certifikátu jsou povolené tyto certifikační autority:

- Kořenová AddTrust externí certifikační autority
- Kořenová certifikační autorita AlphaSSL
- ÁZEV ča infrastruktura 01
- ÁZEV infračervené certifikační autority 02
- Ameroot
- APCA-DM3P
- Atos TrustedRoot 2011
- Kořenová certifikační autorita autopilot
- Baltimore CyberTrust Root
- Veřejná primární certifikační autorita třídy 3
- COMODO certifikační autorita RSA
- COMODO certifikační autoritu zabezpečeného serveru pro ověření domény RSA
- D – DŮVĚRYHODNÁ kořenová třída 3, CA 2 2009
- DigiCert Cloud Services CA – 1
- Globální kořenová certifikační autorita DigiCert
- Globální kořenový adresář G2 DigiCert
- DigiCert High Assurance – CA – 3
- Kořenová certifikační autorita DigiCert High Assurance
- CA serveru DigiCert SHA2 Extended Validation Server
- CA serveru DigiCert SHA2 High Assurance
- DigiCert SHA2, certifikační autorita zabezpečeného serveru
- Kořenová certifikační autorita v čase X3
- D – důvěryhodná kořenová třída 3, CA 2 2009
- Šifrování všude – CA DV TLS
- Svěřit kořenovou certifikační autoritu
- Svěřit kořenovou certifikační autoritu – G2
- Entrust.net certifikační autorita (2048)
- Důvěryhodná globální certifikační autorita
- Primární certifikační autorita s nedůvěryhodným vztahem
- Primární certifikační autorita s geografickou důvěryhodností – G2
- Důvěryhodnost RSA CA 2018
- GlobalSign
- GlobalSign Extended Validation CA-SHA256-G2
- CA ověřování organizace GlobalSign – G2
- Kořenová certifikační autorita GlobalSign
- Přejít na kořenovou certifikační autoritu Daddy – G2
- Přejít na Daddy Secure Certificate Authority – G2
- Pojďme šifrovat autoritu X3
- (Mikrosekundy) e-Szigno Root CA 2009
- QuoVadis root CA2 G3
- RapidSSL RSA CA 2018
- RootCA1 komunikace zabezpečení
- RootCA2 komunikace zabezpečení
- RootCA3 komunikace zabezpečení
- CA Symantec Class 3 EV SSL – G3
- Zabezpečený serverová CA Symantec Class 3 – G4
- Symantec Enterprise Mobile root pro Microsoft
- Primární kořenová certifikační autorita Thawte
- Thawte – primární kořenová certifikační autorita – G2
- Thawte Primary Root CA – G3
- Thawte RSA CA 2018
- CA pro časová razítka Thawte
- TrustAsia TLS RSA CA
- CERTIFIKÁT zabezpečení SSL třídy 3 pro rozšířené ověření
- VeriSign třída 3 – certifikát SSL s rozšířeným ověřením zabezpečení SGC
- VeriSign Class 3 Public Primary Certification Authority – G5
- CA pro mezinárodní servery VeriSign – třída 3
- Kořenový adresář služby pro časová razítko společnosti VeriSign
- VeriSign Universal root certifikační úřad
