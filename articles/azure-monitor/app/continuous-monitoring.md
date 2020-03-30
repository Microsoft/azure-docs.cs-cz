---
title: Průběžné monitorování kanálu vydání DevOps pomocí Azure Pipelines a Azure Application Insights | Dokumenty společnosti Microsoft
description: Obsahuje pokyny pro rychlé nastavení průběžného monitorování pomocí funkce Application Insights
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: e565101218b975ef2bd29b8a32a4aa1bf4300b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655391"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Přidání průběžného monitorování do kanálu vydání

Azure Pipelines se integruje s Azure Application Insights, aby bylo možné nepřetržitě sledovat kanál vydání DevOps v průběhu životního cyklu vývoje softwaru. 

Díky průběžnému monitorování můžou kanály verzí zahrnovat data monitorování z Application Insights a dalších prostředků Azure. Když kanál vydání zjistí výstrahu Application Insights, kanál může gate nebo vrátit zpět nasazení, dokud se výstraha vyřešena. Pokud všechny kontroly projít, nasazení může probíhat automaticky z testování celou cestu do produkčního prostředí, bez nutnosti ručního zásahu. 

## <a name="configure-continuous-monitoring"></a>Konfigurace nepřetržitého monitorování

1. V [Azure DevOps](https://dev.azure.com)vyberte organizaci a projekt.
   
1. V levé nabídce stránky projektu vyberte **Možnost Uvolnění** > kanálů **.** 
   
1. Rozbalte šipku vedle **položky Nový** a vyberte **Nový kanál vydání**. Nebo pokud ještě nemáte kanál, vyberte nový **kanál** na stránce, která se zobrazí.
   
1. V podokně **Vybrat předlohu** vyhledejte a vyberte **nasazení služby Azure App Service s nepřetržitým monitorováním**a pak vyberte **Použít**. 

   ![Nový kanál y Azure Pipelines](media/continuous-monitoring/001.png)

1. V poli **Fáze 1** vyberte hypertextový odkaz na zobrazení **úkolů fáze.**

   ![Zobrazení úkolů fáze](media/continuous-monitoring/002.png)

1. V konfiguračním podokně **Fáze 1** vyplňte následující pole: 

    | Parametr        | Hodnota |
   | ------------- |:-----|
   | **Název fáze**      | Zadejte název etapy nebo jej ponechte ve **fázi 1**. |
   | **Předplatné Azure** | Rozbalte a vyberte propojené předplatné Azure, které chcete použít.|
   | **Typ aplikace** | Rozbalte a vyberte typ aplikace. |
   | **Název služby App Service** | Zadejte název služby Azure App Service. |
   | **Název skupiny prostředků pro přehledy aplikací**    | Rozevírací seznam a vyberte skupinu prostředků, kterou chcete použít. |
   | **Název prostředku Application Insights** | Rozbalte a vyberte prostředek Application Insights pro vybranou skupinu prostředků.

1. Pokud chcete uložit kanál s výchozím nastavením pravidla výstrahy, vyberte **Uložit** vpravo nahoře v okně Azure DevOps. Zadejte popisný komentář a pak vyberte **OK**.

## <a name="modify-alert-rules"></a>Úprava pravidel výstrah

Mimo pole má **nasazení služby Azure App Service se šablonou průběžného monitorování** čtyři pravidla výstrah: **dostupnost**, neúspěšné požadavky , doba **odezvy** **serveru**a **výjimky serveru**. Můžete přidat další pravidla nebo změnit nastavení pravidel tak, aby vyhovovalo potřebám úrovně služeb. 

Změna nastavení pravidla výstrahy:

1. V levém podokně stránky kanálu vydání vyberte **Konfigurovat výstrahy přehledů aplikací**.

1. V podokně **Výstrahy monitoru Azure** vyberte tři tečky **...** vedle **pravidel výstrah**.
   
1. V dialogovém okně **Pravidla výstrah** vyberte symbol rozevíracího souboru vedle pravidla výstrahy, například **Dostupnost**. 
   
1. Upravte **prahovou hodnotu** a další nastavení tak, aby vyhovovaly vašim požadavkům.
   
   ![Upravit výstrahu](media/continuous-monitoring/003.png)
   
1. V yberte **OK**a pak v okně Azure DevOps vyberte **Uložit** vpravo nahoře. Zadejte popisný komentář a pak vyberte **OK**.

## <a name="add-deployment-conditions"></a>Přidání podmínek nasazení

Když přidáte brány nasazení do kanálu vydání, výstraha, která překročí nastavené prahové hodnoty, zabrání nechtěné propagaci vydání. Po vyřešení výstrahy může nasazení pokračovat automaticky.

Přidání bran nasazení:

1. Na stránce hlavního kanálu včásti **Fáze**vyberte symbol **Podmínky před nasazením** nebo Podmínky po **nasazení,** v závislosti na tom, která fáze vyžaduje nepřetržitou monitorovací bránu.
   
   ![Podmínky před nasazením](media/continuous-monitoring/004.png)
   
1. V konfiguračním podokně **Podmínky před nasazením** nastavte **gate gate na** **Povoleno**.
   
1. Vedle **brány nasazení**vyberte **Přidat**.
   
1. V rozevírací nabídce vyberte **výstrahy Programu Azure Monitor** dotazu. Tato možnost umožňuje přístup k výstrahám Azure Monitor a Application Insights.
   
   ![Dotaz na výstrahy Azure Monitor](media/continuous-monitoring/005.png)
   
1. V části **Možnosti vyhodnocení**zadejte požadované hodnoty pro nastavení, jako **je Doba mezi přehodnocením bran** a Časový **majek, po kterém brány selžou**. 

## <a name="view-release-logs"></a>Zobrazit protokoly verzí

Chování brány nasazení a další kroky vydání se zobrazí v protokolech vydání. Otevření protokolů:

1. V levé nabídce stránky kanálu vyberte **Uvolnit.** 
   
1. Vyberte libovolnou verzi. 
   
1. V části **Fáze**vyberte libovolnou fázi, ve které chcete zobrazit souhrn vydání. 
   
1. Chcete-li zobrazit protokoly, vyberte **možnost Zobrazit protokoly** v souhrnu vydání, vyberte hypertextový odkaz **Proběhlý** nebo **Neúspěšný** v libovolné fázi nebo najeďte na libovolnou fázi a vyberte **Protokoly**. 
   
   ![Zobrazit protokoly verzí](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Další kroky

Další informace o Azure Pipelines najdete v [dokumentaci k Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines).
