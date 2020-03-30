---
title: Vazby certifikátů
description: Vysvětlete četná témata související s certifikáty v prostředí služby App Service. Zjistěte, jak fungují vazby certifikátů v aplikacích s jedním klientem ve službě ASE.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 65fc4ed25b0fd360de8e3b1439d1766485eb2e58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688643"
---
# <a name="certificates-and-the-app-service-environment"></a>Certifikáty a prostředí služby App Service 

Prostředí služby App Service Environment (ASE) je nasazení služby Azure App Service, která běží v rámci vaší virtuální sítě Azure (virtuální sítě). Dá se nasadit s koncovým bodem aplikace přístupné ho k Internetu nebo koncovým bodem aplikace, který je ve vaší virtuální síti. Pokud nasadíte službu ASE s koncovým bodem přístupným pro internet, toto nasazení se nazývá externí služba ASE. Pokud nasadíte službu ASE s koncovým bodem ve vaší virtuální síti, toto nasazení se nazývá služba ASE ILB. Další informace o ase Služby ILB můžete získat z [dokumentu Vytvořit a použít službu ASE iLB.](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)

ASE je jeden klientský systém. Vzhledem k tomu, že je jeden tenant, existují některé funkce k dispozici pouze se službou ASE, které nejsou k dispozici ve službě aplikace s více tenanty. 

## <a name="ilb-ase-certificates"></a>Certifikáty Služby ASE ILB 

Pokud používáte externí službu ASE, vaše aplikace jsou zastižené na [appname]. [asename].p.azurewebsites.net. Ve výchozím nastavení jsou všechny ases, dokonce i ILB ASEs, vytvořeny s certifikáty, které následují tento formát. Pokud máte službu ASE ILB, aplikace jsou dostupné na základě názvu domény, který zadáte při vytváření služby ASE ILB. Aby aplikace podporovaly SSL, musíte nahrát certifikáty. Získejte platný certifikát SSL pomocí interních certifikačních úřadů, zakoupením certifikátu od externího vystavittele nebo pomocí certifikátu podepsaného svým držitelem. 

Existují dvě možnosti konfigurace certifikátů pomocí služby ILB ASE.  Můžete nastavit výchozí certifikát se zástupnými symboly pro službu ASE ILB nebo certifikáty pro jednotlivé webové aplikace v ase.  Bez ohledu na volbu, kterou provedete, musí být správně nakonfigurovány následující atributy certifikátu:

- **Předmět:** Tento atribut musí být nastaven na *. [vaše kořenová doména zde] pro certifikát ase se zástupnými symboly ILB. Pokud vytváříte certifikát pro vaši aplikaci, pak by měl být [appname]. [vaše-root-doména-zde]
- **Alternativní název předmětu:** Tento atribut musí obsahovat obojí *. [your-root-domain-here] a *.scm. [vaše kořenová doména zde] pro certifikát ase se zástupnými symboly ILB. Pokud vytváříte certifikát pro vaši aplikaci, pak by měl být [appname]. [your-root-domain-here] a [appname].scm. [your-root-domain-here].

Jako třetí variantu můžete vytvořit certifikát služby ASE ILB, který obsahuje všechny vaše názvy jednotlivých aplikací v san certifikátu namísto použití zástupného znaku. Problém s touto metodou je, že potřebujete znát předem názvy aplikací, které jsou uvedení do služby ASE nebo je třeba zachovat aktualizaci certifikátu Služby ASE ILB.

### <a name="upload-certificate-to-ilb-ase"></a>Nahrát certifikát do služby ASE ILB 

Po vytvoření služby ASE ILB na portálu musí být certifikát nastaven pro službu ASE ILB. Dokud není certifikát nastaven, zobrazí se ve službě ASE banner, na který nebyl certifikát nastaven.  

Nahraný certifikát musí být soubor .pfx. Po nahrání certifikátu provede ase operace škálování k nastavení certifikátu. 

