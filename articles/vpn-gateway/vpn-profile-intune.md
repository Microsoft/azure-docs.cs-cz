---
title: Vytvoření profilu Intune pro klienty VPN Azure
titleSuffix: Azure VPN Gateway
description: Naučte se vytvořit vlastní profil Intune pro nasazení profilů klientů Azure VPN.
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/15/2020
ms.author: cherylmc
ms.openlocfilehash: 63b1d7f95711017feba52cad97f05128d83734da
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655166"
---
# <a name="create-an-intune-profile-to-deploy-vpn-client-profiles"></a>Vytvoření profilu Intune pro nasazení profilů klientů VPN

Profily pro klienty Azure VPN (Windows 10) můžete nasadit pomocí Microsoft Intune. Tento článek vám pomůže vytvořit profil Intune s využitím vlastních nastavení.

> [!NOTE]
> Tato metoda bude fungovat jenom v případě, že nasazujete profily, které používají Azure Active Directory, nebo společný certifikát pro ověřování klientů. Pokud se používají jedinečné klientské certifikáty, bude muset každý uživatel vybrat správný certifikát ručně v rámci klienta Azure VPN.
>

## <a name="prerequisites"></a>Požadavky

* Zařízení jsou už zaregistrovaná ve službě Intune MDM.
* Klient Azure VPN pro Windows 10 je už na klientském počítači nasazený.
* Podporovaná je jenom verze Windows 19H2 nebo vyšší.

## <a name="modify-xml"></a><a name="xml"></a>Upravit XML

V následujících krocích použijeme ukázkový kód XML pro vlastní profil OMA-URI pro Intune s následujícím nastavením:

* Automatické připojení ZAPNUTo
* Bylo povoleno zjišťování důvěryhodných sítí.

Další podporované možnosti najdete v článku [CSP pro podpora vpnv2](/windows/client-management/mdm/vpnv2-csp) .

1. Stáhněte si profil sítě VPN z Azure Portal a extrahujte soubor *azurevpnconfig.xml* z balíčku.
1. Zkopírujte a vložte text uvedený níže do nového souboru textového editoru.

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>

        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. Upravte záznam mezi a položkou ```<ServerUrlList>``` ```</ServerUrlList>``` ze staženého profilu (azurevpnconfig.xml). Změňte plně kvalifikovaný název domény "TrustedNetworkDetection" tak, aby odpovídal vašemu prostředí.
1. Otevřete stažený profil Azure (azurevpnconfig.xml) a zkopírujte celý obsah do schránky zvýrazněním textu a stisknutím klávesy (CTRL) + C. 
1. Vložte zkopírovaný text z předchozího kroku do souboru, který jste vytvořili v kroku 2 mezi ```<CustomConfiguration>  </CustomConfiguration>``` značkami. Uložte soubor s příponou XML.
1. Zapište hodnotu do ```<name>  </name>``` značek. Toto je název profilu. Tento název budete potřebovat při vytváření profilu v Intune. Zavřete soubor a zapamatujte si umístění, kam se uložilo.

## <a name="create-intune-profile"></a>Vytvořit profil Intune

V této části vytvoříte profil Microsoft Intune s vlastním nastavením.

1. Přihlaste se k Intune a přejděte na **zařízení – > konfigurační profily**. Vyberte **+ vytvořit profil**.

   :::image type="content" source="./media/create-profile-intune/configuration-profile.png" alt-text="Konfigurační profily":::
1. V části **platforma** vyberte **Windows 10 a novější**. Jako **profil** vyberte **vlastní**. Potom vyberte **Vytvořit**.
1. Zadejte název a popis profilu a pak vyberte **Další**.
1. Na kartě **nastavení konfigurace** vyberte **Přidat**.

    * **Název:** Zadejte název konfigurace.
    * **Popis:** Volitelný popis.
    * **OMA-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (tyto informace najdete v souboru azurevpnconfig.xml ve <name></name> značce).
    * **Datový typ:** Řetězec (soubor XML).

   Vyberte ikonu složky a vyberte soubor, který jste uložili v kroku 6 v krocích [XML](#xml) . Vyberte možnost **Přidat**.

   :::image type="content" source="./media/create-profile-intune/configuration-settings.png" alt-text="Nastavení konfigurace" lightbox="./media/create-profile-intune/configuration-settings.png":::
1. Vyberte **Další**.
1. V části **přiřazení** vyberte skupinu, do které chcete doručovat konfiguraci. Pak vyberte **Další**.
1. Pravidla použitelnosti jsou volitelná. V případě potřeby definujte jakákoli pravidla a pak vyberte **Další**.
1. Na stránce **Revize + vytvořit** vyberte **vytvořit**.

    :::image type="content" source="./media/create-profile-intune/create-profile.png" alt-text="Vytvořit profil":::
1. Váš vlastní profil se teď vytvoří. Microsoft Intune postup nasazení tohoto profilu najdete v tématu [přiřazení profilů uživatelů a zařízení](/mem/intune/configuration/device-profile-assign).
 
## <a name="next-steps"></a>Další kroky

Další informace o Point-to-site najdete v tématu [informace o Point-to-site](point-to-site-about.md).