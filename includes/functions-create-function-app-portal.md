---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c590b61ee1424d32d83dc5f758682fde37492c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057128"
---
1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

1. Na stránce **Nový** vyberte **Compute** > **Function App**.

1. Na stránce **Základy** použijte nastavení aplikace funkcí, jak je uvedeno v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **Název aplikace function** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky `a-z` jsou (malá `0-9`a `-`velká písmena), a .  |
    |**Publikovat**| kód | Možnost publikování souborů kódu nebo kontejneru Docker |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET Core** pro funkce C# a F#. |
    |**Verze**| Číslo verze | Zvolte verzi nainstalovaného runtime.  |
    |**Oblasti**| Upřednostňovaná oblast | Vyberte [oblast](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. |

    ![Základy](./media/functions-create-function-app-portal/function-app-create-basics.png)

1. Vyberte **další : Hosting**. Na stránce **Hosting** zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Účet úložiště](../articles/storage/common/storage-account-create.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a mohou obsahovat pouze čísla a malá písmena. Můžete také použít existující účet, který musí splňovat [požadavky na účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Operační systém**| Preferovaný operační systém | Operační systém je předem vybrán na základě výběru zásobníku za běhu, ale v případě potřeby můžete nastavení změnit. |
    | **[Plán](../articles/azure-functions/functions-scale.md)** | **Spotřeba (bez serveru)** | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Ve výchozím plánu **spotřeby** jsou prostředky přidávány dynamicky podle požadavků vašich funkcí. V tomto [hostingu bez serveru](https://azure.microsoft.com/overview/serverless-computing/) platíte pouze za dobu, po kterou vaše funkce běží. Pokud používáte plán služby App Service, musíte zajistit správu [škálování vaší aplikace funkcí](../articles/azure-functions/functions-scale.md).  |

    ![Hostování](./media/functions-create-function-app-portal/function-app-create-hosting.png)

1. Vyberte **další : Sledování**. Na stránce **Sledování** zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Výchozí | Vytvoří prostředek Application Insights se stejným *názvem aplikace* v nejbližší podporované oblasti. Rozšířením tohoto nastavení můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v [geografické oblasti Azure,](https://azure.microsoft.com/global-infrastructure/geographies/) kde chcete ukládat data. |

    ![Monitorování](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Vyberte **Revize + vytvořit** a zkontrolujte výběr konfigurace aplikace.

1. Na stránce **Revize + vytvoření** zkontrolujte nastavení a pak vyberte **Vytvořit** pro zřízení a nasazení aplikace funkce.

1. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

1. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte. Můžete také vybrat **připnout na řídicí panel**. Připnutí usnadňuje návrat k tomuto prostředku aplikace funkce z řídicího panelu.

    ![Oznámení o nasazení](./media/functions-create-function-app-portal/function-app-create-notification2.png)