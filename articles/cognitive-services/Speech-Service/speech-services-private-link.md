---
title: Používání privátních koncových bodů ve službě Speech Services
titleSuffix: Azure Cognitive Services
description: Naučte se používat hlasové služby s privátními koncovými body, které poskytuje privátní odkaz na Azure.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: alexeyo
ms.openlocfilehash: c9af0cda14261e8eab7f1ecc05c50a289d7ddfdb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99559651"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>Použití služby Speech Services prostřednictvím privátního koncového bodu

[Privátní odkaz Azure](../../private-link/private-link-overview.md) vám umožňuje připojit se ke službám v Azure pomocí [privátního koncového bodu](../../private-link/private-endpoint-overview.md). Privátní koncový bod je privátní IP adresa, která je přístupná jenom v konkrétní [virtuální síti](../../virtual-network/virtual-networks-overview.md) a podsíti.

Tento článek vysvětluje, jak nastavit a používat privátní a soukromé koncové body se službami Speech v Azure Cognitive Services.

> [!NOTE]
> Než budete pokračovat, přečtěte si téma [Jak používat virtuální sítě s Cognitive Services](../cognitive-services-virtual-networks.md).

Tento článek také popisuje [, jak odebrat privátní koncové body později, ale dál používat prostředek pro rozpoznávání řeči](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Vytvoření vlastního názvu domény

Privátní koncové body vyžadují [pro Cognitive Services název vlastní subdomény](../cognitive-services-custom-subdomains.md). Pro vytvoření prostředku řeči použijte následující pokyny.

> [!WARNING]
> Prostředek řeči s povoleným vlastním názvem domény používá jiný způsob, jak komunikovat se službou Speech Services. Může být nutné upravit kód aplikace v obou těchto scénářích: [povolený privátní koncový bod](#use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled) a [ *není* povolen soukromý koncový bod](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).
>
> Pokud povolíte vlastní název domény, operace se [nevratí](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). Jediným způsobem, jak přejít zpět k [regionálnímu názvu](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) , je vytvoření nového prostředku řeči.
>
> Pokud má váš prostředek řeči velký počet přidružených vlastních modelů a projektů vytvořených pomocí nástroje [Speech Studio](https://speech.microsoft.com/), důrazně doporučujeme, abyste před úpravou prostředku používaného v produkčním prostředku zkoušeli provést konfiguraci s testovacím prostředkem.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

K vytvoření vlastního názvu domény pomocí Azure Portal použijte následující postup:

1. Přejděte na [Azure Portal](https://portal.azure.com/) a přihlaste se ke svému účtu Azure.
1. Vyberte požadovaný prostředek řeči.
1. Ve skupině **pro správu prostředků** v levém podokně vyberte možnost **sítě**.
1. Na kartě **brány firewall a virtuální sítě** vyberte **generovat vlastní název domény**. Zobrazí se nový Pravý panel s pokyny pro vytvoření jedinečné vlastní subdomény pro váš prostředek.
1. Na panelu **generovat vlastní název domény** zadejte název vlastní domény. Vaše úplná vlastní doména bude vypadat takto: `https://{your custom name}.cognitiveservices.azure.com` . 
    
    Pamatujte, že po vytvoření vlastního názvu domény ho _nelze_ změnit.
    
    Po zadání vlastního názvu domény vyberte **Uložit**.
1. Po dokončení operace vyberte ve skupině **Správa prostředků** možnost **klíče a koncový bod**. Potvrďte, že nový název koncového bodu vašeho prostředku začíná tímto způsobem: `https://{your custom name}.cognitiveservices.azure.com` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete vytvořit vlastní název domény pomocí prostředí PowerShell, zkontrolujte, že je v počítači prostředí PowerShell verze 7. x nebo novější s modulem Azure PowerShell verze 5.1.0 nebo novějším. Chcete-li zobrazit verze těchto nástrojů, postupujte podle následujících kroků:

1. V okně PowerShellu zadejte:

    `$PSVersionTable`

    Potvrďte, že `PSVersion` hodnota je 7. x nebo novější. Pokud chcete upgradovat PowerShell, postupujte podle pokynů v tématu [instalace různých verzí PowerShellu](/powershell/scripting/install/installing-powershell).

1. V okně PowerShellu zadejte:

    `Get-Module -ListAvailable Az`

    Pokud se nic neobjeví, nebo pokud je tato verze modulu Azure PowerShell starší než 5.1.0, postupujte podle pokynů v části [instalace Azure PowerShell modulu](/powershell/azure/install-Az-ps) pro upgrade.

Než budete pokračovat, spusťte příkaz `Connect-AzAccount` a vytvořte připojení k Azure.

## <a name="verify-that-a-custom-domain-name-is-available"></a>Ověřte, že je k dispozici vlastní název domény.

Ověřte, zda je k dispozici vlastní doména, kterou chcete použít. Následující kód potvrzuje, že je doména k dispozici pomocí operace [check Domain Availability](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) v REST API Cognitive Services.

> [!TIP]
> Následující *kód nebude v Azure Cloud Shell fungovat.*

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
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

Pokud chcete pro vybraný prostředek řeči povolit vlastní název domény, použijte rutinu [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) .

> [!WARNING]
> Po úspěšném spuštění následujícího kódu vytvoříte vlastní název domény pro prostředek řeči. Pamatujte, že tento název *nelze* změnit.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Tato část vyžaduje nejnovější verzi rozhraní příkazového řádku Azure CLI. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="verify-that-the-custom-domain-name-is-available"></a>Ověřte, že je k dispozici název vlastní domény.

Ověřte, zda je vlastní doména, kterou chcete použít, zadarmo. Použijte metodu [check Domain Availability](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) z REST API Cognitive Services.

Zkopírujte následující blok kódu, vložte upřednostňovaný název vlastní domény a uložte ho do souboru `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Zkopírujte soubor do aktuální složky nebo ho nahrajte do Azure Cloud Shell a spusťte následující příkaz. `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` nahraďte ID vašeho předplatného Azure.

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
## <a name="enable-a-custom-domain-name"></a>Povolení vlastního názvu domény

Pokud chcete pro vybraný prostředek řeči povolit vlastní název domény, použijte příkaz [AZ cognitiveservices Account Account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Vyberte předplatné Azure, které obsahuje prostředek pro rozpoznávání řeči. Pokud má váš účet Azure jenom jedno aktivní předplatné, můžete tento krok přeskočit. `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` nahraďte ID vašeho předplatného Azure.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Nastavte název vlastní domény na vybraný prostředek. Nahraďte vzorové hodnoty parametrů skutečnými a spusťte následující příkaz.

> [!WARNING]
> Po úspěšném provedení následujícího příkazu vytvoříte vlastní název domény pro prostředek řeči. Pamatujte, že tento název *nelze* změnit.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="enable-private-endpoints"></a>Povolit privátní koncové body

Doporučujeme používat [privátní ZÓNU DNS](../../dns/private-dns-overview.md) připojenou k virtuální síti s nezbytnými aktualizacemi privátních koncových bodů. Ve výchozím nastavení se během procesu zřizování vytváří privátní zóna DNS. Pokud používáte vlastní server DNS, možná budete muset změnit také konfiguraci DNS. 

*Před* zřízením privátních koncových bodů pro produkční prostředek pro rozpoznávání řeči se rozhodněte na strategii DNS. A otestujte změny DNS, zejména pokud používáte vlastní server DNS.

K vytvoření soukromých koncových bodů použijte jeden z následujících článků. Tyto články používají jako ukázkový prostředek webovou aplikaci, která umožňuje použití privátních koncových bodů.

- [Vytvoření privátního koncového bodu pomocí Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Vytvoření privátního koncového bodu pomocí Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Vytvoření privátního koncového bodu pomocí Azure CLI](../../private-link/create-private-endpoint-cli.md)

Místo parametrů v článku, který jste zvolili, použijte tyto parametry:

| Nastavení             | Hodnota                                    |
|---------------------|------------------------------------------|
| Typ prostředku       | **Microsoft. Cognitiveservices Account/účty** |
| Prostředek            | **\<your-speech-resource-name>**         |
| Cílový dílčí prostředek | **účtu organizace.**                              |

**DNS pro privátní koncové body:** Přečtěte si obecné principy [DNS pro privátní koncové body v Cognitive Servicesch prostředcích](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Pak ověřte, že konfigurace služby DNS funguje správně, a to tak, že provedete kontroly popsané v následujících částech.

### <a name="resolve-dns-from-the-virtual-network"></a>Řešení DNS z virtuální sítě

Tato kontroly je *povinná*.

Pomocí těchto kroků otestujete vlastní položku DNS z vaší virtuální sítě:

1. Přihlaste se k virtuálnímu počítači umístěnému ve virtuální síti, ke které jste připojili privátní koncový bod. 
1. Otevřete příkazový řádek systému Windows nebo prostředí bash, spusťte příkaz `nslookup` a potvrďte, že úspěšně vyhodnocuje vlastní název domény vašeho prostředku.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. Potvrďte, že IP adresa odpovídá IP adrese vašeho privátního koncového bodu.

### <a name="resolve-dns-from-other-networks"></a>Překlad DNS z jiných sítí

Tuto kontrolu proveďte pouze v případě, že jste v části **síť** svého prostředku povolili možnost **všechny sítě** nebo **vybrané sítě a soukromé koncové body** . 

Pokud plánujete přístup k prostředku pouze pomocí privátního koncového bodu, můžete tuto část přeskočit.

1. Přihlaste se k počítači připojenému k síti s povoleným přístupem k prostředku.
2. Otevřete příkazový řádek systému Windows nebo prostředí bash, spusťte příkaz `nslookup` a potvrďte, že úspěšně vyhodnocuje vlastní název domény vašeho prostředku.

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

> [!NOTE]
> Přeložená IP adresa odkazuje na koncový bod proxy virtuální sítě, který odesílá síťový provoz do privátního koncového bodu pro prostředek Cognitive Services. Chování se bude lišit u prostředku s vlastním názvem domény, ale *bez* privátních koncových bodů. Podrobnosti najdete v [této části](#dns-configuration) .

## <a name="adjust-existing-applications-and-solutions"></a>Upravit stávající aplikace a řešení

Prostředek řeči s povolenou vlastní doménou používá jiný způsob, jak komunikovat se službou Speech Services. To platí pro prostředek řeči s povoleným vlastním doménou s i bez privátních koncových bodů. Informace v této části se týkají obou scénářů.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled"></a>Použití prostředku řeči s vlastním názvem domény a privátním koncovým bodem povoleným

Prostředek řeči s povoleným vlastním názvem domény a privátním koncovým bodem používá jiný způsob, jak komunikovat se službou Speech. V této části se dozvíte, jak použít takový prostředek s rozhraními REST API služby Speech Services a [sadou Speech SDK](speech-sdk.md).

> [!NOTE]
> Prostředek řeči bez privátních koncových bodů, ale s povoleným vlastním názvem domény má také speciální způsob, jak pracovat se službou Speech Services. Tímto způsobem se liší od scénáře prostředku řeči s povoleným privátním koncovým bodem. Pokud máte takový prostředek (například jste provedli prostředek s privátními koncovými body, ale potom se rozhodnete je odebrat), přečtěte si část [použití zdroje řeči s názvem vlastní domény a bez privátních koncových bodů](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>Prostředek řeči s vlastním názvem domény a privátním koncovým bodem: použití s rozhraními REST API

`my-private-link-speech.cognitiveservices.azure.com`Pro tuto část použijeme jako ukázkový název DNS prostředku (vlastní doména) pro řeč.

Služba Speech Services obsahuje rozhraní REST API pro [Převod řeči na text](rest-speech-to-text.md) a [Převod textu na řeč](rest-text-to-speech.md). V případě scénáře s povoleným privátním koncovým bodem zvažte následující informace.

Převod řeči na text má dvě rozhraní REST API. Každé rozhraní API slouží jiným účelům, používá jiné koncové body a vyžaduje jiný přístup, když ho používáte ve scénáři s povoleným privátním koncovým bodem.

Rozhraní REST API pro text jsou:
- [Převod řeči na text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30), který se používá ke [dávkovému přepisu](batch-transcription.md) a [Custom Speech](custom-speech-overview.md). v 3.0 je [následníkem v 2.0](./migrate-v2-to-v3.md)
- [Převod řeči na text REST API pro krátký zvuk](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio), který se používá pro online přepis 

Použití REST API řeči na text pro krátké zvuky a REST API převodu textu na řeč ve scénáři privátního koncového bodu je stejné. Odpovídá [případu sady Speech SDK](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) popsanému dále v tomto článku. 

Převod řeči na text REST API v 3.0 používá jinou sadu koncových bodů, takže vyžaduje jiný přístup k scénáři s povoleným privátním koncovým bodem.

Další pododdíly popisují oba případy.

##### <a name="speech-to-text-rest-api-v30"></a>Převod řeči na text REST API v 3.0

Zdroje řeči obvykle používají [Cognitive Services regionální koncové body](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) pro komunikaci s [převodem řeči na text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Tyto prostředky mají následující formát pojmenování: <p/>`{region}.api.cognitive.microsoft.com`.

Toto je ukázková adresa URL požadavku:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```

> [!NOTE]
> V [tomto článku](sovereign-clouds.md) najdete Azure Government a koncových bodů Azure Čína.

Po povolení vlastní domény pro prostředek řeči (což je nutné pro privátní koncové body) Tento prostředek bude používat následující vzor názvu DNS pro základní REST API koncový bod: <p/>`{your custom name}.cognitiveservices.azure.com`.

To znamená, že v našem příkladu bude název koncového bodu REST API: <p/>`my-private-link-speech.cognitiveservices.azure.com`.

A ukázkovou adresu URL požadavku je potřeba převést na:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Tato adresa URL by měla být dosažitelná z virtuální sítě s připojeným privátním koncovým bodem (poskytuje [správné rozlišení DNS](#resolve-dns-from-the-virtual-network)).

Po povolení vlastního názvu domény pro prostředek řeči obvykle vyměníte název hostitele ve všech adresách URL žádostí s novým názvem hostitele vlastní domény. Všechny ostatní části žádosti (jako cesta `/speechtotext/v3.0/transcriptions` v předchozím příkladu) zůstávají stejné.

> [!TIP]
> Někteří zákazníci vyvíjí aplikace, které používají část názvu DNS místního koncového bodu (například k odeslání žádosti do prostředku řeči nasazeného v konkrétní oblasti Azure).
>
> Vlastní doména pro prostředek řeči neobsahuje *žádné* informace o oblasti, kde je prostředek nasazený. Proto nebude logika aplikace popsaná výše *fungovat a* bude nutné ji změnit.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>REST API řeči na text pro krátký zvuk a převod textu na řeč REST API

[REST API řeči na text pro krátký zvuk](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) a [Převod textu na řeč, REST API](rest-text-to-speech.md) použít dva typy koncových bodů:
- [Cognitive Services místní koncové body](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) pro komunikaci s Cognitive Services REST API k získání autorizačního tokenu
- Speciální koncové body pro všechny ostatní operace

> [!NOTE]
> V [tomto článku](sovereign-clouds.md) najdete Azure Government a koncových bodů Azure Čína.

Podrobný popis speciálních koncových bodů a způsobu, jakým se má jejich adresa URL transformovat pro prostředek řeči privátního koncového bodu, je k dispozici v [této části](#construct-endpoint-url) o použití se sadou Speech SDK. Stejný princip, který je popsaný pro sadu SDK, se vztahuje na REST API pro krátké zvuky a převod textu na řeč REST API.

Seznamte se s materiály v podčásti uvedené v předchozím odstavci a podívejte se na následující příklad. Příklad popisuje REST API převodu textu na řeč. Použití REST API řeči na text pro krátký zvuk je plně ekvivalentní.

> [!NOTE]
> Pokud používáte REST API řeči pro krátké zvuky a text na řeč REST API ve scénářích privátního koncového bodu, použijte klíč předplatného předaný `Ocp-Apim-Subscription-Key` hlavičkou. (Přečtěte si podrobnosti o převodu [řeči na text REST API pro krátké audio](rest-speech-to-text.md#request-headers) a [Převod textu na řeč REST API](rest-text-to-speech.md#request-headers))
>
> Pomocí autorizačního tokenu a jeho předáním do zvláštního koncového bodu přes `Authorization` hlavičku budou fungovat *jenom* v případě, že jste povolili možnost přístup **všech sítí** v části **síť** vašeho prostředku řeči. V ostatních případech se `Forbidden` `BadRequest` při pokusu o získání autorizačního tokenu zobrazí buď nebo.

**Příklad použití převodu textu na řeč REST API**

Jako ukázku `my-private-link-speech.cognitiveservices.azure.com` názvu DNS prostředku (vlastní doména) použijeme západní Evropa jako ukázkovou oblast Azure. Vlastní název domény `my-private-link-speech.cognitiveservices.azure.com` v našem příkladu patří do prostředku řeči vytvořeného v západní Evropa oblasti.

Chcete-li získat seznam hlasů podporovaných v dané oblasti, proveďte následující požadavek:

```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
Další podrobnosti najdete [REST API dokumentaci pro převod textu na řeč](rest-text-to-speech.md).

Pro prostředek řeči s povoleným privátním koncovým bodem je potřeba upravit adresu URL koncového bodu pro stejnou operaci. Stejný požadavek bude vypadat takto:

```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
Přečtěte si podrobné vysvětlení v podčásti [Adresa URL koncového bodu konstrukce](#construct-endpoint-url) pro sadu Speech SDK.

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>Prostředek řeči s vlastním názvem domény a privátním koncovým bodem: využití sadou Speech SDK

Použití sady Speech SDK s vlastním názvem domény a zdroji řeči s povoleným soukromým koncovým bodem vyžaduje, abyste zkontrolovali a pravděpodobně změnili kód aplikace.

`my-private-link-speech.cognitiveservices.azure.com`Pro tuto část použijeme jako ukázkový název DNS prostředku (vlastní doména) pro řeč.

##### <a name="construct-endpoint-url"></a>Vytvořit adresu URL koncového bodu

V rámci scénářů sady SDK (stejně jako v případě krátkých zvuků a REST APIch scénářů psaní textu na text REST API) využívají zdroje řeči vyhrazené regionální koncové body pro různé nabídky služeb. Formát názvu DNS pro tyto koncové body:

`{region}.{speech service offering}.speech.microsoft.com`

Příklad názvu DNS:

`westeurope.stt.speech.microsoft.com`

Všechny možné hodnoty oblasti (první prvek názvu DNS) jsou uvedené v [oblastech podporovaných službou Speech](regions.md). (Viz [Tento článek](sovereign-clouds.md) pro Azure Government a koncové body Azure Čína.) Následující tabulka uvádí možné hodnoty pro nabídku služby pro rozpoznávání řeči (druhý prvek názvu DNS):

| Hodnota názvu DNS | Nabídka služby pro rozpoznávání řeči                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Vlastní příkazy](custom-commands.md)                       |
| `convai`       | [Přepis konverzace](conversation-transcription.md) |
| `s2s`          | [Překlad řeči](speech-translation.md)                 |
| `stt`          | [Převod řeči na text](speech-to-text.md)                         |
| `tts`          | [Převod textu na řeč](text-to-speech.md)                         |
| `voice`        | [Vlastní hlas](how-to-custom-voice.md)                      |

Proto v předchozím příkladu ( `westeurope.stt.speech.microsoft.com` ) představuje koncový bod řeči a text v západní Evropa.

Koncové body s povoleným soukromým koncovým bodem komunikují se službami Speech přes speciální proxy server. Z tohoto důvodu je *nutné změnit adresy URL připojení koncového bodu*. 

Adresa URL standardního koncového bodu vypadá nějak takto: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Adresa URL privátního koncového bodu vypadá nějak takto: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Příklad 1.** Aplikace komunikuje pomocí následující adresy URL (rozpoznávání řeči používá základní model pro AMERICKou angličtinu v Západní Evropa):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Pokud chcete použít ve scénáři s povoleným privátním koncovým bodem, pokud je název vlastní domény prostředku pro rozpoznávání řeči `my-private-link-speech.cognitiveservices.azure.com` , musíte upravit adresu URL takto:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Všimněte si podrobností:

- Název hostitele `westeurope.stt.speech.microsoft.com` je nahrazen názvem hostitele vlastní domény `my-private-link-speech.cognitiveservices.azure.com` .
- Druhý prvek původního názvu DNS ( `stt` ) se stal prvním prvkem cesty URL a předchází původní cestě. Původní adresa URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` se tak bude `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Příklad 2.** Aplikace používá následující adresu URL pro syntetizaci řeči v Západní Evropa pomocí vlastního hlasového modelu:
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Následující ekvivalentní adresa URL používá povolený privátní koncový bod, ve kterém je vlastní název domény prostředku řeči `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Stejný princip v příkladu 1 je použit, ale klíčovým prvkem tento čas je `voice` .

##### <a name="modifying-applications"></a>Úprava aplikací

Chcete-li upravit kód, postupujte podle těchto kroků:

1. Určete adresu URL koncového bodu aplikace:

   - [Povolte protokolování pro aplikaci](how-to-use-logging.md) a spusťte ji pro protokolování aktivity.
   - V souboru protokolu vyhledejte `SPEECH-ConnectionUrl` . V odpovídajících řádcích `value` parametr obsahuje úplnou adresu URL, kterou vaše aplikace používala k přístupu ke službám Speech.

   Příklad:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   Adresa URL, kterou používá aplikace v tomto příkladu:

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. Vytvoření `SpeechConfig` instance pomocí úplné adresy URL koncového bodu:

   1. Upravte koncový bod, který jste právě určili, jak je popsáno v oddílu [Adresa URL předchozího koncového bodu konstrukce](#construct-endpoint-url) .

   1. Změňte způsob vytvoření instance `SpeechConfig` . Pravděpodobně vaše aplikace používá něco podobného:
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      To nebude fungovat pro prostředek řeči s povoleným privátním koncovým bodem z důvodu změn názvu hostitele a adresy URL, které jsme popsali v předchozích částech. Pokud se pokusíte spustit stávající aplikaci bez jakýchkoli úprav pomocí klíče privátního prostředku s povoleným koncovým bodem, zobrazí se chyba ověřování (401).

      Aby to fungovalo, změňte způsob vytvoření instance `SpeechConfig` třídy a použijte inicializaci "z koncového bodu"/"s koncovým bodem". Předpokládejme, že máme definované tyto dvě proměnné:
      - `subscriptionKey` obsahuje klíč prostředku řeči s povoleným privátním koncovým bodem.
      - `endPoint` obsahuje adresu URL koncového bodu s úplnou *úpravou* (pomocí typu vyžadovaného odpovídajícím programovacím jazykem). V našem příkladu by tato proměnná měla obsahovat:
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      Vytvoření `SpeechConfig` instance:
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
> Parametry dotazu zadané v identifikátoru URI koncového bodu se nemění, i když jsou nastavená jinými rozhraními API. Pokud je například jazyk rozpoznávání definován v identifikátoru URI jako parametr dotazu `language=en-US` a je také nastaven na hodnotu `ru-RU` prostřednictvím odpovídající vlastnosti, bude použito nastavení jazyka v identifikátoru URI. Platný jazyk je pak `en-US` .
>
> Parametry nastavené v identifikátoru URI koncového bodu mají vždy přednost. Jiná rozhraní API mohou přepsat pouze parametry, které nejsou zadány v identifikátoru URI koncového bodu.

Po provedení této změny by vaše aplikace měla fungovat s prostředky řeči s povoleným soukromým koncovým bodem. Pracujeme na bezproblémové podpoře scénářů privátních koncových bodů.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints"></a>Použití zdroje řeči s vlastním názvem domény a bez privátních koncových bodů

V tomto článku jsme provedli několik případů, kdy je povolení vlastní domény pro prostředek řeči *nevratné*. Takový prostředek bude používat jiný způsob komunikace se službami Speech, a to v porovnání s těmi, které používají [názvy regionálních koncových bodů](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

V této části se dozvíte, jak používat prostředek řeči s povoleným názvem vlastní domény, ale *bez* jakýchkoli privátních koncových bodů s rozhraními REST API služby Speech a [sadou Speech SDK](speech-sdk.md). Může se jednat o prostředek, který se použil ve scénáři privátního koncového bodu, ale pak se jeho soukromé koncové body odstranily.

#### <a name="dns-configuration"></a>Konfigurace DNS

Zapamatujte si, jak se vlastní název DNS prostředku řeči s povoleným soukromým koncovým bodem [vyřeší z veřejných sítí](#resolve-dns-from-other-networks). V tomto případě se IP adresa přeloží na koncový bod proxy serveru pro virtuální síť. Tento koncový bod slouží k odesílání síťových přenosů do prostředku Cognitive Services s povoleným soukromým koncovým bodem.

Po odebrání *všech* privátních koncových bodů prostředků (nebo hned po povolení vlastního názvu domény) se ale záznam CNAME prostředku pro rozpoznávání řeči znovu zřídí. Nyní odkazuje na IP adresu odpovídajícího [Cognitive Servicesho místního koncového bodu](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

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
Porovnejte ho s výstupem z [této části](#resolve-dns-from-other-networks).

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>Prostředek řeči s vlastním názvem domény a bez privátních koncových bodů: použití s rozhraními REST API

##### <a name="speech-to-text-rest-api-v30"></a>Převod řeči na text REST API v 3.0

Použití převodu řeči na text REST API v 3.0 je plně stejné jako u [prostředků řeči s povoleným soukromým koncovým bodem](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>REST API řeči na text pro krátký zvuk a převod textu na řeč REST API

V takovém případě použití REST API řeči na text pro krátký zvuk a použití REST API převodu textu na řeč nemá žádné rozdíly od obecného případu s jednou výjimkou. (Podívejte se na následující poznámku.) Obě rozhraní API byste měli použít, jak je popsáno v tématu [Převod řeči na text REST API pro krátké zvuky](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) a REST API v dokumentaci pro [Převod textu na řeč](rest-text-to-speech.md) .

> [!NOTE]
> Pokud pro krátké audio a řeč REST API v vlastních scénářích domény používáte REST API pro převod textu na text, použijte klíč předplatného předaný `Ocp-Apim-Subscription-Key` hlavičkou. (Přečtěte si podrobnosti o převodu [řeči na text REST API pro krátké audio](rest-speech-to-text.md#request-headers) a [Převod textu na řeč REST API](rest-text-to-speech.md#request-headers))
>
> Pomocí autorizačního tokenu a jeho předáním do zvláštního koncového bodu přes `Authorization` hlavičku budou fungovat *jenom* v případě, že jste povolili možnost přístup **všech sítí** v části **síť** vašeho prostředku řeči. V ostatních případech se `Forbidden` `BadRequest` při pokusu o získání autorizačního tokenu zobrazí buď nebo.

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>Prostředek řeči s vlastním názvem domény a bez privátních koncových bodů: použití se sadou Speech SDK

Použití sady Speech SDK se zdroji řeči s podporou vlastních domén *bez* privátních koncových bodů je stejné jako u obecného případu, jak je popsáno v [dokumentaci k sadě Speech SDK](speech-sdk.md).

V případě, že jste změnili kód pro použití s [prostředkem řeči s povoleným privátním koncovým bodem](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk), vezměte v úvahu následující skutečnosti.

V části o [prostředcích řeči s povoleným soukromým koncovým bodem](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)jsme zjistili, jak určit adresu URL koncového bodu, upravit ji a zajistit, aby fungovala s inicializací instance třídy z koncového bodu/s koncovým bodem `SpeechConfig` .

Pokud se ale pokusíte spustit stejnou aplikaci po odebrání všech privátních koncových bodů (pro odpovídající opětovné zřízení záznamu DNS nějaký čas), zobrazí se vnitřní chyba služby (404). Důvodem je to, že [záznam DNS](#dns-configuration) teď odkazuje na koncový bod místní Cognitive Services místo na proxy virtuální sítě a tady se nenašly cesty URL `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

Je nutné vrátit aplikaci zpět do standardního vytváření instancí `SpeechConfig` ve stylu následujícího kódu:

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Další informace

* [Azure Private Link](../../private-link/private-link-overview.md)
* [Speech SDK](speech-sdk.md)
* [Rozhraní REST API pro převod řeči na text](rest-speech-to-text.md)
* [Rozhraní REST API pro převod textu na řeč](rest-text-to-speech.md)