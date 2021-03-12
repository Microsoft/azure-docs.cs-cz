---
author: baanders
description: zahrnout soubor pro proces publikování funkce Azure Functions ze sady Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 9f8ca9ab6dc184abb8d9dcd25ebe87cbd7761722
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630254"
---
Pokud chcete projekt publikovat do aplikace Function App v Azure, klikněte pravým tlačítkem na projekt v *Průzkumník řešení* a zvolte **publikovat**.

> [!IMPORTANT] 
> Publikování do aplikace Function App v Azure má za následek další poplatky za vaše předplatné bez ohledu na digitální vlákna Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Snímek obrazovky sady Visual Studio se zobrazením nabídky řešení vpravo vyberte. Publikování je zvýrazněno v nabídce.":::

Na následující stránce *publikování* ponechte výchozí cílový výběr **Azure** a vyberte *Další*. 

V případě konkrétního cíle zvolte **Azure Function App (Windows)** a klikněte na *Další*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Snímek obrazovky sady Visual Studio v dialogovém okně Publikovat Azure Functions. Na stránce konkrétní cíl je vybraná možnost Azure Function App (Windows).":::

Na stránce *funkce instance* vyberte své předplatné. Pak vyberte *+* ikonu pro vytvoření nové funkce Azure Functions.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Snímek obrazovky sady Visual Studio v dialogovém okně Publikovat Azure Functions. Tlačítko + pro vytvoření nové funkce je na stránce instance Functions zvýrazněno.":::

V *Function App (Windows) – vytvořit nové* okno vyplňte pole následujícím způsobem:
* **Název** je název plánu spotřeby, který Azure použije k hostování vaší Azure Functions aplikace. Tím se také stane název aplikace Function App, která obsahuje vaši skutečnou funkci. Můžete zvolit vlastní jedinečnou hodnotu nebo ponechat výchozí návrh.
* Ujistěte se, že **předplatné** odpovídá předplatnému, které chcete použít. 
* Ujistěte se, že **Skupina prostředků** má skupinu prostředků, kterou chcete použít.
* Ponechat **typ plánu** jako *spotřebu*
* Vyberte **umístění** , které odpovídá umístění vaší skupiny prostředků.
* Vytvořte nový prostředek **Azure Storage** pomocí odkazu *New...* . Nastavte umístění tak, aby odpovídalo vaší skupině prostředků, použijte jiné výchozí hodnoty a vyberte OK.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Snímek obrazovky sady Visual Studio v dialogovém okně Publikovat Azure Functions. Jsou vyplněny podrobnosti nové aplikace Function App, včetně názvu, předplatného, skupiny prostředků, typu plánu, umístění a Azure Storage.":::

Potom vyberte **Vytvořit**.

Po krátkém čekání na vytvoření služby App Service by se mělo dialogové okno vrátit na stránku *instance* Functions. vaše nová aplikace Function App se zobrazí v oblasti **aplikace Functions** vnořené pod vaší skupinou prostředků. Vyberte *Dokončit*.

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

Vaše aplikace Function App je teď publikovaná v Azure.