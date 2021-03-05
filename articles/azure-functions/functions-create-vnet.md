---
title: Použití privátních koncových bodů pro integraci Azure Functions s virtuální sítí
description: Podrobný kurz, ve kterém se dozvíte, jak připojit funkci ke službě Azure Virtual Network a jak ji uzamknout pomocí privátních koncových bodů
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: a7bad58167009b4089724165813eb061996f1e6b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200202"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-using-private-endpoints"></a>Kurz: Integrace Azure Functions s virtuální sítí Azure pomocí privátních koncových bodů

V tomto kurzu se dozvíte, jak pomocí Azure Functions připojit k prostředkům ve službě Azure Virtual Network pomocí privátních koncových bodů. Vytvoříte funkci s účtem úložiště uzamčeným za virtuální sítí, která používá aktivační událost fronty služby Service Bus.

> [!div class="checklist"]
> * Vytvoření aplikace Function App v plánu Premium
> * Vytvoření prostředků Azure (Service Bus, účet úložiště, Virtual Network)
> * Uzamknout účet úložiště za soukromým koncovým bodem
> * Uzamknout Service Bus za soukromým koncovým bodem
> * Nasaďte aplikaci funkcí s aktivačními událostmi Service Bus i HTTP.
> * Uzamčení aplikace Function App za soukromým koncovým bodem
> * Otestujte, abyste viděli, že vaše aplikace Function App je zabezpečená za virtuální sítí.
> * Vyčištění prostředků

## <a name="create-a-function-app-in-a-premium-plan"></a>Vytvoření aplikace Function App v plánu Premium

Nejdřív vytvoříte aplikaci funkcí .NET v [plánu Premium] , protože tento kurz bude používat C#. V systému Windows jsou podporovány také jiné jazyky. Tento plán poskytuje škálování bez serveru při podpoře integrace virtuální sítě.

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

1. Na stránce **Nový** vyberte **COMPUTE**  >  **Function App**.

1. Na stránce **základy** použijte nastavení aplikace Function App, jak je uvedeno v následující tabulce:

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace funkcí vytvořena. |
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **Název aplikace funkcí** | Globálně jedinečný název | Název identifikující novou aplikaci funkcí. Platné znaky jsou `a-z` (bez rozlišování malých a velkých písmen), `0-9` a `-`.  |
    |**Publikovat**| Kód | Možnost publikování souborů kódu nebo kontejneru Docker |
    | **Zásobník modulu runtime** | .NET | Tento kurz používá .NET |
    |**Oblast**| Upřednostňovaná oblast | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž nebo poblíž dalších služeb, ke kterým máte přístup. |

