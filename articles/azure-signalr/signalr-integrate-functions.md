---
title: Kurz integrace Azure Functions se službou Azure SignalR | Microsoft Docs
description: V tomto kurzu zjistíte, jak používat Azure Functions se službou Azure SignalR.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>Kurz: Integrace Azure Functions se službou Azure SignalR

V tomto kurzu se používá aplikace chatovací místnosti vytvořená v předchozích kurzech. Pokud jste to ještě neudělali, nejprve dokončete cvičení [Vytvoření chatovací místnosti s využitím služby SignalR](signalr-quickstart-dotnet-core.md) a [Ověřování pomocí služby Azure SignalR](./signalr-authenticate-oauth.md). 

Běžným scénářem u aplikací pracujících v reálném čase je publikování aktualizací obsahu pocházejících ze serveru do webových klientů. Skvělým kandidátem pro generování těchto aktualizací obsahu je služba [Azure Functions](../azure-functions/functions-overview.md). Klíčovou výhodou používání služby Azure Functions je možnost spouštět kód na vyžádání, aniž byste se museli starat o architekturu celé aplikace nebo infrastrukturu, na které se spouští. Kromě toho platíte pouze za čas, kdy je váš kód skutečně spuštěný.  

Za normálních okolností by tento scénář představoval problém v případě, že byste se snažili použít službu SignalR, protože služba SignalR se pokouší zachovat připojení mezi klientem a serverem za účelem nabízení aktualizací obsahu. Vzhledem k tomu, že se kód spouští pouze na vyžádání, není možné zachovat připojení. Služba Azure SignalR však dokáže zajistit podporu tohoto scénáře, protože za vás spravuje připojení za běhu.

