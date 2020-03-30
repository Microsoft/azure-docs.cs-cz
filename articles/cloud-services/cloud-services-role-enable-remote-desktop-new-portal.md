---
title: Povolení funkce Vzdálená plocha pro roli pomocí portálu
titleSuffix: Azure Cloud Services
description: Jak nakonfigurovat aplikaci azure cloudové služby tak, aby umožňovala připojení ke vzdálené ploše
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: d65f4b55be317234c10a0e90cfe413d9e38a6a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247459"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Povolení připojení ke vzdálené ploše pro roli v cloudových službách Azure

> [!div class="op_single_selector"]
> * [Portál Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role spuštěné v Azure. Připojení ke vzdálené ploše můžete použít k řešení a diagnostice problémů s aplikací, když je spuštěna.

Připojení ke vzdálené ploše můžete během vývoje povolit zahrnutím modulů vzdálené plochy do definice služby nebo můžete povolit funkci Vzdálená plocha prostřednictvím rozšíření Vzdálená plocha. Upřednostňovaným přístupem je použití rozšíření Vzdálená plocha, protože můžete povolit vzdálenou plochu i po nasazení aplikace bez nutnosti opětovného nasazení aplikace.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurace vzdálené plochy z portálu Azure

Portál Azure používá přístup rozšíření vzdálené plochy, takže můžete povolit vzdálenou plochu i po nasazení aplikace. Nastavení **vzdálené plochy** pro cloudovou službu umožňuje povolit vzdálenou plochu, změnit místní účet správce používaný pro připojení k virtuálním počítačům, certifikát použitý při ověřování a nastavit datum vypršení platnosti.

1. Klepněte na **položku Cloud Services**, vyberte název cloudové služby a vyberte **položku Vzdálená plocha**.

    ![Vzdálená plocha cloudových služeb](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Zvolte, zda chcete povolit vzdálenou plochu pro jednotlivé role nebo pro všechny role, a pak změňte hodnotu přepínače na **Povoleno**.

3. Vyplňte požadovaná pole pro uživatelské jméno, heslo, vypršení platnosti a certifikát.

    ![Vzdálená plocha cloudových služeb](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Všechny instance rolí budou restartovány při prvním povolení vzdálené plochy a vyberte **ok** (zaškrtnutí). Chcete-li zabránit restartování, musí být v roli nainstalován certifikát použitý k šifrování hesla. Chcete-li zabránit restartování, [nahrajte certifikát pro cloudovou službu](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) a vraťte se do tohoto dialogového okna.

4. V **části Role**vyberte roli, kterou chcete aktualizovat, nebo vyberte **Vše** pro všechny role.

5. Po dokončení aktualizací konfigurace vyberte **uložit**. Bude trvat několik okamžiků, než budou instance vaší role připraveny přijímat připojení.

## <a name="remote-into-role-instances"></a>Vzdálené do instancí rolí

Jakmile je u rolí povolena vzdálená plocha, můžete navolit připojení přímo z portálu Azure:

1. Kliknutím na **Instance** otevřete nastavení **Instance.**
2. Vyberte instanci role, která má nakonfigurovanou vzdálenou plochu.
3. Kliknutím na **Připojit** stáhnete soubor RDP pro instanci role.

    ![Vzdálená plocha cloudových služeb](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klepnutím na tlačítko **Otevřít** a potom na **tlačítko Připojit** spusťte připojení ke vzdálené ploše.

>[!NOTE]
> Pokud vaše cloudová služba sedí za nsg, budete muset vytvořit pravidla, která umožňují provoz na portech **3389** a **20000**.  Vzdálená plocha používá port **3389**.  Instance cloudové služby jsou s vyrovnáváním zatížení, takže nemůžete přímo řídit, ke které instanci se chcete připojit.  Agenti *RemoteForwarder* a *RemoteAccess* spravují provoz RDP a umožňují klientovi odeslat soubor cookie RDP a zadat jednotlivé instance, ke které se chcete připojit.  Agenti *RemoteForwarder* a *RemoteAccess* vyžadují, aby byl otevřen port **20000*** , který může být blokován, pokud máte soubor nSG.

## <a name="additional-resources"></a>Další zdroje

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)
