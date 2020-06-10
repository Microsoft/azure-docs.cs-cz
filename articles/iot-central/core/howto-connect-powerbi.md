---
title: Vizualizace dat IoT Central Azure v řídicím panelu Power BI | Microsoft Docs
description: K vizualizaci a analýze dat IoT Central použijte řešení Power BI pro Azure IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: a484ab2553aeefbbe8c03ae639bdd667e4d9cb8e
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84661191"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Vizualizace a analýza dat IoT Central Azure v řídicím panelu Power BI

*Toto téma se týká správců a vývojářů řešení.*

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Power BI kanál řešení":::

K vytvoření výkonného řídicího panelu Power BI pro monitorování výkonu zařízení IoT použijte Power BI řešení pro Azure IoT Central. Na řídicím panelu Power BI můžete:

- Sledování množství dat, která zařízení odesílají v průběhu času
- Porovnání datových svazků mezi různými proudy telemetrie
- Filtrování dolů na data odesílaná konkrétními zařízeními
- Zobrazit nejnovější data telemetrie v tabulce

Toto řešení nastaví kanál, který čte data z účtu služby Azure Blob Storage pro [průběžnou datovou export](howto-export-data-blob-storage.md) . Kanál používá ke zpracování a transformaci dat Azure Functions, Azure Data Factory a Azure SQL Database. data můžete vizualizovat a analyzovat v Power BI sestavě, kterou stáhnete jako soubor PBIX. Všechny prostředky se vytvoří ve vašem předplatném Azure, takže můžete jednotlivé komponenty přizpůsobit tak, aby vyhovovaly vašim potřebám.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto průvodci, potřebujete aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

Nastavení řešení vyžaduje následující zdroje:

- IoT Central aplikace Další informace najdete v tématu [Vytvoření aplikace Azure IoT Central](./quick-deploy-iot-central.md).
- Průběžné exportování dat je nakonfigurované pro export telemetrie, zařízení a šablon zařízení do úložiště objektů BLOB v Azure. Další informace najdete v tématu [Jak exportovat data IoT do cílových umístění v Azure](howto-export-data.md).
  - Ujistěte se, že pouze vaše aplikace IoT Central exportuje data do kontejneru objektů BLOB.
  - Vaše [zařízení musí odesílat zprávy kódované JSON](../../iot-hub/iot-hub-devguide-messages-d2c.md). Zařízení musí určovat `contentType:application/JSON` `contentEncoding:utf-8` nebo `contentEncoding:utf-16` nebo `contentEncoding:utf-32` ve vlastnostech systému zprávy.
- Power BI Desktop (nejnovější verze) Viz téma [stažení Power BI](https://powerbi.microsoft.com/downloads/).
- Power BI Pro (Pokud chcete řídicí panel sdílet s ostatními).

## <a name="install"></a>Instalace

Pokud chcete nastavit kanál, přejděte na stránku [Power BI řešení pro Azure IoT Central](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) na webu **Microsoft AppSource** . Vyberte **získat hned**a postupujte podle pokynů.

Když otevřete soubor PBIX, ujistěte se, že je čten a postupujte podle pokynů na titulní stránce. Tyto pokyny popisují, jak připojit sestavu ke službě SQL Database.

## <a name="report"></a>Sestava

Soubor PBIX obsahuje zprávu **zařízení a telemetrie** zobrazuje historický přehled telemetrie, která byla odeslána zařízeními. Poskytuje rozpis různých typů telemetrie a také zobrazuje nejnovější telemetrie, kterou odesílají zařízení.

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Power BI zařízení a sestava telemetrie":::

## <a name="pipeline-resources"></a>Prostředky kanálu

Ke všem prostředkům Azure, které tvoří kanál, máte přístup v Azure Portal. Všechny prostředky jsou ve skupině prostředků, kterou jste vytvořili při nastavení kanálu.

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="Azure Portal zobrazení skupiny prostředků":::

Následující seznam popisuje role jednotlivých prostředků v kanálu:

### <a name="azure-functions"></a>Azure Functions

Služba Azure Function App se spustí pokaždé, když IoT Central zapíše nový soubor do úložiště objektů BLOB. Funkce extrahují data z objektů BLOB šablon telemetrie, zařízení a zařízení a naplní mezilehlé tabulky SQL, které Azure Data Factory používá.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory se připojí k databázi SQL jako propojená služba. Spouští uložené procedury pro zpracování dat a jejich uložení do tabulek analýzy.

Azure Data Factory se spouští každých 15 minut a transformují nejnovější dávku dat, aby se načetly do tabulek SQL (což je aktuální minimální počet pro **aktivační událost pro bubnové okno**).

### <a name="azure-sql-database"></a>Azure SQL Database

Azure Data Factory generuje sadu tabulek analýzy pro Power BI. Tato schémata můžete prozkoumat v Power BI a používat je k vytváření vlastních vizualizací.

## <a name="estimated-costs"></a>Odhadované náklady

Stránka [Power BI řešení pro Azure IoT Central](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) na webu Microsoft AppSource obsahuje odkaz na Estimator nákladů na prostředky, které nasadíte.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak vizualizovat data v Power BI, je navržený další krok, kde se dozvíte, [jak spravovat zařízení](howto-manage-devices.md).
