---
title: Vytvoření připojení typu point-to-site k Azure pomocí služby Azure Virtual WAN | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí služby Azure Virtual WAN vytvořit připojení VPN typu point-to-site k Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 06/29/2020
ms.author: alzam
ms.openlocfilehash: 9c93ad0357011008c45b2898260a655509b02dc2
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560684"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Kurz: vytvoření připojení VPN uživatele pomocí Azure Virtual WAN

V tomto kurzu se dozvíte, jak se pomocí služby Virtual WAN připojit ke svým prostředkům v Azure přes připojení VPN IPsec/IKE (IKEv2) nebo OpenVPN. Tento typ připojení vyžaduje, aby byl na klientském počítači nakonfigurovaný klient. Další informace o virtuální síti WAN najdete v tématu [Virtual WAN – přehled](virtual-wan-about.md)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření sítě WAN
> * Vytvoření konfigurace P2S
> * Vytvoření rozbočovače
> * Zadat servery DNS
> * Stáhnout profil klienta VPN
> * Zobrazení virtuální sítě WAN

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Máte virtuální síť, ke které se chcete připojit. Ověřte, že se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť v Azure Portal, přečtěte si [rychlý Start](../virtual-network/quick-create-portal.md).

* Vaše virtuální síť nemá žádné brány virtuální sítě. Pokud má vaše virtuální síť bránu (buď VPN, nebo ExpressRoute), musíte odebrat všechny brány. Tato konfigurace vyžaduje, aby se virtuální sítě místo toho připojovaly k virtuální bráně WAN hub.

* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Centrum je virtuální síť, kterou vytváří a používá virtuální síť WAN. Rozsah adres, který zadáte pro centrum, se nemůže překrývat s žádnou ze stávajících virtuálních sítí, ke kterým se připojujete. Taky se nesmí překrývat s rozsahy adres, ke kterým se připojujete v místním prostředí. Pokud neznáte rozsahy IP adres nacházející se v konfiguraci vaší místní sítě, zajistěte koordinaci s někým, kdo vám poskytne tyto podrobnosti.

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Vytvoření virtuální sítě WAN

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

