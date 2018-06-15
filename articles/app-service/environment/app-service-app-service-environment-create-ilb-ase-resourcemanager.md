---
title: Postup vytvoření ILB App Service Environment pomocí šablony Azure Resource Manageru | Microsoft Docs
description: Naučte se vytvořit interní služby load vyrovnávání App Service Environment pomocí šablony Azure Resource Manager.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.openlocfilehash: ea9407208f1bf555cf1a6d166825896dec89ec29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836851"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Vytvoření ILB ASE pomocí šablon Azure Resource Manageru

> [!NOTE] 
> Tento článek je o v1 App Service Environment. Existuje novější verze App Service Environment, který je jednodušší použít a běží na výkonnější infrastruktury. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
>

## <a name="overview"></a>Přehled
Služby App Service Environment může být vytvořen pomocí adresu interní virtuální sítě místo veřejných virtuálních IP adres.  Tato interní adresa zajišťuje komponentu Azure, názvem vyrovnávání interní zatížení (ILB).  App Service Environment ILB lze vytvořit pomocí portálu Azure.  Je také možné vytvářet pomocí automatizace prostřednictvím šablon Azure Resource Manageru.  Tento článek vás provede kroky a syntaxe, které jsou potřebné k vytvoření ILB App Service Environment pomocí šablony Azure Resource Manager.

Účastnící se automatizace vytváření ILB App Service Environment jsou tři kroky:

