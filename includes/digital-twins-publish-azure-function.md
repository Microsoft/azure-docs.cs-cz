---
author: baanders
description: zahrnout soubor pro proces publikování funkce Azure Functions ze sady Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: ddc56ab05a087c9e86d67a13aebcfb8e65fbd78f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480721"
---
Pokud chcete projekt publikovat do aplikace Function App v Azure, začněte v Průzkumník řešení. Klikněte pravým tlačítkem na projekt a pak zvolte **publikovat**.

> [!IMPORTANT] 
> Publikování do aplikace Function App v Azure má za následek další poplatky za vaše předplatné bez ohledu na digitální vlákna Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Snímek obrazovky sady Visual Studio se zobrazeným kliknutím pravým tlačítkem myši na nabídku řešení. V nabídce je publikace zvýrazněna.":::

Na stránce **publikovat** , která se otevře, ponechte výchozí cílový výběr **Azure**. Pak vyberte **Další**. 

Pro konkrétní cíl zvolte **Azure Function App (Windows)** a pak vyberte **Další**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Snímek obrazovky sady Visual Studio se zobrazením dialogového okna publikovat Azure Functions. Na stránce konkrétní cíl je výběr Azure Function App (Windows).":::

Na kartě **instance funkcí** vyberte své předplatné. Pak vyberte ikonu Plus (+) a vytvořte novou funkci.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Snímek obrazovky sady Visual Studio se zobrazením dialogového okna publikovat Azure Functions. Ikona Plus je zvýrazněna.":::

V okně **Function App (Windows) – vytvořit nové** zadejte následující pole:
* **Název** je název plánu spotřeby, který Azure použije k hostování vaší Azure Functions aplikace. Tento název se použije taky pro aplikaci Function App, která obsahuje vaši skutečnou funkci. Můžete zvolit jedinečnou hodnotu nebo ponechat výchozí návrh.
* Ujistěte se, že **předplatné** odpovídá předplatnému, které chcete použít. 
* Ujistěte se, že **Skupina prostředků** je ta, kterou chcete použít.
* Ponechte výběr **typu plánu** jako **spotřebu**.
* Vyberte **umístění** skupiny prostředků.
* Vytvořte nový prostředek **Azure Storage** tak, že vyberete **Nový** odkaz. Nastavte umístění tak, aby odpovídalo vaší skupině prostředků, použijte jiné výchozí hodnoty a pak vyberte **OK**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Snímek obrazovky sady Visual Studio se zobrazením dialogového okna publikovat Azure Functions. Jsou vyplněny podrobnosti nové aplikace Function App, včetně názvu, předplatného, skupiny prostředků, typu plánu, umístění a Azure Storage.":::

Potom vyberte **Vytvořit**.

Po vytvoření služby App Service se otevře karta **instance Functions** . Vaše nová aplikace Function App se zobrazí v oblasti **aplikace Functions** pod vaší skupinou prostředků. Vyberte **Dokončit**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Snímek obrazovky sady Visual Studio se zobrazením dialogového okna publikovat Azure Functions. Je vybraná karta instance Functions. Nová aplikace Functions se zobrazí ve skupině prostředků.":::

V podokně **publikovat** , které se otevře v hlavním okně sady Visual Studio, ověřte, zda všechny informace vypadají správně. Potom vyberte **Publikovat**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Snímek obrazovky sady Visual Studio zobrazující podokno publikování. Tlačítko publikovat je zvýrazněné.":::

> [!NOTE]
> Pokud se zobrazí automaticky otevírané okno jako v následujícím příkladu, vyberte **pokus o načtení přihlašovacích údajů z Azure** a pak vyberte **Uložit**.
> :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Snímek obrazovky sady Visual Studio se zobrazeným překryvném oknem s názvem přihlašovací údaje pro publikování. Obsahuje pole pro uživatelské jméno a heslo. Obsahuje taky tlačítko pro pokus o načtení přihlašovacích údajů z Azure." border="false":::
>
> Pokud se zobrazí některá z následujících upozornění, postupujte podle pokynů pro upgrade na nejnovější verzi modulu runtime Azure Functions:
> * "Verze funkcí upgradu v Azure."
> * "Vaše verze modulu runtime Functions neodpovídá verzi běžící v Azure."
>
> Tato upozornění se mohou zobrazit, pokud používáte starou verzi sady Visual Studio.

Vaše aplikace Function App je teď publikovaná v Azure.
