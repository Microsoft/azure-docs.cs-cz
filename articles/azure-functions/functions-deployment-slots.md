---
title: Azure Functions sloty nasazení
description: Naučte se vytvářet a používat nasazovací sloty pomocí Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 2dbf7c31e6b0b40fa9dc2d59e86c0ecc731657e1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172405"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions sloty nasazení

Azure Functions sloty nasazení umožňují vaší aplikaci Function App spouštět různé instance s názvem "sloty". Sloty jsou různá prostředí, která se zveřejňují prostřednictvím veřejně dostupného koncového bodu. Jedna instance aplikace je vždycky namapovaná na produkční slot a můžete měnit instance přiřazené k slotu na vyžádání. Aplikace Function App spuštěné v plánu služby App Service mohou mít několik slotů, zatímco v rámci plánu spotřeby je povolen pouze jeden slot.

Následující informace odrážejí, jak jsou funkce ovlivněné odkládacími Sloty:

- Přesměrování provozu je bezproblémové; z důvodu zahození nejsou vyřazeny žádné žádosti.
- Pokud během prohození funguje funkce, pokračuje se v provádění a další triggery se přesměrují do vyměněné instance aplikace.

> [!NOTE]
> Pro plán spotřeby pro Linux nejsou aktuálně dostupné sloty.

## <a name="why-use-slots"></a>Proč používat sloty?

Pro použití slotů nasazení existuje několik výhod. Následující scénáře popisují běžné použití pro Sloty:

- **Různá prostředí pro různé účely**: pomocí různých slotů získáte možnost odlišit instance aplikací před odchodem do produkčního nebo přípravného slotu.
- Průběžné nasazování **: nasazení** do slotu místo přímo do produkčního prostředí umožňuje, aby se aplikace před živým zprovozněním zahřívá. Kromě toho se pomocí slotů omezuje latence pro úlohy aktivované protokolem HTTP. Instance jsou před nasazením zavedeny, což snižuje studenou dobu spouštění nově nasazených funkcí.
- **Snadné nouzové** akce: po prohození s produkčním prostředím má teď pozice s dřív připravenou aplikací předchozí produkční aplikaci. Pokud se změny vyměněné do produkčního slotu neshodují, můžete okamžitě vrátit swap a získat tak zpátky "Poslední známá dobrá instance".

## <a name="swap-operations"></a>Operace prohození

Během swapu je jeden slot považován za zdroj a druhý cíl. Zdrojové sloty má instanci aplikace, která se používá pro cílový slot. Následující kroky zajišťují, že se cílovému slotu během prohození neprojeví výpadky:

