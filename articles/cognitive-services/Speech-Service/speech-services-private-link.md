---
title: Používání privátních koncových bodů ve službě Speech Service
titleSuffix: Azure Cognitive Services
description: Naučte se používat službu Speech s privátními koncovými body, které poskytuje privátní odkaz na Azure.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: f905582615b16780fae179ba6a21bd4343bd47f3
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/23/2020
ms.locfileid: "97755799"
---
# <a name="use-speech-service-through-a-private-endpoint"></a>Používání služby Speech prostřednictvím privátního koncového bodu

[Privátní odkaz Azure](../../private-link/private-link-overview.md) vám umožňuje připojit se ke službám v Azure pomocí [privátního koncového bodu](../../private-link/private-endpoint-overview.md).
Privátní koncový bod je privátní IP adresa dostupná jenom v konkrétní [virtuální síti](../../virtual-network/virtual-networks-overview.md) a podsíti.

Tento článek vysvětluje, jak nastavit a používat privátní a soukromé koncové body pomocí služeb rozpoznávání řeči v Azure.

> [!NOTE]
> Tento článek vysvětluje informace o nastavení a používání privátního propojení se službami rozpoznávání řeči v Azure. Než budete pokračovat, přečtěte si téma Jak [používat virtuální sítě s Cognitive Services](../cognitive-services-virtual-networks.md).

Pokud chcete používat službu rozpoznávání řeči prostřednictvím privátního koncového bodu, proveďte následující úlohy:

1. [Vytvoření vlastního názvu domény prostředku řeči](#create-a-custom-domain-name)
2. [Vytvoření a konfigurace privátních koncových bodů](#enable-private-endpoints)
3. [Upravit stávající aplikace a řešení](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

Chcete-li odebrat privátní koncové body později, ale stále používat prostředek rozpoznávání řeči, provedete úkoly, které jsou v [této části](#use-speech-resource-with-custom-domain-name-without-private-endpoints)k dispozici.

## <a name="create-a-custom-domain-name"></a>Vytvoření vlastního názvu domény

Privátní koncové body vyžadují [název Cognitive Services vlastní subdomény](../cognitive-services-custom-subdomains.md). Podle následujících pokynů vytvořte pro svůj prostředek řeči.

> [!CAUTION]
> Prostředek řeči s povoleným vlastním názvem domény používá jiný způsob, jak komunikovat se službou Speech.
> Je možné, že budete muset upravit kód aplikace pro použití [privátního koncového bodu](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) i pro scénáře, které [ **nejsou** povolené soukromým koncovým bodem](#use-speech-resource-with-custom-domain-name-without-private-endpoints)
>
> Pokud povolíte vlastní název domény, operace se [**nevratí**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). Jediným způsobem, jak přejít zpět k [regionálnímu názvu](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) , je vytvoření nového prostředku řeči.
>
> Pokud má váš prostředek řeči velký počet přidružených vlastních modelů a projektů vytvořených prostřednictvím služby [Speech Studio](https://speech.microsoft.com/) , **důrazně** doporučujeme, abyste před změnou prostředků používaného v produkčním prostředku zkoušeli konfiguraci s testovacím prostředkem.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

K vytvoření vlastního názvu domény pomocí Azure Portal použijte následující postup:

1. Přejdete na [Azure Portal](https://portal.azure.com/) a přihlaste se ke svému účtu Azure.
1. Vyberte požadovaný prostředek řeči.
1. Ve skupině **Správa prostředků** v levém navigačním podokně klikněte na **sítě**.
1. Na kartě **brány firewall a virtuální sítě** klikněte na **generovat vlastní název domény**. Zobrazí se nový Pravý panel s pokyny pro vytvoření jedinečné vlastní subdomény pro váš prostředek.
1. Na panelu generovat vlastní název domény zadejte část vlastní název domény. Vaše úplná vlastní doména bude vypadat takto: `https://{your custom name}.cognitiveservices.azure.com` . 
    **Když vytvoříte vlastní název domény, _nedá_ se změnit. Znovu si přečtěte upozornění na vyšší opatrnost.** Po zadání vlastního názvu domény klikněte na **Uložit**.
1. Po dokončení operace klikněte ve skupině **Správa prostředků** na **klíče a koncový bod**. Ověřte, že se nový název koncového bodu vašeho prostředku spustí tímto způsobem:

    `https://{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete vytvořit vlastní název domény pomocí prostředí PowerShell, zkontrolujte, že je v počítači prostředí PowerShell verze 7. x nebo novější, a to s modulem Azure PowerShell verze 5.1.0 nebo novějším. Chcete-li zobrazit verze těchto nástrojů, postupujte podle následujících kroků:

1. V okně PowerShellu zadejte:

    `$PSVersionTable`

    Potvrďte, že hodnota PSVersion je větší než 7. x. Pokud chcete upgradovat PowerShell, postupujte podle pokynů v tématu [instalace různých verzí PowerShellu](/powershell/scripting/install/installing-powershell) pro upgrade.

1. V okně PowerShellu zadejte:

    `Get-Module -ListAvailable Az`

    Pokud se nic nezobrazí nebo pokud Azure PowerShell verze modulu je nižší než 5.1.0, postupujte podle pokynů v tématu [instalace Azure PowerShell modulu](/powershell/azure/install-Az-ps) pro upgrade.

Než budete pokračovat, spusťte příkaz `Connect-AzAccount` a vytvořte připojení k Azure.

## <a name="verify-custom-domain-name-is-available"></a>Ověřte, že je k dispozici název vlastní domény.

Musíte ověřit, jestli je k dispozici vlastní doména, kterou chcete použít. Pomocí těchto kroků potvrďte, že je doména k dispozici, v REST API Cognitive Services [Zkontrolujte operaci dostupnosti domény](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) .

> [!TIP]
> Následující **kód nebude v Azure Cloud Shell fungovat.**

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare OAuth token to use in request to Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
Pokud je požadovaný název k dispozici, zobrazí se odpověď podobná této:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Pokud je tento název již zabraný, zobrazí se následující odpověď:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Vytvoření vlastního názvu domény

K povolení vlastního názvu domény pro vybraný prostředek řeči použijeme rutinu [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) .

> [!CAUTION]
> Po úspěšném spuštění kódu se vytvoří vlastní název domény pro prostředek řeči.
> Tento název se **nedá** změnit. Další informace najdete v upozornění na vyšší **opatrnost** .

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# CAUTION: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Tato část vyžaduje nejnovější verzi rozhraní příkazového řádku Azure CLI. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="verify-the-custom-domain-name-is-available"></a>Ověřte, že je k dispozici vlastní název domény.

Musíte ověřit, jestli je vlastní doména, kterou chcete použít, zadarmo. Pro REST API Cognitive Services použijeme metodu [kontroly dostupnosti domény](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) .

Zkopírujte níže uvedený blok kódu, vložte upřednostňovaný název vlastní domény a uložte ho do souboru `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Zkopírujte soubor do aktuální složky nebo ho nahrajte do Azure Cloud Shell a spusťte následující příkaz. (Nahraďte `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ID vašeho předplatného Azure).

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Pokud je požadovaný název k dispozici, zobrazí se odpověď podobná této:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Pokud je tento název již zabraný, zobrazí se následující odpověď:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-custom-domain-name"></a>Povolit vlastní název domény

Pokud chcete pro vybraný prostředek řeči povolit vlastní název domény, použijeme příkaz [AZ cognitiveservices Account Account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Vyberte předplatné Azure obsahující prostředek řeči. Pokud má váš účet Azure jenom jedno aktivní předplatné, můžete tento krok přeskočit. (Nahraďte `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ID vašeho předplatného Azure).
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Nastavte název vlastní domény na vybraný prostředek. Nahraďte vzorové hodnoty parametrů skutečnými a spusťte následující příkaz.

> [!CAUTION]
> Po úspěšném provedení příkazu níže vytvoříte vlastní název domény pro prostředek řeči. Tento název se **nedá** změnit. Další informace najdete v upozornění na vyšší opatrnost.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enable-private-endpoints"></a>Povolit privátní koncové body

Povolte privátní koncový bod pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure.

Doporučujeme použít [privátní ZÓNU DNS](../../dns/private-dns-overview.md) připojenou k Virtual Network s nezbytnými aktualizacemi pro privátní koncové body, které ve výchozím nastavení vytvoříme během procesu zřizování. Pokud ale používáte vlastní server DNS, možná budete muset provést další změny v konfiguraci DNS. Viz část [DNS pro privátní koncové body](#dns-for-private-endpoints) . Nejlepším řešením je určit strategii DNS _ *před** zřizováním privátních koncových bodů pro produkční prostředek pro rozpoznávání řeči. Doporučujeme také předběžné testování, zejména pokud používáte vlastní server DNS.

Pro vytvoření privátních koncových bodů použijte následující články. V článcích se používá webová aplikace jako ukázkový prostředek, který umožňuje použití privátních koncových bodů. Místo toho použijte následující parametry:

| Nastavení             | Hodnota                                    |
|---------------------|------------------------------------------|
| Typ prostředku       | **Microsoft. Cognitiveservices Account/účty** |
| Prostředek            | **\<your-speech-resource-name>**         |
| Cílový dílčí prostředek | **účtu organizace.**                              |

- [Vytvoření privátního koncového bodu pomocí webu Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Vytvoření privátního koncového bodu pomocí Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Vytvoření privátního koncového bodu pomocí Azure CLI](../../private-link/create-private-endpoint-cli.md)

### <a name="dns-for-private-endpoints"></a>DNS pro privátní koncové body

Seznamte se s obecnými zásadami [DNS pro privátní koncové body v Cognitive Servicesch prostředcích](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Pak zkontrolujte, jestli konfigurace DNS funguje správně (viz další pododdíly).

#### <a name="mandatory-check-dns-resolution-from-the-virtual-network"></a>(Povinná kontrolní). Překlad DNS z Virtual Network

`my-private-link-speech.cognitiveservices.azure.com`Pro tuto část použijeme jako ukázkový název DNS prostředku pro řeč.

Přihlaste se k virtuálnímu počítači umístěnému ve virtuální síti, ke které jste připojili privátní koncový bod. Otevřete příkazový řádek systému Windows nebo prostředí bash, spusťte `nslookup` a potvrďte, že se úspěšně vyřeší vlastní název domény vašeho prostředku:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```
Ověřte, zda se přeložená IP adresa shoduje s adresou vašeho privátního koncového bodu.

#### <a name="optional-check-dns-resolution-from-other-networks"></a>(Volitelné ověření). Překlad DNS z jiných sítí

Tato Tato možnost je nutná, pokud plánujete použít prostředek řeči s povoleným privátním koncovým bodem v režimu "hybrid", kde jste povolili možnost *všechny sítě* nebo *vybrané sítě a přístup k privátním koncovým bodům* v části *síť* vašeho prostředku. Pokud plánujete přístup k prostředku pouze pomocí privátního koncového bodu, můžete tuto část přeskočit.

`my-private-link-speech.cognitiveservices.azure.com`Pro tento oddíl používáme jako ukázkový název DNS prostředku pro řeč.

Na jakémkoli počítači připojeném k síti, ze které povolíte přístup k prostředku, otevřete příkazový řádek systému Windows nebo prostředí bash, spusťte `nslookup` příkaz a potvrďte, že se název vlastní domény prostředku úspěšně vyřeší:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
Address:  13.69.67.71
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          my-private-link-speech.privatelink.cognitiveservices.azure.com
          westeurope.prod.vnet.cog.trafficmanager.net
```

Všimněte si, že přeložená IP adresa odkazuje na koncový bod proxy virtuální sítě, který odesílá síťový provoz do privátního koncového bodu pro prostředek Cognitive Services. Chování se bude lišit u prostředku s vlastním názvem domény, ale *bez* privátních koncových bodů. Podrobnosti najdete v [této části](#dns-configuration) .

## <a name="adjust-existing-applications-and-solutions"></a>Upravit stávající aplikace a řešení

Prostředek řeči s povolenou vlastní doménou používá jiný způsob, jak komunikovat se službou Speech Services. To platí pro prostředek řeči s povoleným vlastním doménou [s](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) i [bez](#use-speech-resource-with-custom-domain-name-without-private-endpoints) privátních koncových bodů. V aktuální části najdete informace potřebné pro oba případy.

### <a name="use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>Použít prostředek řeči s povoleným vlastním názvem domény a privátním koncovým bodem

Prostředek řeči s povoleným vlastním názvem domény a soukromým koncovým bodem používá jiný způsob, jak komunikovat se službou Speech. V této části se dozvíte, jak tyto prostředky používat se službou Speech Service REST API a [sadou Speech SDK](speech-sdk.md).

> [!NOTE]
> Upozorňujeme, že prostředek řeči bez privátních koncových bodů, ale s povoleným **vlastním názvem domény** má také speciální způsob, jak pracovat se službami Speech, ale tento postup se liší od scénáře použití zdroje řeči privátního koncového bodu. Pokud máte takový prostředek (řekněme, že jste provedli prostředek s privátními koncovými body, ale pak se ho rozhodl odebrat), ujistěte se, že jste obeznámeni s [oddílem korespondent](#use-speech-resource-with-custom-domain-name-without-private-endpoints).

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>Prostředek řeči s vlastním názvem domény a soukromým koncovým bodem. Použití s REST API

`my-private-link-speech.cognitiveservices.azure.com`Pro tuto část použijeme jako ukázkový název DNS prostředku pro rozpoznávání řeči (vlastní doména).

##### <a name="note-on-speech-services-rest-api"></a>Poznámka ke službám Speech REST API

Služba Speech Services má REST API pro [Převod řeči na text](rest-speech-to-text.md) a [Převod textu na řeč](rest-text-to-speech.md). Pro scénář povoleného privátního koncového bodu je třeba zvážit následující:

Převod řeči na text má dvě různá rozhraní REST API. Každé rozhraní API slouží k jinému účelu, používá jiné koncové body a vyžaduje jiný přístup, pokud se používá ve scénáři s povoleným privátním koncovým bodem.

Rozhraní REST API pro text jsou:
- [Převod řeči na text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) se používá ke [dávkovému přepisu](batch-transcription.md) a [Custom Speech](custom-speech-overview.md). v 3.0 je [následníkem v 2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3).
- [Převod řeči na text REST API pro krátký zvuk](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) se používá pro online přepis. 

Použití převodu řeči na REST API text pro krátký zvuk a převod textu na řeč REST API ve scénáři privátního koncového bodu je stejné a stejné jako v [případě sady Speech SDK](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) popsané dále v tomto článku. 

Převod řeči na text REST API v 3.0 používá jinou sadu koncových bodů, takže vyžaduje jiný přístup pro scénář s povoleným privátním koncovým bodem.

Oba případy jsou popsány v následujících pododdílech.


##### <a name="speech-to-text-rest-api-v30"></a>Převod řeči na text REST API v 3.0

Zdroje řeči obvykle používají [Cognitive Services regionální koncové body](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) pro komunikaci s [převodem řeči na text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Tyto prostředky mají následující formát pojmenování: <p/>`{region}.api.cognitive.microsoft.com`

Toto je ukázková adresa URL požadavku:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
Po povolení vlastní domény pro prostředek řeči (což je nutné pro privátní koncové body) bude tento prostředek používat následující vzor názvu DNS pro základní REST API koncový bod: <p/>`{your custom name}.cognitiveservices.azure.com`

To znamená, že v našem příkladu bude název koncového bodu REST API: <p/>`my-private-link-speech.cognitiveservices.azure.com`

A výše uvedená adresa URL požadavku musí být převedena na:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Tato adresa URL by měla být dosažitelná z Virtual Network s připojeným privátním koncovým bodem (poskytuje [správné rozlišení DNS](#mandatory-check-dns-resolution-from-the-virtual-network)).

Obecně řečeno, když povolíte vlastní název domény pro prostředek řeči, musíte nahradit název hostitele ve všech adresách URL žádostí s novým názvem vlastní domény hostitele. Všechny ostatní části žádosti (jako cesta `/speechtotext/v3.0/transcriptions` v příkladu výše) zůstávají stejné.

> [!TIP]
> Někteří zákazníci vyvinuli aplikace, které používají část názvu DNS místního koncového bodu (například k odeslání požadavku do prostředku řeči nasazeného v konkrétní oblasti Azure).
>
> Vlastní název domény prostředku Speech neobsahuje **žádné** informace o oblasti, ve které je prostředek nasazený. Proto aplikační logika popsané výše **nebude fungovat a** je třeba ji změnit.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>REST API řeči na text pro krátký zvuk a převod textu na řeč REST API

[Převod řeči na text REST API pro krátký zvuk](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) a [Převod textu na řeč REST API](rest-text-to-speech.md) použití dvou typů koncových bodů:
- [Cognitive Services místní koncové body](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) pro komunikaci s Cognitive Services REST API k získání autorizačního tokenu
- Speciální koncové body pro všechny ostatní operace

Podrobný popis speciálních koncových bodů a způsobu, jakým se mají transformovat jejich adresy URL pro prostředek řeči privátního koncového bodu, je uvedený v [této](#general-principle) části oddílu "použití se sadou Speech SDK" níže. Stejný princip, který je popsaný pro sadu SDK, platí pro REST API převodu řeči na text v 1.0 a převod textu na řeč.

Seznamte se s materiály v podčásti uvedené v předchozím odstavci a podívejte se na následující příklad. (Příklad popisuje převod textu na řeč REST API; použití převodu řeči na text REST API pro krátký zvuk je plně ekvivalentní)

> [!NOTE]
> Při použití **REST API řeči na text pro krátký zvuk** ve scénářích privátního koncového bodu je třeba použít autorizační token [předaný prostřednictvím](rest-speech-to-text.md#request-headers) `Authorization` [hlavičky](rest-speech-to-text.md#request-headers). předání klíče předplatného řeči do zvláštního koncového bodu přes `Ocp-Apim-Subscription-Key` hlavičku nebude fungovat a vygeneruje se chyba 401. 

**Příklad použití převodu textu na řeč REST API.**

Jako ukázku `my-private-link-speech.cognitiveservices.azure.com` názvu DNS prostředku (vlastní doména) použijeme západní Evropa jako ukázkovou oblast Azure. Vlastní název domény `my-private-link-speech.cognitiveservices.azure.com` v našem příkladu patří do prostředku rozpoznávání řeči vytvořeného v západní Evropa oblasti.

Chcete-li získat seznam hlasů podporovaných v oblasti, je nutné provést následující dvě operace:

- Získání autorizačního tokenu:
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- Pomocí tokenu Získejte seznam hlasů:
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
(Další podrobnosti o krocích uvedených výše v tématu [REST API dokumentaci k převodu textu na řeč](rest-text-to-speech.md))

Pro prostředek řeči s povoleným privátním koncovým bodem musí být upraveny adresy URL koncového bodu pro stejnou operaci sekvence. Stejná sekvence bude vypadat takto:
- Získat autorizační token prostřednictvím
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
(viz podrobné vysvětlení v podčásti [Převod řeči na text REST API v 3.0](#speech-to-text-rest-api-v30) výše)
- Použití získaného tokenu získá seznam hlasů přes
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
(podrobné vysvětlení najdete v části [Obecné obecné](#general-principle) část "použití se sadou Speech SDK" níže)

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>Prostředek řeči s vlastním názvem domény a soukromým koncovým bodem. Použití se sadou Speech SDK

Použití sady Speech SDK s vlastním názvem domény a prostředky řeči s povoleným privátním koncovým bodem vyžaduje kontrolu a pravděpodobná změny kódu vaší aplikace. Pracujeme na bezproblémové podpoře scénáře privátního koncového bodu.

`my-private-link-speech.cognitiveservices.azure.com`Pro tuto část použijeme jako ukázkový název DNS prostředku pro rozpoznávání řeči (vlastní doména).

##### <a name="general-principle"></a>Obecný princip

Zdroje řeči většinou v rámci scénářů SDK (stejně jako u REST API scénářů převodu textu na řeč) používají pro různé nabídky služeb speciální regionální koncové body. Formát názvu DNS pro tyto koncové body: </p>`{region}.{speech service offering}.speech.microsoft.com`

Příklad: </p>`westeurope.stt.speech.microsoft.com`

[Zde](regions.md) jsou uvedeny všechny možné hodnoty pro oblast (první element názvu DNS), které jsou uvedené v následující tabulce uvádí možnou hodnotu pro nabídku služby pro rozpoznávání řeči (druhý prvek názvu DNS):

| Hodnota názvu DNS | Nabídka služby Speech                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Vlastní příkazy](custom-commands.md)                       |
| `convai`       | [Přepis konverzace](conversation-transcription.md) |
| `s2s`          | [Překlad řeči](speech-translation.md)                 |
| `stt`          | [Převod řeči na text](speech-to-text.md)                         |
| `tts`          | [Převod textu na řeč](text-to-speech.md)                         |
| `voice`        | [Vlastní hlas](how-to-custom-voice.md)                      |

Proto příklad uvedený výše ( `westeurope.stt.speech.microsoft.com` ) představuje pro koncový bod řeči a text v západní Evropa.

Koncové body s povoleným soukromým koncovým bodem komunikují se službami Speech prostřednictvím speciálního proxy serveru a kvůli tomu, že **je potřeba změnit adresy URL připojení koncového bodu**. Použije se následující zásada: adresa URL standardního koncového bodu se řídí vzorem <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Změňte ho na: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Příklad 1.** Aplikace komunikuje pomocí následující adresy URL (rozpoznávání řeči používá základní model pro AMERICKou angličtinu v Západní Evropa): 
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Pokud ho chcete použít ve scénáři s povoleným privátním koncovým bodem, pokud je název vlastní domény prostředku řeči `my-private-link-speech.cognitiveservices.azure.com` Tato adresa URL musí být upravena takto:
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Pojďme se podívat blíž:
- Název hostitele `westeurope.stt.speech.microsoft.com` je nahrazen názvem hostitele vlastní domény. `my-private-link-speech.cognitiveservices.azure.com`
- Druhý prvek původního názvu DNS ( `stt` ) se stal prvním prvkem cesty URL a předchází původní cestě, která je původní adresou URL. `/speech/recognition/conversation/cognitiveservices/v1?language=en-US``/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`
 
**Příklad 2.** Aplikace komunikuje pomocí následující adresy URL (syntetizace řeči pomocí vlastního hlasového modelu v Západní Evropa): 
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```
Pokud ho chcete použít ve scénáři s povoleným privátním koncovým bodem, pokud je název vlastní domény prostředku řeči `my-private-link-speech.cognitiveservices.azure.com` Tato adresa URL musí být upravena takto: 
```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Stejný princip, jak je uvedeno v příkladu 1, je použit, ale klíčovým prvkem tento čas je `voice` .

##### <a name="modifying-applications"></a>Úprava aplikací

Chcete-li použít princip popsaný v předchozí části kódu aplikace, je třeba provést dvě hlavní věci:

- Určení adresy URL koncového bodu, kterou vaše aplikace používá
- Upravte adresu URL koncového bodu, jak je popsáno v předchozí části, a vytvořte `SpeechConfig` instanci třídy pomocí této upravené adresy URL explicitně.

###### <a name="determine-application-endpoint-url"></a>Určení adresy URL koncového bodu aplikace

- [Povolit protokolování pro aplikaci](how-to-use-logging.md) a spustit ji pro vygenerování protokolu
- V souboru protokolu vyhledejte `SPEECH-ConnectionUrl` . Řetězec bude obsahovat `value` parametr, který zase bude obsahovat úplnou adresu URL, kterou vaše aplikace používala.

Příklad řádku souboru protokolu s adresou URL koncového bodu:
```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```
Proto adresa URL používaná aplikací v tomto příkladu je:
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
###### <a name="create-speechconfig-instance-using-full-endpoint-url"></a>Vytvoření `SpeechConfig` instance pomocí adresy URL úplného koncového bodu

Upravte koncový bod, který jste určili v předchozí části, jak je popsáno v [Obecné zásadě](#general-principle) .

Nyní je třeba změnit způsob vytvoření instance `SpeechConfig` . Nejpravděpodobnější, že vaše dnešní aplikace používá něco podobného:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Tato akce nebude fungovat pro prostředek řeči s povoleným privátním koncovým bodem z důvodu změn názvů hostitelů a adres URL, které jsme popsali v předchozích částech. Pokud se pokusíte spustit stávající aplikaci bez jakýchkoli úprav pomocí klíče prostředku s povoleným privátním koncovým bodem, zobrazí se chyba ověřování (401).

Aby to fungovalo, musíte změnit způsob vytvoření instance `SpeechConfig` třídy a použít inicializaci "z koncového bodu"/"s koncovým bodem". Předpokládejme, že máme definované tyto dvě proměnné:
- `subscriptionKey` obsahující klíč prostředku řeči s povoleným soukromým koncovým bodem
- `endPoint` obsahuje adresu URL koncového bodu s plnou **úpravou** (pomocí typu, který je vyžadován korespondentem programovacího jazyka). V našem příkladu by tato proměnná měla obsahovat
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
Pak musíme vytvořit instanci třídy takto `SpeechConfig` :
```csharp
var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
```
```cpp
auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
```
```java
SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
```
> [!TIP]
> Parametry dotazu zadané v identifikátoru URI koncového bodu se nemění, i když jsou nastavená jinými rozhraními API. Například pokud je jazyk rozpoznávání definován v identifikátoru URI jako parametr dotazu "Language = en-US" a je také nastaven na "ru-RU" prostřednictvím korespondenční vlastnosti, má nastavení jazyka v identifikátoru URI přednost a efektivní jazyk je "en-US". Pouze parametry, které nejsou zadány v identifikátoru URI koncového bodu, mohou být nastaveny jinými rozhraními API.

Po provedení této změny by aplikace měla fungovat se zdroji řeči s povoleným soukromým hlasem. Pracujeme na bezproblémové podpoře scénáře privátního koncového bodu.

### <a name="use-speech-resource-with-custom-domain-name-without-private-endpoints"></a>Použít prostředek řeči s vlastními názvy domén bez privátních koncových bodů

V tomto článku jsme odkazovali několikrát, takže povolení vlastní domény pro prostředek řeči je **nevratné** a takový prostředek bude používat jiný způsob, jak komunikovat se službou Speech Services porovnáním s "běžnými" (které jsou ty, které používají [názvy regionálních koncových bodů](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)).

V této části se dozvíte, jak používat prostředek řeči s povoleným vlastním názvem domény, ale **bez** jakýchkoli privátních koncových bodů se službou Speech REST API a [sadou Speech SDK](speech-sdk.md). Může se jednat o prostředek, který byl použit ve scénáři privátního koncového bodu, ale následně byl odstraněn jeho soukromý koncový bod.

#### <a name="dns-configuration"></a>Konfigurace DNS

Zapamatujte si, jak se vlastní název DNS prostředku pro řeč s povoleným soukromým koncovým bodem [vyřeší z veřejných sítí](#optional-check-dns-resolution-from-other-networks). V tomto případě přeložená IP adresa odkazuje na koncový bod proxy virtuální sítě, který slouží k odesílání síťového provozu do prostředku Cognitive Services privátního koncového bodu.

Po odebrání **všech** privátních koncových bodů prostředků (nebo hned po povolení vlastního názvu domény) se ale znovu ZŘÍDÍ záznam CNAME prostředku pro rozpoznávání řeči a teď odkazuje na IP adresu korespondenta [Cognitive Services regionální koncový bod](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

Takže výstup `nslookup` příkazu bude vypadat takto:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
Porovnejte ho s výstupem z [této části](#optional-check-dns-resolution-from-other-networks).

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>Prostředek řeči s názvem vlastní domény bez privátních koncových bodů. Použití s REST API

##### <a name="speech-to-text-rest-api-v30"></a>Převod řeči na text REST API v 3.0

Použití převodu řeči na text REST API v 3.0 je plně stejné jako u [prostředků řeči s povoleným soukromým koncovým bodem](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>REST API řeči na text pro krátký zvuk a převod textu na řeč REST API

V tomto případě převod řeči na text REST API pro krátké zvuky a použití REST API textu na řeč nemá žádné rozdíly v obecném případu s jednou výjimkou pro krátký zvuk, REST API pro převod řeči na text (viz poznámka níže). Obě rozhraní API by se měly používat jak je popsáno v tématu [Převod řeči na text REST API pro krátké zvuky](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) a REST API v dokumentaci pro [Převod textu na řeč](rest-text-to-speech.md) .

> [!NOTE]
> Při použití **REST API řeči na text pro krátký zvuk** ve scénářích vlastních domén musíte použít autorizační token [předaný přes](rest-speech-to-text.md#request-headers) `Authorization` [hlavičku](rest-speech-to-text.md#request-headers). k předávání klíče předplatného řeči do zvláštního koncového bodu přes `Ocp-Apim-Subscription-Key` hlavičku **nebude** fungovat a vygeneruje se chyba 401.

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>Prostředek řeči s názvem vlastní domény bez privátních koncových bodů. Použití se sadou Speech SDK

Použití sady Speech SDK s podporou zdrojů řeči s povoleným vlastním názvem domény **bez** privátních koncových bodů vyžaduje kontrolu a pravděpodobná změny kódu aplikace. Všimněte si, že se tyto změny **liší** v případě [prostředku řeči s povoleným privátním koncovým bodem](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk). Pracujeme na zajištění plynulé podpory scénáře privátního koncového bodu nebo vlastní domény.

`my-private-link-speech.cognitiveservices.azure.com`Pro tuto část použijeme jako ukázkový název DNS prostředku pro rozpoznávání řeči (vlastní doména).

V části na [zdroji řeči s povoleným soukromým koncovým bodem](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) jsme zjistili, jak určit použitou adresu URL koncového bodu, upravit ji a zajistit, aby fungovala s inicializací instance třídy z koncového bodu/s koncovým bodem `SpeechConfig` .

Pokud se ale pokusíte spustit stejnou aplikaci po odebrání všech privátních koncových bodů (což zabrání tomu, aby se provisionické opětovné zřizování záznamů DNS), zobrazí se vnitřní chyba služby (404). Důvodem je [záznam DNS](#dns-configuration) , který teď odkazuje na koncový bod regionálního Cognitive Services místo na proxy virtuální sítě a cesty URL, jako by se tam nenašly, ale nenaleznou `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` chybu (404).

Pokud vrátíte aplikaci zpět do instance "standardní", `SpeechConfig` ve stylu
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
vaše aplikace skončí s chybou ověřování (401).

##### <a name="modifying-applications"></a>Úprava aplikací

Chcete-li povolit aplikaci pro scénář rozpoznávání řeči pomocí vlastního názvu domény bez privátních koncových bodů, je nutné provést následující akce:
- Vyžádání autorizačního tokenu prostřednictvím Cognitive Services REST API
- Vytvoření instance `SpeechConfig` třídy pomocí metody "z autorizačního tokenu"/"s autorizačním tokenem" 

###### <a name="request-authorization-token"></a>Požádat o autorizační token

V [tomto článku](../authentication.md#authenticate-with-an-authentication-token) najdete informace o tom, jak získat token prostřednictvím Cognitive Services REST API. 

V adrese URL koncového bodu použijte svůj vlastní název domény, který je v našem příkladu Tato adresa URL:
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> Tuto adresu URL můžete najít v části *klíče a koncový bod* (skupina *pro správu prostředků* ) v prostředku rozpoznávání řeči v Azure Portal.

###### <a name="create-speechconfig-instance-using-authorization-token"></a>Vytvoření `SpeechConfig` instance pomocí autorizačního tokenu

Musíte vytvořit instanci `SpeechConfig` třídy pomocí autorizačního tokenu, který jste získali v předchozí části. Předpokládejme, že jsou definované následující proměnné:

- `token` obsahuje autorizační token získaný v předchozí části.
- `azureRegion` obsahující název [oblasti](regions.md) prostředku řeči (příklad: `westeurope` )
- `outError` (pouze pro [cílový případ C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) )

Pak musíme vytvořit instanci třídy takto `SpeechConfig` :
```csharp
var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
```
```cpp
auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
```
```java
SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
```
> [!NOTE]
> Volající musí ověřit platnost autorizačního tokenu. Než vyprší platnost autorizačního tokenu, volající ho musí aktualizovat voláním tohoto Setter s novým platným tokenem. Při kopírování hodnot konfigurace při vytváření nového nástroje pro rozpoznávání nebo syntetizátoru se nová hodnota tokenu nebude vztahovat na rozpoznávání, které už byly vytvořeny. Pro rozpoznávání nebo syntetizátory, které se vytvořily dřív, je potřeba nastavit autorizační token odpovídajícího nástroje pro rozpoznávání nebo syntetizátoru, aby se token aktualizoval. V opačném případě mají rozpoznávání a syntetizátory při rozpoznávání nebo syntézě chyby.

Po provedení této změny by vaše aplikace měla fungovat s vlastními prostředky řeči s povoleným názvem domény bez privátních koncových bodů. Pracujeme na bezproblémové podpoře vlastního scénáře vlastní domény a privátního koncového bodu.

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Další kroky

* Další informace o [privátním propojení Azure](../../private-link/private-link-overview.md)
* Další informace o [sadě Speech SDK](speech-sdk.md)
* Přečtěte si další informace o převodu [řeči na text REST API](rest-speech-to-text.md)
* Další informace o [REST API převodu textu na řeč](rest-text-to-speech.md)
