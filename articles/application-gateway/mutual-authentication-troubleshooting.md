---
title: Řešení vzájemného ověřování v Azure Application Gateway
description: Přečtěte si, jak řešit problémy vzájemného ověřování na Application Gateway
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 623addd253b3eb28bdf70db02ddfbe4b320cbae7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231392"
---
# <a name="troubleshooting-mutual-authentication-errors-in-application-gateway-preview"></a>Řešení potíží se vzájemným ověřováním v Application Gateway (Preview)

Naučte se řešit problémy se vzájemným ověřováním při použití Application Gateway. 

## <a name="overview"></a>Přehled 

Po nakonfigurování vzájemného ověřování u Application Gateway se může při pokusu o použití vzájemného ověřování zobrazit řada chyb. Mezi běžné příčiny chyb patří:

* Nahrál se certifikát nebo řetěz certifikátů bez certifikátu kořenové certifikační autority.
* Nahrál se řetěz certifikátů s více certifikáty kořenové certifikační autority.
* Nahrál se řetěz certifikátů, který obsahuje jenom listový certifikát bez certifikátu certifikační autority. 
* Chyby ověřování z důvodu neshody rozlišujícího názvu vystavitele  

Projdeme do různých scénářů, které byste mohli spustit, a jak tyto scénáře řešit. Pak budeme řešit kódy chyb a vysvětlovat pravděpodobné příčiny některých chybových kódů, které se můžou zobrazit se vzájemným ověřováním. 

## <a name="scenario-troubleshooting---configuration-problems"></a>Řešení potíží se scénáři – problémy s konfigurací
Při pokusu o konfiguraci vzájemného ověřování se může jednat o několik scénářů. Podíváme se, jak řešit některé nejběžnější nástrah. 

### <a name="self-signed-certificate"></a>Certifikát podepsaný svým držitelem

#### <a name="problem"></a>Problém 

Certifikát klienta, který jste nahráli, je certifikát podepsaný svým držitelem a výsledkem je ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate kódu chyby.

#### <a name="solution"></a>Řešení 

Ověřte, že certifikát podepsaný svým držitelem, který používáte, má rozšíření *BasicConstraintsOid* = "2.5.29.19", které indikuje, že subjekt může fungovat jako certifikační autorita. Tím se zajistí, že použitý certifikát je certifikát certifikační autority. Další informace o tom, jak generovat klientské certifikáty podepsané svým držitelem, najdete v [certifikátech důvěryhodných klientů](./mutual-authentication-certificate-management.md).

## <a name="scenario-troubleshooting---connectivity-problems"></a>Řešení potíží se scénáři – problémy s připojením

Mohli jste být schopni nakonfigurovat vzájemné ověřování bez jakýchkoli problémů, ale při odesílání požadavků do Application Gateway dochází k problémům. Řešíme některé běžné problémy a řešení v následující části. Vlastnost *sslClientVerify* můžete najít v protokolech přístupu Application Gateway. 

### <a name="sslclientverify-is-none"></a>SslClientVerify je NONE

#### <a name="problem"></a>Problém 

Vlastnost *sslClientVerify* se v protokolech Access zobrazuje jako None. 

#### <a name="solution"></a>Řešení 

K tomu dochází, když klient při odesílání žádosti do Application Gateway neodešle klientský certifikát. K tomu může dojít, pokud klient odesílající požadavek do Application Gateway není správně nakonfigurovaný tak, aby používal klientské certifikáty. Jedním ze způsobů, jak ověřit, že nastavení ověřování klienta na Application Gateway pracuje podle očekávání, je prostřednictvím následujícího příkazu OpenSSL:

```
openssl s_client -connect <hostname:port> -cert <path-to-certificate> -key <client-private-key-file> 
```

`-cert`Příznak je listový certifikát, `-key` příznak je soubor privátního klíče klienta. 

