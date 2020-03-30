---
title: Sloty pro nasazení funkcí Azure
description: Naučte se vytvářet a používat sloty pro nasazení pomocí funkcí Azure
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769213"
---
# <a name="azure-functions-deployment-slots"></a>Sloty pro nasazení funkcí Azure

Sloty pro nasazení Funkcí Azure umožňují vaší aplikaci funkcí spouštět různé instance nazývané "sloty". Sloty jsou různá prostředí vystavená prostřednictvím veřejně dostupného koncového bodu. Jedna instance aplikace je vždy mapována na produkční slot a můžete zaměnit instance přiřazené k slotu na vyžádání. Aplikace funkcí spuštěné v rámci plánu Služby Aplikace mohou mít více slotů, zatímco v rámci plánu Spotřeba je povolen pouze jeden slot.

Následující skutečnosti odrážejí, jak jsou funkce ovlivněny prohození slotů:

- Přesměrování provozu je bezproblémové; žádné požadavky jsou vynechány z důvodu prohození.
- Pokud je během odkládání spuštěna funkce, pokračuje spuštění a následné aktivační události jsou směrovány do instance odkládané aplikace.

> [!NOTE]
> Sloty nejsou v současné době k dispozici pro plán Linux Consumption.

## <a name="why-use-slots"></a>Proč používat sloty?

Použití slotů pro nasazení má řadu výhod. Následující scénáře popisují běžné použití slotů:

- **Různá prostředí pro různé účely**: Použití různých slotů vám dává možnost odlišit instance aplikací před přepnutím do produkčního prostředí nebo pracovní hoslotu.
- **Předběžné oteplování**: Nasazení do slotu namísto přímého spuštění umožňuje aplikaci zahřát před spuštěním. Použití slotů navíc snižuje latenci pro úlohy spouštěné protokolem HTTP. Instance se před nasazením zahřejí, což snižuje studený start pro nově nasazené funkce.
- **Snadné záložní :** Po výměně s produkčním prostředím má nyní slot s dříve inscenovanou aplikací předchozí produkční aplikaci. Pokud změny převedeny do produkčního slotu nejsou podle očekávání, můžete okamžitě vrátit swap získat "poslední známé dobré instance" zpět.

## <a name="swap-operations"></a>Swapové operace

Během prohození je jeden slot považován za zdroj a druhý cíl. Zdrojový slot má instanci aplikace, která se použije na cílový slot. Následující kroky zajistí, že cílový slot během prohození nezaznamená prostoje:

