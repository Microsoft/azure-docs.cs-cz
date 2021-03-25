---
title: Použití privátních koncových bodů pro integraci Azure Functions s virtuální sítí
description: V tomto kurzu se dozvíte, jak připojit funkci ke službě Azure Virtual Network a jak ji zamknout pomocí privátních koncových bodů.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: e8ca853908b366b99e150f04ced404f42acc7d21
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027410"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>Kurz: Integrace Azure Functions s virtuální sítí Azure pomocí privátních koncových bodů

V tomto kurzu se dozvíte, jak pomocí Azure Functions připojit k prostředkům ve službě Azure Virtual Network pomocí privátních koncových bodů. Funkci vytvoříte pomocí účtu úložiště, který je uzamčen za virtuální sítí. Virtuální síť používá aktivační událost fronty služby Service Bus.

V tomto kurzu:

> [!div class="checklist"]
> * Vytvořte aplikaci Function App v plánu Premium.
> * Vytvořte prostředky Azure, jako je služba Service Bus, účet úložiště a virtuální síť.
> * Uzamkněte účet úložiště za soukromým koncovým bodem.
> * Zablokujte službu Service Bus za soukromým koncovým bodem.
> * Nasaďte aplikaci Function App, která používá aktivační události služby Service Bus a HTTP.
> * Zablokovat aplikaci Function App za soukromým koncovým bodem.
> * Otestujte, jestli je vaše aplikace funkcí v rámci virtuální sítě zabezpečená.
> * Vyčistěte prostředky.

## <a name="create-a-function-app-in-a-premium-plan"></a>Vytvoření aplikace Function App v plánu Premium

V plánu Premium vytvoříte aplikaci funkcí .NET, protože tento kurz používá C#. V systému Windows jsou podporovány také jiné jazyky. Plán Premium poskytuje škálování bez serveru při podpoře integrace virtuální sítě.

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**.

1. Na stránce **Nový** vyberte **COMPUTE**  >  **Function App**.

1. Na stránce **základy** použijte následující tabulku ke konfiguraci nastavení aplikace Function App.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. |
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které vytvoříte aplikaci Function App. |
    | **Název aplikace funkcí** | Globálně jedinečný název | Název identifikující novou aplikaci funkcí. Platné znaky jsou `a-z` (bez rozlišování malých a velkých písmen), `0-9` a `-`.  |
    |**Publikovat**| Kód | Vyberte možnost publikování souborů kódu nebo kontejneru Docker. |
    | **Zásobník modulu runtime** | .NET | V tomto kurzu se používá .NET. |
    |**Oblast**| Upřednostňovaná oblast | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž nebo poblíž dalších služeb, ke kterým mají přístup vaše funkce. |

