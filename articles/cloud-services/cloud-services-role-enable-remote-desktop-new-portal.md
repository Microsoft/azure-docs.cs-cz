---
title: "Povolit připojení ke vzdálené ploše pro roli v cloudových služeb Azure | Microsoft Docs"
description: "Postup konfigurace aplikace služby azure cloud umožňující připojení ke vzdálené ploše"
services: cloud-services
documentationcenter: 
author: mmccrory
manager: timlt
editor: 
ms.assetid: 73ea1d64-1529-4d72-b58e-f6c10499e6bb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: mmccrory
ms.openlocfilehash: 2169fd95f51b468770a2e1e4c185d493babf220f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Povolit připojení ke vzdálené ploše pro roli v cloudové služby Azure

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Vzdálená plocha umožňuje přístup k ploše role, která běží v Azure. Připojení ke vzdálené ploše můžete odstraňovat a diagnostikovat problémy s aplikací, když je spuštěná.

Můžete povolit připojení ke vzdálené ploše v příslušné roli během vývoje zahrnutím moduly vzdálené plochy v definice služby nebo můžete povolit vzdálené plochy prostřednictvím vzdálené plochy rozšíření. Použití rozšíření vzdálené plochy, protože vzdálená plocha můžete povolit i poté, co je aplikace nasazena, aniž by museli znovu nasaďte aplikaci je žádoucí.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurace vzdálené plochy z portálu Azure

Portál Azure používá vzdálené plochy rozšíření přístup, takže Vzdálená plocha můžete povolit i poté, co je aplikace nasazená. **Vzdálené plochy** nastavení pro cloudové služby umožňuje povolit vzdálené plochy, změňte místní účet správce používá k připojení k virtuálním počítačům, certifikát používané v ověřování a nastavte doba vypršení platnosti datum.

1. Klikněte na tlačítko **cloudové služby**, vyberte název cloudové služby a pak vyberte **vzdálené plochy**.

    ![Cloudové služby Vzdálená plocha](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Zvolte, zda chcete povolit vzdálená plocha pro jednotlivé role nebo pro všechny role, pak změňte hodnotu přepínači na **povoleno**.

3. Vyplňte požadovaná pole pro uživatelské jméno, heslo, vypršení platnosti a certifikát.

    ![Cloudové služby Vzdálená plocha](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Všechny instance role se restartuje, při prvním povolení vzdálené plochy a vyberte **OK** (zaškrtnutí). Abyste zabránili restartování, musí být nainstalovaný certifikát použitý k šifrování hesla v roli. Abyste zabránili restartování, [nahrát certifikát pro cloudové služby](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) a pak se vraťte do tohoto dialogového okna.

4. V **role**, vyberte roli, kterou chcete aktualizovat nebo vyberte **všechny** u všech rolí.

5. Po dokončení aktualizace vaše konfigurace, vyberte **Uložit**. Bude trvat několik minut, než je připraven přijmout připojení instance role.

## <a name="remote-into-role-instances"></a>Vzdálené do instance rolí

Jakmile povolíte vzdálené plochy na rolích, můžete zahájit připojení přímo z portálu Azure:

1. Klikněte na tlačítko **instance** otevřete **instance** nastavení.
2. Vyberte instanci role, která má nakonfigurované připojení ke vzdálené ploše.
3. Klikněte na tlačítko **Connect** stáhnout soubor RDP pro instanci role.

    ![Cloudové služby Vzdálená plocha](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klikněte na tlačítko **otevřete** a potom **Connect** spustit připojení ke vzdálené ploše.

>[!NOTE]
> Pokud cloudové služby nachází za skupinu NSG, musíte vytvořit pravidla, která povolí komunikaci na portech **3389** a **20000**.  Vzdálená plocha používá port **3389**.  Instance cloudové služby jsou Vyrovnávané, takže nemůže přímo řídit kterou instanci pro připojení k.  *RemoteForwarder* a *RemoteAccess* agenty spravovat provoz protokolu RDP a umožňují klientu odesílat soubor cookie s RDP a zadejte jednotlivé instance pro připojení k.  *RemoteForwarder* a *RemoteAccess* agentů vyžadují tento port **20000*** je otevřené, což může být zablokován, pokud máte skupinu NSG.

## <a name="additional-resources"></a>Další zdroje informací:

[Jak konfigurovat Cloud Services](cloud-services-how-to-configure-portal.md)
