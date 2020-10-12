---
title: Integrace Azure Functions s Azure Virtual Network
description: Podrobný kurz, ve kterém se dozvíte, jak připojit funkci ke službě Azure Virtual Network
ms.topic: article
ms.date: 4/23/2020
ms.openlocfilehash: f50c923104fdfcf26f400f20f0de66a82eb3d245
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87387519"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Kurz: Integrace služby Functions s virtuální sítí Azure

V tomto kurzu se dozvíte, jak pomocí Azure Functions připojit k prostředkům ve službě Azure Virtual Network. vytvoříte funkci, která má přístup k Internetu i k virtuálnímu počítači, na kterém je spuštěný WordPress ve virtuální síti.

> [!div class="checklist"]
> * Vytvoření aplikace Function App v plánu Premium
> * Nasazení webu WordPress do virtuálního počítače ve virtuální síti
> * Připojení aplikace Function App k virtuální síti
> * Vytvoření proxy funkce pro přístup k prostředkům WordPress
> * Žádost o soubor WordPress z virtuální sítě

## <a name="topology"></a>Topologie

Následující diagram znázorňuje architekturu řešení, kterou vytvoříte:

 ![Uživatelské rozhraní pro integraci virtuální sítě](./media/functions-create-vnet/topology.png)

Funkce spuštěné v plánu Premium mají stejné možnosti hostování jako webové aplikace v Azure App Service, což zahrnuje funkci Integrace virtuální sítě. Další informace o integraci virtuální sítě, včetně řešení potíží a pokročilých konfigurací, najdete v tématu [integrace aplikace do služby Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu je důležité pochopit IP adresy a podsítě. Můžete začít s [tímto článkem, který se zabývá základy adresování a podsítí](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Mnoho dalších článků a videí je k dispozici online.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-function-app-in-a-premium-plan"></a>Vytvoření aplikace Function App v plánu Premium

Nejdřív v [plánu Premium]vytvoříte aplikaci Function App. Tento plán poskytuje škálování bez serveru při podpoře integrace virtuální sítě.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Aplikaci Function App můžete připnout na řídicí panel tak, že vyberete ikonu připnutí v pravém horním rohu. Připnutí usnadňuje návrat do této aplikace Function App po vytvoření virtuálního počítače.

## <a name="create-a-vm-inside-a-virtual-network"></a>Vytvoření virtuálního počítače ve virtuální síti

V dalším kroku vytvořte předkonfigurovaný virtuální počítač, který spouští WordPress v rámci virtuální sítě ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) by Jetware). Virtuální počítač WordPress se používá kvůli nízkým nákladům a pohodlí. Stejný scénář funguje s jakýmkoli prostředkem ve virtuální síti, jako jsou rozhraní REST API, App Service prostředí a další služby Azure. 

1. Na portálu vyberte v levém navigačním podokně možnost **+ vytvořit prostředek** , v poli typ hledání zadejte `WordPress LEMP7 Max Performance` a stiskněte klávesu ENTER.

1. Ve výsledcích hledání vyberte možnost **WordPress Lemp Max výkon** . Vyberte plán softwaru **WordPress Lemp Max Performance pro CentOS** jako **plán softwaru** a vyberte **vytvořit**.

