---
title: Jak nakonfigurovat cloudovou službu (portál) | Microsoft Docs
description: Naučte se konfigurovat cloudové služby v Azure. Naučte se aktualizovat konfiguraci cloudové služby a nakonfigurovat vzdálený přístup k instancím rolí. V těchto příkladech se používá Azure Portal.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: tagore
ms.openlocfilehash: 4f2b5716e253f2810ff953a10c879574c6a6c342
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576655"
---
# <a name="how-to-configure-cloud-services"></a>Jak konfigurovat Cloud Services

V Azure Portal můžete nakonfigurovat nejčastěji používaná nastavení pro cloudovou službu. Nebo pokud chcete aktualizovat konfigurační soubory přímo, Stáhněte si konfigurační soubor služby, který chcete aktualizovat, a potom aktualizovaný soubor nahrajte a aktualizujte cloudovou službu pomocí změn konfigurace. V obou případech jsou aktualizace konfigurace vloženy do všech instancí role.

Můžete také spravovat instance rolí cloudové služby nebo vzdálené plochy.

V případě, že pro každou roli máte aspoň dvě instance rolí, může Azure v rámci aktualizací konfigurace zajistit jenom 99,95% dostupnost služby. To umožňuje, aby jeden virtuální počítač zpracovával požadavky klienta při aktualizaci druhého. Další informace najdete v tématu [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Změna cloudové služby

Po otevření [Azure Portal](https://portal.azure.com/)přejděte do své cloudové služby. Tady můžete spravovat spoustu aspektů.

![Stránka nastavení](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Odkaz **Nastavení** nebo **všechna nastavení** otevřou **Nastavení** , kde můžete změnit **vlastnosti**, změnit **konfiguraci**, spravovat **certifikáty**, nastavit **pravidla upozornění**a spravovat **uživatele** , kteří mají k této cloudové službě přístup.

![Nastavení cloudové služby Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Spravovat verzi hostovaného operačního systému

Ve výchozím nastavení Azure pravidelně aktualizuje hostovaný operační systém na nejnovější podporovanou image v rámci řady operačních systémů, kterou jste zadali v konfiguraci služby (. cscfg), jako je například Windows Server 2016.

Pokud potřebujete cílit na konkrétní verzi operačního systému, můžete ji nastavit v **konfiguraci**.

![Nastavit verzi operačního systému](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Výběr konkrétní verze operačního systému zakáže automatické aktualizace operačního systému a provede opravu vaší zodpovědnosti. Musíte zajistit, aby vaše instance role přijímaly aktualizace, nebo můžete svou aplikaci vystavit chybám zabezpečení.

## <a name="monitoring"></a>Monitorování

Do své cloudové služby můžete přidat výstrahy. Klikněte na **Nastavení**  >  **pravidla výstrahy**  >  **Přidat výstrahu**.

![Snímek obrazovky s nastavením, který se zvýrazní pomocí možnosti pravidla upozornění, je zvýrazněný a zobrazený červeně a možnost Přidat výstrahu popsaný červeně.](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Tady můžete nastavit výstrahu. Pomocí rozevíracího seznamu **metrika** můžete nastavit výstrahu pro následující typy dat.

* Čtení z disku
* Zápis na disk
* Síťové vstupy
* Síťové výstupy
* Procento CPU

![Snímek obrazovky s podoknem přidat pravidlo výstrahy se sadou možností konfigurace](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Konfigurace monitorování z dlaždice metriky

Místo používání **Nastavení**  >  **pravidla upozornění**můžete kliknout na jeden z dlaždic metriky v části **monitorování** v cloudové službě.

![Monitorování cloudové služby](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Z tohoto místa můžete přizpůsobit graf, který se používá pro dlaždici, nebo přidat pravidlo výstrahy.

## <a name="reboot-reimage-or-remote-desktop"></a>Restartování, obnovení Image nebo Vzdálená plocha

Vzdálenou plochu můžete nastavit prostřednictvím [Azure Portal (nastavení vzdálené plochy)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShellu](cloud-services-role-enable-remote-desktop-powershell.md)nebo pomocí sady [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Pokud chcete restartovat, obnovit image nebo vzdálené úložiště do cloudové služby, vyberte instanci cloudové služby.

![Instance cloudové služby](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Pak můžete iniciovat připojení ke vzdálené ploše, vzdáleně restartovat instanci nebo vzdáleně obnovit image (začít s čerstvou imagí) instance.

![Tlačítka instance cloudové služby](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Překonfigurujte svůj. cscfg.

Možná budete muset znovu nakonfigurovat cloudovou službu prostřednictvím souboru [služby konfigurace (cscfg)](cloud-services-model-and-package.md#cscfg) . Nejdřív je potřeba stáhnout soubor. cscfg, upravit ho a pak ho nahrát.

1. Kliknutím na ikonu **Nastavení** nebo na odkaz **všechna nastavení** otevřete **Nastavení**.

    ![Stránka nastavení](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Klikněte na položku **Konfigurace** .

    ![Okno Konfigurace](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Klikněte na tlačítko **Stáhnout**.

    ![Stáhnout](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Po aktualizaci konfiguračního souboru služby nahrajte a nainstalujte aktualizace konfigurace:

    ![Nahrávání](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Vyberte soubor. cscfg a klikněte na **OK**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).
* Nakonfigurujte [vlastní název domény](cloud-services-custom-domain-name-portal.md).
* [Spravujte svou cloudovou službu](cloud-services-how-to-manage-portal.md).
* Nakonfigurujte [certifikáty TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).



