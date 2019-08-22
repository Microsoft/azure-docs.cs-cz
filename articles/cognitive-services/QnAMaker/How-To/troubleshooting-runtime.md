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
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b58a08c4cfa97356be4064456b2e9ec5cceccfbc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876344"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Tipy pro řešení potíží pro podporu služba QnA Maker a modulu runtime

Qnamakerem zahrnuje prostředky hostované v předplatném Azure uživatele. Ladění může vyžadovat od uživatelů manipulaci s prostředky Azure Qnamakerem nebo poskytnout týmu podpory Qnamakerem Další informace o jejich nastavení.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Jak získat nejnovější aktualizace modulu runtime QnA maker

Modul runtime Qnamakerem je součástí Azure App Service nasazeného při [vytváření služby qnamakerem](./set-up-qnamaker-service-azure.md) v Azure Portal. Aktualizace probíhají pravidelně modulu runtime. QnA Maker App Service se v režimu automatické aktualizace 2019 po vydání kumulativního rozšíření webu (verze 5 +). Tato hodnota je již navržena tak, aby se během upgradů mohla zajímat žádná výpadky. 

Aktuální verzi můžete zjistit na adrese https://www.qnamaker.ai/UserSettings. Pokud je vaše verze starší než verze 5. x, je nutné restartovat App Service, aby se projevily nejnovější aktualizace.

1. Přejděte do služby QnA maker (skupinu prostředků) [webu Azure portal](https://portal.azure.com)

    ![Skupina prostředků Azure QnA maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Klikněte na tlačítko ve službě App Service a otevřete část – Přehled

     ![QnA maker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Restartujte služby App service. By se měla dokončit během několika sekund. Všimněte si, že všechny závislé aplikace nebo roboty, které používají tuto službu Qnamakerem, nebudou během této doby restartování k dispozici koncovým uživatelům.

    ![Restartování služby App Service QnA maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Jak získat název hostitele služby QnA maker
Název hostitele služby QnA maker je užitečné pro účely ladění, když požádáte podporu QnA maker nebo UserVoice. Název hostitele je adresa URL v tomto formátu: https:// *{hostname}* . azurewebsites.NET.
    
1. Přejděte do služby QnA maker (skupinu prostředků) [webu Azure portal](https://portal.azure.com)

    ![Skupina prostředků Azure QnA maker na webu Azure portal](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Vyberte App Service přidružené k prostředku QnA Maker. Názvy jsou obvykle stejné.

     ![Vyberte službu QnA maker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Název hostitele adresy URL je k dispozici v části Přehled

    ![Název hostitele QnA maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vylepšení otázek znalostní báze s aktivním učením](./improve-knowledge-base.md)
