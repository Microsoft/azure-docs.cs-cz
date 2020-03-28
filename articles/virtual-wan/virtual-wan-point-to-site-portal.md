---
title: Vytvoření připojení typu point-to-site k Azure pomocí služby Azure Virtual WAN | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí služby Azure Virtual WAN vytvořit připojení VPN typu point-to-site k Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: alzam
ms.openlocfilehash: fd415e1da00f52a9a3b55c946a07a30cf841cf4a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060296"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Kurz: Vytvoření připojení VPN uživatele pomocí azure virtuální sítě WAN

V tomto kurzu se dozvíte, jak se pomocí služby Virtual WAN připojit ke svým prostředkům v Azure přes připojení VPN IPsec/IKE (IKEv2) nebo OpenVPN. Tento typ připojení vyžaduje, aby byl na klientském počítači nakonfigurovaný klient. Další informace o virtuální paměti WAN naleznete v [přehledu virtuální sítě WAN](virtual-wan-about.md)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření sítě WAN
> * Vytvoření rozbočovače
> * Vytvoření konfigurace P2S
> * Stažení profilu klienta VPN
> * Použití konfigurace P2S na rozbočovač
> * Připojení virtuální sítě k rozbočovači
> * Stažení a použití konfigurace klienta VPN
> * Zobrazení virtuální sítě WAN

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Máte virtuální síť, ke které se chcete připojit. Ověřte, zda se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť na webu Azure Portal, podívejte se na [úvodní příručku](../virtual-network/quick-create-portal.md).

* Virtuální síť nemá žádné brány virtuální sítě. Pokud vaše virtuální síť má bránu (vpn nebo ExpressRoute), musíte odebrat všechny brány. Tato konfigurace vyžaduje, aby virtuální sítě byly připojeny místo toho k bráně centra Virtuální WAN.

* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Rozbočovač je virtuální síť, která je vytvořena a používávirtuální WAN. Rozsah adres, který zadáte pro rozbočovač, se nemůže překrývat s žádnou z existujících virtuálních sítí, ke kterým se připojujete. Taky se nesmí překrývat s rozsahy adres, ke kterým se připojujete v místním prostředí. Pokud nejste obeznámeni s rozsahy IP adres umístěnými v místní konfiguraci sítě, koordinujte je s někým, kdo vám tyto podrobnosti může poskytnout.

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Vytvoření virtuální sítě WAN

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

