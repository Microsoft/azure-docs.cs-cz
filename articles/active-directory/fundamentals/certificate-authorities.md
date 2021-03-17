---
title: Azure Active Directory certifikační autority
description: Seznam důvěryhodných certifikátů používaných v Azure
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bea6f4161bcca063cd2c58d4c463473426f159ba
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96859127"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Certifikační autority, které používá Azure Active Directory

> [!IMPORTANT]
> Informace na této stránce jsou relevantní pouze pro entity, které explicitně zadávají seznam přijatelných certifikačních autorit (CAs). Tento postup, označovaný jako připnutí certifikátu, by se měl vyhnout, pokud neexistují žádné jiné možnosti.

Všechny entity, které se pokoušejí o přístup ke službám Azure Active Directory (Azure AD) identity prostřednictvím protokolů TLS/SSL, se zobrazí s certifikáty z níže uvedených certifikačních autorit. Pokud tato entita důvěřuje těmto certifikačním autoritám, může certifikáty použít k ověření identity a legitimity služeb identit a k vytvoření zabezpečených připojení.

Certifikační autority je možné klasifikovat do kořenových certifikačních autorit a zprostředkujících certifikačních autorit. Kořenové certifikační autority obvykle mají jeden nebo víc přidružených zprostředkujících certifikačních autorit. Tento článek obsahuje seznam kořenových certifikačních autorit používaných službami Azure AD identity a zprostředkující certifikační autority přidružené ke každé z těchto kořenových adresářů. Pro každou certifikační autoritu zahrneme identifikátory URI (Uniform Resource Identifier) pro stažení přidružených souborů k informacím autority (AIA) a distribučního bodu seznamu odvolaných certifikátů (CDP). V případě potřeby také poskytujeme identifikátor URI pro koncový bod protokolu stavu certifikátu online (OCSP).

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>CAs používané v cloudech Azure Public a Azure US pro státní správu

Různé služby můžou používat jiné kořenové nebo zprostředkující certifikační autority. Proto mohou být požadovány všechny níže uvedené položky.

### <a name="digicert-global-root-g2"></a>Globální kořenový adresář G2 DigiCert


| Kořenová CA| Sériové číslo| Datum vypršení platnosti data vydání| Kryptografický otisk SHA1| Identifikátory URI |
| - |- |-|-|-|-|
| Globální kořenový adresář G2 DigiCert| 033af1e6a711a 9a0bb2864b11d09fae5| Od 1. srpna 2013 <br>15. ledna 2038| df3c24f9bfd666761b268 073fe06d1cc8d4f82a4| [AIA](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[BOD](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>Přidružené zprostředkující certifikační autority

| Vydání a zprostředkující certifikační autorita| Sériové číslo| Datum vypršení platnosti data vydání| Kryptografický otisk SHA1| Identifikátory URI |
| - | - | - | - | - | 
| Microsoft Azure certifikační autorita pro vydávání TLS 01| 0aafa6c5ca63c45141 ea3be1f7c75317| 29. července 2020<br>27. června 2024| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[BOD](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.crl)|
|Microsoft Azure vydávající CA TLS 02| 0c6ae97cced59983 8690a00a9ea53214| 29. července 2020<br>27. června 2024| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[BOD](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure CA vydávající certifikační autoritu 05| 0d7bede97d8209967a 52631b8bdd18bd| 29. července 2020<br>27. června 2024| 6c3af02e7f269aa73a fd0eff2a88a4a1f04ed1e5| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[BOD](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure CA vydávající certifikát TLS 06| 02e79171fb8021e93fe 2d983834c50c0| 29. července 2020<br>27. června 2024| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer)<br>[BOD](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Baltimore CyberTrust Root

| Kořenová CA| Sériové číslo| Datum vypršení platnosti data vydání| Kryptografický otisk SHA1| Identifikátory URI |
| - | - | - | - | - | 
| Baltimore CyberTrust Root| 020000b9| 12. května 2000<br>12. května 2025| d4de20d05e66fc53fe 1a50882c78db2852cae474|<br>[BOD](http://crl3.digicert.com/Omniroot2025.crl)<br>[ZAMÍTNUT](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>Přidružené zprostředkující certifikační autority

| Vydání a zprostředkující certifikační autorita| Sériové číslo| Datum vypršení platnosti data vydání| Kryptografický otisk SHA1| Identifikátory URI |
| - | - | - | - | - | 
| Microsoft RSA TLS CA 01| 703d7a8f0ebf55aaa 59f98eaf4a206004eb2516a| 21. července 2020<br>8. října 2024| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[BOD](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[ZAMÍTNUT](http://ocsp.msocsp.com/) |
| Microsoft RSA TLS – CA 02| b0c2d2d13cdd56cdaa 6ab6e2c04440be4a429c75| 21. července 2020<br>20. května 2024| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[BOD](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[ZAMÍTNUT](http://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>Globální kořenová certifikační autorita DigiCert

| Kořenová CA| Sériové číslo| Datum vypršení platnosti data vydání| Kryptografický otisk SHA1| Identifikátory URI |
| - | - | - | - | - | 
| Globální kořenová certifikační autorita DigiCert| 083be056904246 b1a1756ac95991c74a| 9. listopadu 2006<br>9. listopadu 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [BOD](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[ZAMÍTNUT](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>Přidružené zprostředkující certifikační autority

| Vydání a zprostředkující certifikační autorita| Sériové číslo| Datum vypršení platnosti data vydání| Kryptografický otisk SHA1| Identifikátory URI |
| - | - | - | - | - |
| DigiCert SHA2, certifikační autorita zabezpečeného serveru| 01fda3eb6eca75c 888438b724bcfbc91| 8. března 2013, 8. března 2023| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[BOD](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[ZAMÍTNUT](http://ocsp.digicert.com/) |
| DigiCert SHA2, certifikační autorita zabezpečeného serveru |02742eaa17ca8e21 c717bb1ffcfd0ca0 |22. září 2020<br>22. září 2030|626d44e704d1ceabe3bf 0d53397464ac8080142c|[AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA-2.crt)<br>[BOD](http://crl3.digicert.com/DigiCertSHA2SecureServerCA.crl)<br>[ZAMÍTNUT](http://ocsp.digicert.com/)|


## <a name="cas-used-in-azure-china-21vianet-cloud"></a>CAs používané v cloudu Azure Čína 21Vianet

### <a name="digicert-global-root-ca"></a>Globální kořenová certifikační autorita DigiCert


| Kořenová CA| Sériové číslo| Datum vypršení platnosti data vydání| Kryptografický otisk SHA1| Identifikátory URI |
| - | - | - | - | - |
| Globální kořenová certifikační autorita DigiCert| 083be056904246b 1a1756ac95991c74a| Listopad 9, 2006<br>Listopad 9, 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [BOD](http://ocsp.digicert.com/)<br>[ZAMÍTNUT](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>Přidružená zprostředkující certifikační autorita

| Vydání a zprostředkující certifikační autorita| Sériové číslo| Datum vypršení platnosti data vydání| Kryptografický otisk SHA1| Identifikátory URI |
| - | - | - | - | - | - |
| DigiCert Basic RSA CN – CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 4. března 2020<br>4. března 2030| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[BOD](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[ZAMÍTNUT](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>Další kroky
[Další informace o Microsoft 365 řetězech šifrování](/microsoft-365/compliance/encryption-office-365-certificate-chains)
