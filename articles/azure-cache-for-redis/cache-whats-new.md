---
title: Co je nového v mezipaměti Azure pro Redis
description: Nedávné aktualizace pro Azure cache pro Redis
author: yegu-ms
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: yegu
ms.openlocfilehash: b30e83b89b25e6400b8c7e0419406631fa1edcd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91492452"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Co je nového v mezipaměti Azure pro Redis

## <a name="azure-tls-certificate-change"></a>Změna certifikátu Azure TLS

Microsoft aktualizuje služby Azure tak, aby používaly certifikáty serveru TLS z jiné sady certifikačních autorit (CAs). Tato změna je zahrnuta v fázích od 13. srpna 2020 do 26. října 2020 (odhad). Azure tuto změnu provádí, protože [aktuální certifikáty certifikační autority nejsou v rozporu s jedním z požadavků na základní hodnoty fóra CA nebo prohlížeče](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951). Problém byl hlášen 1. července 2020 a vztahuje se na více oblíbených poskytovatelů infrastruktury veřejných klíčů (PKI) po celém světě. Většina certifikátů TLS používaných službami Azure dnes pochází z kořenové infrastruktury veřejných klíčů *Baltimore CyberTrust* . Služba Azure cache for Redis bude nadále zřetězena do kořenového adresáře CyberTrust Baltimore. Certifikáty serveru TLS se ale budou vydávat novými zprostředkujícími certifikačními autoritami (ICAs) od 12. října 2020.

> [!NOTE]
> Tato změna je omezená na služby ve veřejných [oblastech Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Nezahrnuje svrchovaná (např. Čína) nebo cloudy státní správy.
>
>

### <a name="does-this-change-affect-me"></a>Má tato změna vliv na mě?

Očekáváme, že tato změna neovlivní většinu Azure cache pro zákazníky s Redis. Vaše aplikace může být ovlivněná, pokud explicitně určuje seznam přijatelných certifikátů, což je postup známý jako "připnutí certifikátu". Pokud je připnuté na zprostředkující nebo listový certifikát namísto kořenového adresáře Baltimore CyberTrust, měli byste **provést okamžitý** postup pro změnu konfigurace certifikátu.

Následující tabulka poskytuje informace o certifikátech, které jsou odebírány. V závislosti na tom, který certifikát vaše aplikace používá, ho možná budete muset aktualizovat, aby se zabránilo ztrátě připojení ke službě Azure cache pro instanci Redis.

| Typ certifikační autority | Current | Po válcování (říjen 12, 2020) | Akce |
| ----- | ----- | ----- | ----- |
| Kořenová složka | Kryptografický otisk: d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> Vypršení platnosti: pondělí, květen 12, 2025, 4:59:00 odp.<br><br> Název předmětu:<br> CN = Baltimore CyberTrust Root<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | Neměnit | Žádné |
| Intermediate | Kryptografické otisky<br> CN = Microsoft IT – CA pro TLS 1<br> Kryptografický otisk: 417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT – CA pro TLS 2<br> Kryptografický otisk: 54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT CA pro TLS 4<br> Kryptografický otisk: 8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT CA pro TLS 5<br> Kryptografický otisk: Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> Vypršení platnosti: pátek, 20. května 2024 5:52:38 dop.<br><br> Název předmětu:<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = Washington<br> C = US<br> | Kryptografické otisky<br> CN = CA Microsoft RSA TLS 01<br> Kryptografický otisk: 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = CA Microsoft RSA TLS 02<br> Kryptografický otisk: b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> Vypršení platnosti: úterý 8. října 2024 12:00:00 AM;<br><br> Název předmětu:<br> O = Microsoft Corporation<br> C = US<br> | Vyžadováno |

### <a name="what-actions-should-i-take"></a>Jaké akce mám provést?

Pokud vaše aplikace používá úložiště certifikátů operačního systému nebo si kořen Baltimore připnete mimo jiné, není nutné provádět žádnou akci. Na druhé straně, pokud vaše aplikace připnout na zprostředkující nebo listový certifikát TLS, doporučujeme připnout následující kořeny:

| Certifikát | Kryptografický otisk |
| ----- | ----- |
| [Kořenová certifikační autorita Baltimore](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Kořenová certifikační autorita RSA společnosti Microsoft 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [Globální kořenový adresář G2 DigiCert](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> U zprostředkujících i koncových certifikátů se očekává, že se často mění. Doporučujeme, abyste na ně nemuseli pořizovat žádné závislosti. Místo toho připnout aplikaci do kořenového certifikátu, protože se tato aplikace vymění méně často.
>
>

Pokud chcete pokračovat v připnutí zprostředkujících certifikátů, přidejte následující do seznamu připnutých zprostředkujících certifikátů, který obsahuje několik dalších, aby se minimalizovaly budoucí změny:

| Běžný název certifikační autority | Kryptografický otisk |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS – CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure certifikační autorita pro vydávání TLS 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure vydávající CA TLS 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure CA vydávající certifikační autoritu 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure CA vydávající certifikát TLS 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

Pokud vaše aplikace ověřuje certifikát v kódu, budete ho muset upravit, aby rozpoznal vlastnosti (například vystavitele, kryptografický otisk) nově připnutých certifikátů. Toto dodatečné ověření by se mělo týkat všech připnutých certifikátů, které se budou považovat za budoucí kontrolu.

## <a name="next-steps"></a>Další kroky

Pokud máte další otázky, kontaktujte nás prostřednictvím [podpory](https://azure.microsoft.com/support/options/).  
