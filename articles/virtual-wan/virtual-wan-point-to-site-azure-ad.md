---
title: 'Konfigurace ověřování Azure AD pro připojení VPN uživatele: virtuální síť WAN'
description: Přečtěte si, jak nakonfigurovat ověřování Azure Active Directory pro uživatele VPN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alzam
ms.openlocfilehash: 1102e2dafcf1a78bc9c243f27549b13793ec5408
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079169"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Konfigurace ověřování Azure Active Directory pro uživatele VPN

V tomto článku se dozvíte, jak nakonfigurovat ověřování Azure AD pro uživatele VPN ve virtuální síti WAN, aby se připojily k prostředkům v Azure prostřednictvím připojení VPN OpenVPN. Ověřování Azure Active Directory je k dispozici pouze pro brány pomocí protokolu OpenVPN a klientů se systémem Windows.

Tento typ připojení vyžaduje, aby byl na klientském počítači nakonfigurovaný klient. Další informace o službě Virtual WAN najdete v tématu [Přehled služby Virtual WAN](virtual-wan-about.md).

V tomto článku získáte informace o těchto tématech:

* Vytvoření virtuální sítě WAN
* Vytvořit virtuální rozbočovač
* Vytvoření konfigurace sítě VPN uživatele
* Stažení profilu sítě VPN uživatele virtuální sítě WAN
* Použít konfiguraci VPN uživatele na virtuálním rozbočovači
* Připojení virtuální sítě k virtuálnímu rozbočovači
* Stažení a použití konfigurace klienta VPN uživatele
* Zobrazení virtuální sítě WAN

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Máte virtuální síť, ke které se chcete připojit. Ověřte, že se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť v Azure Portal, přečtěte si [rychlý Start](../virtual-network/quick-create-portal.md).

* Vaše virtuální síť nemá žádné brány virtuální sítě. Pokud má vaše virtuální síť bránu (buď VPN, nebo ExpressRoute), musíte odebrat všechny brány. Tato konfigurace vyžaduje, aby se virtuální sítě místo toho připojovaly k virtuální bráně WAN hub.

* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Centrum je virtuální síť, kterou vytváří a používá virtuální síť WAN. Rozsah adres, který zadáte pro centrum, se nemůže překrývat s žádnou ze stávajících virtuálních sítí, ke kterým se připojujete. Taky se nesmí překrývat s rozsahy adres, ke kterým se připojujete v místním prostředí. Pokud neznáte rozsahy IP adres nacházející se v konfiguraci vaší místní sítě, zajistěte koordinaci s někým, kdo vám poskytne tyto podrobnosti.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Vytvoření virtuální sítě WAN

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

