---
title: Vazby certifikátů
description: Vysvětlete nejrůznější témata týkající se certifikátů v App Service Environment. Přečtěte si, jak vazby certifikátů fungují na aplikacích s jedním klientem v pomocném mechanismu.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 306445e26e5b236b49273b9ab8888ecc610bc075
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962039"
---
# <a name="certificates-and-the-app-service-environment"></a>Certifikáty a App Service Environment 

App Service Environment (pomocného programu) je nasazení Azure App Service, které běží v rámci služby Azure Virtual Network (VNet). Dá se nasadit pomocí koncového bodu aplikace přístupného pro Internet nebo koncového bodu aplikace, který je ve vaší virtuální síti. Pokud nasadíte službu pomocného bodu s dostupným koncovým bodem Internetu, toto nasazení se nazývá externí přístupový objekt. Pokud nasadíte službu pomocného bodu s koncovým bodem ve vaší virtuální síti, toto nasazení se nazývá interního nástroje pomocného programu. Další informace o interního nástroje pomocného mechanismu pro vytváření najdete v dokumentu [Vytvoření a použití interního nástroje](./create-ilb-ase.md) .

Pomocným mechanismem je jeden klientský systém. Vzhledem k tomu, že se jedná o jediného tenanta, jsou k dispozici pouze některé funkce s pomocným mechanismem řízení, které nejsou k dispozici ve víceklientské App Service. 

## <a name="ilb-ase-certificates"></a>Certifikáty interního nástroje pomocného mechanismu 

Pokud používáte externí pomocného mechanismu pro přístup, vaše aplikace se dosáhnou v [AppName]. [asename]. p. azurewebsites. NET. Ve výchozím nastavení jsou všechny služby ASE, dokonce i interního nástroje služby ASE, vytvořené pomocí certifikátů, které následují po tomto formátu. Když máte interního nástroje pomocného nástroje, aplikace se dosáhnou na základě názvu domény, který zadáte při vytváření interního nástroje pro pomocného mechanismu. Aby aplikace podporovaly protokol TLS, je nutné nahrávat certifikáty. Získejte platný certifikát TLS/SSL pomocí interních certifikačních autorit, zakoupením certifikátu od externího vystavitele nebo pomocí certifikátu podepsaného svým držitelem. 

Existují dvě možnosti konfigurace certifikátů pomocí pomocného programu interního nástroje.  Můžete nastavit výchozí certifikát se zástupnými znaky pro interního nástroje pomocného uživatele nebo nastavit certifikáty pro jednotlivé webové aplikace v pomocném panelu.  Bez ohledu na to, jakou možnost provedete, musí být správně nakonfigurované následující atributy certifikátu:

- **Předmět:** Tento atribut musí být nastaven na hodnotu *. [vaše kořenová doména – tady] pro certifikát se zástupným MECHANISMem interního nástroje. Pokud vytváříte certifikát pro vaši aplikaci, měla by být [AppName]. [vaše kořenová doména – tady]
- **Alternativní název subjektu:** Tento atribut musí zahrnovat *. [vaše kořenová-doména-sem] a *. SCM. [vaše kořenová doména-tady] pro certifikát interního nástroje pomocného certifikátu. Pokud vytváříte certifikát pro vaši aplikaci, měla by být [AppName]. [vaše-kořenová-doména-sem] a [AppName]. SCM. [vaše kořenová-doména-zde].

Třetí varianta vám umožní vytvořit certifikát interního nástroje pomocného programu, který bude obsahovat všechny názvy jednotlivých aplikací v síti SAN certifikátu namísto použití zástupné odkazy. Problém s touto metodou je, že potřebujete znát název aplikací, které umístíte do pomocného mechanismu, nebo musíte aktualizovat certifikát interního nástroje pomocného programu.

### <a name="upload-certificate-to-ilb-ase"></a>Nahrát certifikát do interního nástroje pomocného mechanismu 

Po vytvoření pomocného interního NÁSTROJEu na portálu se musí nastavit certifikát pro pomocného mechanismu interního nástroje. Dokud se certifikát nenastaví, zobrazí se v pomocném okně zpráva, že certifikát nebyl nastaven.  

