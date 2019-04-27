---
title: Integrace Azure Functions s Azure virtual network
description: Podrobný kurz, který vám ukáže, jak se připojit ke službě Azure virtual network funkce
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 96ab479d3373eb6e575a00898f7007a4df252e39
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125667"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integrace aplikace function app s Azure virtual network

V tomto kurzu se dozvíte, jak pomocí služby Azure Functions pro připojení k prostředkům ve virtuální síti Azure.

V tomto kurzu nasadíme webu z Wordpressu na virtuálním počítači ve virtuální síti, která není přístupný z Internetu. Pak nasadíme funkce s přístupem k Internetu a virtuální sítě. Použijeme tuto funkci pro přístup k prostředkům z webu WordPress nasazené ve virtuální síti.

Další informace o tom, jak systém funguje, najdete v řešení potíží a pokročilá konfigurace [integrujte svou aplikaci s Azure virtual network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). Služba Azure functions v plánu Premium mají stejné možnosti hostování jako webové aplikace, tak všechny funkce a omezení v tomto článku použít u funkcí.

## <a name="topology"></a>Topologie

 ![Uživatelské rozhraní pro integrace služby virtual network][1]

## <a name="create-a-vm-inside-a-virtual-network"></a>Vytvoření virtuálního počítače ve virtuální síti

Pokud chcete začít, vytvoříme předem nakonfigurovaných virtuálních počítačů, na kterém běží WordPress ve virtuální síti. 

Zvolili jsme WordPress na virtuálním počítači, protože je jedním z nejlevnější prostředky, které je možné nasadit ve virtuální síti. Všimněte si, že tento scénář může také pracovat s jakýmikoli prostředky ve virtuální síti, jako je například rozhraní REST API služby App Service Environment a dalšími službami Azure.

1. Přejděte na web Azure Portal.
2. Přidat nový prostředek tak, že otevřete **vytvořit prostředek** okno.
3. Vyhledejte "[WordPress LEMP7 Max Performance na CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" a otevřete okno pro jeho vytvoření. 
4. Na **Základy** kartu, nakonfigurujte virtuální počítač s následujícími informacemi:
    1. Vytvořte novou skupinu prostředků pro tento virtuální počítač, aby vymazání prostředků jednodušší na konci tohoto kurzu. Tady s ukázkovým používáme "Kurzu funkce virtuální sítě".
    1. Zadejte jedinečný název virtuálního počítače. Jako příklad používáme "Připojení typu VNET-Wordpress".
    1. Vyberte oblast co nejblíže k vám.
    1. Vyberte velikost jako B1s (1 virtuální procesor, 1 GB paměti).
    1. Pro účet správce vyberte ověřování pomocí hesla a zadejte jedinečné uživatelské jméno a heslo. V tomto kurzu nebudete potřebovat k přihlášení k virtuálnímu počítači, pokud potřebujete řešit.
    
        ![Základní informace o kartě pro vytvoření virtuálního počítače](./media/functions-create-vnet/create-vm-1.png)

