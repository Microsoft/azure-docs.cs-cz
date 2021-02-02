---
title: Změny certifikátu Azure TLS
description: Změny certifikátu Azure TLS
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 11/10/2020
ms.author: mbaldwin
ms.openlocfilehash: 04b68899b796088bd4b89509a413c7a1fe5369bb
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475348"
---
# <a name="azure-tls-certificate-changes"></a>Změny certifikátu Azure TLS  

Microsoft aktualizuje služby Azure tak, aby používaly certifikáty TLS z jiné sady kořenových certifikačních autorit (CAs). Tato změna se provádí, protože aktuální certifikáty certifikační autority nejsou v rozporu s jedním z požadavků na základní hodnoty fóra CA nebo prohlížeče a odvolají se 15. února 2021.

## <a name="when-will-this-change-happen"></a>Kdy bude tato změna provedena?

Stávající koncové body Azure byly od 13. srpna 2020 převedené způsobem. Všechny nově vytvořené koncové body služby Azure TLS/SSL obsahují aktualizované certifikáty, které jsou zřetězené až s novými kořenovými certifikačními autoritami.

Podrobnosti specifické pro službu:

- Služba [Azure Active Directory](../../active-directory/index.yml) (Azure AD) zahájila tento přechod 7. července 2020.
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) a [DPS](../../iot-dps/index.yml) zůstanou v kořenové certifikační autoritě Baltimore CyberTrust, ale jejich zprostředkující certifikační autority se změní. [Kliknutím sem](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456)zobrazíte podrobnosti.
- [Azure Storage](../../storage/index.yml) zůstane v kořenové certifikační autoritě Baltimore CyberTrust, ale jejich zprostředkující certifikační autority se změní. [Kliknutím sem](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518)zobrazíte podrobnosti.
- [Mezipaměť Azure pro Redis](../../azure-cache-for-redis/index.yml) zůstane v kořenové certifikační autoritě Baltimore CyberTrust, ale jejich zprostředkující certifikační autorita se změní. [Kliknutím sem](../../azure-cache-for-redis/cache-whats-new.md)zobrazíte podrobnosti.
- Azure Instance Metadata Service zůstane v kořenové certifikační autoritě Baltimore CyberTrust, ale jejich zprostředkující certifikační autority se změní. [Kliknutím sem](/answers/questions/172717/action-required-for-attested-data-tls-with-azure-i.html)zobrazíte podrobnosti.

> [!IMPORTANT]
> Zákazníci možná budou muset po této změně aktualizovat své aplikace, aby se předešlo chybám připojení při pokusu o připojení ke službám Azure.

## <a name="what-is-changing"></a>Co se mění?

V dnešní době je většina certifikátů TLS používaných službami Azure zřetězená s následující kořenovou certifikační autoritou:

| Běžný název certifikační autority | Kryptografický otisk (SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Certifikáty TLS používané službami Azure budou zřetězit až do jednoho z následujících kořenových certifikačních autorit:

| Běžný název certifikační autority | Kryptografický otisk (SHA1) |
|--|--|
| [Globální kořenový adresář G2 DigiCert](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [Globální kořenová certifikační autorita DigiCert](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D – DŮVĚRYHODNÁ kořenová třída 3, CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Kořenová certifikační autorita RSA společnosti Microsoft 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Kořenová certifikační autorita Microsoft ECC 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Kdy je možné vyřadit starý zprostředkující kryptografický otisk?

Aktuální certifikáty certifikační autority *nebudou* odvolány do 15. února 2021. Po tomto datu můžete ze svého kódu odebrat staré kryptografické otisky.

Pokud se změní toto datum, budete upozorněni na nové datum odvolání.

## <a name="will-this-change-affect-me"></a>Bude se tato změna týkat? 

Očekáváme, že **většina zákazníků Azure** nebude mít vliv na.  Vaše aplikace ale může mít vliv, pokud explicitně určí seznam přijatelných certifikačních autorit. Tento postup se označuje jako připnutí certifikátu.

Tady je několik způsobů, jak zjistit, jestli je vaše aplikace ovlivněná:

- Vyhledejte ve zdrojovém kódu svůj kryptografický otisk, běžný název a další vlastnosti certifikátu pro všechny certifikační autority IT společnosti Microsoft, které najdete [tady](https://www.microsoft.com/pki/mscorp/cps/default.htm). Pokud dojde ke shodě, bude ovlivněna vaše aplikace. Pokud chcete tento problém vyřešit, aktualizujte zdrojový kód tak, aby obsahoval nové certifikační autority. V rámci osvědčeného postupu se ujistěte, že je možné certifikační autority přidat nebo upravit v krátkém časovém oznámení. Oborové předpisy vyžadují, aby se certifikáty certifikační autority nahradily během sedmi dnů, takže se zákazníci, kteří spoléhají na připnutí, musí reagovat rychle.

- Pokud máte aplikaci, která se integruje s rozhraními API Azure nebo jinými službami Azure, a nejste si jistí, jestli používá připnutí certifikátů, obraťte se na dodavatele aplikace.

- Různé operační systémy a jazykové moduly runtime, které komunikují se službami Azure, můžou vyžadovat další kroky pro správné sestavení řetězce certifikátů s těmito novými kořeny:
    - **Linux**: řada distribucí vyžaduje, abyste do/etc/SSL/certs. přidali certifikační autority. Konkrétní pokyny najdete v dokumentaci k distribuci.
    - **Java**: Ujistěte se, že úložiště klíčů Java obsahuje výše uvedené CAS.
    - **Windows spuštěné v odpojených prostředích**: systémy běžící v odpojených prostředích budou muset mít do úložiště důvěryhodných kořenových certifikačních autorit přidané nové kořeny a do úložiště zprostředkujících certifikačních autorit se přidaly zprostředkující.
    - **Android**: Projděte si dokumentaci k vašemu zařízení a verzi Androidu.
    - **Další hardwarová zařízení, zejména IoT**: obraťte se na výrobce zařízení.

- Pokud máte prostředí, ve kterém jsou pravidla brány firewall nastavená tak, aby umožňovala odchozí volání jenom na určitý seznam odvolaných certifikátů (CRL) stáhnout a/nebo online ověřovací umístění protokolu OCSP (Online Certificate Status Protocol). Bude potřeba, abyste povolili následující seznam CRL a adresy URL protokolu OCSP:

    - http://crl3&#46;d igicert&#46;com
    - http://crl4&#46;d igicert&#46;com
    - http://ocsp&#46;d igicert&#46;com
    - http://www&#46;d-Trust&#46;NET
    - http://root-c3-ca2-2009&#46;&#46;protokolu OCSP-Trust&#46;NET
    - http://crl&#46; Microsoft&#46;com
    - http://oneocsp&#46; Microsoft&#46;com
    - http://ocsp&#46; msocsp&#46;com
    - http://www&#46; Microsoft&#46;com/pkiops

## <a name="next-steps"></a>Další kroky

Pokud máte další otázky, kontaktujte nás prostřednictvím [podpory](https://azure.microsoft.com/support/options/).