Certifikát, který nahrajete, musí být soubor. pfx. Po nahrání certifikátu pomocného modulu pro nastavení certifikátu provede operaci škálování. 

Nemůžete vytvořit pomocného mechanismu řízení a nahrát ho jako jednu akci na portálu nebo dokonce i v jedné šabloně. V rámci samostatné akce můžete certifikát nahrát pomocí šablony, jak je popsáno v tématu [Vytvoření POmocného programu z dokumentu šablony](./create-from-template.md) .  

Pokud chcete rychle vytvořit certifikát podepsaný svým vlastníkem pro účely testování, můžete použít tento bit prostředí PowerShell:

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password
```

Při vytváření certifikátu podepsaného svým vlastníkem budete muset ověřit, že název subjektu má formát CN = {ASE_NAME_HERE} _InternalLoadBalancingASE.

## <a name="application-certificates"></a>Certifikáty aplikací 

Aplikace, které jsou hostované v pomocném formuláři, můžou používat funkce certifikátu zaměřené na aplikace, které jsou k dispozici ve víceklientské App Service. Mezi tyto funkce patří:  

- Certifikáty SNI 
- Protokol SSL založený na protokolu IP, který je podporován pouze s externím MECHANISMem pro čtení.  INTERNÍHO nástroje pomocného mechanismu nepodporuje protokol SSL založený na protokolu IP.
- Hostované certifikáty trezoru klíčů 

Pokyny pro nahrávání a správu těchto certifikátů jsou k dispozici v tématu [Přidání certifikátu TLS/SSL v Azure App Service](../configure-ssl-certificate.md).  Pokud jednoduše konfigurujete certifikáty tak, aby odpovídaly vlastnímu názvu domény, který jste přiřadili k vaší webové aplikaci, budou tyto pokyny stačit. Pokud nahráváte certifikát pro webovou aplikaci interního nástroje pomocného programu pro pojmenování s výchozím názvem domény, zadejte web SCM v síti SAN certifikátu, jak je uvedeno výše. 

## <a name="tls-settings"></a>Nastavení TLS 

Nastavení TLS můžete nakonfigurovat na úrovni aplikace.  

## <a name="private-client-certificate"></a>Certifikát privátního klienta 

Běžným případem použití je konfigurace aplikace jako klienta v modelu klient-server. Pokud server zabezpečíte pomocí privátního certifikátu certifikační autority, budete muset klientský certifikát nahrát do své aplikace.  Následující pokyny budou načítat certifikáty do truststore pracovníků, na kterých vaše aplikace běží. Pokud certifikát načtete do jedné aplikace, můžete ho použít spolu s ostatními aplikacemi ve stejném App Service plánu, aniž byste museli znovu odeslat certifikát.

Pokud chcete nahrát certifikát do vaší aplikace v pomocném mechanismu služby:

1. Vygenerujte soubor *. cer* pro váš certifikát. 
2. Přejít do aplikace, která potřebuje certifikát, v Azure Portal
3. V aplikaci přejdete na nastavení SSL. Klikněte na Nahrát certifikát. Vyberte Veřejný. Vyberte místní počítač. Zadejte název. Procházejte a vyberte svůj soubor *. cer* . Vyberte nahrát. 
4. Zkopírujte kryptografický otisk.
5. Přejít na nastavení aplikace. Vytvoří nastavení aplikace WEBSITE_LOAD_ROOT_CERTIFICATES s kryptografickým otiskem jako hodnotou. Pokud máte více certifikátů, můžete je umístit do stejného nastavení oddělené čárkami a bez mezer jako 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Certifikát bude k dispozici pro všechny aplikace ve stejném plánu služby App Service jako aplikace, která nakonfiguruje toto nastavení. Pokud potřebujete, aby byla k dispozici pro aplikace v jiném plánu App Service, bude nutné zopakovat operaci nastavení aplikace v aplikaci v tomto plánu App Service. Pokud chcete ověřit, že je certifikát nastavený, otevřete konzolu Kudu a vydejte následující příkaz v konzole ladění PowerShellu:

```azurepowershell-interactive
dir cert:\localmachine\root
```

K provedení testování můžete vytvořit certifikát podepsaný svým vlastníkem a vygenerovat soubor *. cer* pomocí následujícího prostředí PowerShell: 

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.cer"
export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
```