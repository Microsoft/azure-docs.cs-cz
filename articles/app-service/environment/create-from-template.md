---
title: Vytvoření služby Azure App Service environment pomocí šablony Resource Manageru
description: Vysvětluje, jak vytvořit prostředí, externí nebo ILB Azure App Service pomocí šablony Resource Manageru
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 69ead9e6dae400ce16cb2442c7b1c13e348d1572
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Vytvořit App Service Environment pomocí šablony Azure Resource Manager

## <a name="overview"></a>Přehled
Prostředí Azure App Service (ASEs) může být vytvořen pomocí koncový bod přístupné z Internetu nebo koncový bod na interní adresu ve virtuální sítě Azure (VNet). Vytvoření s vnitřní koncový bod tohoto koncového bodu je zadaný ve Azure název součásti (ILB) pro vyrovnávání zatížení interní. App Service Environment na interní IP adresu se nazývá ILB App Service Environment. App Service Environment se veřejný koncový bod se nazývá externí App Service Environment. 

App Service Environment lze vytvořit pomocí portálu Azure nebo šablonu Azure Resource Manager. Tento článek vás provede kroky a syntaxe, je potřeba vytvořit na externí App Service Environment nebo ILB App Service Environment pomocí šablony Resource Manageru. Naučte se vytvořit na portálu Azure App Service Environment, najdete v tématu [zkontrolujte externí App Service Environment] [ MakeExternalASE] nebo [zkontrolujte App Service Environment ILB][MakeILBASE].

Když vytvoříte na portálu Azure App Service Environment, můžete vytvořit virtuální síť ve stejnou dobu nebo vyberte dříve existující virtuální síť k nasazení do. Při vytváření App Service Environment ze šablony, je nutné začít s: 

* Virtuální sítě Resource Manageru.
* Podsítě v této virtuální sítě. Doporučujeme, abyste velikost podsítě App Service Environment `/25` 128 adresy pro přizpůsobení budoucímu růstu. Po vytvoření App Service Environment, nelze změnit velikost.
* ID prostředku z vaší virtuální sítě. Tyto informace můžete získat z portálu Azure v části vlastnosti vaší virtuální sítě.
* Odběr, který chcete nasadit do.
* Umístění, které chcete nasadit do.

K automatizovanému vytvoření vaší App Service Environment:

1. App Service Environment vytvořte ze šablony. Pokud vytvoříte externí App Service Environment, budete hotovi po provedení tohoto kroku. Pokud vytvoříte app Service Environment ILB, existují udělat pár dalších věcí.

2. Po vytvoření vaší App Service Environment ILB je nahrát certifikát SSL, který odpovídá vaší domény ILB App Service Environment.

