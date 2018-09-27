---
title: Certifikáty a Azure App Service Environment
description: Popisují mnoho témat souvisejících s certifikáty ve službě ASE
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.openlocfilehash: 3d417d560d8a88100f31def27c7db5f9b2493062
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47167889"
---
# <a name="certificates-and-the-app-service-environment"></a>Certifikáty a App Service Environment 

App Service Environment je nasazení služby Azure App Service, který běží v rámci vaší virtuální sítě Azure. Je možné nasadit pomocí koncového bodu přístupné aplikace Internetu nebo koncový bod aplikace, která je ve vaší virtuální síti. Pokud nasadíte službu ASE s internet přístupném koncovém bodu, se nazývá tohoto nasazení externí služby ASE. Pokud nasadíte službu ASE s koncovým bodem ve vaší virtuální síti, se nazývá tohoto nasazení službu ASE. Další informace o služba ASE s ILB z [vytvoření a použití prostředí ILB ASE](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase) dokumentu.

Služba ASE je systém s jedním tenantem. Protože se jedná jednoho tenanta, existují určité funkce k dispozici pouze s ASE, které nejsou k dispozici v App Service pro více tenantů. 

## <a name="ilb-ase-certificates"></a>Služba ASE s ILB certifikáty 

Pokud používáte externí služby ASE, se dosáhne své aplikace na [NázevAplikace]. [asename]. p.azurewebsites.net. Ve výchozím nastavení jsou všechny služby ase, dokonce i služeb ase s ILB, vytvořenou s certifikáty, které dodržovat tento formát. Pokud máte službu ASE, se dosáhne aplikace na základě názvu domény, který jste zadali při vytvoření ILB ASE. Aby aplikace pro podporu protokolu SSL budete muset nahrát certifikáty. Získáte platný certifikát protokolu SSL pomocí interní certifikační autority, nákupu certifikát od externího vystavitele nebo pomocí certifikátu podepsaného svým držitelem. 

Existují dvě možnosti pro konfiguraci certifikátů se vaše služba ASE s ILB.  Můžete nastavit výchozí certifikát se zástupným znakem pro ILB ASE nebo nastavit certifikáty pro jednotlivé webové aplikace ve službě ASE.  Bez ohledu na volbu, které provedete musí být správně nakonfigurované následující atributy certifikátu:

- **Předmět:** tento atribut musí být nastaven *. [ your kořenové zdejsi korenova] pro certifikát se zástupným znakem služba ASE s ILB. Když vytváří se certifikát pro vaši aplikaci, měla by být [NázevAplikace]. [your zdejsi korenova]
- **Alternativní název subjektu:** tento atribut musí obsahovat *. [ your zdejsi korenova] a *.scm. [your root zdejsi korenova] certifikátu služba ASE s ILB zástupný znak. Když vytváří se certifikát pro vaši aplikaci, měla by být [NázevAplikace]. [your zdejsi korenova] a [NázevAplikace] .scm. [your zdejsi korenova].

Jako třetí typ variant můžete vytvořit certifikát služba ASE s ILB, který obsahuje všechny názvy jednotlivých aplikací v síti SAN namísto použití zástupných znaků odkaz certifikátu. Problém s touto metodou je, že budete muset předem znát názvy aplikací, které ukládáte ve službě ASE nebo musíte aktualizovat certifikát služba ASE s ILB.

### <a name="upload-certificate-to-ilb-ase"></a>Nahrajte certifikát do prostředí ILB ASE 

Po vytvoření ILB ASE na portálu musí být nastavena certifikát pro služba ASE s ILB. Dokud se nenastaví certifikát služby ASE se zobrazí banner, že certifikát nebyl nastaven.  

Certifikát, který nahrajete, musí být soubor .pfx. Po nahrání certifikátu služby ASE bude provádět operace škálování na nastavit certifikát. 

Nelze vytvořit službu ASE a nahrajte certifikát jako jednu akci na portálu nebo dokonce i v jedné šabloně. Jako samostatnou akci, můžete nahrát certifikát s použitím šablony, jak je popsáno v [vytvoření ASE ze šablony](./create-from-template.md) dokumentu.  

Pokud chcete vytvořit certifikát podepsaný svým rychle pro testování, můžete použít následující verze prostředí PowerShell:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     


## <a name="application-certificates"></a>Certifikáty k aplikaci 

Aplikace, které jsou hostované ve službě ASE můžou používat funkce zaměřený na aplikace certifikátů, které jsou k dispozici v App Service pro více tenantů. Mezi tyto funkce patří:  

- Certifikátů SNI 
- SSL na základě IP adresy, které jsou podporovány pouze pro externí služby ASE.  Službu ASE nepodporuje založené na protokolu IP SSL.
- Certifikáty hostované služby KeyVault 

Pokyny pro nahrání a správu těchto certifikátů jsou k dispozici v tomto kurzu SSL služby App https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl.  Pokud konfigurujete jednoduše certifikáty tak, aby odpovídaly vlastního názvu domény, který jste přiřadili do vaší webové aplikace, bude stačit tyto pokyny. Pokud nahráváte certifikát pro webovou aplikaci služby ILB ASE pomocí výchozí název domény, zadejte web scm v síti SAN certifikátu, protože jste si předtím poznamenali. 

## <a name="tls-settings"></a>Nastavení protokolu TLS 

Na úrovni aplikace můžete nakonfigurovat nastavení protokolu TLS.  

## <a name="private-client-certificate"></a>Privátní klientského certifikátu 

Běžným případem použití je konfigurace aplikace pro klienta v modelu klient server. Pokud je zabezpečit váš server s privátní certifikát certifikační Autority, musíte nahrát na server certifikát klienta do vaší aplikace.  Postupujte podle následujících pokynů načte truststore pracovní procesy, které vaše aplikace běží na certifikáty. Při načítání certifikátu na jednu aplikaci, můžete ho použít s vašimi aplikacemi v rámci stejného plánu služby App Service bez odeslat certifikát znovu.

Nahrání certifikátu do vaší aplikace ve vaší službě ASE:

1. Generování *.cer* soubor certifikátu. 
2. Přejít do aplikace, který potřebuje certifikát na webu Azure Portal
3. Přejděte na nastavení SSL v aplikaci. Klikněte na tlačítko Nahrát certifikát. Vyberte veřejné. Vyberte místní počítač. Zadejte název. Procházet a vyberte váš *.cer* souboru. Výběr nahrání. 
4. Zkopírujte kryptografický otisk.
5. Přejděte do nastavení aplikace. Vytvoření WEBSITE_LOAD_ROOT_CERTIFICATES nastavení aplikace s kryptografickým otiskem jako hodnotu. Pokud máte víc certifikátů, je umístíte do stejné nastavení oddělená čárkami a žádné prázdné znaky, jako jsou 

    84EC242A4EC7957817B8E48913E50953552DAFA6 6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Tento certifikát bude k dispozici ve všech aplikacích ve stejném plánu služby app service jako aplikace, která se toto nastavení nakonfigurované. Pokud je nutné být k dispozici pro aplikace v různých plán služby App Service, musíte opakovat operace nastavení aplikace, které v aplikaci v tomto plánu služby App Service. Pokud chcete zkontrolovat, že je nastavena certifikát, přejděte do konzoly Kudu a vydávání tohoto příkazu dir cert: \localmachine\root v konzole ladění Powershellu. 

Provést testování, můžete vytvořit certifikát podepsaný svým a vygenerovat *.cer* soubor s následující příkaz Powershellu: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

