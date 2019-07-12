---
title: Integrace Azure Functions s Azure virtual network
description: Podrobný kurz, který vám ukáže, jak se připojit ke službě Azure virtual network funkce
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0a31b58a3c843a2add0c84dc1a3ad4ab6417815e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612888"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Kurz: integrate funkce do služby Azure virtual network

V tomto kurzu se dozvíte, jak pomocí služby Azure Functions pro připojení k prostředkům ve virtuální síti Azure. vytvoříte funkci, která má přístup k oběma Internetu a do virtuálního počítače s Wordpressem ve virtuální síti.

> [!div class="checklist"]
> * Vytvoření aplikace funkcí v plánu Premium
> * Nasazení webu z Wordpressu na virtuální počítač ve virtuální síti
> * Připojení aplikace function app k virtuální síti
> * Vytvoření funkce proxy pro přístup k prostředkům WordPress
> * Žádost o soubor WordPress z virtuální sítě

> [!NOTE]  
> Tento kurz vytvoří aplikaci funkcí v plánu Premium. Tento plán hostování je aktuálně ve verzi preview. Další informace najdete v tématu [plán Premium].

## <a name="topology"></a>Topologie

Následující diagram znázorňuje architekturu řešení, kterou vytvoříte:

 ![Uživatelské rozhraní pro integrace služby virtual network](./media/functions-create-vnet/topology.png)

Funkce spuštěné v plánu Premium mají stejné možnosti hostování jako webové aplikace ve službě Azure App Service, který zahrnuje funkci integrace virtuální sítě. Další informace o integrace virtuální sítě, včetně řešení potíží a pokročilá konfigurace, najdete v článku [integrujte svou aplikaci s Azure virtual network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu je důležité se napřed seznámit IP adresování a zapojení podsítě. Můžete začít s [tohoto článku, který obsahuje základní informace o přidělování adres a podsítí](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Mnoho že další články a videa jsou k dispozici online.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-function-app-in-a-premium-plan"></a>Vytvoření aplikace funkcí v plánu Premium

Nejprve vytvoříte aplikaci function app v [plán Premium]. Tento plán umožňuje škálování bez serveru při současné podpoře integrace služby virtual network.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Aplikace function app na řídicí panel můžete připnout tak, že vyberete ikonu připínáčku v pravém horním rohu. Připnutí usnadňuje po vytvoření virtuálního počítače se vraťte k této aplikace function app.

## <a name="create-a-vm-inside-a-virtual-network"></a>Vytvoření virtuálního počítače ve virtuální síti

Dále vytvořte předem nakonfigurovaných virtuálních počítačů, na kterém běží WordPress ve virtuální síti ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) ve společnosti Jetware). WordPress virtuálního počítače se používá z důvodu jeho s nízkými náklady a pohodlí. Tento scénář stejné spolupracuje s jakýmkoliv prostředkům ve virtuální síti, jako je například rozhraní REST API služby App Service Environment a dalšími službami Azure. 

1. Na portálu, vyberte **+ vytvořit prostředek** na levém navigačním podokně hledání zadejte do pole `WordPress LEMP7 Max Performance`, a stiskněte klávesu Enter.

1. Zvolte **Wordpress LEMP Max Performance** ve výsledcích hledání. Vyberte plán softwaru **Wordpress LEMP Max Performance pro CentOS** jako **plán softwaru** a vyberte **vytvořit**.

