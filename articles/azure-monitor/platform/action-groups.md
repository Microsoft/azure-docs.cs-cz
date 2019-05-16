---
title: Vytvoření a Správa skupin akcí na webu Azure Portal
description: Zjistěte, jak vytvářet a spravovat skupiny akcí na webu Azure Portal.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/10/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: fba5119feb1ff7a0170a573371e479caa5fc33eb
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544484"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Vytvoření a Správa skupin akcí na webu Azure Portal
## <a name="overview"></a>Přehled ##
Skupiny akcí je kolekce předvolby oznamování určené vlastník předplatného Azure. Upozornění Azure Monitor a stavy služeb použití skupin akcí uživatelům oznámit, že výstraha byla aktivována. Různé výstrahy použít stejnou skupinu akcí nebo skupiny různých akcí v závislosti na požadavcích uživatele. V rámci předplatného můžete nakonfigurovat až 2 000 skupin akcí.

Nakonfigurujete akci, kterou chcete upozornit osoby, e-mail nebo SMS, že dostanou oznamující, že byly přidány do skupiny akcí potvrzení.

V tomto článku se dozvíte, jak vytvořit a spravovat skupiny akcí na webu Azure Portal.

Každá akce se skládá z následujících vlastností:

* **Název**: Jedinečný identifikátor v rámci skupiny akcí.  
* **Typ akce**: Provést akci. Mezi příklady patří odesílání hlasový hovor, SMS, e-mailů; nebo aktivace různé automatizované akce. Zobrazit typy dále v tomto článku.
* **Podrobnosti o**: Související podrobnosti, které se liší podle *typ akce*.

Informace o tom, jak nakonfigurovat skupiny akcí pomocí šablon Azure Resource Manageru najdete v tématu [šablon Resource Manageru skupiny akcí](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Vytvoření skupiny akcí s využitím webu Azure portal ##
1. V [portál](https://portal.azure.com)vyberte **monitorování**. **Monitorování** podokně konsoliduje všechny vaše monitorování nastavení a data v jednom zobrazení.

    !["Monitor" service](./media/action-groups/home-monitor.png)
1. Vyberte **výstrahy** vyberte **spravovat skupiny akcí**.

    ![Správa skupin akcí na tlačítko](./media/action-groups/manage-action-groups.png)
1. Vyberte **přidat skupinu akcí**a přejít k vyplnění polí.

    ![Příkaz "Přidat skupinu akcí"](./media/action-groups/add-action-group.png)
1. Zadejte název do pole **název skupiny akcí** pole a zadejte název do pole **krátký název** pole. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

      ![Dialogové okno Přidat skupinu akcí"](./media/action-groups/action-group-define.png)

1. **Předplatné** pole autofills s vaším aktuálním předplatným. Toto předplatné je ten, ve kterém se uloží skupiny akcí.

1. Vyberte **skupiny prostředků** v skupinu akcí se uloží.

1. Definujte seznam akcí. Zadejte následující úkony:

    a. **Název**: Zadejte jedinečný identifikátor pro tuto akci.

    b. **Typ akce**: Vyberte e-mailu/SMS nebo nabízená/hlasové, aplikace logiky, Webhooku, ITSM nebo Runbooku Automation.

    c. **Podrobnosti o**: Závislosti na typu akce, zadejte telefonní číslo, e-mailovou adresu, webhooku identifikátor URI, aplikace Azure, připojení ITSM nebo runbooku Automation. Akce ITSM, kromě určit **pracovní položku** a ostatní pole ITSM nástroj vyžaduje.

1. Vyberte **OK** k vytvoření skupiny akcí.

## <a name="manage-your-action-groups"></a>Spravovat skupiny akcí ##
Po vytvoření skupiny akcí je viditelná ve **skupiny akcí** část **monitorování** podokně. Vyberte skupinu akcí, které chcete spravovat:

* Přidání, úprava nebo odebrání akce.
* Odstraňte skupinu akcí.

## <a name="action-specific-information"></a>Informace o konkrétní akci
> [!NOTE]
> Zobrazit [limity předplatného služby pro monitorování](https://docs.microsoft.com/azure/azure-subscription-service-limits#monitor-limits) pro číselné limity na každé z níže uvedených položek.  

**Aplikace Azure nabízené** – může mít omezený počet akcí Azure aplikace do skupiny akcí.

**E-mailu** – E-maily se budou odesílat z těchto e-mailových adres. Ujistěte se, že filtrování e-mailu je správně nakonfigurována
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Může mít omezený počet e-mailových akcí do skupiny akcí. Zobrazit [tady informace](./../../azure-monitor/platform/alerts-rate-limiting.md) článku

**ITSM** – do skupiny akcí může mít omezený počet akce ITSM. Akce ITSM vyžaduje připojení ITSM. Zjistěte, jak vytvořit [připojení ITSM](../../azure-monitor/platform/itsmc-overview.md).

**Aplikace logiky** – může mít omezený počet akce aplikace logiky do skupiny akcí.

**Funkce App** -funkci klávesy pro aplikace Function App nakonfigurovaný jako akce jsou číst prostřednictvím rozhraní API funkce, které aktuálně vyžaduje v2 aplikace function App ke konfiguraci nastavení "AzureWebJobsSecretStorageType" aplikace "files". Další informace najdete v tématu [změní na správu klíčů ve V2 funkce]( https://aka.ms/funcsecrets).

**Sada Runbook** -omezený počet akce sada Runbook může mít v skupiny akcí. Odkazovat [limity předplatného Azure](../../azure-subscription-service-limits.md) pro omezení datových částí sady Runbook.

**SMS** – může mít omezený počet SMS akce v skupiny akcí. Viz také [tady informace](./../../azure-monitor/platform/alerts-rate-limiting.md) a [chování výstrah SMS](../../azure-monitor/platform/alerts-sms-behavior.md) další důležité informace. 

**Hlasové** – může mít omezený počet akcí hlasu ve skupině akcí. Zobrazit [tady informace](./../../azure-monitor/platform/alerts-rate-limiting.md) článku.

**Webhook** – může mít omezený počet akce Webhooku v skupiny akcí. Webhooky jsou spuštěna znovu s použitím následujících pravidel. Volání webhooku je opakovat maximálně 2 při následující stavové kódy HTTP vrácené časy jsou časy: 408, 429, 503, 504 nebo když koncový bod HTTP neodpovídá. První opakování se provede po 10 sekundách. Druhý opakování se stane po 100 sekund. Žádné skupiny akcí po dvou selháních, zavolá koncový bod po dobu 30 minut. 

Zdrojové rozsahy IP adres
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 52.244.65.137
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Pokud chcete dostávat informace o změnách na tyto IP adresy, doporučujeme, abyste že konfigurace upozornění Service Health, která monitoruje Informační oznámení týkající se služby skupiny akcí.

## <a name="next-steps"></a>Další postup ##
* Další informace o [chování výstrah SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Získat [pochopení schéma webhooku v upozornění protokolu aktivit](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Další informace o [konektor ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Další informace o [rychlosti](../../azure-monitor/platform/alerts-rate-limiting.md) na výstrahy.
* Získat [přehled upozornění protokolu aktivit](../../azure-monitor/platform/alerts-overview.md)a zjistěte, jak dostávat upozornění.  
* Zjistěte, jak [konfigurace oznámení pokaždé, když se pošle oznámení o stavu služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
