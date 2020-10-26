---
title: Připojení veřejného koncového bodu pro virtuální počítače Azure&Standard interního nástroje ve scénářích SAP HA
description: Připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích SAP pro vysokou dostupnost
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
ms.date: 06/16/2020
ms.author: radeltch
ms.openlocfilehash: d4d21ac0fc0f218b9168adfad3e1b2ec42092b42
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544745"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích SAP pro vysokou dostupnost

Oborem tohoto článku je popis konfigurací, které umožní odchozí připojení k veřejným koncovým bodům. Konfigurace jsou hlavně v kontextu vysoké dostupnosti s Pacemaker pro SUSE/RHEL.  

Pokud používáte Pacemaker s agentem Azure plot v řešení vysoké dostupnosti, musí mít virtuální počítače odchozí připojení k rozhraní API pro správu Azure.  
Článek obsahuje několik možností, které vám umožní vybrat možnost, která je pro váš scénář nejvhodnější.  

## <a name="overview"></a>Přehled

Při implementaci vysoké dostupnosti pro řešení SAP prostřednictvím clusteringu se [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)jedna z nezbytných součástí. Azure nabízí dvě SKU nástroje pro vyrovnávání zatížení: Standard a Basic.

Služba Azure Load Balancer úrovně Standard nabízí oproti základnímu nástroji pro vyrovnávání zatížení některé výhody. Například funguje v rámci zón dostupnosti Azure, nabízí lepší možnosti monitorování a protokolování pro snazší řešení potíží a nižší latenci. Funkce "porty vysoké dostupnosti" pokrývá všechny porty, to znamená, že už není nutné zobrazit seznam všech jednotlivých portů.  

Mezi základní a standardní SKU nástroje pro vyrovnávání zatížení Azure jsou důležité rozdíly. Jedním z nich je zpracování odchozího provozu do veřejného koncového bodu. Úplné porovnání nástroje pro vyrovnávání zatížení na úrovni Basic a Standard SKU najdete v tématu [Load Balancer porovnání skladové](../../../load-balancer/load-balancer-overview.md)položky.  
 
Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní (bez veřejné IP adresy) standardního nástroje pro vyrovnávání zatížení Azure, neexistuje odchozí připojení k veřejným koncovým bodům, pokud se neprovede další konfigurace.  

Pokud je virtuálnímu počítači přiřazená veřejná IP adresa nebo se virtuální počítač nachází v back-endu nástroje pro vyrovnávání zatížení s veřejnou IP adresou, bude mít odchozí připojení k veřejným koncovým bodům.  

Systémy SAP často obsahují citlivá podniková data. Je zřídka přijatelné pro virtuální počítače hostující systémy SAP, aby měly veřejné IP adresy. Ve stejnou chvíli existují scénáře, které by vyžadovaly odchozí připojení z virtuálního počítače do veřejných koncových bodů.  

Příklady scénářů, které vyžadují přístup k veřejnému koncovému bodu Azure, jsou:  
- Použití agenta Azure plot jako ochranného mechanismu v clusterech Pacemaker
- Azure Backup
- Azure Site Recovery  
- Použití veřejného úložiště pro opravy operačního systému
- Tok dat aplikace SAP může vyžadovat odchozí připojení k veřejnému koncovému bodu.

Pokud vaše nasazení SAP nevyžaduje odchozí připojení k veřejným koncovým bodům, nemusíte implementovat další konfiguraci. Je dostačující vytvořit interní standardní SKU Azure Load Balancer pro váš scénář s vysokou dostupností za předpokladu, že není potřeba příchozí připojení z veřejných koncových bodů.  

> [!Note]
> Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní služby pro vyrovnávání zatížení (bez veřejné IP adresy), nebude žádné odchozí připojení k Internetu, pokud se neprovede další konfigurace, která umožní směrování na veřejné koncové body.  
>Pokud virtuální počítače mají buď veřejné IP adresy, nebo už jsou ve fondu back-end služby Azure Load Balancer s veřejnou IP adresou, virtuální počítač už bude mít odchozí připojení k veřejným koncovým bodům.