1. V **Základy** kartu, použijte nastavení virtuálního počítače uvedená v tabulce pod obrázkem:

    ![Základní informace o kartě pro vytvoření virtuálního počítače](./media/functions-create-vnet/create-vm-1.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se prostředky vytvoří. | 
    | **[Skupina prostředků](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | Zvolte `myResourceGroup`, nebo skupinu prostředků, který jste vytvořili pomocí aplikace function app. Stejnou skupinu prostředků pro aplikace function app, WordPress virtuálního počítače a plán hostování usnadňuje vyčištění prostředků po dokončení tohoto kurzu. |
    | **Název virtuálního počítače** | VNET-Wordpress | Název virtuálního počítače musí být jedinečný ve skupině prostředků |
    | **[Region](https://azure.microsoft.com/regions/)** | (Evropa) Evropa – západ | Zvolte oblast blízko vás nebo blízko ní. funkce, které přistupují k virtuálnímu počítači. |
    | **Velikost** | B1s | Zvolte **změnit velikost** a potom vyberte image standardní B1s, který má 1 virtuálních procesorů a 1 GB paměti. |
    | **Typ ověřování** | Heslo | Pokud chcete použít ověřování pomocí hesla, musíte zadat také **uživatelské jméno**, zabezpečené **heslo**a potom **potvrzení hesla**. V tomto kurzu nebudete potřebovat k přihlášení k virtuálnímu počítači, pokud potřebujete řešit. |

1. Zvolte **sítě** kartu a v části Konfigurace virtuální sítě vyberte **vytvořit nový**.

1. V **vytvořit virtuální síť**, použijte nastavení v tabulce pod obrázkem:

    ![Karta síť se o vytvoření virtuálního počítače](./media/functions-create-vnet/create-vm-2.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Název** | myResourceGroup-vnet | Můžete použít výchozí název vygenerované pro vaši virtuální síť. |
    | **Rozsah adres** | 10.10.0.0/16 | Použijte rozsah jednu adresu pro virtuální síť. |
    | **Název podsítě** | Kurz – Net | Název podsítě. |
    | **Rozsah adres** (podsítě) | 10.10.1.0/24   | Velikost podsítě určuje, kolik rozhraní lze přidat do podsítě. Tato podsíť se používá ve Web WordPress.  A `/24` podsítě poskytuje 254 hostiteli adresy. |

1. Vyberte **OK** při vytváření virtuální sítě.

1. Zpátky **sítě** , vyberte **žádný** pro **veřejnou IP adresu**.

1. Zvolte **správu** kartu, pak v **účet úložiště diagnostiky**, zvolte účet úložiště, který jste vytvořili pomocí aplikace function app.

1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření, vyberte **vytvořit**. Virtuální počítač vytvořit proces trvá několik minut. Vytvořený virtuální počítač pouze přístup k virtuální síti.

1. Po vytvoření virtuálního počítače, zvolte **přejít k prostředku** zobrazíte stránku pro nový virtuální počítač, klikněte na tlačítko **sítě** pod **nastavení**.

1. Ověřte, že neexistuje žádná **veřejnou IP adresu**. Poznamenejte si **privátní IP**, který použijete k připojení k virtuálnímu počítači z aplikace function app.

    ![Nastavení sítě na virtuálním počítači](./media/functions-create-vnet/vm-networking.png)

Teď máte webu z Wordpressu nasazené výhradně ve vaší virtuální síti. Tento web není přístupný z veřejného Internetu.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Připojit vaši aplikaci function app k virtuální síti

S WordPress lokality, kde běží na virtuálním počítači ve virtuální síti můžete teď k této virtuální síti připojit vaši aplikaci function app.

1. Ve vaší nové aplikaci function app, vyberte **funkce platformy** > **sítě**.

    ![Vyberte sítě do aplikace function App](./media/functions-create-vnet/networking-0.png)

1. V části **integrace virtuální sítě**vyberte **klepnutím sem můžete nakonfigurovat**.

    ![Stav pro konfiguraci sítě](./media/functions-create-vnet/Networking-1.png)

1. Na stránce integrace virtuální sítě vyberte **přidání virtuální sítě (preview)** .

    ![Přidání integrace virtuální sítě ve verzi preview](./media/functions-create-vnet/networking-2.png)

1. V **stav síťových funkcí**, použijte nastavení v tabulce pod obrázkem:

    ![Definovat virtuální sítě app – funkce](./media/functions-create-vnet/networking-3.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | Tato virtuální síť je ta, kterou jste vytvořili dříve. |
    | **Podsíť** | Vytvořit novou podsíť | Vytvořte podsíť ve virtuální síti pro aplikaci function app používat. Integrace virtuální sítě musí být nakonfigurován pro použití prázdnou podsíť. Nevadí, že vaše funkce použijte jinou podsíť než váš virtuální počítač. Virtuální sítě automaticky směruje provoz mezi těmito dvěma podsítěmi. |
    | **Název podsítě** | Net – funkce | Název nové podsítě. |
    | **Blok adres virtuální sítě** | 10.10.0.0/16 | Zvolte stejný blok adres používané web WordPress. Měli byste mít pouze jeden blok adres definované. |
    | **Rozsah adres** | 10.10.2.0/24   | Velikost podsítě omezí celkový počet instancí, které vaše aplikace funkcí plánu Premium můžete škálovat na. Tento příklad používá `/24` podsíť s 254 hostiteli k dispozici adresy. Tato podsíť je nadměrně zřízených, ale snadno spočítat. |

1. Vyberte **OK** přidat podsíť. Zavřete integrace virtuální sítě a stav síťových funkcí stránky se vraťte na stránku aplikace funkce.

Aplikace function app teď přístup k virtuální síti, ve kterém je spuštěn web WordPress. Dále použijete [proxy služby Azure Functions](functions-proxies.md) vrátit soubor z webu WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Vytvořit proxy pro přístup k prostředkům virtuálního počítače

Integrace virtuální sítě povolené můžete vytvořit proxy serveru ve vaší aplikaci funkcí ke směrování žádostí na virtuální počítač provozovaný ve virtuální síti.

1. V aplikaci function app, vyberte **proxy** >  **+** , potom použijte nastavení proxy v tabulce pod obrázkem:

    ![Definujte nastavení proxy serveru](./media/functions-create-vnet/create-proxy.png)

    | Nastavení  | Navrhovaná hodnota  | Popis      |
    | -------- | ---------------- | ---------------- |
    | **Název** | Zařízení | Název může obsahovat libovolnou hodnotu. Používá se k identifikaci proxy serveru. |
    | **Šablona trasy** | /Plant | Trasy, která se mapuje na prostředek virtuálního počítače. |
    | **Adresa URL back-endu** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Nahraďte `<YOUR_VM_IP>` s IP adresou vašeho virtuálního počítače WordPress, který jste vytvořili dříve. Toto mapování vrátí jeden soubor z webu. |

1. Vyberte **vytvořit** přidání proxy serveru pro vaši aplikaci function app.

## <a name="try-it-out"></a>Vyzkoušet

1. V prohlížeči se pokusí o přístup k adresu URL jste použili jako **URL back-end**. Podle očekávání, vyprší časový limit žádosti. Vzhledem k tomu, že vaše lokalita WordPress je připojený jenom k vaší virtuální sítě a nikoli po Internetu, dojde k vypršení časového limitu.

1. Kopírovat **adresu URL proxy serveru** hodnotu nový proxy server a vložte ji do adresního řádku prohlížeče. Vrácené obrázek je z webu WordPress spuštěných ve virtuální síti.

    ![Soubor obrázku zařízení vrácená z webu WordPress](./media/functions-create-vnet/plant.png)

Aplikace function app je připojený k Internetu a virtuální sítí. Proxy server je přijímání žádosti přes veřejný internet a pak funguje jako jednoduchý proxy server HTTP pro předávání tento požadavek na připojené virtuální sítě. Proxy server potom předává požadavky odpověď zpět do veřejně přes internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu se web WordPress slouží jako rozhraní API, která je volána pomocí proxy aplikace function App. Tento scénář je dobré kurz, protože je snadné nastavení a vizualizace. Můžete použít jakékoli jiné rozhraní API nasazené ve virtuální síti. Může také jste vytvořili funkci s kódem, který volá rozhraní API nasazené ve virtuální síti. Vyzkoušet realističtější scénář je funkce, která používá data klientských rozhraní API volat instanci systému SQL Server nasazený ve virtuální síti.

Funkce běžící v plánu Premium sdílet stejnou základní infrastruktury služby App Service jako webové aplikace v plánech PremiumV2. V dokumentaci k [webové aplikace ve službě Azure App Service](../app-service/overview.md) se vztahuje na funkce plánu Premium.

> [!div class="nextstepaction"]
> [Další informace o možnosti sítě v funkce](./functions-networking-options.md)

[Plán Premium]: functions-scale.md#premium-plan
