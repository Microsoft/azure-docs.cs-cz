---
title: Připojení nástroje Configuration Manager ke službě Azure Monitor | Dokumenty společnosti Microsoft
description: Tento článek ukazuje kroky pro připojení Nástroje configuration manageru k pracovnímu prostoru v Azure Monitoru a zahájení analýzy dat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 3140c0de6fbe090e3d040202cd581c455f03b6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655252"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Připojení správce konfigurace ke službě Azure Monitor
Prostředí Microsoft Endpoint Configuration Manager můžete připojit k Azure Monitoru a synchronizovat data kolekce zařízení a odkazovat na tyto kolekce v Azure Monitoru a Azure Automation.  

## <a name="prerequisites"></a>Požadavky

Azure Monitor podporuje aktuální větev nástroje Configuration Manager verze 1606 a vyšší.

>[!NOTE]
>Funkce pro připojení nástroje Configuration Manager k pracovnímu prostoru Analýzy protokolů je volitelná a ve výchozím nastavení není povolená. Tuto funkci je nutné povolit před použitím. Další informace naleznete v části [Enable optional features from updates](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Přehled konfigurace

Následující kroky shrnují kroky konfigurace integrace nástroje Configuration Manager pomocí nástroje Azure Monitor.  

1. Ve službě Azure Active Directory zaregistrujte Nástroj Configuration Manager jako webovou aplikaci nebo aplikaci webového rozhraní API a ujistěte se, že máte id klienta a tajný klíč klienta z registrace z Azure Active Directory. Podrobné informace o tom, jak tento krok provést, naleznete [v tématu Pomocí portálu vytvořte zaregistrovaný objekt aplikace a služby Active Directory, které mají přístup k prostředkům.](../../active-directory/develop/howto-create-service-principal-portal.md)

2. Ve službě Azure Active Directory [udělte nástroji Configuration Manager (registrovaná webová aplikace) oprávnění k přístupu k Azure Monitoru](#grant-configuration-manager-with-permissions-to-log-analytics).

3. Ve Správci konfigurace přidejte připojení pomocí průvodce **službami Azure.**

4. [Stáhněte a nainstalujte agenta Log Analytics pro Systém Windows](#download-and-install-the-agent) do počítače, ve kterýje spuštěna role systému lokality bodů připojení služby Configuration Manager. Agent odesílá data nástroje Configuration Manager do pracovního prostoru Analýzy protokolů v Azure Monitoru.

5. Ve službě Azure Monitor [importujte kolekce z Configuration Manageru](#import-collections) jako skupiny počítačů.

6. Ve službě Azure Monitor můžete zobrazit data z nástroje Configuration Manager jako [skupiny počítačů](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Udělit Nástroji pro konfiguraci s oprávněními k log analytics

V následujícím postupu udělíte roli *přispěvatele* v pracovním prostoru Analýzy protokolů instanačnímu objektu aplikace a služby AD, které jste vytvořili dříve pro nástroj Configuration Manager. Pokud ještě nemáte pracovní prostor, přečtěte [si tématu Vytvoření pracovního prostoru ve službě Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) před pokračováním. To umožňuje nástroji Configuration Manager ověřit a připojit se k pracovnímu prostoru Log Analytics.  

> [!NOTE]
> V pracovním prostoru Analýzy protokolů je nutné zadat oprávnění pro Nástroj pro nástroj Configuration Manager. V opačném případě se při použití průvodce konfigurací ve Správci konfigurace zobrazí chybová zpráva.
>

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.

2. V seznamu pracovních prostorů Analýzy protokolů vyberte pracovní prostor, který chcete upravit.

3. V levém podokně vyberte **ovládací prvek přístupu (IAM).**

4. Na stránce Řízení přístupu (IAM) klikněte na **Přidat přiřazení role** a zobrazí se podokno Přidat přiřazení **role.**

5. V podokně **Přidat přiřazení rolí** vyberte v rozevíracím seznamu **Role** roli **Přispěvatel.**  

6. V rozevíracím seznamu **Přiřadit přístup k** aplikaci Configuration Manager vytvořené dříve ve službě AD vyberte a klepněte na tlačítko **OK**.  

## <a name="download-and-install-the-agent"></a>Stažení a instalace agenta

V článku [Připojení počítačů s Windows k Azure Monitoru v Azure,](agent-windows.md) abyste porozuměli metodám, které jsou k dispozici pro instalaci agenta Log Analytics pro Windows do počítače hostujícího roli systému lokality bodů připojení služby Configuration Manager.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Připojení nástroje Configuration Manager k pracovnímu prostoru Log Analytics

>[!NOTE]
> Chcete-li přidat připojení Log Analytics, musí mít prostředí nástroje Configuration Manager nakonfigurovaný [pro](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) režim online.

> [!NOTE]
> Je nutné připojit lokalitu nejvyšší úrovně ve vaší hierarchii k Azure Monitoru. Pokud připojíte samostatnou primární lokalitu k Azure Monitoru a pak přidáte lokalitu centrální správy do vašeho prostředí, budete muset odstranit a znovu vytvořit připojení v rámci nové hierarchie.

1. V pracovním prostoru **Správy** nástroje Configuration Manager vyberte **Cloud Services** a pak vyberte **Služby Azure**. 

2. Klikněte pravým tlačítkem myši na **Služby Azure** a pak vyberte **Konfigurovat služby Azure**. Zobrazí se stránka **Konfigurovat služby Azure.** 
   
3. Na obrazovce **Obecné** zkontrolujte, zda jste provedli následující akce a že máte podrobnosti pro každou položku, a pak vyberte **Další**.

4. Na stránce Služby Azure průvodce služby Azure:

    1. Ve Správci konfigurace zadejte **název** objektu.
    2. Zadejte volitelný **popis,** který vám pomůže identifikovat službu.
    3. Vyberte **konektor OMS**služby Azure .

    >[!NOTE]
    >OMS se teď označuje jako Log Analytics, což je funkce Azure Monitor.

5. Chcete-li pokračovat na stránku vlastností aplikace Azure průvodcem službami Azure, vyberte **možnost Další.**

6. Na stránce **App** průvodce službami Azure nejprve vyberte prostředí Azure ze seznamu a klikněte na **importovat**.

7. Na stránce **Importovat aplikace** zadejte následující informace:

    1. Zadejte **název klienta Azure AD** pro aplikaci.

    2. Zadejte pro **ID klienta Azure AD** klienta Azure AD. Tyto informace najdete na stránce **Vlastnosti služby** Azure Active Directory. 

    3. Zadejte pro **název aplikace** název aplikace.

    4. Zadejte pro **ID klienta**, ID aplikace vytvořené aplikace Azure AD vytvořené dříve.

    5. Zadejte pro **tajný klíč**, tajný klíč klienta vytvořené aplikace Azure AD.

    6. Zadejte pro **vypršení platnosti tajného klíče**, datum vypršení platnosti klíče.

    7. Zadejte pro **identifikátor URI ID**aplikace , identifikátor URI ID aplikace vytvořené aplikace Azure AD vytvořené dříve.

    8. Vyberte **Ověřit** a vpravo by se výsledky měly zobrazit **úspěšně ověřené!**.

8. Na stránce **Konfigurace** zkontrolujte informace a ověřte, zda jsou předvyplněná pole pracovního prostoru **Azure , skupiny** **prostředků Azure**a Operations **Management Suite,** což znamená, že aplikace Azure AD má ve skupině prostředků dostatečná oprávnění. Pokud jsou pole prázdná, znamená to, že vaše aplikace nemá požadovaná práva. Vyberte kolekce zařízení, které chcete shromažďovat a předávat do pracovního prostoru, a pak vyberte **Přidat**.

9. Zkontrolujte možnosti na stránce **Potvrdit nastavení** a vyberte **Další,** abyste začali vytvářet a konfigurovat připojení.

10. Po dokončení konfigurace se zobrazí stránka **Dokončení.** Vyberte **Zavřít**. 

Po propojení nástroje Configuration Manager s nástrojem Azure Monitor můžete přidat nebo odebrat kolekce a zobrazit vlastnosti připojení.

## <a name="update-log-analytics-workspace-connection-properties"></a>Aktualizovat vlastnosti připojení pracovního prostoru služby Log Analytics

Pokud vyprší platnost hesla nebo tajného klíče klienta nebo dojde ke ztrátě, budete muset ručně aktualizovat vlastnosti připojení Log Analytics.

1. V pracovním prostoru **Správce konfigurace vyberte** **Cloud Services** a pak vyberte **Konektor OMS,** chcete-li otevřít stránku **Vlastnosti připojení OMS.**
2. Na této stránce klikněte na kartu **Azure Active Directory** a zobrazte si **klienta**, **ID klienta**, **vypršení platnosti tajného klíče klienta**. **Ověřte** **tajný klíč klienta,** pokud vypršela jeho platnost.

## <a name="import-collections"></a>Import kolekce

Po přidání připojení analýzy protokolů do nástroje Configuration Manager a instalaci agenta do počítače se spuštěnou rolí lokality bodů připojení služby Configuration Manager je dalším krokem import kolekcí z nástroje Configuration Manager v Azure. Sledujte jako počítačové skupiny.

Po dokončení počáteční konfigurace pro import kolekcí zařízení z hierarchie jsou informace o kolekci načteny každé 3 hodiny, aby bylo členství aktuální. Tuto funkci můžete kdykoli zakázat.

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **pracovní prostory Analýzy protokolů**.
2. V seznamu pracovních prostorů Analýzy protokolů vyberte nástroj Configuration Manager pracovního prostoru, u který je zaregistrován.  
3. Vyberte **Upřesňující nastavení**.
4. Vyberte **Skupiny počítačů** a potom vyberte **SCCM**.  
5. Vyberte **Importovat členství v kolekci nástroje Configuration Manager** a klepněte na tlačítko **Uložit**.  
   
    ![Skupiny počítačů – karta SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Zobrazení dat z nástroje Configuration Manager

Po přidání připojení Analýzy protokolů do nástroje Configuration Manager a instalaci agenta do počítače se spuštěnou rolí lokality bodu připojení služby Configuration Manager se data z agenta odešlou do pracovního prostoru Log Analytics v Azure Monitoru. V Nástroji Azure Monitor se kolekce nástroje Configuration Manager zobrazují jako [skupiny počítačů](../../azure-monitor/platform/computer-groups.md). Skupiny můžete zobrazit na stránce **Správce konfigurace** v části **Nastavení\Skupiny počítačů**.

Po importu kolekcí můžete zjistit, kolik počítačů s členstvím v kolekci bylo zjištěno. Můžete také zobrazit počet kolekcí, které byly importovány.

![Skupiny počítačů – karta SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Po klepnutí na některou z nich se otevře editor dotazů protokolu zobrazující všechny importované skupiny nebo všechny počítače, které patří do každé skupiny. Pomocí [hledání protokolu](../../azure-monitor/log-query/log-query-overview.md)můžete provést další hloubkovou analýzu dat členství v kolekci.

## <a name="next-steps"></a>Další kroky

Pomocí [funkce Hledání protokolů](../../azure-monitor/log-query/log-query-overview.md) můžete zobrazit podrobné informace o datech nástroje Configuration Manager.
