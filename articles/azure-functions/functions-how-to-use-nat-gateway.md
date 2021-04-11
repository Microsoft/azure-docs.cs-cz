---
title: Řízení Azure Functions odchozí IP adresy pomocí brány NAT služby Azure Virtual Network
description: Podrobný kurz, ve kterém se dozvíte, jak nakonfigurovat překlad adres (NAT) pro funkci připojenou k virtuální síti Azure
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 5bb491e367ed813f09197a193745c231261c88c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658113"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>Kurz: řízení Azure Functions odchozí IP adresy pomocí brány NAT služby Azure Virtual Network

Překlad adres (NAT) pro virtuální sítě zjednodušuje pouze odchozí připojení k Internetu. Při konfiguraci v podsíti všechna odchozí připojení používá vaše zadané statické veřejné IP adresy. Překlad adres (NAT) může být užitečný pro Azure Functions nebo Web Apps, které potřebují využívat službu třetí strany, která jako bezpečnostní opatření používá povolených IP adresu. Další informace najdete v tématu [co je Virtual Network překlad adres (NAT)?](../virtual-network/nat-overview.md).

V tomto kurzu se dozvíte, jak používat NAT virtuální sítě ke směrování odchozího provozu z funkce aktivované protokolem HTTP. Tato funkce umožňuje kontrolu vlastní odchozí IP adresy. V tomto kurzu:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření aplikace funkcí plánu Premium
> * Vytvoření veřejné IP adresy
> * Vytvoření brány NAT
> * Konfigurace aplikace Functions pro směrování odchozích přenosů přes bránu NAT

## <a name="topology"></a>Topologie

Následující diagram znázorňuje architekturu řešení, kterou vytvoříte:

![Uživatelské rozhraní pro integraci brány NAT](./media/functions-how-to-use-nat-gateway/topology.png)

