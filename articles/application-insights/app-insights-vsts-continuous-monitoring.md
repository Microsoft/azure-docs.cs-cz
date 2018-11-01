---
title: Průběžné monitorování kanálu pro vydávání verzí DevOps s Azure DevOps a Azure Application Insights | Dokumentace Microsoftu
description: Obsahuje pokyny pro rychlé nastavení nepřetržitého monitorování pomocí Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 75401614b6892402083af5192b691f00d82c8d05
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413617"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Přidání průběžného monitorování do kanálu pro vydávání verzí

Služby Azure DevOps se integruje s Azure Application Insights a povolit průběžné monitorování kanálu pro vydávání verzí DevOps v průběhu životního cyklu vývoje softwaru. 

Nepřetržité monitorování zajišťovaný kanály verzí můžete začlenit data monitorování z Application Insights a další prostředky Azure teď podporuje služby Azure DevOps. Při zjištění výstrahu Application Insights nasazení může zůstat ověřované nebo vrácena zpět, dokud výstraha vyřeší. Pokud všechny kontroly úspěšně prošel zpracováním, nasazení můžete přejít automaticky z testu úplně přenést do produkčního prostředí bez nutnosti ručního zásahu. 

## <a name="configure-continuous-monitoring"></a>Nakonfigurovat nepřetržité monitorování

1. Vyberte existující projekt Azure DevOps služby.

2. Najeďte myší na **sestavení a vydání** > vyberte **verze** > klikněte na tlačítko **znaménko plus** > **definice vydané verze vytvořit** > Vyhledejte **monitorování** > **nasazení služby Azure App Service pomocí nepřetržité monitorování.**

   ![Nový kanál pro vydávání verzí služby Azure DevOps](media/app-insights-vsts-continuous-monitoring/001.png)

3. Klikněte na tlačítko **použít.**

4. Vedle červeného vykřičníku vyberte text modrou barvu na **zobrazit úlohy prostředí.**

   ![Úlohy v zobrazení prostředí](media/app-insights-vsts-continuous-monitoring/002.png)

   Konfigurační pole se zobrazí, použijte následující tabulku k vyplnění vstupních polí.

    | Parametr        | Hodnota |
   | ------------- |:-----|
   | **Název prostředí**      | Název, který popisuje kanálu prostředí vydané verze |
   | **Předplatné Azure** | Rozevírací seznam se naplní žádná předplatná Azure, propojené s organizační služby Azure DevOps|
   | **Název služby App Service** | Ruční zadání nové hodnoty mohou být potřebné pro toto pole v závislosti na jiné výběry |
   | **Skupina prostředků**    | Naplní rozevírací seznam dostupných skupin prostředků |
   | **Název prostředku Application Insights** | Rozevírací seznam se naplní všechny prostředky Application Insights, které odpovídají dříve vybraná skupina prostředků.

5. Vyberte **výstrah nakonfigurovat Application Insights**

6. Výchozí pravidla upozornění, vyberte **Uložit** > zadejte popisný komentář > klikněte na tlačítko **OK**

## <a name="modify-alert-rules"></a>Úprava pravidla výstrah

1. Pokud chcete upravit předdefinované nastavení výstrah, klikněte na pole s **symbol tří teček...**  napravo od **pravidla upozornění.**

   (Out-of-box čtyři pravidla upozornění jsou k dispozici: dostupnost, neúspěšné požadavky, doba odezvy serveru, výjimky serveru.)

2. Kliknutím na symbol rozevíracího seznamu vedle **dostupnosti.**

3. Změnit dostupnost **prahová hodnota** pro splnění požadavků na úrovni služby.

   ![Upravit upozornění](media/app-insights-vsts-continuous-monitoring/003.png)

4. Vyberte **OK** > **Uložit** > zadejte popisný komentář > klikněte na tlačítko **OK.**

## <a name="add-deployment-conditions"></a>Přidání podmínek nasazení

1. Klikněte na tlačítko **kanálu** > vyberte **Pre** nebo **po nasazení podmínek** symbolu v závislosti na fázi, která vyžaduje bránu průběžné monitorování.

   ![Před nasazením podmínky](media/app-insights-vsts-continuous-monitoring/004.png)

2. Nastavte **brány** k **povoleno** > **schválení brány**> klikněte na tlačítko **přidat.**

3. Vyberte **Azure Monitor** (Tato možnost vám dává možnost na výstrahy přístup i z Azure monitoru a Application Insights)

    ![Azure Monitor](media/app-insights-vsts-continuous-monitoring/005.png)

4. Zadejte **časový limit brány** hodnotu.

5. Zadejte **Interval vzorkování.**

## <a name="deployment-gate-status-logs"></a>Protokoly brány stav nasazení

Po přidání brány nasazení upozornění ve službě Application Insights, překračuje dříve definovanou prahovou hodnotu, chrání vaše nasazení z nežádoucí vydání povýšení. Po vyřešení výstrahy nasazení automaticky pokračovat.

Chcete-li sledovat toto chování, vyberte **verze** > verze klikněte pravým tlačítkem na název **otevřete** > **protokoly.**

![Logs](media/app-insights-vsts-continuous-monitoring/006.png)

## <a name="next-steps"></a>Další postup

Další informace o kanálech Azure si je vyzkoušet [šablon rychlý start.](https://docs.microsoft.com/azure/devops/pipelines)
