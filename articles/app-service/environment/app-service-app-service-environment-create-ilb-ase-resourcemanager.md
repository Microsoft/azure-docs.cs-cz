---
title: Vytvoření ILB ASE pomocí šablony Azure Resource Manageru – App Service | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit interní služby load balancer prostředí ASE pomocí šablon Azure Resource Manageru.
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
ms.custom: seodec18
ms.openlocfilehash: 35e0dc5dabaf1602b87ec6a8be86ed609f3ea12f
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107374"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Vytvoření ILB ASE pomocí šablon Azure Resource Manageru

> [!NOTE] 
> Tento článek je o App Service Environment v1. Existuje novější verze služby App Service Environment, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
Služby App Service Environment lze vytvořit pomocí interní adresy virtuální sítě namísto veřejných virtuálních IP adres.  Tato interní adresa je poskytovaný Azure komponenta s názvem interní nástroj pro vyrovnávání zatížení (ILB).  Lze vytvořit prostředí ILB ASE pomocí webu Azure portal.  Je také možné vytvářet pomocí automatizace prostřednictvím šablon Azure Resource Manageru.  Tento článek vás provede kroky a syntaxe, které jsou potřebné k vytvoření ILB ASE pomocí šablon Azure Resource Manageru.

Součástí automatizace vytvoření ILB ASE jsou tři kroky:

1. Nejprve základní služby ASE se vytvoří ve virtuální síti pomocí adresu interního nástroje pro vyrovnávání namísto veřejných virtuálních IP adres.  Název kořenové domény v rámci tohoto kroku, je přiřazená služba ASE s ILB.
2. Po vytvoření ILB ASE se nahrát certifikát SSL.  
3. Nahraného certifikátu SSL je explicitně přiřadit služba ASE s ILB jako svůj certifikát SSL "Výchozí".  Tento certifikát SSL se použije pro provoz protokolu SSL pro aplikace v prostředí ILB ASE, když aplikace se tak vyřeší, pomocí běžných kořenovou doménu přiřazená služby ASE (např.) https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Vytvoření základní služba ASE s ILB
Jsou k dispozici na Githubu příklad šablony Azure Resource Manageru a její přidružené parametry souboru [tady][quickstartilbasecreate].

Většina parametrů v *azuredeploy.parameters.json* souboru jsou společné pro vytváření služeb ase s ILB, jak služby ase vázán na veřejných virtuálních IP adres.  Seznam níže volá výstupní parametry zejména nebo které jsou jedinečné, při vytváření prostředí ILB ASE:

* *internalLoadBalancingMode*:  Ve většině případů sadě tuto hodnotu na 3, což znamená, že přenosy HTTP/HTTPS na portech 80/443 a ovládací prvek/datový kanál, který porty naslouchali službou FTP služby ase, bude vázán k ILB přidělenou adresu interní virtuální síti.  Pokud místo toho tato vlastnost nastavena na 2, pak pouze služba FTP související s porty (ovládací prvek a data kanály) bude vázán k adresu ILB, zatímco zůstane na veřejných virtuálních IP adres se přenosy HTTP/HTTPS.
* *dnsSuffix*:  Tento parametr definuje výchozí kořenové domény, který se přiřadí služby ASE.  Ve veřejné variantu služby Azure App Service, výchozí kořenové domény pro všechny webové aplikace je *azurewebsites.net*.  Ale protože ILB ASE je interní virtuální síti zákazníka, nemá smysl použít veřejné služby výchozí kořenovou doménu.  Místo toho službu ASE by měl mít výchozí kořenové domény, který dává smysl pro použití v rámci interní virtuální síti vaší společnosti.  Hypotetický společnosti Contoso Corporation může například použít výchozí kořenové domény *interní contoso.com* pro aplikace, které jsou určeny pouze byly přeložitelný a přístupná v rámci virtuální sítě společnosti Contoso. 
* *ipSslAddressCount*:  Tento parametr je automaticky nastavena na výchozí hodnotu 0 v *azuredeploy.json* protože služeb ase s ILB mít pouze jednu adresu ILB.  Nejsou žádné explicitní adresy IP SSL pro službu ASE a proto fondu adres IP SSL pro službu ASE musí být nastavena na hodnotu nula, jinak dojde k chybě zřizování. 

Jakmile *azuredeploy.parameters.json* souboru bylo vyplněno pro službu ASE, služba ASE s ILB mohou být vytvořeny pomocí následujícího fragmentu kódu Powershellu.  Změňte soubor cesty tak, aby odpovídaly, kde jsou umístěny soubory šablon Azure Resource Manageru na vašem počítači.  Nezapomeňte taky zadat vlastní hodnoty pro název nasazení Azure Resource Manageru a název skupiny prostředků.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po Azure Resource Manageru šablony se odešle, že bude trvat několik hodin služba ASE s ILB, který se má vytvořit.  Po dokončení vytváření služba ASE s ILB se zobrazí v seznamu prostředí App Service pro předplatné, které aktivuje nasazení portálu uživatelského prostředí.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Nahrání a konfigurace certifikátu SSL "Výchozí"
Po vytvoření služba ASE s ILB certifikát SSL by měly být přidružené služby ASE, použijte "Výchozí" certifikát SSL pro navazování připojení SSL k aplikacím.  Pokračování hypotetickým příkladem společnosti Contoso Corporation v případě, že služba ASE výchozí DNS přípona je *interní contoso.com*, pak připojení k *https://some-random-app.internal-contoso.com* vyžaduje certifikát SSL, který je platné pro **.internal contoso.com*. 

