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
ms.date: 05/25/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 72c5c4b0f0ab752bb02e6bee7cd038afca44ee1b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605191"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Kurz: hybridní cloud připojení nakonfigurujte Azure a Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Přístup k prostředkům s zabezpečení v globální Azure a Azure zásobníku pomocí vzoru hybridní připojení.

V tomto kurzu vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Zachovat data místně splňovat zákonné požadavky nebo ochranu osobních údajů, ale mají přístup k globální prostředky Azure.
> - Zachovat starší verze systému při použití nasazení škálovat cloudové aplikace a prostředky v globální Azure.

## <a name="prerequisites"></a>Požadavky

Několik součásti jsou potřeba k vytvoření hybridního nasazení připojení. Doba přípravy, některé z těchto součástí bude trvat, budete muset podle toho naplánovat.

**Azure Stack**

Výrobce OEM nebo hardwaru partnerem Azure můžete nasadit provozní zásobník Azure a všech uživatelů můžete nasadit Azure zásobníku Development Kit (ASDK).

**Azure součásti zásobníku**

Operátor zásobník Azure musí nasadit App Service, vytvářet plány a nabízí, vytvořit odběr klienta a přidat bitovou kopii systému Windows Server 2016. Pokud již máte některé z těchto součástí, ujistěte se, že splňují požadavky před zahájením tohoto kurzu.