3. Nahrané certifikát SSL přiřazený k App Service Environment ILB jako certifikát SSL jeho "Výchozí".  Tento certifikát se používá pro přenosy protokolu SSL pro aplikace v App Service Environment ILB, když používají běžné kořenové domény, kterému je přiřazen App Service Environment (například https://someapp.mycustomrootdomain.com).


## <a name="create-the-ase"></a>Vytvořte App Service Environment
Šablony Resource Manageru, která vytvoří App Service Environment a jeho přidružené parametry souboru je k dispozici [v příklad] [ quickstartasev2create] na Githubu.

Pokud chcete, aby se App Service Environment ILB, použijte tyto šablony Resource Manageru [příklady][quickstartilbasecreate]. Budou nahrazovat které případy použití. Většina z parametrů v *azuredeploy.parameters.json* jsou společné pro vytvoření ILB ASEs a externí ASEs souboru. V následujícím seznamu volá výstupní parametry speciální Poznámka, nebo které jsou jedinečné, při vytváření App Service Environment ILB:

* *internalLoadBalancingMode*: ve většině případů sadu to do 3, to znamená přenosy HTTP/HTTPS na porty 80/443 i data ovládacího prvku nebo porty channel naslouchali pomocí služby FTP v App Service Environment, bude vázán k ILB přidělené virtuální síti interní Adresa. Pokud je tato vlastnost nastavena na 2, jenom FTP související se službou porty (řízení a datové kanály) je vázána na adresu ILB. Přenosy HTTP/HTTPS zůstává na veřejných virtuálních IP adres.
* *dnsSuffix*: Tento parametr definuje výchozí kořenové domény, kterému je přiřazen App Service Environment. Ve veřejné změna Azure App Service, Výchozí kořenová doména pro všechny webové aplikace je *azurewebsites.net*. App Service Environment ILB je interní virtuální sítě zákazníka, a proto nemá smysl použít veřejné služby výchozí kořenové domény. Místo toho ILB App Service Environment, musí mít výchozí kořenové domény, která je vhodná pro použití v rámci společnosti interní virtuální sítě. Společnost Contoso může například použít výchozí kořenovou doménu z *interní contoso.com* pro aplikace, které by měla být přeložitelný a přístupné pouze v rámci virtuální síť společnosti Contoso. 
* *ipSslAddressCount*: Tento parametr automaticky bude jako výchozí nastavena na hodnotu 0 v *azuredeploy.json* soubor protože ILB ASEs mít pouze jednu adresu ILB. Nejsou žádné explicitní IP SSL adresy pro ILB App Service Environment. Fond adres IP SSL pro App Service Environment ILB proto musí být nastavena na hodnotu nula. Jinak dojde k chybě při zřizování. 

Po *azuredeploy.parameters.json* souboru je vyplněno, vytvořte App Service Environment pomocí fragmentu kódu Powershellu. Změňte cesty k souborům tak, aby odpovídala umístění souboru šablony Resource Manager na váš počítač. Nezapomeňte zadat vlastní hodnoty pro název nasazení Resource Manager a název skupiny prostředků:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Trvá přibližně za hodinu App Service Environment, který se má vytvořit. Potom App Service Environment se zobrazí na portálu v seznamu ASEs pro odběr, který aktivoval nasazení.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Nahrání a konfigurace certifikátu protokolu SSL "Výchozí"
Certifikát SSL musí být přidružen App Service Environment jako "Výchozí" certifikátu SSL, který se používá k navázání připojení SSL k aplikacím. Pokud je App Service Environment, výchozí příponu DNS *interní contoso.com*, připojení k https://some-random-app.internal-contoso.com vyžaduje certifikát SSL, který je platný pro **.internal contoso.com*. 

Získejte platný certifikát SSL pomocí interní certifikačních autorit, nákupu certifikát z externího vystavitele, nebo certifikát podepsaný svým držitelem. Bez ohledu na zdroji certifikátu protokolu SSL musí být správně nakonfigurované následující atributy certifikátu:

* **Předmět**: Tento atribut musí být nastaven na **.your kořenové domény here.com*.
* **Alternativní název subjektu**: Tento atribut musí obsahovat i **.your kořenové domény here.com* a **.scm.your-kořenové-domain-here.com*. Připojení SSL k webu SCM/Kudu spojené s každou aplikaci použít adresu ve tvaru *your-app-name.scm.your-root-domain-here.com*.

S platným certifikátem SSL v dolním je potřeba dva další přípravné kroky. Převeďte/uložte certifikát SSL jako soubor .pfx. Nezapomeňte, že soubor .pfx musí zahrnovat všechny zprostředkující a kořenové certifikáty. Zabezpečte ho pomocí hesla.

Soubor PFX je potřeba převést na řetězec ve formátu base64, protože certifikát SSL je odeslán pomocí šablony Resource Manageru. Protože šablony Resource Manageru jsou textové soubory, soubor PFX je převést na řetězec ve formátu base64. Tímto způsobem může být zahrnuta jako parametr šablony.

Použijte následující fragment kódu prostředí PowerShell pro:

* Vygenerujte certifikát podepsaný svým držitelem.
* Exportujte certifikát jako soubor .pfx.
* Soubor .pfx převeďte řetězec s kódováním base64.
* Uložte řetězec s kódováním base64 do samostatného souboru. 

Tento kód prostředí PowerShell pro kódování base64 byla přizpůsobena z [skriptů prostředí PowerShell blog][examplebase64encoding]:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Po certifikát SSL je úspěšně vygenerovat a převedeno na řetězec s kódováním base64, pomocí šablony Resource Manageru příklad [nakonfigurujte certifikát protokolu SSL výchozí] [ quickstartconfiguressl] na Githubu. 

Parametry v *azuredeploy.parameters.json* souboru jsou tady:

* *appServiceEnvironmentName*: název ILB App Service Environment se právě nastavuje.
* *existingAseLocation*: textový řetězec obsahující oblasti Azure, kde byla nasazena App Service Environment ILB.  Například: "Jihu USA".
* *pfxBlobString*: řetězec s kódováním based64 reprezentace souboru .pfx. Pomocí fragmentu kódu, která je uvedena výše a zkopírujte řetězec obsažené v "exportedcert.pfx.b64". Vložte jej jako hodnotu *pfxBlobString* atribut.
* *heslo*: heslo používané k zabezpečení soubor .pfx.
* *certificateThumbprint*: kryptografický otisk certifikátu. Pokud načetlo tuto hodnotu z prostředí PowerShell (například *$certificate. Kryptografický otisk* z dřívějších fragment kódu), můžete použít hodnotu, jako je. Pokud zkopírujete hodnotu z dialogové okno certifikát, nezapomeňte odebrat nadbytečné mezery. *CertificateThumbprint* by měl vypadat podobně jako AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: identifikátor popisný řetězec podle vlastní volby umožňuje identity certifikát. Název se používá jako součást jedinečný identifikátor správce prostředků *Microsoft.Web/certificates* entita, která představuje certifikát SSL. Název *musí* končit následující příponu: \_yourASENameHere_InternalLoadBalancingASE. Portál Azure používá tato přípona jako indikátoru, certifikát se používá k zabezpečení App Service Environment povolené ILB.

Příklad zkrácené *azuredeploy.parameters.json* zobrazí se zde:

```json
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
```

Po *azuredeploy.parameters.json* souboru je vyplněno, nakonfigurujte certifikát protokolu SSL výchozí pomocí fragmentu kódu Powershellu. Změňte cesty k souborům tak, aby odpovídaly, kde jsou umístěny soubory šablon Resource Manager na váš počítač. Nezapomeňte zadat vlastní hodnoty pro název nasazení Resource Manager a název skupiny prostředků:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Jak dlouho trvá přibližně 40 minutách na front-endu na použití změny App Service Environment. Například pro výchozí velikosti App Service Environment používající dva elementy end front, šablony trvá přibližně jednu hodinu a 20 minut. Když šablona je spuštěný, nelze škálovat App Service Environment.  

Po dokončení šablony aplikace v App Service Environment ILB je přístupná prostřednictvím protokolu HTTPS. Připojení jsou zabezpečené pomocí výchozí certifikát SSL. Certifikát protokolu SSL výchozí se používá, když aplikace v App Service Environment ILB řešeny pomocí kombinace názvu aplikace a výchozí název hostitele. Například https://mycustomapp.internal-contoso.com používá výchozí certifikát SSL pro **.internal contoso.com*.

Stejně jako aplikace, které běží na veřejné víceklientské služby, ale vývojáři můžete nakonfigurovat vlastní hostitel názvy pro jednotlivé aplikace. Také mohou nakonfigurovat jedinečnou vazby certifikátů SNI SSL pro jednotlivé aplikace.

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##
Služba App Service Environment má dvě verze: ASEv1 a ASEv2. Předchozí informace se týkaly verze ASEv2. V této části jsou uvedené rozdíly mezi verzemi ASEv1 a ASEv2.

V ASEv1 můžete spravovat všechny prostředky ručně. To se týká front-endů, pracovních procesů a IP adres používaných pro zabezpečení SSL na základě protokolu IP. Předtím, než můžete škálovat plán služby App Service, musí horizontální navýšení kapacity fondu pracovního procesu, který chcete hostovat ho.

Verze ASEv1 používá jiný cenový model než verze ASEv2. Ve verzi ASEv1 se platí za každý přidělený virtuální procesor. Vcpu, které se používají pro front-end nebo pracovních procesů, které nejsou hostování jakékoli úlohy, který zahrnuje. Ve verzi ASEv1 je výchozí maximální velikost služby ASE celkem 55 hostitelů. To zahrnuje pracovní procesy i front-endy. Jedna z výhod verze ASEv1 spočívá v tom, že se dá nasadit do klasické virtuální sítě i do virtuální sítě Resource Manager. Další informace o verzi ASEv1 najdete v článku [Úvod do služby App Service Environment verze 1][ASEv1Intro].

K vytvoření ASEv1 pomocí šablony Resource Manageru, najdete v části [vytvoření App Service Environment ILB v1 pomocí šablony Resource Manageru][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
