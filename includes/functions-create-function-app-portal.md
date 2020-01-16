---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/17/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 7da9f795f1865d635a26cd3acf3866dff3565716
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021240"
---
1. V nabídce Azure Portal vyberte **vytvořit prostředek**.

    ![Přidání prostředku pomocí nabídky Azure Portal](./media/functions-create-function-app-portal/create-function-app-resource.png)

1. Na stránce **Nový** vyberte **COMPUTE** > **Function App**.

1. Použijte nastavení aplikace Function App uvedená v tabulce pod obrázkem.

    ![Základy](./media/functions-create-function-app-portal/function-app-create-basics.png)

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **Název aplikace funkcí** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z` (nerozlišuje velká a malá písmena), `0-9`a `-`.  |
    |**Publikování**| kód | Možnost publikování souborů kódu nebo kontejneru Docker |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. |
    |**Oblast**| Upřednostňovaná oblast | Vyberte [oblast](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. |

    Vyberte tlačítko **Další: hostování >** .

1. Zadejte následující nastavení pro hostování.

    ![Hostování](./media/functions-create-function-app-portal/function-app-create-hosting.png)

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Účet úložiště](../articles/storage/common/storage-account-create.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít od 3 do 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít i existující účet, který musí splňovat [požadavky na účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Operační systém**| Preferovaný operační systém | Operační systém je předem vybraný pro vás na základě výběru zásobníku modulu runtime, ale v případě potřeby můžete změnit nastavení. |
    | **[Rozhraní](../articles/azure-functions/functions-scale.md)** | Plán Consumption | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Ve výchozím **plánu Consumption** se prostředky přidávají dynamicky podle požadavků příslušných funkcí. U tohoto hostování [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) platíte jenom za dobu, kdy jsou funkce spuštěné. Pokud používáte plán služby App Service, musíte zajistit správu [škálování vaší aplikace funkcí](../articles/azure-functions/functions-scale.md).  |

    Vyberte tlačítko **Další: > monitorování** .

1. Pro monitorování zadejte následující nastavení.

    ![Sledování](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Výchozí | Vytvoří Application Insights prostředek stejného *názvu aplikace* v nejbližší podporované oblasti. Rozbalením tohoto nastavení můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v [geografických oblastech Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , kam chcete data uložit. |

    Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte výběry konfigurace aplikace.

1. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

1. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Oznámení o nasazení](./media/functions-create-function-app-portal/function-app-create-notification2.png)

1. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte. Můžete také vybrat **Připnout na řídicí panel**. Připnutí usnadňuje návrat k tomuto prostředku Function App z vašeho řídicího panelu.
