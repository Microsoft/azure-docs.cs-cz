---
title: Správa prostředků služby Datadog – Partnerská řešení Azure
description: Tento článek popisuje správu prostředků služby Datadog v Azure Portal. Jak nastavit jednotné přihlašování, odstranit organizaci v influent a získat podporu.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 04aef540bc134e5ec307be6a232ce47f0923e528
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046347"
---
# <a name="manage-the-datadog-resource"></a>Správa prostředku služby Datadog

Tento článek popisuje, jak spravovat nastavení pro integraci Azure s služby Datadog.

## <a name="resource-overview"></a>Přehled prostředků

Pokud chcete zobrazit podrobnosti o prostředku služby Datadog, vyberte v levém podokně **Přehled** .

:::image type="content" source="media/manage/resource-overview.png" alt-text="Přehled prostředků služby Datadog" border="true" lightbox="media/manage/resource-overview.png":::

Mezi tyto podrobnosti patří:

- Název skupiny prostředků
- Umístění/oblast
- Předplatné
- Značky
- Odkaz jednotného přihlašování na organizaci služby Datadog
- Nabídka/plán služby Datadog
- Fakturační období

Obsahuje taky odkazy na řídicí panely, protokoly a mapy hostitelů služby Datadog.

Obrazovka Přehled poskytuje souhrn prostředků, které odesílají protokoly a metriky do služby Datadog.

- Typ prostředku – typ prostředku Azure.
- Celkem prostředků – počet všech prostředků pro daný typ prostředku.
- Prostředky odesílající protokoly – počet prostředků, které odesílají protokoly do služby Datadog prostřednictvím integrace.
- Prostředky odesílající metriky – počet prostředků, které odesílají metriky do služby Datadog prostřednictvím integrace.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>Překonfigurujte pravidla pro metriky a protokoly.

Pokud chcete změnit pravidla konfigurace pro metriky a protokoly, v levém podokně vyberte **metriky a protokoly** .

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="Upravte konfiguraci protokolů a metriky pro prostředek služby Datadog." border="true":::

