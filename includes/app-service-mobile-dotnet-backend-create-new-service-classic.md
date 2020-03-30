---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175689"
---
1. Přihlaste se k webu [Azure Portal].
2. Vyberte **možnost +NEW** > **Web + Mobilní** > **mobilní aplikace**a zadejte název back-endu mobilních aplikací.
3. V **části Skupina prostředků**vyberte existující skupinu prostředků nebo vytvořte novou (pomocí stejného názvu jako aplikace). 
4. Pro **plán služby App Service**je vybrán výchozí plán (ve [vrstvě Standard).](https://azure.microsoft.com/pricing/details/app-service/) Můžete také vybrat jiný plán nebo [vytvořit nový](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Nastavení plánu služby App Service určuje [umístění, funkce, náklady a výpočetní prostředky](https://azure.microsoft.com/pricing/details/app-service/) spojené s vaší aplikací. Další informace o plánech služby App Service a o tom, jak vytvořit nový plán v jiné cenové úrovni a v požadovaném umístění, najdete [v článku Podrobné informace o plánech služby Azure App Service](../articles/app-service/overview-hosting-plans.md).
   
5. Vyberte **Vytvořit**. Tento krok vytvoří back-end mobilních aplikací. 
6. V podokně **Nastavení** pro nový back-end mobilních aplikací vyberte **možnost Rychlý start** > platformě klientských aplikací > Připojit **databázi**. 
   
   ![Výběrpro připojení databáze](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. V podokně **Přidat datové připojení** vyberte možnost Databáze >  **SQL**Vytvořit**novou databázi**. Zadejte název databáze, zvolte cenovou úroveň a pak vyberte **Server**. Novou databázi můžete použít opakovaně. Pokud už ve stejném umístění databázi máte, můžete místo toho vybrat možnost **Použít existující databázi**. Nedoporučujeme používat databázi v jiném umístění, vzhledem k nákladům na šířku pásma a vyšší latenci.
   
   ![Výběr databáze](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. V podokně **Nový server** zadejte do pole **Název serveru** jedinečný název serveru, zadejte přihlašovací jméno a heslo, vyberte **Povolit službám Azure přístup k serveru**a vyberte **OK**. Tento krok vytvoří novou databázi.
9. Zpět v podokně **Přidat datové připojení** vyberte **připojovací řetězec**, zadejte hodnoty přihlašovacích údajů a hesla pro databázi a vyberte **OK**. 

   Než budete pokračovat, počkejte několik minut, než bude databáze úspěšně nasazena.

<!-- URLs. -->
[Portál Azure]: https://portal.azure.com/
