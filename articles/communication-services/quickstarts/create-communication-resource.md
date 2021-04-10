---
title: Rychlý Start – vytváření a Správa prostředků v komunikačních službách Azure
titleSuffix: An Azure Communication Services quickstart
description: V tomto rychlém startu se dozvíte, jak vytvořit a spravovat první prostředek služby Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-azcli-net-ps
ms.openlocfilehash: aabb8bdf4105702aa623c45bc291770b05b8279e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726767"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>Rychlý Start: vytvoření a Správa prostředků komunikačních služeb

Začněte s komunikačními službami Azure tím, že zřizujete první prostředek komunikační služby. Prostředky komunikačních služeb lze zřídit prostřednictvím [Azure Portal](https://portal.azure.com) nebo pomocí sady .NET Management SDK. Sada Management SDK a Azure Portal vám umožní vytvářet, konfigurovat, aktualizovat a odstraňovat vaše prostředky a rozhraní pomocí [Azure Resource Manager](../../azure-resource-manager/management/overview.md), nasazení a služby pro správu Azure. Všechny funkce, které jsou k dispozici v sadách SDK, jsou k dispozici v Azure Portal. 


> [!WARNING]
> Mějte na paměti, že když jsou komunikační služby k dispozici v několika geografických oblastech, aby bylo možné získat telefonní číslo, musí mít zdroj umístění dat nastavené na US. Všimněte si také, že během veřejné verze Preview nejde přenést prostředky komunikace do jiného předplatného.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-azcli"
[!INCLUDE [Azure CLI](./includes/create-resource-azcli.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

::: zone pivot="platform-powershell"
[!INCLUDE [PowerShell](./includes/create-resource-powershell.md)]
::: zone-end


## <a name="access-your-connection-strings-and-service-endpoints"></a>Přístup k vašim připojovacím řetězcům a koncovým bodům služby

Připojovací řetězce umožňují sady SDK komunikačních služeb připojit a ověřit v Azure. K připojovacím řetězcům služby Communication Services a koncovým bodům služby můžete přistupovat z Azure Portal nebo programově pomocí rozhraní API Azure Resource Manager.

Po přechodu na prostředek služby Communication Services vyberte z navigační nabídky **klíče** a zkopírujte **připojovací řetězec** nebo hodnoty **koncového bodu** pro použití sady SDK služby komunikace. Všimněte si, že máte přístup k primárním a sekundárním klíčům. To může být užitečné ve scénářích, kdy byste chtěli poskytnout dočasný přístup k prostředkům komunikačních služeb třetí straně nebo přípravnému prostředí.

:::image type="content" source="./media/key.png" alt-text="Snímek stránky s klíčem komunikačních služeb":::

K informacím o klíčích můžete také přistupovat pomocí Azure CLI, jako je vaše skupina prostředků nebo klíče pro konkrétní prostředek. 

Nainstalujte rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) a pomocí následujícího příkazu se přihlaste. Abyste se mohli připojit k účtu Azure, budete muset zadat svoje přihlašovací údaje.
```azurecli
az login
```

Teď máte přístup k důležitým informacím o vašich prostředcích.
```azurecli
az communication list --resource-group "<resourceGroup>"

az communication list-key --name "<communicationName>" --resource-group "<resourceGroup>"
```

Pokud chcete vybrat konkrétní předplatné, můžete také zadat ```--subscription``` příznak a zadat ID předplatného.
```
az communication list --resource-group  "resourceGroup>"  --subscription "<subscriptionID>"

az communication list-key --name "<communicationName>" --resource-group "resourceGroup>" --subscription "<subscriptionID>"
```

## <a name="store-your-connection-string"></a>Uložení připojovacího řetězce

Sady SDK komunikačních služeb používají připojovací řetězce k autorizaci požadavků na komunikační služby. Máte několik možností pro uložení připojovacího řetězce:

* Aplikace spuštěná na ploše nebo v zařízení může uložit připojovací řetězec do souboru **app.config** nebo **web.config** . Přidejte připojovací řetězec do oddílu **appSettings** v těchto souborech.
* Aplikace spuštěná v Azure App Service může připojovací řetězec Uložit do [nastavení aplikace App Service](../../app-service/configure-common.md). Přidejte připojovací řetězec do části **připojovací řetězce** na kartě nastavení aplikace v rámci portálu.
* Připojovací řetězec můžete uložit v [Azure Key Vault](../../data-factory/store-credentials-in-key-vault.md).
* Pokud používáte aplikaci místně, můžete uložit připojovací řetězec do proměnné prostředí.

### <a name="store-your-connection-string-in-an-environment-variable"></a>Uložení připojovacího řetězce do proměnné prostředí

Chcete-li konfigurovat proměnnou prostředí, otevřete okno konzoly a z následujících karet vyberte svůj operační systém. Nahraďte `<yourconnectionstring>` skutečným připojovacím řetězcem.

#### <a name="windows"></a>[Windows](#tab/windows)

Otevřete okno konzoly a zadejte následující příkaz:

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

Po přidání proměnné prostředí možná bude nutné restartovat všechny spuštěné programy, které budou potřebovat číst tuto proměnnou prostředí, a to včetně okna konzoly. Například pokud používáte Visual Studio jako editor, před spuštěním příkladu restartujte Visual Studio.

#### <a name="macos"></a>[macOS](#tab/unix)

Upravte **. zshrc** a přidejte proměnnou prostředí:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.zshrc`, aby se změny projevily. Pokud jste vytvořili proměnnou prostředí s otevřeným IDE, může být nutné zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, aby bylo možné získat přístup k proměnné.

#### <a name="linux"></a>[Linux](#tab/linux)

Upravte **.bash_profile** a přidejte proměnnou prostředí:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bash_profile`, aby se změny projevily. Pokud jste vytvořili proměnnou prostředí s otevřeným IDE, může být nutné zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, aby bylo možné získat přístup k proměnné.

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

Pokud máte při odstraňování prostředků přiřazená nějaká telefonní čísla, budou se telefonní čísla z vašeho prostředku automaticky vydávat ve stejnou dobu.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak:

> [!div class="checklist"]
> * Vytvoření prostředku Communication Services
> * Konfigurace geografických a značek prostředků
> * Přístup ke klíčům pro daný prostředek
> * Odstranit prostředek

> [!div class="nextstepaction"]
> [Vytvoření prvních přístupových tokenů uživatele](access-tokens.md)
