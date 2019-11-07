---
title: Vytvoření připojení typu point-to-site k Azure pomocí služby Azure Virtual WAN | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí služby Azure Virtual WAN vytvořit připojení VPN typu point-to-site k Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: alzam
ms.openlocfilehash: 0319e3aec71d37b49a094861fdcbb3b96b6def67
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585430"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Kurz: vytvoření připojení VPN uživatele pomocí Azure Virtual WAN

V tomto kurzu se dozvíte, jak se pomocí služby Virtual WAN připojit ke svým prostředkům v Azure přes připojení VPN IPsec/IKE (IKEv2) nebo OpenVPN. Tento typ připojení vyžaduje, aby byl na klientském počítači nakonfigurovaný klient. Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření sítě WAN
> * Vytvoření rozbočovače
> * Vytvoření konfigurace P2S
> * Stáhnout profil klienta VPN
> * Použití konfigurace P2S na rozbočovač
> * Připojení virtuální sítě k rozbočovači
> * Stažení a použití konfigurace klienta VPN
> * Zobrazení virtuální sítě WAN
> * Zobrazení stavu prostředků

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Máte virtuální síť, ke které se chcete připojit. Ověřte, že se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť v Azure Portal, přečtěte si [rychlý Start](../virtual-network/quick-create-portal.md).

* Vaše virtuální síť nemá žádné brány virtuální sítě. Pokud má vaše virtuální síť bránu (buď VPN, nebo ExpressRoute), musíte odebrat všechny brány. Tato konfigurace vyžaduje, aby se virtuální sítě místo toho připojovaly k virtuální bráně WAN hub.

* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Centrum je virtuální síť, kterou vytváří a používá virtuální síť WAN. Rozsah adres, který zadáte pro centrum, se nemůže překrývat s žádnou ze stávajících virtuálních sítí, ke kterým se připojujete. Taky se nesmí překrývat s rozsahy adres, ke kterým se připojujete v místním prostředí. Pokud neznáte rozsahy IP adres nacházející se v konfiguraci vaší místní sítě, zajistěte koordinaci s někým, kdo vám poskytne tyto podrobnosti.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="wan"></a>Vytvoření virtuální sítě WAN

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

