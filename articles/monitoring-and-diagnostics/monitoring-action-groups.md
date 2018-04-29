---
title: Vytvoření a Správa skupin akce na portálu Azure | Microsoft Docs
description: Naučte se vytvářet a spravovat skupiny akce na portálu Azure.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: dukek
ms.openlocfilehash: 07e3c1a95aa223121117f3deba0269fb6cc280c2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Vytvoření a Správa skupin akce na portálu Azure
## <a name="overview"></a>Přehled ##
Tento článek ukazuje, jak vytvořit a spravovat skupiny akce na portálu Azure.

Seznam akcí, můžete nakonfigurovat skupiny akcí. Tyto skupiny pak může být použit jednotlivé výstrahy, které definujete, zajistíte, že jsou stejné akce trvá pokaždé, když se výstraha.

Každá akce se skládá z následujících vlastností:

* **Název**: Jedinečný identifikátor v rámci skupiny pro akce.  
* **Typ akce**: odeslat telefonní hovor nebo SMS, e-mailovou zprávu, volat webhook, jehož, odesílání dat do nástroj na ITSM, volání aplikace logiky, odesílání nabízených oznámení do aplikace Azure nebo spustit runbook služby automatizace.
* **Podrobnosti o**: odpovídající telefonního čísla, e-mailovou adresu, webhooku identifikátor URI nebo ITSM podrobnosti připojení.

Informace o tom, jak pomocí šablony Azure Resource Manager můžete nakonfigurovat skupiny akcí najdete v tématu [šablony správce prostředků skupiny akce](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Vytvořit skupinu akce pomocí portálu Azure ##
1. V [portál](https://portal.azure.com), vyberte **monitorování**. **Monitorování** slučuje okno veškeré monitorování nastavení a data v jednom zobrazení.

    ![Službu "Sledování"](./media/monitoring-action-groups/home-monitor.png)
2. V **nastavení** vyberte **skupiny akcí**.

    ![Na kartě "Akce skupiny"](./media/monitoring-action-groups/action-groups-blade.png)
3. Vyberte **přidat akci skupinu**a vyplňte příslušná pole.

    ![Příkaz "Přidat skupinu akce"](./media/monitoring-action-groups/add-action-group.png)
4. Zadejte název do pole **název skupiny akce** pole a zadejte název do pole **krátký název** pole. Krátký název se používá namísto názvu skupiny úplné akce při odesílání oznámení pomocí této skupiny.

      ![Dialogové okno Přidat skupinu akce"](./media/monitoring-action-groups/action-group-define.png)

5. **Předplatné** pole autofills s vaším aktuálním předplatným. Toto předplatné je ten, ve kterém je akce skupinu uložit.

6. Vyberte **skupiny prostředků** ve skupině akce je uložen.

7. Definujte seznam akcí, tím, že poskytuje každá akce:

    a. **Název**: Zadejte jedinečný identifikátor pro tuto akci.

    b. **Typ akce**: Vyberte e-mailu nebo SMS nebo nabízená nebo hlasové, aplikace logiky, Webhooku, ITSM nebo sady Automation Runbook.

    c. **Podrobnosti o**: v závislosti na typu akce, zadejte telefonní číslo, e-mailovou adresu, webhooku identifikátor URI, aplikace Azure, ITSM připojení nebo sady Automation runbook. Pro akci ITSM kromě určit **pracovní položka** a vyžaduje vaše nástroje ITSM další pole.

8. Vyberte **OK** vytvořit skupinu akce.

## <a name="action-specific-information"></a>Konkrétní informace o akci
<dl>
<dt>Nabízená aplikace Azure</dt>
<dd>Ve skupině Akce může mít až 10 akce aplikace Azure.</dd>
<dd>V tuto chvíli akce aplikace Azure podporuje pouze ServiceHealth výstrahy. Všechny výstrahy čas se budou ignorovat. V tématu [Konfigurace upozornění pokaždé, když je odeslána oznámení o stavu služby](monitoring-activity-log-alerts-on-service-notifications.md).</dd>

<dt>E-mailu</dt>
<dd>Může mít až 50 akce e-mailu ve skupině Akce</dd>
<dd>Najdete v článku [míru omezení informací](./monitoring-alerts-rate-limiting.md) článku</dd>

<dt>ITSM</dt>
<dd>Může mít až 10 akce ITSM ve skupině Akce</dd>
<dd>Akce ITSM vyžaduje připojení k ITSM. Naučte se vytvářet [ITSM připojení](../log-analytics/log-analytics-itsmc-overview.md).</dd>

<dt>Aplikace logiky</dt>
<dd>Může mít až 10 akce aplikace logiky ve skupině Akce</dd>

<dt>sady runbook</dt>
<dd>Může mít až 10 akce sady Runbook ve skupině Akce</dd>

<dt>SMS</dt>
<dd>Může mít až 10 akce SMS ve skupině Akce</dd>
<dd>Najdete v článku [míru omezení informací](./monitoring-alerts-rate-limiting.md) článku</dd>
<dd>Najdete v článku [SMS výstrahy chování](monitoring-sms-alert-behavior.md) článku</dd>

<dt>hlas</dt>
<dd>Může mít až 10 akce hlasové ve skupině Akce</dd>
<dd>Najdete v článku [míru omezení informací](./monitoring-alerts-rate-limiting.md) článku</dd>

<dt>Webhooku</dt>
<dd>Může mít až 10 akce Webhooku ve skupině Akce
<dd>Logika opakovaných pokusů - časový limit pro odpověď je 10 sekund. Voláním webhooku se bude opakovat maximálně 2 při následující stavové kódy HTTP vrácené časy jsou: 408 429, 503, 504 nebo koncový bod HTTP neodpovídá. První opakování se stane po 10 sekundách. Druhý a poslední opakování se stane po 100 sekund.</dd>
</dl>

## <a name="manage-your-action-groups"></a>Správa skupin akce ##
Jakmile vytvoříte skupinu akcí, se zobrazí na **skupiny akcí** části **monitorování** okno. Vyberte skupinu akce, kterou chcete spravovat:

* Přidat, upravit nebo odebrat akce.
* Odstraňte skupinu akce.

## <a name="next-steps"></a>Další postup ##
* Další informace o [SMS výstrahy chování](monitoring-sms-alert-behavior.md).  
* Získání [porozumět schéma výstrahy webhooku protokolu činnosti](monitoring-activity-log-alerts-webhook.md).  
* Další informace o [ITSM konektoru](../log-analytics/log-analytics-itsmc-overview.md)
* Další informace o [omezení rychlosti](monitoring-alerts-rate-limiting.md) výstrah.
* Získat [přehled výstrah aktivity protokolu](monitoring-overview-alerts.md)a zjistěte, jak dostávat výstrahy.  
* Zjistěte, jak [Konfigurace upozornění pokaždé, když je odeslána oznámení o stavu služby](monitoring-activity-log-alerts-on-service-notifications.md).
