---
title: 'Konfigurace ověřování Azure AD pro připojení VPN uživatele: Virtuální síť WAN'
description: Přečtěte si, jak nakonfigurovat ověřování Azure Active Directory pro uživatelskou VPN.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: alzam
ms.openlocfilehash: 703b832d58f2374eac131cfd380ba27f2c890618
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059488"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Konfigurace ověřování azure služby Active Directory pro uživatelskou VPN

Tento článek ukazuje, jak nakonfigurovat ověřování Azure AD pro uživatele VPN ve virtuální síti WAN pro připojení k prostředkům v Azure přes připojení OpenVPN VPN. Azure Active Directory ověřování je k dispozici pouze pro brány pomocí protokolu OpenVPN a klientů se systémem Windows.

Tento typ připojení vyžaduje, aby byl na klientském počítači nakonfigurovaný klient. Další informace o službě Virtual WAN najdete v tématu [Přehled služby Virtual WAN](virtual-wan-about.md).

V tomto článku získáte informace o těchto tématech:

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

* Máte virtuální síť, ke které se chcete připojit. Ověřte, zda se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť na webu Azure Portal, přečtěte si [úvodní příručku](../virtual-network/quick-create-portal.md).

* Virtuální síť nemá žádné brány virtuální sítě. Pokud vaše virtuální síť má bránu (vpn nebo ExpressRoute), musíte odebrat všechny brány. Tato konfigurace vyžaduje, aby virtuální sítě byly připojeny místo toho k bráně centra Virtuální WAN.

* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Rozbočovač je virtuální síť, která je vytvořena a používávirtuální WAN. Rozsah adres, který zadáte pro rozbočovač, se nemůže překrývat s žádnou z existujících virtuálních sítí, ke kterým se připojujete. Taky se nesmí překrývat s rozsahy adres, ke kterým se připojujete v místním prostředí. Pokud nejste obeznámeni s rozsahy IP adres umístěnými v místní konfiguraci sítě, koordinujte je s někým, kdo vám tyto podrobnosti může poskytnout.

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Vytvoření virtuální sítě WAN

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

