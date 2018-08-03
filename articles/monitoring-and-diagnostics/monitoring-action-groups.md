---
title: Vytvoření a Správa skupin akcí na webu Azure Portal
description: Zjistěte, jak vytvářet a spravovat skupiny akcí na webu Azure Portal.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/1/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 091a097fc9fafd5bdc6a2521f4fa2a1b6b77ba4c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422549"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Vytvoření a Správa skupin akcí na webu Azure Portal
## <a name="overview"></a>Přehled ##
Skupiny akcí je kolekce předvolby oznamování definovaný uživatelem. Upozornění Azure Monitor a stavy služeb jsou nakonfigurovány pro použití skupinu určité akce, když se aktivuje upozornění. Různé výstrahy použít stejnou skupinu akcí nebo skupiny různých akcí v závislosti na požadavcích uživatele.

V tomto článku se dozvíte, jak vytvořit a spravovat skupiny akcí na webu Azure Portal.

Každá akce se skládá z následujících vlastností:

* **Název**: Jedinečný identifikátor v rámci skupiny akcí.  
* **Typ akce**: odesílat hlasový hovor nebo SMS, odeslat e-mailu, volání webhooku, odesílání dat k nástroji ITSM, volat aplikaci logiky, odesílání nabízených oznámení do aplikace Azure nebo spustit runbook služby Automation.
* **Podrobnosti o**: odpovídající telefonního čísla, e-mailovou adresu, webhooku identifikátor URI nebo podrobnosti připojení ITSM.

Informace o tom, jak nakonfigurovat skupiny akcí pomocí šablon Azure Resource Manageru najdete v tématu [šablon Resource Manageru skupiny akcí](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Vytvoření skupiny akcí s využitím webu Azure portal ##
1. V [portál](https://portal.azure.com)vyberte **monitorování**. **Monitorování** okno konsoliduje všechny vaše monitorování nastavení a data v jednom zobrazení.

    !["Monitor" service](./media/monitoring-action-groups/home-monitor.png)
1. V **nastavení** vyberte **skupiny akcí**.

    ![Na kartě "Akce skupiny"](./media/monitoring-action-groups/action-groups-blade.png)
1. Vyberte **přidat skupinu akcí**a přejít k vyplnění polí.

    ![Příkaz "Přidat skupinu akcí"](./media/monitoring-action-groups/add-action-group.png)
1. Zadejte název do pole **název skupiny akcí** pole a zadejte název do pole **krátký název** pole. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

      ![Dialogové okno Přidat skupinu akcí"](./media/monitoring-action-groups/action-group-define.png)

1. **Předplatné** pole autofills s vaším aktuálním předplatným. Toto předplatné je ten, ve kterém se uloží skupiny akcí.

1. Vyberte **skupiny prostředků** v skupinu akcí se uloží.

1. Definujte seznam akcí tím, že poskytuje všechny akce:

    a. **Název**: Zadejte jedinečný identifikátor pro tuto akci.

    b. **Typ akce**: Vyberte e-mailu/SMS nebo nabízená/hlasové, aplikace logiky, Webhooku, ITSM nebo Runbooku Automation.

    c. **Podrobnosti o**: založená na typu akce, zadejte telefonní číslo, e-mailovou adresu, webhooku identifikátor URI, aplikace Azure, připojení ITSM nebo runbooku Automation. Akce ITSM, kromě určit **pracovní položku** a ostatní pole ITSM nástroj vyžaduje.

1. Vyberte **OK** k vytvoření skupiny akcí.

## <a name="action-specific-information"></a>Informace o konkrétní akci
<dl>
<dt>Nabídky Azure aplikace</dt>
<dd>Do skupiny akcí může mít až 10 akce aplikace Azure.</dd>
<dd>V tuto chvíli podporuje pouze akce Azure aplikace ServiceHealth výstrahy. Další upozornění vždy se bude ignorovat. Zobrazit [konfigurace oznámení pokaždé, když se pošle oznámení o stavu služby](monitoring-activity-log-alerts-on-service-notifications.md).</dd>

<dt>E-mailu</dt>
<dd>E-maily se budou odesílat z těchto e-mailových adres. Ujistěte se, že filtrování e-mailu je správně nakonfigurována

    - azure-noreply@microsoft.com
    - azureemail-noreply@microsoft.com
    - alerts-noreply@mail.windowsazure.com
    
</dd>
<dd>Může mít až 1 000 e-mailových akcí do skupiny akcí</dd>
<dd>Zobrazit [tady informace](./monitoring-alerts-rate-limiting.md) článku</dd>

<dt>ITSM</dt>
<dd>Můžete mít až 10 akce ITSM v skupiny akcí</dd>
<dd>Akce ITSM vyžaduje připojení ITSM. Zjistěte, jak vytvořit [připojení ITSM](../log-analytics/log-analytics-itsmc-overview.md).</dd>

<dt>Aplikace logiky</dt>
<dd>Může mít až 10 akce aplikace logiky do skupiny akcí</dd>

<dt>Sady Runbook</dt>
<dd>Můžete mít až 10 akce sada Runbook v skupiny akcí</dd>

<dt>SMS</dt>
<dd>Můžete mít až 10 SMS akce v skupiny akcí</dd>
<dd>Zobrazit [tady informace](./monitoring-alerts-rate-limiting.md) článku</dd>
<dd>Zobrazit [chování výstrah SMS](monitoring-sms-alert-behavior.md) článku</dd>

<dt>Hlasu</dt>
<dd>Můžete mít až 10 hlasové akce v skupiny akcí</dd>
<dd>Zobrazit [tady informace](./monitoring-alerts-rate-limiting.md) článku</dd>

<dt>Webhook</dt>
<dd>Můžete mít až 10 akce Webhooku v skupiny akcí
<dd>Zkuste logic - časový limit pro odpověď je 10 sekund. Volání webhooku se bude opakovat maximálně 2 při následující stavové kódy HTTP vrácené časy jsou časy: 408, 429, 503, 504 nebo koncový bod HTTP neodpovídá. Prvním opakováním se stane po 10 sekundách. Druhý a poslední opakování se stane po 100 sekund.</dd>
</dl>

## <a name="manage-your-action-groups"></a>Spravovat skupiny akcí ##
Po vytvoření skupiny akcí je viditelná ve **skupiny akcí** část **monitorování** okno. Vyberte skupinu akcí, které chcete spravovat:

* Přidání, úprava nebo odebrání akce.
* Odstraňte skupinu akcí.

## <a name="next-steps"></a>Další postup ##
* Další informace o [chování výstrah SMS](monitoring-sms-alert-behavior.md).  
* Získat [pochopení schéma webhooku v upozornění protokolu aktivit](monitoring-activity-log-alerts-webhook.md).  
* Další informace o [konektor ITSM](../log-analytics/log-analytics-itsmc-overview.md)
* Další informace o [rychlosti](monitoring-alerts-rate-limiting.md) na výstrahy.
* Získat [přehled upozornění protokolu aktivit](monitoring-overview-alerts.md)a zjistěte, jak dostávat upozornění.  
* Zjistěte, jak [konfigurace oznámení pokaždé, když se pošle oznámení o stavu služby](monitoring-activity-log-alerts-on-service-notifications.md).
