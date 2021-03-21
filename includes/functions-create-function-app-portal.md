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
ms.openlocfilehash: 9ec5920295117b1726a684bcd08534cd6917f7e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100569793"
---
1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

1. Na stránce **Nový** vyberte **COMPUTE**  >  **Function App**.

1. Na stránce **základy** použijte nastavení aplikace Function App, jak je uvedeno v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace funkcí vytvořena. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **Název aplikace funkcí** | Globálně jedinečný název | Název identifikující novou aplikaci funkcí. Platné znaky jsou `a-z` (bez rozlišování malých a velkých písmen), `0-9` a `-`.  |
    |**Publikovat**| Kód | Možnost publikování souborů kódu nebo kontejneru Docker |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Úpravy v portálu jsou dostupné jenom pro skript JavaScript, PowerShell, TypeScript a C#. Funkce knihovny tříd jazyka C#, Java a Python musí být [vyvíjeny místně](../articles/azure-functions/functions-develop-local.md#local-development-environments).  |
    |**Verze**| Číslo verze | Vyberte verzi nainstalovaného modulu runtime. |
    |**Oblast**| Upřednostňovaná oblast | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž nebo poblíž dalších služeb, ke kterým máte přístup. |

1. Vyberte **Další: Hostování**. Na stránce **hostování** zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Účet úložiště](../articles/storage/common/storage-account-create.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít i existující účet, který musí splňovat [požadavky na účet úložiště](../articles/azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Operační systém**| Windows | Operační systém je předem vybraný pro vás na základě výběru zásobníku modulu runtime, ale v případě potřeby můžete změnit nastavení. Úpravy v portálu se podporují jenom v systému Windows. |
    | **[Plánování](../articles/azure-functions/functions-scale.md)** | **Využití (bez serverů)** | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Ve výchozím plánu **Využití** se prostředky přidávají dynamicky podle požadavků příslušných funkcí. V tomto hostiteli bez [serveru](https://azure.microsoft.com/overview/serverless-computing/) platíte jenom za čas, kdy se vaše funkce spouštějí. Pokud používáte plán služby App Service, musíte zajistit správu [škálování vaší aplikace funkcí](../articles/azure-functions/functions-scale.md).  |

1. Vyberte **Další: monitorování**. Na stránce **monitorování** zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Výchozí | Vytvoří Application Insights prostředek stejného *názvu aplikace* v nejbližší podporované oblasti. Rozbalením tohoto nastavení nebo výběrem možnosti **vytvořit nový** můžete změnit název Application Insights nebo zvolit jinou oblast v [geografickém umístění Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , kam chcete ukládat data. |

1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte výběry konfigurace aplikace.

1. Na stránce **Revize + vytvořit** zkontrolujte nastavení a pak vyberte **vytvořit** a nasaďte a nasaďte aplikaci Function App.

1. Vyberte ikonu **oznámení** v pravém horním rohu portálu a sledujte zprávu o **úspěšném nasazení** .

1. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte. Můžete také vybrat **Připnout na řídicí panel**. Připnutí usnadňuje návrat k tomuto prostředku Function App z vašeho řídicího panelu.

    ![Oznámení o nasazení](./media/functions-create-function-app-portal/function-app-create-notification2.png)