V tomto kurzu použijete službu Azure Functions ke generování zpráv na začátku každé minuty pomocí funkce časovače. Funkce bude publikovat zprávy do všech klientů chatovací místnosti vytvořené v předchozích kurzech. Další informace o funkcích časovače najdete v tématu věnovaném [funkci časovače](../azure-functions/functions-create-scheduled-function.md).

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Skvělou volbou je však editor [Visual Studio Code](https://code.visualstudio.com/), který je dostupný pro platformy Windows, macOS a Linux.

Kód pro tento kurz je k dispozici ke stažení v [úložišti GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer).

![Chatovací aplikace se zprávami ze serveru](./media/signalr-integrate-functions/signalr-functions-complete.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nové funkce časovače ve službě Azure Functions pomocí Azure CLI
> * Konfigurace funkce časovače pro nasazení z místního úložiště Git
> * Připojení časovače ke službě SignalR za účelem nabízení aktualizací každou minutu

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* [Git](https://git-scm.com/)
* [Sada .NET Core SDK](https://www.microsoft.com/net/download/windows) 
* [Nakonfigurovaná služba Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Stáhněte nebo naklonujte úložiště GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>Vytvoření Function App

Je potřeba vytvořit aplikaci funkcí, která definuje spouštěcí prostředí pro vaše funkce. Aplikace funkcí umožňuje seskupit několik funkcí jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. Další informace najdete v tématu [Vytvoření první funkce pomocí Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md).

V této části pomocí služby Azure Cloud Shell vytvoříte novou aplikaci funkcí Azure nakonfigurovanou pro nasazení z místního úložiště Git. 

Při vytváření prostředků aplikace funkcí je vytvořte ve stejné skupině prostředků, kterou jste používali v předchozích kurzech. Tento přístup usnadňuje správu všech prostředků pro kurzy.

Zkopírujte níže uvedený skript do textového editoru a nahraďte hodnoty parametrů proměnných hodnotami pro vaše prostředky. Aktualizovaný skript zkopírujte a vložte do služby Azure Cloud Shell a stisknutím klávesy **Enter** vytvořte účet úložiště a aplikaci funkcí.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| Parametr | Popis |
| -------------------- | --------------- |
| ResourceGroupName | Tento název skupiny prostředků byl navržený v předchozích kurzech. Je vhodné udržovat všechny prostředky pro kurzy seskupené. Použijte stejnou skupinu prostředků, kterou jste používali v předchozích kurzech. | 
| location | Aktualizujte tuto proměnnou na stejné umístění, ve kterém jste vytvořili skupinu prostředků v předchozích kurzech. | 
| functionappName | Aktualizujte tuto proměnnou na jedinečný název vaší nové aplikace funkcí. Například signalrfunctionapp22665120. | 
| storageAccountName | Zadejte název nového účtu úložiště, do kterého se uloží kód a nastavení aplikace funkcí. | 



## <a name="configure-the-function-app"></a>Konfigurace aplikace Function App

V této části nakonfigurujete aplikaci funkcí s použitím nastavení aplikace, které obsahuje připojovací řetězec pro váš prostředek služby Azure SignalR. Kód vaší funkce se pomocí tohoto nastavení připojí k chatovací místnosti a bude do ní publikovat zprávy. Pro aplikaci funkcí také nakonfigurujete nasazení z místního úložiště Git.

Zkopírujte níže uvedený skript a nahraďte hodnoty parametrů. Vložte aktualizovaný skript do služby Azure Cloud Shell a stiskněte **Enter**.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| Parametr | Popis |
| -------------------- | --------------- |
| ResourceGroupName | Tento název skupiny prostředků byl navržený v předchozích kurzech. Je vhodné udržovat všechny prostředky pro kurzy seskupené. Použijte stejnou skupinu prostředků, kterou jste používali v předchozích kurzech. | 
| functionappName | Aktualizujte tuto proměnnou na jedinečný název vaší nové aplikace funkcí. Například signalrfunctionapp22665120. | 
| connstring | Zadejte připojovací řetězec pro váš prostředek služby SignalR. Tento připojovací řetězec můžete získat na stránce prostředku služby SignalR na webu Azure Portal kliknutím na **Klíče** v části **NASTAVENÍ**. | 



Poznamenejte si adresu URL nasazení Gitu vrácenou předchozím příkazem. Tuto adresu URL použijete k nasazení kódu funkce.


## <a name="the-timer-function"></a>Funkce časovače

Ukázkovou funkci časovače najdete v adresáři */samples/Timer* ve staženém nebo naklonovaném úložišti GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer). Kód funkce časovače se nachází v souboru *TimerFunction.cs*. Tento kód pomocí připojovacího řetězce uloženého s výchozím klíčem konfigurace (*Azure:SignalR:ConnectionString*) vytvoří pro centrum proxy server. Vzhledem k tomu, že se kód funkce spouští na straně serveru, není důvod vyžadovat jeho ověřování jako u běžného klienta. Kód je důvěryhodný a může používat připojovací řetězec. Díky tomuto proxy serveru centra může kód funkce volat jakoukoli metodu, kterou jste v centru definovali. Když se aktivuje trigger, kód zavolá metodu `BroadcastMessage`, která publikuje zprávu obsahující aktuální čas.

Trigger kódu funkce je *timerTrigger* definovaný ve vazbě v souboru *TimerFunction/function.json*. Obsahuje [výraz CRON](https://wikipedia.org/wiki/Cron#CRON_expression) nastavující aktivaci triggeru časovače na začátek každé minuty.

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>Vytvoření funkce časovače

Pomocí [rozhraní příkazového řádku .NET Core](https://docs.microsoft.com/dotnet/core/tools/) a následujících pokynů vytvořte funkci a připravte ji na nasazení:

1. Přejděte do podadresáře */samples/Timer* staženého nebo naklonovaného úložiště GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples).

2. Pomocí následujícího příkazu obnovte balíčky NuGet:

        dotnet restore

3. Sestavte aplikaci funkcí *Timer* pomocí následujícího příkazu:

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>Vytvoření a nasazení místního úložiště Git

1. V prostředí Git přejděte do podadresáře sestavení */samples/Timer/bin/Release/net461*.

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. Inicializujte adresář jako nové úložiště Git pomocí následujícího příkazu:

        git init

3. Přidejte nové potvrzení pro všechny soubory v adresáři sestavení.

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. Přidejte vzdálený koncový bod pro adresu URL nasazení Gitu, kterou jste si poznamenali během konfigurace aplikace funkcí:

        git remote add Azure <enter your Git deployment URL>

5. Nasazení aplikace funkcí

        git push Azure master

   Po nasazení kódu se časovač začne okamžitě aktivovat každou minutu a bude spouštět váš kód.

## <a name="test-the-chat-app"></a>Test chatovací aplikace

Přejděte do chatovací aplikace a funkce časovače, kterou jste právě vytvořili, teď bude na začátku každé minuty hlásit čas.

![Chatovací aplikace se zprávami ze serveru](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v testování aplikace, můžete vytvořené prostředky zachovat.

V opačném případě, pokud jste už s ukázkovou aplikací skončili, můžete prostředky Azure odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.
> 
> 

Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

Do textového pole **Filtrovat podle názvu...** zadejte název vaší skupiny prostředků. V pokynech v tomto článku se používala skupina prostředků *SignalRTestResources*. Ve výsledcích hledání klikněte na **...** u vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

   
![Odstranění](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění zadáním názvu vaší skupiny prostředků a klikněte na **Odstranit**.
   
Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak prostřednictvím integrace se službou Azure Functions nabízet klientům aktualizace na základě triggerů Azure Functions. Další informace o použití služby Azure SignalR najdete v ukázkách Azure CLI pro službu SignalR.

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku pro službu Azure SignalR](./signalr-cli-samples.md)



