---
title: Připojit Configuration Manager k Azure Monitor | Microsoft Docs
description: Tento článek popisuje kroky pro připojení Configuration Manager k pracovnímu prostoru v Azure Monitor a zahájení analýzy dat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 20d55de7ef0f7b7f49b922409f8cb61874fae0f6
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448159"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Připojit Configuration Manager k Azure Monitor
Prostředí Microsoft Endpoint Configuration Manager můžete připojit, aby se Azure Monitor synchronizovaná data kolekce zařízení a odkazovala na tyto kolekce v Azure Monitor a Azure Automation.  

## <a name="prerequisites"></a>Požadavky

Azure Monitor podporuje Configuration Manager aktuální větev verze 1606 a vyšší.

>[!NOTE]
>Funkce pro připojení Configuration Manager k pracovnímu prostoru Log Analytics je volitelná a není ve výchozím nastavení povolená. Tuto funkci musíte před použitím povolit. Další informace naleznete v části [Enable optional features from updates](/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Přehled konfigurace

Následující kroky shrnují postup konfigurace Configuration Manager integrace s Azure Monitor.  

1. V Azure Active Directory Zaregistrujte Configuration Manager jako webovou aplikaci nebo aplikaci webového rozhraní API a ujistěte se, že máte ID klienta a tajný klíč klienta z registrace z Azure Active Directory. Podrobné informace o tom, jak tento krok provést, najdete v tématu [použití portálu k vytvoření aplikace služby Active Directory a instančního objektu, který má přístup k prostředkům](../../active-directory/develop/howto-create-service-principal-portal.md) .

2. V Azure Active Directory [udělte Configuration Manager (registrovaná webová aplikace) oprávnění pro přístup k Azure monitor](#grant-configuration-manager-with-permissions-to-log-analytics).

3. V Configuration Manager přidejte připojení pomocí průvodce **službami Azure** .

4. [Stáhněte a nainstalujte agenta Log Analytics pro systém Windows](#download-and-install-the-agent) na počítač se spuštěnou rolí systému lokality Configuration Manager spojovacího bodu služby. Agent odesílá data Configuration Manager do pracovního prostoru Log Analytics v Azure Monitor.

5. V Azure Monitor [importujte kolekce z Configuration Manager](#import-collections) jako skupiny počítačů.

6. V Azure Monitor zobrazit data z Configuration Manager jako [skupiny počítačů](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Udělení Configuration Manager s oprávněním Log Analytics

V následujícím postupu udělíte roli *Přispěvatel* v pracovním prostoru Log Analytics k aplikacím a instančnímu objektu služby AD, který jste vytvořili dříve pro Configuration Manager. Pokud ještě nemáte pracovní prostor, přečtěte si téma [Vytvoření pracovního prostoru v Azure monitor](../learn/quick-create-workspace.md) než budete pokračovat. To umožňuje Configuration Manager ověřovat a připojovat se k pracovnímu prostoru Log Analytics.  

> [!NOTE]
> Pro Configuration Manager je nutné zadat oprávnění v pracovním prostoru Log Analytics. V opačném případě se zobrazí chybová zpráva, když použijete Průvodce konfigurací v nástroji Configuration Manager.
>

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.

2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který chcete upravit.

3. V levém podokně vyberte **řízení přístupu (IAM)**.

4. Na stránce řízení přístupu (IAM) klikněte na **Přidat přiřazení role** a otevře se podokno **Přidat přiřazení role** .

5. V podokně **Přidat přiřazení role** v rozevíracím seznamu **role** vyberte roli **Přispěvatel** .  

6. V rozevíracím seznamu **přiřadit přístup k** vyberte Configuration Manager aplikaci vytvořenou ve službě AD dříve a pak klikněte na **OK**.  

## <a name="download-and-install-the-agent"></a>Stažení a instalace agenta

Přečtěte si článek [připojení počítačů s Windows k Azure monitor v Azure](agent-windows.md) , abyste pochopili metody dostupné pro instalaci agenta Log Analytics pro Windows v počítači, který je hostitelem role systému lokality bodu připojení služby Configuration Manager.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Připojení Configuration Manager k pracovnímu prostoru Log Analytics

>[!NOTE]
> Aby bylo možné přidat Log Analytics připojení, musí mít Configuration Manager prostředí pro online režim nakonfigurovaný [spojovací bod služby](/configmgr/core/servers/deploy/configure/about-the-service-connection-point) .

> [!NOTE]
> Lokalitu nejvyšší úrovně ve vaší hierarchii musíte připojit k Azure Monitor. Pokud k Azure Monitor připojíte samostatnou primární lokalitu a potom do svého prostředí přidáte lokalitu centrální správy, je nutné odstranit a znovu vytvořit připojení v rámci nové hierarchie.

1. V pracovním prostoru **správa** Configuration Manager vyberte **cloudové služby** a pak vyberte **služby Azure**. 

2. Klikněte pravým tlačítkem na **služby Azure** a pak vyberte **Konfigurovat služby Azure**. Zobrazí se stránka **Konfigurace služeb Azure** . 
   
3. Na obrazovce **Obecné** potvrďte, že jste provedli následující akce a že máte podrobnosti pro každou položku, a pak vyberte **Další**.

4. Na stránce služby Azure v Průvodci službami Azure:

    1. Zadejte **název** objektu v Configuration Manager.
    2. Zadejte volitelný **Popis** , který vám usnadní identifikaci služby.
    3. Vyberte konektor služby Azure Service **OMS**.

    >[!NOTE]
    >OMS se teď označuje jako Log Analytics což je funkce Azure Monitor.

5. Výběrem možnosti **Další** přejděte na stránku vlastností aplikace Azure Průvodce službami Azure.

6. Na stránce **aplikace** v Průvodci službami Azure nejdřív vyberte prostředí Azure ze seznamu a klikněte na **importovat**.

7. Na stránce **importovat aplikace** zadejte následující informace:

    1. Zadejte **název tenanta Azure AD** pro aplikaci.

    2. Zadejte pro **ID tenanta Azure AD** tohoto TENANTA Azure AD. Tyto informace najdete na stránce **vlastností** Azure Active Directory. 

    3. Jako **název aplikace** zadejte název aplikace.

    4. Zadejte pro **ID klienta**ID aplikace vytvořené aplikace Azure AD, kterou jste vytvořili dříve.

    5. Zadejte tajný **klíč**klienta pro vytvořenou aplikaci Azure AD.

    6. Zadejte pro **vypršení platnosti tajného klíče**, datum vypršení platnosti klíče.

    7. Zadejte identifikátor **URI ID aplikace**. identifikátor ID aplikace pro vytvořenou aplikaci Azure AD, kterou jste vytvořili dříve.

    8. Vyberte možnost **ověřit** a napravo by se měly výsledky zobrazit po **úspěšném ověření**.

8. Na stránce **Konfigurace** si přečtěte informace, abyste ověřili, že pole pracovních prostorů **Azure**, **skupiny prostředků Azure**a **pracovního prostoru Operations Management Suite** jsou předem vyplněná, což značí, že aplikace Azure AD má ve skupině prostředků dostatečná oprávnění. Pokud jsou pole prázdná, znamená to, že vaše aplikace nemá požadovaná práva. Vyberte kolekce zařízení, které chcete shromáždit a předejte do pracovního prostoru, a pak vyberte **Přidat**.

9. Zkontrolujte možnosti na stránce **Potvrdit nastavení** a vyberte **Další** a začněte vytvářet a konfigurovat připojení.

10. Po dokončení konfigurace se zobrazí stránka **dokončení** . Vyberte **Zavřít**. 

Po propojení Configuration Manager s Azure Monitor můžete přidat nebo odebrat kolekce a zobrazit vlastnosti připojení.

## <a name="update-log-analytics-workspace-connection-properties"></a>Aktualizovat vlastnosti připojení k pracovnímu prostoru Log Analytics

Pokud heslo nebo tajný klíč klienta vyprší nebo dojde ke ztrátě, budete muset ručně aktualizovat vlastnosti Log Analyticsho připojení.

1. V pracovním prostoru **správa** Configuration Manager vyberte **Cloud Services** a pak výběrem možnosti **konektor OMS** otevřete stránku **vlastností připojení OMS** .
2. Na této stránce klikněte na kartu **Azure Active Directory** pro zobrazení vašeho **tenanta**, **ID klienta**, **vypršení platnosti tajného klíče klienta**. **Ověřte** , jestli vypršela platnost **tajného klíče klienta** .

## <a name="import-collections"></a>Importovat kolekce

Po přidání Log Analytics připojení k Configuration Manager a instalaci agenta do počítače, na kterém je spuštěná role systému lokality spojovacího bodu služby Configuration Manager, je dalším krokem import kolekcí z Configuration Manager v Azure Monitor jako skupiny počítačů.

Po dokončení počáteční konfigurace pro import kolekcí zařízení z vaší hierarchie se informace o kolekci načtou každé 3 hodiny, aby se členství zachovalo aktuální. Tuto možnost můžete kdykoli zakázat.

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics pracovní prostory**.
2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor Configuration Manager je zaregistrován v.  
3. Vyberte **Upřesňující nastavení**.
4. Vyberte **skupiny počítačů** a pak vyberte **SCCM**.  
5. Vyberte **Import Configuration Manager členství v kolekcích** a pak klikněte na **Uložit**.  
   
    ![Snímek obrazovky s rozšířenými nastaveními skupiny počítačů pro S C C M, který obsahuje možnost pro import Configuration Manager členství v kolekcích.](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Zobrazit data z Configuration Manager

Po přidání Log Analytics připojení Configuration Manager a instalaci agenta do počítače se spuštěnou rolí systému lokality spojovacího bodu služby Configuration Manager služba se data z agenta odesílají do Log Analytics pracovního prostoru v Azure Monitor. V Azure Monitor se kolekce Configuration Manager zobrazí jako [skupiny počítačů](./computer-groups.md). Skupiny můžete zobrazit na stránce **Configuration Manager** v části **skupiny Settings\Computer**.

Po importu kolekcí můžete zjistit, kolik počítačů s členstvím v kolekci bylo zjištěno. Můžete také zobrazit počet importovaných kolekcí.

![Snímek obrazovky s rozšířenými nastaveními skupiny počítačů pro S C C M ukazující možnost importu vybraných členství kolekce Configuration Manager.](./media/collect-sccm/sccm-computer-groups02.png)

Když kliknete na jeden, otevře se Editor dotazů protokolů, ve kterém se zobrazí všechny importované skupiny nebo všechny počítače, které patří do jednotlivých skupin. Pomocí [prohledávání protokolů](../log-query/log-query-overview.md)můžete provádět podrobnější analýzu dat členství v kolekci.

## <a name="next-steps"></a>Další kroky

Pomocí [prohledávání protokolu](../log-query/log-query-overview.md) můžete zobrazit podrobné informace o datech Configuration Manager.

