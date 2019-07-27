---
title: Řešení potíží – QnA maker
titleSuffix: Azure Cognitive Services
description: QnA maker se skládá z komponent, které jsou hostované v Azure účet uživatele. Ladění může vyžadovat, aby uživatelé pracovat s svoje prostředky Azure QnA maker nebo tým podpory QnA maker poskytnout další informace o jejich nastavení.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 94a3dfd9a3560321d7e2753ccd385fb1a5323107
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559917"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Tipy pro řešení potíží pro podporu služba QnA Maker a modulu runtime
QnA maker se skládá z komponent, které jsou hostované v Azure účet uživatele. Ladění může vyžadovat, aby uživatelé pracovat s svoje prostředky Azure QnA maker nebo tým podpory QnA maker poskytnout další informace o jejich nastavení.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Jak získat nejnovější aktualizace modulu runtime QnA maker
QnA maker runtime je součástí nasazení služby Azure App Service, kdy jste [vytvořit službu QnA maker](./set-up-qnamaker-service-azure.md) na webu Azure portal. Aktualizace probíhají pravidelně modulu runtime. QnA Maker App Service je v režimu automatických aktualizací, který zveřejní 2019 naši verzi kumulativního rozšíření webu (verze 5 +). Tato hodnota je již navržena tak, aby se během upgradů mohla zajímat žádná výpadky. Aktuální verzi můžete zjistit na adrese https://www.qnamaker.ai/UserSettings. Pokud je vaše verze starší než verze 5. x, je nutné restartovat App Service a použít tak nejnovější aktualizace, které se použijí pro instalaci Qnamakerem.

1. Přejděte do služby QnA maker (skupinu prostředků) [webu Azure portal](https://portal.azure.com)

    ![Skupina prostředků Azure QnA maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Klikněte na tlačítko ve službě App Service a otevřete část – Přehled

     ![QnA maker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Restartujte služby App service. By se měla dokončit během několika sekund. Všimněte si, že podřízené aplikací/robotů stavět na tuto službu QnA maker bude k dispozici koncovým uživatelům během tohoto období restartování.

    ![Restartování služby App Service QnA maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Jak získat název hostitele služby QnA maker
Název hostitele služby QnA maker je užitečné pro účely ladění, když požádáte podporu QnA maker nebo UserVoice. Název hostitele je adresa URL tento formát: https:// *{název hostitele}* . azurewebsites.net.
    
1. Přejděte do služby QnA maker (skupinu prostředků) [webu Azure portal](https://portal.azure.com)

    ![Skupina prostředků Azure QnA maker na webu Azure portal](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Klikněte na tlačítko ve službě App Service

     ![Vyberte službu QnA maker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Název hostitele adresy URL je k dispozici v části Přehled

    ![Název hostitele QnA maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vylepšení otázek znalostní báze s aktivním učením](./improve-knowledge-base.md)