1. Vyberte **Další: hostování**. Na stránce **hostování** zadejte následující nastavení:

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Účet úložiště](../storage/common/storage-account-create.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít i existující účet, který musí splňovat [požadavky na účet úložiště](./storage-considerations.md#storage-account-requirements). |
    |**Operační systém**| Windows | V tomto kurzu se používá Windows. |
    | **[Plánování](./functions-scale.md)** | Premium | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Vyberte **Premium**. Ve výchozím nastavení se vytvoří nový plán App Service. Výchozí **SKU a velikost** jsou **EP1**, kde EP představuje _elastickou prémii_. Další informace najdete v [seznamu SKU úrovně Premium](./functions-premium-plan.md#available-instance-skus).<br/>Při spouštění funkcí JavaScriptu na plánu Premium byste měli zvolit instanci, která má méně vCPU. Další informace najdete v tématu [Výběr plánů Premium s jedním jádrem](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Vyberte **Další: monitorování**. Na stránce **monitorování** zadejte následující nastavení:

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Výchozí | Vytvoří Application Insights prostředek stejného *názvu aplikace* v nejbližší podporované oblasti. Rozbalením tohoto nastavení můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v [geografickém geografickém prostředí Azure](https://azure.microsoft.com/global-infrastructure/geographies/) pro ukládání vašich dat. |

1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte výběry konfigurace aplikace.

1. Na stránce **Revize + vytvořit** zkontrolujte nastavení a pak vyberte **vytvořit** a nasaďte a nasaďte aplikaci Function App.

1. Vyberte ikonu **oznámení** v pravém horním rohu portálu a sledujte zprávu o **úspěšném nasazení** .

1. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte. Můžete také vybrat **Připnout na řídicí panel**. Připnutí usnadňuje návrat k tomuto prostředku Function App z vašeho řídicího panelu.

1. Gratulujeme! Úspěšně jste vytvořili prémiovou aplikaci Function App.

## <a name="create-azure-resources"></a>Vytvoření prostředků Azure

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Pro virtuální sítě se vyžaduje samostatný účet úložiště, který se vytvořil při počátečním vytváření vaší aplikace Function App.

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

1. Na nové stránce vyhledejte **účet úložiště** a vyberte **vytvořit** .

1. Na kartě **základy** nastavte nastavení, jak je uvedeno v následující tabulce. Zbývající hodnota může být ponechána jako výchozí:

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Vyberte skupinu prostředků, kterou jste vytvořili pomocí aplikace Function App. |
    | **Název** | mysecurestorage| Název účtu úložiště, na který se použije privátní koncový bod |
    | **[Oblast](https://azure.microsoft.com/regions/)** | myFunctionRegion | Vyberte oblast, ve které jste aplikaci Function App vytvořili. |

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**.

### <a name="create-a-service-bus"></a>Vytvoření Service Bus

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

1. Na nové stránce vyhledejte **Service Bus** a vyberte **vytvořit**.

1. Na kartě **základy** nastavte nastavení, jak je uvedeno v následující tabulce. Zbývající hodnota může být ponechána jako výchozí:

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. |
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Vyberte skupinu prostředků, kterou jste vytvořili pomocí aplikace Function App. |
    | **Název** | myServiceBus| Název vaší služby Service Bus, pro kterou bude privátní koncový bod použit. |
    | **[Oblast](https://azure.microsoft.com/regions/)** | myFunctionRegion | Vyberte oblast, ve které jste aplikaci Function App vytvořili. |
    | **Cenová úroveň** | Premium | Vyberte tuto vrstvu pro použití privátních koncových bodů s Service Bus. |

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Prostředky Azure v tomto kurzu buď Integrujte s nebo jsou umístěné v rámci virtuální sítě. Pomocí privátních koncových bodů zachováte síťový provoz, který je obsažený ve virtuální síti.

V tomto kurzu se vytvoří dvě podsítě:
- **výchozí**: podsíť pro privátní koncové body. Privátní IP adresy jsou předány z této podsítě.
- **funkce**: podsíť pro integraci Azure Functions virtuální sítě. Tato podsíť je delegovaná do aplikace Function App.

Teď vytvořte virtuální síť, do které se aplikace Function App integruje.

1. V nabídce webu Azure Portal nebo na domovské stránce vyberte **Vytvořit prostředek**.

1. Na nové stránce vyhledejte **Virtual Network** a vyberte **vytvořit**.

1. Na kartě **základy** použijte nastavení virtuální sítě, jak je uvedeno níže:

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Vyberte skupinu prostředků, kterou jste vytvořili pomocí aplikace Function App. |
    | **Název** | myVirtualNet| Název virtuální sítě, ke které se vaše aplikace Function App připojí |
    | **[Oblast](https://azure.microsoft.com/regions/)** | myFunctionRegion | Vyberte oblast, ve které jste aplikaci Function App vytvořili. |

1. Na kartě **IP adresy** vyberte **Přidat podsíť**. Použijte nastavení uvedená níže při přidávání podsítě:

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Snímek obrazovky s zobrazením konfigurace pro vytvoření virtuální sítě":::

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Název podsítě** |  – funkce | Název podsítě, ke které se aplikace Function App připojí | 
    | **Rozsah adres podsítě** | 10.0.1.0/24 | Všimněte si, že náš adresní prostor IPv4 v imagi výše je 10.0.0.0/16. Pokud byl výše uvedený 10.1.0.0/16, doporučuje se *Rozsah adres Doporučené podsítě* 10.1.1.0/24. |

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**.

## <a name="lock-down-your-storage-account-with-private-endpoints"></a>Uzamknutí účtu úložiště pomocí privátních koncových bodů

Soukromé koncové body Azure slouží k připojení ke konkrétním prostředkům Azure pomocí privátní IP adresy. Toto připojení zajišťuje, že síťový provoz zůstane v rámci vybrané virtuální sítě a že přístup je k dispozici pouze pro konkrétní prostředky. Teď vytvořte privátní koncové body pro Azure File Storage a Azure Blob Storage pomocí svého účtu úložiště.

1. V novém účtu úložiště vyberte v nabídce vlevo možnost **sítě** .

1. Vyberte kartu **připojení privátního koncového bodu** a vyberte **privátní koncový bod**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Snímek obrazovky s postupem, jak přejít k vytvoření privátních koncových bodů pro účet úložiště.":::

1. Na kartě **základy** použijte nastavení privátního koncového bodu, jak je uvedeno níže:

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Vyberte skupinu prostředků, kterou jste vytvořili pomocí aplikace Function App. | |
    | **Název** | koncový bod souboru | Název privátního koncového bodu pro soubory z vašeho účtu úložiště. |
    | **[Oblast](https://azure.microsoft.com/regions/)** | myFunctionRegion | Vyberte oblast, ve které jste vytvořili účet úložiště. |

1. Na kartě **prostředek** použijte nastavení privátního koncového bodu, jak je uvedeno níže:

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **Typ prostředku**  | Microsoft. Storage/storageAccounts | Toto je typ prostředku pro účty úložiště. |
    | **Prostředek** | mysecurestorage | Účet úložiště, který jste právě vytvořili |
    | **Cílový dílčí prostředek** |  – soubor | Tento privátní koncový bod se bude používat pro soubory z účtu úložiště. |

1. Na kartě **Konfigurace** vyberte možnost **výchozí** pro nastavení podsítě.

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**. Prostředky ve virtuální síti teď můžou komunikovat se soubory úložiště.

1. Vytvořte další privátní koncový bod pro objekty blob. Na kartě **prostředky** použijte následující nastavení. U všech ostatních nastavení použijte stejné nastavení z kroků pro vytvoření souboru privátního koncového bodu, které jste právě následovali.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **Typ prostředku**  | Microsoft. Storage/storageAccounts | Toto je typ prostředku pro účty úložiště. |
    | **Prostředek** | mysecurestorage | Účet úložiště, který jste právě vytvořili |
    | **Cílový dílčí prostředek** | blob | Tento privátní koncový bod se bude používat pro objekty BLOB z účtu úložiště. |

## <a name="lock-down-your-service-bus-with-a-private-endpoint"></a>Uzamčení služby Service Bus pomocí privátního koncového bodu

Nyní vytvořte privátní koncový bod pro váš Azure Service Bus.

1. V nové službě Service Bus vyberte v nabídce vlevo možnost **sítě** .

1. Vyberte kartu **připojení privátního koncového bodu** a vyberte **privátní koncový bod**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Snímek obrazovky s navigací k privátním koncovým bodům pro Service Bus":::

1. Na kartě **základy** použijte nastavení privátního koncového bodu, jak je uvedeno níže:

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Vyberte skupinu prostředků, kterou jste vytvořili pomocí aplikace Function App. |
    | **Název** | SB – koncový bod | Název privátního koncového bodu pro soubory z vašeho účtu úložiště. |
    | **[Oblast](https://azure.microsoft.com/regions/)** | myFunctionRegion | Vyberte oblast, ve které jste vytvořili účet úložiště. |

1. Na kartě **prostředek** použijte nastavení privátního koncového bodu, jak je uvedeno níže:

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **Typ prostředku**  | Microsoft. ServiceBus/obory názvů | Toto je typ prostředku pro Service Bus. |
    | **Prostředek** | myServiceBus | Service Bus, který jste vytvořili dříve v tomto kurzu. |
    | **Cílový podprostředek** | namespace | Tento privátní koncový bod se použije pro obor názvů ze služby Service Bus. |

1. Na kartě **Konfigurace** vyberte možnost **výchozí** pro nastavení podsítě.

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**. Prostředky ve virtuální síti teď můžou komunikovat se službou Service Bus.

## <a name="create-a-file-share"></a>Vytvoření sdílené složky

1. V účtu úložiště, který jste vytvořili, vyberte v nabídce vlevo možnost **sdílené složky** .

1. Vyberte **+ sdílení souborů**. Pro účely tohoto kurzu zadejte **soubory** jako název sdílené složky.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Snímek obrazovky s postupem, jak vytvořit sdílenou složku v účtu úložiště.":::

## <a name="get-storage-account-connection-string"></a>Získání připojovacího řetězce účtu úložiště

1. V účtu úložiště, který jste vytvořili, vyberte **přístupové klíče** v nabídce vlevo.

1. Vyberte **Zobrazit klíče**. Zkopírujte připojovací řetězec klíč1 a uložte ho. Tento připojovací řetězec budeme potřebovat později při konfiguraci nastavení aplikace.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Snímek obrazovky s postupem získání připojovacího řetězce účtu úložiště":::

## <a name="create-a-queue"></a>Vytvoření fronty

To bude fronta, ze které se aktivační událost Service Bus Azure Functions obdrží události.

1. V nabídce vlevo vyberte ve službě Service Bus možnost **fronty** .

1. Vyberte **zásady sdíleného přístupu**. Pro účely tohoto kurzu poskytněte **frontu** jako název fronty.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Snímek obrazovky s postupem, jak vytvořit frontu služby Service Bus.":::

## <a name="get-service-bus-connection-string"></a>Získat připojovací řetězec služby Service Bus

1. V nabídce vlevo vyberte ve službě Service Bus **zásady sdíleného přístupu** .

1. Vyberte **RootManageSharedAccessKey**. Zkopírujte **primární připojovací řetězec** a uložte ho. Tento připojovací řetězec budeme potřebovat později při konfiguraci nastavení aplikace.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Snímek obrazovky s postupem získání připojovacího řetězce služby Service Bus":::

## <a name="integrate-function-app-with-your-virtual-network"></a>Integrace aplikace Function App s vaší virtuální sítí

Pokud chcete používat aplikaci Function App s virtuálními sítěmi, budete ji muset připojit k podsíti. Pro integraci virtuální sítě Azure Functions používáme konkrétní podsíť a výchozí dílčí síť pro všechny ostatní privátní koncové body vytvořené v tomto kurzu.

1. V aplikaci Function App vyberte v nabídce vlevo možnost **sítě** .

1. Pokud **chcete konfigurovat** integraci virtuální sítě, vyberte kliknutím sem.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Snímek obrazovky s navigací k integraci virtuální sítě":::

1. Vyberte **Přidat virtuální síť** .

1. V okně, které se otevře v části **Virtual Network** vyberte virtuální síť, kterou jste vytvořili dříve.

1. Vyberte **podsíť** , kterou jsme vytvořili dříve s názvem **Functions**. Vaše aplikace Function App je teď integrovaná s vaší virtuální sítí!

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Snímek obrazovky s připojením aplikace funkcí k podsíti":::

## <a name="configure-your-function-app-settings-for-private-endpoints"></a>Konfigurace nastavení aplikace Function App pro privátní koncové body

1. V aplikaci Function App vyberte v nabídce vlevo možnost **Konfigurace** .

1. Chcete-li používat aplikaci Function App s virtuálními sítěmi, bude nutné aktualizovat následující nastavení aplikace. V tabulce nastavení aplikace podle potřeby vyberte **+ Nová nastavení aplikace** nebo tužka **úpravou** ve sloupci úplně vpravo. Až budete hotovi, vyberte **Uložit**.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Snímek obrazovky s postupem konfigurace nastavení aplikace Function App pro soukromé koncové body.":::

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | Připojovací řetězec účtu úložiště, který jste vytvořili. Toto je připojovací řetězec úložiště z [získat připojovací řetězec účtu úložiště](#get-storage-account-connection-string). Změnou tohoto nastavení teď bude aplikace Function App používat pro normální operace za běhu účet zabezpečeného úložiště. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | Připojovací řetězec účtu úložiště, který jste vytvořili. Když změníte toto nastavení, aplikace Function app teď použije účet zabezpečeného úložiště pro soubory Azure, které se použijí při nasazení. |
    | **WEBSITE_CONTENTSHARE** | files | Název sdílené složky, kterou jste vytvořili v účtu úložiště. Toto nastavení aplikace se používá ve spojení s WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Vytvořte nastavení aplikace pro připojovací řetězec vaší služby Service Bus. Toto je připojovací řetězec úložiště z [Get připojovacího řetězce služby Service Bus](#get-service-bus-connection-string).|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Vytvořit toto nastavení aplikace Hodnota 1 umožňuje škálování aplikace Function App, když máte účet úložiště omezený na virtuální síť. Toto nastavení byste měli povolit při omezení účtu úložiště na virtuální síť. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Vytvořit toto nastavení aplikace Jakmile se vaše aplikace integruje s virtuální sítí, bude používat stejný server DNS jako virtuální síť. To je jedno ze dvou potřebných nastavení. aplikace Function App funguje s Azure DNS privátními zónami a vyžaduje se při použití privátních koncových bodů. Tato nastavení budou posílat všechna odchozí volání z vaší aplikace do vaší virtuální sítě. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Vytvořit toto nastavení aplikace Jakmile se vaše aplikace integruje s virtuální sítí, bude používat stejný server DNS jako virtuální síť. To je jedno ze dvou potřebných nastavení. aplikace Function App funguje s Azure DNS privátními zónami a vyžaduje se při použití privátních koncových bodů. Tato nastavení budou posílat všechna odchozí volání z vaší aplikace do vaší virtuální sítě. |

1. V zobrazení **Konfigurace** vyberte kartu **nastavení běhu funkce** .

1. Nastavte **monitorování škálování za běhu** na **zapnuto** a vyberte **Uložit**. Škálování řízené za běhu umožňuje připojit funkce triggeru jiného typu než HTTP ke službám běžícím ve vaší virtuální síti.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Snímek obrazovky s postupem povolení škálování řízeného modulem runtime pro Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger-to-your-function-app"></a>Nasazení triggeru služby Service Bus a triggeru http do aplikace Function App

1. V GitHubu přejděte k následujícímu ukázkovému úložišti, které obsahuje aplikaci Function App se dvěma funkcemi, triggerem protokolu HTTP a triggerem Service Bus fronty.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. V horní části stránky vyberte tlačítko **rozvětvení** , aby se vytvořilo rozvětvení tohoto úložiště ve vlastním účtu GitHubu nebo organizaci.

1. V aplikaci Function App vyberte v nabídce vlevo možnost **centrum nasazení** . Pak vyberte **Nastavení**.

1. Na kartě **Nastavení** použijte nastavení nasazení, jak je uvedeno níže:

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Zdroj** | GitHubu | V kroku 2 byste měli mít vytvořené úložiště GitHub s ukázkovým kódem. | 
    | **Organizace**  | myOrganization | Jedná se o organizaci, kterou vaše úložiště kontroluje, obvykle váš účet. |
    | **Úložiště** | myRepo | Úložiště, které jste vytvořili pomocí ukázkového kódu. |
    | **Větev** | main | Toto je úložiště, které jste právě vytvořili, takže použijte hlavní větev. |
    | **Zásobník modulu runtime** | .NET | Vzorový kód je v jazyce C#. |

1. Vyberte **Uložit**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Snímek obrazovky s postupem nasazení Azure Functionsho kódu prostřednictvím portálu":::

1. Vaše počáteční nasazení může trvat několik minut. Po úspěšném nasazení aplikace se na kartě **protokoly** zobrazí stavová zpráva o **úspěchu (aktivní)** . V případě potřeby aktualizujte stránku. 

1. Gratulujeme! Úspěšně jste nasadili ukázkovou aplikaci Function App.

## <a name="lock-down-your-function-app-with-a-private-endpoint"></a>Uzamknutí aplikace Function App pomocí privátního koncového bodu

Nyní vytvořte privátní koncový bod pro aplikaci Function App. Tento soukromý koncový bod připojí vaši aplikaci funkcí soukromě a bezpečně k vaší virtuální síti pomocí privátní IP adresy. Další informace o privátních koncových bodech najdete v [dokumentaci k soukromým koncovým bodům](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

1. V aplikaci Function App vyberte v nabídce vlevo možnost **sítě** .

1. Vyberte **kliknutím sem proveďte konfiguraci** v části připojení privátního koncového bodu.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Snímek obrazovky s navigací k Function App soukromý koncový bod":::

1. Vyberte **Přidat**.

1. V nabídce, která se otevře, použijte nastavení privátního koncového bodu, jak je uvedeno níže:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Snímek obrazovky s postupem vytvoření Function App privátního koncového bodu.":::

1. Pro přidání privátního koncového bodu vyberte **OK** . Gratulujeme! Úspěšně jste provedli zabezpečení vaší aplikace Function App, Service Bus a účtu úložiště s privátními koncovými body!

### <a name="test-your-locked-down-function-app"></a>Test uzamčené aplikace Function App

1. V aplikaci Function App vyberte v nabídce vlevo možnost **funkce** .

1. Vyberte **ServiceBusQueueTrigger**.

1. V nabídce vlevo vyberte **monitor**. uvidíte, že nemůžete monitorovat svoji aplikaci. Důvodem je to, že váš prohlížeč nemá přístup k virtuální síti, takže nemůže přistupovat přímo k prostředkům v rámci virtuální sítě. Nyní si ukážeme další metodu, pomocí které můžete nadále monitorovat funkci, Application Insights.

1. Ve vaší aplikaci Function App vyberte v levé nabídce **Application Insights** a vyberte **Zobrazit Application Insights data**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Snímek obrazovky s informacemi o tom, jak zobrazit Application Insights pro Function App.":::

1. V nabídce vlevo vyberte **živá metriky** .

1. Otevřete novou kartu. V Service Bus v nabídce vlevo vyberte **fronty** .

1. Vyberte svou frontu.

1. V nabídce vlevo vyberte **Service Bus Průzkumník** . V části **Odeslat** vyberte jako **typ obsahu** **Text/prostý** a zadejte zprávu. 

1. Vyberte **Odeslat** a odešlete zprávu.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Snímek obrazovky s postupem odesílání zpráv Service Bus pomocí portálu":::

1. Na kartě s otevřenými **živými metrikami** byste měli vidět, že se aktivovala aktivační událost fronty Service Bus. Pokud tomu tak není, odešlete zprávu znovu z **průzkumníka Service Bus** .

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Snímek obrazovky s postupem, jak zobrazit zprávy pomocí živých metrik pro aplikace Function App.":::

1. Gratulujeme! Úspěšně jste otestovali nastavení aplikace Function App se soukromými koncovými body!

### <a name="private-dns-zones"></a>Privátní DNS zóny
Použití privátního koncového bodu pro připojení k prostředkům Azure znamená připojení k privátní IP adrese namísto veřejného koncového bodu. Stávající služby Azure jsou nakonfigurovány tak, aby používaly existující službu DNS pro připojení k veřejnému koncovému bodu. Aby bylo možné se připojit k privátnímu koncovému bodu, bude nutné přepsat konfiguraci DNS.

Pro každý prostředek Azure nakonfigurovaný s privátním koncovým bodem se vytvořila privátní zóna DNS. Pro každou soukromou IP adresu přidruženou k privátnímu koncovému bodu se vytvoří záznam DNS A.

V tomto kurzu byly vytvořeny následující zóny DNS:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci funkcí Premium, účet úložiště a Service Bus a ty jste zabezpečili za soukromé koncové body. Přečtěte si další informace o různých funkcích sítě, které jsou k dispozici níže:

> [!div class="nextstepaction"]
> [Další informace o možnostech sítě ve funkcích](./functions-networking-options.md)

[Plán Premium]: functions-premium-plan.md
