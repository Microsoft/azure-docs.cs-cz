---
title: Propojení Configuration Manageru k Log Analytics | Dokumentace Microsoftu
description: Tento článek popisuje kroky k propojení Configuration Manageru k Log Analytics a začněte analyzovat data.
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
ms.component: ''
ms.openlocfilehash: 5cd28ec685842f6782699a7538e2f582c3226271
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52955936"
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Propojení Configuration Manageru k Log Analytics
Můžete svoje prostředí System Center Configuration Manager připojit ke službě Azure Log Analytics k synchronizaci zařízení shromažďování dat a odkazují na tyto kolekce v Log Analytics a Azure Automation.  

## <a name="prerequisites"></a>Požadavky

Log Analytics podporuje aktuální větve System Center Configuration Manageru verze 1606 a vyšší.  

## <a name="configuration-overview"></a>Přehled konfigurace
Následující kroky shrnují postup konfigurace integrace nástroje Configuration Manager pomocí služby Log Analytics.  

1. Na webu Azure Portal zaregistrujte se jako webovou aplikaci nebo webové rozhraní API aplikace nástroje Configuration Manager a ujistěte se, že máte ID klienta a tajný klíč klienta z registrace ze služby Azure Active Directory. Zobrazit [použití portálu k vytvoření služby Active Directory a instančního objektu, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md) podrobné informace o tom, k provedení tohoto kroku.
2. Na webu Azure Portal [udělit Configuration Manageru (zaregistrovanou webovou aplikaci) pomocí oprávnění pro přístup k Log Analytics](#grant-configuration-manager-with-permissions-to-log-analytics).
3. V nástroji Configuration Manager [přidat připojení pomocí Průvodce přidáním připojení OMS](#add-an-oms-connection-to-configuration-manager).
4. V nástroji Configuration Manager [aktualizovat vlastnosti připojení](#update-oms-connection-properties) Pokud tajný klíč klienta nebo hesla nikdy vyprší platnost nebo dojde ke ztrátě.
5. [Stáhnout a nainstalovat agenta Microsoft Monitoring Agent](#download-and-install-the-agent) v počítači s aplikací nástroji Configuration Manager service připojení role systému lokality bodu. Agent odesílá data Configuration Manageru k pracovnímu prostoru Log Analytics.
6. Ve službě Log Analytics [importovat kolekce z nástroje Configuration Manager](#import-collections) jako skupiny počítačů.
7. V Log Analytics, zobrazení dat z nástroje Configuration Manager jako [skupiny počítačů](../azure-monitor/platform/computer-groups.md).

Další informace o připojení nástroje Configuration Manager ke službě Log Analytics na [synchronizovat data z nástroje Configuration Manager ke službě Microsoft Log Analytics](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Udělení oprávnění ke službě Log Analytics verze Configuration Manager
V následujícím postupu udělíte *Přispěvatel* role ve vašem pracovním prostoru Log Analytics do aplikace AD a instanční objekt služby, které jste vytvořili dříve pro nástroj Configuration Manager.  Pokud již nemáte pracovní prostor, přečtěte si téma [vytvořit pracovní prostor v Azure Log Analytics](../azure-monitor/learn/quick-create-workspace.md) než budete pokračovat.  To umožňuje nástroji Configuration Manager k ověření a připojení k pracovnímu prostoru Log Analytics.  

> [!NOTE]
> Je třeba zadat oprávnění ve službě Log Analytics pro nástroj Configuration Manager. Jinak obdržíte chybovou zprávu při použití Průvodce konfigurací v nástroji Configuration Manager.
>

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.<br><br> ![Azure Portal](media/log-analytics-sccm/azure-portal-01.png)<br><br>  
2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor změnit.
3. V levém podokně vyberte **řízení přístupu (IAM)**.
4. V stránku řízení přístupu (IAM), klikněte na tlačítko **přidat přiřazení role** a **přidat přiřazení role** otevře se podokno.
5. V **přidat přiřazení role** podokně v části **Role** rozevíracího seznamu vyberte **Přispěvatel** role.  
6. V části **přiřadit přístup k** rozevíracího seznamu vyberte aplikace nástroje Configuration Manager předtím vytvořili ve službě AD a potom klikněte na tlačítko **OK**.  

## <a name="download-and-install-the-agent"></a>Stažení a instalace agenta
Přečtěte si článek [počítače Windows se připojit ke službě Log Analytics v Azure](../azure-monitor/platform/agent-windows.md) k porozumění metodám, k dispozici pro instalaci agenta Microsoft Monitoring Agent na počítači, který hostuje službu Configuration Manager role systému lokality spojovacího bodu.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Přidání připojení k Log Analytics do Configuration Manageru
Pokud chcete přidat připojení k Log Analytics, musíte mít prostředí Configuration Manageru [spojovací bod služby](https://technet.microsoft.com/library/mt627781.aspx) konfigurován pro online režim.

1. V **správu** pracovní prostor nástroje Configuration Manager, vyberte **konektor OMS**. Tím se otevře **přidat Průvodce připojením Log Analytics**. Vyberte **Další**.

   >[!NOTE]
   >OMS se teď označuje jako Log Analytics.
   
2. Na **Obecné** obrazovky, potvrďte, že jste provedli následující akce a že jste podrobnosti pro každou položku a pak vyberte **Další**.

   1. Na webu Azure Portal, zaregistrovaný nástroje Configuration Manager jako webovou aplikaci nebo webové rozhraní API app a že máte [ID klienta z registrace](../active-directory/develop/quickstart-v1-add-azure-ad-app.md).
   2. Na webu Azure Portal vytvoříte tajného klíče aplikace registrovaná aplikace v Azure Active Directory.  
   3. Na webu Azure Portal jste zadali zaregistrovanou webovou aplikaci s oprávněním pro přístup k Log Analytics.  
      ![Připojení k Log Analytics Průvodce Obecná stránka](./media/log-analytics-sccm/sccm-console-general01.png)
3. Na **Azure Active Directory** obrazovku, nakonfigurovat nastavení připojení ke službě Log Analytics tím, že poskytuje vaše **Tenanta**, **ID klienta**, a **klienta Tajný klíč**a pak vyberte **Další**.  
   ![Připojení k Log Analytics Průvodce služby Azure Active Directory stránky](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Pokud jste provedli všechny postupy úspěšně, potom informace na **konfigurace připojení k OMS** obrazovky se automaticky zobrazí na této stránce. Informace o nastavení připojení by se měla objevit pro vaše **předplatného Azure**, **skupiny prostředků Azure**, a **pracovní prostor Operations Management Suite**.  
   ![Připojení ke stránce připojení k Log Analytics Průvodce Log Analytics](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. Průvodce se připojí ke službě Log Analytics pomocí informace, které jste vstup. Vyberte kolekce zařízení, které chcete synchronizovat se službou a potom klikněte na tlačítko **přidat**.  
   ![Vybrat kolekce](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Ověřte nastavení připojení **Souhrn** obrazovky a pak vyberte **Další**. **Průběh** obrazovka se zobrazuje stav připojení a pak by měl **Complete**.

> [!NOTE]
> Musíte se připojit lokalitu nejvyšší úrovně ve vaší hierarchii ke službě Log Analytics. Pokud samostatný primární webový server připojení k Log Analytics a pak přidáte lokalitu centrální správy do vašeho prostředí, budete muset odstranit a znovu vytvořte připojení v nové hierarchii.
>
>

Po propojení nástroje Configuration Manager ke službě Log Analytics, můžete přidat nebo odebrat kolekce a zobrazit vlastnosti připojení.

## <a name="update-log-analytics-connection-properties"></a>Aktualizovat vlastnosti připojení k Log Analytics
Pokud někdy tajný klíč klienta nebo hesla vyprší platnost nebo dojde ke ztrátě, bude nutné ručně aktualizovat vlastnosti připojení Log Analytics.

1. V nástroji Configuration Manager přejděte na **Cloud Services**a pak vyberte **konektor OMS** otevřít **vlastnosti připojení OMS** stránky.
2. Na této stránce klikněte na tlačítko **Azure Active Directory** kartu k zobrazení vašeho **Tenanta**, **ID klienta**, **klienta vypršení platnosti tajného klíče**. **Ověřte** vaše **tajného klíče klienta** Pokud vypršela platnost.

## <a name="import-collections"></a>Importovat kolekce
Po přidání připojení k Log Analytics do nástroje Configuration Manager a instalace agenta na počítači se systémem připojení nástroje Configuration Manager služby role systému lokality bodu, dalším krokem je importovat kolekce z nástroje Configuration Manager v protokolu Analytics jako skupiny počítačů.

Po dokončení počáteční konfiguraci pro import kolekce zařízení v hierarchii, informace o členství v kolekci se načte každé tři hodiny zachovat aktuální členství. Můžete to kdykoliv vypnout.

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který je zaregistrován nástroje Configuration Manager.  
3. Vyberte **Upřesňující nastavení**.<br><br> ![Upřesňující nastavení Log Analytics](media/log-analytics-sccm/log-analytics-advanced-settings-01.png)<br><br>  
4. Vyberte **skupiny počítačů** a pak vyberte **SCCM**.  
5. Vyberte **členství v kolekcích Configuration Manageru Import** a potom klikněte na tlačítko **Uložit**.  
   ![Skupiny počítačů – SCCM kartu](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Zobrazení dat z nástroje Configuration Manager
Po přidání připojení k Log Analytics do nástroje Configuration Manager a instalace agenta v počítači se spuštěnou nástroji Configuration Manager service připojení role systému lokality bodu, z agenta se odešlou do služby Log Analytics. Vaše kolekce nástroje Configuration Manager ve službě Log Analytics se zobrazí jako [skupiny počítačů](../azure-monitor/platform/computer-groups.md). Můžete zobrazit skupiny z **nástroje Configuration Manager** stránky **Settings\Computer skupiny**.

Po importu kolekce se zobrazí, kolik počítačů s členstvím v kolekci byl zjištěn. Zobrazí se také počet kolekcí, které byly naimportovány.

![Skupiny počítačů – SCCM kartu](./media/log-analytics-sccm/sccm-computer-groups02.png)

Po kliknutí na některé z nich, otevře se vyhledávání, zobrazí všechny importované skupiny nebo všechny počítače, které patří do jednotlivých skupin. Pomocí [prohledávání protokolů](../azure-monitor/log-query/log-query-overview.md), můžete začít potřebují podrobně analyzovat data Configuration Manageru.

## <a name="next-steps"></a>Další postup
* Použití [prohledávání protokolů](../azure-monitor/log-query/log-query-overview.md) Chcete-li zobrazit podrobné informace týkající se vašich dat nástroje Configuration Manager.