1. Přejděte na stránku Virtual WAN. Na portálu vyberte **+ vytvořit prostředek**. Do vyhledávacího pole zadejte **virtuální síť WAN** a vyberte **ENTER**.
1. Z výsledků vyberte **virtuální síť WAN** . Na stránce virtuální síť WAN vyberte **vytvořit** a otevřete stránku vytvořit síť WAN.
1. Na stránce **vytvořit síť WAN** na kartě **základy** vyplňte následující pole:

   ![Virtual WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Subscription** (Předplatné) – vyberte předplatné, které chcete použít.
   * **Skupina prostředků** – vytvořte nové nebo použijte existující.
   * **Umístění skupiny prostředků** – vyberte umístění prostředku z rozevíracího seznamu. Síť WAN je globální prostředek, takže se nenachází v určité oblasti. Přesto je ale potřeba oblast vybrat, abyste mohli snáz spravovat a vyhledávat prostředek sítě WAN, který vytvoříte.
   * **Název** – zadejte název, který chcete zavolat do sítě WAN.
   * **Zadejte:** Standardní. Pokud vytvoříte základní síť WAN, můžete vytvořit jenom základní centrum. Základní centra se podporují jenom pro připojení VPN typu Site-to-site.
1. Po dokončení vyplňování polí vyberte **zkontrolovat + vytvořit**.
1. Po úspěšném ověření vyberte **vytvořit** a vytvořte virtuální síť WAN.

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Vytvoření konfigurace P2S

Konfigurace P2S definuje parametry pro připojení vzdálených klientů.

1. Přejděte na **All resources** (Všechny prostředky).
1. Vyberte virtuální síť WAN, kterou jste vytvořili.
1. V horní části stránky vyberte **+ vytvořit uživatel VPN config** a otevřete stránku **vytvořit novou konfiguraci VPN uživatele** .

   :::image type="content" source="media/virtual-wan-point-to-site-portal/p2s1.jpg" alt-text="Uživatelské konfigurace sítě VPN":::

1. Na stránce **vytvořit novou konfiguraci VPN uživatele** zadejte následující pole:

   * **Název konfigurace** – Název, kterým chcete na konfiguraci odkazovat.
   * **Typ tunelu** – Protokol, který se má použít pro tunel.
   * **Název kořenového certifikátu** – Popisný název certifikátu.
   * **Data veřejného certifikátu** -Base-64 kódovaná data certifikátu X. 509
  
1. Vyberte **vytvořit** a vytvořte konfiguraci.

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Vytvoření centra s bránou Point-to-site

1. V části virtuální síť WAN vyberte rozbočovače a vyberte **+ nové centrum**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub1.jpg" alt-text="nové centrum":::

1. Na stránce vytvořit virtuální rozbočovač vyplňte následující pole.

   * **Oblast** – vyberte oblast, do které chcete nasadit virtuální rozbočovač.
   * **Název** – zadejte název, který chcete zavolat do svého virtuálního rozbočovače.
   * **Privátní adresní prostor centra** – rozsah adres rozbočovače v zápisu CIDR.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub2.jpg" alt-text="vytvořit virtuální rozbočovač":::

1. Na kartě Point-to-site vyplňte následující pole:

   * **Jednotky škálování brány** – což představuje agregovanou kapacitu brány VPN uživatele.
   * **Najeďte na položku Konfigurace lokality** , kterou jste vytvořili v předchozím kroku.
   * **Fond adres klienta** – pro vzdálené uživatele.
   * **IP adresa vlastního serveru DNS**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub-with-p2s.png" alt-text="centrum s Point-to-site":::

1. Vyberte **Zkontrolovat a vytvořit**.
1. Na stránce **úspěšné ověření** vyberte **vytvořit**.

## <a name="specify-dns-server"></a><a name="dns"></a>Zadat server DNS

Virtuální brány VPN uživatele sítě WAN umožňují zadat až 5 serverů DNS. Tuto možnost můžete nakonfigurovat během procesu vytváření centra nebo ji později změnit. Provedete to tak, že vyhledáte virtuální rozbočovač. V části **uživatelské rozhraní VPN (Point-to-site)** klikněte na konfigurovat a zadejte IP adresy serveru DNS do textového pole **vlastní servery DNS** .

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="vlastní DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Stažení profilu sítě VPN

Pomocí profilu sítě VPN nakonfigurujte své klienty.

1. Na stránce pro virtuální síť WAN vyberte **Konfigurace sítě VPN uživatele**.
2. V horní části stránky vyberte **Stáhnout uživatel konfigurace sítě VPN**. Stažení konfigurace na úrovni sítě WAN poskytuje předdefinovanou Traffic Manager profil VPN uživatele. Další informace o globálních profilech nebo profilech založených na centrálních profilech najdete v těchto [profilech rozbočovače](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile).   Scénáře převzetí služeb při selhání jsou zjednodušené s globálním profilem.

   Pokud z nějakého důvodu není rozbočovač k dispozici, integrovaná správa provozu poskytovaná službou zajišťuje připojení prostřednictvím jiného centra k prostředkům Azure pro uživatele typu Point-to-site. Konfiguraci sítě VPN pro konkrétní centrum si můžete vždycky stáhnout tak, že přejdete do konkrétního centra. V části **Uživatelská síť VPN (Ukázat na lokalitu)** stáhněte profil **VPN uživatele** virtuálního rozbočovače.

1. Po vytvoření souboru můžete vybrat odkaz a stáhnout ho.
1. K nakonfigurování klientů VPN použijte profilový soubor.

### <a name="configure-user-vpn-clients"></a>Konfigurace klientů VPN uživatelů

Pomocí staženého profilu nakonfigurujte klienty pro vzdálený přístup. Postup se v jednotlivých operačních systémech liší, postupujte tedy podle odpovídajících pokynů uvedených níže:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Z oficiálního webu si stáhněte klienta OpenVPN a nainstalujte ho.
1. Stáhněte si profil sítě VPN pro bránu. To se dá udělat na kartě Konfigurace sítě VPN uživatele v Azure Portal nebo New-AzureRmVpnClientConfiguration v PowerShellu.
1. Rozbalte profil. V Poznámkovém bloku otevřete konfigurační soubor vpnconfig.ovpn ze složky OpenVPN.
1. V části klientského certifikátu P2S vyplňte veřejný klíč klientského certifikátu P2S v kódování Base-64. V případě certifikátu s formátem PEM stačí otevřít soubor .cer a zkopírovat klíč Base-64 uvedený mezi hlavičkami certifikátu. Postup najdete v tématu [Postup exportu certifikátu pro získání kódovaného veřejného klíče.](certificates-point-to-site.md)
1. V části privátního klíče vyplňte privátní klíč klientského certifikátu P2S v kódování Base-64. Postup najdete v tématu [postup extrakce privátního klíče.](howto-openvpn-clients.md#windows).
1. Ostatní pole ponechte beze změny. S použitím vyplněné konfigurace ve vstupu klienta se připojte k síti VPN.
1. Zkopírujte soubor vpnconfig.ovpn do složky C:\Program Files\OpenVPN\config.
1. Na hlavním panelu systému klikněte pravým tlačítkem na ikonu OpenVPN a vyberte **připojit**.

##### <a name="ikev2"></a>IKEv2

1. Vyberte konfigurační soubory klienta VPN, které odpovídají architektuře počítače s Windows. V případě 64bitové architektury procesoru zvolte instalační balíček VpnClientSetupAmd64. V případě 32bitové architektury procesoru zvolte instalační balíček VpnClientSetupX86.
1. Dvakrát klikněte na balíček a nainstalujte ho. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, klikněte na **Další informace**a potom na **přesto spustit**.
1. V klientském počítači přejděte na **nastavení sítě** a vyberte **síť VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje.
1. Než se pokusíte o připojení, ověřte, že jste na klientském počítači nainstalovali klientský certifikát. Klientský certifikát se vyžaduje k ověřování při použití typu nativního ověřování certifikátů Azure. Další informace o generování certifikátů najdete v tématu věnovaném [generování certifikátů](certificates-point-to-site.md). Informace o instalaci klientského certifikátu najdete v tématu [instalace klientského certifikátu](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Zobrazení virtuální sítě WAN

1. Přejděte na virtuální síť WAN.
1. Na stránce **Přehled** představuje každý bod na mapě rozbočovač.
1. V části **centra a připojení** můžete zobrazit stav centra, lokalitu, oblast, stav připojení VPN a v a v bajtech.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Položku myResourceGroup nahraďte názvem vaší skupiny prostředků a spusťte následující příkaz PowerShellu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
