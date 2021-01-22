---
author: baanders
description: zahrnout soubor pro proces publikování funkce Azure Functions ze sady Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 069a29f49172cf3bad6b7f7b6aca28f32f5d63b3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683847"
---
Pokud chcete projekt publikovat do aplikace Function App v Azure, klikněte pravým tlačítkem na projekt v *Průzkumník řešení* a zvolte **publikovat**.

> [!IMPORTANT] 
> Publikování do aplikace Function App v Azure má za následek další poplatky za vaše předplatné bez ohledu na digitální vlákna Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Visual Studio: publikování projektu":::

Na následující stránce *publikování* ponechte výchozí cílový výběr **Azure** a potom klikněte na tlačítko *Další*. 

V případě konkrétního cíle zvolte **Azure Function App (Windows)** a stiskněte tlačítko *Další*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Publikování funkce Azure v aplikaci Visual Studio: konkrétní cíl":::

Na stránce *funkce instance* vyberte své předplatné. To by mělo naplnit pole *skupinami prostředků* v rámci vašeho předplatného.

Vyberte skupinu prostředků vaší instance a přejděte *+* k vytvoření nové funkce Azure Functions.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Publikování funkce Azure Functions v aplikaci Visual Studio: instance Functions (před aplikací Function App)":::

V *Function App (Windows) – vytvořit nové* okno vyplňte pole následujícím způsobem:
* **Název** je název plánu spotřeby, který Azure použije k hostování vaší Azure Functions aplikace. Tím se také stane název aplikace Function App, která obsahuje vaši skutečnou funkci. Můžete zvolit vlastní jedinečnou hodnotu nebo ponechat výchozí návrh.
* Ujistěte se, že **předplatné** odpovídá předplatnému, které chcete použít. 
* Ujistěte se, že **Skupina prostředků** má skupinu prostředků, kterou chcete použít.
* Ponechat **typ plánu** jako *spotřebu*
* Vyberte **umístění** , které odpovídá umístění vaší skupiny prostředků.
* Vytvořte nový prostředek **Azure Storage** pomocí odkazu *New...* . Nastavte umístění tak, aby odpovídalo vaší skupině prostředků, použijte jiné výchozí hodnoty a stiskněte OK.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Publikování funkce Azure v aplikaci Visual Studio: Function App (Windows) – vytvořit nový":::

Potom vyberte **Vytvořit**.

To by mělo vrátit se zpátky na stránku *instance Functions* , kde je vaše nová aplikace Functions teď viditelná pod vaší skupinou prostředků. *Zakončete* volání.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Publikování funkce Azure Functions v aplikaci Visual Studio: instance Functions (po aplikaci Function App)":::

V podokně *publikovat* , které se otevře zpátky v hlavním okně sady Visual Studio, zaškrtněte políčko všechny informace vypadají správně a vyberte **publikovat**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Publikování funkce Azure v aplikaci Visual Studio: publikování":::

> [!NOTE]
> Pokud se zobrazí automaticky otevírané okno: :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="publikovat funkci Azure v aplikaci Visual Studio: přihlašovací údaje pro publikování" border="false":::
> Vyberte **pokus o načtení přihlašovacích údajů z Azure** a **uložte** ji.
>
> Pokud se zobrazí upozornění na *Upgrade verze funkcí v Azure* nebo že *vaše verze modulu runtime Functions neodpovídá verzi běžící v Azure*:
>
> Postupujte podle pokynů a upgradujte na nejnovější verzi modulu runtime Azure Functions. K tomuto problému může dojít, pokud používáte starší verzi sady Visual Studio.

Aby aplikace Function App mohla přistupovat k digitálním funkcím Azure, musí mít identitu spravovanou systémem a mít oprávnění pro přístup k instanci digitálních vláken Azure. Nastavíte tuto hodnotu jako další.