1. **Použít nastavení:** Nastavení z cílového slotu se aplikují na všechny instance zdrojové patice. Například nastavení produkčního prostředí se aplikuje na pracovní instanci. K použitým nastavením patří následující kategorie:
    - Nastavení aplikace [konkrétního slotu](#manage-settings) a připojovací řetězce (Pokud je k dispozici)
    - Nastavení [průběžného nasazování](../app-service/deploy-continuous-deployment.md) (Pokud je povolené)
    - Nastavení [ověřování App Service](../app-service/overview-authentication-authorization.md) (Pokud je povolené)

1. **Počkat na restartování a dostupnost:** Swap počká na všechny instance ve zdrojové patici, aby bylo možné dokončit jeho restart a bude k dispozici pro požadavky. Pokud se nepovede restartování jakékoli instance, operace přepnutí vrátí všechny změny zdrojové patice a zastaví operaci.

1. **Postup aktualizace:** Pokud se všechny instance ve zdrojové patici zahřívá úspěšně, tyto dva sloty dokončí prohození přepnutím pravidel směrování. Po provedení tohoto kroku bude mít cílová patice (například produkční slot) aplikaci, která byla dříve zahřívání ve zdrojové patici.

1. **Opakovat operaci:** Teď, když má zdrojový slot předem prohozenou aplikaci v cílovém slotu, dokončete stejnou operaci tím, že použijete všechna nastavení a restartujete instance pro zdrojovou pozici.

Mějte na paměti následující skutečnosti:

- V jakémkoli bodě operace swapu se inicializace vyměněných aplikací stane ve zdrojové pozici. Cílový slot zůstane online, zatímco je zdrojová slot připravený, ať už je prohození úspěšné nebo neúspěšné.

- Pokud chcete vyměnit pracovní slot s produkčním slotem, ujistěte se, že produkční slot je *vždycky* cílový slot. Tato operace přepnutí nijak neovlivní vaši produkční aplikaci.

- Nastavení související se zdroji událostí a vazbami musí být *před zahájením swapu* nakonfigurovaná jako [nastavení slotu nasazení](#manage-settings) . Pokud je označíte jako "vždy", zajistíte tím, že události a výstupy budou směrovány do správné instance.

## <a name="manage-settings"></a>Správa nastavení

Některá nastavení konfigurace jsou specifická pro slot. Následující seznamy obsahují informace o tom, která nastavení se při prohození slotů mění a která zůstávají stejná.

**Nastavení specifická pro slot**:

* Publikování koncových bodů
* Vlastní názvy domén
* Neveřejné certifikáty a nastavení TLS/SSL
* Nastavení škálování
* Plánovače WebJobs
* Omezení IP adresy
* Vždy zapnuto
* Nastavení diagnostiky
* Sdílení prostředků mezi zdroji (CORS)

**Nastavení, která nejsou specifická pro slot**:

* Obecná nastavení, například verze architektury, 32 nebo 64 bitů, webové sokety
* Nastavení aplikace (může být nakonfigurováno na slot)
* Připojovací řetězce (můžou být nakonfigurované tak, aby se nastavily na slot)
* Mapování obslužných rutin
* Veřejné certifikáty
* Obsah webových úloh
* Hybridní připojení *
* Integrace virtuální sítě *
* Koncové body služby *
* Content Delivery Network Azure *

Funkce označené hvězdičkou (*) jsou plánovány jako neswapé. 

> [!NOTE]
> Některá nastavení aplikace, která platí pro nezaměnitelné nastavení, se také nemění. Například vzhledem k tomu, že nastavení diagnostiky nejsou zaměněna, související nastavení aplikace, například `WEBSITE_HTTPLOGGING_RETENTION_DAYS` a `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` , se nemění ani v případě, že se nezobrazují jako nastavení slotu.
>

### <a name="create-a-deployment-setting"></a>Vytvoření nastavení nasazení

Nastavení můžete označit jako nastavení nasazení, což způsobí, že bude "rychlé". Rychlé nastavení se v instanci aplikace nemění.

Pokud vytvoříte nastavení nasazení v jednom slotu, nezapomeňte vytvořit stejné nastavení s jedinečnou hodnotou v jakémkoli jiném slotu, který je součástí swapu. V případě, že se hodnota nastavení nezmění, názvy nastavení zůstávají konzistentní mezi sloty. Konzistence názvů zajišťuje, že se váš kód nepokouší získat přístup k nastavení, které je definováno v jednom slotu, ale ne jiné.

Chcete-li vytvořit nastavení nasazení, použijte následující postup:

1. V aplikaci Function App přejděte na **sloty nasazení** a potom vyberte název slotu.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Najděte sloty v Azure Portal." border="true":::

1. Vyberte **Konfigurace** a pak vyberte název nastavení, který chcete s aktuálním slotem vyznačit.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Nakonfigurujte nastavení aplikace pro slot ve Azure Portal." border="true":::

1. Vyberte **nastavení slotu nasazení** a pak vyberte **OK**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Nakonfigurujte nastavení slotu nasazení." border="true":::

1. Po nastavení oddílu zmizí, vyberte **Uložit** , aby se změny zachovaly.

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Uložte nastavení slotu nasazení." border="true":::

## <a name="deployment"></a>Nasazení

Při vytváření slotu jsou sloty prázdné. K nasazení aplikace do slotu můžete použít kteroukoli z [podporovaných technologií nasazení](./functions-deployment-technologies.md) .

## <a name="scaling"></a>Škálování

Všechny sloty se škálují na stejný počet pracovních procesů jako produkční slot.

- V případě plánů spotřeby se slot škáluje podle škály aplikace Function App.
- U App Service plánů se aplikace škáluje na pevný počet pracovních procesů. Sloty běží na stejném počtu pracovních procesů jako plán aplikace.

## <a name="add-a-slot"></a>Přidat slot

Slot můžete přidat prostřednictvím rozhraní příkazového [řádku](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create) nebo prostřednictvím portálu. Následující kroky ukazují, jak vytvořit novou patici na portálu:

1. Přejděte do aplikace Function App.

1. Vyberte možnost **sloty nasazení** a pak vyberte **+ Přidat slot**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Přidejte Azure Functions slot nasazení." border="true":::

1. Zadejte název slotu a vyberte **Přidat**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Název Azure Functions slot pro nasazení." border="true":::

## <a name="swap-slots"></a>Prohození slotů

Sloty můžete prohodit přes rozhraní příkazového [řádku](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap) nebo prostřednictvím portálu. Následující kroky ukazují, jak odkládací sloty na portálu:

1. Přejděte do aplikace Function App.
1. Vyberte možnost **sloty nasazení** a pak vyberte **swap**.

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Snímek obrazovky zobrazující stránku slot nasazení se zvolenou akcí přidat slot" border="true":::

1. Ověřte nastavení konfigurace pro svůj swap a vyberte **prohození** .
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="Zahodí slot nasazení." border="true":::

Operace přepnutí může chvíli trvat.

## <a name="roll-back-a-swap"></a>Vrácení swapu zpět

Pokud dojde k chybě zahození nebo pokud chcete jednoduše vrátit zpět změny, můžete se vrátit k počátečnímu stavu. Chcete-li se vrátit do předem vyměněného stavu, proveďte další prohození pro obrácení swapu.

## <a name="remove-a-slot"></a>Odebrat slot

Slot můžete odebrat přes rozhraní příkazového [řádku](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete) nebo prostřednictvím portálu. Následující kroky ukazují, jak odebrat slot na portálu:

1. V aplikaci Function App přejděte na **sloty nasazení** a potom vyberte název slotu.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Najděte sloty v Azure Portal." border="true":::

1. Vyberte **Odstranit**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Snímek obrazovky zobrazující stránku Přehled s vybranou akcí odstranit" border="true":::

1. Zadejte název slotu nasazení, který chcete odstranit, a pak vyberte **Odstranit**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Odstraňte slot nasazení v Azure Portal." border="true":::

1. Zavřete podokno potvrzení odstranění.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Potvrzení odstranění slotu nasazení" border="true":::

## <a name="automate-slot-management"></a>Automatizace správy slotů

Pomocí [Azure CLI](/cli/azure/functionapp/deployment/slot)můžete pro slot automatizovat následující akce:

- [vytvořeny](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create)
- [delete](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete)
- [list](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-list)
- [adresu](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap)
- [Automatické prohození](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Změnit plán App Service

Pomocí aplikace Function App, která běží v plánu App Service, můžete změnit základní plán App Service pro slot.

> [!NOTE]
> V plánu spotřeby nemůžete změnit plán App Service přihrádky.

Pomocí následujících kroků můžete změnit plán App Service přihrádky:

1. V aplikaci Function App přejděte na **sloty nasazení** a potom vyberte název slotu.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Najděte sloty v Azure Portal." border="true":::

1. V části **plán App Service** vyberte **změnit plán App Service**.

1. Vyberte plán, na který chcete upgradovat, nebo vytvořte nový plán.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Změňte plán App Service v Azure Portal." border="true":::

1. Vyberte **OK**.

## <a name="limitations"></a>Omezení

Azure Functions sloty nasazení mají následující omezení:

- Počet slotů dostupných pro aplikaci závisí na plánu. Plán spotřeby má povolený jenom jeden slot nasazení. Pro aplikace spuštěné v plánu App Service jsou k dispozici další sloty.
- Výměna slotu resetuje klíče pro aplikace, které mají `AzureWebJobsSecretStorageType` nastavení aplikace rovno `files` .
- Pro plán spotřeby pro Linux nejsou dostupné žádné sloty.

## <a name="support-levels"></a>Úrovně podpory

Existují dvě úrovně podpory pro sloty nasazení:

- **Všeobecná dostupnost (GA)**: plně podporovaná a schválená pro použití v produkčním prostředí.
- **Verze Preview**: zatím není podporovaná, ale očekává se, že bude v budoucnu dostupný stav GA.

| Operační systém/plán hostování           | Úroveň podpory     |
| ------------------------- | -------------------- |
| Spotřeba Windows       | Všeobecná dostupnost |
| Windows Premium           | Všeobecná dostupnost  |
| Vyhrazená pro Windows         | Všeobecná dostupnost |
| Spotřeba Linux         | Nepodporované          |
| Linux Premium             | Všeobecná dostupnost  |
| Vyhrazený pro Linux           | Všeobecná dostupnost |

## <a name="next-steps"></a>Další kroky

- [Technologie nasazení v Azure Functions](./functions-deployment-technologies.md)
