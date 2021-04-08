---
title: Nepřetržité monitorování kanálu pro vydávání verzí DevOps pomocí Azure Pipelines a Azure Application Insights | Microsoft Docs
description: Poskytuje pokyny pro rychlé nastavení nepřetržitého monitorování pomocí Application Insights
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: fd7cd6a107ed45adb60167a57661b60be5dc8212
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86517123"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Přidání průběžného monitorování do kanálu pro vydávání verzí

Azure Pipelines se integruje se službou Azure Application Insights, která umožňuje nepřetržité monitorování kanálu verzí DevOps v průběhu životního cyklu vývoje softwaru. 

Díky průběžnému monitorování můžou kanály vydávání verzí zahrnovat monitorování dat z Application Insights a dalších prostředků Azure. Když kanál vydaných verzí zjistí výstrahu Application Insights, kanál může bránu nebo vrácení nasazení vrátit, dokud se upozornění nevyřeší. Pokud všechny kontroly projde, můžou nasazení automaticky pokračovat z testu na produkční prostředí bez nutnosti ručního zásahu. 

## <a name="configure-continuous-monitoring"></a>Konfigurovat průběžné monitorování

1. V [Azure DevOps](https://dev.azure.com)vyberte organizaci a projekt.
   
1. V levé nabídce stránky projektu vyberte možnost   >  **vydané verze** kanálů. 
   
1. Přetáhněte šipku vedle tlačítka **Nový** a vyberte **Nový kanál vydání**. Nebo, pokud ještě nemáte kanál, vyberte na zobrazené stránce **Nový kanál** .
   
1. V podokně **Vybrat šablonu** vyhledejte a vyberte **nasazení Azure App Service s nepřetržitým monitorováním** a pak vyberte **použít**. 

   ![Nový kanál verze Azure Pipelines](media/continuous-monitoring/001.png)

1. V poli **fáze 1** vyberte hypertextový odkaz pro **zobrazení úloh fáze.**

   ![Zobrazit úlohy fáze](media/continuous-monitoring/002.png)

1. V podokně Konfigurace **fáze 1** vyplňte následující pole: 

    | Parametr        | Hodnota |
   | ------------- |:-----|
   | **Název fáze**      | Zadejte název fáze nebo ho ponechte ve **fázi 1**. |
   | **Předplatné Azure** | Rozevírací seznam a vyberte propojené předplatné Azure, které chcete použít.|
   | **Typ aplikace** | Rozevírací seznam a vyberte typ aplikace. |
   | **Název App Service** | Zadejte název vašeho Azure App Service. |
   | **Název skupiny prostředků pro Application Insights**    | Rozevírací seznam a vyberte skupinu prostředků, kterou chcete použít. |
   | **Název prostředku Application Insights** | Rozevírací seznam a vyberte prostředek Application Insights pro skupinu prostředků, kterou jste vybrali.

1. Pokud chcete uložit kanál s výchozím nastavením pravidla upozornění, vyberte **Uložit** v pravém horním rohu okna Azure DevOps. Zadejte popisný komentář a pak vyberte **OK**.

## <a name="modify-alert-rules"></a>Upravit pravidla upozornění

Dopředné: **nasazení Azure App Service se šablonou nepřetržitého monitorování** má čtyři pravidla výstrahy **: dostupnost**, **neúspěšné požadavky**, **Doba odezvy serveru** a **výjimky serveru**. Můžete přidat další pravidla nebo změnit nastavení pravidla tak, aby splňovalo potřeby úrovně služeb. 

Úprava nastavení pravidla výstrahy:

V levém podokně stránky kanál verze vyberte **konfigurovat Application Insights výstrahy**.

Čtyři výchozí pravidla výstrah se vytvářejí prostřednictvím vloženého skriptu:

```bash
$subscription = az account show --query "id";$subscription.Trim("`"");$resource="/subscriptions/$subscription/resourcegroups/"+"$(Parameters.AppInsightsResourceGroupName)"+"/providers/microsoft.insights/components/" + "$(Parameters.ApplicationInsightsResourceName)";
az monitor metrics alert create -n 'Availability_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg availabilityResults/availabilityPercentage < 99' --description "created from Azure DevOps";
az monitor metrics alert create -n 'FailedRequests_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count requests/failed > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerResponseTime_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg requests/duration > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerExceptions_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count exceptions/server > 5' --description "created from Azure DevOps";
```

Můžete upravit skript a přidat další pravidla upozornění, upravit podmínky upozornění nebo odebrat pravidla výstrah, která nedávají smysl pro vaše účely nasazení.

## <a name="add-deployment-conditions"></a>Přidat podmínky nasazení

Když do kanálu pro vydání přidáte brány nasazení, výstraha, která přesahuje nastavené prahové hodnoty, zabrání v povýšení nechtěného vydání. Po vyřešení výstrahy může nasazení automaticky pokračovat.

Přidání bran nasazení:

1. Na hlavní stránce kanálu vyberte v části **fáze** možnost **podmínky před nasazením** nebo symbol **podmínky po nasazení** . v závislosti na tom, kterou fázi potřebuje nepřetržitý monitorovací bránu.
   
   ![Podmínky před nasazením](media/continuous-monitoring/004.png)
   
1. V podokně Konfigurace **podmínek před nasazením** nastavte možnost **brány** na **povoleno**.
   
1. Vedle pole **brány nasazení** vyberte **Přidat**.
   
1. V rozevírací nabídce vyberte možnost **dotaz Azure monitor výstrahy** . Tato možnost umožňuje přístup k výstrahám Azure Monitor i Application Insights.
   
   ![Výstrahy Azure Monitor dotazů](media/continuous-monitoring/005.png)
   
1. V části **Možnosti vyhodnocení** zadejte hodnoty, jako je **čas mezi opakovaným vyhodnocením bran** a **časovým limitem, po kterém selžou**. 

## <a name="view-release-logs"></a>Zobrazit protokoly verzí

V protokolech vydaných verzí uvidíte chování brány nasazení a další kroky vydání. Otevření protokolů:

1. V levé nabídce stránky kanálu vyberte **releases (vydané verze** ). 
   
1. Vyberte libovolnou verzi. 
   
1. V části **fáze** výběrem libovolné fáze zobrazíte souhrn vydaných verzí. 
   
1. Pokud chcete zobrazit protokoly, vyberte v souhrnu verzí možnost **Zobrazit protokoly** , v libovolné fázi vyberte hypertextový odkaz **úspěšný** nebo **neúspěšný** , nebo najeďte myší na libovolnou fázi a vyberte **protokoly**. 
   
   ![Zobrazit protokoly verzí](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Další kroky

Další informace o Azure Pipelines najdete v dokumentaci k [Azure Pipelines](/azure/devops/pipelines).
