---
title: Hybridní cloud připojení nakonfigurovat Azure a Azure zásobníku | Microsoft Docs
description: Zjistěte, jak nakonfigurovat připojení hybridního cloudu s Azure a Azure zásobníku.
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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 048e2636aabe406728c8fe1b93ef861f13346256
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Kurz: hybridní cloud připojení nakonfigurujte Azure a Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Přístup k prostředkům s zabezpečení v globální Azure a Azure zásobníku pomocí vzoru hybridní připojení. 

V tomto kurzu vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Zachovat data místně pro ochranu osobních údajů nebo zákonné požadavky, ale mají přístup k globální prostředky Azure.
> - Zachovat starší verze systému při použití nasazení škálovat cloudové aplikace a prostředky v globální Azure.

## <a name="prerequisites"></a>Požadavky

Několik součásti jsou nutné k vytvoření hybridního nasazení připojení a může trvat delší dobu přípravy. 

Partnerem OEM nebo Hardware Azure může nasazovat provozní zásobník Azure, ale všichni uživatelé mohou ASDK. Operátor zásobník Azure musí také nasadit App Service, vytvářet plány a nabízí, vytvořit odběr klienta a přidat bitovou kopii systému Windows Server 2016. 

Pokud již máte některé z těchto součástí, ujistěte se, že splňují požadavky před zahájením.

