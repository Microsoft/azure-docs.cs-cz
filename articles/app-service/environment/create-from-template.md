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
ms.openlocfilehash: 92422a254bcfd5b31731dda6d1790cc85f467860
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643504"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Vytvoření služby ASE s použitím šablony Azure Resource Manageru

## <a name="overview"></a>Přehled
Azure App Service Environment (ase) lze vytvořit pomocí koncového bodu přístupné z Internetu nebo koncový bod na interní adresu ve službě Azure virtual network (VNet). Při vytvoření pomocí interního koncového bodu, že je koncový bod zadaný pomocí Azure komponenta volá interní nástroj pro vyrovnávání zatížení (ILB). Služba ASE pro interní IP adresy se nazývá službu ASE. Služba ASE s veřejným koncovým bodem se nazývá externí služby ASE. 

Služba ASE je vytvořit pomocí webu Azure portal nebo šablony Azure Resource Manageru. Tento článek vás provede kroky a syntaxi, je potřeba vytvořit externí služby ASE nebo prostředí ILB ASE pomocí šablon Resource Manageru. Zjistěte, jak vytvořit službu ASE na webu Azure Portal, najdete v článku [vytvořit externí služby ASE] [ MakeExternalASE] nebo [vytvořit prostředí ILB ASE][MakeILBASE].

Při vytváření služby ASE na webu Azure Portal můžete vytvořit virtuální síť ve stejnou dobu nebo vybrat stávající virtuální síť k nasazení do. Při vytváření prostředí ASE ze šablony, musí začínat znakem: 

* Virtuální síť Resource Manageru.
* Podsítě v dané virtuální síti. Doporučujeme, abyste je velikost podsítě služby ASE `/24` s 256 adres pro případné budoucí růst a škálování potřebám. Po vytvoření služby ASE, nelze změnit velikost.
* ID prostředku z vaší virtuální sítě. Tyto informace můžete získat z webu Azure portal ve vlastnostech vaší virtuální sítě.
* Předplatné, které chcete nasadit do.
* Umístění, které chcete nasadit do.

K automatizaci vytvoření vaší služby ASE:

1. Vytvoření služby ASE ze šablony. Pokud vytvoříte externí služby ASE, skončíte po provedení tohoto kroku. Pokud vytvoříte službu ASE, existují udělat pár dalších věcí.

2. Po vytvoření služby ASE s ILB, se nahraje certifikát SSL, který se shoduje s doménou ILB ASE.

