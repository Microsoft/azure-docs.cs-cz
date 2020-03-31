---
title: Plány údržby fondu Synapse SQL
description: Plánování údržby umožňuje zákazníkům plánovat potřebné události plánované údržby, které Azure Synapse Analytics používá k zavádění nových funkcí, upgradů a oprav.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: f193580ca03d4b1805f3c044658a34f468f3f44f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346566"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Správa aktualizací a údržby služeb pomocí plánů údržby

Funkce plánu údržby integruje oznámení plánované údržby stavu služby, monitorování kontroly stavu prostředků a službu plánování údržby pro fond Synapse SQL (datový sklad) v rámci Azure Synapse Analytics. 

Pomocí plánování údržby byste měli zvolit časové okno, kdy je vhodné přijímat nové funkce, upgrady a opravy. Budete muset zvolit primární a sekundární okno údržby během sedmidenního období, každé okno musí být v rámci samostatných denních rozsahů.

Můžete například naplánovat primární okno od soboty 22:00 do neděle 01:00 a potom naplánovat sekundární okno od středy od středy od 19:00 do 22:00. Pokud údržbu nelze provést během primárního okna údržby, pokusí se o údržbu znovu během sekundárního okna údržby. Údržba servisu může v některých případech nastat během primárního i sekundárního okna. Aby bylo zajištěno rychlé dokončení všech operací údržby, mohou vrstvy DW400c a nižší chod datového skladu dokončit údržbu mimo určené okno údržby.

Všechny nově vytvořené instance datového skladu budou mít během nasazení použitý plán údržby definovaný systémem. Plán lze upravit ihned po dokončení nasazení.

Přestože okno údržby může být mezi třemi a osmi hodinami, neznamená to, že datový sklad bude po dobu trvání offline. Údržba může dojít kdykoli v rámci tohoto okna a měli byste očekávat, že jeden odpojit během tohoto období trvající ~ 5 -6 minut jako služba nasazuje nový kód do datového skladu. DW400c a nižší může dojít k několika krátkým ztrátám v připojení v různých časech během údržby okna. Při spuštění údržby budou zrušeny všechny aktivní relace a nepotvrzené transakce budou vráceny zpět. Chcete-li minimalizovat prostoje instancí, ujistěte se, že váš datový sklad nemá žádné dlouhotrvající transakce před vámi zvolené období údržby.

Všechny operace údržby by měly být dokončeny v rámci zadaných oken údržby, pokud nejsme povinni nasadit aktualizaci citlivou na čas. Pokud je datový sklad během plánované údržby pozastaven, bude během operace obnovení aktualizován. Budete upozorněni ihned po dokončení údržby datového skladu.

## <a name="alerts-and-monitoring"></a>Výstrahy a monitorování

Integrace s oznámeními o stavu služby a monitorem kontroly stavu prostředků umožňuje zákazníkům zůstat informováni o blížící se činnosti údržby. Tato automatizace využívá Azure Monitor. Můžete se rozhodnout, jak chcete být upozorněni na blížící se události údržby. Můžete si také vybrat, které automatizované toky vám pomohou řídit prostoje a minimalizovat provozní dopad.

24hodinové předběžné oznámení předchází všem událostem údržby, které nejsou pro DWC400c a nižší úrovně.

> [!NOTE]
> V případě, že jsme povinni nasadit časovou aktualizaci, může být výrazně zkrácena doba rozšířeného oznámení.

Pokud jste obdrželi předběžné oznámení, že bude probíhat údržba, ale údržbu nelze provést během časového období v oznámení, obdržíte oznámení o zrušení. Údržba se pak obnoví během příštího plánovaného udržovacího období.

Všechny aktivní události údržby se zobrazí v části **Stav služby – plánovaná údržba.** Historie stavu služby obsahuje úplný záznam minulých událostí. Údržbu můžete sledovat prostřednictvím řídicího panelu portálu azure service health check během aktivní události.

### <a name="maintenance-schedule-availability"></a>Dostupnost plánu údržby