1. Přejděte na stránku Virtuální wan. Na portálu klikněte na **+ Vytvořit prostředek**. Do vyhledávacího pole zadejte **virtuální wan** a vyberte Enter.
2. Z výsledků vyberte **virtuální wan.** Na stránce Virtuální wan kliknutím na **Vytvořit** otevřete stránku Vytvořit wan.
3. Na stránce **Vytvořit wan** vyplňte na kartě **Základy** následující pole:

   ![Virtuální síť WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Subscription** (Předplatné) – vyberte předplatné, které chcete použít.
   * **Skupina prostředků** – vytvořit nové nebo použít existující.
   * **Umístění skupiny prostředků** – z rozevíracího souboru zvolte umístění prostředků. Síť WAN je globální prostředek, takže se nenachází v určité oblasti. Přesto je ale potřeba oblast vybrat, abyste mohli snáz spravovat a vyhledávat prostředek sítě WAN, který vytvoříte.
   * **Název** – Zadejte název, který chcete volat wan.
   * **Typ:** Standardní. Pokud vytvoříte základní wan, můžete vytvořit pouze základní rozbočovač. Základní rozbočovače jsou schopny pouze připojení k síti VPN.
4. Po vyplnění polí vyberte **Zkontrolovat +Vytvořit**.
5. Jakmile ověření projde, vyberte **Vytvořit** a vytvořte virtuální WAN.

## <a name="create-an-empty-virtual-hub"></a><a name="hub"></a>Vytvoření prázdného virtuálního rozbočovače

1. Pod virtuální wan, vyberte Rozbočovače a klikněte **+New Hub**

   ![nová lokalita](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. Na stránce vytvořit virtuální rozbočovač vyplňte následující pole.

   **Oblast** – vyberte oblast, ve které chcete virtuální rozbočovač nasadit.

   **Název** – Zadejte název, který chcete volat virtuální rozbočovač.

   **Privátní adresní prostor** rozbočovače – rozsah adres centra v zápisu CIDR.

   ![nová lokalita](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Klikněte na **Zkontrolovat + vytvořit**
4. Na stránce **ověření předakovací** klikněte na **vytvořit**

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Vytvoření konfigurace P2S

Konfigurace P2S definuje parametry pro připojení vzdálených klientů.

1. Přejděte na **All resources** (Všechny prostředky).
2. Klikněte na virtuální síť WAN, kterou jste vytvořili.
3. Kliknutím na **+Vytvořit uživatelskou konfiguraci VPN** v horní části stránky otevřete **konfigurační** stránku Vytvořit novou uživatelskou VPN.

   ![nová lokalita](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. Na stránce **Vytvořit novou uživatelskou konfiguraci VPN** vyplňte následující pole:

   **Název konfigurace** – Název, kterým chcete na konfiguraci odkazovat.

   **Typ tunelu** – Protokol, který se má použít pro tunel.

   **Název kořenového certifikátu** – Popisný název certifikátu.

   **Data veřejného certifikátu** - Base-64 kódovaná data certifikátu X.509.
  
5. Kliknutím na **Vytvořit** vytvořte konfiguraci.

## <a name="edit-hub-assignment"></a><a name="edit"></a>Úprava přiřazení k rozbočovači

1. Přejděte k noži **Hubs** pod virtuální wan
2. Vyberte rozbočovač, ke kterému chcete přidružit konfiguraci serveru VPN, a klikněte na **tlačítko ...**

   ![nová lokalita](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Klikněte na **Upravit virtuální rozbočovač**.
4. Zaškrtněte políčko **Zahrnout bránu bod k webu** a vyberte **požadovanou jednotku škálování brány.**

   ![nová lokalita](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Zadejte **fond adres,** ze kterého budou klientům VPN přiřazeny ip adresy.
6. Klikněte na **Potvrdit.**
7. Operace může trvat až 30 minut.

## <a name="download-vpn-profile"></a><a name="download"></a>Stažení profilu sítě VPN

Pomocí profilu sítě VPN nakonfigurujte své klienty.

1. Na stránce virtuální sítě WAN klikněte na **Konfigurace uživatelské sítě VPN**.
2. V horní části stránky klikněte na **Stáhnout uživatelskou konfiguraci VPN**.
3. Jakmile se dokončí vytváření souboru, můžete ho kliknutím na odkaz stáhnout.
4. Pomocí souboru profilu nakonfigurujte klienty VPN.

### <a name="configure-user-vpn-clients"></a>Konfigurace klienta VPN uživatele
Pomocí staženého profilu nakonfigurujte klienty pro vzdálený přístup. Postup se v jednotlivých operačních systémech liší, postupujte tedy podle odpovídajících pokynů uvedených níže:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Z oficiálního webu si stáhněte klienta OpenVPN a nainstalujte ho.
2. Stáhněte si profil sítě VPN pro bránu. To lze provést z karty Konfigurace uživatelské VPN na webu Azure Portal nebo New-AzureRmVpnClientConfiguration v PowerShellu.
3. Rozbalte profil. V Poznámkovém bloku otevřete konfigurační soubor vpnconfig.ovpn ze složky OpenVPN.
4. V části klientského certifikátu P2S vyplňte veřejný klíč klientského certifikátu P2S v kódování Base-64. V případě certifikátu s formátem PEM stačí otevřít soubor .cer a zkopírovat klíč Base-64 uvedený mezi hlavičkami certifikátu. Postup najdete v [tématu Jak exportovat certifikát, abyste získali kódovaný veřejný klíč.](certificates-point-to-site.md)
5. V části privátního klíče vyplňte privátní klíč klientského certifikátu P2S v kódování Base-64. Postup naleznete v tématu [Extrahování soukromého klíče.](howto-openvpn-clients.md#windows). .
6. Ostatní pole ponechte beze změny. S použitím vyplněné konfigurace ve vstupu klienta se připojte k síti VPN.
7. Zkopírujte soubor vpnconfig.ovpn do složky C:\Program Files\OpenVPN\config.
8. Klikněte pravým tlačítkem na ikonu OpenVPN na hlavním panelu a klikněte na Připojit.

##### <a name="ikev2"></a>IKEv2

1. Vyberte konfigurační soubory klienta VPN, které odpovídají architektuře počítače s Windows. V případě 64bitové architektury procesoru zvolte instalační balíček VpnClientSetupAmd64. V případě 32bitové architektury procesoru zvolte instalační balíček VpnClientSetupX86.
2. Dvakrát klikněte na balíček a nainstalujte ho. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, klikněte na Další informace a pak na Přesto spustit.
3. Na klientském počítači přejděte do Nastavení sítě a klikněte na Síť VPN. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje.
4. Než se pokusíte o připojení, ověřte, že jste na klientském počítači nainstalovali klientský certifikát. Klientský certifikát se vyžaduje k ověřování při použití typu nativního ověřování certifikátů Azure. Další informace o generování certifikátů naleznete v [tématu Generování certifikátů](certificates-point-to-site.md). Informace o instalaci klientského certifikátu naleznete v [tématu Instalace klientského certifikátu](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Zobrazení virtuální sítě WAN

1. Přejděte na virtuální síť WAN.
2. Na stránce Overview (Přehled) každý bod na mapě představuje jeden rozbočovač.
3. V části Hubs and connections (Rozbočovače a připojení) můžete zjistit stav rozbočovače, lokalitu, oblast, stav připojení VPN a přijaté a odeslané bajty.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Položku myResourceGroup nahraďte názvem vaší skupiny prostředků a spusťte následující příkaz PowerShellu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