1. Přesunout **sítě** kartě a zadejte následující informace:
    1.  Vytvořte novou virtuální síť.
    1.  Zadejte rozsah privátních adres a podsítí v rámci tohoto rozsahu adres. Velikost podsítě určí, kolik virtuálních počítačů můžete použít v plánu služby App Service. Pokud jsou pro vás, nová IP adresování a zapojení podsítě je [dokument, který vysvětluje](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Přidělování IP adres a podsítí jsou důležité v tomto scénáři, proto doporučujeme, abyste si několik článků a videí, několik online dokud má smysl. 
    
        V tomto příkladu jsme se rozhodli použít 10.10.0.0/16 síť s podsítí 10.10.1.0/24. Jsme předimenzování a použití /16 podsítě vzhledem k tomu, že je snadno spočítat podsítě, které jsou k dispozici v síti 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Zpět na **sítě** kartu, nastavte veřejnou IP adresu na **žádný**. Tento krok bude nasazení virtuálního počítače s přístupem k virtuální síti.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. Vytvoření virtuálního počítače. Tento proces bude trvat přibližně 5 minut.
8. Po vytvoření virtuálního počítače, přejděte do jeho **sítě** kartu a poznamenejte si privátní IP adresu pro pozdější. Virtuální počítač by neměl mít veřejnou IP adresu.

    ![14]

Teď máte webu z Wordpressu nasazené výhradně ve vaší virtuální síti. Tento web není přístupný z veřejného Internetu.

## <a name="create-a-function-app-in-a-premium-plan"></a>Vytvoření aplikace funkcí v plánu Premium

Dalším krokem je vytvoření aplikace funkcí v plánu Premium. Plán Premium přináší měřítku a bez serveru všechny výhody vyhrazený plán služby App Service. Aplikace Function App vytvořena prostřednictvím plánu Consumption nepodporují integrace služby virtual network.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-virtual-network"></a>Připojit vaši aplikaci function app k virtuální síti

Pomocí webu z Wordpressu hostování souborů z v rámci vaší virtuální sítě můžete teď připojení aplikace function app k virtuální síti.

1.  Na portálu pro danou aplikaci funkcí z předchozího kroku vyberte **funkce platformy**. Potom vyberte **sítě**.

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Vyberte **klepnutím sem můžete nakonfigurovat** pod **integrace virtuální sítě**.

    ![Stav pro konfiguraci sítě](./media/functions-create-vnet/Networking-1.png)

1. Na stránce integrace virtuální sítě vyberte **přidání virtuální sítě (preview)**.

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Vytvořte novou podsíť pro funkci a plán služby App Service použít. Všimněte si, že velikost podsítě omezí celkový počet virtuálních počítačů, které můžete přidat do plánu služby App Service. Virtuální síť bude automaticky směrovat provoz mezi podsítěmi ve virtuální síti, takže nezáleží, že funkce je v jiné podsíti, z vašeho virtuálního počítače. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-virtual-network"></a>Vytvořit funkci, která se získá přístup k prostředku ve virtuální síti

Aplikace function app můžete nyní přístup k virtuální síti s náš web WordPress. Proto vytvoříme pomocí funkce pro přístup k souboru a obsluhovat zpět uživateli. V tomto příkladu použijeme webu z Wordpressu jako proxy server a rozhraní API jako volání funkce vzhledem k tomu, že jsou oba usnadňuje nastavení a vizualizace. 

Stejně snadno můžete použít jakékoli jiné rozhraní API nasazené ve virtuální síti. Můžete také použít jinou funkci s kódem, který provede volání rozhraní API pro rozhraní API nasazené ve vaší virtuální síti. Instance systému SQL Server nasazeny v rámci vaší virtuální sítě je ideální příklad.

1. Na portálu otevřete aplikaci function app v předchozím kroku.
1. Vytvořit proxy tak, že vyberete **proxy** > **+**.

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Nakonfigurujte název proxy serveru a směrování. Tento příklad používá "/ zařízení" jako trasu.
1. Zadejte vaše lokalita WordPress IP z předchozí a nastavit **URL back-end** do `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

Teď když zkusíte navštivte adresu URL back-end přímo zadáním nebo vložením do na nové kartě prohlížeče, na stránce by vypršení časového limitu. Je to proto, že vaše lokalita WordPress je připojený k vaší virtuální sítě a nikoli po Internetu. Pokud vložíte adresu URL vašeho proxy serveru do prohlížeče, měli byste vidět obrázku zařízení (získaných z webu WordPress) uvnitř virtuální sítě. 

Aplikace function app je připojený k Internetu a virtuální sítí. Proxy server je přijímání žádosti přes veřejný internet a pak funguje jako jednoduchý proxy server HTTP pro předávání tohoto požadavku podél do virtuální sítě. Proxy server potom předává požadavky odpověď zpět do prostřednictvím veřejného Internetu. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>Další postup

Funkce běžící v plánu Premium sdílet stejnou základní infrastruktury služby App Service jako webové aplikace v plánech PremiumV2. V dokumentaci pro webové aplikace se vztahuje na funkce plánu Premium.

* [Další informace o možnosti sítě v funkce](./functions-networking-options.md)
* [Přečtěte si funkce sítě – nejčastější dotazy](./functions-networking-faq.md)
* [Další informace o virtuálních sítí v Azure](../virtual-network/virtual-networks-overview.md)
* [Povolit další síťové funkce a ovládací prvek s App Service Environment](../app-service/environment/intro.md)
* [Připojení k jednotlivým místním prostředkům bez nutnosti změn brány firewall pomocí hybridních připojení](../app-service/app-service-hybrid-connections.md)
* [Další informace o proxy služby Functions](./functions-proxies.md)

<!--Image references -->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
