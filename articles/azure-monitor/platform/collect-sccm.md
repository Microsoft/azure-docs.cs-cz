---
title: Propojení Configuration Manageru do Azure Monitor | Dokumentace Microsoftu
description: Tento článek popisuje kroky k propojení Configuration Manageru do pracovního prostoru ve službě Azure Monitor a začněte analyzovat data.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: 26ddb0cdd2728f9dff5d45494a14841cdc1a20cd
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922889"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Propojení Configuration Manageru do Azure monitoru
Můžete připojit prostředí System Center Configuration Manager do Azure monitoru k synchronizaci zařízení shromažďování dat a odkazují na tyto kolekce v Azure Monitor a Azure Automation.  

## <a name="prerequisites"></a>Požadavky

Azure Monitor podporuje aktuální větve System Center Configuration Manageru verze 1606 a vyšší.  

## <a name="configuration-overview"></a>Přehled konfigurace
Následující kroky shrnují postup konfigurace integrace nástroje Configuration Manager pomocí Azure monitoru.  

1. Na webu Azure Portal zaregistrujte se jako webovou aplikaci nebo webové rozhraní API aplikace nástroje Configuration Manager a ujistěte se, že máte ID klienta a tajný klíč klienta z registrace ze služby Azure Active Directory. Zobrazit [použití portálu k vytvoření služby Active Directory a instančního objektu, který má přístup k prostředkům](../../active-directory/develop/howto-create-service-principal-portal.md) podrobné informace o tom, k provedení tohoto kroku.
2. Na webu Azure Portal [udělit Configuration Manageru (zaregistrovanou webovou aplikaci) pomocí oprávnění pro přístup k Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).
3. V nástroji Configuration Manager, přidejte připojení pomocí Průvodce přidáním připojení OMS.
4. V nástroji Configuration Manager, aktualizujte vlastnosti připojení, pokud tajný klíč klienta nebo hesla nikdy vyprší platnost nebo dojde ke ztrátě.
5. [Stáhnout a nainstalovat agenta Microsoft Monitoring Agent](#download-and-install-the-agent) v počítači s aplikací nástroji Configuration Manager service připojení role systému lokality bodu. Agent odesílá data Configuration Manageru k pracovnímu prostoru Log Analytics ve službě Azure Monitor.
6. Ve službě Azure Monitor [importovat kolekce z nástroje Configuration Manager](#import-collections) jako skupiny počítačů.
7. Ve službě Azure Monitor, zobrazení dat z nástroje Configuration Manager jako [skupiny počítačů](computer-groups.md).

Další informace o připojení nástroje Configuration Manager do Azure monitoru v [synchronizaci dat z nástroje Configuration Manager s pracovním prostorem Microsoft Log Analytics](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Udělení oprávnění ke službě Log Analytics verze Configuration Manager
V následujícím postupu udělíte *Přispěvatel* role ve vašem pracovním prostoru Log Analytics do aplikace AD a instanční objekt služby, které jste vytvořili dříve pro nástroj Configuration Manager.  Pokud již nemáte pracovní prostor, přečtěte si téma [vytvořit pracovní prostor ve službě Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) než budete pokračovat.  To umožňuje nástroji Configuration Manager k ověření a připojení k pracovnímu prostoru Log Analytics.  

> [!NOTE]
> Je třeba zadat oprávnění v pracovním prostoru Log Analytics pro nástroj Configuration Manager. Jinak obdržíte chybovou zprávu při použití Průvodce konfigurací v nástroji Configuration Manager.
>

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor změnit.
3. V levém podokně vyberte **řízení přístupu (IAM)**.
4. V stránku řízení přístupu (IAM), klikněte na tlačítko **přidat přiřazení role** a **přidat přiřazení role** otevře se podokno.
5. V **přidat přiřazení role** podokně v části **Role** rozevíracího seznamu vyberte **Přispěvatel** role.  
6. V části **přiřadit přístup k** rozevíracího seznamu vyberte aplikace nástroje Configuration Manager předtím vytvořili ve službě AD a potom klikněte na tlačítko **OK**.  

## <a name="download-and-install-the-agent"></a>Stažení a instalace agenta
Přečtěte si článek [počítače připojit Windows do Azure monitoru v Azure](agent-windows.md) k porozumění metodám, k dispozici pro instalaci agenta Microsoft Monitoring Agent na počítači, který hostuje spojovací bod služby nástroje Configuration Manager role systému lokality.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Přidání připojení k Log Analytics do Configuration Manageru
Pokud chcete přidat připojení k Log Analytics, musíte mít prostředí Configuration Manageru [spojovací bod služby](https://technet.microsoft.com/library/mt627781.aspx) konfigurován pro online režim.

1. V **správu** pracovní prostor nástroje Configuration Manager, vyberte **konektor OMS**. Tím se otevře **přidat Průvodce připojením Log Analytics**. Vyberte **Další**.

   >[!NOTE]
   >OMS se teď označuje jako Log Analytics, což je funkce služby Azure Monitor.
   
2. Na **Obecné** obrazovky, potvrďte, že jste provedli následující akce a že jste podrobnosti pro každou položku a pak vyberte **Další**.

   1. Na webu Azure Portal, zaregistrovaný nástroje Configuration Manager jako webovou aplikaci nebo webové rozhraní API app a že máte [ID klienta z registrace](../../active-directory/develop/quickstart-register-app.md).
   2. Na webu Azure Portal vytvoříte tajného klíče aplikace registrovaná aplikace v Azure Active Directory.  
   3. Na webu Azure Portal jste zadali zaregistrovanou webovou aplikaci s oprávněním pro přístup k pracovnímu prostoru Log Analytics ve službě Azure Monitor.  
      ![Připojení k Log Analytics Průvodce Obecná stránka](./media/collect-sccm/sccm-console-general01.png)
3. Na **Azure Active Directory** obrazovku, nakonfigurovat nastavení připojení k pracovnímu prostoru Log Analytics tím, že poskytuje vaše **Tenanta**, **ID klienta**a **Tajný klíč klienta**a pak vyberte **Další**.  
   ![Připojení k Log Analytics Průvodce služby Azure Active Directory stránky](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. Pokud jste provedli všechny postupy úspěšně, potom informace na **konfigurace připojení k OMS** obrazovky se automaticky zobrazí na této stránce. Informace o nastavení připojení by se měla objevit pro vaše **předplatného Azure**, **skupiny prostředků Azure**, a **pracovní prostor Operations Management Suite**.  
   ![Připojení ke stránce připojení k Log Analytics Průvodce Log Analytics](./media/collect-sccm/sccm-wizard-configure04.png)
5. Průvodce se připojí k pracovnímu prostoru Log Analytics pomocí informace, které jste vstup. Vyberte kolekce zařízení, které chcete synchronizovat se službou a potom klikněte na tlačítko **přidat**.  
   ![Vybrat kolekce](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. Ověřte nastavení připojení **Souhrn** obrazovky a pak vyberte **Další**. **Průběh** obrazovka se zobrazuje stav připojení a pak by měl **Complete**.

> [!NOTE]
> Musíte se připojit lokalitu nejvyšší úrovně ve vaší hierarchii, do Azure monitoru. Pokud samostatný primární webový server připojení k Azure Monitor a pak přidáte lokalitu centrální správy do vašeho prostředí, budete muset odstranit a znovu vytvořte připojení v nové hierarchii.
>
>

Po propojení nástroje Configuration Manager do Azure monitoru, můžete přidat nebo odebrat kolekce a zobrazit vlastnosti připojení.

## <a name="update-log-analytics-workspace-connection-properties"></a>Aktualizovat vlastnosti připojení pracovního prostoru Log Analytics
Pokud někdy tajný klíč klienta nebo hesla vyprší platnost nebo dojde ke ztrátě, bude nutné ručně aktualizovat vlastnosti připojení Log Analytics.

1. V nástroji Configuration Manager přejděte na **Cloud Services**a pak vyberte **konektor OMS** otevřít **vlastnosti připojení OMS** stránky.
2. Na této stránce klikněte na tlačítko **Azure Active Directory** kartu k zobrazení vašeho **Tenanta**, **ID klienta**, **klienta vypršení platnosti tajného klíče**. **Ověřte** vaše **tajného klíče klienta** Pokud vypršela platnost.

## <a name="import-collections"></a>Importovat kolekce
Po přidání připojení k Log Analytics do nástroje Configuration Manager a instalace agenta na počítači se systémem připojení nástroje Configuration Manager služby role systému lokality bodu, dalším krokem je importovat kolekce z nástroje Configuration Manager v Azure Monitorovat jako skupiny počítačů.

Po dokončení počáteční konfiguraci pro import kolekce zařízení v hierarchii, informace o členství v kolekci se načte každé tři hodiny zachovat aktuální členství. Můžete to kdykoliv vypnout.

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **pracovních prostorů Log Analytics**.
2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který je zaregistrován nástroje Configuration Manager.  
3. Vyberte **Upřesňující nastavení**.
4. Vyberte **skupiny počítačů** a pak vyberte **SCCM**.  
5. Vyberte **členství v kolekcích Configuration Manageru Import** a potom klikněte na tlačítko **Uložit**.  
   ![Skupiny počítačů – SCCM kartu](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Zobrazení dat z nástroje Configuration Manager
Po přidání připojení k Log Analytics do nástroje Configuration Manager a instalace agenta v počítači se spuštěnou nástroji Configuration Manager service připojení role systému lokality bodu, z agenta se odešlou do pracovního prostoru Log Analytics ve službě Azure Monitor. Vaše kolekce nástroje Configuration Manager ve službě Azure Monitor, se zobrazí jako [skupiny počítačů](../../azure-monitor/platform/computer-groups.md). Můžete zobrazit skupiny z **nástroje Configuration Manager** stránky **Settings\Computer skupiny**.

Po importu kolekce se zobrazí, kolik počítačů s členstvím v kolekci byl zjištěn. Zobrazí se také počet kolekcí, které byly naimportovány.

![Skupiny počítačů – SCCM kartu](./media/collect-sccm/sccm-computer-groups02.png)

Po kliknutí na některé z nich, otevře se vyhledávání, zobrazí všechny importované skupiny nebo všechny počítače, které patří do jednotlivých skupin. Pomocí [prohledávání protokolů](../../azure-monitor/log-query/log-query-overview.md), můžete začít potřebují podrobně analyzovat data Configuration Manageru.

## <a name="next-steps"></a>Další postup
* Použití [prohledávání protokolů](../../azure-monitor/log-query/log-query-overview.md) Chcete-li zobrazit podrobné informace týkající se vašich dat nástroje Configuration Manager.
