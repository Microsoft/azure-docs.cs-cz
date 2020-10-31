---
title: Použití portálu k povolení vzdálené plochy pro roli
titleSuffix: Azure Cloud Services
description: Konfigurace aplikace cloudové služby Azure tak, aby povolovala připojení ke vzdálené ploše
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: 507af87c3126be00a802bcbc5170f8ad364c06fc
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099313"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Povolení Připojení ke vzdálené ploše pro roli v Azure Cloud Services

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role spuštěné v Azure. Připojení ke vzdálené ploše můžete použít k řešení potíží a diagnostikování problémů s aplikací v době, kdy je spuštěná.

Můžete povolit připojení ke vzdálené ploše ve vaší roli během vývoje zahrnutím modulů vzdálené plochy do definice služby nebo můžete povolit vzdálenou plochu prostřednictvím rozšíření vzdálené plochy. Upřednostňovaným přístupem je použití rozšíření vzdálené plochy, protože můžete povolit vzdálenou plochu i po nasazení aplikace bez nutnosti opětovného nasazení aplikace.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurace vzdálené plochy z Azure Portal

Azure Portal používá přístup k rozšíření vzdálené plochy, takže můžete povolit vzdálenou plochu i po nasazení aplikace. Nastavení **vzdálené plochy** pro cloudovou službu umožňuje povolit vzdálenou plochu, změnit účet místního správce, který se používá pro připojení k virtuálním počítačům, certifikát použitý v ověřování a nastavit datum vypršení platnosti.

1. Klikněte na **Cloud Services** , vyberte název cloudové služby a pak vyberte **Vzdálená plocha** .

    ![Snímek obrazovky, který zvýrazní možnost vzdálené plochy.](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Zvolte, zda chcete povolit vzdálenou plochu pro jednotlivé role nebo pro všechny role, a pak změňte hodnotu přepínač na **povoleno** .

3. Vyplňte požadovaná pole pro uživatelské jméno, heslo, vypršení platnosti a certifikát.

    ![Služba Cloud Services Remote Desktop](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Všechny instance role se restartují při prvním povolení vzdálené plochy a výběr **OK** (zaškrtnutí). Aby nedocházelo k restartování, musí se v této roli nainstalovat certifikát používaný k zašifrování hesla. Abyste zabránili restartování, [Nahrajte certifikát pro cloudovou službu](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) a pak se vraťte do tohoto dialogu.

4. V části **role** vyberte roli, kterou chcete aktualizovat, nebo vyberte možnost **vše** pro všechny role.

5. Po dokončení aktualizací konfigurace vyberte **Uložit** . Než budou instance rolí připravené na příjem připojení, bude chvíli trvat.

## <a name="remote-into-role-instances"></a>Vzdáleně do instancí rolí

Jakmile je na rolích povolena vzdálená plocha, můžete iniciovat připojení přímo z Azure Portal:

1. Kliknutím na **instance** otevřete nastavení **instance** .
2. Vyberte instanci role, která má nakonfigurovanou vzdálenou plochu.
3. Kliknutím na **připojit** Stáhněte soubor RDP pro instanci role.

    ![Snímek obrazovky, který zvýrazní tlačítko připojit](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klikněte na **otevřít** a pak na **připojit** a spusťte připojení ke vzdálené ploše.

>[!NOTE]
> Pokud se vaše cloudová služba koná za NSG, možná budete muset vytvořit pravidla, která povolují provoz na portech **3389** a **20000** .  Vzdálená plocha používá port **3389** .  Instance cloudových služeb jsou vyrovnávány zatížení, takže nemůžete přímo určovat, ke které instanci se má připojit.  Agenti *RemoteForwarder* a *RemoteAccess* spravují provoz protokolu RDP a umožňují klientovi odeslat soubor cookie RDP a zadat jednotlivou instanci, ke které se připojí.  Agenti *RemoteForwarder* a *RemoteAccess* vyžadují, aby byl otevřený port **20000** *, který může být zablokován, pokud máte NSG.

## <a name="additional-resources"></a>Další materiály

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)