Funkce spuštěné v plánu Premium mají stejné možnosti hostování jako webové aplikace v Azure App Service, což zahrnuje funkci Integrace virtuální sítě. Další informace o integraci virtuální sítě, včetně řešení potíží a pokročilých konfigurací, najdete v tématu [integrace aplikace do služby Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu je důležité pochopit IP adresy a podsítě. Můžete začít s [tímto článkem, který se zabývá základy adresování a podsítí](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Mnoho dalších článků a videí je k dispozici online.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud jste už dokončili [funkce integrace s kurzem virtuální sítě Azure](./functions-create-vnet.md) , můžete přeskočit a [vytvořit funkci triggeru http](#create-function).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**. Z Azure Marketplace vyberte **síť**  >  **virtuální síť**.

1. V části **vytvořit virtuální síť** zadejte nebo vyberte zadaná nastavení, jak je uvedeno v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup* a pak vyberte **OK**. |
    | Name | Zadejte *myResourceGroup-VNet*. |
    | Umístění | Vyberte **USA – východ**.|

1. Vyberte **Další: IP adresy** a pro **adresní prostor IPv4** zadejte *10.10.0.0/16*.

1. Vyberte **Přidat podsíť** a potom zadejte *kurz – NET* pro **název podsítě** a *10.10.1.0/24* pro **Rozsah adres podsítě**.

    ![Karta IP adresy pro vytvoření virtuální sítě](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. Vyberte **Přidat** a pak vybrat **zkontrolovat + vytvořit**. Ponechte REST jako výchozí a vyberte **vytvořit**.

1. V nástroji **vytvořit virtuální síť** vyberte **vytvořit**.

V dalším kroku vytvoříte aplikaci funkcí v [plánu Premium](functions-premium-plan.md). Tento plán poskytuje škálování bez serveru při podpoře integrace virtuální sítě.

## <a name="create-a-function-app-in-a-premium-plan"></a>Vytvoření aplikace Function App v plánu Premium

> [!NOTE]  
> Pro nejlepší prostředí v tomto kurzu vyberte .NET for runtime stack a vyberte Windows pro operační systém. Můžete také vytvořit aplikaci Function App ve stejné oblasti jako vaše virtuální síť.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>Připojení aplikace Function App k virtuální síti

Teď můžete aplikaci Function App připojit k virtuální síti.

1. V aplikaci Function App vyberte v nabídce vlevo možnost sítě a potom v části **Integrace virtuální** **sítě** vyberte **pro konfiguraci možnost kliknutím sem**.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="Výběr možnosti sítě v aplikaci Function App":::

1. Na stránce **Integrace virtuální sítě** vyberte **Přidat virtuální síť**.

1. V části **stav síťové funkce** použijte nastavení v tabulce pod obrázkem:

    ![Definovat funkci Virtual Network aplikace Function App](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup – VNet | Tato virtuální síť je ta, kterou jste vytvořili dříve. |
    | **Podsíť** | Vytvořit novou podsíť | Vytvořte podsíť ve virtuální síti, kterou bude používat aplikace Function App. Integrace virtuální sítě musí být nakonfigurovaná tak, aby používala prázdnou podsíť. |
    | **Název podsítě** | Function-Net | Název nové podsítě. |
    | **Blok adres virtuální sítě** | 10.10.0.0/16 | Měli byste mít definován pouze jeden blok adres. |
    | **Blok adres podsítě** | 10.10.2.0/24   | Velikost podsítě omezuje celkový počet instancí, na které může vaše aplikace Functions plánu Premium škálovat. V tomto příkladu `/24` se používá podsíť s 254 dostupnými adresami hostitele. Tato podsíť je předem zřízená, ale je snadno vypočtená. |

1. Vyberte **OK** , chcete-li přidat podsíť. Zavřete stránky stav **Integrace virtuální** **sítě a síťové funkce** a vraťte se na stránku Function App.

Aplikace Function app teď má přístup k virtuální síti. Dále do aplikace Function App přidáte funkci aktivovanou protokolem HTTP.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Vytvoření funkce triggeru HTTP

1. V levé nabídce okna **Functions (funkce** ) vyberte **funkce** a pak v horní nabídce vyberte **Přidat** . 
 
1. V **novém okně funkce** vyberte **Trigger http** a přijměte výchozí název **nové funkce** nebo zadejte nový název. 

1. V **kódu + test** nahraďte kód generovaný šablonou C# (. csx) pomocí následujícího kódu: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    Tento kód volá externí web, který vrací IP adresu volajícího, což v tomto případě je tato funkce. Tato metoda vám umožní snadno určit odchozí IP adresu, kterou používá vaše aplikace Function App.

Teď jste připraveni spustit funkci a zkontrolovat aktuální odchozí IP adresy.

## <a name="verify-current-outbound-ips"></a>Ověřit aktuální odchozí IP adresy

Nyní můžete funkci spustit. Ale nejdřív se vraťte na portálu a podívejte se, jaké odchozí IP adresy používá aplikace Function App.  

1. Ve vaší aplikaci Function App vyberte **vlastnosti** a zkontrolujte pole **odchozí IP adresy** .

    ![Zobrazit odchozí IP adresy aplikace Function App](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. Teď se vraťte k funkci triggeru HTTP, vyberte **kód + test** a potom **test/běh**.

    ![Test – funkce](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. Výběrem **příkazu Spustit** spusťte funkci a pak přepněte do **výstupu**. 

    ![Výstup testovací funkce](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. Ověřte, že IP adresa v těle odpovědi HTTP je jednou z hodnot odchozích IP adres, které jste si zobrazili dříve.

Nyní můžete vytvořit veřejnou IP adresu a použít bránu NAT k úpravě této odchozí IP adresy.

## <a name="create-public-ip"></a>Vytvoření veřejné IP adresy

1. Ve vaší skupině prostředků vyberte **Přidat**, vyhledejte Azure Marketplace pro **veřejnou IP adresu** a vyberte **vytvořit**. Použijte nastavení v tabulce pod obrázkem:

    ![Vytvoření veřejné IP adresy](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | Nastavení      | Navrhovaná hodnota  |
    | ------------ | ---------------- |
    | **Verze protokolu IP** | IPv4 |
    | **SKU** | Standard |
    | **Úroveň** | Regionální |
    | **Název** | Odchozí IP adresa |
    | **Předplatné** | Ujistěte se, že se vaše předplatné zobrazuje. | 
    | **Skupina prostředků** | myResourceGroup (nebo název, který jste přiřadili ke svojí skupině prostředků) |
    | **Umístění** | Východní USA (nebo umístění, které jste přiřadili k ostatním prostředkům) |
    | **Zóna dostupnosti** | Žádná zóna |

1. Vyberte **vytvořit** a odešlete nasazení.

1. Až se nasazení dokončí, přejděte k nově vytvořenému prostředku veřejné IP adresy a Prohlédněte si IP adresu v **přehledu**.

    ![Zobrazit veřejnou IP adresu](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>Vytvoření brány NAT

Teď vytvoříme bránu NAT. Když začnete s [předchozím kurzem k virtuálním sítím](functions-create-vnet.md), `Function-Net` byl navržený název podsítě a `MyResourceGroup-vnet` byl navržený název virtuální sítě v tomto kurzu.

1. Z vaší skupiny prostředků vyberte **Přidat**, vyhledejte Azure Marketplace pro **bránu NAT** a vyberte **vytvořit**. K naplnění karty **základy** použijte nastavení v tabulce pod obrázkem:

    ![Vytvoření brány NAT](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | Nastavení      | Navrhovaná hodnota  |
    | ------------ | ---------------- |  
    | **Předplatné** | Vaše předplatné | 
    | **Skupina prostředků** | myResourceGroup (nebo název, který jste přiřadili ke svojí skupině prostředků) |
    | **Název brány NAT** | myNatGateway |
    | **Oblast** | Východní USA (nebo umístění, které jste přiřadili k ostatním prostředkům) |
    | **Zóna dostupnosti** | Žádné |

1. Vyberte **Další: odchozí IP adresa**. V poli **veřejné IP adresy** vyberte dříve vytvořenou veřejnou IP adresu. Nechte nevybrané **předpony veřejných IP adres** .

1. Vyberte **Další: podsíť**. Vyberte prostředek *myResourceGroup-VNet* v poli **virtuální síť** a *funkce-NET* Subnet.

    ![Vybrat podsíť](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. Pokud chcete nasazení odeslat, vyberte **zkontrolovat + vytvořit** a **vytvořit** .

Jakmile se nasazení dokončí, bude brána NAT připravena směrovat provoz z podsítě aplikace Function App na Internet.

## <a name="update-function-configuration"></a>Aktualizovat konfiguraci funkce

Nyní je nutné přidat nastavení aplikace `WEBSITE_VNET_ROUTE_ALL` nastavené na hodnotu `1` .  Toto nastavení vynutí odchozí přenosy přes virtuální síť a přidruženou bránu NAT. Bez tohoto nastavení se internetový provoz nesměruje přes integrovanou virtuální síť a budou se vám zobrazovat stejné odchozí IP adresy. 

1. V Azure Portal přejděte do aplikace Function App a v nabídce na levé straně vyberte **Konfigurace** .

1. V části **nastavení aplikace** vyberte **+ Nastavení nové aplikace** a dokončit pomocí následujících hodnot vyplňte pole:

    |Název pole  |Hodnota |
    |---|---|
    |**Název**    |WEBSITE_VNET_ROUTE_ALL|
    |**Hodnota**   |1|

1. Kliknutím na **tlačítko OK** zavřete dialogové okno nové nastavení aplikace.

1. Vyberte **Uložit** a pak **pokračujte** v ukládání nastavení.

Aplikace Function App je teď nakonfigurovaná tak, aby směrovala přenosy přes přidruženou virtuální síť.

## <a name="verify-new-outbound-ips"></a>Ověřit nové odchozí IP adresy

Opakujte [postup dříve](#verify-current-outbound-ips) a spusťte funkci znovu. Nyní by se měla zobrazit odchozí IP adresa, kterou jste nakonfigurovali v překladu adres (NAT) zobrazeném ve výstupu funkce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vytvořili jste prostředky pro dokončení tohoto kurzu. Budou se vám účtovat tyto prostředky v závislosti na [stavu vašeho účtu](https://azure.microsoft.com/account/) a [cenách služeb](https://azure.microsoft.com/pricing/). Abyste se vyhnuli mimořádným nákladům, odstraňte je, když je víte, že je už nepotřebujete. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Možnosti sítí Azure Functions](functions-networking-options.md)
