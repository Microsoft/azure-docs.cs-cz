---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 10/25/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: fca8c3b0b1bd4d22720a6d15313e297d05b7fac9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021275"
---
1. Otevřete Azure Portal z [https://portal.azure.com](https://portal.azure.com)

1. Vyberte tlačítko **vytvořit prostředek** .

    ![Vytvořit prostředek](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. Vyberte **compute** > **Function App**.

    ![Vytvoření aplikace Function App na portálu Azure Portal](./media/functions-premium-create/function-app-create-start.png)

1. Použijte nastavení aplikace Function App uvedená v tabulce pod obrázkem.

    ![Základy](./media/functions-premium-create/function-app-create-basics.png)

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **Název aplikace funkcí** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z` (nerozlišuje velká a malá písmena), `0-9`a `-`.  |
    |**Publikování**| kód | Možnost publikování souborů kódu nebo kontejneru Docker |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. |
    |**Oblast**| Upřednostňovaná oblast | Vyberte [oblast](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. |

    Vyberte tlačítko **Další: hostování >** .

1. Zadejte následující nastavení hostování.

    ![Hostování](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Účet úložiště](../articles/storage/common/storage-account-create.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít od 3 do 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít i existující účet, který musí splňovat [požadavky na účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Operační systém**| Preferovaný operační systém | Operační systém je předem vybraný pro vás na základě výběru zásobníku modulu runtime, ale v případě potřeby můžete změnit nastavení. |
    | **[Rozhraní](../articles/azure-functions/functions-scale.md)** | Premium | Jako typ plánu vyberte **Premium (Preview)** a vyberte výchozí hodnoty pro výběr *plánu* a SKU Windows a nastavení *velikosti* . |

    Vyberte tlačítko **Další: > monitorování** .

1. Zadejte následující nastavení monitorování.

    ![Sledování](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Výchozí | Vytvoří Application Insights prostředek stejného *názvu aplikace* v nejbližší podporované oblasti. Rozbalením tohoto nastavení můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v [geografických oblastech Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , kam chcete data uložit. |

    Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte výběry konfigurace aplikace.

1. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.