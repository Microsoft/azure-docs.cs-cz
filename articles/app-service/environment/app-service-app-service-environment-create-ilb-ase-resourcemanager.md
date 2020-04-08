---
title: Vytvoření služby ASE ILB v1
description: Vytvořte prostředí služby App Service s interním vyrovnáváním zatížení (Služba ASE Služby ILB). Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: f05780610a2a6033b069721b143aca5e5efa6c35
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804516"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Vytvoření ILB ASE pomocí šablon Azure Resource Manageru

> [!NOTE] 
> Tento článek je o prostředí služby App Service v1. K dispozici je novější verze prostředí služby App Service, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi začněte [s úvodem do prostředí služby App Service](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
Prostředí služby App Service lze vytvořit s interní adresou virtuální sítě namísto veřejné vip adresy.  Tato interní adresa je poskytována součástí Azure s názvem interní vyrovnávání zatížení (ILB).  ASE ILB lze vytvořit pomocí portálu Azure.  Můžete také vytvořit pomocí automatizace pomocí šablon Azure Resource Manager.  Tento článek vás provede kroky a syntaxe potřebné k vytvoření služby ASE ILB se šablonami Azure Resource Manager.

Existují tři kroky při automatizaci vytváření služby ASE ILB:

1. Nejprve základní služby ASE je vytvořen ve virtuální síti pomocí interní adresu nástroje pro vyrovnávání zatížení namísto veřejné VIP.  V rámci tohoto kroku je kořenový název domény přiřazen ke správě Služby ASE ILB.
2. Po vytvoření služby ASE ILB je odeslán certifikát TLS/SSL.  
3. Nahraný certifikát TLS/SSL je explicitně přiřazen ase Služby ILB jako jeho "výchozí" certifikát TLS/SSL.  Tento certifikát TLS/SSL se použije pro přenos TLS do aplikací ve službě ILB ASE, když jsou aplikace adresovány pomocí společné kořenové domény přiřazené službě ASE (např. `https://someapp.mycustomrootcomain.com`)

## <a name="creating-the-base-ilb-ase"></a>Vytvoření základní služby ALB ASE
Příklad šablony Azure Resource Manager a jeho přidružené parametry souboru, jsou k dispozici na GitHub [zde][quickstartilbasecreate].

Většina parametrů v souboru *azuredeploy.parameters.json* je společná k vytvoření ilb ASEs, stejně jako ASEs vázané na veřejné VIP.  Níže uvedený seznam volá parametry zvláštní poznámky, nebo které jsou jedinečné, při vytváření služby ASE ILB:

* *internalLoadBalancingMode*: Ve většině případů nastavte tuto možnost na 3, což znamená, že přenosy HTTP/HTTPS na portech 80/443 a porty řídicího/datového kanálu naslouchané službou FTP ve službě ASE budou vázány na interní adresu virtuální sítě přidělenou službou ILB.  Pokud je tato vlastnost místo toho nastavena na 2, budou pouze porty související se službou FTP (řídicí i datové kanály) vázány na adresu ILB, zatímco provoz HTTP/HTTPS zůstane ve veřejné virtuální ip službě.
* *dnsSuffix*: Tento parametr definuje výchozí kořenovou doménu, která bude přiřazena službě ASE.  Ve veřejné variantě služby Azure App Service je výchozí kořenová doména pro všechny webové aplikace *azurewebsites.net*.  Nicméně vzhledem k tomu, že služba ASE ILB je interní ve virtuální síti zákazníka, nemá smysl používat výchozí kořenovou doménu veřejné služby.  Místo toho by služba ASE ILB měla mít výchozí kořenovou doménu, která má smysl pro použití v interní virtuální síti společnosti.  Hypotetická společnost Contoso Corporation může například používat výchozí kořenovou doménu *internal-contoso.com* pro aplikace, které jsou určeny pouze k řešení a přístupným v rámci virtuální sítě společnosti Contoso. 
* *ipSslAddressCount*: Tento parametr je automaticky výchozí hodnotu 0 v souboru *azuredeploy.json,* protože ILB ASEs mají pouze jednu adresu ILB.  Neexistují žádné explicitní adresy IP-SSL pro službu ASE ILB, a proto musí být fond adres IP-SSL pro službu ASE ILB nastaven na nulu, jinak dojde k chybě zřizování. 

Po vyplnění souboru *azuredeploy.parameters.json* pro službu ASE ILB lze službu ASE ILB vytvořit pomocí následujícího fragmentu kódu prostředí Powershell.  Změňte soubor PATHs tak, aby odpovídaly, kde jsou umístěny soubory šablon Azure Resource Manager ve vašem počítači.  Nezapomeňte také zadat vlastní hodnoty pro název nasazení Azure Resource Manager a název skupiny prostředků.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po odeslání šablony Azure Resource Manager bude trvat několik hodin pro Vytvoření služby ASE ILB.  Po dokončení vytváření se služba ASE ILB zobrazí na portálu UX v seznamu prostředí služby App Service pro předplatné, které spustilo nasazení.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Nahrávání a konfigurace výchozího certifikátu TLS/SSL
Po vytvoření služby ASE ILB by měl být certifikát TLS/SSL přidružen ke službě ASE jako "výchozí" použití certifikátu TLS/SSL pro navazování připojení TLS/SSL k aplikacím.  Pokračování v hypotetickém příkladu contoso corporation, pokud je výchozí *internal-contoso.com*přípona DNS *https://some-random-app.internal-contoso.com* služby ASE internal-contoso.com , pak připojení vyžaduje certifikát TLS/SSL, který je platný pro **.internal-contoso.com*. 

Existuje celá řada způsobů, jak získat platný certifikát TLS/SSL, včetně interních certifikačních autorit, zakoupení certifikátu od externího vystavittele a použití certifikátu podepsaného svým držitelem.  Bez ohledu na zdroj certifikátu TLS/SSL je třeba správně nakonfigurovat následující atributy certifikátu:

* *Předmět*: Tento atribut musí být nastaven na **.your-root-domain-here.com*
* *Alternativní název předmětu*: Tento atribut musí obsahovat **.your-root-domain-here.com*a **.scm.your-root-domain-here.com*.  Důvodem pro druhou položku je, že Připojení TLS k webu SCM/Kudu přidruženému ke každé aplikaci budou provedena pomocí adresy formuláře *your-app-name.scm.your-root-domain-here.com*.

S platným certifikátem TLS/SSL v ruce jsou zapotřebí další dva přípravné kroky.  Certifikát TLS/SSL je třeba převést/uložit jako soubor .pfx.  Nezapomeňte, že soubor .pfx musí obsahovat všechny zprostředkující a kořenové certifikáty a také musí být zabezpečen heslem.

Výsledný soubor .pfx pak musí být převeden na řetězec base64, protože certifikát TLS/SSL se nahraje pomocí šablony Azure Resource Manager.  Vzhledem k tomu, že šablony Azure Resource Manager jsou textové soubory, soubor .pfx je třeba převést na řetězec base64, aby mohl být zahrnut jako parametr šablony.

Fragment kódu prostředí Powershell udává příklad generování certifikátu podepsaného svým držitelem, exportu certifikátu jako souboru Pfx, převodu souboru Pfx na kódovaný řetězec base64 a uložení kódu base64 do samostatného souboru.  Kód Powershellu pro kódování base64 byl upraven z [blogu Powershell Scripts .][examplebase64encoding]

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Jakmile je certifikát TLS/SSL úspěšně vygenerován a převeden na kódovaný řetězec base64, lze použít ukázkovou šablonu Azure Resource Manager na GitHubu pro [konfiguraci výchozího certifikátu TLS/SSL.][configuringDefaultSSLCertificate]

Parametry v souboru *azuredeploy.parameters.json* jsou uvedeny níže:

* *appServiceEnvironmentName*: Název nakonfigurované služby ASE ILB.
* *existingAseLocation*: Textový řetězec obsahující oblast Azure, kde byla nasazena služba ASE ILB.  Například: "Jižní střed USA".
* *pfxBlobString*: Reprezentace řetězce .pfx 64.  Pomocí fragmentu kódu, který byl zobrazen dříve, byste zkopírovali řetězec obsažený v souboru "exportedcert.pfx.b64" a vložili jej jako hodnotu atributu *pfxBlobString.*
* *heslo*: Heslo používané k zabezpečení souboru .pfx.
* *certificateThumbprint*: Kryptografický otisk certifikátu.  Pokud tuto hodnotu načtete z aplikace Powershell (např. *$certificate. Kryptografický otisk* z předchozího fragmentu kódu) můžete použít hodnotu tak, jak je.  Pokud však zkopírujete hodnotu z dialogového okna Certifikát systému Windows, nezapomeňte odstranit cizí mezery.  *CertifikátOtisk palce* by měl vypadat nějak jako: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *název certifikátu*: Popisný identifikátor řetězce, který si vlastní zvolíte k identifikaci certifikátu.  Název se používá jako součást jedinečného identifikátoru Správce prostředků Azure pro entitu *Microsoft.Web/certificates* představující certifikát TLS/SSL.  Název **musí** končit následující příponou: \_yourASENameHere_InternalLoadBalancingASE.  Tato přípona se používá portál jako indikátor, že certifikát se používá pro zabezpečení služby ASE s povolenou ILB.

Zkrácený příklad *azuredeploy.parameters.json* je uveden níže:

    {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
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

Po vyplnění souboru *azuredeploy.parameters.json* lze výchozí certifikát TLS/SSL nakonfigurovat pomocí následujícího fragmentu kódu Powershellu.  Změňte soubor PATHs tak, aby odpovídaly, kde jsou umístěny soubory šablon Azure Resource Manager ve vašem počítači.  Nezapomeňte také zadat vlastní hodnoty pro název nasazení Azure Resource Manager a název skupiny prostředků.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po odeslání šablony Azure Resource Manager bude trvat zhruba čtyřicet minut na front-end služby ASE použít změnu.  Například s výchozí velikosti služby ASE pomocí dvou front-endů, šablona bude trvat přibližně jednu hodinu a dvacet minut na dokončení.  Při spuštění šablony ase nebude moci škálovat.  

Po dokončení šablony lze k aplikacím služby ILB ase přistupovat přes protokol HTTPS a připojení budou zabezpečena pomocí výchozího certifikátu TLS/SSL.  Výchozí certifikát TLS/SSL se použije, když jsou aplikace ve službě ILB ASE adresovány pomocí kombinace názvu aplikace a výchozího názvu hostitele.  Například *https://mycustomapp.internal-contoso.com* použít výchozí certifikát TLS/SSL pro **.internal-contoso.com*.

Stejně jako aplikace spuštěné ve veřejné službě s více tenanty však mohou vývojáři také konfigurovat vlastní názvy hostitelů pro jednotlivé aplikace a pak nakonfigurovat jedinečné vazby certifikátů SNI TLS/SSL pro jednotlivé aplikace.  

## <a name="getting-started"></a>Začínáme
Pokud chcete začít s prostředím služby App Service, [přečtěte si informace o úvodu do prostředí služby App Service](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