1. **Použít nastavení:** Nastavení z cílové ho slotu se použijí na všechny instance zdrojové patice. Například nastavení výroby se použijí na pracovní instanci. Použitá nastavení zahrnují následující kategorie:
    - Nastavení aplikace [specifické pro slot](#manage-settings) a připojovací řetězce (pokud je k dispozici)
    - [Nastavení průběžného nasazení](../app-service/deploy-continuous-deployment.md) (pokud je povoleno)
    - Nastavení [ověřování služby App Service](../app-service/overview-authentication-authorization.md) (pokud je povoleno)

1. **Počkejte na restartování a dostupnost:** Prohození čeká na každou instanci ve zdrojové matné pozici k dokončení jeho restartování a být k dispozici pro požadavky. Pokud se některá instance nepodaří restartovat, odkládací operace vrátí všechny změny do zdrojové patice a zastaví operaci.

1. **Aktualizovat směrování:** Pokud jsou všechny instance ve zdrojové patici úspěšně zahřáté, dva sloty dokončí výměnu přepnutím pravidel směrování. Po tomto kroku cílový slot (například produkční slot) má aplikaci, která se dříve zahřála ve zdrojovém slotu.

1. **Opakujte operaci:** Teď, když zdrojový slot má předswapovou aplikaci dříve v cílovém slotu, proveďte stejnou operaci použitím všech nastavení a restartováním instancí pro zdrojový slot.

Mějte na paměti následující skutečnosti:

- V libovolném okamžiku operace prohození dojde k inicializaci zaměněných aplikací ve zdrojovém slotu. Cílový slot zůstane online, zatímco zdrojový slot je připravován, zda je výměna úspěšná nebo neúspěšná.

- Chcete-li vyměnit pracovní slot s produkčním slotem, ujistěte se, že produkční slot je *vždy* cílový slot. Tímto způsobem operace prohození nemá vliv na produkční aplikace.

- Nastavení související se zdroji událostí a vazbami je třeba *před zahájením prohození*nakonfigurovat jako [nastavení slotu pro nasazení](#manage-settings) . Označení je jako "lepkavé" dopředu zajišťuje události a výstupy jsou směrovány na správnou instanci.

## <a name="manage-settings"></a>Správa nastavení

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Vytvoření nastavení nasazení

Nastavení můžete označit jako nastavení nasazení, díky kterému je "lepkavé". Rychlé nastavení se nezamění s instancí aplikace.

Pokud vytvoříte nastavení nasazení v jedné patice, ujistěte se, že vytvořit stejné nastavení s jedinečnou hodnotu v jakékoli jiné patice zapojené do swapu. Tímto způsobem, zatímco hodnota nastavení se nezmění, názvy nastavení zůstávají konzistentní mezi sloty. Tato konzistence názvu zajišťuje, že váš kód se nepokouší o přístup k nastavení, které je definováno v jednom slotu, ale ne v jiném.

K vytvoření nastavení nasazení použijte následující kroky:

- Přechod na *sloty* v aplikaci funkce
- Klikněte na název slotu
- V části *Funkce platformy > obecné nastavení*klikněte na **Konfigurace**
- Klikněte na název nastavení, který chcete držet s aktuálním slotem
- Klikněte na zaškrtávací políčko **Nastavení patice nasazení.**
- Klikněte na tlačítko **OK**.
- Jakmile nastavení okna zmizí, klikněte na **Uložit,** aby změny zůstaly

![Nastavení slotu pro nasazení](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Nasazení

Sloty jsou prázdné při vytváření slotu. K nasazení aplikace do patice můžete použít libovolnou [z podporovaných technologií nasazení.](./functions-deployment-technologies.md)

## <a name="scaling"></a>Škálování

Všechny sloty se škálují na stejný počet pracovníků jako výrobní slot.

- Pro plány spotřeby se slot škáluje podle velikosti aplikace funkce.
- U plánů služby App Service se aplikace škáluje na pevný počet pracovníků. Sloty běží na stejný počet pracovníků jako plán aplikace.

## <a name="add-a-slot"></a>Přidat slot

Můžete přidat slot přes [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) nebo prostřednictvím portálu. Následující kroky ukazují, jak vytvořit nový slot na portálu:

1. Přejděte do aplikace funkce a klikněte na **znaménko plus** vedle *slotů*.

    ![Přidání slotu pro nasazení funkcí Azure](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Do textového pole zadejte název a stiskněte tlačítko **Vytvořit.**

    ![Název slotu pro nasazení Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Swap sloty

Sloty můžete vyměnit prostřednictvím [cli](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) nebo prostřednictvím portálu. Následující kroky ukazují, jak vyměnit sloty na portálu:

1. Přechod do aplikace funkce
1. Klikněte na název zdrojové pozice, kterou chcete vyměnit.
1. Na kartě *Přehled* klikněte ![na slot pro **odkládací** tlačítko Swap Azure Functions.](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Ověřte nastavení konfigurace pro odkládací a klikněte na **Zaměnit** ![slot pro prohození funkcí Azure.](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

Operace může trvat chvíli při provádění operace odkládacího režimu.

## <a name="roll-back-a-swap"></a>Vrácení výměny zpět

Pokud zaměňování má za následek chybu nebo jednoduše chcete "vrátit zpět" odkládací, můžete vrátit zpět do počátečního stavu. Chcete-li se vrátit do stavu před prohození, proveďte další prohození pro stornovat prohození.

## <a name="remove-a-slot"></a>Odebrání patice

Můžete odebrat slot přes [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) nebo prostřednictvím portálu. Následující kroky ukazují, jak odebrat patice na portálu:

1. Přechod na přehled funkční aplikace

1. Klikněte na tlačítko **Odstranit**

    ![Přidání slotu pro nasazení funkcí Azure](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>Automatizace správy slotů

Pomocí [příkazového příkazového příkazu k azure](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)můžete automatizovat následující akce pro slot:

- [Vytvořit](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [delete](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [Swap](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [automatický proměn](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Změnit plán služby App Service

S funkcí aplikace, která běží v rámci plánu služby App Service, máte možnost změnit základní plán služby App Service pro slot.

> [!NOTE]
> Plán služby App Service slotu nelze změnit v rámci plánu Spotřeba.

Pomocí následujících kroků můžete změnit plán služby App Service:

1. Přechod na slot

1. V části *Funkce platformy*klikněte na **Všechna nastavení.**

    ![Změna plánu služeb aplikace](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Klikněte na **plán služby App Service.**

1. Vyberte nový plán služby App Service nebo vytvořte nový plán

1. Klikněte na tlačítko **OK**.

    ![Změna plánu služeb aplikace](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Omezení

Sloty pro nasazení funkcí Azure mají následující omezení:

- Počet slotů dostupných pro aplikaci závisí na plánu. Plán spotřeby je povolen pouze jeden slot nasazení. Další sloty jsou k dispozici pro aplikace spuštěné v rámci plánu služby App Service.
- Výměna slotu obnoví klíče pro aplikace, které mají nastavení `AzureWebJobsSecretStorageType` aplikace rovné `files`.
- Sloty nejsou k dispozici pro plán Linux Consumption.

## <a name="support-levels"></a>Úrovně podpory

Existují dvě úrovně podpory pro nasazení slotů:

- **Obecná dostupnost (GA)**: Plně podporována a schválena pro použití ve výrobě.
- **Náhled**: Zatím není podporován, ale očekává se, že v budoucnu dosáhne stavu GA.

| Plán os/hosting           | Úroveň podpory     |
| ------------------------- | -------------------- |
| Spotřeba systému Windows       | Všeobecná dostupnost |
| Windows Premium           | Všeobecná dostupnost  |
| Windows Vyhrazené         | Všeobecná dostupnost |
| Spotřeba Linuxu         | Nepodporované          |
| Linux Premium             | Všeobecná dostupnost  |
| Linux Dedicated           | Všeobecná dostupnost |

## <a name="next-steps"></a>Další kroky

- [Technologie nasazení ve funkcích Azure](./functions-deployment-technologies.md)
