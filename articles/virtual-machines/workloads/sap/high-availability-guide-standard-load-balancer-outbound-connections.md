---
title: Připojení veřejného koncového bodu pro virtuální počítače Azure&standardILB ve scénářích SAP HA
description: Připojení veřejného koncového bodu pro virtuální počítače pomocí nástroje Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: radeltch
ms.openlocfilehash: 4fd01764c183098a8bd78d502eea7ab173fa22cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293914"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Připojení veřejného koncového bodu pro virtuální počítače pomocí nástroje Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP

Rozsah tohoto článku je k popisu konfigurace, které umožní odchozí připojení k veřejné koncové bodu. Konfigurace jsou hlavně v kontextu vysoké dostupnosti s kardiostimulátorem pro SUSE / RHEL.  

Pokud používáte Pacemaker s agentem Azure fence ve vašem řešení s vysokou dostupností, pak virtuální počítače musí mít odchozí připojení k rozhraní API pro správu Azure.  
Článek představuje několik možností, které vám umožní vybrat možnost, která je nejvhodnější pro váš scénář.  

## <a name="overview"></a>Přehled

Při implementaci vysoké dostupnosti pro řešení SAP prostřednictvím clusteringu je jednou z nezbytných součástí [Nástroj pro vyrovnávání zatížení Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Azure nabízí dva sloky pro vyrovnávání zatížení: standardní a základní.

Standardní Azure vyrovnávání zatížení nabízí některé výhody oproti základní vyrovnávání zatížení. Například funguje napříč zónami dostupnosti Azure, má lepší možnosti monitorování a protokolování pro snadnější řešení potíží, sníženou latenci. Funkce "HA porty" pokrývá všechny porty, to znamená, že již není nutné vypsat všechny jednotlivé porty.  

Existují některé důležité rozdíly mezi základní a standardní skladovou položkou azure vyrovnávání zatížení. Jedním z nich je zpracování odchozího provozu na veřejný koncový bod. Úplné porovnání základního a standardního účetního vytížení skladových položk naleznete v tématu [Porovnání skladových položk vykladatelů zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).  
 
Když virtuální počítače bez veřejných IP adres jsou umístěny v back-endovém fondu interní (žádná veřejná IP adresa) standardní nástroj pro vyrovnávání zatížení Azure, neexistuje žádné odchozí připojení k veřejným koncovým bodům, pokud není provedena další konfigurace.  

Pokud je virtuálnímu virtuálnímu virtuálnímu síti přiřazena veřejná IP adresa nebo je virtuální ho v back-endovém fondu vyvyřič u nabili s veřejnou IP adresou, bude mít odchozí připojení k veřejným koncovým bodům.  

Systémy SAP často obsahují citlivá obchodní data. Je zřídka přijatelné, aby virtuální počítače hostující systémy SAP měly veřejné IP adresy. Současně existují scénáře, které by vyžadovaly odchozí připojení z virtuálního virtuálního zařízení do veřejných koncových bodů.  

Příklady scénářů, které vyžadují přístup k veřejnému koncovému bodu Azure, jsou:  
- Použití Azure Fence Agent jako mechanismuš oplocení v klastrech pacemakeru
- Azure Backup
- Azure Site Recovery  
- Použití veřejného úložiště pro opravy operačního systému
- Tok dat aplikace SAP může vyžadovat odchozí připojení k veřejnému koncovému bodu.

Pokud vaše nasazení SAP nevyžaduje odchozí připojení k veřejným koncovým bodům, nemusíte implementovat další konfiguraci. Stačí vytvořit interní standard SKU Azure Balancer pro váš scénář s vysokou dostupností, za předpokladu, že také není potřeba příchozí připojení z veřejných koncových bodů.  

> [!Note]
> Když virtuální počítače bez veřejných IP adres jsou umístěny v back-endovém fondu interní (žádná veřejná IP adresa) standardní azure vyrovnávání zatížení, bude existovat žádné odchozí připojení k internetu, pokud se provádí další konfigurace umožňující směrování do veřejných koncových bodů.  
>Pokud virtuální počítače mají buď veřejné IP adresy nebo už jsou v back-endovém fondu Azure Balancer s veřejnou IP adresou, virtuální počítač už bude mít odchozí připojení k veřejným koncovým bodům.


Nejprve si přečtěte tyto články:

* Standardní vyrovnávání zatížení Azure
  * [Přehled nástroje Pro vyrovnávání zatížení Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) – komplexní přehled nástroje pro vyrovnávání zatížení Azure, důležitých principů, konceptů a kurzů 
  * [Odchozí připojení v Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) – scénáře, jak dosáhnout odchozího připojení v Azure
  * [Odchozí pravidla vykladače zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)– vysvětluje koncepty odchozích pravidel vykladače zatížení a způsob vytváření odchozích pravidel
* Brána Azure Firewall
  * [Azure Firewall –](https://docs.microsoft.com/azure/firewall/overview)přehled Azure Firewall
  * [Kurz: Nasazení a konfigurace Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) – pokyny ke konfiguraci Azure Firewall přes Portál Azure
* [Virtuální sítě – uživatelsky definovaná pravidla](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) – koncepty a pravidla směrování Azure  
* [Značky služby Skupiny zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) – jak zjednodušit skupiny zabezpečení sítě a konfiguraci brány firewall pomocí značek služeb

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Další externí Azure Standard Load Balancer pro odchozí připojení k internetu

Jednou z možností, jak dosáhnout odchozího připojení k veřejným koncovým bodům, aniž by bylo možné příchozí připojení k virtuálnímu virtuálnímu počítačům z veřejného koncového bodu, je vytvořit druhý vyvyřič zatížení s veřejnou IP adresou, přidat virtuální zařízení do back-endového fondu druhého vyvažovače zatížení a definovat pouze [odchozí pravidla](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).  
Pomocí [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview) můžete řídit veřejné koncové body, které jsou přístupné pro odchozí volání z virtuálního soudu.  
Další informace naleznete v tématu Scénář 2 v dokumentu [Odchozí připojení](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios).  
Konfigurace bude vypadat takto:  

![Řízení připojení k veřejným koncovým bodům pomocí skupin zabezpečení sítě](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Důležité informace

- Můžete použít jeden další veřejný nástroj pro vyrovnávání zatížení pro více virtuálních počítačů ve stejné podsíti k dosažení odchozího připojení k veřejnému koncovému bodu a optimalizaci nákladů  
- Pomocí [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview) můžete řídit, které veřejné koncové body jsou přístupné z virtuálních účtů. Skupinu zabezpečení sítě můžete přiřadit k podsíti nebo ke každému virtuálnímu virtuálnímu soudu. Pokud je to možné, použijte [service značky](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) ke snížení složitosti pravidel zabezpečení.  
- Azure standardní nástroj pro vyrovnávání zatížení s veřejnou IP adresou a odchozí mi pravidly umožňuje přímý přístup k veřejnému koncovému bodu. Pokud máte požadavky na podnikové zabezpečení, aby všechny odchozí provoz projít prostřednictvím centralizované podnikové řešení pro auditování a protokolování, nemusí být možné splnit požadavek s tímto scénářem.  

>[!TIP]
>Pokud je to možné, použijte [značky Service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) ke snížení složitosti skupiny zabezpečení sítě . 

### <a name="deployment-steps"></a>Kroky nasazení

1. Vytvořit balancer zatížení  
   1. Na [webu Azure Portal](https://portal.azure.com) klikněte na Všechny prostředky, Přidejte a vyhledejte **položku Balancer.**  
   1. Klikněte na **Vytvořit.** 
   1. Název pro vyrovnávání zatížení **MyPublicILB**  
   1. Vybrat **veřejné** jako typ, **standardní** jako skladovou položku  
   1. Vyberte **Vytvořit veřejnou IP adresu** a zadejte jako název **MyPublicILB FrondEndIP.**  
   1. Vybrat **zónu redundantní** jako zónu dostupnosti  
   1. Klikněte na Zkontrolovat a vytvořit a potom na Vytvořit.  
2. Vytvořte back-endový fond **MyBackendPoolOfPublicILB** a přidejte virtuální chod.  
   1. Výběr virtuální sítě  
   1. Vyberte virtuální chod a jejich IP adresy a přidejte je do back-endového fondu.  
3. [Vytvořte odchozí pravidla](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule). V současné době není možné vytvořit odchozí pravidla z webu Azure Portal. Odchozí pravidla můžete vytvořit pomocí [azure cli](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Vytvořte pravidla skupiny zabezpečení sítě, abyste omezili přístup k určitým veřejným koncovým bodům. Pokud existuje skupina zabezpečení sítě, můžete ji upravit. Následující příklad ukazuje, jak povolit přístup k rozhraní AZURE management API: 
   1. Přechod na skupinu zabezpečení sítě
   1. Klikněte na Odchozí pravidla zabezpečení.
   1. Přidejte pravidlo do **příkazu Odepřít** veškerý odchozí přístup k **Internetu**.
   1. Přidejte pravidlo **pro povolení** přístupu k **AzureCloudu**s prioritou nižší než priorita pravidla pro odepření veškerého přístupu k internetu.


   Odchozí pravidla zabezpečení budou vypadat takto: 

   ![Odchozí připojení s druhým vyvažovačem zatížení s veřejnou IP adresou](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Další informace o skupinách zabezpečení sítě Azure najdete v [tématu Skupiny zabezpečení ](https://docs.microsoft.com/azure/virtual-network/security-overview). 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Azure Firewall pro odchozí připojení k Internetu

Další možností, jak dosáhnout odchozího připojení k veřejným koncovým bodům, aniž by bylo možné příchozí připojení k virtuálnímu počítači z veřejných koncových bodů, je s Azure Firewall. Azure Firewall je spravovaná služba s integrovanou vysokou dostupností a může se rozprostírat ve více zónách dostupnosti.  
Budete také muset nasadit [uživatelem definovanou trasu](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)přidruženou k podsíti, kde se nasazují virtuální počítače a nástroj pro vyrovnávání zatížení Azure, ukazující na bránu firewall Azure, abyste směrovali provoz přes Azure Firewall.  
Podrobnosti o nasazení brány Azure Firewall najdete v tématu [Nasazení a konfigurace brány Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal).  

Architektura by vypadala takto:

![Odchozí připojení k bráně Azure Firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Důležité informace

- Brána firewall Azure je cloudová nativní služba s integrovanou vysokou dostupností a podporuje zónové nasazení.
- Vyžaduje další podsíť, která musí být s názvem AzureFirewallSubnet. 
- Pokud přenos velkých datových sad odchozí virtuální sítě, kde jsou umístěny virtuální počítače SAP, do virtuálního počítače v jiné virtuální síti nebo do veřejného koncového bodu, nemusí to být nákladově efektivní řešení. Jedním z takových příkladů je kopírování velkých záloh napříč virtuálními sítěmi. Podrobnosti najdete v tématu Ceny Azure Firewall.  
- Pokud podnikové řešení brány firewall není Azure Firewall a máte požadavky na zabezpečení mít všechny odchozí provoz projít přes centralizované podnikové řešení, toto řešení nemusí být praktické.  

>[!TIP]
>Pokud je to možné, použijte [značky Service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) ke snížení složitosti pravidel azure firewall.  

### <a name="deployment-steps"></a>Kroky nasazení

1. Kroky nasazení předpokládají, že už máte pro virtuální počítače definovanou virtuální síť a podsíť.  
2. Vytvořte podsíť **AzureFirewallSubnet** ve stejné virtuální síti, kde se nasazují VMS a standardní nástroj pro vyrovnávání zatížení.  
   1. Na webu Azure Portal přejděte do virtuální sítě: Klikněte na Všechny prostředky, Vyhledejte virtuální síť, klikněte na virtuální síť, vyberte podsítě.  
   1. Klikněte na Přidat podsíť. Zadejte **AzureFirewallSubnet** jako název. Zadejte příslušný rozsah adres. Uložte.  
3. Vytvořte bránu Azure Firewall.  
   1. Na webu Azure Portal vyberte Všechny prostředky, klikněte na Přidat, Brána firewall, Vytvořit. Vyberte skupinu prostředků (vyberte stejnou skupinu prostředků, kde je virtuální síť).  
   1. Zadejte název prostředku Azure Firewall. Například **MyAzureFirewall**.  
   1. Vyberte oblast a vyberte alespoň dvě zóny dostupnosti zarovnané s zónami dostupnosti, kde se nasazují vaše virtuální počítače.  
   1. Vyberte virtuální síť, kde se nasazují virtuální počítače SAP a nástroj pro vyrovnávání zatížení Azure.  
   1. Veřejná IP adresa: Klikněte na Vytvořit a zadejte název. Například **MyFirewallPublicIP**.  
4. Vytvořte pravidlo brány Azure, které umožní odchozí připojení k určeným veřejným koncovým bodům. Příklad ukazuje, jak povolit přístup k veřejnému koncovému bodu rozhraní Azure Management API.  
   1. Vyberte Pravidla, Kolekce síťových pravidel a klikněte na Přidat kolekci síťových pravidel.  
   1. Název: **MyOutboundRule**, zadejte prioritu, vyberte akci **povolit**.  
   1. Služba: Název **AzureAPI**.  Protokol: Vyberte **libovolné**. Zdrojová adresa: zadejte rozsah pro podsíť, kde se nasazují virtuální počítače a standardní vyrovnávání zatížení například: **11.97.0.0/24**. Cílové porty: zadejte <b>*</b>.  
   1. Uložit
   1. Vzhledem k tomu, že jste stále umístěni na Azure Firewall, vyberte přehled. Poznamenejte si privátní IP adresu brány Azure Firewall.  
5. Vytvoření trasy do Brány Azure Firewall  
   1. Na portálu Azure vyberte Všechny prostředky a klikněte na Přidat, Směrovat tabulku, Vytvořit.  
   1. Zadejte název MyRouteTable, vyberte Předplatné, Skupina prostředků a Umístění (odpovídající umístění virtuální sítě a brány firewall).  
   1. Uložit  

   Pravidlo brány firewall by ![vypadalo takto: Odchozí připojení pomocí azure firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Vytvořte uživatelem definovanou trasu z podsítě virtuálních počítačů do privátní IP adresy **myAzureFirewall**.
   1. Po umístění v tabulce tras klepněte na položku Trasy. Vyberte Přidat. 
   1. Název postupu: ToMyAzureFirewall, Adresa předpona: **0.0.0.0/0**. Další typ směrování: Vyberte virtuální zařízení. Další adresa směrování: zadejte privátní IP adresu nakonfigurované brány firewall: **11.97.1.4**.  
   1. Uložit

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Použití proxy pro pacemaker volání rozhraní AZURE Management API

Pomocí proxy serveru můžete povolit volání pacemakeru do veřejného koncového bodu rozhraní API pro správu Azure.  

### <a name="important-considerations"></a>Důležité informace

  - Pokud již existuje podnikový proxy server, můžete prostřednictvím něj směrovat odchozí volání do veřejných koncových bodů. Odchozí volání veřejných koncových bodů budou procházet řídicím bodem společnosti.  
  - Ujistěte se, že konfigurace proxy umožňuje odchozí připojení k rozhraní AZURE management API:`https://management.azure.com`  
  - Ujistěte se, že je trasa z virtuálních připojení k proxy  
  - Proxy server bude zpracovávat pouze volání HTTP/HTTPS. Pokud je další potřeba provádět odchozí volání do veřejného koncového bodu přes různé protokoly (jako je RFC), bude zapotřebí alternativní řešení  
  - Proxy řešení musí být vysoce dostupné, aby se zabránilo nestabilitě v clusteru Pacemaker  
  - V závislosti na umístění proxy serveru může zavést další latence ve volání z agenta Azure Fence do rozhraní API azure managementu. Pokud je váš podnikový proxy server stále v prostorách, zatímco váš cluster Pacemaker je v Azure, změřte latenci a zvažte, zda je toto řešení vhodné pro vás  
  - Pokud již není k dispozici vysoce dostupný podnikový proxy server, nedoporučujeme tuto možnost, protože zákazníkovi by vznikly dodatečné náklady a složitost. Pokud se však rozhodnete nasadit další proxy řešení za účelem povolení odchozího připojení z kardiostimulátoru do veřejného rozhraní API Azure Management, ujistěte se, že proxy server je vysoce dostupný a latence z virtuálních počítačů na proxy server je nízká.  

### <a name="pacemaker-configuration-with-proxy"></a>Konfigurace kardiostimulátoru s proxy serverem 

Existuje mnoho různých možností Proxy k dispozici v průmyslu. Podrobné pokyny pro nasazení serveru proxy jsou mimo rozsah tohoto dokumentu. V níže uvedeném příkladu předpokládáme, že váš proxy server reaguje na **MyProxyService** a naslouchá portu **MyProxyPort**.  
Chcete-li povolit komunikaci s rozhraním API pro správu Azure, proveďte na všech uzlech clusteru následující kroky:  

1. Upravte konfigurační soubor kardiostimulátoru /etc/sysconfig/pacemaker a přidejte následující řádky (všechny uzly clusteru):

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Restartujte službu kardiostimulátoru ve **všech** uzlech clusteru.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak nakonfigurovat kardiostimulátor na SUSE v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Přečtěte si, jak nakonfigurovat kardiostimulátor v Red Hatu v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
