---
title: Řešení potíží s QnAMaker | Microsoft Docs
titleSuffix: Azure
description: Řešení potíží s vaší QnAMaker runtime
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 06/04/2018
ms.author: saneppal
ms.openlocfilehash: 23847c81a39ea392b6e64575406c9dc338b852de
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "35343907"
---
# <a name="qnamaker-troubleshooting"></a>Řešení potíží s QnAMaker
QnAMaker se skládá ze součásti, které jsou hostované v Azure účet uživatele. Ladění může vyžadovat uživatelům pracovat s prostředky jejich QnAMaker Azure nebo tým podpory QnAMaker poskytnout další informace o jejich nastavení.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Jak získat nejnovější aktualizace modulu runtime QnAMaker
QnAMaker runtime je součástí nasazení služby Azure App Service, kdy jste [vytvoření služby QnAMaker](./set-up-qnamaker-service-azure.md) na portálu Azure. Pravidelně jsou provedeny aktualizace modulu runtime. Použít nejnovější aktualizace se týkají vašeho QnAMaker nastavení, musíte restartovat App Service.
1. Přejděte do služby QnAMaker (skupina prostředků) [portálu Azure](https://portal.azure.com)

    ![Skupina prostředků QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Klikněte na App Service a otevřete část – Přehled

     ![QnAMaker aplikační služby](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Restartujte službu aplikace. Ho by se měla dokončit během několika sekund. Všimněte si, že podřízené aplikace nebo robotů sestavení na tuto službu QnAMaker bude k dispozici koncovým uživatelům během této doby restartování.

    ![Restartování služby App Service QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Jak získat název hostitele služby QnAMaker
Název hostitele služby QnAMaker je užitečné pro účely ladění při kontaktování podpory QnAMaker nebo UserVoice. Název hostitele je adresa URL tohoto formátu: https://*{hostname}*. azurewebsites.net.
    
1. Přejděte do služby QnAMaker (skupina prostředků) [portálu Azure](https://portal.azure.com)

    ![Skupina prostředků QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Klikněte na App Service

     ![QnAMaker aplikační služby](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Název hostitele adresy URL je k dispozici v části Přehled

    ![Název hostitele QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Použít QnAMaker rozhraní API](./upgrade-qnamaker-service.md)
