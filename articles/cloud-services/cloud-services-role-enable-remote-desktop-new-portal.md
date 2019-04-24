---
title: Povolit připojení ke vzdálené ploše pro roli v cloudových službách Azure | Dokumentace Microsoftu
description: Jak nakonfigurovat aplikaci služby azure cloud umožňuje připojení ke vzdálené ploše
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: 73ea1d64-1529-4d72-b58e-f6c10499e6bb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: mmccrory
ms.openlocfilehash: e9e5308f63034efefc0616997301bfc1b383fd84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60527353"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Povolit připojení ke vzdálené ploše pro roli v cloudových službách Azure

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role, která běží v Azure. Připojení ke vzdálené ploše můžete použít k odstranění potíží a Diagnostikujte problémy s vaší aplikací je spuštěný.

Můžete povolit připojení ke vzdálené ploše ve vaší roli při vývoji začleněním modulů Vzdálená plocha v definici služby nebo můžete zvolit povolení vzdálené plochy prostřednictvím vzdálené plochy rozšíření. Upřednostňovaný způsob je použít rozšíření vzdálené plochy, protože vzdálená plocha můžete povolit, i když je aplikace nasazená, aniž byste museli znova nasazovat aplikaci.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurace vzdálené plochy na webu Azure Portal

Na webu Azure portal používá přístup rozšíření vzdálené plochy, takže Vzdálená plocha můžete povolit, i když je aplikace nasazená. **Vzdálené plochy** nastavení pro cloudovou službu můžete povolit vzdálenou plochu, změnit účet místního správce pro připojení k virtuálním počítačům, certifikát používat pro ověřování a nastavení vypršení platnosti datum.

1. Klikněte na tlačítko **Cloud Services**, vyberte název cloudové služby a pak vyberte **vzdálené plochy**.

    ![Cloudové služby Vzdálená plocha](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Zvolte, jestli chcete povolit vzdálenou plochu pro jednotlivé role nebo pro všechny role a potom změňte hodnotu přepínače na **povoleno**.

3. Vyplňte povinná pole pro uživatelské jméno, heslo, vypršení platnosti a certifikátu.

    ![Cloudové služby Vzdálená plocha](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Všechny instance rolí se restartuje, když nejdřív povolit vzdálenou plochu a vyberte **OK** (zaškrtnutí). Pokud chcete zabránit restartování, certifikát používaný k šifrování hesla musí být nainstalovaný v roli. Abyste zabránili restartování, [nahrát na server certifikát pro cloudovou službu](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) a pak se vraťte do tohoto dialogu.

4. V **role**, vyberte roli, kterou chcete aktualizovat, nebo vyberte **všechny** u všech rolí.

5. Po dokončení konfigurace aktualizace, vyberte **Uložit**. Bude trvat několik minut, než je vaše instance rolí jsou připraveni získat připojení.

## <a name="remote-into-role-instances"></a>Vzdáleně se připojte k instancím rolí

Jakmile na rolích je povolená Vzdálená plocha, může iniciovat připojení přímo z portálu Azure portal:

1. Klikněte na tlačítko **instance** otevřít **instance** nastavení.
2. Vyberte, která má nakonfigurované ke vzdálené ploše instance role.
3. Klikněte na tlačítko **připojit** chcete stáhnout soubor RDP pro instanci role.

    ![Cloudové služby Vzdálená plocha](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klikněte na tlačítko **otevřít** a potom **připojit** spustit připojení ke vzdálené ploše.

>[!NOTE]
> Pokud vaše Cloudová služba se nachází za skupinu zabezpečení sítě, budete muset vytvořit pravidla, která umožňují přenosy na portech **3389** a **20000**.  Vzdálené plochy používá port **3389**.  Instance cloudové služby jsou Vyrovnávané, takže nemůže přímo řídí kterou instanci chcete připojit k.  *RemoteForwarder* a *RemoteAccess* agenty spravovat provoz protokolu RDP a povolit klienta k odeslání do souboru cookie protokolu RDP a zadejte jednotlivé instance pro připojení k.  *RemoteForwarder* a *RemoteAccess* agentů vyžadují tento port **20000*** je otevřít, což může být blokován, pokud máte skupinu zabezpečení sítě.

## <a name="additional-resources"></a>Další materiály

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)
