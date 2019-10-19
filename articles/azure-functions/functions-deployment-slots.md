---
title: Azure Functions sloty nasazení
description: Naučte se vytvářet a používat nasazovací sloty pomocí Azure Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: funkce Azure Functions
ms.service: azure-functions
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 50337745b008cdd38dd860a0329e44ee712e7acd
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "70085671"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions sloty nasazení

Azure Functions sloty nasazení umožňují vaší aplikaci Function App spouštět různé instance s názvem "sloty". Sloty jsou různá prostředí, která se zveřejňují prostřednictvím veřejně dostupného koncového bodu. Jedna instance aplikace je vždycky namapovaná na produkční slot a můžete měnit instance přiřazené k slotu na vyžádání. Aplikace Function App spuštěné v plánu služby App Service mohou mít několik slotů, zatímco v části spotřeba je povolena pouze jedna patice.

Následující informace odrážejí, jak jsou funkce ovlivněné odkládacími Sloty:

- Přesměrování provozu je bezproblémové; z důvodu zahození nejsou vyřazeny žádné žádosti.
- Pokud během prohození funguje funkce, pokračuje provádění a následné triggery se směrují do vyměněné instance aplikace.

> [!NOTE]
> Pro plán spotřeby pro Linux nejsou k dispozici žádné sloty.

## <a name="why-use-slots"></a>Proč používat sloty?

Pro použití slotů nasazení existuje několik výhod. Následující scénáře popisují běžné použití pro Sloty:

- **Různá prostředí pro různé účely**: pomocí různých slotů získáte možnost odlišit instance aplikací před odchodem do produkčního nebo přípravného slotu.
- Průběžné nasazování **: nasazení**do slotu místo přímo do produkčního prostředí umožňuje, aby se aplikace před živým zprovozněním zahřívá. Kromě toho se pomocí slotů omezuje latence pro úlohy aktivované protokolem HTTP. Instance jsou před nasazením zavedeny, což snižuje studenou dobu spouštění nově nasazených funkcí.
- **Snadné nouzové**akce: po prohození s produkčním prostředím má teď pozice s dřív připravenou aplikací předchozí produkční aplikaci. Pokud se změny vyměněné do produkčního slotu neshodují, můžete okamžitě vrátit swap a získat tak zpátky "Poslední známá dobrá instance".

## <a name="swap-operations"></a>Operace prohození

Během swapu je jeden slot považován za zdroj a druhý cíl. Zdrojové sloty má instanci aplikace, která se používá pro cílový slot. Následující kroky zajišťují, že se cílovému slotu během prohození neprojeví výpadky:

1. **Použít nastavení:** Nastavení z cílového slotu se aplikují na všechny instance zdrojové patice. Například nastavení produkčního prostředí se aplikuje na pracovní instanci. K použitým nastavením patří následující kategorie:
    - Nastavení aplikace [konkrétního slotu](#manage-settings) a připojovací řetězce (Pokud je k dispozici)
    - Nastavení [průběžného nasazování](../app-service/deploy-continuous-deployment.md) (Pokud je povolené)
    - Nastavení [ověřování App Service](../app-service/overview-authentication-authorization.md) (Pokud je povolené)

1. **Počkat na restartování a dostupnost:** Swap počká na všechny instance ve zdrojové patici, aby bylo možné dokončit jeho restart a bude k dispozici pro požadavky. Pokud se nepovede restartování jakékoli instance, operace přepnutí vrátí všechny změny zdrojové patice a zastaví operaci.

1. **Postup aktualizace:** Pokud se všechny instance ve zdrojové patici zahřívá úspěšně, tyto dva sloty dokončí prohození přepnutím pravidel směrování. Po provedení tohoto kroku bude mít cílová patice (například produkční slot) aplikaci, která byla dříve zahřívání ve zdrojové patici.

1. **Opakovat operaci:** Teď, když má zdrojový slot předem prohozenou aplikaci v cílové patici, proveďte stejnou operaci pomocí všech nastavení a restartováním instancí zdrojové patice.

Mějte na paměti následující skutečnosti:

- V jakémkoli bodě operace swapu se inicializace vyměněných aplikací stane ve zdrojové pozici. Cílový slot zůstane online, zatímco se připravuje zdrojová slot, bez ohledu na to, jestli je prohození úspěšné nebo neúspěšné.

- Pokud chcete vyměnit pracovní slot s produkčním slotem, ujistěte se, že produkční slot je *vždycky* cílový slot. Tato operace přepnutí nijak neovlivní vaši produkční aplikaci.

- Nastavení související se zdroji událostí a vazbami je potřeba nakonfigurovat jako [nastavení slotu nasazení](#manage-settings) , *než zahájíte prohození*. Pokud je označíte jako "vždy", zajistíte tím, že události a výstupy budou směrovány do správné instance.

## <a name="manage-settings"></a>Správa nastavení

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Vytvoření nastavení nasazení

Nastavení můžete označit jako nastavení nasazení, což způsobí, že bude "rychlé". Nastavení s rychlým použitím se v instanci aplikace nemění.

Pokud vytvoříte nastavení nasazení v jednom slotu, ujistěte se, že jste vytvořili stejné nastavení s jedinečnou hodnotou v jakékoli jiné pozici, která je zapojená do swapu. V případě, že se hodnota nastavení nezmění, názvy nastavení zůstávají konzistentní mezi sloty. Konzistence názvů zajišťuje, že se váš kód nepokouší získat přístup k nastavení, které je definováno v jednom slotu, ale ne jiné.

Chcete-li vytvořit nastavení nasazení, použijte následující postup:

- Přechod na *sloty* v aplikaci Function App
- Klikněte na název slotu.
- V části *funkce platformy > Obecné nastavení*klikněte na **Konfigurace** .
- Klikněte na název nastavení, který chcete s aktuálním slotem vylepit.
- Zaškrtněte políčko **nastavení slotu nasazení** .
- Klikněte na tlačítko **OK**.
- Po nezobrazení okna nastavení klikněte na **Uložit** , aby se změny zachovaly.

![Nastavení slotu nasazení](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Nasazení

Při vytváření slotu jsou sloty prázdné. K nasazení aplikace do slotu můžete použít kteroukoli z [podporovaných technologií nasazení](./functions-deployment-technologies.md) .

## <a name="scaling"></a>Škálování

Všechny sloty se škálují na stejný počet pracovních procesů jako produkční slot.

- V případě plánů spotřeby se slot škáluje podle škály aplikace Function App.
- U App Service plánů se aplikace škáluje na pevný počet pracovních procesů. Sloty běží na stejném počtu pracovních procesů jako plán aplikace.

## <a name="add-a-slot"></a>Přidat slot

Slot můžete přidat prostřednictvím rozhraní příkazového [řádku](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) nebo prostřednictvím portálu. Následující kroky ukazují, jak vytvořit novou patici na portálu:

1. Přejděte do aplikace Function App a klikněte na **symbol plus** vedle položku *sloty*.

    ![Přidat slot nasazení Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Do textového pole zadejte název a klikněte na tlačítko **vytvořit** .

    ![Název Azure Functions slot pro nasazení](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Prohození slotů

Sloty můžete prohodit přes rozhraní příkazového [řádku](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) nebo prostřednictvím portálu. Následující kroky ukazují, jak odkládací sloty na portálu:

1. Přechod do aplikace Function App
1. Klikněte na název zdrojové patice, kterou chcete prohodit.
1. Na kartě *Přehled* klikněte na tlačítko **swap** ![Swap Azure Functions slot nasazení ](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Ověřte nastavení konfigurace zahození a klikněte na **swap** ![Swap Azure Functions slot nasazení ](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

Operace přepnutí může chvíli trvat.

## <a name="roll-back-a-swap"></a>Vrácení swapu zpět

Pokud dojde k chybě zahození nebo pokud chcete jednoduše vrátit zpět změny, můžete se vrátit k počátečnímu stavu. Chcete-li se vrátit do předem vyměněného stavu, proveďte další prohození pro obrácení swapu.

## <a name="remove-a-slot"></a>Odebrat slot

Slot můžete odebrat přes rozhraní příkazového [řádku](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) nebo prostřednictvím portálu. Následující kroky ukazují, jak odebrat slot na portálu:

1. Přejít na Přehled aplikace Function App

1. Klikněte na tlačítko **Odstranit** .

    ![Přidat slot nasazení Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>Automatizace správy slotů

Pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)můžete pro slot automatizovat následující akce:

- [vytvoření](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [odstranění](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [adresu](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [Automatické prohození](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Změnit plán služby App Service

Pomocí aplikace Function App, která běží v plánu App Service, máte možnost změnit základní plán služby App Service pro slot.

> [!NOTE]
> V plánu spotřeby nemůžete změnit plán App Service přihrádky.

Pomocí následujícího postupu změníte plán služby App Service pro slot:

1. Přejít na slot

1. V části *funkce platformy*klikněte na **všechna nastavení** .

    ![Změnit plán služby App Service](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Klikněte na **plán App Service**

1. Vyberte plán nové App Service nebo vytvořte nový plán.

1. Klikněte na tlačítko **OK**.

    ![Změnit plán služby App Service](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Omezení

Azure Functions sloty nasazení mají následující omezení:

- Počet slotů dostupných pro aplikaci závisí na plánu. Plán spotřeby má povolený jenom jeden slot nasazení. Pro aplikace spuštěné v plánu App Service jsou k dispozici další sloty.
- Výměna slotu resetuje klíče pro aplikace, které mají nastavení aplikace `AzureWebJobsSecretStorageType` rovno `files`.
- Pro plán spotřeby pro Linux nejsou k dispozici žádné sloty.

## <a name="support-levels"></a>Úrovně podpory

Existují dvě úrovně podpory pro sloty nasazení:

- **Všeobecná dostupnost (GA)** : plně podporovaná a schválená pro použití v produkčním prostředí.
- **Verze Preview**: zatím není podporovaná, ale očekává se, že bude v budoucnu dostupný stav GA.

| Operační systém/plán hostování           | Úroveň podpory     |
| ------------------------- | -------------------- |
| Spotřeba Windows       | Všeobecná dostupnost |
| Windows Premium (Preview) | Preview              |
| Vyhrazená pro Windows         | Všeobecná dostupnost |
| Spotřeba Linux         | Nepodporované          |
| Linux Premium (Preview)   | Preview              |
| Vyhrazený pro Linux           | Všeobecná dostupnost |

## <a name="next-steps"></a>Další kroky

- [Technologie nasazení v Azure Functions](./functions-deployment-technologies.md)