3. Služba ASE s ILB nahraného certifikátu SSL je přiřazen jako svůj certifikát SSL "Výchozí".  Tento certifikát se používá pro provoz protokolu SSL pro aplikace na služba ASE s ILB, při použití běžných kořenové domény, který je přiřazen do služby ASE (například https://someapp.mycustomrootdomain.com).


## <a name="create-the-ase"></a>Vytvoření služby ASE
Šablony Resource Manageru, která vytvoří služba ASE a její přidružené parametry souboru je k dispozici [v příklad] [ quickstartasev2create] na Githubu.

Pokud chcete vytvořit službu ASE, použijte tyto šablony Resource Manageru [příklady][quickstartilbasecreate]. Jejich Postarejte se o případu použití. Většina parametrů v *azuredeploy.parameters.json* souboru jsou společné pro vytváření služeb ase s ILB a externí služby ase. V následujícím seznamu volá výstupní parametry zejména nebo které jsou jedinečné, když vytvoříte službu ASE:

* *internalLoadBalancingMode*: ve většině případů sadu tuto hodnotu na 3, což znamená, že přenosy HTTP/HTTPS na portech 80/443 a daty ovládacího prvku/portů channel naslouchali službou FTP služby ase, bude vázán k ILB přidělené virtuální síti typu interní Adresa. Pokud je tato vlastnost nastavena na 2, pouze související se službou portů FTP (ovládací prvek a data kanály) jsou vázány na adresu ILB. Přenosy HTTP/HTTPS zůstávají ve veřejných virtuálních IP adres.
* *příponu DNS*: Tento parametr definuje výchozí kořenové domény, který je přiřazen do služby ASE. Ve veřejné variantu služby Azure App Service, výchozí kořenové domény pro všechny webové aplikace je *azurewebsites.net*. Protože prostředí ILB ASE je interní virtuální síti zákazníka, nemá smysl použít veřejné služby výchozí kořenovou doménu. Místo toho službu ASE by měl mít výchozí kořenové domény, který dává smysl pro použití v rámci interní virtuální síti vaší společnosti. Například společnost Contoso může použít výchozí kořenové domény *interní contoso.com* pro aplikace, které jsou určeny byly přeložitelný a přístupný pouze v rámci virtuální sítě společnosti Contoso. 
* *ipSslAddressCount*: Tento parametr automaticky výchozí hodnotu na hodnotu 0 v *azuredeploy.json* protože služeb ase s ILB mít pouze jednu adresu ILB. Nejsou žádné explicitní adresy IP SSL pro službu ASE. Fond adres IP SSL pro službu ASE proto musí být nastavena na hodnotu nula. V opačném případě dojde k zřizování chybě. 

Po *azuredeploy.parameters.json* souboru je vyplněna, vytvoření služby ASE s použitím prostředí PowerShell fragmentu kódu. Změna cesty k souborům tak, aby odpovídaly umístění souboru šablony Resource Manageru na vašem počítači. Nezapomeňte zadat vlastní hodnoty pro název nasazení Resource Manageru a názvu skupiny prostředků:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Trvá přibližně hodinu pro službu ASE, který se má vytvořit. Potom služby ASE se zobrazí na portálu v seznamu služeb ase pro předplatné, které aktivuje nasazení.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Nahrání a konfigurace certifikátu SSL "Výchozí"
Certifikát SSL musí být přidruženy služby ASE jako "Výchozí" certifikát SSL, který se používá k navázání připojení SSL k aplikacím. Pokud je výchozí příponu DNS službu ASE *interní contoso.com*, připojení k https://some-random-app.internal-contoso.com vyžaduje certifikát SSL, který je platný pro **.internal contoso.com*. 

Získáte platný certifikát protokolu SSL pomocí interní certifikační autority, nákupu certifikát od externího vystavitele nebo pomocí certifikátu podepsaného svým držitelem. Bez ohledu na zdroj certifikátu SSL musí být správně nakonfigurované následující atributy certifikátu:

* **Předmět**: Tento atribut musí být nastaven **.vase kořenové domain-here.com*.
* **Alternativní název předmětu**: Tento atribut musí obsahovat **.vase kořenové domain-here.com* a **.vase-kořenové-domain-here.com*. Připojení SSL k webu SCM/kudu spojenému s každou aplikací používá adresu ve tvaru *your-app-name.scm.your-root-domain-here.com*.

Pomocí platného certifikátu SSL v rukou jsou potřeba další dva přípravné kroky. Převeďte/uložte certifikát SSL jako soubor .pfx. Mějte na paměti, že soubor .pfx musí obsahovat všechny zprostředkující a kořenové certifikáty. Zabezpečte ho pomocí hesla.

Soubor PFX je potřeba převést na řetězec ve formátu base64, protože certifikát SSL je odeslán pomocí šablony Resource Manageru. Vzhledem k tomu, že šablony Resource Manageru jsou textové soubory, soubor PFX je převést na řetězec ve formátu base64. Tímto způsobem může být zahrnut jako parametr šablony.

Použijte následující fragment kódu Powershellu pro:

* Vygenerujte certifikát podepsaný svým držitelem.
* Exportujte certifikát jako soubor .pfx.
* Převeďte soubor .pfx do řetězce s kódováním base64.
* Uložte řetězec s kódováním base64 do samostatného souboru. 

Tento kód Powershellu pro kódování base64 byla vycházejí z [blogu skripty prostředí PowerShell][examplebase64encoding]:

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

Poté, co certifikát SSL je úspěšně vygenerována a převedeno na řetězec s kódováním base64, pomocí šablony Resource Manageru příklad [konfigurace výchozího certifikátu SSL] [ quickstartconfiguressl] na Githubu. 

Parametry v *azuredeploy.parameters.json* souboru jsou uvedené tady:

* *appServiceEnvironmentName*: název služba ASE s ILB, která se právě nastavuje.
* *existingAseLocation*: textový řetězec obsahující oblast Azure, ve kterém byla nasazena služba ASE s ILB.  Příklad: "Střed USA – jih".
* *pfxBlobString*: řetězec s kódováním based64 reprezentace souboru .pfx. Použijte fragment kódu je uvedeno výše a zkopírujte řetězec obsažený v "exportedcert.pfx.b64". Vložte ji jako hodnotu *pfxBlobString* atribut.
* *heslo*: heslo používané k zabezpečení souboru .pfx.
* *Miniatura certifikátu*: kryptografický otisk certifikátu. Pokud se načetlo tuto hodnotu z prostředí PowerShell (například *$certificate. Kryptografický otisk* z předchozích fragmentu kódu), můžete použít hodnotu, jako je. Pokud zkopírujete hodnoty z dialogového okna certifikátu Windows, nezapomeňte odstranit nadbytečné mezery. *CertificateThumbprint* by měla vypadat podobně jako AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *název certifikátu*: použít popisný řetězec identifikátoru vlastní identity certifikát. Název se používá jako součást jedinečný identifikátor správce prostředků *Microsoft.Web/certificates* entity, který představuje certifikát SSL. Název *musí* končit následující příponu: \_yourASENameHere_InternalLoadBalancingASE. Na webu Azure portal používá tato přípona jako indikátor, že tento certifikát slouží k zabezpečení služby ASE s ILB povolena.

Příklad zkrácený *azuredeploy.parameters.json* je znázorněna zde:

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

Po *azuredeploy.parameters.json* souboru je vyplněna, konfigurace výchozího certifikátu SSL s použitím fragment kódu Powershellu. Změna cesty k souborům tak, aby odpovídaly, kde jsou umístěny soubory šablon Resource Manageru na vašem počítači. Nezapomeňte zadat vlastní hodnoty pro název nasazení Resource Manageru a názvu skupiny prostředků:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Trvá přibližně 40 minut na front-endu služby ASE na použití změny. Například pro ASE se výchozí velikosti, která používá dva front-endy šablony trvá přibližně jednu hodinu a 20 minut. Když šablona je spuštěn, nelze škálovat služby ASE.  

Po dokončení šablonu aplikace na služba ASE s ILB je možný přes protokol HTTPS. Připojení jsou zabezpečené pomocí výchozího certifikátu SSL. Výchozí certifikát SSL se používá při aplikace v prostředí ILB ASE se tak vyřeší, s použitím kombinace výchozí název hostitele a název aplikace. Například https://mycustomapp.internal-contoso.com používá výchozí certifikát SSL pro **.internal contoso.com*.

Stejně jako aplikace, které běží na veřejné víceklientské služby, ale vývojáři můžete nakonfigurovat vlastní hostitele pro jednotlivé aplikace. Také mohou nakonfigurovat jedinečných vazeb certifikátů SNI SSL pro jednotlivé aplikace.

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##
Služba App Service Environment má dvě verze: ASEv1 a ASEv2. Předchozí informace se týkaly verze ASEv2. V této části jsou uvedené rozdíly mezi verzemi ASEv1 a ASEv2.

Ve verzi ASEv1 je spravovat všechny prostředky ručně. To se týká front-endů, pracovních procesů a IP adres používaných pro zabezpečení SSL na základě protokolu IP. Před horizontálním navýšení kapacity plánu služby App Service, musíte horizontální navýšení kapacity fondu pracovních procesů, které chcete hostovat ho.

Verze ASEv1 používá jiný cenový model než verze ASEv2. Ve verzi ASEv1 se platí za každý přidělený virtuální procesor. To zahrnuje virtuálních procesorů, které se používají pro front-endy nebo pracovní procesy, které nejsou hostiteli žádných úloh. Ve verzi ASEv1 je výchozí maximální velikost služby ASE celkem 55 hostitelů. To zahrnuje pracovní procesy i front-endy. Jedna z výhod verze ASEv1 spočívá v tom, že se dá nasadit do klasické virtuální sítě i do virtuální sítě Resource Manager. Další informace o verzi ASEv1 najdete v článku [Úvod do služby App Service Environment verze 1][ASEv1Intro].

Vytvoření ASEv1 pomocí šablony Resource Manageru najdete v tématu [vytvoření v1 ILB ASE pomocí šablony Resource Manageru][ILBASEv1Template].


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