I když plánování údržby není ve vybrané oblasti k dispozici, můžete plán údržby kdykoli zobrazit a upravit. Jakmile bude plánování údržby k dispozici ve vaší oblasti, identifikovaný plán se okamžitě aktivuje ve vašem fondu SYNAPse SQL.

## <a name="view-a-maintenance-schedule"></a>Zobrazení plánu údržby 

Ve výchozím nastavení mají všechny nově vytvořené instance datového skladu osmihodinové okno primární a sekundární údržby použité během nasazení. Jak je uvedeno výše, můžete změnit okna, jakmile je nasazení dokončeno. Mimo zadaná časová období údržby nedojde bez předchozího upozornění k žádné údržbě.

Chcete-li zobrazit plán údržby, který byl použit pro váš fond Synapse SQL, proveďte následující kroky:

1.    Přihlaste se k [portálu Azure](https://portal.azure.com/).
2.    Vyberte fond Synapse SQL, který chcete zobrazit. 
3.    Vybraný fond Synapse SQL se otevře v okně přehledu. Plán údržby použitý v datovém skladu se zobrazí pod **plánem údržby**.

![Přehledová čepel](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Změna plánu údržby 

Plán údržby lze kdykoli aktualizovat nebo změnit. Pokud vybraná instance prochází aktivním cyklem údržby, nastavení se uloží. Budou aktivní během příštího identifikovaného udržovacího období. [Přečtěte si další informace](../../service-health/resource-health-overview.md) o monitorování datového skladu během akce aktivní údržby. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Identifikace primárních a sekundárních oken

Primární a sekundární okna musí mít samostatné denní rozsahy. Příkladem je primární okno úterý až čtvrtek a sekundární okno sobota-neděle.

Chcete-li změnit plán údržby fondu Synapse SQL, proveďte následující kroky:
1.    Přihlaste se k [portálu Azure](https://portal.azure.com/).
2.    Vyberte fond Synapse SQL, který chcete aktualizovat. Stránka se otevře v okně přehledu. 
3.    Otevřete stránku pro nastavení plánu údržby výběrem souhrnného odkazu **Plán údržby** v okně přehledu. Nebo vyberte možnost **Plán údržby** v nabídce prostředků na levé straně.  

    ![Přehled možností čepele](./media/maintenance-scheduling/maintenance-change-option.png)

4. Určete preferovaný rozsah dnů pro primární okno údržby pomocí možností v horní části stránky. Tento výběr určuje, zda se primární okno zobrazí v den pracovního dne nebo o víkendu. Výběr aktualizuje rozevírací hodnoty. Během náhledu nemusí některé oblasti ještě podporovat úplnou sadu dostupných možností **dne.**

   ![Nůž pro nastavení údržby](./media/maintenance-scheduling/maintenance-settings-page.png)

5. Pomocí rozevíracích seznamů zvolte preferovaná okna primární a sekundární údržby:
   - **Den**: Preferovaný den k provádění údržby během vybraného okna.
   - **Čas zahájení**: Upřednostňovaný čas zahájení pro okno údržby.
   - **Časové okno**: Preferovaná doba trvání časového období.

   Oblast **Souhrn plánu** v dolní části okna se aktualizuje na základě vybraných hodnot. 
  
6. Vyberte **Uložit**. Zobrazí se zpráva potvrzující, že nový plán je nyní aktivní. 

   Pokud ukládáte plán v oblasti, která nepodporuje plánování údržby, zobrazí se následující zpráva. Vaše nastavení se uloží a stanou se aktivními, jakmile bude tato funkce dostupná ve vybrané oblasti.    

   ![Zpráva o dostupnosti oblasti](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Další kroky
- [Přečtěte si další informace](../../azure-monitor/platform/alerts-metric.md) o vytváření, zobrazování a správě výstrah pomocí Azure Monitoru.
- [Přečtěte si další informace](../..//azure-monitor/platform/alerts-log-webhook.md) o akcích webhooku pro pravidla upozornění protokolu.
- [Další informace](../..//azure-monitor/platform/action-groups.md) Vytváření a správa akčních skupin.
- [Přečtěte si další informace](../../service-health/service-health-overview.md) o azure service health.
