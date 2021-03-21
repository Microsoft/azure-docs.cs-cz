---
title: Vytvoření pomocného mechanismu pro ARM
description: Naučte se vytvářet externí nebo interního nástroje App Service prostředí pomocí Azure Resource Manager šablony.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 15cd0979fdc2468ab50451042cd99a8442470139
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148175"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Vytvoření pomocného objektu pomocí šablony Azure Resource Manager

## <a name="overview"></a>Přehled

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Prostředí Azure App Service (služby ASE) se dají vytvořit s koncovým bodem přístupným z Internetu nebo koncovým bodem na interní adrese ve službě Azure Virtual Network (VNet). Při vytvoření pomocí interního koncového bodu je tento koncový bod poskytovaný součástí Azure s názvem interní nástroj pro vyrovnávání zatížení (interního nástroje). K pomocnému objektu pro interní IP adresu se říká interního nástroje pomocného mechanismu. Pomocného bodu s veřejným koncovým bodem se nazývá externí pomocného mechanismu. 

Pomocného programu se dá vytvořit pomocí Azure Portal nebo šablony Azure Resource Manager. Tento článek vás provede jednotlivými kroky a syntaxí, které potřebujete k vytvoření externího pomocného mechanismu pro vytváření nebo interního nástroje pomocného programu pomocí šablon Správce prostředků. Informace o tom, jak vytvořit pomocného mechanismu řízení v Azure Portal, najdete v tématu [Vytvoření externího POmocného][MakeExternalASE] panelu nebo [Vytvoření interního nástroje][MakeILBASE].

Při vytváření pomocného mechanismu pro Azure Portal můžete vytvořit virtuální síť ve stejnou dobu nebo zvolit stávající virtuální síť, do které se mají nasadit. Při vytváření pomocného mechanismu ze šablony musíte začít s: 

* Virtuální síť Správce prostředků.
* Podsíť v této virtuální síti. Pro účely budoucího růstu a škálování doporučujeme velikost podsítě pro pomocného mechanismu řízení `/24` s 256 adresami. Po vytvoření pomocného mechanismu se velikost nedá změnit.
* ID prostředku z vaší virtuální sítě. Tyto informace můžete získat z Azure Portal ve vlastnostech virtuální sítě.
* Předplatné, do kterého chcete nasadit.
* Umístění, do kterého chcete nasadit.

Automatizace vytváření pomocného mechanismu:

1. Vytvořte si pomocného mechanismu ze šablony. Pokud vytvoříte externí pomocného bodu služby, budete po tomto kroku hotovi. Pokud vytvoříte interního nástroje pomocného mechanismu, budete mít několik dalších věcí.

2. Po vytvoření pomocného mechanismu interního nástroje se nahraje certifikát TLS/SSL, který odpovídá vaší doméně pomocného mechanismu pro interního nástroje.

3. Nahraný certifikát TLS/SSL se přiřadí k interního nástroje pomocnému modulu pro zápis prostřednictvím výchozího certifikátu TLS/SSL.  Tento certifikát se používá pro přenos TLS/SSL do aplikací v interního nástroje pomocném modulu pro zápis, když používají společnou kořenovou doménu, která je přiřazená k pomocnému mechanismu řízení (například `https://someapp.mycustomrootdomain.com` ).


## <a name="create-the-ase"></a>Vytvoření pomocného mechanismu
[V příkladu][quickstartasev2create] na GitHubu je k dispozici šablona správce prostředků, která vytvoří pomocného průvodce a soubor přidružených parametrů.

Chcete-li vytvořit interního nástroje pomocného mechanismu pro vytváření, použijte tyto [Příklady][quickstartilbasecreate]šablon Správce prostředků. Využívají k tomuto případu použití. Většina parametrů v souboru *azuredeploy.parameters.jsv* souboru je společná pro vytváření interního nástrojech služby ASE a externích služby ase. Následující seznam volá parametry zvláštní poznámky, nebo které jsou jedinečné, když vytvoříte interního nástroje pomocného programu pro vytváření:

