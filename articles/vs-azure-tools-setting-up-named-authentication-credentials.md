---
title: Nastavte pověření s názvem ověřování | Microsoft Docs
description: Zjistěte, jak k zadání přihlašovacích údajů, které Visual Studio můžete použít k ověřování žádostí Azure, takže můžete publikovat aplikaci do Azure ze sady Visual Studio nebo monitorování stávající cloudovou službu.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 61570907-42a1-40e8-bcd6-952b21a55786
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 3dce80f5c6611cb2a22293d1a574980db5ea8378
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31791575"
---
# <a name="set-up-named-authentication-credentials"></a>Nastavení s názvem pověření pro ověření

K publikování aplikace do Azure nebo k monitorování stávající cloudovou službu, sada Visual Studio vyžaduje přihlašovací údaje k ověřování žádostí Azure, a to svoje ID předplatného Azure a certifikát X.509 v3 s klíčem alespoň 2 048 bitů. Zadáte tyto přihlašovací údaje prostřednictvím některý z následujících metod:

- V sadě Visual Studio vyberte **zobrazení > Průzkumníka serveru**, klikněte pravým tlačítkem myši **Azure** uzlu, vyberte **připojit k předplatnému Microsoft Azure**a přihlaste se.
- Vytvořte soubor předplatné (`.publishsettings`), který obsahuje veřejný klíč pro certifikát. Soubor odběru může obsahovat přihlašovací údaje pro více než jedno předplatné, jak je popsáno v tomto článku.

Poznámka: tyto přihlašovací údaje se liší od přihlašovací údaje používané k ověřování žádostí služby Azure storage.

## <a name="create-a-subscription-file"></a>Vytvořte soubor odběru

V Průzkumníku serveru, klikněte pravým tlačítkem myši **Azure** uzel a vyberte možnost **spravovat a odběry filtru**. Vyberte **certifikáty** kartě a pak udělejte jednu z následujících akcí:

- Vyberte **Import** otevřete **předplatná Microsoft Azure Import** dialogové okno. Vyberte **soubor odběru stažení** propojit a v prohlížeči Uložte stažený soubor do dočasné složky. V dialogovém okně přejděte do umístění stahování a potom importovat pro použití při ověřování.
- Zvolte aktivní předplatné a vyberte **upravit**, otevře se dialogové okno, ve kterém můžete upravit stávající předplatné pro použití při ověřování.
- Vyberte **nový** otevřete **nové předplatné** dialogové okno pole a zadejte požadované podrobnosti. Na kterou odešlete certifikát do své cloudové služby jsou uvedené v dialogovém okně, přihlaste se k portálu Azure, přejděte do cloudové služby, vyberte **Nastavení > certifikáty pro správu**, vyberte **nahrát**, pak Zadejte cestu k `.cer` souboru.

Pokud chcete vytvořit certifikát, najdete pokyny v [vytvoření a nahrání certifikátu správy pro Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) a ručně odeslat certifikát, který chcete [portál Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Další postup

- [Obecný přehled Web Apps](https://docs.microsoft.com/azure/app-service/)
- [Nasazení aplikace do služby Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-deploy-local-git) 
- [Nasazení WebJobs pomocí sady Visual Studio](https://docs.microsoft.com/azure/app-service/websites-dotnet-deploy-webjobs)
- [Vytvoření a nasazení cloudové služby](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)