---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2020
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: cdeba09c91bee72508632b8468126150017df7cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98187033"
---
1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

1. Na stránce **Nový** vyberte **COMPUTE**  >  **Function App**.

1. Na stránce **základy** použijte nastavení aplikace Function App, jak je uvedeno v následující tabulce:

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace funkcí vytvořena. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **Název aplikace funkcí** | Globálně jedinečný název | Název identifikující novou aplikaci funkcí. Platné znaky jsou `a-z` (bez rozlišování malých a velkých písmen), `0-9` a `-`.  |
    |**Publikovat**| Kód | Možnost publikování souborů kódu nebo kontejneru Docker |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Úpravy v portálu se v současné době nepodporují pro vývoj v jazyce [Python](../articles/azure-functions/functions-reference-python.md).|
    |**Oblast**| Upřednostňovaná oblast | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž nebo poblíž dalších služeb, ke kterým máte přístup. |

1. Vyberte **Další: hostování**. Na stránce **hostování** zadejte následující nastavení:

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Účet úložiště](../articles/storage/common/storage-account-create.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít i existující účet, který musí splňovat [požadavky na účet úložiště](../articles/azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Operační systém**| Preferovaný operační systém | Operační systém je předem vybraný pro vás na základě výběru zásobníku modulu runtime, ale v případě potřeby můžete změnit nastavení. Python se podporuje jenom na Linux. Úpravy v portálu se podporují jenom v systému Windows.|
    | **[Plánování](../articles/azure-functions/functions-scale.md)** | Premium | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Vyberte **Premium**. Ve výchozím nastavení se vytvoří nový plán App Service. Výchozí **SKU a velikost** jsou **EP1**, kde EP představuje _elastickou prémii_. Další informace najdete v [seznamu SKU úrovně Premium](../articles/azure-functions/functions-premium-plan.md#available-instance-skus).<br/>Při spouštění funkcí JavaScriptu na plánu Premium byste měli zvolit instanci, která má méně vCPU. Další informace najdete v tématu [Výběr plánů Premium s jedním jádrem](../articles/azure-functions/functions-reference-node.md#considerations-for-javascript-functions).  |

1. Vyberte **Další: monitorování**. Na stránce **monitorování** zadejte následující nastavení:

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Výchozí | Vytvoří Application Insights prostředek stejného *názvu aplikace* v nejbližší podporované oblasti. Rozbalením tohoto nastavení můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v [geografickém geografickém prostředí Azure](https://azure.microsoft.com/global-infrastructure/geographies/) pro ukládání vašich dat. |

1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte výběry konfigurace aplikace.

1. Na stránce **Revize + vytvořit** zkontrolujte nastavení a pak vyberte **vytvořit** a nasaďte a nasaďte aplikaci Function App.

1. Vyberte ikonu **oznámení** v pravém horním rohu portálu a sledujte zprávu o **úspěšném nasazení** .

1. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte. Můžete také vybrat **Připnout na řídicí panel**. Připnutí usnadňuje návrat k tomuto prostředku Function App z vašeho řídicího panelu.

    ![Oznámení o nasazení](./media/functions-premium-create/function-app-create-notification2.png)