Nejprve si přečtěte následující dokumenty:

* Standard Load Balancer Azure
  * [Přehled azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) – úplný přehled služby Azure Load Balancer, důležité principy, koncepty a kurzy 
  * [Odchozí připojení v Azure](../../../load-balancer/load-balancer-outbound-connections.md#scenarios) – scénáře, jak dosáhnout odchozího připojení v Azure
  * [Odchozí pravidla nástroje pro vyrovnávání zatížení](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules)– vysvětluje koncepty odchozích pravidel nástroje pro vyrovnávání zatížení a postup vytváření odchozích pravidel.
* Brána Azure Firewall
  * [Přehled Azure firewall](../../../firewall/overview.md)– přehled Azure firewall
  * [Kurz: nasazení a konfigurace Azure firewall](../../../firewall/tutorial-firewall-deploy-portal.md) – pokyny ke konfiguraci Azure Firewall prostřednictvím Azure Portal
* [Virtuální sítě – uživatelsky definovaná pravidla](../../../virtual-network/virtual-networks-udr-overview.md#user-defined) – koncepce a pravidla směrování Azure  
* [Značky služby skupin zabezpečení](../../../virtual-network/network-security-groups-overview.md#service-tags) – jak zjednodušit skupiny zabezpečení sítě a konfiguraci brány firewall pomocí značek služeb

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Další externí Standard Load Balancer Azure pro odchozí připojení k Internetu

Jedna z možností pro dosažení odchozího připojení k veřejným koncovým bodům, aniž by bylo možné povolit příchozí připojení k virtuálnímu počítači z veřejného koncového bodu, je vytvořit druhý nástroj pro vyrovnávání zatížení s veřejnou IP adresou, přidat virtuální počítače do back-endu pro druhý nástroj pro vyrovnávání zatížení a definovat pouze [odchozí pravidla](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules).  
Pomocí [skupin zabezpečení sítě](../../../virtual-network/network-security-groups-overview.md) můžete řídit veřejné koncové body, které jsou přístupné pro odchozí volání z virtuálního počítače.  
Další informace najdete v tématu Scénář 2 v dokumentu [odchozí připojení](../../../load-balancer/load-balancer-outbound-connections.md#scenarios).  
Konfigurace by vypadala takto:  

![Řízení připojení k veřejným koncovým bodům pomocí skupin zabezpečení sítě](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Důležité informace

- K zajištění odchozího připojení k veřejnému koncovému bodu a optimalizaci nákladů můžete použít jeden další veřejný Load Balancer pro více virtuálních počítačů ve stejné podsíti.  
- Pomocí [skupin zabezpečení sítě](../../../virtual-network/network-security-groups-overview.md) můžete řídit, které veřejné koncové body jsou z virtuálních počítačů dostupné. Skupinu zabezpečení sítě můžete přiřadit buď k podsíti, nebo ke každému virtuálnímu počítači. Pokud je to možné, používejte [značky služby](../../../virtual-network/network-security-groups-overview.md#service-tags) ke snížení složitosti pravidel zabezpečení.  
- Azure Load Balancer úrovně Standard s veřejnou IP adresou a odchozími pravidly umožňuje přímý přístup k veřejnému koncovému bodu. Pokud máte podnikové požadavky na zabezpečení, aby všechny odchozí přenosy procházely prostřednictvím centralizovaného podnikového řešení pro auditování a protokolování, možná nebudete moci splnit požadavek v tomto scénáři.  

>[!TIP]
>Pokud je to možné, použijte [značky služby](../../../virtual-network/network-security-groups-overview.md#service-tags) ke snížení složitosti skupiny zabezpečení sítě. 

### <a name="deployment-steps"></a>Kroky nasazení

1. Vytvořit Load Balancer  
   1. V [Azure Portal](https://portal.azure.com) klikněte na všechny prostředky, přidat a vyhledejte **Load Balancer**  
   1. Klikněte na **Vytvořit** . 
   1. Název Load Balancer **MyPublicILB**  
   1. Vyberte možnost **Public** jako typ, **Standard** as SKU.  
   1. Vyberte **vytvořit veřejnou IP adresu** a zadejte ji jako název **MyPublicILBFrondEndIP** .  
   1. Vybrat **zónu redundantní** jako zóna dostupnosti  
   1. Klikněte na tlačítko zkontrolovat a vytvořit a pak klikněte na tlačítko vytvořit.  
2. Vytvořte back-end fond **MyBackendPoolOfPublicILB** a přidejte virtuální počítače.  
   1. Vyberte virtuální síť.  
   1. Vyberte virtuální počítače a jejich IP adresy a přidejte je do fondu back-endu.  
3. [Vytváření odchozích pravidel](../../../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard%3ftabs%3doption-1-create-load-balancer-standard#create-outbound-rule-configuration). V současné době není možné vytvářet odchozí pravidla z Azure Portal. Odchozí pravidla můžete vytvořit pomocí [Azure CLI](../../../cloud-shell/overview.md?view=azure-cli-latest).  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Vytvořte pravidla skupiny zabezpečení sítě, abyste omezili přístup ke konkrétním veřejným koncovým bodům. Pokud existuje existující skupina zabezpečení sítě, můžete ji upravit. Následující příklad ukazuje, jak povolit přístup k rozhraní API pro správu Azure: 
   1. Přejít do skupiny zabezpečení sítě
   1. Klikněte na odchozí pravidla zabezpečení.
   1. Přidejte pravidlo, které **zamítne** veškerý odchozí přístup k **Internetu** .
   1. Přidejte pravidlo pro **Povolení** přístupu k **AzureCloud** s nižší prioritou, než je priorita pravidla, abyste odepřeli veškerý přístup k Internetu.


   Odchozí pravidla zabezpečení by vypadala takto: 

   ![Odchozí připojení s druhým Load Balancer s veřejnou IP adresou](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Další informace o skupinách zabezpečení sítě Azure najdete v tématu [skupiny zabezpečení ](../../../virtual-network/network-security-groups-overview.md). 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Azure Firewall pro odchozí připojení k Internetu

Další možností, jak dosáhnout odchozího připojení k veřejným koncovým bodům, aniž byste povolili příchozí připojení k virtuálnímu počítači z veřejných koncových bodů, je Azure Firewall. Azure Firewall je spravovaná služba s integrovanou vysokou dostupností a může zahrnovat víc Zóny dostupnosti.  
Budete taky muset nasadit [uživatelem definovanou trasu](../../../virtual-network/virtual-networks-udr-overview.md#custom-routes)přidruženou k podsíti, ve které jsou nasazené virtuální počítače a Azure Load Balancer, přejít na bránu Azure firewall a směrovat provoz přes Azure firewall.  
Podrobnosti o tom, jak nasadit Azure Firewall, najdete v tématu [nasazení a konfigurace Azure firewall](../../../firewall/tutorial-firewall-deploy-portal.md).  

Architektura by vypadala takto:

![Odchozí připojení s Azure Firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Důležité informace

- Azure firewall je cloudová nativní služba s integrovanou vysokou dostupností, která podporuje nasazení napříč oblastmi.
- Vyžaduje další podsíť, která musí mít název AzureFirewallSubnet. 
- Při přenosu velkých datových sad odchozích dat z virtuální sítě, kde jsou umístěné virtuální počítače SAP, k virtuálnímu počítači v jiné virtuální síti nebo k veřejnému koncovému bodu, nemusí být nákladově efektivní řešení. Takový příklad kopíruje velké zálohy mezi virtuálními sítěmi. Podrobnosti najdete v tématu Azure Firewall ceny.  
- Pokud řešení brány firewall pro podnikové sítě není Azure Firewall a máte požadavky na zabezpečení, které budou mít všechny odchozí přenosy přes centralizované podnikové řešení, toto řešení nemusí být praktické.  

>[!TIP]
>Pokud je to možné, použijte [značky služby](../../../virtual-network/network-security-groups-overview.md#service-tags) ke snížení složitosti Azure firewall pravidel.  

### <a name="deployment-steps"></a>Kroky nasazení

1. Postup nasazení předpokládá, že už máte virtuální síť a podsíť definované pro vaše virtuální počítače.  
2. Vytvořte podsíť **AzureFirewallSubnet** ve stejném Virtual Network, kde jsou nasazené virtuální počítače a Standard Load Balancer.  
   1. V Azure Portal přejděte na Virtual Network: klikněte na všechny prostředky, vyhledejte Virtual Network, klikněte na Virtual Network a vyberte podsítě.  
   1. Klikněte na Přidat podsíť. Jako název zadejte **AzureFirewallSubnet** . Zadejte odpovídající rozsah adres. Uložte.  
3. Vytvořte Azure Firewall.  
   1. V Azure Portal vyberte všechny prostředky, klikněte na Přidat, brána firewall, vytvořit. Vyberte skupinu prostředků (vyberte stejnou skupinu prostředků, kde Virtual Network).  
   1. Zadejte název prostředku Azure Firewall. Například **MyAzureFirewall** .  
   1. Vyberte oblast a vyberte aspoň dvě zóny dostupnosti zarovnané do zón dostupnosti, ve kterých jsou nasazené vaše virtuální počítače.  
   1. Vyberte svůj Virtual Network, kde se nasadí virtuální počítače SAP a služba Azure Load Balancer úrovně Standard.  
   1. Veřejná IP adresa: klikněte na vytvořit a zadejte název. Pro instanci **MyFirewallPublicIP** .  
4. Vytvořte Azure Firewall pravidlo, které povolí odchozí připojení k zadaným veřejným koncovým bodům. V tomto příkladu se dozvíte, jak povolíte přístup ke veřejnému koncovému bodu rozhraní API pro správu Azure.  
   1. Vyberte pravidla, kolekce síťových pravidel a pak klikněte na přidat kolekci pravidel sítě.  
   1. Název: **MyOutboundRule** , zadejte priority, vyberte akce **Allow** .  
   1. Služba: název **ToAzureAPI** .  Protokol: vyberte **libovolný** . Zdrojová adresa: zadejte rozsah vaší podsítě, ve kterém jsou nasazené virtuální počítače a Standard Load Balancer pro instanci: **11.97.0.0/24** . Cílové porty: zadejte <b>*</b> .  
   1. Uložit
   1. Jak jste pořád na Azure Firewall, vyberte přehled. Poznamenejte si privátní IP adresu Azure Firewall.  
5. Vytvořit trasu pro Azure Firewall  
   1. V Azure Portal vyberte všechny prostředky a pak klikněte na Přidat, směrovací tabulku, vytvořit.  
   1. Zadejte název MyRouteTable, vyberte předplatné, skupinu prostředků a umístění (které odpovídá umístění virtuální sítě a brány firewall).  
   1. Uložit  

   Pravidlo brány firewall by vypadalo takto: ![ diagram, který ukazuje, jak by brána firewall vypadala jako.](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Vytvořte uživatelsky definovanou trasu z podsítě vašich virtuálních počítačů do privátní IP adresy **MyAzureFirewall** .
   1. V případě, že jste umístění umístili do směrovací tabulky, klikněte na trasy. Vyberte Přidat. 
   1. Název trasy: ToMyAzureFirewall, předpona adresy: **0.0.0.0/0** . Typ dalšího segmentu směrování: vyberte virtuální zařízení. Adresa dalšího segmentu směrování: zadejte privátní IP adresu brány firewall, kterou jste nakonfigurovali: **11.97.1.4** .  
   1. Uložit

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Volání rozhraní API pro správu Azure pomocí proxy serveru Pacemaker

Proxy server můžete použít k povolení volání Pacemaker do veřejného koncového bodu rozhraní API pro správu Azure.  

### <a name="important-considerations"></a>Důležité informace

  - Pokud již existuje podnikový proxy server, můžete směrovat odchozí volání na veřejné koncové body. Odchozí volání veřejných koncových bodů procházejí podnikovým řídicím bodem.  
  - Ujistěte se, že konfigurace proxy serveru umožňuje odchozí připojení k rozhraní API pro správu Azure: `https://management.azure.com` a `https://login.microsoftonline.com`  
  - Ujistěte se, že existuje trasa z virtuálních počítačů do proxy serveru.  
  - Proxy bude zpracovávat pouze volání HTTP/HTTPS. Pokud je potřeba provést odchozí volání do veřejného koncového bodu přes různé protokoly (například RFC), bude potřeba alternativní řešení.  
  - Řešení proxy musí být vysoce dostupné, aby nedošlo k nestabilitě v clusteru Pacemaker.  
  - V závislosti na umístění proxy serveru může způsobit další latenci v voláních z agenta Azure plot do rozhraní API pro správu Azure. Pokud je váš podnikový proxy stále v místním prostředí, cluster Pacemaker je v Azure, latence měření a zvážení, pokud je toto řešení vhodné pro vás.  
  - Pokud na svém místě ještě nemáte vysoce dostupný podnikový proxy server, nedoporučujeme tuto možnost, protože zákazník by vyvolal další náklady a složitost. Pokud se ale rozhodnete nasadit další řešení proxy serveru, aby bylo možné povolit odchozí připojení z Pacemaker do veřejného rozhraní API pro správu Azure, ujistěte se, že je proxy server vysoce dostupný a že latence z virtuálních počítačů na proxy server je nízká.  

### <a name="pacemaker-configuration-with-proxy"></a>Konfigurace Pacemaker s proxy serverem 

V odvětví je k dispozici celá řada různých možností proxy serveru. Podrobné pokyny pro nasazení proxy serveru jsou mimo rozsah tohoto dokumentu. V následujícím příkladu předpokládáme, že váš proxy server reaguje na **MyProxyService** a naslouchá na portu **MyProxyPort** .  
Pokud chcete, aby služba Pacemaker komunikovala s rozhraním API pro správu Azure, proveďte následující kroky na všech uzlech clusteru:  

1. Upravte konfigurační soubor Pacemaker/etc/sysconfig/Pacemaker a přidejte následující řádky (všechny uzly clusteru):

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Restartujte službu Pacemaker na **všech** uzlech clusteru.  
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

## <a name="other-solutions"></a>Další řešení

Pokud je odchozí provoz směrován přes bránu firewall jiného výrobce:

- Pokud používáte Azure plot agent, ujistěte se, že konfigurace brány firewall umožňuje odchozí připojení k rozhraní API pro správu Azure: `https://management.azure.com` a `https://login.microsoftonline.com`   
- Pokud používáte infrastrukturu aktualizace veřejného cloudu Azure SUSE k instalaci aktualizací a oprav, přečtěte si téma [infrastruktura aktualizace veřejného cloudu azure 101](https://suse.com/c/azure-public-cloud-update-infrastructure-101/) .

## <a name="next-steps"></a>Další kroky

* [Naučte se konfigurovat Pacemaker v SUSE v Azure](./high-availability-guide-suse-pacemaker.md)
* [Informace o tom, jak nakonfigurovat Pacemaker na Red Hat v Azure](./high-availability-guide-rhel-pacemaker.md)