1. Nejprve základní App Service Environment se vytvoří ve virtuální síti pomocí adresu služby Vyrovnávání zatížení interní místo veřejných virtuálních IP adres.  V rámci tohoto kroku se App Service Environment ILB přiřazen název kořenové domény.
2. Po vytvoření App Service Environment ILB je nahrát certifikát SSL.  
3. Nahraný certifikát SSL je explicitně přiřadit App Service Environment ILB jako certifikát SSL jeho "Výchozí".  Tento certifikát SSL se použije pro přenosy protokolu SSL na aplikace v App Service Environment ILB, když jsou aplikace řešit pomocí běžných kořenové domény přiřazené App Service Environment (např. https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Vytvoření základní ILB App Service Environment
Příklad šablony Azure Resource Manageru a jeho přidružené parametry souboru jsou dostupné na Githubu [sem][quickstartilbasecreate].

Většina z parametrů v *azuredeploy.parameters.json* souboru jsou společné pro vytváření ILB ASEs, jak ASEs vázána na veřejných virtuálních IP adres.  V seznamu níže volá metodu výstupní parametry speciální Poznámka nebo které jsou jedinečné, při vytváření ILB App Service Environment:

* *interalLoadBalancingMode*: ve většině případů to do 3, to znamená přenosy HTTP/HTTPS na porty 80/443 i data ovládacího prvku nebo porty channel naslouchali pomocí služby FTP v App Service Environment, sada bude vázán k ILB přidělené interní virtuální sítě Adresa.  Pokud místo toho tato vlastnost nastavena na 2, pak pouze službu FTP související s porty (řízení a datové kanály) budou vázána na adresu ILB, zatímco zůstane nainstalována na veřejné VIP přenosy HTTP/HTTPS.
* *dnsSuffix*: Tento parametr definuje výchozí kořenové domény, který se přiřadí App Service Environment.  Ve veřejné změna Azure App Service, Výchozí kořenová doména pro všechny webové aplikace je *azurewebsites.net*.  Ale vzhledem k tomu, že je interní virtuální sítě zákazníka App Service Environment ILB, nemá smysl použít veřejné služby výchozí kořenové domény.  Místo toho ILB App Service Environment, musí mít výchozí kořenové domény, která je vhodná pro použití v rámci společnosti interní virtuální sítě.  Společnosti Contoso Corporation může například použít výchozí kořenovou doménou *interní contoso.com* pro aplikace, které jsou určeny pouze jako přeložit a dostupné v rámci společnosti Contoso virtuální sítě. 
* *ipSslAddressCount*: Tento parametr se automaticky převezme na hodnotu 0 v *azuredeploy.json* soubor protože ILB ASEs mít pouze jednu adresu ILB.  Nejsou žádné explicitní IP SSL adresy pro ILB App Service Environment a proto fondu IP SSL adresy pro ILB App Service Environment musí být nastavena na hodnotu nula, jinak dojde k chybě při zřizování. 

Jednou *azuredeploy.parameters.json* souboru bylo vyplněno pro App Service Environment ILB, App Service Environment ILB mohou být vytvořeny pomocí následující fragment kódu prostředí Powershell.  Změňte soubor cesty, které se shodují, kde jsou umístěny soubory šablon Azure Resource Manager na váš počítač.  Nezapomeňte také zadat vlastní hodnoty pro název nasazení Azure Resource Manager a název skupiny prostředků.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po Azure Resource Manager šablony je odeslán, že bude trvat několik hodin, než App Service Environment ILB vytvořit.  Po dokončení vytvoření App Service Environment ILB se zobrazí na portálu činnosti koncového uživatele v seznamu prostředí App Service pro předplatné, který aktivoval nasazení.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Odesílání a konfiguraci "Výchozí" certifikát SSL
Po vytvoření App Service Environment ILB certifikát SSL by měly být přidružené App Service Environment, jako "Výchozí" certifikát SSL použijte pro vytvoření připojení SSL k aplikacím.  Pokračováním společnosti Contoso Corporation příklad, pokud App Service Environment výchozí DNS přípona je *interní contoso.com*, pak připojení k *https://some-random-app.internal-contoso.com* vyžaduje certifikát SSL, který je platný pro **.internal contoso.com*. 

Existuje mnoho různých způsobů, jak získat platný certifikát SSL včetně interní certifikační autority, nákupu certifikát z externího vystavitelů a používá certifikát podepsaný svým držitelem.  Bez ohledu na zdroji certifikátu protokolu SSL musí být správně nakonfigurované následující atributy certifikátu:

* *Předmět*: Tento atribut musí být nastaven na **.your kořenové domény here.com*
* *Alternativní název subjektu*: Tento atribut musí obsahovat i **.your kořenové domény here.com*, a **.scm.your-kořenové-domain-here.com*.  Důvod pro druhou položku je, že připojení SSL k webu SCM/Kudu spojené s každou aplikaci, bude provedeno pomocí adresu ve tvaru *your-app-name.scm.your-root-domain-here.com*.

S platným certifikátem SSL v dolním je potřeba dva další přípravné kroky.  Certifikát SSL musí být uložený jako soubor .pfx s jejich převést.  Mějte na paměti, že soubor .pfx musí zahrnovat všechny zprostředkující a kořenové certifikáty a také musí být zabezpečené pomocí hesla.

Potom soubor .pfx výsledné potřeba převést na řetězec ve formátu base64, protože certifikát SSL, nebude možné odesílat pomocí šablony Azure Resource Manager.  Vzhledem k tomu, že šablony Azure Resource Manager jsou textové soubory, je potřeba převést na řetězec ve formátu base64, může být zahrnuta jako parametr šablony soubor .pfx.

Následující fragment kódu prostředí Powershell ukazuje příklad generování certifikát podepsaný svým držitelem, Export certifikátu jako soubor .pfx, převodu soubor .pfx s kódováním base64 řetězec kódovaný, a potom uložení Base64, pomocí kódovaný řetězec do samostatného souboru.  Kód prostředí Powershell pro kódování base64 byla přizpůsobena z [Blog skripty prostředí Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Jakmile se certifikát SSL byl úspěšně generované a převést na s kódováním base64 řetězce, příklad šablony Azure Resource Manager na GitHub pro [konfigurace certifikátu protokolu SSL výchozí] [ configuringDefaultSSLCertificate] můžete použít.

Parametry v *azuredeploy.parameters.json* souboru jsou uvedeny níže:

* *appServiceEnvironmentName*: název ILB App Service Environment se právě nastavuje.
* *existingAseLocation*: textový řetězec obsahující oblasti Azure, kde byla nasazena App Service Environment ILB.  Například: "Jihu USA".
* *pfxBlobString*: based64 kódovaný řetězcovou reprezentaci soubor .pfx.  Pomocí fragmentu kódu, která je uvedena výše, by řetězec obsažené v "exportedcert.pfx.b64" zkopírujte a vložte jej jako hodnotu *pfxBlobString* atribut.
* *heslo*: heslo používané k zabezpečení soubor .pfx.
* *certificateThumbprint*: kryptografický otisk certifikátu.  Pokud načetlo tuto hodnotu z prostředí Powershell (například *$certificate. Kryptografický otisk* z dřívějších fragment kódu), můžete použít hodnotu jako-je.  Při kopírování hodnotu z dialogového okna certifikátu Windows, ale musíte odebrat nadbytečné mezery.  *CertificateThumbprint* by měl vypadat podobně jako: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: identifikátor popisný řetězec podle vlastní volby umožňuje identity certifikát.  Název se používá jako součást jedinečný identifikátor Azure Resource Manager *Microsoft.Web/certificates* entity představující certifikát SSL.  Název **musí** končit následující příponu: \_yourASENameHere_InternalLoadBalancingASE.  Tato přípona se používá portálem jako indikátoru, certifikát se používá k zabezpečení App Service Environment povolené ILB.

Příklad zkrácené *azuredeploy.parameters.json* jsou uvedeny níže:

    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Jednou *azuredeploy.parameters.json* bylo vyplněno souboru, výchozí certifikát SSL můžete konfigurovat pomocí následující fragment kódu prostředí Powershell.  Změňte soubor cesty, které se shodují, kde jsou umístěny soubory šablon Azure Resource Manager na váš počítač.  Nezapomeňte také zadat vlastní hodnoty pro název nasazení Azure Resource Manager a název skupiny prostředků.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po odeslání šablony Azure Resource Manageru, že bude trvat přibližně 40 minutách minut za App Service Environment front-end použití změny.  Například s výchozí velikost App Service Environment pomocí dvou front-end, šablona bude trvat přibližně jednu hodinu a dvacet minut.  Šablony spuštěného App Service Environment nebude možné škálovat.  

Po dokončení šablony aplikace v App Service Environment ILB je přístupná prostřednictvím protokolu HTTPS a nebude zabezpečené připojení pomocí výchozí certifikát SSL.  Certifikát protokolu SSL výchozí se použije, když jsou aplikace v App Service Environment ILB řešit pomocí kombinace názvu aplikace a výchozí název hostitele.  Například *https://mycustomapp.internal-contoso.com* využije výchozí certifikát SSL pro **.internal contoso.com*.

Ale stejně jako aplikace běžící na veřejné víceklientské služby, vývojáři můžou také nakonfigurovat vlastní hostitel názvy pro jednotlivé aplikace a pak nakonfigurujte jedinečný vazby certifikátů SNI SSL pro jednotlivé aplikace.  

## <a name="getting-started"></a>Začínáme
Chcete-li začít pracovat s prostředí App Service, přečtěte si téma [Úvod do služby App Service Environment](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