1. Přejděte na stránku Virtuální wan. Na portálu klikněte na **+ Vytvořit prostředek**. Do vyhledávacího pole zadejte **virtuální wan** a vyberte Enter.
2. Z výsledků vyberte **virtuální wan.** Na stránce Virtuální wan kliknutím na **Vytvořit** otevřete stránku Vytvořit wan.
3. Na stránce **Vytvořit wan** vyplňte na kartě **Základy** následující pole:

   ![Virtuální síť WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Subscription** (Předplatné) – vyberte předplatné, které chcete použít.
   * **Skupina prostředků** – vytvořit nové nebo použít existující.
   * **Umístění skupiny prostředků** – z rozevíracího souboru zvolte umístění prostředků. Síť WAN je globální prostředek, takže se nenachází v určité oblasti. Přesto je ale potřeba oblast vybrat, abyste mohli snáz spravovat a vyhledávat prostředek sítě WAN, který vytvoříte.
   * **Název** – Zadejte název, který chcete volat wan.
   * **Typ:** Standardní. Pokud vytvoříte základní wan, můžete vytvořit pouze základní rozbočovač. Základní rozbočovače jsou schopny pouze připojení k síti VPN.
4. Po vyplnění polí vyberte **Zkontrolovat +Vytvořit**.
5. Po uplynutí platnosti ověření vyberte **Vytvořit** a vytvořte virtuální wan.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Vytvoření prázdného virtuálního rozbočovače

1. Pod virtuální wan, vyberte Rozbočovače a klikněte na **+New Hub**.

   ![nová lokalita](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Na stránce vytvořit virtuální rozbočovač vyplňte následující pole.

   **Oblast** – vyberte oblast, ve které chcete virtuální rozbočovač nasadit.

   **Název** – Zadejte název, který chcete volat virtuální rozbočovač.

   **Privátní adresní prostor** rozbočovače – rozsah adres centra v zápisu CIDR.

   ![nová lokalita](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Klikněte na **Zkontrolovat a vytvořit**.
4. Na stránce **ověření předaného** klikněte na **vytvořit**.

## <a name="create-a-new-p2s-configuration"></a><a name="site"></a>Vytvoření nové konfigurace P2S

Konfigurace P2S definuje parametry pro připojení vzdálených klientů.

1. V rámci virtuální sítě WAN vyberte **konfigurace uživatelské sítě VPN**.

   ![nová konfigurace](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. klepněte na tlačítko **+Vytvořit uživatelskou konfiguraci vpn .**

   ![nová konfigurace](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Zadejte informace a klikněte na **Vytvořit.**

   ![nová konfigurace](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Úprava přiřazení k rozbočovači

1. Přejděte do okna **Rozbočovače** pod virtuální sítě WAN.
2. Vyberte rozbočovač, ke kterému chcete přidružit konfiguraci serveru VPN, a klikněte na tři tečky (...).

   ![nová lokalita](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Klikněte na **Upravit virtuální rozbočovač**.
4. Zaškrtněte políčko **Zahrnout bránu bod k webu** a vyberte **požadovanou jednotku škálování brány.**

   ![nová lokalita](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Zadejte **fond adres,** ze kterého budou klientům VPN přiřazeny ip adresy.
6. Klikněte na **Confirm** (Potvrdit).
7. Operace může trvat až 30 minut.

## <a name="download-vpn-profile"></a><a name="device"></a>Stažení profilu sítě VPN

Pomocí profilu sítě VPN nakonfigurujte své klienty.

1. Na stránce virtuální sítě WAN klikněte na **Konfigurace uživatelské sítě VPN**.
2. V horní části stránky klikněte na **Stáhnout uživatelskou konfiguraci VPN**.
3. Jakmile se dokončí vytváření souboru, můžete ho kliknutím na odkaz stáhnout.
4. Pomocí souboru profilu nakonfigurujte klienty VPN.

## <a name="configure-user-vpn-clients"></a>Konfigurace klienta VPN uživatele

Chcete-li se připojit, musíte stáhnout klienta VPN Azure a importovat profil klienta VPN, který byl stažen v předchozích krocích v každém počítači, který se chce připojit k virtuální síti.

> [!NOTE]
> Ověřování Azure AD je podporované&reg; jenom pro připojení protokolu OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Stažení klienta Azure VPN

Tento [odkaz](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) použijte ke stažení klienta Azure VPN.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Import profilu klienta

1. Na stránce vyberte **Importovat**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Přejděte do souboru XML profilu a vyberte ho. S vybraným souborem vyberte **Otevřít**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Zadejte název profilu a vyberte **Uložit**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Chcete-li se připojit k síti VPN, vyberte **možnost Připojit.**

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Po připojení se ikona změní na zelenou a řekne **Připojeno**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Odstranění profilu klienta

1. Vyberte tři tečky (...) vedle profilu klienta, který chcete odstranit. Potom vyberte **Odebrat**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Vyberte **Odebrat,** chcete-li odstranit.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostika problémů s připojením

1. Chcete-li diagnostikovat problémy s připojením, můžete použít nástroj **diagnostika.** Vyberte tři tečky (...) vedle připojení VPN, které chcete diagnostikovat, abyste odhalili nabídku. Pak vyberte **Diagnostikovat**.

    ![Diagnostikovat](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Na stránce **Vlastnosti připojení** vyberte **Spustit diagnózu**.

    ![Diagnostikovat](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Přihlaste se pomocí svých přihlašovacích údajů.

    ![Diagnostikovat](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Prohlédněte si výsledky diagnostiky.

    ![Diagnostikovat](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Zobrazení virtuální sítě WAN

1. Přejděte na virtuální síť WAN.
2. Na stránce Overview (Přehled) každý bod na mapě představuje jeden rozbočovač.
3. V části Hubs and connections (Rozbočovače a připojení) můžete zjistit stav rozbočovače, lokalitu, oblast, stav připojení VPN a přijaté a odeslané bajty.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Položku myResourceGroup nahraďte názvem vaší skupiny prostředků a spusťte následující příkaz PowerShellu:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
