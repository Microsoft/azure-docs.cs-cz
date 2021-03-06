---
title: Kurz – porozumění certifikátům veřejného klíče X. 509 pro Azure IoT Hub | Microsoft Docs
description: Kurz – porozumění certifikátům veřejného klíče X. 509 pro Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 5503f9ad57180146c25a01c133a27b34e643496c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378342"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Kurz: porozumění certifikátům veřejného klíče X. 509

Certifikáty X. 509 jsou digitální dokumenty, které představují uživatele, počítače, služby nebo zařízení. Jsou vydávány certifikační autoritou (CA), podřízenou certifikační autoritou nebo registrační autoritou a obsahují veřejný klíč subjektu certifikátu. Neobsahují privátní klíč subjektu, který musí být bezpečně uložen. Certifikáty veřejného klíče jsou zdokumentovány v [dokumentu RFC 5280](https://tools.ietf.org/html/rfc5280). Jsou digitálně podepsané a obecně obsahují následující informace:

* Informace o předmětu certifikátu
* Veřejný klíč, který odpovídá privátnímu klíči subjektu
* Informace o vydávající certifikační autoritě
* Podporované algoritmy šifrování a digitálního podepisování
* Informace o tom, jak určit odvolání a stav platnosti certifikátu

## <a name="certificate-fields"></a>Pole certifikátu

V průběhu času existují tři verze certifikátů. Každá verze přidá pole do před. Verze 3 je aktuální a obsahuje kromě polí verze 3 pole verze 1 a 2. Verze 1 definuje následující pole:

* **Version (verze**): hodnota (1, 2 nebo 3), která určuje číslo verze certifikátu.
* **Sériové číslo**: jedinečné číslo pro každý certifikát vydaný certifikační autoritou
* **Algoritmus podpisu certifikační autority**: název algoritmu, který certifikační autorita používá k podepsání obsahu certifikátu.
* **Název vystavitele**: rozlišující název (DN) vystavující certifikační autority certifikátu.
* **Doba platnosti**: časové období, za které se certifikát považuje za platný.
* **Název subjektu**: název entity reprezentované certifikátem.
* **Informace o veřejném klíči subjektu**: veřejný klíč vlastněný subjektem certifikátu

Verze 2 přidala následující pole obsahující informace o vystaviteli certifikátu. Tato pole se ale používají zřídka.

* **Jedinečné ID vystavitele**: jedinečný identifikátor vydávající certifikační autority definovaný certifikační autoritou
* **Jedinečné ID subjektu**: jedinečný identifikátor subjektu certifikátu, který je definovaný vystavující certifikační autoritou.

Certifikáty verze 3 přidaly tato rozšíření:

* **Identifikátor klíče autority**: může to být jedna ze dvou hodnot:
  * Subjekt certifikační autority a sériové číslo certifikátu certifikační autority, který tento certifikát vystavil
  * Hodnota hash veřejného klíče certifikační autority, která vystavila tento certifikát
* **Identifikátor klíče subjektu**: hodnota hash veřejného klíče aktuálního certifikátu
* **Použití klíče** Definuje službu, pro kterou se dá certifikát použít. Může to být jedna nebo víc z následujících hodnot:
  * **Digitální podpis**
  * **Neodmítnutí**
  * **Šifrování klíče**
  * **Zašifrování dat**
  * **Klíčová smlouva**
  * **Podpis certifikátu klíče**
  * **Odvolaný znak CRL**
  * **Jenom zakódování**
  * **Pouze dekódování**
* **Období použití privátního klíče**: doba platnosti privátního klíče pro část páru klíčů
* **Zásady certifikátů**: zásady používané k ověření předmětu certifikátu
* **Mapování zásad**: namapuje zásadu v jedné organizaci na zásadu v jiné.
* **Alternativní název subjektu**: seznam alternativních názvů pro předmět
* **Alternativní název vystavitele**: seznam alternativních názvů vydávající certifikační autority
* **Subject – atribut DIR**: atributy z adresáře X. 500 nebo LDAP
* **Základní omezení**: Umožňuje certifikátu určit, jestli je vydaný certifikační autoritou, nebo uživateli, počítači, zařízení nebo službě. Toto rozšíření zahrnuje také omezení délky cesty, které omezuje počet podřízených certifikačních autorit, které mohou existovat.
* **Omezení názvů**: Určuje, které obory názvů se povolují v certifikátu vydaném certifikační autoritou.
* **Omezení zásad**: dá se použít k zákazu mapování zásad mezi certifikačními autoritami.
* **Rozšířené použití klíče**: Určuje, jak se dá veřejný klíč certifikátu použít nad rámec účelů určených v rozšíření **použití klíče** .
* **Distribuční body seznamu CRL**: obsahuje jednu nebo více adres URL, na kterých je publikován základní seznam odvolaných certifikátů (CRL).
* **Inhibice anyPolicy**: zabrání použití **všech zásad vystavování** OID (2.5.29.32.0) v certifikátech podřízené certifikační autority.
* **Čerstvý seznam CRL**: obsahuje jednu nebo více adres URL, na které je publikován ROZDÍLOVÝ seznam CRL vystavující certifikační autority.
* **Přístup k informacím autority**: obsahuje jednu nebo více adres URL, na kterých je certifikát vydávající certifikační autority publikovaný.
* **Přístup k informacím o subjektu**: obsahuje informace o tom, jak načíst další podrobnosti předmětu certifikátu.

## <a name="certificate-formats"></a>Formáty certifikátů

Certifikáty se dají ukládat v nejrůznějších formátech. Ověřování Azure IoT Hub obvykle používá formáty PEM a PFX.

### <a name="binary-certificate"></a>Binární certifikát

Obsahuje nezpracovaný binární certifikát formuláře s použitím kódování DER ASN. 1.

### <a name="ascii-pem-format"></a>Formát ASCII PEM

Certifikát PEM (přípona PEM) obsahuje certifikát s kódováním base64, který začíná na-----počáteční-----certifikátu a končící-----m KONCOVým CERTIFIKÁTem-----. Formát PEM je velmi běžný a vyžaduje ho IoT Hub při nahrávání určitých certifikátů.

### <a name="ascii-pem-key"></a>Klíč ASCII (PEM)

Obsahuje klíč DER s kódováním base64, který může obsahovat další metadata o algoritmu používaném pro ochranu heslem.

### <a name="pkcs7-certificate"></a>Certifikát PKCS # 7

Formát navržený pro přenos podepsaných nebo šifrovaných dat. Je definovaný v [dokumentu RFC 2315](https://tools.ietf.org/html/rfc2315). Může zahrnovat celý řetěz certifikátů.

### <a name="pkcs8-key"></a>Klíč PKCS # 8

Formát soukromého úložiště klíčů definovaného [specifikací RFC 5208](https://tools.ietf.org/html/rfc5208).

### <a name="pkcs12-key-and-certificate"></a>Klíč a certifikát PKCS # 12

Složitý formát, který může ukládat a chránit klíč a celý řetěz certifikátů. Obvykle se používá s příponou. pfx. PKCS č. 12 je synonymem formátu PFX.

## <a name="for-more-information"></a>Další informace

Další informace najdete v následujících tématech:

* [Průvodce laymanem na X. 509 Certificate žargonu](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Konceptuální porozumění certifikátům CA X. 509 v oboru IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)

## <a name="next-steps"></a>Další kroky

Pokud chcete vygenerovat testovací certifikáty, které můžete použít k ověření zařízení pro vaši IoT Hub, přečtěte si následující témata:

* [Vytváření testovacích certifikátů pomocí skriptů Microsoft-Supplied](tutorial-x509-scripts.md)
* [Vytváření testovacích certifikátů pomocí OpenSSL](tutorial-x509-openssl.md)
* [Vytváření Self-Signed testovacích certifikátů pomocí OpenSSL](tutorial-x509-self-sign.md)

Pokud máte certifikát certifikační autority (CA) nebo certifikát podřízené certifikační autority a chcete ho nahrát do služby IoT Hub a prověřit si, že ho vlastníte, přečtěte si téma [prokazující vlastnictví certifikátu certifikační autority](tutorial-x509-prove-possession.md).