Nelze vytvořit službu ASE a nahrát certifikát jako jednu akci na portálu nebo dokonce v jedné šabloně. Jako samostatnou akci můžete certifikát nahrát pomocí šablony, jak je popsáno v dokumentu [Vytvořit službu ASE z](./create-from-template.md) dokumentu šablony.  

Pokud chcete rychle vytvořit certifikát podepsaný svým držitelem pro testování, můžete použít následující bit prostředí PowerShell:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
Při vytváření certifikátu podepsaného svým držitelem se budete muset ujistit, že název subjektu má formát CN={ASE_NAME_HERE}_InternalLoadBalancingASE.

## <a name="application-certificates"></a>Certifikáty žádostí 

Aplikace, které jsou hostované ve službě ASE, můžou používat funkce certifikátu zaměřené na aplikace, které jsou dostupné ve víceklientské službě App Service. Mezi tyto funkce patří:  

- Certifikáty SNI 
- Protokol SSL založený na protokolu IP, který je podporován pouze externí službou ASE.  ASE Služby ILB nepodporuje protokol SSL založený na protokolu IP.
- Hostované certifikáty KeyVault 

Pokyny pro nahrávání a správu těchto certifikátů jsou k dispozici v [části Přidání certifikátu SSL ve službě Azure App Service](../configure-ssl-certificate.md).  Pokud jednoduše konfigurujete certifikáty tak, aby odpovídaly vlastnímu názvu domény, který jste přiřadili webové aplikaci, budou tyto pokyny stačit. Pokud nahráváte certifikát pro webovou aplikaci Služby ASE ILB s výchozím názvem domény, zadejte web scm v síti SAN certifikátu, jak bylo uvedeno výše. 

## <a name="tls-settings"></a>Nastavení TLS 

Nastavení TLS můžete nakonfigurovat na úrovni aplikace.  

## <a name="private-client-certificate"></a>Certifikát soukromého klienta 

Běžným případem použití je konfigurace aplikace jako klienta v modelu klienta a serveru. Pokud zabezpečíte server soukromým certifikátem certifikační autority, budete muset klientský certifikát do aplikace nahrát.  Následující pokyny načtou certifikáty do úložiště důvěryhodnosti pracovníků, na kterých vaše aplikace běží. Pokud certifikát načtete do jedné aplikace, můžete ho použít s ostatními aplikacemi ve stejném plánu služby App Service, aniž byste certifikát znovu nahráli.

Jak nahrát certifikát do aplikace ve své ase:

1. Vygenerujte soubor *CER* pro váš certifikát. 
2. Přejděte do aplikace, která potřebuje certifikát na webu Azure Portal.
3. Přejděte v aplikaci do nastavení SSL. Klikněte na Nahrát certifikát. Vyberte Veřejné. Vyberte Místní počítač. Zadejte název. Procházejte a vyberte soubor *CER.* Vyberte nahrát. 
4. Zkopírujte otisk palce.
5. Přejděte do nastavení aplikace. Vytvořte nastavení aplikace WEBSITE_LOAD_ROOT_CERTIFICATES s kryptografickým otiskem jako hodnotou. Pokud máte více certifikátů, můžete je umístit do stejného nastavení odděleného čárkami a bez mezer, jako je 

    84EC242A4EC7957817B8E48913E509535552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Certifikát bude k dispozici všem aplikacím ve stejném plánu služby App Service jako aplikace, která toto nastavení nakonfigurovala. Pokud potřebujete, aby byla dostupná pro aplikace v jiném plánu služby App Service, budete muset zopakovat operaci Nastavení aplikace v aplikaci v tomto plánu služby App Service. Chcete-li zkontrolovat, zda je certifikát nastavený, přejděte na konzolu Kudu a v konzole ladění prostředí PowerShell vyjevte následujícím příkazem:

    dir cert:\localmachine\root

Chcete-li provést testování, můžete vytvořit vlastní podepsaný certifikát a vygenerovat soubor *CER* s následujícím prostředím PowerShell: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