1. Vyberte **Další: hostování**. Na stránce **hostování** zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Účet úložiště](../storage/common/storage-account-create.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků. Můžou obsahovat jenom číslice a malá písmena. Můžete použít i existující účet, který musí splňovat [požadavky na účet úložiště](./storage-considerations.md#storage-account-requirements). |
    |**Operační systém**| Windows | V tomto kurzu se používá Windows. |
    | **[Plánování](./functions-scale.md)** | Premium | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Ve výchozím nastavení se při výběru úrovně **Premium** vytvoří nový plán App Service. Výchozí **SKU a velikost** jsou **EP1**, kde *EP* představuje _elastickou prémii_. Další informace najdete v seznamu [SKU úrovně Premium](./functions-premium-plan.md#available-instance-skus).<br/><br/>Když spustíte funkce JavaScriptu na plánu Premium, vyberte instanci, která má méně vCPU. Další informace najdete v tématu [Výběr plánů Premium s jedním jádrem](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Vyberte **Další: monitorování**. Na stránce **monitorování** zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Výchozí | Vytvoří Application Insights prostředek se stejným názvem aplikace v nejbližší podporované oblasti. Toto nastavení rozbalte, pokud potřebujete změnit **název nového prostředku** nebo ukládat data na jiné **místo** v [geografickém prostředí Azure](https://azure.microsoft.com/global-infrastructure/geographies/). |

1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte výběry konfigurace aplikace.

1. Na stránce **Revize + vytvořit** zkontrolujte nastavení. Pak vyberte **vytvořit** k zřízení a nasazení aplikace Function App.

1. V pravém horním rohu portálu vyberte ikonu **oznámení** a sledujte zprávu o **úspěšném nasazení** .

1. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte. Můžete také vybrat **Připnout na řídicí panel**. Připnutí usnadňuje návrat k tomuto prostředku Function App z vašeho řídicího panelu.

Gratulujeme! Úspěšně jste vytvořili aplikaci funkcí Premium.

## <a name="create-azure-resources"></a>Vytvoření prostředků Azure

V dalším kroku vytvoříte účet úložiště, Service Bus a virtuální síť. 
### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Vaše virtuální sítě budou potřebovat účet úložiště, který je oddělený od účtu, který jste vytvořili pomocí aplikace Function App.

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**.

1. Na **nové** stránce vyhledejte *účet úložiště*. Potom vyberte **Vytvořit**.

1. Na kartě **základy** použijte následující tabulku ke konfiguraci nastavení účtu úložiště. Všechna ostatní nastavení můžou používat výchozí hodnoty.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Skupina prostředků, kterou jste vytvořili pomocí aplikace Function App. |
    | **Název** | mysecurestorage| Název účtu úložiště, na který se použije privátní koncový bod. |
    | **[Věřitel](https://azure.microsoft.com/regions/)** | myFunctionRegion | Oblast, ve které jste vytvořili aplikaci Function App. |

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**.

### <a name="create-a-service-bus"></a>Vytvoření služby Service Bus

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**.

1. Na **nové** stránce vyhledejte *Service Bus*. Potom vyberte **Vytvořit**.

1. Na kartě **základy** nakonfigurujte nastavení služby Service Bus pomocí následující tabulky. Všechna ostatní nastavení můžou používat výchozí hodnoty.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. |
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Skupina prostředků, kterou jste vytvořili pomocí aplikace Function App. |
    | **Název** | myServiceBus| Název služby Service Bus, pro kterou bude privátní koncový bod použit. |
    | **[Věřitel](https://azure.microsoft.com/regions/)** | myFunctionRegion | Oblast, ve které jste vytvořili aplikaci Function App. |
    | **Cenová úroveň** | Premium | Vyberte tuto vrstvu pro použití privátních koncových bodů s Azure Service Bus. |

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Prostředky Azure v tomto kurzu buď Integrujte s nebo jsou umístěné ve virtuální síti. Soukromé koncové body použijete k zahrnutí síťového provozu v rámci virtuální sítě.

V tomto kurzu se vytvoří dvě podsítě:
- **výchozí**: podsíť pro privátní koncové body. Privátní IP adresy jsou předány z této podsítě.
- **funkce**: podsíť pro integraci Azure Functions virtuální sítě. Tato podsíť je delegovaná do aplikace Function App.

Vytvořte virtuální síť, do které se aplikace Function App integruje:

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**.

1. Na **nové** stránce vyhledejte *virtuální síť*. Potom vyberte **Vytvořit**.

1. Na kartě **základy** použijte následující tabulku ke konfiguraci nastavení virtuální sítě.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Skupina prostředků, kterou jste vytvořili pomocí aplikace Function App. |
    | **Název** | myVirtualNet| Název virtuální sítě, ke které se aplikace Function App připojí |
    | **[Věřitel](https://azure.microsoft.com/regions/)** | myFunctionRegion | Oblast, ve které jste vytvořili aplikaci Function App. |

1. Na kartě **IP adresy** vyberte **Přidat podsíť**. Pomocí následující tabulky nakonfigurujte nastavení podsítě.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Snímek obrazovky s zobrazením konfigurace pro vytvoření virtuální sítě":::

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Název podsítě** |  – funkce | Název podsítě, ke které se aplikace Function App připojí | 
    | **Rozsah adres podsítě** | 10.0.1.0/24 | Rozsah adres podsítě. Na předchozím obrázku si všimněte, že adresní prostor IPv4 je 10.0.0.0/16. Pokud byla hodnota 10.1.0.0/16, doporučuje se rozsah adres Doporučené podsítě 10.1.1.0/24. |

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**.

## <a name="lock-down-your-storage-account"></a>Uzamknout účet úložiště

Soukromé koncové body Azure slouží k připojení ke konkrétním prostředkům Azure pomocí privátní IP adresy. Toto připojení zajistí, že síťový provoz zůstane v rámci vybrané virtuální sítě a přístup je dostupný jenom pro konkrétní prostředky. 

Vytvoření privátních koncových bodů pro úložiště Azure Files a Azure Blob Storage pomocí svého účtu úložiště:

1. V nabídce vlevo vyberte v novém účtu úložiště možnost **sítě**.

1. Na kartě **připojení privátního koncového bodu** vyberte **privátní koncový bod**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Snímek obrazovky s postupem pro vytvoření privátních koncových bodů pro účet úložiště":::

1. Na kartě **základy** použijte nastavení privátního koncového bodu, které je uvedené v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Vyberte skupinu prostředků, kterou jste vytvořili pomocí aplikace Function App. | |
    | **Název** | koncový bod souboru | Název privátního koncového bodu pro soubory z vašeho účtu úložiště. |
    | **[Věřitel](https://azure.microsoft.com/regions/)** | myFunctionRegion | Vyberte oblast, ve které jste vytvořili účet úložiště. |

1. Na kartě **prostředek** použijte nastavení privátního koncového bodu, které je uvedené v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **Typ prostředku**  | Microsoft. Storage/storageAccounts | Typ prostředku pro účty úložiště. |
    | **Prostředek** | mysecurestorage | Účet úložiště, který jste vytvořili. |
    | **Cílový dílčí prostředek** |  – soubor | Soukromý koncový bod, který se bude používat pro soubory z účtu úložiště. |

1. Na kartě **Konfigurace** pro nastavení **podsítě** vyberte **výchozí**.

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**. Prostředky ve virtuální síti teď můžou komunikovat se soubory úložiště.

1. Vytvořte další privátní koncový bod pro objekty blob. Na kartě **prostředky** použijte nastavení uvedená v následující tabulce. Pro všechna ostatní nastavení použijte stejné hodnoty, jako jste použili k vytvoření privátního koncového bodu pro soubory.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **Typ prostředku**  | Microsoft. Storage/storageAccounts | Typ prostředku pro účty úložiště. |
    | **Prostředek** | mysecurestorage | Účet úložiště, který jste vytvořili. |
    | **Cílový dílčí prostředek** | blob | Privátní koncový bod, který se bude používat pro objekty BLOB z účtu úložiště. |

## <a name="lock-down-your-service-bus"></a>Uzamčení služby Service Bus

Vytvořte privátní koncový bod pro uzamknutí služby Service Bus:

1. V nabídce na levé straně nové služby Service Bus vyberte **sítě**.

1. Na kartě **připojení privátního koncového bodu** vyberte **privátní koncový bod**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Snímek obrazovky s přechodem k privátním koncovým bodům pro Service Bus":::

1. Na kartě **základy** použijte nastavení privátního koncového bodu, které je uvedené v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Skupina prostředků, kterou jste vytvořili pomocí aplikace Function App. |
    | **Název** | SB – koncový bod | Název privátního koncového bodu pro soubory z vašeho účtu úložiště. |
    | **[Věřitel](https://azure.microsoft.com/regions/)** | myFunctionRegion | Oblast, ve které jste vytvořili účet úložiště. |

1. Na kartě **prostředek** použijte nastavení privátního koncového bodu, které je uvedené v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **Typ prostředku**  | Microsoft. ServiceBus/obory názvů | Typ prostředku pro Service Bus. |
    | **Prostředek** | myServiceBus | Služba Service Bus, kterou jste vytvořili dříve v tomto kurzu. |
    | **Cílový podprostředek** | namespace | Privátní koncový bod, který se použije pro obor názvů ze služby Service Bus. |

1. Na kartě **Konfigurace** pro nastavení **podsítě** vyberte **výchozí**.

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**. 

Prostředky ve virtuální síti teď můžou komunikovat se službou Service Bus.

## <a name="create-a-file-share"></a>Vytvoření sdílené složky

1. V nabídce na levé straně účtu úložiště, který jste vytvořili, vyberte **sdílení souborů**.

1. Vyberte **+ sdílení souborů**. Pro účely tohoto kurzu pojmenujte *soubory* sdílení souborů.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Snímek obrazovky s postupem, jak vytvořit sdílenou složku v účtu úložiště.":::

1. Vyberte **Vytvořit**.

## <a name="get-the-storage-account-connection-string"></a>Získání připojovacího řetězce účtu úložiště

1. V nabídce na levé straně účtu úložiště, který jste vytvořili, vyberte **přístupové klíče**.

1. Vyberte **Zobrazit klíče**. Zkopírujte a uložte připojovací řetězec **klíč1**. Tento připojovací řetězec budete potřebovat při konfiguraci nastavení aplikace.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Snímek obrazovky s postupem získání připojovacího řetězce účtu úložiště":::

## <a name="create-a-queue"></a>Vytvoření fronty

Vytvořte frontu, ve které bude aktivační událost služby Azure Functions Service Bus získávat události:

1. Ve službě Service Bus v nabídce na levé straně vyberte **fronty**.

1. Vyberte **zásady sdíleného přístupu**. Pro účely tohoto kurzu pojmenujte *frontu* seznamu.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Snímek obrazovky s postupem, jak vytvořit frontu služby Service Bus.":::

1. Vyberte **Vytvořit**.

## <a name="get-a-service-bus-connection-string"></a>Získání připojovacího řetězce služby Service Bus

1. V nabídce vlevo vyberte ve službě Service Bus **zásady sdíleného přístupu**.

1. Vyberte **RootManageSharedAccessKey**. Zkopírujte a uložte **primární připojovací řetězec**. Tento připojovací řetězec budete potřebovat při konfiguraci nastavení aplikace.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Snímek obrazovky s postupem získání připojovacího řetězce služby Service Bus":::

## <a name="integrate-the-function-app"></a>Integrace aplikace Function App

Pokud chcete používat aplikaci Function App s virtuálními sítěmi, musíte ji připojit k podsíti. Pro integraci virtuální sítě Azure Functions použijete určitou podsíť. Pro jiné privátní koncové body, které vytvoříte v tomto kurzu, použijete výchozí podsíť.

1. V nabídce na levé straně aplikace Function App vyberte **síť**.

1. V části **Integrace virtuální** sítě vyberte **Konfigurovat kliknutím sem**.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Snímek obrazovky s postupem, jak přejít k integraci virtuální sítě":::

1. Vyberte **Přidat virtuální síť**.

1. V části **Virtual Network** vyberte virtuální síť, kterou jste vytvořili dříve.

1. Vyberte podsíť **Functions** , kterou jste vytvořili dříve. Vaše aplikace Function App je teď integrovaná s vaší virtuální sítí!

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Snímek obrazovky s připojením aplikace funkcí k podsíti":::

## <a name="configure-your-function-app-settings"></a>Konfigurace nastavení aplikace Function App

1. V nabídce na levé straně aplikace Function App vyberte **Konfigurace**.

1. Pokud chcete používat aplikaci Function App s virtuálními sítěmi, aktualizujte nastavení aplikace uvedené v následující tabulce. Pokud chcete přidat nebo upravit nastavení, vyberte **+ Nastavení nové aplikace** nebo ikonu **Upravit** ve sloupci vpravo v tabulce nastavení aplikace. Po dokončení vyberte **Uložit**.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Snímek obrazovky s postupem konfigurace nastavení aplikace Function App pro soukromé koncové body.":::

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | Připojovací řetězec účtu úložiště, který jste vytvořili. Tento připojovací řetězec úložiště je z oddílu [získání připojovacího řetězce účtu úložiště](#get-the-storage-account-connection-string) . Toto nastavení umožňuje, aby aplikace Function App používala zabezpečený účet úložiště pro běžné operace za běhu. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | Připojovací řetězec účtu úložiště, který jste vytvořili. Toto nastavení umožňuje, aby aplikace Function App používala účet zabezpečeného úložiště pro soubory Azure, který se používá během nasazení. |
    | **WEBSITE_CONTENTSHARE** | files | Název sdílené složky, kterou jste vytvořili v účtu úložiště. Toto nastavení použijte u WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Vytvořte toto nastavení aplikace pro připojovací řetězec vaší služby Service Bus. Tento připojovací řetězec úložiště je z oddílu [získání připojovacího řetězce služby Service Bus](#get-a-service-bus-connection-string) .|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Vytvořit toto nastavení aplikace Hodnota 1 umožňuje škálování aplikace Function App, pokud je váš účet úložiště omezený na virtuální síť. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Vytvořit toto nastavení aplikace Když se vaše aplikace integruje s virtuální sítí, bude používat stejný server DNS jako virtuální síť. Vaše aplikace Function App potřebuje toto nastavení, aby mohl pracovat s Azure DNS privátní zóny. Je vyžadován při použití soukromých koncových bodů. Toto nastavení a WEBSITE_VNET_ROUTE_ALL budou odesílat všechna odchozí volání z vaší aplikace do vaší virtuální sítě. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Vytvořit toto nastavení aplikace Když se vaše aplikace integruje s virtuální sítí, používá stejný server DNS jako virtuální síť. Vaše aplikace Function App potřebuje toto nastavení, aby mohl pracovat s Azure DNS privátní zóny. Je vyžadován při použití soukromých koncových bodů. Toto nastavení a WEBSITE_DNS_SERVER budou odesílat všechna odchozí volání z vaší aplikace do vaší virtuální sítě. |

1. V zobrazení **Konfigurace** vyberte kartu **nastavení modulu runtime funkce** .

1. Nastavte **monitorování škálování modulu runtime** na **zapnuto**. Pak vyberte **Uložit**. Škálování řízené za běhu umožňuje připojit funkce triggeru jiného typu než HTTP ke službám, které běží ve vaší virtuální síti.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Snímek obrazovky s postupem povolení škálování řízeného modulem runtime pro Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Nasazení triggeru služby Service Bus a triggeru HTTP

1. V GitHubu přejdete na následující ukázkové úložiště. Obsahuje aplikaci funkcí a dvě funkce, aktivační událost HTTP a Trigger fronty služby Service Bus.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. V horní části stránky vyberte možnost **rozvětvení** , aby se vytvořilo rozvětvení tohoto úložiště ve vlastním účtu GitHubu nebo organizaci.

1. V nabídce na levé straně aplikace Function App vyberte **centrum nasazení**. Pak vyberte **Nastavení**.

1. Na kartě **Nastavení** použijte nastavení nasazení uvedená v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Zdroj** | GitHubu | Měli byste mít vytvořené úložiště GitHub pro ukázkový kód v kroku 2. | 
    | **Organizace**  | myOrganization | Organizace, na kterou je vaše úložiště vráceno. Obvykle je to váš účet. |
    | **Úložiště** | myRepo | Úložiště, které jste vytvořili pro ukázkový kód. |
    | **Větev** | main | Hlavní větev úložiště, kterou jste vytvořili. |
    | **Zásobník modulu runtime** | .NET | Vzorový kód je v jazyce C#. |

1. Vyberte **Uložit**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Snímek obrazovky s postupem nasazení Azure Functionsho kódu prostřednictvím portálu":::

1. Vaše počáteční nasazení může trvat několik minut. Po úspěšném nasazení aplikace se na kartě **protokoly** zobrazí stavová zpráva o **úspěchu (aktivní)** . V případě potřeby aktualizujte stránku.

Gratulujeme! Úspěšně jste nasadili ukázkovou aplikaci Function App.

## <a name="lock-down-your-function-app"></a>Uzamknutí aplikace Function App

Nyní vytvořte soukromý koncový bod pro uzamknutí aplikace Function App. Tento soukromý koncový bod připojí vaši aplikaci funkcí soukromě a bezpečně k vaší virtuální síti pomocí privátní IP adresy. 

Další informace najdete v dokumentaci ke [soukromým koncovým bodem](../private-link/private-endpoint-overview.md).

1. V nabídce na levé straně aplikace Function App vyberte **síť**.

1. V části **připojení privátního koncového bodu** vyberte **Konfigurovat připojení privátních koncových bodů**.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Snímek obrazovky s navigací k privátnímu koncovému bodu aplikace Function App":::

1. Vyberte **Přidat**.

1. V podokně, které se otevře, použijte následující nastavení privátního koncového bodu:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Snímek obrazovky s postupem vytvoření privátního koncového bodu aplikace Function App Název je functionapp-Endpoint. Předplatné je &quot;privátní test sub CACHHAI&quot;. Virtuální síť je MyVirtualNet – kurz. Podsíť je nastavená jako výchozí.":::

1. Pro přidání privátního koncového bodu vyberte **OK** . 
 
Gratulujeme! Úspěšně jste provedli zabezpečení vaší aplikace Function App, Service Bus a účtu úložiště přidáním privátních koncových bodů!

### <a name="test-your-locked-down-function-app"></a>Testování uzamčené aplikace Function App

1. V nabídce na levé straně aplikace Function App vyberte **funkce**.

1. Vyberte **ServiceBusQueueTrigger**.

1. V nabídce na levé straně vyberte **monitor**. 
 
Uvidíte, že nemůžete monitorovat svoji aplikaci. Váš prohlížeč nemá přístup k virtuální síti, takže nemůže přistupovat přímo k prostředkům v rámci virtuální sítě. 
 
Tady je alternativní způsob, jak monitorovat funkci pomocí Application Insights:

1. V nabídce na levé straně aplikace Function App vyberte **Application Insights**. Pak vyberte **zobrazit Application Insights data**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Snímek obrazovky s informacemi o tom, jak zobrazit Application Insights pro aplikaci Function App":::

1. V nabídce na levé straně vyberte **živá metriky**.

1. Otevřete novou kartu. Ve službě Service Bus v nabídce na levé straně vyberte **fronty**.

1. Vyberte svou frontu.

1. V nabídce na levé straně vyberte **Service Bus Průzkumník**. V části **Odeslat** pro **typ obsahu** vyberte **Text/prostý**. Pak zadejte zprávu. 

1. Vyberte **Odeslat** a odešlete zprávu.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Snímek obrazovky, jak odesílat zprávy služby Service Bus pomocí portálu.":::

1. Na kartě **živé metriky** byste měli vidět, že se aktivovala aktivační událost fronty služby Service Bus. Pokud tomu tak není, odešlete zprávu znovu z **aplikace Service Bus Explorer**.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Snímek obrazovky zobrazující zprávy pomocí živých metrik pro aplikace Function App":::

Gratulujeme! Úspěšně jste otestovali instalaci funkcí aplikací pomocí soukromých koncových bodů.

## <a name="understand-private-dns-zones"></a>Principy privátních zón DNS
Použili jste privátní koncový bod pro připojení k prostředkům Azure. Připojujete se k privátní IP adrese místo veřejného koncového bodu. Stávající služby Azure jsou nakonfigurované tak, aby používaly existující službu DNS pro připojení k veřejnému koncovému bodu. Aby bylo možné se připojit k privátnímu koncovému bodu, musíte přepsat konfiguraci DNS.

Pro každý prostředek Azure nakonfigurovaný s privátním koncovým bodem se vytvoří privátní zóna DNS. Pro každou soukromou IP adresu přidruženou k privátnímu koncovému bodu se vytvoří záznam DNS.

V tomto kurzu byly vytvořeny následující zóny DNS:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci funkcí Premium, účet úložiště a Service Bus. Všechny tyto prostředky jsou zabezpečeny za soukromé koncové body. 

Další informace o dostupných síťových funkcích získáte pomocí následujících odkazů:

> [!div class="nextstepaction"]
> [Možnosti sítě v Azure Functions](./functions-networking-options.md)


> [!div class="nextstepaction"]
> [Plán Azure Functions Premium](./functions-premium-plan.md)