1. Přejděte na stránku Virtual WAN. Na portálu klikněte na **+ Vytvořit prostředek**. Do vyhledávacího pole zadejte **virtuální síť WAN** a vyberte Enter.
2. Z výsledků vyberte **virtuální síť WAN** . Na stránce virtuální síť WAN kliknutím na **vytvořit** otevřete stránku vytvořit síť WAN.
3. Na stránce **vytvořit síť WAN** na kartě **základy** vyplňte následující pole:

   ![Virtual WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Subscription** (Předplatné) – vyberte předplatné, které chcete použít.
   * **Skupina prostředků** – vytvořte nové nebo použijte existující.
   * **Umístění skupiny prostředků** – vyberte umístění prostředku z rozevíracího seznamu. Síť WAN je globální prostředek, takže se nenachází v určité oblasti. Přesto je ale potřeba oblast vybrat, abyste mohli snáz spravovat a vyhledávat prostředek sítě WAN, který vytvoříte.
   * **Název** – zadejte název, který chcete zavolat do sítě WAN.
   * **Zadejte:** Standardní. Pokud vytvoříte základní síť WAN, můžete vytvořit jenom základní centrum. Základní centra se podporují jenom pro připojení VPN typu Site-to-site.
4. Po dokončení vyplňování polí vyberte **zkontrolovat + vytvořit**.
5. Po úspěšném ověření vyberte **vytvořit** a vytvořte virtuální síť WAN.

## <a name="site"></a>Vytvořit prázdné virtuální rozbočovač

1. V části virtuální síť WAN vyberte centra a klikněte na **+ nové centrum** .

   ![nová lokalita](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. Na stránce vytvořit virtuální rozbočovač vyplňte následující pole.

   **Oblast** – vyberte oblast, do které chcete nasadit virtuální rozbočovač.

   **Název** – zadejte název, který chcete zavolat do svého virtuálního rozbočovače.

   **Privátní adresní prostor centra** – rozsah adres rozbočovače v zápisu CIDR.

   ![nová lokalita](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Klikněte na tlačítko **zkontrolovat + vytvořit** .
4. Na stránce **ověřování proběhlo úspěšně** klikněte na **vytvořit** .

## <a name="site"></a>Vytvoření konfigurace P2S

Konfigurace P2S definuje parametry pro připojení vzdálených klientů.

1. Přejděte na **All resources** (Všechny prostředky).
2. Klikněte na virtuální síť WAN, kterou jste vytvořili.
3. Kliknutím na **+ vytvořit uživatelskou konfiguraci sítě VPN** v horní části stránky otevřete stránku pro **Vytvoření nové konfigurace sítě VPN uživatele** .

   ![nová lokalita](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. Na stránce **vytvořit novou konfiguraci VPN uživatele** zadejte následující pole:

   **Název konfigurace** – Název, kterým chcete na konfiguraci odkazovat.

   **Typ tunelu** – Protokol, který se má použít pro tunel.

   **Název kořenového certifikátu** – Popisný název certifikátu.

   **Data veřejného certifikátu** -Base-64 kódovaná data certifikátu X. 509
  
   ![nová lokalita](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Kliknutím na **Vytvořit** vytvořte konfiguraci.

## <a name="hub"></a>Upravit přiřazení centra

1. Přejděte do okna **centra** v rámci virtuální sítě WAN.
2. Vyberte centrum, ke kterému chcete přiřadit konfiguraci serveru VPN, a klikněte na **...**

   ![nová lokalita](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Klikněte na **Upravit virtuální rozbočovač**.
4. Zaškrtněte políčko **Zahrnout bránu Point-to-site** a vyberte **jednotku škálování brány** , kterou chcete použít.

   ![nová lokalita](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Zadejte **fond adres** , ze kterého budou klienti VPN přiřazeni IP adresy.
6. Klikněte na **Potvrdit** .
7. Dokončení operace může trvat až 30 minut.

## <a name="device"></a>Stáhnout profil VPN

Pomocí profilu sítě VPN nakonfigurujte své klienty.

1. Na stránce pro virtuální síť WAN klikněte na **Konfigurace sítě VPN uživatele**.
2. V horní části stránky klikněte na **Stáhnout uživatelskou konfiguraci VPN**.
3. Jakmile se dokončí vytváření souboru, můžete ho kliknutím na odkaz stáhnout.
4. K nakonfigurování klientů VPN použijte profilový soubor.

### <a name="configure-user-vpn-clients"></a>Konfigurace klientů VPN uživatelů
Pomocí staženého profilu nakonfigurujte klienty pro vzdálený přístup. Postup se v jednotlivých operačních systémech liší, postupujte tedy podle odpovídajících pokynů uvedených níže:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Z oficiálního webu si stáhněte klienta OpenVPN a nainstalujte ho.
2. Stáhněte si profil sítě VPN pro bránu. To se dá udělat na kartě Konfigurace sítě VPN uživatele v Azure Portal nebo New-AzureRmVpnClientConfiguration v PowerShellu.
3. Rozbalte profil. V Poznámkovém bloku otevřete konfigurační soubor vpnconfig.ovpn ze složky OpenVPN.
4. V části klientského certifikátu P2S vyplňte veřejný klíč klientského certifikátu P2S v kódování Base-64. V případě certifikátu s formátem PEM stačí otevřít soubor .cer a zkopírovat klíč Base-64 uvedený mezi hlavičkami certifikátu. Tady zjistíte, jak exportovat certifikát a získat zakódovaný veřejný klíč.
5. V části privátního klíče vyplňte privátní klíč klientského certifikátu P2S v kódování Base-64. Tady zjistíte, jak extrahovat privátní klíč.
6. Ostatní pole ponechte beze změny. S použitím vyplněné konfigurace ve vstupu klienta se připojte k síti VPN.
7. Zkopírujte soubor vpnconfig.ovpn do složky C:\Program Files\OpenVPN\config.
8. Klikněte pravým tlačítkem na ikonu OpenVPN na hlavním panelu a klikněte na Připojit.

##### <a name="ikev2"></a>IKEv2

1. Vyberte konfigurační soubory klienta VPN, které odpovídají architektuře počítače s Windows. V případě 64bitové architektury procesoru zvolte instalační balíček VpnClientSetupAmd64. V případě 32bitové architektury procesoru zvolte instalační balíček VpnClientSetupX86.
2. Dvakrát klikněte na balíček a nainstalujte ho. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, klikněte na Další informace a pak na Přesto spustit.
3. Na klientském počítači přejděte do Nastavení sítě a klikněte na Síť VPN. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje.
4. Než se pokusíte o připojení, ověřte, že jste na klientském počítači nainstalovali klientský certifikát. Klientský certifikát se vyžaduje k ověřování při použití typu nativního ověřování certifikátů Azure. Další informace o generování certifikátů najdete v tématu Generování certifikátů. Informace o instalaci klientského certifikátu najdete v tématu Instalace klientského certifikátu.

## <a name="viewwan"></a>Zobrazení virtuální sítě WAN

1. Přejděte na virtuální síť WAN.
2. Na stránce Overview (Přehled) každý bod na mapě představuje jeden rozbočovač. Podržením ukazatele na některém z těchto bodů zobrazíte souhrn stavu rozbočovače.
3. V části Hubs and connections (Rozbočovače a připojení) můžete zjistit stav rozbočovače, lokalitu, oblast, stav připojení VPN a přijaté a odeslané bajty.

## <a name="viewhealth"></a>Zobrazení stavu prostředku

1. Přejděte na svoji síť WAN.
2. Na stránce sítě WAN v části **SUPPORT + Troubleshooting** (Podpora a řešení potíží) klikněte na **Health** (Stav) a prohlédněte si stav svého prostředku.


## <a name="cleanup"></a>Vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Položku myResourceGroup nahraďte názvem vaší skupiny prostředků a spusťte následující příkaz PowerShellu:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).