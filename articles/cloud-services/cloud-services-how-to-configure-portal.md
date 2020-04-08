---
title: Jak nakonfigurovat cloudovou službu (portál) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat cloudové služby v Azure. Naučte se aktualizovat konfiguraci cloudové služby a nakonfigurovat vzdálený přístup k instancím rolí. Tyto příklady používají portál Azure.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: tagore
ms.openlocfilehash: e862818a4fe2471af574d153d43f0096af7847b8
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811414"
---
# <a name="how-to-configure-cloud-services"></a>Jak konfigurovat Cloud Services

Nejčastěji používaná nastavení pro cloudovou službu můžete nakonfigurovat na webu Azure Portal. Pokud chcete konfigurační soubory aktualizovat přímo, stáhněte konfigurační soubor služby, který chcete aktualizovat, a potom nahrajte aktualizovaný soubor a aktualizujte cloudovou službu změnami konfigurace. V obou případech jsou aktualizace konfigurace vytlačovány do všech instancí rolí.

Můžete také spravovat instance rolí cloudových služeb nebo vzdálené plochy do nich.

Azure může zajistit dostupnost služby 99,95 procenta během aktualizací konfigurace, pokud máte alespoň dvě instance role pro každou roli. To umožňuje jednomu virtuálnímu počítači zpracovávat požadavky klientů, zatímco druhý je aktualizován. Další informace naleznete v [tématu Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Změna cloudové služby

Po otevření [portálu Azure](https://portal.azure.com/)přejděte na cloudovou službu. Odtud se vám podaří mnoho aspektů.

![Stránka Nastavení](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Odkazy **Nastavení** nebo **Všechna nastavení** se otevřou **Nastavení,** ve kterém můžete změnit **vlastnosti**, změnit **konfiguraci**, spravovat **certifikáty**, nastavit **pravidla výstrah**a spravovat **uživatele,** kteří mají přístup k této cloudové službě.

![Nastavení cloudové služby Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Správa verze hostovaného operačního systému

Ve výchozím nastavení Azure pravidelně aktualizuje váš hostovaný operační systém na nejnovější podporovanou bitovou kopii v rámci řady operačních služeb, kterou jste zadali v konfiguraci služby (.cscfg), jako je Například Windows Server 2016.

Pokud potřebujete cílit na konkrétní verzi operačního systému, můžete ji nastavit v **konfiguraci**.

![Nastavení verze operačního systému](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Výběr konkrétní verze operačního systému zakáže automatické aktualizace operačního systému a provede opravu vaší odpovědnosti. Je nutné zajistit, aby instance rolí přijímaly aktualizace, jinak můžete aplikaci vystavit chybám zabezpečení.

## <a name="monitoring"></a>Monitorování

Do cloudové služby můžete přidat upozornění. Klepněte na **tlačítko Nastavení** > **pravidel výstrah Přidat** > **výstrahu**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Odtud můžete nastavit výstrahu. V rozevíracím poli **Metrika** můžete nastavit výstrahu pro následující typy dat.

* Čtení na disku
* Zápis na disk
* Síť v
* Síť je venku
* Procento CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Konfigurace monitorování z metrické dlaždice

Místo **použití** > **nastavení pravidel upozornění**můžete kliknout na jednu z dlaždic metriky v části **Monitorování** cloudové služby.

![Monitorování cloudových služeb](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Zde můžete přizpůsobit graf použitý s dlaždicí nebo přidat pravidlo výstrahy.

## <a name="reboot-reimage-or-remote-desktop"></a>Restartování, reimage nebo vzdálená plocha

Vzdálenou plochu můžete nastavit prostřednictvím [portálu Azure (nastavení vzdálené plochy),](cloud-services-role-enable-remote-desktop-new-portal.md) [PowerShellu](cloud-services-role-enable-remote-desktop-powershell.md)nebo prostřednictvím [sady Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Chcete-li restartovat, reimage nebo vzdálené do cloudové služby, vyberte instanci cloudové služby.

![Instance cloudové služby](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Potom můžete zahájit připojení ke vzdálené ploše, vzdáleně restartovat instanci nebo vzdáleně reimage (začít s čerstvým obrázkem) instance.

![Tlačítka instance služby Cloud](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Změna konfigurace rozhraní .cscfg

Možná budete muset překonfigurovat cloudovou službu prostřednictvím souboru [konfigurace služby (cscfg).](cloud-services-model-and-package.md#cscfg) Nejprve je třeba stáhnout soubor .cscfg, upravit jej a pak jej nahrát.

1. Kliknutím na ikonu **Nastavení** nebo na odkaz **Všechna nastavení** otevřete **nastavení**.

    ![Stránka Nastavení](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Klikněte na položku **Konfigurace.**

    ![Konfigurační čepel](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Klikněte na tlačítko **Stáhnout**.

    ![Stáhnout](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Po aktualizaci konfiguračního souboru služby nahrajte a aplikujte aktualizace konfigurace:

    ![Odeslat](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Vyberte soubor CSCFG a klepněte na tlačítko **OK**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).
* Konfigurace [vlastního názvu domény](cloud-services-custom-domain-name-portal.md).
* [Spravujte cloudovou službu](cloud-services-how-to-manage-portal.md).
* Konfigurace [certifikátů TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).



