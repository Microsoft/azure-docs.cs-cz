---
title: Jak nakonfigurovat cloudovou službu (portál) | Dokumentace Microsoftu
description: Zjistěte, jak konfigurovat cloud services v Azure. Zjistěte, jak aktualizovat konfiguraci cloudové služby a konfigurace vzdáleného přístupu do instancí rolí. Tyto příklady pomocí webu Azure portal.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jeconnoc
ms.openlocfilehash: 904056363c685ef0a16b229ce72383eb80701a39
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006409"
---
# <a name="how-to-configure-cloud-services"></a>Postup konfigurace služby Cloud Services

Nejčastěji používaná nastavení pro cloudovou službu můžete nakonfigurovat na webu Azure Portal. Pokud chcete místo toho aktualizovat konfigurační soubory přímo, stáhněte si příslušný konfigurační soubor služby a pak ho aktualizujte a nahrajte. Tím aktualizujete konfiguraci cloudové služby. V obou případech se aktualizovaná konfigurace projeví ve všech instancích rolí.

Můžete také spravovat instance rolí cloudové služby nebo Vzdálená plocha do nich.

Azure můžete pouze zajistit 99,95 % dostupnost služeb během aktualizace konfigurace Pokud budete mít aspoň dvě instance role pro každou roli. Která umožňuje jeden virtuální počítač ke zpracování požadavků klientů druhé během aktualizace. Další informace najdete v tématu [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Změnit cloudové služby

Po otevření [webu Azure portal](https://portal.azure.com/), přejděte ke cloudové službě. Odsud můžete spravovat mnoho aspektů.

![Stránka nastavení](./media/cloud-services-how-to-configure-portal/cloud-service.png)

**Nastavení** nebo **všechna nastavení** odkazy se otevřou **nastavení** kde můžete změnit **vlastnosti**, změnit  **Konfigurace**, spravovat **certifikáty**, nastavit **pravidla upozornění**a spravovat **uživatelé** kteří mají přístup k této cloudové služby.

![Nastavení služby Azure cloud](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Správa verzí hostovaného operačního systému

Ve výchozím nastavení Azure pravidelně aktualizuje hostovaného operačního systému na nejnovější podporované image v rámci řada operačního systému, které jste zadali v konfiguraci služby (.cscfg), jako je například Windows Server 2016.

Pokud je potřeba cílit na konkrétní verze operačního systému, můžete ho nastavit v **konfigurace**.

![Nastavit verzi operačního systému](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Výběr konkrétní verze operačního systému se zakáže automatické operačního systému se aktualizuje a díky použití dílčích oprav vaší povinností. Ujistěte se, že vaše instance rolí jsou příjem aktualizací nebo může zveřejněte svou aplikaci k ohrožení bezpečnosti.

## <a name="monitoring"></a>Monitorování

Výstrahy můžete přidat ke cloudové službě. Klikněte na tlačítko **nastavení** > **pravidla upozornění** > **přidat upozornění**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Z tohoto místa můžete nastavit výstrahy. S **metrika** rozevíracího seznamu, můžete nastavit výstrahy pro následující typy dat.

* Čtení z disku
* Zápis na disk
* Sítě v
* Síťové výstupy
* Procento CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Konfigurace monitorování metrik dlaždici

Namísto použití **nastavení** > **pravidla upozornění**, můžete kliknout na jednu z metrik dlaždice v **monitorování** části cloudové služby.

![Monitorování cloudové služby](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Odsud můžete přizpůsobit graf používá s dlaždicí nebo přidání pravidla výstrahy.

## <a name="reboot-reimage-or-remote-desktop"></a>Restartování, obnovení z Image nebo vzdálené plochy

Můžete nastavit prostřednictvím vzdálené plochy [webu Azure portal (nastavení vzdálené plochy)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), nebo prostřednictvím [sady Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

K restartování, obnovení z Image nebo vzdáleně se připojte Cloudovou službu, vyberte instanci cloudové služby.

![Instance cloudové služby](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Potom iniciovat připojení ke vzdálené ploše, vzdáleně restartovat instanci nebo vzdáleně obnovit z Image (začít s novou imagí) instance.

![Tlačítka Instance cloudové služby](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Změna konfigurace vaší .cscfg

Možná budete muset změnit konfiguraci cloudové služby až [konfigurace služby (cscfg)](cloud-services-model-and-package.md#cscfg) souboru. Je třeba nejprve stáhněte si soubor .cscfg, upravte ho a pak ho nahrát.

1. Klikněte na **nastavení** ikonu nebo **všechna nastavení** odkaz otevřete **nastavení**.

    ![Stránka nastavení](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Klikněte na **konfigurace** položky.

    ![Okno Konfigurace](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Klikněte na tlačítko **Stáhnout**.

    ![Ke stažení](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Po aktualizaci konfigurační soubor služby nahrát a zavést aktualizace konfigurace:

    ![Odeslat](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Vyberte soubor .cscfg a klikněte na tlačítko **OK**.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).
* Konfigurace [vlastního názvu domény](cloud-services-custom-domain-name-portal.md).
* [Správa cloudové služby](cloud-services-how-to-manage-portal.md).
* Konfigurace [certifikáty ssl](cloud-services-configure-ssl-certificate-portal.md).
