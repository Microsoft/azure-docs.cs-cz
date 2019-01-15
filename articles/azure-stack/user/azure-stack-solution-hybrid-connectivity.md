---
title: Konfigurace připojení k hybridní cloud s Azure a Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat připojení k hybridní cloud s Azure a Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 19c5e530822d1d9c2b4ec815b37b3373e8e39b02
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54305974"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Kurz: Konfigurace připojení k hybridní cloud s Azure a Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Přístup k prostředkům se zabezpečením v globální Azure a použití vzoru připojení k hybridní službě Azure Stack.

V tomto kurzu vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Zachovejte data místně splňovat zákonné požadavky nebo ochrany osobních údajů, ale mají přístup ke globální prostředky Azure.
> - Zachovat starší verze systému při používání nasazení škálovaných cloudových aplikací a prostředků v globální Azure.

> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí a povolení ten jediný hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace kdekoli.  
> 
> Dokument White Paper [aspekty návrhu pro hybridní aplikace](https://aka.ms/hybrid-cloud-applications-pillars) kontroly pro navrhování, nasazování a provozování hybridní pilířů kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) aplikace. Aspekty návrhu při optimalizaci návrhu hybridní aplikace, minimalizovat problémy v produkčním prostředí.


## <a name="prerequisites"></a>Požadavky

Několik součásti jsou potřeba k vytvoření hybridního připojení k nasazení. Čas chcete připravit, některé z těchto součástí bude trvat, budete muset podle toho naplánujte.

**Azure Stack**

Výrobce OEM nebo Hardware partnerem Azure můžete nasadit produkčního prostředí Azure Stack, a všichni uživatelé můžou nasazovat Azure Stack Development Kit (ASDK).

**Komponenty služby Azure Stack**

Operátor Azure stacku musí nasadit službu App Service, vytvořte plány a nabídky, vytvořte předplatné tenanta a přidejte image Windows serveru 2016. Pokud už máte některé z těchto komponent, ujistěte se, že splňují požadavky před zahájením tohoto kurzu.

V tomto kurzu se předpokládá, že máte některé základní znalosti o Azure a Azure Stack. Další informace před zahájením tohoto kurzu, přečtěte si následující články:

 - [Úvod do Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Klíčové koncepty služby Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Pokud ještě nemáte předplatné Azure, vytvořte si  [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)  před tím, než začnete.
 - Vytvoření [webová aplikace](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) v Azure. Poznamenejte si adresu URL webové aplikace, protože ho budete potřebovat v kurzu.

### <a name="azure-stack"></a>Azure Stack

 - Použití služby Azure Stack v produkčním prostředí nebo nasazení Azure Stack Development Kit z https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Nasazení ASDK může trvat až 7 hodin, tak podle toho naplánujte.

 - Nasazení [služby App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) služeb PaaS do služby Azure Stack.
 - [Vytvořit plány a nabídky](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) v prostředí Azure Stack.
 - [Vytvoření tenanta předplatného](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) v rámci prostředí Azure Stack.

### <a name="before-you-begin"></a>Před zahájením

Ověřte, že splňujete následující kritéria ještě před zahájením konfigurace hybridní připojení ke cloudu:

 - Budete potřebovat veřejnou adresu IPv4 přístupnou zvenčí pro vaše zařízení VPN. Tato IP adresa nesmí být umístěné za službou NAT.
 - Všechny prostředky jsou nasazené ve stejné oblasti nebo umístění.

#### <a name="tutorial-example-values"></a>Kurz ukázkové hodnoty

Příklady v tomto kurzu použijte následující hodnoty. Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů. Další informace o službě VPN Gateway nastavení Obecné naleznete v tématu [o nastavení služby VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specifikace připojení:

 - **Typ sítě VPN**: Trasové
 - **Typ připojení**: Site-to-site (IPsec)
 - **Typ brány**: Síť VPN
 - **Název připojení Azure**: Azure-Gateway-AzureStack-S2SGateway (na portálu vyplní tato hodnota)
 - **Název připojení služby Azure Stack**: AzureStack-Gateway-Azure-S2SGateway (na portálu vyplní tato hodnota)
 - **Sdílený klíč**: Žádné kompatibilní s hardwarem sítě VPN se shodnými hodnotami na obou stranách připojení
 - **Předplatné**: Žádné preferované předplatné
 - **Skupina prostředků**: Test-Infra

Síť a podsíť IP adres:

| Připojení Azure/Azure Stack | Název | Podsíť | IP adresa |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Virtuální síť Azure Stack | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure Virtual Network Gateway | Azure Gateway |  |  |
| Brána virtuální sítě Azure Stack | AzureStack-Gateway |  |  |
| Azure Public IP | Azure-GatewayPublicIP |  | Určí při vytvoření |
| Veřejná IP adresa Azure Stack | AzureStack-GatewayPublicIP |  | Určí při vytvoření |
| Brány místní sítě Azure | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Hodnota Azure stacku veřejnou IP adresu |
| Brána místní sítě Azure Stack | Azure-S2SGateway<br>10.100.102.0/23 |  | Hodnota Azure veřejné IP adresy |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Vytvoření virtuální sítě v globálním Azure a Azure Stack

Následujícím postupem vytvoření virtuální sítě pomocí portálu. Můžete je použít [ukázkové hodnoty](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) jako kurz při použití v tomto článku. Ale pokud používáte v tomto článku Konfigurace produkčním prostředí, nahraďte příklady nastavení s vlastními hodnotami.

> [!IMPORTANT]
> Ujistěte se, že není k překrytí IP adres v Azure nebo ve službě Azure Stack adresní prostory virtuální sítě.

Vytvoření virtuální sítě v Azure:

1. Pomocí svého prohlížeče pro připojení k [webu Azure portal](http://portal.azure.com/) a přihlaste se pomocí svého účtu Azure.
2. Vyberte **vytvořit prostředek**. V **Hledat na marketplace** zadejte `virtual network`". Najít **virtuální síť** v seznamu výsledků a pak vyberte **virtuální sítě**.
3. Z **vybrat model nasazení** vyberte **Resource Manageru**a pak vyberte **vytvořit**.
4. Na **vytvořit virtuální síť**, nakonfigurujte nastavení virtuální sítě. Povinná pole názvy mají předponu červenou hvězdičkou.  Při zadání platnou hodnotu hvězdičky se změní na zelená značka zaškrtnutí.

Vytvoření virtuální sítě ve službě Azure Stack:

* Opakujte předchozí kroky (1 – 4) pomocí služby Azure Stack **portál pro klienty**.

## <a name="add-a-gateway-subnet"></a>Přidání podsítě brány

Před připojením virtuální sítě k bráně, je potřeba vytvořit podsíť brány pro virtuální síť, kterou chcete připojit k. Služby brány používají IP adresy, kterou zadáte v podsíti brány.

V [webu Azure portal](http://portal.azure.com/), přejděte na virtuální síť Resource Manageru, kde chcete vytvořit bránu virtuální sítě.

1. Vyberte virtuální síť a otevřete **virtuální síť** stránky.
2. V **nastavení**vyberte **podsítě**.
3. Na **podsítě** stránce **+ podsíť brány** otevřít **přidat podsíť** stránky.

    ![Přidání podsítě brány](media/azure-stack-solution-hybrid-connectivity/image4.png)

4.  **Název** pro podsítě se automaticky vyplní hodnotou "GatewaySubnet". Tato hodnota je vyžadována pro Azure podsíť rozpoznala jako podsíť brány.
5. Změnit **rozsah adres** hodnoty, které jsou k dispozici odpovídaly požadavkům vaší konfigurace, a potom vyberte **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Vytvořit bránu virtuální sítě v Azure a Azure Stack

Pomocí následujících kroků a vytvořte bránu virtuální sítě v Azure.

1. Na levé straně stránky portálu vyberte **+**  a do vyhledávacího pole zadejte "Brána virtuální sítě".
2. V **výsledky**vyberte **Brána virtuální sítě**.
3. V **Brána virtuální sítě**vyberte **vytvořit** otevřít **vytvořit bránu virtuální sítě** stránky.
4. Na **vytvořit bránu virtuální sítě**, zadejte hodnoty pro bránu sítě, jak je znázorněno v **hodnoty v tomto příkladu**a následující další hodnoty:

    - **Skladová položka**: základní
    - **Virtual Network**: Výběr virtuální sítě, které jste vytvořili dříve. Podsíť brány, kterou jste vytvořili se vybere automaticky.
    - **První konfigurace IP adresy**:  Toto je veřejná IP adresa brány.
        - Vyberte **vytvořit konfiguraci IP adresy brány**, což vás přesměruje na **zvolte veřejnou IP adresu** stránky.
        - Vyberte **+ vytvořit novou** otevřít **vytvoření veřejné IP adresy** stránky.
        - Zadejte **název** pro veřejnou IP adresu. Jako SKU ponechte **základní**a pak vyberte **OK** uložte provedené změny.

       > [!Note]
       > VPN Gateway v současné době podporuje pouze přidělení dynamické veřejné IP adresy. Nicméně to neznamená, že změní IP adresa po přiřazení k vaší bráně VPN. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. Změna velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN nezmění IP adresu.

4. Ověřte nastavení brány.
5. Vyberte **vytvořit** a vytvořte bránu VPN. Nastavení brány se ověří a na řídicím panelu se zobrazí na dlaždici "Nasazování brány virtuální sítě".

   >[!Note]
   >Vytváření brány může trvat až 45 minut. K zobrazení stavu dokončení může být nutné obnovit stránku portálu.

    Po vytvoření brány můžete zobrazit IP adresa přiřazená zobrazením virtuální sítě na portálu. Brána se zobrazí jako připojené zařízení. Pokud chcete zobrazit další informace o bráně, vyberte zařízení.

6. Opakujte předchozí kroky (1-5) v nasazení Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Vytvořit bránu místní sítě v Azure a Azure Stack

Brána místní sítě obvykle odkazuje na vaše místní umístění. Umístění určíte název, který Azure nebo ve službě Azure Stack může odkazovat na a pak zadejte:

- IP adresa místního zařízení VPN, kterou vytváříte připojení.
- Předpony IP adres, které se budou směrovat přes bránu VPN pro zařízení VPN. Předpony adres, které zadáte, jsou předpony ve vaší místní síti.

  >[!Note]
  >Pokud vaše místní síť se změní nebo potřebujete změnit veřejnou IP adresu pro zařízení VPN, můžete snadno aktualizovat tyto hodnoty později.

1. Na portálu vyberte **+ vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **bránu místní sítě**a pak vyberte **Enter** pro hledání. Zobrazí se seznam výsledků.
3. Vyberte **bránu místní sítě**a pak vyberte **vytvořit** otevřít **vytvořit bránu místní sítě** stránky.
4. Na **vytvořit bránu místní sítě**, zadejte hodnoty brány místní sítě, pomocí našich **hodnoty v tomto příkladu**. Zahrnout následující další hodnoty.

    - **IP adresa**: Toto je veřejnou IP adresu zařízení VPN, který chcete připojit k Azure nebo Azure Stack. Zadejte platnou veřejnou IP adresu, která není za službou NAT, takže Azure přístup k adrese. Pokud momentálně nemáte IP adresu, můžete použít hodnotu jako v příkladu jako zástupný znak, ale budete se muset vrátit zpět a nahradit zástupnou hodnotu s veřejnou IP adresu zařízení VPN. Azure se nemůže připojit k zařízení, dokud je zadat platnou adresu.
    - **Adresní prostor**: Toto je rozsah adres pro síť, která tato místní síť představuje. Můžete přidat více různých rozsahů adres. Ujistěte se, že rozsahy nepřekrývají s rozsahy jiných sítí, které chcete připojit k. Azure bude směrovat zadaný rozsah adres na místní IP adresu zařízení VPN. Použijte vlastní hodnoty, pokud se chcete připojit k místní lokalitě, nikoli příkladem hodnoty.
    - **Konfigurovat nastavení protokolu BGP**: Používejte jenom při konfiguraci BGP. V jiných případech tuto možnost nevybírejte.
    - **Předplatné**: Ověřte, že se zobrazuje správné předplatné.
    - **Skupina prostředků**: Vyberte skupinu prostředků, kterou chcete použít. Můžete buď vytvořit novou skupinu prostředků nebo vyberte ten, který jste vytvořili.
    - **Umístění**: Vyberte umístění, které tento objekt se vytvoří v. Můžete chtít vybrat stejné umístění, která se nachází vaše virtuální síť v, ale není potřeba udělat.
5. Když dokončíte zadáním požadované hodnoty, vyberte **vytvořit** a vytvořte bránu místní sítě.
6. Opakujte tyto kroky (1-5) v nasazení Azure Stack.

## <a name="configure-your-connection"></a>Konfigurace připojení

Připojení Site-to-Site k místní síti vyžadují zařízení VPN. Zařízení VPN, které jste nakonfigurovali, se označuje jako připojení. Konfigurace připojení, budete potřebovat:

- Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáváte při vytváření připojení VPN Site-to-Site. V našich ukázkách používáme základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
- Veřejnou IP adresu vaší brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Chcete-li najít veřejnou IP adresu brány VPN pomocí webu Azure portal, přejděte do brány virtuální sítě a pak vyberte název brány.

Použijte následující postup k vytvoření připojení VPN typu Site-to-Site mezi bránou virtuální sítě a vaše místní zařízení VPN.

1. Na webu Azure Portal, vyberte **+ vytvořit prostředek**.
2. Vyhledejte **připojení**.
3. V **výsledky**vyberte **připojení**.
4. Na **připojení**vyberte **vytvořit**.
5. Na **vytvořit připojení**, nakonfigurujte následující nastavení:

    - **Typ připojení**: Vyberte Site-to-site (IPSec).
    - **Skupina prostředků**: Vyberte testovací skupinu prostředků.
    - **Virtual Network Gateway**: Vyberte bránu virtuální sítě, kterou jste vytvořili.
    - **Brána místní sítě**: Vyberte bránu místní sítě jste vytvořili.
    - **Název připojení**: To se automaticky vyplní pomocí hodnot z dvě brány.
    - **Sdílený klíč**: Tato hodnota musí odpovídat hodnotě, kterou používáte pro vaše místní zařízení VPN. Tomto příkladu používá "abc123", ale které můžete (a měli byste) používat něco složitějšího. Důležité je, že tato hodnota musí být stejnou hodnotu, kterou zadáte při konfiguraci zařízení VPN.
    - Hodnoty pro **předplatné**, **skupiny prostředků**, a **umístění** jsou opraveny.

6. Vyberte **OK** a vytvořte připojení.

Zobrazí se připojení v **připojení** stránku brány virtuální sítě. Stav se změní z *neznámý* k *připojení*a potom do *Succeeded*.

## <a name="next-steps"></a>Další postup

- Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
