---
title: Vytvoření pomocného interního nástroje v1
description: Vytvořte App Service prostředí s interním nástrojem pro vyrovnávání zatížení (interního nástroje pomocného programu pro čtení). Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: a6cc1cae640b97ecb3d95ee1e4f8ec34750e32d2
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832999"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Vytvoření ILB ASE pomocí šablon Azure Resource Manageru

> [!NOTE] 
> Tento článek se týká App Service Environment v1. Existuje novější verze App Service Environment, kterou je snazší použít a která je spuštěná na výkonnější infrastruktuře. Další informace o nové verzi začíná [úvodem do App Service Environment](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
App Service prostředí je možné vytvořit pomocí interní adresy virtuální sítě namísto veřejné virtuální IP adresy.  Tuto interní adresu poskytuje komponenta Azure s názvem interní nástroj pro vyrovnávání zatížení (interního nástroje).  INTERNÍHO nástroje pomocného programu je možné vytvořit pomocí Azure Portal.  Můžete ji také vytvořit pomocí automatizace pomocí Azure Resource Manager šablon.  Tento článek vás provede kroky a syntaxí, které je potřeba k vytvoření interního nástroje pomocného programu pomocí šablon Azure Resource Manager.

Při automatizaci vytváření interního nástroje pomocného programu pro pořízení se dotýkají tři kroky:

1. Ve virtuální síti se nejdřív vytvoří základní nástroj pro čtení a místo veřejné virtuální IP adresy se použije interní adresa nástroje pro vyrovnávání zatížení.  V rámci tohoto kroku se k interního nástroje pomocnému programu přiřadí název kořenové domény.
2. Po vytvoření pomocného mechanismu interního nástroje se nahraje certifikát TLS/SSL.  
3. Nahraný certifikát TLS/SSL se explicitně přiřadí k interního nástroje pomocnému modulu pro zápis certifikátu standardem TLS/SSL.  Tento certifikát TLS/SSL se bude používat pro přenosy TLS do aplikací v interního nástroje pomocném programu při adresování aplikací pomocí společné kořenové domény přiřazené k pomocnému mechanismu řízení (např. `https://someapp.mycustomrootcomain.com` ).

## <a name="creating-the-base-ilb-ase"></a>Vytvoření základního pomocného mechanismu interního nástroje
Ukázková šablona Azure Resource Manager a její přidružené soubory parametrů jsou k dispozici [na GitHubu][quickstartilbasecreate].

Většina parametrů v souboru *azuredeploy.parameters.jsv* souboru je společná pro vytváření jak interního nástroje služby ASE, tak i služby ASE vázaného na veřejnou virtuální IP adresu.  Seznam níže volá při vytváření interního nástroje pomocného programu pro přihlašování do paměti parametry speciální poznámky, nebo které jsou jedinečné:

* *internalLoadBalancingMode*: ve většině případů je tato nastavení nastavena na hodnotu 3, což znamená, že přenos HTTP/HTTPS na portech 80/443 a porty ovládacího prvku/datového kanálu, na které naslouchá služba FTP v pomocném mechanismu řízení, bude vázán na interní adresu přidělenou virtuální síti interního nástroje.  Pokud je tato vlastnost nastavená na hodnotu 2, budou se svázat jenom porty související se službou FTP (řízení a datové kanály) na interního nástroje adrese, zatímco přenosy HTTP/HTTPS zůstanou ve veřejné virtuální IP adrese.
* *dnsSuffix*: Tento parametr definuje výchozí kořenovou doménu, která bude přiřazena k pomocnému mechanismu řízení.  Ve veřejné variaci Azure App Service je výchozí kořenová doména pro všechny webové aplikace *azurewebsites.NET*.  Vzhledem k tomu, že služba interního nástroje pomocného mechanismu je interní pro virtuální síť zákazníka, nemá smysl použít výchozí kořenovou doménu veřejné služby.  Místo toho by měl mít interního nástroje pomocného programu k dispozici výchozí kořenovou doménu, která dává smysl pro použití v interní virtuální síti společnosti.  Hypotetická společnost Contoso může například použít výchozí kořenovou doménu *internal-contoso.com* pro aplikace, které mají být přestupnější a přístupné v rámci virtuální sítě společnosti Contoso. 
* *ipSslAddressCount*: Tento parametr je automaticky nastaven na hodnotu 0 v *azuredeploy.js* souboru, protože interního nástroje služby ASE má pouze jednu adresu interního nástroje.  Pro interního nástroje pomocného mechanismu zabezpečení nejsou k dispozici žádné explicitní adresy IP-SSL, takže fond adres IP-SSL pro interního nástrojeho přihlašování k musí být nastaven na hodnotu nula. v opačném případě dojde k chybě zřizování. 

Po vyplnění *azuredeploy.parameters.js* v souboru pro interního nástroje pomocného programu pro přihlášení k interního nástroje může být vytvořený pomocí následujícího fragmentu kódu prostředí PowerShell.  Změňte cesty k souboru tak, aby odpovídaly, kde jsou soubory šablon Azure Resource Manager umístěny na vašem počítači.  Nezapomeňte také uvést vlastní hodnoty pro název nasazení Azure Resource Manager a název skupiny prostředků.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Po odeslání šablony Azure Resource Manager může trvat několik hodin, než se vytvoří pomocného nástroje interního nástroje.  Až se vytváření dokončí, v uživatelském rozhraní portálu se zobrazí interního nástroje pomocnému programu portálu v seznamu App Service prostředí pro předplatné, které nasazení aktivovalo.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Nahrává a konfiguruje se výchozí certifikát TLS/SSL.
Po vytvoření pomocného programu interního nástroje se certifikát TLS/SSL musí přidružit k tomuto pomocnému programu jako výchozí certifikát TLS/SSL k vytvoření připojení TLS/SSL k aplikacím.  Pokud je výchozí přípona DNS *internal-contoso.com* v případě, že se používá hypotetický příklad společnosti Contoso Corporation, připojení k nástroji *`https://some-random-app.internal-contoso.com`* vyžaduje certifikát TLS/SSL, který je platný pro **. Internal-contoso.com*. 

Existuje mnoho způsobů, jak získat platný certifikát TLS/SSL, včetně interních certifikačních autorit, koupit certifikát od externího vystavitele a použít certifikát podepsaný svým držitelem.  Bez ohledu na zdroj certifikátu TLS/SSL musí být správně nakonfigurovány následující atributy certifikátu:

* *Subject*: Tento atribut musí být nastaven na hodnotu **. your-root-Domain-here.com*
* *Alternativní název subjektu*: Tento atribut musí zahrnovat buď **. your-root-Domain-here.com*, nebo **. SCM.your-root-Domain-here.com*.  Důvodem pro druhou položku je to, že připojení TLS k webu SCM/Kudu přidruženému k jednotlivým aplikacím se provede pomocí adresy formuláře *Your-App-Name.SCM.your-root-Domain-here.com*.

S platným certifikátem TLS/SSL jsou potřeba dva další přípravné kroky.  Certifikát TLS/SSL musí být převeden nebo uložen jako soubor. pfx.  Mějte na paměti, že soubor. pfx musí zahrnovat všechny zprostředkující a kořenové certifikáty a musí být zabezpečený heslem.

Výsledný soubor. pfx se pak musí převést na řetězec Base64, protože certifikát TLS/SSL se nahraje pomocí šablony Azure Resource Manager.  Vzhledem k tomu, že šablony Azure Resource Manager jsou textové soubory, musí být soubor. pfx převeden na řetězec Base64, aby jej bylo možné zahrnout jako parametr šablony.

Následující fragment kódu PowerShellu ukazuje příklad generování certifikátu podepsaného svým držitelem, Export certifikátu jako souboru. pfx, převedení souboru. pfx na řetězec kódovaný v kódování Base64 a uložení řetězce kódovaného pomocí Base64 do samostatného souboru.  Kód PowerShellu pro kódování Base64 byl přizpůsoben na [blogu PowerShell Scripts][examplebase64encoding].

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Jakmile se certifikát TLS/SSL úspěšně vygeneroval a převede na řetězec kódovaný v kódování Base64, dá se pro [konfiguraci výchozího certifikátu TLS/SSL][configuringDefaultSSLCertificate] použít ukázkový Azure Resource Manager šablonu na GitHubu.

Parametry v souboru *azuredeploy.parameters.js* jsou uvedeny níže:

* *appServiceEnvironmentName*: Název konfigurovaného pomocného programu interního nástroje.
* *existingAseLocation*: textový řetězec obsahující oblast Azure, ve které byl nasazený pomocný modul interního nástroje.  Například: "Střed USA – jih".
* *pfxBlobString*: řetězcová reprezentace based64 kódovaného souboru. pfx.  Pomocí fragmentu kódu uvedeného výše byste zkopírovali řetězec obsažený v souboru "exportedcert. pfx. B64" a vložili jste ho jako hodnotu atributu *pfxBlobString* .
* *heslo*: heslo použité k zabezpečení souboru. pfx.
* *certificateThumbprint*: kryptografický otisk certifikátu.  Pokud tuto hodnotu načtete z PowerShellu (např. *$Certificate. Kryptografický otisk* z dřívějšího fragmentu kódu) můžete použít hodnotu tak, jak je.  Pokud však zkopírujete hodnotu z dialogu certifikát systému Windows, nezapomeňte oddělit nadbytečné mezery.  *CertificateThumbprint* by měl vypadat nějak takto: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* identifikátor *certifikátu*: popisný identifikátor řetězce, který se používá k identifikaci certifikátu.  Název se používá jako součást jedinečného identifikátoru Azure Resource Manager pro entitu *Microsoft. Web/Certificates* , která představuje certifikát TLS/SSL.  Název **musí** končit následující příponou:  \_ yourASENameHere_InternalLoadBalancingASE.  Tato přípona se používá na portálu jako ukazatel, který se používá k zabezpečení interního nástroje přihlašování s povolenými právy.

Následující zkrácený příklad *azuredeploy.parameters.js* je uveden níže:

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

Po vyplnění *azuredeploy.parameters.js* v souboru se dá výchozí certifikát TLS/SSL nakonfigurovat pomocí následujícího fragmentu kódu prostředí PowerShell.  Změňte cesty k souboru tak, aby odpovídaly, kde jsou soubory šablon Azure Resource Manager umístěny na vašem počítači.  Nezapomeňte také uvést vlastní hodnoty pro název nasazení Azure Resource Manager a název skupiny prostředků.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Po odeslání šablony Azure Resource Manager bude pro použití změny trvat zhruba 40 minut na front-endu na úrovni služeb.  Například s výchozím pomocným mechanismem velikosti, který používá dvě front-endy, bude šablona trvat přibližně jednu hodinu a 20 minut.  I když je šablona spuštěná, nebude možné škálovat škálování.  

Po dokončení šablony se k aplikacím na interního nástroje přihlašování pomocí protokolu HTTPS budou moct připojovat přes HTTPS a připojení se budou zabezpečit pomocí výchozího certifikátu TLS/SSL.  Výchozí certifikát TLS/SSL se použije, když se aplikace na pomocném programu interního nástroje řeší pomocí kombinace názvu aplikace a výchozího názvu hostitele.  Například *`https://mycustomapp.internal-contoso.com`* by byl použit výchozí certifikát TLS/SSL pro **. Internal-contoso.com*.

Nicméně stejně jako aplikace spuštěné ve veřejné víceklientské službě můžou vývojáři nakonfigurovat také vlastní názvy hostitelů pro jednotlivé aplikace a potom nakonfigurovat jedinečné vazby certifikátů SNI TLS/SSL pro jednotlivé aplikace.  

## <a name="getting-started"></a>Začínáme
Pokud chcete začít pracovat se App Service prostředími, přečtěte si téma [Úvod do App Service Environment](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