1. Přejděte na stránku Virtual WAN. Na portálu klikněte na **+ Vytvořit prostředek**. Do vyhledávacího pole zadejte **virtuální síť WAN** a vyberte Enter.
2. Z výsledků vyberte **virtuální síť WAN** . Na stránce virtuální síť WAN kliknutím na **vytvořit** otevřete stránku vytvořit síť WAN.
3. Na stránce **vytvořit síť WAN** na kartě **základy** vyplňte následující pole:

   ![Virtual WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Subscription** (Předplatné) – vyberte předplatné, které chcete použít.
   * **Skupina prostředků** – vytvořte nové nebo použijte existující.
   * **Umístění skupiny prostředků** – vyberte umístění prostředku z rozevíracího seznamu. Síť WAN je globální prostředek, takže se nenachází v určité oblasti. Přesto je ale potřeba oblast vybrat, abyste mohli snáz spravovat a vyhledávat prostředek sítě WAN, který vytvoříte.
   * **Název** – zadejte název, který chcete zavolat do sítě WAN.
   * **Zadejte:** Standardní. Pokud vytvoříte základní síť WAN, můžete vytvořit jenom základní centrum. Základní centra se podporují jenom pro připojení VPN typu Site-to-site.
4. Po dokončení vyplňování polí vyberte **zkontrolovat + vytvořit**.
5. Po úspěšném ověření vyberte **vytvořit** a vytvořte virtuální síť WAN.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Vytvořit prázdné virtuální rozbočovač

1. V části virtuální síť WAN vyberte centra a klikněte na **+ nové centrum**.

   ![nová lokalita](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Na stránce vytvořit virtuální rozbočovač vyplňte následující pole.

   **Oblast** – vyberte oblast, do které chcete nasadit virtuální rozbočovač.

   **Název** – zadejte název, který chcete zavolat do svého virtuálního rozbočovače.

   **Privátní adresní prostor centra** – rozsah adres rozbočovače v zápisu CIDR.

   ![nová lokalita](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Klikněte na **Zkontrolovat a vytvořit**.
4. Na stránce **ověřování proběhlo úspěšně** klikněte na **vytvořit**.

## <a name="create-a-new-user-vpn-configuration"></a><a name="site"></a>Vytvořit novou konfiguraci sítě VPN uživatele

Konfigurace sítě VPN uživatele definuje parametry pro připojení vzdálených klientů.

1. V části virtuální síť WAN vyberte **Konfigurace sítě VPN uživatele**.

   ![Nová konfigurace](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. klikněte na **+ vytvořit uživatelskou konfiguraci sítě VPN**.

   ![Nová konfigurace](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Zadejte informace a klikněte na **vytvořit**.

   * **Název konfigurace** – zadejte název, kterým chcete zavolat konfiguraci sítě VPN uživatele.
   * **Typ tunelu** – vyberte OpenVPN.
   * **Metoda ověřování** – vyberte Azure Active Directory.
   * **Cílová skupina** – zadejte ID aplikace podnikové aplikace [Azure](openvpn-azure-ad-tenant.md) , která je zaregistrovaná ve vašem tenantovi Azure AD. 
   * **Stavil** - `https://sts.windows.net/<your Directory ID>/`
   * **Tenant AAD** - `https://login.microsoftonline.com/<your Directory ID>`
  


   ![Nová konfigurace](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Úprava přiřazení k rozbočovači

1. Přejděte do okna **centra** v rámci virtuální sítě WAN.
2. Vyberte centrum, ke kterému chcete přiřadit konfiguraci serveru VPN, a klikněte na tlačítko se třemi tečkami (...).

   ![nová lokalita](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Klikněte na **Upravit virtuální rozbočovač**.
4. Zaškrtněte políčko **Zahrnout bránu Point-to-site** a vyberte **jednotku škálování brány** , kterou chcete použít.

   ![nová lokalita](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Zadejte **fond adres** , ze kterého budou klienti VPN přiřazeni IP adresy.
6. Klikněte na **Confirm** (Potvrdit).
7. Dokončení operace může trvat až 30 minut.

## <a name="download-user-vpn-profile"></a><a name="device"></a>Stáhnout profil VPN uživatele

Pomocí profilu sítě VPN nakonfigurujte své klienty.

1. Na stránce pro virtuální síť WAN klikněte na **Konfigurace sítě VPN uživatele**.
2. V horní části stránky klikněte na **Stáhnout uživatelskou konfiguraci VPN**.
3. Jakmile se dokončí vytváření souboru, můžete ho kliknutím na odkaz stáhnout.
4. K nakonfigurování klientů VPN použijte profilový soubor.

## <a name="configure-user-vpn-clients"></a>Konfigurace klientů VPN uživatelů

Abyste se mohli připojit, je potřeba stáhnout klienta Azure VPN a importovat profil klienta VPN, který jste stáhli v předchozích krocích v každém počítači, který se chce připojit k virtuální síti.

> [!NOTE]
> Ověřování Azure AD se podporuje jenom pro &reg; připojení protokolu OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Stažení klienta Azure VPN

Pomocí tohoto [odkazu](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) si stáhněte klienta Azure VPN.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Import profilu klienta

1. Na stránce vyberte **importovat**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Přejděte k souboru XML profilu a vyberte ho. Když je vybraný soubor, vyberte **otevřít**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Zadejte název profilu a vyberte **Uložit**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Vyberte **připojit** a připojte se k síti VPN.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Po připojení se ikona změní na zelenou a znamená se **připojit**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Odstranění profilu klienta

1. Vyberte tři tečky (...) vedle profilu klienta, který chcete odstranit. Pak vyberte **Odebrat**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Vyberte **Odebrat** a odstraňte.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostika problémů s připojením

1. K diagnostice problémů s připojením můžete použít nástroj pro **diagnostiku** . Vyberte tři tečky (...) vedle připojení VPN, které chcete diagnostikovat, aby se nabídka zobrazila. Pak vyberte **Diagnostika**.

    ![diagnóz](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Na stránce **Vlastnosti připojení** vyberte **Spustit diagnostiku**.

    ![diagnóz](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Přihlaste se pomocí svých přihlašovacích údajů.

    ![diagnóz](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Zobrazení výsledků diagnostiky.

    ![diagnóz](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Zobrazení virtuální sítě WAN

1. Přejděte na virtuální síť WAN.
2. Na stránce Overview (Přehled) každý bod na mapě představuje jeden rozbočovač.
3. V části Hubs and connections (Rozbočovače a připojení) můžete zjistit stav rozbočovače, lokalitu, oblast, stav připojení VPN a přijaté a odeslané bajty.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Položku myResourceGroup nahraďte názvem vaší skupiny prostředků a spusťte následující příkaz PowerShellu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