1. Na kartě **základy** použijte nastavení virtuálního počítače, jak je uvedeno v tabulce pod obrázkem:

    ![Karta základy pro vytvoření virtuálního počítače](./media/functions-create-vnet/create-vm-1.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. | 
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Vyberte `myResourceGroup` nebo skupinu prostředků, kterou jste vytvořili pomocí aplikace Function App. Použití stejné skupiny prostředků pro aplikaci Function App, virtuálního počítače WordPress a plánu hostování usnadňuje vyčištění prostředků, pokud jste s tímto kurzem hotovi. |
    | **Název virtuálního počítače** | VNET-Wordpress | Název virtuálního počítače musí být ve skupině prostředků jedinečný. |
    | **[Oblast](https://azure.microsoft.com/regions/)** | Evropským Západní Evropa | Vyberte oblast poblíž nebo poblíž funkcí, které přistupují k virtuálnímu počítači. |
    | **Velikost** | B1s | Zvolte možnost **změnit velikost** a pak vyberte standardní image B1s, která má 1 vCPU a 1 GB paměti. |
    | **Typ ověřování** | Heslo | Chcete-li použít ověřování heslem, je nutné zadat také **uživatelské jméno**, zabezpečené **heslo**a **potvrzení hesla**. Pro tento kurz se nemusíte přihlašovat k virtuálnímu počítači, pokud nepotřebujete řešit problémy. |

1. Zvolte kartu **síť** a v části konfigurovat virtuální sítě vyberte **vytvořit novou**.

1. V části **vytvořit virtuální síť**použijte nastavení v tabulce pod obrázkem:

    ![Karta sítě pro vytvoření virtuálního počítače](./media/functions-create-vnet/create-vm-2.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Název** | myResourceGroup – VNet | Můžete použít výchozí název vygenerovaný pro vaši virtuální síť. |
    | **Rozsah adres** | 10.10.0.0/16 | Pro virtuální síť použijte jeden rozsah adres. |
    | **Název podsítě** | Tutorial-Net | Název podsítě. |
    | **Rozsah adres** (podsíť) | 10.10.1.0/24   | Velikost podsítě určuje, kolik rozhraní lze do podsítě přidat. Tuto podsíť používá web WordPress.  `/24`Podsíť poskytuje adresy hostitele 254. |

1. Vyberte **OK** a vytvořte virtuální síť.

1. Zpátky na kartě **sítě** vyberte pro **veřejnou IP adresu**možnost **žádné** .

1. Zvolte kartu **Správa** a potom v části **účet úložiště diagnostiky**zvolte účet úložiště, který jste vytvořili pomocí aplikace Function App.

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**. Proces vytvoření virtuálního počítače trvá několik minut. Vytvořený virtuální počítač má přístup jenom k virtuální síti.

1. Po vytvoření virtuálního počítače klikněte **na přejít k prostředku** , abyste si zobrazili stránku pro nový virtuální počítač, a pak v části **Nastavení**vyberte **sítě** .

1. Ověřte, že neexistuje žádná **Veřejná IP adresa**. Poznamenejte si **soukromou IP adresu**, kterou používáte pro připojení k virtuálnímu počítači z aplikace Function App.

    ![Nastavení sítě ve virtuálním počítači](./media/functions-create-vnet/vm-networking.png)

Teď máte web WordPress nasazený zcela v rámci vaší virtuální sítě. Tento web není přístupný z veřejného Internetu.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Připojení aplikace Function App k virtuální síti

Díky webu WordPress běžícímu na virtuálním počítači ve virtuální síti teď můžete připojit aplikaci funkcí k této virtuální síti.

1. V nové aplikaci Function App vyberte v nabídce vlevo možnost **sítě** .

1. V části **Integrace virtuální**sítě vyberte **Konfigurovat kliknutím sem**.

    :::image type="content" source="./media/functions-create-vnet/networking-0.png" alt-text="Výběr možnosti sítě v aplikaci Function App":::

1. Na stránce **Integrace virtuální sítě** vyberte **Přidat virtuální síť**.

    :::image type="content" source="./media/functions-create-vnet/networking-2.png" alt-text="Výběr možnosti sítě v aplikaci Function App":::

1. V části **stav síťové funkce**použijte nastavení v tabulce pod obrázkem:

    ![Definovat funkci Virtual Network aplikace Function App](./media/functions-create-vnet/networking-3.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup – VNet | Tato virtuální síť je ta, kterou jste vytvořili dříve. |
    | **Podsíť** | Vytvořit novou podsíť | Vytvořte podsíť ve virtuální síti, kterou bude používat aplikace Function App. Integrace virtuální sítě musí být nakonfigurovaná tak, aby používala prázdnou podsíť. Nezáleží na tom, že vaše funkce používají jinou podsíť než váš virtuální počítač. Virtuální síť automaticky směruje přenosy mezi těmito dvěma podsítěmi. |
    | **Název podsítě** | Function-Net | Název nové podsítě. |
    | **Blok adres virtuální sítě** | 10.10.0.0/16 | Vyberte stejný blok adres používaný webem WordPress. Měli byste mít definován pouze jeden blok adres. |
    | **Rozsah adres** | 10.10.2.0/24   | Velikost podsítě omezuje celkový počet instancí, na které může vaše aplikace Functions plánu Premium škálovat. V tomto příkladu `/24` se používá podsíť s 254 dostupnými adresami hostitele. Tato podsíť je předem zřízená, ale je snadno vypočtená. |

1. Vyberte **OK** , chcete-li přidat podsíť. Zavřete stránky stav **Integrace virtuální** **sítě a síťové funkce** a vraťte se na stránku Function App.

Aplikace Function app teď má přístup k virtuální síti, ve které je web WordPress spuštěný. V dalším kroku použijete [proxy služby Azure Functions](functions-proxies.md) k vrácení souboru z webu WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Vytvoření proxy serveru pro přístup k prostředkům virtuálního počítače

Když máte povolenou integraci virtuální sítě, můžete ve své aplikaci Function App vytvořit proxy server, abyste předali požadavky na virtuální počítač běžící ve virtuální síti.

1. Ve vaší aplikaci Function App vyberte v nabídce vlevo možnost  **proxy** a pak vyberte **Přidat**. Použijte nastavení proxy serveru v tabulce pod obrázkem:

    :::image type="content" source="./media/functions-create-vnet/create-proxy.png" alt-text="Výběr možnosti sítě v aplikaci Function App":::

    | Nastavení  | Navrhovaná hodnota  | Popis      |
    | -------- | ---------------- | ---------------- |
    | **Název** | Plant | Název může být libovolná hodnota. Slouží k identifikaci proxy serveru. |
    | **Šablona směrování** | /plant | Trasa, která se mapuje na prostředek virtuálního počítače. |
    | **Adresa URL back-endu** | http://<YOUR_VM_IP>/wp-content/Themes/twentyseventeen/assets/images/header.jpg | Nahraďte `<YOUR_VM_IP>` IP adresou vašeho virtuálního počítače WordPress, který jste vytvořili dříve. Toto mapování vrátí jeden soubor z webu. |

1. Výběrem **vytvořit** přidejte proxy do aplikace Function App.

## <a name="try-it-out"></a>Vyzkoušet

1. V prohlížeči se pokuste získat přístup k adrese URL, kterou jste použili jako **adresu URL back-endu**. Podle očekávání vypršel časový limit žádosti. Dojde k vypršení časového limitu, protože vaše lokalita WordPress je připojená jenom k vaší virtuální síti, a ne k Internetu.

1. Zkopírujte hodnotu **URL proxy serveru** z nového proxy serveru a vložte ji do adresního řádku v prohlížeči. Vrácený obrázek je z webu WordPress běžícího ve vaší virtuální síti.

    ![Soubor bitové kopie podniku vrácený z webu WordPress](./media/functions-create-vnet/plant.png)

Vaše aplikace Function App je připojená k Internetu i k vaší virtuální síti. Proxy přijímá požadavek přes veřejný Internet a pak funguje jako jednoduchý proxy server HTTP, který předává tento požadavek do připojené virtuální sítě. Proxy pak přenáší odpověď zpátky na veřejně přes Internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu web WordPress slouží jako rozhraní API, které se volá pomocí proxy v aplikaci Function App. Tento scénář představuje dobrý kurz, protože se snadno nastavuje a vizualizuje. Můžete použít jakékoli jiné rozhraní API nasazené v rámci virtuální sítě. Mohli jste také vytvořit funkci s kódem, který volá rozhraní API nasazená v rámci virtuální sítě. Realističtější scénář je funkce, která používá rozhraní API klienta data k volání instance SQL Server nasazené ve virtuální síti.

Funkce spuštěné v plánu Premium sdílí stejnou základní infrastrukturu App Service jako webové aplikace v plánech PremiumV2. Veškerá dokumentace k [webovým aplikacím v Azure App Service](../app-service/overview.md) se vztahuje na vaše funkce plánu Premium.

> [!div class="nextstepaction"]
> [Další informace o možnostech sítě ve funkcích](./functions-networking-options.md)

[Plán Premium]: functions-scale.md#premium-plan
