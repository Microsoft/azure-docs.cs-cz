---
title: Vytvoření ase s ARM
description: Zjistěte, jak vytvořit externí prostředí služby ILB App Service pomocí šablony Azure Resource Manager.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 1212e77db5e0ec83f8dd966a14872a682b3e0202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295534"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Vytvoření služby ASE pomocí šablony Azure Resource Manager

## <a name="overview"></a>Přehled

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Prostředí služby Azure App Service (ASEs) lze vytvořit pomocí koncového bodu přístupného k internetu nebo koncového bodu na interní adrese ve virtuální síti Azure (VNet). Při vytvoření s interníkoncový bod, tento koncový bod je k dispozici součást Azure s názvem interní vyrovnávání zatížení (ILB). ASE na interní IP adresu se nazývá ASE ILB. ASE s veřejný koncový bod se nazývá externí ase. 

Službu ASE lze vytvořit pomocí portálu Azure nebo šablony Azure Resource Manager. Tento článek vás provede kroky a syntaxe, které potřebujete k vytvoření externí služby ASE nebo Služby ASE Služby ILB se šablonami Správce prostředků. Informace o tom, jak vytvořit službu ASE na webu Azure portal, najdete v [tématu Vytvoření externí služby ASE][MakeExternalASE] nebo [Vytvoření služby ASE ILB][MakeILBASE].

Když vytvoříte službu ASE na webu Azure Portal, můžete vytvořit virtuální síť současně nebo zvolit již existující virtuální síť, do které se chcete nasadit. Při vytváření ase ze šablony, musíte začít s: 

* Virtuální síť Správce prostředků.
* Podsíť v této virtuální síti. Doporučujeme velikost podsítě ase `/24` s 256 adresami, aby vyhovovala budoucím potřebám růstu a škálování. Po vytvoření ase nelze změnit velikost.
* ID prostředku z vaší virtuální sítě. Tyto informace můžete získat z portálu Azure pod vlastnostmi virtuální sítě.
* Předplatné, do kterého se chcete nasadit.
* Umístění, do kterého chcete nasadit.

Automatizace vytváření ase:

1. Vytvořte ase ze šablony. Pokud vytvoříte externí sek, jste hotovi po tomto kroku. Pokud vytvoříte službu ASE ilb, je třeba provést několik dalších věcí.

2. Po vytvoření služby ASE ilb se nahraje certifikát SSL, který odpovídá vaší doméně služby ASE iLB.

3. Nahraný certifikát SSL je přiřazen k ase Služby ILB jako jeho "výchozí" certifikát SSL.  Tento certifikát se používá pro přenos ssl do aplikací ve službě ILB ASE při použití společné `https://someapp.mycustomrootdomain.com`kořenové domény, která je přiřazena službě ASE (například ).


## <a name="create-the-ase"></a>Vytvoření ase
Šablona Správce prostředků, která vytvoří službu ASE a její přidružené parametry souboru je k dispozici [v příkladu][quickstartasev2create] na GitHub.

Chcete-li vytvořit službu ASE ILB, použijte tyto [příklady][quickstartilbasecreate]šablon Správce prostředků . Obstarávají se tím případem použití. Většina parametrů v souboru *azuredeploy.parameters.json* je společná pro vytváření ILB ASEs a externích ASE. Následující seznam volá parametry zvláštní poznámky nebo které jsou jedinečné při vytváření služby ASE ILB:

* *internalLoadBalancingMode*: Ve většině případů nastavte tuto možnost na 3, což znamená, že přenosy HTTP/HTTPS na portech 80/443 a porty řídicího/datového kanálu naslouchané službou FTP ve službě ASE budou vázány na interní adresu virtuální sítě přidělenou službou ILB. Pokud je tato vlastnost nastavena na 2, jsou na adresu ILB vázány pouze porty související se službou FTP (řídicí i datové kanály). Provoz HTTP/HTTPS zůstává ve veřejné virtuální ip.
* *dnsSuffix*: Tento parametr definuje výchozí kořenovou doménu, která je přiřazena službě ASE. Ve veřejné variantě služby Azure App Service je výchozí kořenová doména pro všechny webové aplikace *azurewebsites.net*. Vzhledem k tomu, že služba ASE Služby ILB je interní ve virtuální síti zákazníka, nemá smysl používat výchozí kořenovou doménu veřejné služby. Místo toho by služba ASE ILB měla mít výchozí kořenovou doménu, která má smysl pro použití v interní virtuální síti společnosti. Společnost Contoso Corporation může například používat výchozí kořenovou doménu *internal-contoso.com* pro aplikace, které jsou určeny k řešení a přístupným pouze ve virtuální síti společnosti Contoso. 
* *ipSslAddressCount*: Tento parametr automaticky výchozí hodnotu 0 v souboru *azuredeploy.json,* protože ILB ASEs mají pouze jednu adresu ILB. Neexistují žádné explicitní adresy IP-SSL pro službu ASE ILB. Proto musí být fond adres IP-SSL pro službu ASE ILB nastaven na nulu. V opačném případě dojde k chybě zřizování. 

Po vyplnění souboru *azuredeploy.parameters.json* vytvořte službu ASE pomocí fragmentu kódu Prostředí PowerShell. Změňte cesty k souborům tak, aby odpovídaly umístění souborů šablon Správce prostředků v počítači. Nezapomeňte zadat vlastní hodnoty pro název nasazení Správce prostředků a název skupiny prostředků:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

To trvá asi hodinu pro vytvoření ase. Potom se služba ASE zobrazí na portálu v seznamu ases pro předplatné, které spustilo nasazení.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Nahrání a konfigurace "výchozího" certifikátu SSL
Certifikát SSL musí být přidružen ke službě ASE jako "výchozí" certifikát SSL, který se používá k navázání připojení SSL k aplikacím. Pokud je *výchozí*přípona DNS služby ASE `https://some-random-app.internal-contoso.com` internal-contoso.com , vyžaduje připojení k certifikátu SSL platný pro **.internal-contoso.com*. 

Získejte platný certifikát SSL pomocí interních certifikačních úřadů, zakoupením certifikátu od externího vystavittele nebo pomocí certifikátu podepsaného svým držitelem. Bez ohledu na zdroj certifikátu SSL musí být správně nakonfigurovány následující atributy certifikátu:

* **Předmět**: Tento atribut musí být nastaven na **.your-root-domain-here.com*.
* **Alternativní název předmětu**: Tento atribut musí obsahovat **.your-root-domain-here.com* a *.scm.your-root-domain-here.com*. Připojení SSL k webu SCM/Kudu přidruženému ke každé aplikaci používají adresu formuláře *your-app-name.scm.your-root-domain-here.com*.

S platným certifikátem SSL v ruce jsou zapotřebí dva další přípravné kroky. Převeďte/uložte certifikát SSL jako soubor .pfx. Nezapomeňte, že soubor .pfx musí obsahovat všechny zprostředkující a kořenové certifikáty. Zabezpečte ho pomocí hesla.

Soubor .pfx je třeba převést na řetězec base64, protože certifikát SSL je odeslán pomocí šablony Správce prostředků. Vzhledem k tomu, že šablony Správce prostředků jsou textové soubory, musí být soubor .pfx převeden na řetězec base64. Tímto způsobem může být zahrnuta jako parametr šablony.

Pomocí následujícího fragmentu kódu Prostředí PowerShellu:

* Vygenerujte certifikát podepsaný svým držitelem.
* Exportujte certifikát jako soubor .pfx.
* Převeďte soubor .pfx na řetězec kódovaný base64.
* Uložte řetězec kódovaný base64 do samostatného souboru. 

Tento kód prostředí PowerShell pro kódování base64 byl upraven z [blogu skriptů prostředí PowerShell][examplebase64encoding]:

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

Po úspěšném vygenerování certifikátu SSL a jeho převedení na řetězec kódovaný base64 použijte ukázkovou šablonu Správce prostředků [Nakonfigurujte výchozí certifikát SSL][quickstartconfiguressl] na GitHubu. 

Parametry v souboru *azuredeploy.parameters.json* jsou uvedeny zde:

