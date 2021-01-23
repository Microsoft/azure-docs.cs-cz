---
author: baanders
description: zahrnout soubor pro proces publikování funkce Azure Functions ze sady Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 63b393f519ad29baa05fef046ee1e8ba9e5330d8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701151"
---
Pokud chcete projekt publikovat do aplikace Function App v Azure, klikněte pravým tlačítkem na projekt v *Průzkumník řešení* a zvolte **publikovat**.

> [!IMPORTANT] 
> Publikování do aplikace Function App v Azure má za následek další poplatky za vaše předplatné bez ohledu na digitální vlákna Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Snímek obrazovky sady Visual Studio se zobrazením nabídky řešení vpravo vyberte. Publikování je zvýrazněno v nabídce.":::

Na následující stránce *publikování* ponechte výchozí cílový výběr **Azure** a potom klikněte na tlačítko *Další*. 

V případě konkrétního cíle zvolte **Azure Function App (Windows)** a stiskněte tlačítko *Další*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Snímek obrazovky sady Visual Studio v dialogovém okně Publikovat Azure Functions. Na stránce konkrétní cíl je vybraná možnost Azure Function App (Windows).":::

Na stránce *funkce instance* vyberte své předplatné. To by mělo naplnit pole *skupinami prostředků* v rámci vašeho předplatného.

Vyberte skupinu prostředků vaší instance a přejděte *+* k vytvoření nové funkce Azure Functions.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Snímek obrazovky sady Visual Studio v dialogovém okně Publikovat Azure Functions. Tlačítko + pro vytvoření nové funkce je na stránce instance Functions zvýrazněno.":::

V *Function App (Windows) – vytvořit nové* okno vyplňte pole následujícím způsobem:
* **Název** je název plánu spotřeby, který Azure použije k hostování vaší Azure Functions aplikace. Tím se také stane název aplikace Function App, která obsahuje vaši skutečnou funkci. Můžete zvolit vlastní jedinečnou hodnotu nebo ponechat výchozí návrh.
* Ujistěte se, že **předplatné** odpovídá předplatnému, které chcete použít. 
* Ujistěte se, že **Skupina prostředků** má skupinu prostředků, kterou chcete použít.
* Ponechat **typ plánu** jako *spotřebu*
* Vyberte **umístění** , které odpovídá umístění vaší skupiny prostředků.
* Vytvořte nový prostředek **Azure Storage** pomocí odkazu *New...* . Nastavte umístění tak, aby odpovídalo vaší skupině prostředků, použijte jiné výchozí hodnoty a stiskněte OK.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Snímek obrazovky sady Visual Studio v dialogovém okně Publikovat Azure Functions. Jsou vyplněny podrobnosti nové aplikace Function App, včetně názvu, předplatného, skupiny prostředků, typu plánu, umístění a Azure Storage.":::

Potom vyberte **Vytvořit**.

To by mělo vrátit se zpátky na stránku *instance Functions* , kde je vaše nová aplikace Functions teď viditelná pod vaší skupinou prostředků. *Zakončete* volání.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Publikování funkce Azure Functions v aplikaci Visual Studio: instance Functions (po aplikaci Function App)":::

V podokně *publikovat* , které se otevře zpátky v hlavním okně sady Visual Studio, zaškrtněte políčko všechny informace vypadají správně a vyberte **publikovat**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Snímek obrazovky sady Visual Studio v dialogovém okně Publikovat Azure Functions. Nová aplikace Function App se zobrazí v seznamu aplikací funkcí a existuje tlačítko pro dokončení.":::

> [!NOTE]
> Pokud se zobrazí automaticky otevírané okno s :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="názvem obrazovky pro místní okno sady Visual Studio nazvané přihlašovací údaje pro publikování. Obsahuje pole pro uživatelské jméno a heslo a tlačítko k pokusu o načtení přihlašovacích údajů z Azure." border="false":::
> Vyberte **pokus o načtení přihlašovacích údajů z Azure** a **uložte** ji.
>
> Pokud se zobrazí upozornění na *Upgrade verze funkcí v Azure* nebo že *vaše verze modulu runtime Functions neodpovídá verzi běžící v Azure*:
>
> Postupujte podle pokynů a upgradujte na nejnovější verzi modulu runtime Azure Functions. K tomuto problému může dojít, pokud používáte starší verzi sady Visual Studio.

Aby aplikace Function App mohla přistupovat k digitálním funkcím Azure, musí mít identitu spravovanou systémem a mít oprávnění pro přístup k instanci digitálních vláken Azure. Nastavíte tuto hodnotu jako další.