Další informace najdete v tématu [Konfigurace metrik a protokolů](create.md#configure-metrics-and-logs).

## <a name="view-monitored-resources"></a>Zobrazit monitorované prostředky

Pokud chcete zobrazit seznam prostředků, které generují protokoly do služby Datadog, v levém podokně vyberte **monitorované prostředky** .

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="Zobrazit prostředky monitorované pomocí služby Datadog" border="true":::

Seznam prostředků můžete filtrovat podle typu prostředku, názvu skupiny prostředků, umístění a podle toho, jestli prostředek odesílá protokoly a metriky.

Protokoly sloupců **do služby Datadog** určují, jestli prostředek odesílá protokoly do služby Datadog. Pokud prostředek neodesílá protokoly, toto pole indikuje, proč se protokoly neodesílají do služby Datadog. Důvody:

- Prostředek nepodporuje odesílání protokolů. Pro posílání protokolů do služby Datadog lze nakonfigurovat pouze typy prostředků s kategoriemi protokolu monitorování.
- Bylo dosaženo limitu pěti nastavení diagnostiky. Každý prostředek Azure může mít maximálně pět nastavení diagnostiky. Další informace najdete v tématu [nastavení diagnostiky](../../azure-monitor/essentials/diagnostic-settings.md).
- Chyba Prostředek je nakonfigurovaný tak, aby odesílal protokoly do služby Datadog, ale je blokovaný chybou.
- Protokoly nejsou nakonfigurované. Pro posílání protokolů do služby Datadog jsou nakonfigurované jenom prostředky Azure, které mají příslušné značky prostředků.
- Oblast není podporována. Prostředek Azure je v oblasti, která v současné době nepodporuje odesílání protokolů do služby Datadog.
- Agent služby Datadog není nakonfigurovaný. Virtuální počítače bez nainstalovaného agenta služby Datadog negenerují protokoly do služby Datadog.

## <a name="api-keys"></a>API Keys

Pokud chcete zobrazit seznam klíčů rozhraní API pro prostředek služby Datadog, vyberte **klíče** v levém podokně. Zobrazí se informace o klíčích.

:::image type="content" source="media/manage/keys.png" alt-text="Klíče rozhraní API pro organizaci služby Datadog" border="true":::

Azure Portal poskytuje zobrazení klíčů rozhraní API jen pro čtení. Pokud chcete klíče spravovat, vyberte odkaz služby Datadog Portal. Po provedení změn na portálu služby Datadog aktualizujte zobrazení Azure Portal.

Integrace služby Azure služby Datadog poskytuje možnost instalace agenta služby Datadog na virtuální počítač nebo App Service. Pokud není vybraný výchozí klíč, instalace agenta služby Datadog se nezdařila.

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Monitorování virtuálních počítačů s využitím agenta Datadog

Agenty služby Datadog můžete na virtuální počítače nainstalovat jako rozšíření. V části Konfigurace organizace služby Datadog v levém podokně klikněte na **Agent virtuálního počítače** . Tato obrazovka zobrazuje seznam všech virtuálních počítačů v rámci předplatného.

Pro každý virtuální počítač se zobrazí následující data:

- Název prostředku – název virtuálního počítače
- Stav prostředku – určuje, jestli je virtuální počítač zastavený nebo spuštěný. Agent služby Datadog se dá nainstalovat jenom na virtuální počítače, na kterých běží. Pokud se virtuální počítač zastaví, instalace agenta služby Datadog se zakáže.
- Verze agenta – číslo verze agenta služby Datadog.
- Stav agenta – určuje, jestli je na virtuálním počítači spuštěný agent služby Datadog.
- Integrace povolena – klíčové metriky shromažďované agentem služby Datadog.
- Install – metoda – konkrétní nástroj, který se používá k instalaci agenta služby Datadog. Například pro příkaz pro zadání nebo skript.
- Odesílání protokolů – určuje, zda agent služby Datadog odesílá protokoly do služby Datadog.

Vyberte virtuální počítač, na který chcete nainstalovat agenta služby Datadog. Vyberte **instalovat agenta**.

Portál požádá o potvrzení, že chcete nainstalovat agenta s výchozím klíčem. Kliknutím na **tlačítko OK** zahajte instalaci. Azure zobrazuje stav jako **instalování** , dokud není agent nainstalovaný a zřízený.

Po instalaci agenta služby Datadog se stav změní na nainstalováno.

Chcete-li zjistit, zda byl nainstalován agent služby Datadog, vyberte virtuální počítač a přejděte do okna rozšíření.

Agenty služby Datadog můžete odinstalovat na virtuálním počítači tak, že kliknete na možnost **Agent virtuálního počítače**. Vyberte virtuální počítač a **odinstalujte agenta**.

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>Monitorování App Services pomocí agenta služby Datadog jako rozšíření

Agenty služby Datadog můžete nainstalovat do App Services jako rozšíření. Přejít na **rozšíření App Service** v levém podokně. Tato obrazovka zobrazuje seznam všech aplikačních služeb v rámci předplatného.

Pro každou službu App Service se zobrazí následující datové prvky:

- Název prostředku – název virtuálního počítače.
- Stav prostředku – určuje, jestli je služba App Service zastavená nebo spuštěná. Agent služby Datadog se dá nainstalovat jenom na App Services, na kterých běží. Pokud se služba App Service zastavila, instalace agenta služby Datadog je zakázaná.
- Plán služby App Service – konkrétní plán nakonfigurovaný pro službu App Service.
- Verze agenta – číslo verze agenta služby Datadog.

Pokud chcete nainstalovat agenta služby Datadog, vyberte App Service a **nainstalujte rozšíření**. Nejnovější agent služby Datadog je nainstalován ve službě App Service jako rozšíření.

Portál potvrdí, že chcete nainstalovat agenta služby Datadog. Nastavení aplikace pro konkrétní službu App Service se navíc aktualizuje pomocí výchozího klíče. Služba App Service se po dokončení instalace agenta služby Datadog restartuje.

Výběrem **OK** zahajte proces instalace pro agenta služby Datadog. Portál zobrazuje stav **instalování** , dokud není nainstalován agent. Po instalaci agenta služby Datadog se stav změní na nainstalováno.

Pokud chcete odinstalovat agenty služby Datadog ve službě App Service, použijte **App Service rozšíření**. Výběr služby App Service a **rozšíření pro odinstalaci**

## <a name="reconfigure-single-sign-on"></a>Překonfigurujte jednotné přihlašování.

Pokud chcete znovu nakonfigurovat jednotné přihlašování, vyberte v levém podokně **jednotné přihlašování** .

Pokud chcete zavést jednotné přihlašování prostřednictvím Azure Active Directory, vyberte **Povolit jednotné přihlašování prostřednictvím Azure Active Directory**.

Portál načte příslušnou aplikaci služby Datadog z Azure Active Directory. Aplikace pochází z názvu podnikové aplikace, který jste zvolili při nastavování integrace. Vyberte název aplikace služby Datadog, jak je znázorněno níže:
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="Překonfigurujte aplikaci jednotného přihlašování." border="true":::
 
## <a name="disable-or-enable-integration"></a>Zakázat nebo povolit integraci

Odesílání protokolů a metrik z Azure do služby Datadog můžete zastavit. Budou se vám dál účtovat další služby služby Datadog, které nesouvisí s monitorováním metrik a protokolů.

Pokud chcete zakázat integraci Azure s služby Datadog, přečtěte si **Přehled**. Vyberte **Zakázat** a **OK**.
 
:::image type="content" source="media/manage/disable.png" alt-text="Zakáže prostředek služby Datadog." border="true":::

Pokud chcete povolit integraci Azure s služby Datadog, přečtěte si **Přehled**. Vyberte **Povolit** a **OK**. Výběrem možnosti **Povolit** načtete předchozí konfiguraci pro metriky a protokoly. Konfigurace určuje, které prostředky Azure emitují metriky a protokoly do služby Datadog. Po dokončení kroku budou metriky a protokoly odesílány do služby Datadog.
 
:::image type="content" source="media/manage/enable.png" alt-text="Povolte prostředek služby Datadog." border="true":::

## <a name="delete-datadog-resource"></a>Odstranit prostředek služby Datadog

V levém podokně můžete přejít na **Přehled** a vybrat **Odstranit**. Potvrďte, že chcete odstranit prostředek služby Datadog. Vyberte **Odstranit**.

:::image type="content" source="media/manage/delete.png" alt-text="Odstranit prostředek služby Datadog" border="true":::

Pokud je k organizaci služby Datadog namapován pouze jeden prostředek služby Datadog, protokoly a metriky již nejsou odesílány do služby Datadog. Všechna fakturace se služby Datadog až do Azure Marketplace.

Pokud je k organizaci služby Datadog namapován více než jeden prostředek služby Datadog, odstranění prostředku služby Datadog pouze zastaví odesílání protokolů a metrik pro daný prostředek služby Datadog. Vzhledem k tomu, že je organizace služby Datadog propojená s jinými prostředky Azure, bude fakturace pokračovat prostřednictvím Azure Marketplace.

## <a name="getting-support"></a>Získání podpory

Pokud se chcete obrátit na podporu Azure služby Datadog Integration, v levém podokně vyberte **nový support Request** . Vyberte odkaz na portál služby Datadog.

:::image type="content" source="media/manage/support-request.png" alt-text="Vytvořit novou žádost o podporu pro služby Datadog" border="true":::

## <a name="next-steps"></a>Další kroky

Pomoc při řešení potíží najdete v tématu [řešení potíží s služby Datadog řešeními](troubleshoot.md).