* *appServiceEnvironmentName*: Název nakonfigurované služby ASE ILB.
* *existingAseLocation*: Textový řetězec obsahující oblast Azure, kde byla nasazena služba ASE ILB.  Například: "Jižní střed USA".
* *pfxBlobString*: Reprezentace řetězce kódu 64 na základě .pfx. Použijte dříve zobrazený fragment kódu a zkopírujte řetězec obsažený v souboru "exportedcert.pfx.b64". Vložte jej jako hodnotu atributu *pfxBlobString.*
* *heslo*: Heslo používané k zabezpečení souboru .pfx.
* *certificateThumbprint*: Kryptografický otisk certifikátu. Pokud tuto hodnotu načtete z prostředí PowerShell (například *$certificate. Kryptografický otisk* z předchozího fragmentu kódu) můžete použít hodnotu tak, jak je. Pokud zkopírujete hodnotu z dialogového okna Certifikát systému Windows, nezapomeňte odstranit cizí mezery. *CertifikátOtisk palce* by měl vypadat podobně jako AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *název certifikátu*: Popisný identifikátor řetězce, který si vlastní zvolíte k identifikaci certifikátu. Název se používá jako součást jedinečného identifikátoru Správce prostředků entity *Microsoft.Web/certificates,* která představuje certifikát SSL. Název *musí* končit následující příponou: \_yourASENameHere_InternalLoadBalancingASE. Portál Azure používá tuto příponu jako indikátor, že certifikát se používá k zabezpečení služby ASE s povolenou službou ILB.

Zkrácený příklad *azuredeploy.parameters.json* je uveden zde:

```json
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
```

Po vyplnění souboru *azuredeploy.parameters.json* nakonfigurujte výchozí certifikát SSL pomocí fragmentu kódu Prostředí PowerShell. Změňte cesty k souborům tak, aby odpovídaly místu, kde jsou v počítači umístěny soubory šablon Správce prostředků. Nezapomeňte zadat vlastní hodnoty pro název nasazení Správce prostředků a název skupiny prostředků:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Trvá zhruba 40 minut na front-end ase použít změnu. Například pro výchozí velikosti ase, který používá dva front-endy, šablona trvá přibližně jednu hodinu a 20 minut na dokončení. V době, kdy je šablona spuštěna, nelze škálovat.  

Po dokončení šablony aplikace na ASE ILB lze přistupovat přes HTTPS. Připojení jsou zabezpečena pomocí výchozího certifikátu SSL. Výchozí certifikát SSL se používá, když jsou aplikace ve službě ASE ILB adresovány pomocí kombinace názvu aplikace a výchozího názvu hostitele. Používá například `https://mycustomapp.internal-contoso.com` výchozí certifikát SSL pro **.internal-contoso.com*.

Stejně jako aplikace, které běží ve veřejné víceklientské službě, však vývojáři mohou konfigurovat vlastní názvy hostitelů pro jednotlivé aplikace. Mohou také nakonfigurovat jedinečné vazby certifikátů SNI SSL pro jednotlivé aplikace.

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##
Služba App Service Environment má dvě verze: ASEv1 a ASEv2. Předchozí informace se týkaly verze ASEv2. V této části jsou uvedené rozdíly mezi verzemi ASEv1 a ASEv2.

V ASEv1 spravovat všechny prostředky ručně. To se týká front-endů, pracovních procesů a IP adres používaných pro zabezpečení SSL na základě protokolu IP. Než budete moci škálovat plán služby App Service, musíte horizontální navýšení kapacity fondu pracovních procesů, který chcete hostovat.

Verze ASEv1 používá jiný cenový model než verze ASEv2. Ve verzi ASEv1 se platí za každý přidělený virtuální procesor. To zahrnuje virtuální procesory, které se používají pro front-endy nebo pracovníky, kteří nejsou hostování žádné úlohy. Ve verzi ASEv1 je výchozí maximální velikost služby ASE celkem 55 hostitelů. To zahrnuje pracovní procesy i front-endy. Jedna z výhod verze ASEv1 spočívá v tom, že se dá nasadit do klasické virtuální sítě i do virtuální sítě Resource Manager. Další informace o verzi ASEv1 najdete v článku [Úvod do služby App Service Environment verze 1][ASEv1Intro].

Pokud chcete vytvořit ASEv1 pomocí šablony Správce prostředků, přečtěte si informace [o vytvoření služby ASE Služby ILB v1 se šablonou Správce prostředků][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
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
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
