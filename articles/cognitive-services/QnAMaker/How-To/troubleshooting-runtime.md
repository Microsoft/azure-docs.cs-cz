---
title: Řešení potíží – QnA maker
titlesuffix: Azure Cognitive Services
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
ms.openlocfilehash: 5b65a121e895b4855c7c69d2b67e7055c88ddd08
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466046"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Tipy pro řešení potíží pro podporu služba QnA Maker a modulu runtime
QnA maker se skládá z komponent, které jsou hostované v Azure účet uživatele. Ladění může vyžadovat, aby uživatelé pracovat s svoje prostředky Azure QnA maker nebo tým podpory QnA maker poskytnout další informace o jejich nastavení.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Jak získat nejnovější aktualizace modulu runtime QnA maker
QnA maker runtime je součástí nasazení služby Azure App Service, kdy jste [vytvořit službu QnA maker](./set-up-qnamaker-service-azure.md) na webu Azure portal. Aktualizace probíhají pravidelně modulu runtime. QnA Maker App Service je na automatické aktualizace režimu příspěvek naše dubna 2019 verze rozšíření lokality (verze 5 +). Již je navržená k zajištění NULOVÝMI výpadky během upgradu. V aktuální verzi, můžete zjistit https://www.qnamaker.ai/UserSettings. Pokud je vaše verze starší než verze 5.x, musíte restartovat App Service a použijte nejnovější aktualizace chcete použít pro nastavení QnA maker.

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
> [Zlepšení znalostní báze otázek pomocí aktivní učení](./improve-knowledge-base.md)
