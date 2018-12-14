---
title: Vytvoření a Správa skupin akcí na webu Azure Portal
description: Zjistěte, jak vytvářet a spravovat skupiny akcí na webu Azure Portal.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 432f1a89979829bd43596d0d6a3ab7a2a3bfb996
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336464"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Vytvoření a Správa skupin akcí na webu Azure Portal
## <a name="overview"></a>Přehled ##
Skupiny akcí je kolekce předvolby oznamování určené vlastník předplatného Azure. Upozornění Azure Monitor a stavy služeb použití skupin akcí uživatelům oznámit, že výstraha byla aktivována. Různé výstrahy použít stejnou skupinu akcí nebo skupiny různých akcí v závislosti na požadavcích uživatele.

Když akce konfigurován pro osoby pro oznámení e-mailem nebo SMS osoby se zobrazí potvrzení, která udává, že / Jana se přidal do skupiny akcí.

V tomto článku se dozvíte, jak vytvořit a spravovat skupiny akcí na webu Azure Portal.

Každá akce se skládá z následujících vlastností:

* **Název**: Jedinečný identifikátor v rámci skupiny akcí.  
* **Typ akce**: Akce k provedení. Mezi příklady patří odesílání hlasový hovor, SMS, e-mailů; nebo aktivace různé automatizované akce. Zobrazit typy dále v tomto článku. 
* **Podrobnosti o**: Související podrobnosti, které se liší podle *typ akce*. 

Informace o tom, jak nakonfigurovat skupiny akcí pomocí šablon Azure Resource Manageru najdete v tématu [šablon Resource Manageru skupiny akcí](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Vytvoření skupiny akcí s využitím webu Azure portal ##
1. V [portál](https://portal.azure.com)vyberte **monitorování**. **Monitorování** okno konsoliduje všechny vaše monitorování nastavení a data v jednom zobrazení.

    !["Monitor" service](./media/action-groups/home-monitor.png)
1. Vyberte **výstrahy** vyberte **spravovat skupiny akcí**.

    ![Správa skupin akcí na tlačítko](./media/action-groups/manage-action-groups.png)
1. Vyberte **přidat skupinu akcí**a přejít k vyplnění polí.

    ![Příkaz "Přidat skupinu akcí"](./media/action-groups/add-action-group.png)
1. Zadejte název do pole **název skupiny akcí** pole a zadejte název do pole **krátký název** pole. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

      ![Dialogové okno Přidat skupinu akcí"](./media/action-groups/action-group-define.png)

1. **Předplatné** pole autofills s vaším aktuálním předplatným. Toto předplatné je ten, ve kterém se uloží skupiny akcí.

1. Vyberte **skupiny prostředků** v skupinu akcí se uloží.

1. Definujte seznam akcí tím, že poskytuje všechny akce:

    a. **Název**: Zadejte jedinečný identifikátor pro tuto akci.

    b. **Typ akce**: Vyberte e-mailu/SMS nebo nabízená/hlasové, aplikace logiky, Webhooku, ITSM nebo Runbooku Automation.

    c. **Podrobnosti o**: Závislosti na typu akce, zadejte telefonní číslo, e-mailovou adresu, webhooku identifikátor URI, aplikace Azure, připojení ITSM nebo runbooku Automation. Akce ITSM, kromě určit **pracovní položku** a ostatní pole ITSM nástroj vyžaduje.

1. Vyberte **OK** k vytvoření skupiny akcí.

## <a name="manage-your-action-groups"></a>Spravovat skupiny akcí ##
Po vytvoření skupiny akcí je viditelná ve **skupiny akcí** část **monitorování** okno. Vyberte skupinu akcí, které chcete spravovat:

* Přidání, úprava nebo odebrání akce.
* Odstraňte skupinu akcí.

## <a name="action-specific-information"></a>Informace o konkrétní akci
**Aplikace Azure nabízené** -může obsahovat až 10 aplikací Azure akce skupiny akcí. V tuto chvíli podporuje pouze akce Azure aplikace ServiceHealth výstrahy. Další upozornění vždy se bude ignorovat. Zobrazit [konfigurace oznámení pokaždé, když se pošle oznámení o stavu služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

**E-mailu** – E-maily se budou odesílat z těchto e-mailových adres. Ujistěte se, že filtrování e-mailu je správně nakonfigurována
   - azure-noreply@microsoft.com
   - azureemail-noreply@microsoft.com
   - alerts-noreply@mail.windowsazure.com

Můžete mít až 1 000 e-mailových akcí do skupiny akcí. Zobrazit [tady informace](./../../azure-monitor/platform/alerts-rate-limiting.md) článku

**ITSM** – můžete mít až 10 akce ITSM jedná o smluvní jednání akce skupiny ITSM vyžaduje připojení ITSM. Zjistěte, jak vytvořit [připojení ITSM](../../azure-monitor/platform/itsmc-overview.md).

**Aplikace logiky** – může mít až 10 akce aplikace logiky do skupiny akcí

**Funkce App** – funkční klávesy pro aplikace Function App nakonfigurovaný jako akce jsou číst prostřednictvím rozhraní API funkce, které aktuálně vyžaduje v2 aplikace function App ke konfiguraci nastavení "AzureWebJobsSecretStorageType" k "files" aplikace, najdete v článku [ Změní na správu klíčů ve V2 funkce]( https://aka.ms/funcsecrets) Další informace.

**Sada Runbook** -může obsahovat až 10 akce sada Runbook akce skupiny najdete [limity předplatného Azure](../../azure-subscription-service-limits.md) omezení datových částí sady Runbook pro

**SMS** – může mít až 10 SMS akce v skupiny akcí v [tady informace](./../../azure-monitor/platform/alerts-rate-limiting.md) najdete v článku [chování výstrah SMS](../../azure-monitor/platform/alerts-sms-behavior.md) článku

**Hlasové** -může obsahovat až 10 hlasové akce skupiny akcí</dd>
Zobrazit [tady informace](./../../azure-monitor/platform/alerts-rate-limiting.md) článku</dd>

**Webhook** – můžete mít až 10 akce Webhooku v skupiny akcí. Zkuste logic - časový limit pro odpověď je 10 sekund. Volání webhooku se bude opakovat maximálně 2 při následující stavové kódy HTTP vrácené časy jsou časy: 408, 429, 503, 504 nebo koncový bod HTTP neodpovídá. Prvním opakováním se stane po 10 sekundách. Druhý a poslední opakování se stane po 100 sekund.

Zdrojové rozsahy IP adres
    - 13.106.57.181
    - 13.106.54.3
    - 13.106.54.19
    - 13.106.38.142
    - 13.106.38.148
    - 13.106.57.196

Získat informace o změnách na tyto IP adresy, doporučujeme, abyste konfiguraci [upozornění na stav služby](./../../monitoring-and-diagnostics/monitoring-service-notifications.md) monitoruje informační upozornění týkající se služby skupiny akcí.


## <a name="next-steps"></a>Další postup ##
* Další informace o [chování výstrah SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Získat [pochopení schéma webhooku v upozornění protokolu aktivit](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Další informace o [konektor ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Další informace o [rychlosti](../../azure-monitor/platform/alerts-rate-limiting.md) na výstrahy.
* Získat [přehled upozornění protokolu aktivit](../../azure-monitor/platform/alerts-overview.md)a zjistěte, jak dostávat upozornění.  
* Zjistěte, jak [konfigurace oznámení pokaždé, když se pošle oznámení o stavu služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