Existuje řada různých způsobů, jak získat platný certifikát SSL, včetně interní certifikační autority, nákupu certifikát od externího vystavitele nebo pomocí certifikátu podepsaného svým držitelem.  Bez ohledu na zdroj certifikátu SSL musí být správně nakonfigurované následující atributy certifikátu:

* *Předmět*:  Tento atribut musí být nastaven **.vase kořenové domain-here.com*
* *Alternativní název subjektu*:  Tento atribut musí obsahovat **.vase kořenové domain-here.com*, a **.vase-kořenové-domain-here.com*.  Důvodem pro druhou položku je, že připojení SSL k webu SCM/kudu spojenému s každou aplikací se bude realizovat pomocí adresy formuláře *your-app-name.scm.your-root-domain-here.com*.

Pomocí platného certifikátu SSL v rukou jsou potřeba další dva přípravné kroky.  Certifikát SSL musí být převeden a uložen jako soubor .pfx.  Mějte na paměti, že soubor .pfx musí obsahovat všechny zprostředkující a kořenové certifikáty a také musí být zabezpečené pomocí hesla.

Soubor .pfx výsledná pak musí převést na řetězec ve formátu base64, protože certifikát SSL se nahraje pomocí šablony Azure Resource Manageru.  Šablony Azure Resource Manageru jsou textové soubory, soubor .pfx musí převést na řetězec ve formátu base64, takže může být zahrnut jako parametr šablony.

Následující fragment kódu Powershellu ukazuje příklad generování certifikátu podepsaného svým držitelem, Export certifikátu jako soubor .pfx, převádění soubor .pfx s kódováním base64 řetězec s kódováním a ten uložíte base64 kódovaný řetězec do samostatného souboru.  Kód Powershellu pro kódování base64 byla vycházejí z [blogu skripty prostředí Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Jakmile certifikát SSL byl úspěšně vygenerované a převést na kódovanou jako base64 string, příklad šablony Azure Resource Manageru na Githubu pro [konfigurace výchozího certifikátu SSL] [ configuringDefaultSSLCertificate] je možné.

Parametry v *azuredeploy.parameters.json* souboru jsou uvedeny níže:

* *appServiceEnvironmentName*:  Název služba ASE s ILB, která se právě nastavuje.
* *existingAseLocation*:  Textový řetězec obsahující oblast Azure, ve kterém byla nasazena služba ASE s ILB.  Příklad:  "Střední část jihu USA".
* *pfxBlobString*:  Based64 kódovaný řetězcová reprezentace souboru .pfx.  Pomocí fragmentu kódu je uvedeno výše, by řetězec obsažený v "exportedcert.pfx.b64" zkopírujte a vložte ji jako hodnotu *pfxBlobString* atribut.
* *password*:  Heslo používané k zabezpečení souboru .pfx.
* *certificateThumbprint*:  Kryptografický otisk certifikátu  Pokud se načetlo tuto hodnotu z prostředí Powershell (například *$certificate. Kryptografický otisk* z předchozích fragmentu kódu), můžete použít hodnotu jako-je.  Při kopírování hodnoty z dialogového okna certifikátu Windows, ale nezapomeňte odstranit nadbytečné mezery.  *CertificateThumbprint* by měl vypadat nějak takto:  AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *název certifikátu*:  Vlastní výběr identifikátor popisný řetězec použít na identitu certifikátu.  Název se používá jako součást jedinečný identifikátor pro Azure Resource Manageru *Microsoft.Web/certificates* entita, která představuje certifikát SSL.  Název **musí** končit následující příponu: \_yourASENameHere_InternalLoadBalancingASE.  Tato přípona se používá portálem jako indikátor, že certifikát se používá k zabezpečení služby ASE s ILB povolena.

Příklad zkrácený *azuredeploy.parameters.json* je uveden níže:

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

Jakmile *azuredeploy.parameters.json* souboru bylo vyplněno, výchozího certifikátu SSL můžete nakonfigurovat pomocí následujícího fragmentu kódu Powershellu.  Změňte soubor cesty tak, aby odpovídaly, kde jsou umístěny soubory šablon Azure Resource Manageru na vašem počítači.  Nezapomeňte taky zadat vlastní hodnoty pro název nasazení Azure Resource Manageru a název skupiny prostředků.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po Azure Resource Manageru šablony se odešle, že bude trvat přibližně 40 minut za službu ASE front-endu na použití změny.  Například s výchozí velikost služby ASE pomocí dvou front endů, šablona bude trvat přibližně jednu hodinu a dvacet minut dokončení.  Je spuštěn v šabloně služby ASE nebude možné škálovat.  

Po dokončení šablonu aplikace na služba ASE s ILB je přístupná přes protokol HTTPS a připojení, bude možné zabezpečit pomocí výchozího certifikátu SSL.  Výchozí certifikát SSL se použije při aplikací v prostředí ILB ASE se tak vyřeší, pomocí kombinace výchozí název hostitele a název aplikace.  Například *https://mycustomapp.internal-contoso.com* využije výchozí certifikát SSL pro **.internal contoso.com*.

Však stejně jako aplikace běžící na veřejné víceklientská služba, vývojáři mohou také nakonfigurovat vlastní hostitele pro jednotlivé aplikace a nakonfigurujte jedinečných vazeb certifikátů SNI SSL pro jednotlivé aplikace.  

## <a name="getting-started"></a>Začínáme
Začínáme s App Service Environment najdete v článku [Úvod do služby App Service Environment](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