Další informace o tom, jak použít příkaz OpenSSL `s_client` , najdete na [stránce ručního](https://www.openssl.org/docs/man1.0.2/man1/openssl-s_client.html)zobrazení.

### <a name="sslclientverify-is-failed"></a>SslClientVerify se nezdařilo

#### <a name="problem"></a>Problém

Vlastnost *sslClientVerify* se v protokolech Access zobrazuje jako "neúspěšná". 

#### <a name="solution"></a>Řešení

Existuje několik potenciálních příčin selhání v protokolech přístupu. Níže je uveden seznam běžných příčin selhání:
* **Nepovedlo se získat certifikát vystavitele:** Certifikát vystavitele klientského certifikátu se nepovedlo najít. To obvykle znamená, že řetěz certifikátů důvěryhodné klientské certifikační autority není na Application Gateway úplný. Ověřte, zda byl dokončen řetěz certifikátů důvěryhodné klientské certifikační autority na Application Gateway.  
* **Nepovedlo se získat certifikát místního vystavitele:** Podobně jako nemůžete získat certifikát vystavitele, certifikát vystavitele klientského certifikátu se nepovedlo najít. To obvykle znamená, že řetěz certifikátů důvěryhodné klientské certifikační autority není na Application Gateway úplný. Ověřte, zda byl dokončen řetěz certifikátů důvěryhodné klientské certifikační autority na Application Gateway.
* **Nepovedlo se ověřit první certifikát:** Certifikát klienta nelze ověřit. K této chybě dochází konkrétně v případě, že klient prezentuje jenom listový certifikát, jehož vystavitel není důvěryhodný. Ověřte, zda byl dokončen řetěz certifikátů důvěryhodné klientské certifikační autority na Application Gateway.
* **Nelze ověřit vystavitele klientského certifikátu:** K této chybě dochází, pokud je konfigurace *VerifyClientCertIssuerDN* nastavena na hodnotu true. K tomu obvykle dochází v případě, že rozlišující název vystavitele certifikátu klienta neodpovídá *ClientCertificateIssuerDN* extrahované z řetězu certifikátů důvěryhodné certifikační autority klienta odeslaného zákazníkem. Další informace o tom, jak Application Gateway extrahuje *ClientCertificateIssuerDN*, najdete v [Application Gateway extrahování rozlišujícího názvu vystavitele](./mutual-authentication-overview.md#verify-client-certificate-dn). Jako osvědčený postup se ujistěte, že nahráváte jeden řetěz certifikátů na soubor Application Gateway. 

Další informace o tom, jak extrahovat celý řetěz certifikátů certifikační autority důvěryhodných klientů pro odeslání do Application Gateway, najdete v tématu [postup extrakce řetězů certifikátů certifikační autority důvěryhodných klientů](./mutual-authentication-certificate-management.md).

## <a name="error-code-troubleshooting"></a>Řešení potíží s kódem chyby
Pokud vidíte některý z následujících kódů chyb, máme několik doporučených řešení, která vám pomůžou vyřešit problém, na který se můžete setkat. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustspecifydata"></a>Kód chyby: ApplicationGatewayTrustedClientCertificateMustSpecifyData

#### <a name="cause"></a>Příčina

Chybí data certifikátu. Nahraný certifikát mohl být prázdný soubor bez dat certifikátu. 

#### <a name="solution"></a>Řešení

Ověřte, že nahraný soubor certifikátu neobsahuje žádná chybějící data. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustnothaveprivatekey"></a>Kód chyby: ApplicationGatewayTrustedClientCertificateMustNotHavePrivateKey

#### <a name="cause"></a>Příčina

V řetězu certifikátů je privátní klíč. V řetězu certifikátů by neměl být privátní klíč. 

#### <a name="solution"></a>Řešení

Překontrolujte řetěz certifikátů, který se nahrál, a odeberte privátní klíč, který byl součástí řetězu. Znovu nahrajte řetězec bez privátního klíče. 

### <a name="error-code-applicationgatewaytrustedclientcertificateinvaliddata"></a>Kód chyby: ApplicationGatewayTrustedClientCertificateInvalidData

#### <a name="cause"></a>Příčina

Za tímto kódem chyby existují dvě možné příčiny.
1. Analýza se nezdařila z důvodu nepředložení řetězce ve správném formátu. Application Gateway očekává, že řetěz certifikátů bude ve formátu PEM a taky očekává, že se jednotlivé údaje o certifikátech mají oddělit. 
2. Analyzátor nenalezl žádné informace k analýze. Nahrané soubory by mohly potenciálně mít jenom oddělovače, ale žádná data certifikátu. 

#### <a name="solution"></a>Řešení

V závislosti na příčině této chyby existují dvě možná řešení. 
* Ověřte, zda byl nahraný řetěz certifikátů ve správném formátu (PEM) a zda byla data certifikátu správně oddělená. 
* Ověřte, zda nahraný soubor certifikátu obsahoval data certifikátu kromě oddělovačů. 

### <a name="error-code-applicationgatewaytrustedclientcertificatedoesnotcontainanycacertificate"></a>Kód chyby: ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate

#### <a name="cause"></a>Příčina

Certifikát nahraný jenom obsahuje listový certifikát bez certifikátu certifikační autority. Nahrávání řetězu certifikátů pomocí certifikátů certifikační autority a listový certifikát se dá přijmout jenom v případě, že bude listový certifikát jenom ignorovaný, ale certifikát musí mít certifikační autoritu. 

#### <a name="solution"></a>Řešení 

Prohlédněte si, že řetěz certifikátů, který se odeslal, obsahuje víc než jenom listový certifikát. Musí existovat rozšíření *BasicConstraintsOid* = "2.5.29.19", které indikuje, že předmět může fungovat jako certifikační autorita.

### <a name="error-code-applicationgatewayonlyonerootcaallowedintrustedclientcertificate"></a>Kód chyby: ApplicationGatewayOnlyOneRootCAAllowedInTrustedClientCertificate

#### <a name="cause"></a>Příčina

Řetěz certifikátů obsahoval více certifikátů kořenové certifikační autority *nebo* obsahovalo nulové kořenové certifikáty certifikační autority. 

#### <a name="solution"></a>Řešení

Nahrané certifikáty musí obsahovat přesně jeden kořenový certifikát certifikační autority (a v případě potřeby ale mnoho certifikátů Zprostředkující certifikační autority).


