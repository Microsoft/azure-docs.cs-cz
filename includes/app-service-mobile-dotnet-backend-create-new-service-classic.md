---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729403"
---
1. Přihlaste se k webu [Azure Portal].
2. Vyberte **+ nová** > **Web + mobilní zařízení** > **mobilní aplikace**a potom zadejte název pro back-endu Mobile Apps.
3. Pro **skupiny prostředků**, vyberte existující skupinu prostředků nebo vytvořte novou (za použití stejného názvu jako aplikace). 
4. Pro **plán služby App Service**, výchozí plán (v [úrovně Standard](https://azure.microsoft.com/pricing/details/app-service/)) je vybraná. Můžete také vybrat jiný plán nebo [vytvořit nový](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Určit nastavení plánu služby App Service [umístění, funkce, náklady a výpočetní prostředky](https://azure.microsoft.com/pricing/details/app-service/) spojené s vaší aplikací. Další informace o službě App Service plány a jak vytvořit nový plán jinou cenovou úroveň a v požadovaném umístění najdete v článku [podrobný přehled plánů služby Azure App Service](../articles/app-service/overview-hosting-plans.md).
   
5. Vyberte **Vytvořit**. Tento krok vytvoří back-endu Mobile Apps. 
6. V **nastavení** podokno pro novou službu Mobile Apps back-endu, vyberte **úvodní** > aplikační platforma vašeho klienta > **připojení databáze**. 
   
   ![Výběry pro připojení databáze](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. V **přidat datové připojení** vyberte **SQL Database** > **vytvořit novou databázi**. Zadejte název databáze, zvolte cenovou úroveň a potom vyberte **Server**. Novou databázi můžete použít opakovaně. Pokud už ve stejném umístění databázi máte, můžete místo toho vybrat možnost **Použít existující databázi**. Nedoporučujeme použití databáze v jiném umístění, protože náklady na šířku pásma a vyšší latenci.
   
   ![Výběr databáze](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. V **nový server** podokně zadejte jedinečný název serveru v **název serveru** zadejte přihlašovací jméno a heslo, vyberte **služby Azure umožňují přístup k serveru**a vyberte  **OK**. Tento krok vytvoří novou databázi.
9. Zpátky **přidat datové připojení** vyberte **připojovací řetězec**, zadejte hodnoty přihlašovací jméno a heslo pro vaši databázi a vyberte **OK**. 

   Počkejte pár minut a databáze se úspěšně nasazena, než budete pokračovat.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