Tento kurz předpokládá, že máte některé základní znalosti o Azure a Azure zásobníku. Další informace před zahájením tohoto kurzu, najdete v následujících článcích:

 - [Úvod do Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Klíčové koncepty Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
 - Vytvoření [webová aplikace](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) v Azure. Poznamenejte si adresu URL webové aplikace, protože ho budete potřebovat v kurzu.

### <a name="azure-stack"></a>Azure Stack

 - Použít provozním zásobník Azure nebo nasadit Development Kit zásobník Azure z https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Nasazení ASDK může trvat až 7 hodin, proto podle toho naplánovat.

 - Nasazení [služby App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) služby PaaS do protokolů Azure.
 - [Vytvoření plánu a nabídky](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) v prostředí Azure zásobníku.
 - [Vytvoření odběru klienta](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) v prostředí Azure zásobníku.

### <a name="before-you-begin"></a>Než začnete

Ověřte, ještě před zahájením konfigurace hybridní připojení cloudu splňovat následující kritéria:

 - Musíte zvenčí veřejnou adresu IPv4 pro vaše zařízení VPN. Tato IP adresa nesmí být umístěné za adres (NAT)
 - Všechny prostředky nasazených ve stejném oblasti nebo umístění.

#### <a name="tutorial-example-values"></a>Kurz ukázkové hodnoty

V příkladech v tomto kurzu použijte následující hodnoty. Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo na ně odkazují lépe porozumět příklady. Další celkové informace o nastavení VPN Gateway najdete v tématu [Informace o nastavení služby VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specifikace připojení:

 - **Typ sítě VPN**: založené na směrování
 - **Typ připojení**: Site-to-site (IPsec)
 - **Typ brány**: sítě VPN
 - **Název připojení Azure**: Azure-Gateway-AzureStack-S2SGateway (bude portálu automatické vyplňování tuto hodnotu)
 - **Název připojení Azure zásobníku**: AzureStack-Gateway-Azure-S2SGateway (bude portálu automatické vyplňování tuto hodnotu)
 - **Sdílený klíč**: všechny kompatibilní s hardware sítě VPN, se shodnými hodnotami na obou stranách připojení
 - **Předplatné**: žádné preferované předplatného
 - **Skupina prostředků**: Test-Infra

Síť a podsíť IP adresy:

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

Pomocí následujících kroků pro vytvoření virtuální sítě pomocí portálu. Pomocí těchto [ukázkové hodnoty](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) Pokud používáte tento článek jako kurzu. Ale pokud používáte v tomto článku Konfigurace provozním prostředí, nahraďte příklad nastavení vlastními hodnotami.

> [!IMPORTANT]
> Je nutné zajistit, že není překrývají IP adres v Azure nebo Azure zásobníku adresní prostory virtuální sítě.

Vytvoření virtuální sítě v Azure:

1. Použít prohlížeč pro připojení k [portál Azure](http://portal.azure.com/) a přihlaste se pomocí účtu Azure.
2. Vyberte **vytvořit prostředek**. V **vyhledávání na webu marketplace** zadejte `virtual network`". Najít **virtuální síť** v seznamu výsledků a potom vyberte **virtuální sítě**.
3. Z **vybrat model nasazení** vyberte **Resource Manager**a potom vyberte **vytvořit**.
4. Na **vytvořit virtuální síť**, nakonfigurujte nastavení virtuální sítě. Povinná pole názvy mají předponu červená hvězdička.  Když zadáte platnou hodnotu, změny se tedy hvězdička zelená značka zaškrtnutí.

Vytvoření virtuální sítě v Azure zásobníku:

* Opakujte předchozí kroky (1-4) pomocí Azure zásobníku **portál klienta**.

## <a name="add-a-gateway-subnet"></a>Přidání podsítě brány

Před připojením virtuální sítě k bráně, je potřeba vytvořit podsíť brány pro virtuální síť, která chcete připojit. Služby brány pomocí IP adresy, které zadáte v podsíť brány.

V [portál Azure](http://portal.azure.com/), přejděte do virtuální sítě Resource Manager, kde chcete vytvořit bránu virtuální sítě.

1. Vyberte virtuální síť, otevřete **virtuální síť** stránky.
2. V **nastavení**, vyberte **podsítě**.
3. Na **podsítě** vyberte **+ podsíť brány** otevřete **přidat podsíť** stránky.

    ![Přidání podsítě brány](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. **Název** pro podsíť je automaticky vyplněno hodnotu "GatewaySubnet". Tato hodnota je vyžadována pro Azure rozpoznat podsítě jako podsíť brány.
5. Změna **rozsahu adres** hodnoty, které jsou k dispozici odpovídající vaše požadavky na konfiguraci a potom vyberte **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Vytvoření brány virtuální sítě Azure a Azure zásobníku

Použijte následující postup k vytvoření brány virtuální sítě v Azure.

1. Na levé straně stránky portálu, vyberte **+** a do pole vyhledávání zadejte "brány virtuální sítě".
2. V **výsledky**, vyberte **Brána virtuální sítě**.
3. V **Brána virtuální sítě**, vyberte **vytvořit** otevřete **vytvořit bránu virtuální sítě** stránky.
4. Na **vytvořit bránu virtuální sítě**, zadejte hodnoty pro bránu sítě, jak je znázorněno v **kurz ukázkové hodnoty**a následující další hodnoty:

    - **Skladová položka**: základní
    - **Virtuální síť**: vyberte virtuální síť, který jste vytvořili dříve. Podsíť brány, kterou jste vytvořili je automaticky vybrán.
    - **První konfigurace protokolu IP**: Toto je veřejná IP adresa brány.
        - Vyberte **konfigurace IP brány vytvořit**, který přejdete **zvolte veřejnou IP adresu** stránky.
        - Vyberte **+ vytvořit nový** otevřete **vytvoření veřejné IP adresy** stránky.
        - Zadejte **název** pro svoji veřejnou IP adresu. Nechte SKU jako **základní**a potom vyberte **OK** uložte provedené změny.

       > [!Note]
       > Brána sítě VPN v současné době podporuje pouze přidělení dynamické veřejné IP adresy. Neznamená to ale, že IP adresa změní po je přiřazen k bráně VPN. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. Změna velikosti, resetování nebo jiné operace údržby/upgradu vaší brány sítě VPN neměnit IP adresu.

4. Ověřte nastavení brány.
5. Vyberte **vytvořit** k vytvoření brány VPN. Nastavení brány se ověří a na řídicím panelu se zobrazí na dlaždici "Brána nasazení virtuální sítě".

   >[!Note]
   >Vytváření brány může trvat až 45 minut. K zobrazení stavu dokončení může být nutné obnovit stránku portálu.

    Po vytvoření brány můžete zobrazit IP adresu přiřazen prohlížením virtuální sítě na portálu. Brána se zobrazí jako připojené zařízení. Pokud chcete zobrazit další informace o bráně, vyberte zařízení.

6. Opakujte předchozí kroky (1-5) na vaše nasazení Azure zásobníku.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Vytvoření brány místní sítě v Azure a Azure zásobníku

Brána místní sítě obvykle odkazuje na vaše místní umístění. Lokality dáte název, který Azure nebo Azure zásobníku může odkazovat na a pak zadejte:

- IP adresa místní zařízení VPN, který vytváříte připojení pro.
- Předpony IP adres, které budou směrovány přes bránu VPN do zařízení VPN. Předpony adres, které zadáte, jsou předpony ve vaší místní síti.

  >[!Note]
  >Pokud změny vaší místní sítě, nebo můžete potřebovat změnit veřejnou IP adresu pro zařízení VPN, můžete snadno aktualizovat tyto hodnoty později.

1. Na portálu, vyberte **+ vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **brány místní sítě**, pak vyberte **Enter** pro vyhledávání. Zobrazí se seznam výsledků.
3. Vyberte **brány místní sítě**, pak vyberte **vytvořit** otevřete **vytvořit bránu místní sítě** stránky.
4. Na **vytvořit bránu místní sítě**, zadejte hodnoty pro bránu místní sítě pomocí našich **kurz ukázkové hodnoty**. Zahrnují následující další hodnoty.

    - **IP adresa**: Toto je veřejnou IP adresu zařízení VPN, který chcete pro připojení k Azure nebo Azure zásobníku. Zadejte platnou veřejnou IP adresu, která není za zařízení NAT, takže Azure lze spojit adresu. Pokud nyní nemáte IP adresu, můžete použít hodnotu z příkladu jako zástupný znak, ale budete muset přejděte zpět a nahraďte zástupný symbol s veřejnou IP adresu vašeho zařízení VPN. Azure se nemůže připojit k zařízení, dokud zadejte platnou adresu.
    - **Adresní prostor**: Toto je rozsah adres pro síť, která představuje tento místní sítě. Můžete přidat více různých rozsahů adres. Ujistěte se, že rozsahy, které zadáte nepřekrývaly s jinými sítěmi, které se chcete připojit k rozsahy. Azure bude směrovat zadaný rozsah adres na místní IP adresu zařízení VPN. Použijte vlastní hodnoty, pokud se chcete připojit k místní lokalitě, není příkladem hodnoty.
    - **Konfigurace nastavení protokolu BGP**: používejte jenom v případě, že konfigurace protokolu BGP. V jiných případech tuto možnost nevybírejte.
    - **Předplatné**: Ověřte, že se zobrazuje správné předplatné.
    - **Skupina prostředků**: Vyberte skupinu prostředků, který chcete použít. Můžete buď vytvořit novou skupinu prostředků nebo vyberte ten, který jste už vytvořili.
    - **Umístění**: Vyberte umístění, které tento objekt se vytvoří v. Můžete vybrat stejné umístění, které virtuální sítě se nachází v, ale není nutné učinit.
5. Po dokončení výběru požadované hodnoty, vyberte **vytvořit** k vytvoření brány místní sítě.
6. Opakujte tyto kroky (1-5) na vaše nasazení Azure zásobníku.

## <a name="configure-your-connection"></a>Nakonfigurujte připojení

Připojení Site-to-Site k místní síti vyžadují zařízení VPN. Zařízení VPN, které nakonfigurujete, se označuje jako připojení. Chcete-li konfigurovat připojení, je třeba:

- Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáváte při vytváření připojení VPN Site-to-Site. V našich ukázkách používáme základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
- Veřejnou IP adresu vaší brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Najít veřejnou IP adresu brány VPN pomocí portálu Azure, přejděte do brány virtuální sítě a potom vyberte název vaší brány.

Použijte následující postup k vytvoření připojení VPN typu Site-to-Site mezi bránou virtuální sítě a vaše místní zařízení VPN.

1. Na portálu Azure vyberte **+ vytvořit prostředek**.
2. Vyhledejte **připojení**.
3. V **výsledky**, vyberte **připojení**.
4. Na **připojení**, vyberte **vytvořit**.
5. Na **vytvořit připojení**, nakonfigurujte následující nastavení:

    - **Typ připojení**: vyberte Site-to-site (IPSec).
    - **Skupina prostředků**: vyberte testovací skupinu prostředků.
    - **Brána virtuální sítě**: Vyberte bránu virtuální sítě, který jste vytvořili.
    - **Bránu místní sítě**: Vyberte bránu místní sítě jste vytvořili.
    - **Název připojení**: to je automaticky vyplněno z hodnot dvě brány.
    - **Sdílený klíč**: Tato hodnota musí odpovídat hodnotě, kterou používáte pro vaše místní zařízení VPN. Kurz příklad používá 'abc123', ale můžete (a měli) používáte něco složitější. Důležité je, že tato hodnota musí být stejnou hodnotou, kterou zadáte při konfiguraci zařízení VPN.
    - Hodnoty pro **předplatné**, **skupiny prostředků**, a **umístění** jsou pevné.

6. Vyberte **OK** vytvořte připojení.

Zobrazí se připojení v **připojení** stránky bránu virtuální sítě. Stav přejde z *neznámé* k *připojení*a poté na *úspěšné*.

## <a name="next-steps"></a>Další postup

- Další informace o vzorů cloudu Azure, najdete v části [vzory návrhu cloudové](https://docs.microsoft.com/azure/architecture/patterns).