* *internalLoadBalancingMode*: ve většině případů nastavte tuto hodnotu na 3, což znamená, že přenos HTTP/HTTPS na portech 80/443 a porty ovládacího prvku/datového kanálu, na které naslouchá služba FTP v pomocném mechanismu řízení, bude vázán na interní adresu přidělené virtuální sítě. Pokud je tato vlastnost nastavená na hodnotu 2, budou se na interního nástroje adrese svázat jenom porty související se službou FTP (ovládací prvky a kanály dat). Přenosy HTTP/HTTPS zůstávají ve veřejné virtuální IP adrese.
* *dnsSuffix*: Tento parametr definuje výchozí kořenovou doménu, která je přiřazená k pomocnému objektu pro řízení. Ve veřejné variaci Azure App Service je výchozí kořenová doména pro všechny webové aplikace *azurewebsites.NET*. Vzhledem k tomu, že interního nástroje pomocného uživatele je interní pro virtuální síť zákazníka, nemá smysl používat výchozí kořenovou doménu veřejné služby. Místo toho by měl mít interního nástroje pomocného programu k dispozici výchozí kořenovou doménu, která dává smysl pro použití v interní virtuální síti společnosti. Společnost Contoso může například používat výchozí kořenovou doménu *internal-contoso.com* pro aplikace, které mají být přeložitelný a přístupné pouze v rámci virtuální sítě společnosti Contoso. 
* *ipSslAddressCount*: Tento parametr se automaticky nastaví na hodnotu 0 v *azuredeploy.js* souboru, protože interního nástroje služby ASE má jenom jednu adresu interního nástroje. Pro interního nástroje pomocného mechanismu zabezpečení nejsou k dispozici žádné explicitní adresy IP-SSL. Proto fond adres IP-SSL pro interního nástroje pomocného modulu musí být nastaven na hodnotu nula. V opačném případě dojde k chybě zřizování. 

Po vyplnění *azuredeploy.parameters.js* v souboru vytvořte pomocí fragmentu kódu PowerShellu pomocného programu. Změňte cestu k souboru tak, aby odpovídala umístěním souborů šablony Správce prostředků na vašem počítači. Nezapomeňte zadejte vlastní hodnoty pro název nasazení Správce prostředků a název skupiny prostředků:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Vytvoření pomocného mechanismu trvá přibližně hodinu. Potom se v seznamu služby ASE pro předplatné, které spustilo nasazení, zobrazí na portálu.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Nahrajte a nakonfigurujte výchozí certifikát TLS/SSL.
Certifikát TLS/SSL musí být přidružený k tomuto pomocnému programu jako výchozí certifikát TLS/SSL, který se používá k navázání připojení TLS k aplikacím. Pokud je výchozí příponou DNS *internal-contoso.com*, připojení k `https://some-random-app.internal-contoso.com` vyžaduje certifikát TLS/SSL, který je platný pro **. Internal-contoso.com*. 

Získejte platný certifikát TLS/SSL pomocí interních certifikačních autorit, zakoupením certifikátu od externího vystavitele nebo pomocí certifikátu podepsaného svým držitelem. Bez ohledu na zdroj certifikátu TLS/SSL musí být správně nakonfigurované následující atributy certifikátu:

* **Subject**: Tento atribut musí být nastaven na hodnotu **. your-root-Domain-here.com*.
* **Alternativní název subjektu**: Tento atribut musí zahrnovat buď **. your-root-Domain-here.com* , nebo **. SCM.your-root-Domain-here.com*. Připojení TLS k webu SCM/Kudu přidruženému ke každé aplikaci používají adresu formuláře *Your-App-Name.SCM.your-root-Domain-here.com*.

S platným certifikátem TLS/SSL jsou potřeba dva další přípravné kroky. Převeďte nebo uložte certifikát TLS/SSL jako soubor. pfx. Mějte na paměti, že soubor. pfx musí zahrnovat všechny zprostředkující a kořenové certifikáty. Zabezpečte ho pomocí hesla.

Soubor. pfx se musí převést na řetězec Base64, protože certifikát TLS/SSL se nahrává pomocí šablony Správce prostředků. Vzhledem k tomu, že šablony Správce prostředků jsou textové soubory, musí být soubor. pfx převeden na řetězec base64. Tímto způsobem je možné jej zahrnout jako parametr šablony.

Následující fragment kódu prostředí PowerShell použijte k těmto akcím:

* Vygeneruje certifikát podepsaný svým držitelem.
* Exportujte certifikát jako soubor. pfx.
* Převeďte soubor. pfx na řetězec kódovaný v kódování Base64.
* Uložte řetězec s kódováním base64 do samostatného souboru. 

Tento kód PowerShellu pro kódování Base64 byl přizpůsoben na [blogu PowerShell Scripts][examplebase64encoding]:

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

Po úspěšném vygenerování certifikátu TLS/SSL a jeho převedení na řetězec kódovaný v kódování Base64 použijte šablonu ukázka Správce prostředků a [nakonfigurujte výchozí certifikát SSL][quickstartconfiguressl] na GitHubu. 

Tady jsou uvedené parametry *azuredeploy.parameters.js* v souboru:

* *appServiceEnvironmentName*: Název konfigurovaného pomocného programu interního nástroje.
* *existingAseLocation*: textový řetězec obsahující oblast Azure, ve které byl nasazený pomocný modul interního nástroje.  Například: "Střed USA – jih".
* *pfxBlobString*: řetězcová reprezentace souboru. pfx zakódovaná based64. Použijte dříve zobrazený fragment kódu a zkopírujte řetězec obsažený v souboru "exportedcert. pfx. B64". Vložte ho jako hodnotu atributu *pfxBlobString* .
* *heslo*: heslo použité k zabezpečení souboru. pfx.
* *certificateThumbprint*: kryptografický otisk certifikátu. Pokud tuto hodnotu načtete z PowerShellu (například *$Certificate. Kryptografický otisk* z dřívějšího fragmentu kódu) můžete použít hodnotu jako. Pokud zkopírujete hodnotu z dialogového okna certifikát systému Windows, nezapomeňte oddělit nadbytečné mezery. *CertificateThumbprint* by měl vypadat nějak takto: AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* identifikátor *certifikátu*: popisný identifikátor řetězce, který se používá k identifikaci certifikátu. Název se používá jako součást jedinečného identifikátoru Správce prostředků pro entitu *Microsoft. Web/Certificates* , která představuje certifikát TLS/SSL. Název *musí* končit následující příponou: \_ yourASENameHere_InternalLoadBalancingASE. Azure Portal používá tuto příponu jako indikátor, který certifikát používá k zabezpečení interního NÁSTROJEho přihlašování s povoleným protokolem.

Zde je zobrazen zkrácený příklad *azuredeploy.parameters.jsv* :

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

Po vyplnění *azuredeploy.parameters.js* v souboru nakonfigurujte výchozí certifikát TLS/SSL pomocí fragmentu kódu prostředí PowerShell. Změňte cesty k souboru tak, aby odpovídaly, kde jsou soubory šablon Správce prostředků umístěny na vašem počítači. Nezapomeňte zadejte vlastní hodnoty pro název nasazení Správce prostředků a název skupiny prostředků:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Změny se projeví přibližně 40 minut na front-endu. Například pro pomocného objekt pro pořízení velikosti, který používá dva front-end, bude šablona trvat přibližně jednu hodinu a 20 minut. Když je šablona spuštěná, nemůže se pomocného mechanismu škálovat.  

Po dokončení šablony se k aplikacím na interního nástroje přihlašování pomocí protokolu HTTPS dostanete. Připojení jsou zabezpečená pomocí výchozího certifikátu TLS/SSL. Výchozí certifikát TLS/SSL se používá v případě, že aplikace na pomocném programu interního nástroje jsou řešeny pomocí kombinace názvu aplikace a výchozího názvu hostitele. Například `https://mycustomapp.internal-contoso.com` používá výchozí certifikát TLS/SSL pro **. Internal-contoso.com*.

Nicméně stejně jako aplikace, které běží na veřejné víceklientské službě, můžou vývojáři nakonfigurovat vlastní názvy hostitelů pro jednotlivé aplikace. Můžou taky nakonfigurovat jedinečné vazby certifikátu TLS/SSL SNI pro jednotlivé aplikace.

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##
Služba App Service Environment má dvě verze: ASEv1 a ASEv2. Předchozí informace se týkaly verze ASEv2. V této části jsou uvedené rozdíly mezi verzemi ASEv1 a ASEv2.

V ASEv1 můžete všechny prostředky spravovat ručně. To se týká front-endů, pracovních procesů a IP adres používaných pro zabezpečení SSL na základě protokolu IP. Než budete moct plán App Service škálovat, musíte škálovat fond pracovních procesů, který chcete hostovat.

Verze ASEv1 používá jiný cenový model než verze ASEv2. Ve verzi ASEv1 se platí za každý přidělený virtuální procesor. To zahrnuje vCPU, které se používají pro front-endy nebo pracovní procesy, které nehostují žádné úlohy. Ve verzi ASEv1 je výchozí maximální velikost služby ASE celkem 55 hostitelů. To zahrnuje pracovní procesy i front-endy. Jedna z výhod verze ASEv1 spočívá v tom, že se dá nasadit do klasické virtuální sítě i do virtuální sítě Resource Manager. Další informace o verzi ASEv1 najdete v článku [Úvod do služby App Service Environment verze 1][ASEv1Intro].

Chcete-li vytvořit ASEv1 pomocí šablony Správce prostředků, přečtěte si téma [Vytvoření interního nástroje s POmocným mechanismem V1 se šablonou správce prostředků][ILBASEv1Template].


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
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md