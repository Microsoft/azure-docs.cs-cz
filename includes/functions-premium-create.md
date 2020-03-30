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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021275"
---
1. Otevření portálu Azure z[https://portal.azure.com](https://portal.azure.com)

1. Vyberte tlačítko **Vytvořit zdroj.**

    ![Vytvoření prostředku](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. Vyberte **aplikaci výpočetní** > **funkce**.

    ![Vytvoření aplikace Function App na portálu Azure Portal](./media/functions-premium-create/function-app-create-start.png)

1. Použijte nastavení aplikace Function App uvedená v tabulce pod obrázkem.

    ![Základy](./media/functions-premium-create/function-app-create-basics.png)

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **Název aplikace function** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky `a-z` jsou (malá `0-9`a `-`velká písmena), a .  |
    |**Publikovat**| kód | Možnost publikování souborů kódu nebo kontejneru Docker |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. |
    |**Oblasti**| Upřednostňovaná oblast | Vyberte [oblast](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. |

    Vyberte tlačítko **Další : Hostování >.**

1. Zadejte následující nastavení hostingu.

    ![Hostování](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Účet úložiště](../articles/storage/common/storage-account-create.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete také použít existující účet, který musí splňovat [požadavky na účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Operační systém**| Preferovaný operační systém | Operační systém je předem vybrán na základě výběru zásobníku za běhu, ale v případě potřeby můžete nastavení změnit. |
    | **[Plán](../articles/azure-functions/functions-scale.md)** | Premium | V části Typ plánu vyberte **Premium (Náhled)** a vyberte výchozí hodnoty pro výběr *y Windows Plan* a *Sku a velikosti.* |

    Vyberte **tlačítko Další : Monitorování >.**

1. Zadejte následující nastavení monitorování.

    ![Monitorování](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Výchozí | Vytvoří prostředek Application Insights se stejným *názvem aplikace* v nejbližší podporované oblasti. Rozšířením tohoto nastavení můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v [geografické oblasti Azure,](https://azure.microsoft.com/global-infrastructure/geographies/) kde chcete ukládat data. |

    Chcete-li zkontrolovat výběr konfigurace aplikace, vyberte **zkontrolovat + vytvořit.**

1. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.