Toto téma také předpokládá, že máte některé informace o Azure a Azure zásobníku. Pokud chcete další informace, než budete pokračovat, ujistěte se, že jste začínat tato témata:
 - [Úvod do Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Klíčové koncepty Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure
 - Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
 - Vytvoření [webová aplikace](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) v Azure. Poznamenejte si nové URL webové aplikace, jako je později použít.

### <a name="azure-stack"></a>Azure Stack
 - Použít provozním zásobník Azure nebo nasadit Azure zásobníku Development Kit vedou následující odkazy:
    - https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 
    - Instalace obvykle trvá několik hodin k dokončení, takže Plánujte odpovídajícím způsobem.
 - Nasazení [služby App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) služby PaaS do protokolů Azure. 
 - [Vytvoření plánu nebo nabídky](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) v prostředí Azure zásobníku. 
 - [Vytvoření odběru klienta](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) v prostředí Azure zásobníku. 


### <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

 - Ověřte, že máte veřejnou IPv4 adresu pro vaše zařízení VPN. Tato IP adresa nesmí být umístěná za překladem adres (NAT).
 - Zkontrolujte, zda že všechny prostředky nasazených ve stejném oblasti nebo umístění.

#### <a name="example-values"></a>Příklady hodnot

V příkladech v tomto článku se používají následující hodnoty. Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo odkazovat na ně, abyste lépe pochopili, příklady v tomto článku. Další celkové informace o nastavení VPN Gateway najdete v tématu [Informace o nastavení služby VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specifikace připojení:
 - **Typ sítě VPN**: založené na směrování
 - **Typ připojení**: Site-to-site (IPsec)
 - **Typ brány**: sítě VPN
 - **Název připojení Azure**: Azure-Gateway-AzureStack-S2SGateway (bude portálu automatické vyplňování tuto hodnotu)
 - **Název připojení Azure zásobníku**: AzureStack-Gateway-Azure-S2SGateway (bude portálu automatické vyplňování tuto hodnotu)
 - **Sdílený klíč**: všechny kompatibilní s hardware sítě VPN, se shodnými hodnotami na obou stranách připojení
 - **Předplatné**: žádné preferované předplatného
 - **Skupina prostředků**: Test-Infra

| Připojení Azure nebo Azure zásobníku | Název | Podsíť | IP adresa |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Virtuální síť Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Virtuální síť Azure zásobníku | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Brána virtuální sítě Azure | Brány Azure |  |  |
| Brána virtuální sítě Azure zásobníku | AzureStack brány |  |  |
| Azure veřejnou IP adresu | Azure GatewayPublicIP |  | Určit při vytváření |
| Veřejná IP adresa Azure zásobníku | AzureStack GatewayPublicIP |  | Určit při vytváření |
| Brána místní sítě Azure. | AzureStack S2SGateway<br>   10.100.100.0/23 |  | Veřejná IP adresa hodnotu Azure zásobníku |
| Brána místní sítě Azure zásobníku | Azure S2SGateway<br>10.100.102.0/23 |  | Hodnota Azure veřejnou IP adresu |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Vytvoření virtuální sítě v globální Azure a Azure zásobníku

> [!Note]  
> Je nutné zajistit, že je bez překrytí čísel IP adresy v Azure nebo Azure zásobníku adresní prostory virtuální sítě. 

K vytvoření virtuální sítě v modelu nasazení Resource Manager pomocí portálu Azure. Použijte [ukázkové hodnoty](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values), pokud tento postup používáte v rámci kurzu. Pokud se nejedná o kurz, nezapomeňte ukázkové hodnoty nahradit svými. 

1. V prohlížeči přejděte na web [Azure Portal](http://portal.azure.com/) a přihlaste se pomocí svého účtu Azure.
2. Klikněte na **Vytvořit prostředek**. V **vyhledávání na webu marketplace** zadejte `virtual network`". Vyhledejte **virtuální síť** ze seznamu vrácených a otevřete **virtuální sítě** stránky.
3. U dolního okraje stránky virtuální sítě z **vybrat model nasazení** vyberte **Resource Manager**a potom vyberte **vytvořit**. Tím otevřete stránku Vytvořit virtuální síť.
4. Na stránce **Vytvořit virtuální síť** nakonfigurujte nastavení virtuální sítě. Po vyplnění polí se červený vykřičník změní na zelenou značku zaškrtnutí, pokud jsou znaky zadané do pole platné.
5. Opakujte tyto kroky ze **portál klienta** Azure zásobníku.

## <a name="add-a-gateway-subnet"></a>Přidání podsítě brány

Před připojením virtuální sítě k bráně musíte nejdříve vytvořit podsíť brány pro virtuální síť, ke které se chcete připojit. Služby brány používají IP adresy určené v podsíti brány.

Na [portálu](http://portal.azure.com/) přejděte na virtuální síť Resource Manageru, pro kterou chcete vytvořit bránu virtuální sítě.

1. V **nastavení** část stránky virtuální sítě, vyberte **podsítě** rozšířit **podsítě** stránky.
2. Na **podsítě** vyberte **+ podsíť brány** otevřete **přidat podsíť** stránky.

    ![](media/azure-stack-solution-hybrid-connectivity/image4.png)

3. **Název** podsítě se automaticky vyplní hodnotou GatewaySubnet. Tato hodnota je vyžadována pro Azure rozpoznat podsítě jako podsíť brány. Upravit automaticky vyplněné **rozsahu adres** hodnoty tak, aby odpovídaly vaše požadavky na konfiguraci, pak vybrat **OK** v dolní části stránky vytvořte podsíť.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Vytvoření brány virtuální sítě Azure a Azure zásobníku

1. Na levé straně stránky portálu, vyberte + a zadejte brány virtuální sítě v hledání. V **výsledky**, vyhledejte a vyberte **Brána virtuální sítě**.
2. V dolní části **Brána virtuální sítě** vyberte **vytvořit**. Tím otevřete stránku **Vytvořit bránu virtuální sítě**.
3. Na **vytvořit bránu virtuální sítě** stránky, zadejte hodnoty pro bránu virtuální sítě podle popisu v ukázkové hodnoty plus další hodnoty, které jsou popsané dál.
    - **Skladová položka**: základní
    - **Virtuální síť**: vyberte virtuální síť, který jste vytvořili dříve. Automaticky vybere podsíť brány, kterou jste vytvořili dříve. 
    - **První konfigurace protokolu IP**: Toto je veřejná IP adresa brány. 
        - Klikněte na tlačítko **konfigurace IP brány vytvořit** a bude to trvat, abyste **zvolte veřejnou IP adresu** stránky.
        - Klikněte na tlačítko **+ vytvořit nový** otevřete **vytvoření veřejné IP adresy** stránky.
        - Zadejte **název** pro svoji veřejnou IP adresu. Nechte SKU jako **základní**, pak vyberte **OK** v dolní části této stránky a uložit provedené změny.

    > [!Note]  
    > Brána sítě VPN aktuálně podporuje jenom dynamické veřejné IP adresy přidělení. To ale neznamená, že se IP adresa po přiřazení k vaší bráně VPN bude měnit. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

4. Ověřte nastavení. 
5. Kliknutím na **Vytvořit** zahajte proces vytváření brány VPN. Nastavení se ověří a na řídicím panelu se zobrazí dlaždice Nasazování brány virtuální sítě. Vytváření brány může trvat až 45 minut. K zobrazení stavu dokončení může být nutné obnovit stránku portálu.

    Po vytvoření brány můžete zobrazením virtuální sítě na portálu zobrazit IP adresu, která jí byla přiřazena. Brána se zobrazí jako připojené zařízení. Můžete vybrat připojené zařízení (bránu virtuální sítě) Chcete-li zobrazit další informace.
6. Opakujte tyto kroky nasazení Azure zásobníku.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Vytvoření brány místní sítě v Azure a Azure zásobníku

Brána místní sítě obvykle odkazuje na vaše místní umístění. Dáte název, podle kterého Azure nebo Azure zásobníku provést na ni odkazuje, a potom zadejte IP adresu místního zařízení VPN ke které se vytvoří připojení webu. Zadáte také předpony IP adres, které se budou přes bránu VPN směrovat do zařízení VPN. Předpony adres, které zadáte, jsou předpony ve vaší místní síti. Pokud se změní vaše místní síť nebo potřebujete změnit veřejnou IP adresu pro zařízení VPN, můžete hodnoty snadno aktualizovat později.

1. Na portálu, vyberte **+ vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **brány místní sítě**, stiskněte **Enter** pro vyhledávání. Zobrazí se seznam výsledků. Klikněte na tlačítko **brány místní sítě**, vyberte **vytvořit** tlačítko Otevřít **vytvořit bránu místní sítě** stránky.
3. Na **stránka brány místní sítě vytvořit**, zadejte hodnoty pro bránu místní sítě podle popisu v našem příkladu hodnoty plus další hodnoty níže uvedených.
    - **IP adresa**: Toto je veřejnou IP adresu zařízení VPN, který chcete pro připojení k Azure nebo Azure zásobníku. Zadejte platnou veřejnou IP adresu. IP adresa nemůže být za serverem NAT a musí být dostupná pro Azure. Pokud momentálně nemáte IP adresu, můžete použít hodnoty uvedené v příkladu, ale budete se muset vrátit zpět a nahradit zástupnou IP adresu veřejnou IP adresou svého zařízení VPN. V opačném případe se Azure nebude moci připojit.
    - **Adresní prostor** odkazuje na rozsahy adres sítě, kterou tato místní síť představuje. Můžete přidat více různých rozsahů adres. Zkontrolujte, že se zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se budete chtít připojit. Azure bude směrovat zadaný rozsah adres na místní IP adresu zařízení VPN. Tady použijte vlastní hodnoty, pokud se chcete připojit k místní lokalitě, nikoli hodnoty v příkladu.
    - **Konfigurace nastavení protokolu BGP**: používejte jenom v případě, že konfigurace protokolu BGP. V jiných případech tuto možnost nevybírejte.
    - **Předplatné**: Ověřte, že se zobrazuje správné předplatné.
    - **Skupina prostředků**: Vyberte skupinu prostředků, který chcete použít. Můžete vytvořit novou skupinu prostředků, nebo vybrat skupinu prostředků, kterou jste už vytvořili.
    - **Umístění**: Vyberte umístění, které tento objekt se vytvoří v. Můžete vybrat stejné umístění, ve kterém se nachází vaše virtuální síť, ale není to povinné.
4. Po dokončení zadání hodnoty, vyberte **vytvořit** tlačítko v dolní části stránky vytvořte bránu místní sítě.
5. Opakujte tyto kroky nasazení Azure zásobníku.

## <a name="configure-your-connection"></a>Nakonfigurujte připojení

Připojení Site-to-Site k místní síti vyžadují zařízení VPN. V tomto kroku nakonfigurujete zařízení VPN, označuje jako připojení. Při konfiguraci připojení, budete potřebovat následující:
    - Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáváte při vytváření připojení VPN Site-to-Site. V našich ukázkách používáme základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
    - Veřejnou IP adresu vaší brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Najít veřejnou IP adresu brány VPN pomocí portálu Azure, přejděte do brány virtuální sítě a potom vyberte název vaší brány.
Vytvořte připojení VPN typu Site-to-Site mezi bránou virtuální sítě a místním zařízením VPN.
1. Na portálu, vyberte **+ vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **připojení**, stiskněte **Enter** pro vyhledávání. Zobrazí se seznam výsledků. Klikněte na tlačítko **připojení**, pak klikněte na tlačítko Vytvořit otevřete **vytvořit připojení** stránky.
3. Na **vytvořit připojení** nakonfigurujte hodnoty pro připojení.
     - Základní informace:
        1. **Typ připojení**: vyberte Site-to-site (IPSec).
        2. **Skupina prostředků**: (vyberte testovací skupinu prostředků)
     - Nastavení:
        1. **Brána virtuální sítě**: Vyberte bránu virtuální sítě, který jste vytvořili dříve.
        2. **Bránu místní sítě**: Vyberte bránu místní sítě jste vytvořili dříve. 
        3. **Název připojení**: to automaticky naplnit hodnotami z dvě brány. 
        4. **Sdílený klíč**: hodnota musí odpovídat hodnotě, kterou používáte pro vaše místní zařízení VPN. V příkladu se používá abc123, ale můžete (a měli byste) používat něco složitějšího. Důležité je, aby hodnota, kterou zde zadáte, byla stejná jako hodnota, kterou zadáte při konfiguraci zařízení VPN.
    - Hodnoty položek **Předplatné**, **Skupina prostředků** a **Umístění** jsou pevné.
4. Vytvořte připojení kliknutím na **OK**. Zobrazí se vytváří se připojení flash na obrazovce.
5. Můžete zobrazit připojení v ** stránky připojení brány virtuální sítě. Stav přejde z **neznámé** k **připojení**a poté na **úspěšné**.

## <a name="next-steps"></a>Další postup

- Další informace o vzorů cloudu Azure, najdete v části [vzory návrhu cloudové](https://docs.microsoft.com/azure/architecture/patterns).
