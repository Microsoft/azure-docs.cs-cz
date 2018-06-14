---
title: Postup konfigurace cloudové služby (portál) | Microsoft Docs
description: Zjistěte, jak nakonfigurovat cloudové služby v Azure. Naučte se aktualizovat konfiguraci této cloudové služby a konfigurace vzdáleného přístupu k instancí rolí. Tyto příklady použití portálu Azure.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: adegeo
ms.openlocfilehash: 255e496881f6269d37d3b2d982ba31861458631c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
ms.locfileid: "29874317"
---
# <a name="how-to-configure-cloud-services"></a>Postup konfigurace cloudové služby

Můžete nakonfigurovat nastavení nejčastěji používaných pro cloudové služby na portálu Azure. Pokud chcete místo toho aktualizovat konfigurační soubory přímo, stáhněte si příslušný konfigurační soubor služby a pak ho aktualizujte a nahrajte. Tím aktualizujete konfiguraci cloudové služby. V obou případech se aktualizovaná konfigurace projeví ve všech instancích rolí.

Můžete také spravovat instance role cloudové služby nebo vzdálené plochy do nich.

Azure můžete pouze zajistit dostupnost služby 99,95 % během aktualizace konfigurace Pokud máte aspoň dvě instance role pro každou roli. Umožňující jeden virtuální počítač ke zpracování požadavků klientů dalších během aktualizace. Další informace najdete v tématu [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Změnit cloudové služby

Po otevření [portál Azure](https://portal.azure.com/), přejděte do cloudové služby. Tady můžete spravovat mnoho aspektů.

![Nastavení stránky](./media/cloud-services-how-to-configure-portal/cloud-service.png)

**Nastavení** nebo **všechna nastavení** odkazy se otevře **nastavení** kterém lze změnit **vlastnosti**, změnit  **Konfigurace**, spravovat **certifikáty**, nastavte **výstrah pravidla**a spravovat **uživatelé** kteří mají přístup k této cloudové služby.

![Nastavení služby Azure cloud](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Správa verzí hostovaného operačního systému

Ve výchozím nastavení Azure pravidelně aktualizuje hostovaný operační systém na nejnovější podporovanou bitovou kopii v rámci řada operačního systému, který jste zadali v konfiguraci služby (.cscfg), například Windows Server 2016.

Pokud budete se muset zaměřit na konkrétní verzi operačního systému, můžete ho nastavit **konfigurace**.

![Nastavit verzi operačního systému](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Výběr konkrétní verze operačního systému zakáže automatické OS aktualizace a zajišťuje opravy vaší povinností. Ujistěte se, že instance role jsou přijímá aktualizace nebo může odhalí aplikace k ohrožení zabezpečení.

## <a name="monitoring"></a>Monitorování

Výstrahy můžete přidat do cloudové služby. Klikněte na tlačítko **nastavení** > **pravidla výstrahy** > **přidat upozornění**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Zde můžete nastavit výstrahy. Pomocí **metrika** rozevíracího seznamu, můžete nastavit upozornění pro následující typy dat.

* Čtení z disku
* Zápis disku
* Sítě v
* Sítě out
* Procento CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Konfigurace monitorování z metriky dlaždice

Místo použití **nastavení** > **pravidla výstrah**, můžete kliknutím na jednu z metriky dlaždice v **monitorování** části cloudové služby.

![Cloudová služba monitorování](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Tady můžete přizpůsobit graf použít s dlaždice nebo přidání pravidla výstrahy.

## <a name="reboot-reimage-or-remote-desktop"></a>Restartování, obnovení z Image nebo vzdálené plochy

Můžete nastavit vzdálené plochy prostřednictvím [portálu Azure (nastavení vzdálené plochy)](cloud-services-role-enable-remote-desktop-new-portal.md), [prostředí PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), nebo pomocí [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Restartování, obnovení z Image nebo vzdálené do cloudové služby, vyberte instanci služby cloudu.

![Instance cloudové služby](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Potom iniciovat připojení ke vzdálené ploše, vzdáleně restartovat instanci nebo vzdáleně obnovit z Image (začít s novou bitovou kopii) instance.

![Cloudové služby Instance tlačítka](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Překonfigurujte vaší .cscfg

Budete muset překonfigurovat cloudové služby prostřednictvím [konfigurace služby (cscfg)](cloud-services-model-and-package.md#cscfg) souboru. Je třeba nejprve stáhněte soubor .cscfg, upravte ho a pak nahrajte ho.

1. Klikněte na **nastavení** ikonu nebo **všechna nastavení** odkaz otevře **nastavení**.

    ![Nastavení stránky](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Klikněte na **konfigurace** položky.

    ![Okno Konfigurace](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Klikněte na tlačítko **Stáhnout**.

    ![Ke stažení](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Po aktualizaci konfigurační soubor služby, odesílání a aktualizace konfigurace:

    ![Odeslat](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Vyberte soubor .cscfg a klikněte na tlačítko **OK**.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [nasazení cloudové služby](cloud-services-how-to-create-deploy-portal.md).
* Konfigurace [vlastní název domény](cloud-services-custom-domain-name-portal.md).
* [Správa služby cloud](cloud-services-how-to-manage-portal.md).
* Konfigurace [certifikáty ssl](cloud-services-configure-ssl-certificate-portal.md).
